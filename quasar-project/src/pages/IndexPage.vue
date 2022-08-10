<template>
  <q-page padding>
    <q-table title="Dados" :rows="posts" :columns="columns" row-key="name">
      <template v-slot:body-cell-actions="props">
        <q-td :props="props">
          <q-btn
            icon="delete"
            color="negative"
            dense
            size="m"
            @click="handleDeletePost(props.row.id)"
          />
        </q-td>
      </template>
    </q-table>
  </q-page>
</template>

<script>
import { defineComponent, onMounted, ref } from "vue";
import postsService from "src/services/posts";
import { useQuasar } from "quasar";

export default defineComponent({
  name: "IndexPage",
  setup() {
    const posts = ref([]);
    const { list, remove } = postsService();
    const columns = [
      {
        name: "id",
        label: "Id",
        field: "id",
        sortable: true,
        align: "left",
      },
      {
        name: "title",
        label: "Title",
        field: "title",
        sortable: true,
        align: "left",
      },
      {
        name: "author",
        label: "Autor",
        field: "author",
        sortable: true,
        align: "left",
      },
      {
        name: "actions",
        label: "Ações",
        field: "actions",
        sortable: true,
        align: "right",
      },
    ];
    const $q = useQuasar();
    onMounted(() => {
      getPosts();
    });

    const getPosts = async () => {
      try {
        const data = await list(); //consigo pegar posts direto porque no meu arquivo axios.js o endereço da minha api esta configurada como o endpoint default.
        posts.value = data;
      } catch (error) {
        console.error(error);
      }
    };

    const handleDeletePost = async (id) => {
      try {
        $q.dialog({
          title: "Remover",
          message: "Deseja realmente excluir o post?",
          cancel: true,
          persistent: true,
        }).onOk(async () => {
          await remove(id);
          $q.notify({
            message: "Apagado com sucesso!",
            icon: "check",
            color: "positive",
          });
          await getPosts();
        });
      } catch (error) {
        $q.notify({
          message: "Erro ao apagar!",
          icon: "times",
          color: "negative",
        });
      }
    };

    return {
      posts,
      columns,
      handleDeletePost,
    };
  },
});
</script>
