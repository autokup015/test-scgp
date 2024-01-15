<template>
  <div>
    <b-modal
      id="modal-add-newdata"
      body-class="p-0"
      hide-header
      hide-footer
      no-close-on-backdrop
    >
      <div class="modal-add-newdata">
        <div class="header">
          <h5>Add new data</h5>
        </div>
        <div class="body">
          <b-row>
            <b-col cols="4">Material :</b-col>
            <b-col cols="8">
              <div class="g-form">
                <div class="input">
                  <b-form-input
                    v-model="form.Material"
                    @keyup.enter="onCheckData"
                  >
                  </b-form-input>
                  <b-icon
                    class="icon"
                    icon="search"
                    scale="0.8"
                    @click="onCheckData"
                  ></b-icon>
                </div>
                <p class="g-text error" v-if="$v.form.Material.$error">
                  Please fill material
                </p>
                <p class="g-text error">
                  {{ textError.material }}
                </p>
              </div>
            </b-col>
            <b-col cols="4">ProductCode :</b-col>
            <b-col cols="8"> {{ form.ProductCode }} </b-col>
            <b-col cols="4">Description :</b-col>
            <b-col cols="8"> {{ form.Description }} </b-col>
            <b-col cols="12">
              <p class="g-text error" v-if="$v.form.$error">
                Data is not correct
              </p>
            </b-col>
          </b-row>
        </div>
        <div class="footer-modal">
          <div class="box-footer">
            <b-button class="g-btn" @click="onSave" variant="success"
              >Add</b-button
            >
            <div class="mx-1"></div>
            <b-button class="g-btn" @click="closeModal" variant="danger"
              >Cancel</b-button
            >
          </div>
        </div>
      </div>
    </b-modal>
  </div>
</template>

<script>
import { required } from "vuelidate/lib/validators";
export default {
  props: {
    newItems: {
      type: Array,
      default: [],
    },
    data: {
      type: Array,
      default: [],
    },
    maxLocation: {
      type: Number,
      default: 0,
    },
  },
  data() {
    return {
      form: {
        Material: "",
        ProductCode: "",
        Description: "",
      },

      // state error
      textError: {
        material: "",
      },
    };
  },
  validations() {
    const material = { required };

    const form = {
      Material: { required },
      ProductCode: { required },
      Description: { required },
    };

    return {
      material,
      form,
    };
  },
  methods: {
    async onCheckData() {
      this.$v.form.Material.$touch();

      if (this.$v.form.Material.$error) {
        return;
      }

      // clear error
      this.textError.material = "";

      const findDuplicate = this.newItems.some((x) => {
        return x.name.toLowerCase().match(this.form.Material.toLowerCase());
      });

      if (findDuplicate) {
        // duplicate
        this.textError.material = "Data is duplicate";
        return;
      }

      this.form = await this.mockApi();
    },

    onSave() {
      this.$v.form.$touch();

      if (this.$v.form.$error) {
        return;
      }

      let { Material, ProductCode } = this.form;

      let finalAddData = [];
      let mockCount = this.data.length + 1;

      for (let i = 0; i < this.maxLocation; i++) {
        const index = i + 1;

        let newObj = {
          ID: mockCount,
          Material,
          ProductCode,
          Location: `A${index}`,
          QTY: 0,
        };

        finalAddData.push(newObj);

        mockCount++;
      }

      this.$emit("addNewData", finalAddData);
      this.closeModal();
    },
    closeModal() {
      this.clearForm();
      this.$bvModal.hide("modal-add-newdata");
    },
    clearForm() {
      this.material = "";

      this.form = {
        material: "",
        productCode: "",
        description: "",
      };

      this.$v.material.$reset();
      this.$v.form.$reset();
    },

    mockApi() {
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          let str = "" + (this.newItems.length + 1);

          let pad = "0000";
          let pad2 = "000000";

          let newMaterial = pad.substring(0, pad.length - str.length) + str;
          let newProductCode =
            pad2.substring(0, pad2.length - str.length) + str;

          const finalMaterialMock = `MAT${newMaterial}`;
          resolve({
            Material: finalMaterialMock,
            ProductCode: `PC${newProductCode}`,
            Description: "XXXXXXXXXXXX XXXX",
          });
        }, 500);
      });
    },
  },
};
</script>

<style lang="scss" scoped>
.modal-add-newdata {
  padding: 20px;

  .body {
    padding: 20px 0px;
  }

  .footer-modal {
    .box-footer {
      display: flex;
    }
  }
}

// ! OVERRIDE

.g-btn {
  width: 100%;
}
</style>
