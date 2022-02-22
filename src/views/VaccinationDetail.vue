<template>
  <div v-if="loaded" class="column items-center justify-center">
    <div style="max-width: 550px">
      <p class="q-pa-sm forcewrap">
        {{ vaccineRecord }}
      </p>
    </div>
    <div>
      <QRcode :text="vaccineRecord" />
    </div>
    <div>
      <AppButton text="Delete" :action="deleteRecord" color="red" />
    </div>
    <div>
      <AppButton text="Back" :action="goBack" />
    </div>
  </div>

  <div v-else class="row fullScreen items-center justify-center">
    <div class="column fullScreen items-center justify-center">
      <q-spinner color="primary" size="2rem" />
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import { useRouter } from "vue-router";
import { storage } from "../composables/handlers";
import QRcode from "../components/QRcode.vue";
import { vdata } from "../constants/constants";
import AppButton from "../components/AppButton.vue";

export default {
  components: { QRcode, AppButton },
  setup() {
    const router = useRouter();
    const vaccineRecord = ref(vdata);
    const loaded = ref(false);

    (async () => {
      let vaccine = JSON.stringify(await storage.get("vaccine")).slice(1, -1);
      if (vaccine == "ul") {
        vaccineRecord.value = "";
      } else {
        vaccineRecord.value = vaccine;
      }
      loaded.value = true;
    })();

    const goBack = () => {
      router.replace({ name: "Records" });
    };

    const deleteRecord = async () => {
      await storage.set("vaccine", "No Vaccination Record");
      goBack();
    };

    return { vaccineRecord, goBack, loaded, deleteRecord };
  },
};
</script>

<style scoped>
.headerText {
  font-size: 1rem;
  font-weight: bold;
}
.forcewrap {
  overflow-wrap: break-word;
  max-width: 100vw;
}

.fullScreen {
  height: 100%;
  width: 100%;
}
</style>