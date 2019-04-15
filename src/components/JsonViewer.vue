<template>
    <div class="d-flex fit " >
    <q-card
        dark>

      <q-card-separator />

      <q-card-main>
        <q-table
            :title="file"
            :data="tableData"
            :columns="columns"
            row-key="name"
            dense
          />
          <q-tr slot="body" slot-scope="props" :props="props" @click.native="rowClick(props.row)" class="cursor-pointer" style="background-color: #05ffb0;">
            <q-td v-for="col in props.cols" :key="col.name" :props="props">
               {{ col.value }} 
            </q-td>
          </q-tr>
      </q-table>

  <!--
        <vue-scrolling-table >
          <template slot="thead">
            <tr>
              <th v-for="field in file_json.schema.fields"
                  :key="field.name">

                  {{ field.name }}

              </th>
            </tr>

          </template>

          <template slot="tbody">
            <tr v-for="item in file_json.data" :key="item.index">
              <td v-for="field in file_json.schema.fields"
                  :key="field.name">
                {{ item[field.name] }}
              </td>

            </tr>
          </template>
        </vue-scrolling-table>
    -->
      </q-card-main>

    </q-card>

	</div>
</template>

<style>
.q-card {
  height: 100%;
  width: 100%;
}


</style>

<script>
    const VueScrollingTable = require("vue-scrolling-table").default;
    const log = require("electron-log");
    const remote = require('electron').remote;
    const Mousetrap = require('mousetrap');


	//const fs = __non_webpack_require__("fs");
    const fs = remote.require('fs');

    log.info('Hello, log');

	export default {
		components: {
            VueScrollingTable,
		},
		props: [
			'file',
		],
		data() {
			return {
                columns: [
                  ],

                tableData: [],

				logLevels: ["Debug", "Info", "Warning", "Error", "Fatal"],
                file_json: null,
                offset: 0,
                count: 30,
			}
		},
		mounted: function() {
            //var fs = require('fs');
            let file_data = fs.readFileSync(this.file);
            this.file_json = JSON.parse(file_data);
            log.info("mounted");
            //log.info(this.file_json);
            
            var fields = this.file_json.schema.fields


            for(var field in fields) {
                log.info(fields[field].name);
                this.columns.push({
                    name: fields[field].name,
                    required: true,
                    label: fields[field].name,
                    align: 'left',
                    field: fields[field].name,
                    sortable: true,
                    classes: 'my-class',
                    //style: 'width: 500px'
                });
            }

            this.setData(this.offset, this.count);

            Mousetrap.bind('ctrl+e', function() { 
                log.info(this.offset, this.count);
                this.scrollDown(1);
            }.bind(this));
            
            Mousetrap.bind('ctrl+y', function() { 
                log.info(this.offset, this.count);
                this.scrollUp(1);
            }.bind(this));


/*
			this.clusterize = new Clusterize({
				scrollElem: this.$refs.logLinesScroll,
				contentElem: this.$refs.logLinesContent,
				show_no_data_row: false
			});

			this.startTail();
            */
		},
		beforeDestroy: function() {
		},
		methods: {
            scrollUp: function(count) {
                var data = this.file_json.data

                if(this.offset >= count) {
                    return;
                }

                this.offset = this.offset - count;
                
                var dataView = data.slice(this.offset, this.offset + this.count);
                this.tableData = dataView;
            },

            scrollDown: function(count) {
                var data = this.file_json.data

                if(this.offset + count + this.count >= data.length - 1) {
                    return;
                }

                this.offset = this.offset + count;
                
                var dataView = data.slice(this.offset, this.offset + this.count);
                this.tableData = dataView;
            },

            setData: function(offset, count) {

                log.info(offset);
                var fields = this.file_json.schema.fields
                var data = this.file_json.data
                
                var dataView = data.slice(offset, offset + count);

                this.tableData = dataView;

                /*
                this.tableData = [];
                for(var d in dataView) {
                    log.info(dataView[d]);
                    this.tableData.push(dataView[d]);

                }
                */
            }
        }

	}
</script>
