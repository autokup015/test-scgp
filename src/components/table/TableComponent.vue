<template>
  <div>
    <slot name="headerTable"> </slot>

    <b-table
      :items="items"
      :fields="fields"
      show-empty
      empty-text="No Data naja"
    >
      <!-- header -->

      <template #head(data)>
        <div id="listID" class="box-listID">
          <div class="listID" v-for="(item, index) in maxLocation" :key="index">
            <p>A{{ item }}</p>
          </div>
        </div>
      </template>

      <!-- body -->
      <template #cell(Material)="{ item }">
        <p>
          {{ item.Material }}
        </p>
      </template>

      <template #cell(data)="{ item }">
        <div id="listID" class="box-listID">
          <div
            class="listID"
            v-for="(count, index) in maxLocation"
            :key="index"
            :id="`A${count}`"
          >
            <b-form-input
              type="number"
              :name="`A${count}`"
              :value="handdleQTY(count, item)"
              @change="handdleChangeQTY(count, item, $event)"
              @keydown.tab.prevent="handdleTapKey(count, item)"
            >
            </b-form-input>
            <!-- value : {{handdleQTY(count, item)}} -->
          </div>
        </div>
      </template>

      <template #cell(summary)="{ item }">
        <h5 class="g-text green">
          {{ calculateSummary(item.data) }}
        </h5>
      </template>
    </b-table>
  </div>
</template>

<script>
export default {
  props: {
    items: {
      type: Array,
      default: [],
      required: true,
    },
    fields: {
      type: Array,
      default: [],
      required: true,
    },
    maxLocation: {
      type: Number,
      default: 0,
    },
    newID: {
      type: Number,
      default: 0,
    },
  },
  methods: {
    handdleChangeQTY(index, item, $event) {
      let findData = item.data.find((x) => {
        return x.Location == `A${index}`;
      });

      if (findData) {
        findData.QTY = parseInt($event ? $event : 0);
      } else {
        const newData = {
          ID: this.newID,
          Material: item.data[0].Material,
          ProductCode: item.data[0].ProductCode,
          Location: `A${index}`,
          QTY: parseInt($event ? $event : 0),
        };
        this.$emit("addNewLocationData", newData);
      }
    },
    handdleQTY(index, item) {
      let findData = item.data.find((x) => {
        return x.Location == `A${index}`;
      });

      if (findData) {
        return findData.QTY;
      } else {
        return 0;
      }
    },
    handdleTapKey(index, item) {
      const getInput = document.getElementsByName(`A${index}`);
      const positionInput = parseInt(item.name.replace("MAT", ""));

      if (positionInput == this.items.length) {
        // หมดแถวแล้ว

        const newInputFocus = document.getElementsByName(`A${index + 1}`);

        if (newInputFocus.length > 0) {
          newInputFocus[0].focus();
        }
      } else {
        getInput[positionInput].focus();
      }
    },
    calculateSummary(data) {
      const sum = data.reduce((a, b) => {
        return a + b.QTY;
      }, 0);

      return sum;
    },
  },
};
</script>

<style lang="scss" scoped>
.box-listID {
  display: flex;

  .listID {
    width: 100%;
  }
}
</style>
