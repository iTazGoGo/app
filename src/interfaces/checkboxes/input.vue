<template>
  <div class="interface-checkboxes">
    <v-checkbox
      v-for="(label, val) in options.choices"
      :id="label"
      :key="label"
      :value="name + '-' + val"
      :disabled="readonly"
      :label="label"
      :checked="selection.includes(val)"
      @change="updateValue(val, $event)"
    ></v-checkbox>
  </div>
</template>

<script>
import mixin from "@directus/extension-toolkit/mixins/interface";

export default {
  name: "interface-checkboxes",
  mixins: [mixin],
  computed: {
    selection() {
      if (this.value == null) return [];

      let selection;

      // Conver the value to an array
      if (typeof this.value === "string") {
        if (this.value.includes(",")) {
          selection = selection.split(",");
        } else {
          selection = [this.value];
        }
      } else {
        selection = this.value;
      }

      if (this.options.wrap && selection.length > 2) {
        selection.pop();
        selection.shift();
      }

      return selection;
    }
  },
  methods: {
    updateValue(val) {
      let selection = [...this.selection];

      if (selection.includes(val)) {
        selection.splice(selection.indexOf(val), 1);
      } else {
        selection.push(val);
      }

      selection = selection.join(",");

      if (this.options.wrap && selection.length > 0) {
        selection = `,${selection},`;
      }

      if (this.type === "array") {
        selection = selection.split(",");
      }

      this.$emit("input", selection);
    }
  }
};
</script>

<style lang="scss" scoped>
.interface-checkboxes {
  max-width: var(--width-x-large);
  display: grid;
  grid-gap: 20px;
  grid-template-columns: repeat(4, 1fr);
}
</style>
