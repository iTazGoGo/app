<template>
  <v-popover
    v-if="value && value.length > 0 && options.template"
    trigger="hover"
  >
    <div class="readonly-many-to-many">
      {{
        $tc("item_count", (value || []).length, { count: (value || []).length })
      }}
    </div>

    <template slot="popover">
      <ul class="list">
        <li v-for="(val, i) in value" :key="i">{{ render(val) }}</li>
      </ul>
    </template>
  </v-popover>

  <div v-else class="readonly-many-to-many">
    {{
      $tc("item_count", (value || []).length, { count: (value || []).length })
    }}
  </div>
</template>

<script>
import mixin from "@directus/extension-toolkit/mixins/interface";

export default {
  name: "readonly-one-to-many",
  mixins: [mixin],
  methods: {
    render(val) {
      return this.$helpers.micromustache.render(this.options.template, val);
    }
  }
};
</script>

<style lang="scss" scoped>
.list {
  max-height: 200px;
  overflow-y: scroll;

  list-style: none;
  padding: 0;

  li {
    color: var(--dark-gray);
    padding: 8px 0;

    &:not(:last-of-type) {
      border-bottom: 1px solid var(--lightest-gray);
    }
  }
}
</style>
