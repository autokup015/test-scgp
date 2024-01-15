<template>
  <div class="homepage">
    <b-row>
      <b-col cols="6">
        <div class="g-form">
          <div class="input">
            <h5 class="text">Material:</h5>
            <b-form-input
              ref="searchValue"
              placeholder="xxx"
              @keyup.enter="onSearch"
            >
            </b-form-input>
          </div>
        </div>
      </b-col>
      <b-col cols="6">
        <div class="search">
          <b-button class="g-btn" variant="outline-primary" @click="onSearch">
            Search
            <b-icon icon="search" scale="0.7"></b-icon>
          </b-button>
        </div>
      </b-col>
    </b-row>

    <div>
      <TableComponent
        :items="newItems"
        :fields="fields"
        :maxLocation="maxLocation"
        :newID="newDataId"
        @addNewLocationData="addNewLocationData"
      >
        <template #headerTable>
          <div class="table-add">
            <b-button
              class="g-btn add"
              variant="outline-primary"
              @click="openModalAddData"
              >Add</b-button
            >
          </div>
        </template>
      </TableComponent>
    </div>
    <div class="footer-save">
      <b-button class="g-btn save" variant="outline-primary" @click="onSave"
        >save</b-button
      >
    </div>

    <ModalNewData
      :newItems="newItems"
      :data="data"
      :maxLocation="maxLocation"
      @addNewData="addNewData"
    />
  </div>
</template>

<script>
// data
import DataMock from "@/assets/json/mockdata.json";

// component
import TableComponent from "@/components/table/TableComponent.vue";
import ModalNewData from "@/components/modal/ModalNewData.vue";

export default {
  components: {
    TableComponent,
    ModalNewData,
  },
  data() {
    return {
      // state
      data: [],
      search: "",

      // state table
      maxLocation: 0,
      fields: [{ key: "name", label: "Material", class: "text-center" }],
      items: [],
    };
  },
  computed: {
    newDataId() {
      let cloneArr = [...this.data];
      const findNewId = cloneArr.sort((a, b) => {
        return b.ID - a.ID;
      });

      return findNewId[0]?.ID + 1 || 0;
    },
    newItems() {
      let newArr = [];

      this.data.forEach((x) => {
        const findData = newArr.find((y) => {
          return y.name == x.Material;
        });

        // no data
        if (!findData) {
          const newData = {
            name: x.Material,
            data: [x],
          };

          newArr.push(newData);
        } else {
          // have data

          findData.data = [...findData.data, x];
        }
      });

      let finalArr = newArr.filter((x) => {
        return x.name.match(this.search);
      });

      return finalArr;
    },
  },
  mounted() {
    this.getDataTable();
  },
  methods: {
    getDataTable() {
      const mapLocation = DataMock.data.map((x) => {
        return x.Location.replace("A", "");
      });

      let findMaxLocation = parseInt(
        mapLocation.sort((a, b) => {
          return b - a;
        })[0]
      );

      const arr = [];

      for (let i = 0; i < findMaxLocation; i++) {
        const numberLocation = 1 + i;

        const newLocationKey = {
          key: `A${numberLocation}`,
          lable: `A${numberLocation}`,
        };

        arr.push(newLocationKey);
      }

      this.maxLocation = findMaxLocation;

      this.fields = [
        ...this.fields,
        { key: "data", label: "data" },
        { key: "summary", lable: "Summary", class: "text-center" },
      ];
      // this.fields = [
      //   ...this.fields,
      //   ...arr,
      //   { key: "summary", lable: "Summary" },
      // ];

      // ? ==========================================================================================

      this.data = [...DataMock.data];

      let arrTest = [];

      DataMock.data.forEach((x) => {
        const findData = arrTest.find((y) => {
          return y.name == x.Material;
        });

        // no data
        if (!findData) {
          const newData = {
            name: x.Material,
            data: [x],
          };

          arrTest.push(newData);
        } else {
          // have data

          findData.data = [...findData.data, x];
        }
      });

      this.items = arrTest;
    },
    onSearch() {
      const getSearch = this.$refs.searchValue.localValue;

      this.search = getSearch;
    },
    addNewData(obj) {
      this.data = [...this.data, ...obj];
    },
    onSave() {
      console.log("this.data :>> ", this.data);
    },
    openModalAddData() {
      this.$bvModal.show("modal-add-newdata");
    },
    addNewLocationData(obj) {
      const finalData = [...this.data, obj];

      this.data = finalData;
    },
  },
};
</script>

<style lang="scss" scoped>
.homepage {
  max-width: 1380px;
  margin: auto;
  padding: 20px;

  .search {
    text-align: right;
  }

  // table component

  .table-add {
    padding: 10px 0px;
    text-align: right;
  }

  .footer-save {
    text-align: right;
  }
}

// ! OVERRIDE

.g-btn {
  &.add {
    min-width: 100px;
  }

  &.save {
    min-width: 100px;
  }
}
</style>
