<template>
  <q-page class="flex flex-center">

        <json-viewer 
            ref=json_viewer
            :file="'/home/pete/prog/NefCom/logparser/demo/Drain_result/HDFS_2k.log_structured.json'" 
            />

  </q-page>
</template>

<style>
</style>

<script>
    import JsonViewer from "../components/JsonViewer.vue";
    const log = require("electron-log");
export default {
    name: 'PageIndex',
    components: {
        JsonViewer
    },
    mounted: function() {
    },
}
</script>
