<template>
  <div id="q-casder-child">
    <q-item-section side>
      <q-select
        :options-selected-class="optionsSelectedClass"
        behavior="menu"
        multiple
        borderless
        :options="options"
        v-model="model"
        ref="self"
        :menu-offset="dense ? [-63, (index + 1) * -42 - (2 * index)] : [-69, (index + 1) * -64 - (8 * index)]"
        :fill-input="false"
        hide-selected
        @input="emitChange"
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
      >
        <template v-slot:option="scope">
          <div v-bind:style="maxWidth">
            <q-item
              v-bind="scope.itemProps"
              v-on="scope.itemEvents"
              @click="showChildPopup(scope.opt)"
            >
              <q-item-section>{{scope.opt.label}}</q-item-section>
              <q-cascader-child
                v-if="scope.opt.children"
                :ref="scope.opt.label"
                :options="scope.opt.children"
                :width="width"
                @input="emitChildChange"
                :bg-color="bgColor"
                :label-color="labelColor"
                :color="color"
                :dark="dark"
                :standout="standout"
                :rounded="rounded"
                :square="square"
                :options-selected-class="optionsSelectedClass"
                :options-dense="optionsDense"
                :dense="dense"
                :item-aligned="itemAligned"
                :popup-content-class="popupContentClass"
                :popup-content-style="popupContentStyle"
              />
            </q-item>
          </div>
        </template>
      </q-select>
    </q-item-section>
  </div>
</template>
<script>
export default {
  props: {
    options: Array,
    value: Array,
    width: String,
    labelColor: String,
    bgColor: String,
    color: String,
    dark: Boolean,
    standout: [String, Boolean],
    rounded: Boolean,
    square: Boolean,
    dense: Boolean,
    optionsDense: Boolean,
    itemAligned: Boolean,
    popupContentClass: String,
    popupContentStyle: [Array, String, Object],
    optionsSelectedClass: String
  },
  data: function() {
    return {
      model: [],
      modelCopy: [],
      index: 0
    };
  },
  methods: {
    emitChange() {
      if (
        this.model[0] &&
        !this.options.find(x => x.label === this.model[0].label)
      ) {
        this.model = [];
        this.model.push(this.modelCopy[0]);
      }
      if (this.model.length === 0) this.model.push(this.modelCopy[0]);
      else {
        if (
          this.options.find(
            x => x.label === this.model[this.model.length - 1].label
          )
        ) {
          var last = this.model[this.model.length - 1];
          this.model = [];
          this.model.push(last);
        }
      }
      this.modelCopy = Object.assign({}, this.model);
      this.$emit("input", this.model);
    },
    emitChildChange(newVal) {
      this.model.splice(1);
      this.model = this.model.concat(newVal);
      this.$emit("input", this.model);
    },
    showPopup(index) {
      this.index = index
      this.$refs.self.showPopup();
    },
    showChildPopup(val) {
      if (this.$refs[val.label]) {
        var optionIndex = this.options.findIndex(x => x.id === val.id)
        this.$refs[val.label].showPopup(optionIndex);
      }
    }
  },
  computed: {
    maxWidth: function() {
      var ret = `width: ${this.width};`;
      return ret;
    }
  },
  name: "q-cascader-child"
};
</script>
                 