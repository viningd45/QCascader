<template>
  <div id="q-cascader">
    <div v-bind:style="maxWidth">
      <q-select
        v-model="model"
        :filled="filled"
        :options="options"
        v-bind:label="label"
        behavior="menu"
        multiple
        ref="baseSelect"
        @input="emitChange"
        :bg-color="bgColor"
        :label-color="labelColor"
        :color="color"
        :dark="dark"
        :outlined="outlined"
        :borderless="borderless"
        :standout="standout"
        :clearable="clearable"
        :rounded="rounded"
        :square="square"
        :dense="dense"
        :item-aligned="itemAligned"
        :popup-content-class="popupContentClass"
        :popup-content-style="popupContentStyle"
        :options-dense="optionsDense"
        :options-selected-class="optionsSelectedClass"
        :virtual-scroll-item-size="300"
        :display-value="displayValue"
      >
        <template v-slot:option="scope">
          <q-item
            v-bind="scope.itemProps"
            v-on="scope.itemEvents"
            @click="showPopup(scope.opt)"
          >
            <q-item-section>{{scope.opt.label}}</q-item-section>
            <q-cascader-child
              v-if="scope.opt.children"
              :width="width"
              :options="scope.opt.children"
              @input="emitChildChange"
              :ref="scope.opt.label"
              :bg-color="bgColor"
              :label-color="labelColor"
              :color="color"
              :dark="dark"
              :standout="standout"
              :rounded="rounded"
              :square="square"
              :dense="dense"
              :item-aligned="itemAligned"
              :popup-content-class="popupContentClass"
              :popup-content-style="popupContentStyle"
              :options-dense="optionsDense"
              :options-selected-class="optionsSelectedClass"
            />
          </q-item>
        </template>
      </q-select>
    </div>
  </div>
</template>
<script>
import child from "./q-cascader-child";

export default {
  components: {
    "q-cascader-child": child
  },
  props: {
    options: Array,
    value: Array,
    label: String,
    width: {
      type: String,
      default: "150"
    },
    labelColor: String,
    bgColor: String,
    color: String,
    dark: Boolean,
    filled: Boolean,
    outlined: Boolean,
    borderless: Boolean,
    standout: [String, Boolean],
    rounded: Boolean,
    square: Boolean,
    dense: Boolean,
    optionsDense: Boolean,
    itemAligned: Boolean,
    popupContentClass: String,
    popupContentStyle: [Array, String, Object],
    clearable: Boolean,
    optionsSelectedClass: String
  },
  data: function() {
    return {
      model: [],
      modelCopy: []
    };
  },
  methods: {
    emitChange() {
      if (this.model) {
        // check to handle clearable case which sets model: null, ensures that a non-null value is returns
        if (
          this.model[0] &&
          !this.options.find(x => x.label === this.model[0].label)
        ) {
          // if item at first index is not in base list of options, replace it with last copy
          this.model = [];
          this.model.push(this.modelCopy[0]);
        }
        if (this.model.length === 0) {
          // force option to stay selected
          this.model = [];
          this.model.push(this.modelCopy[0]);
        } else {
          if (
            this.options.find(
              x => x.label === this.model[this.model.length - 1].label
            )
          ) {
            // if new item in base list selected, replace initial selection
            var last = this.model[this.model.length - 1];
            this.model = [];
            this.model.push(last);
          }
        }
      } else {
        this.model = [];
      }
      this.modelCopy = Object.assign({}, this.model);
      this.$emit("input", this.model);
    },
    emitChildChange(newVal) {
      this.model.splice(1);
      this.model = this.model.concat(newVal); // concat value from child onto first index of base
      this.$emit("input", this.model);
    },
    showPopup(val) {
      if (this.$refs[val.label]) {
        var optionIndex = this.options.findIndex(x => x.id === val.id)
        this.$refs[val.label].showPopup(optionIndex);
      } 
    }
  },
  computed: {
    maxWidth: function() {
      var ret = `max-width: ${this.width};`;
      return ret;
    },
    displayValue: function() {
      var ret = "";
      var sanitizedModel = this.model.map(x => x.value)
      for (var i = 0; i < sanitizedModel.length; i++) {
        if (i === sanitizedModel.length - 1) ret += sanitizedModel[i];
        else ret += sanitizedModel[i] + " / ";
      }
      return ret;
    }
  },
  name: "q-cascader"
};
</script>