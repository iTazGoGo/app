<template>
  <header class="v-header" :class="{ scrolled }">
    <button :disabled="navActive" class="nav-toggle" @click="activateNav">
      <i class="material-icons">menu</i>
    </button>
    <div class="title" :class="{ 'has-breadcrumb': navBreadcrumb }">
      <ol class="breadcrumb" v-if="navBreadcrumb">
        <li
          v-for="{ name, path, color = null } in navBreadcrumb"
          :key="path"
          class="breadcrumb-item"
        >
          <router-link
            :to="path"
            :style="{ color: color ? `var(--${color})` : null }"
          >
            {{ name }}
          </router-link>
        </li>
      </ol>

      <div class="flex">
        <h1>{{ title || currentPage.name }}</h1>
        <slot name="title" />
      </div>
    </div>
    <slot />
    <v-header-button
      v-if="infoToggle && !itemDetail"
      :label="$t('info')"
      icon="info"
      no-background
      @click="toggleInfo"
    />
    <v-header-button
      v-if="infoToggle && itemDetail"
      :label="$t('info')"
      icon="info"
      no-background
      class="info-mobile"
      @click="toggleInfo"
    />
    <slot name="buttons" />
  </header>
</template>

<script>
import { TOGGLE_NAV, TOGGLE_INFO } from "../../store/mutation-types";

export default {
  name: "v-header",
  props: {
    title: {
      type: String,
      default: null
    },
    breadcrumb: {
      type: Array,
      default: null
    },
    infoToggle: {
      type: Boolean,
      default: false
    },
    itemDetail: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      scrolled: false
    };
  },
  computed: {
    defaultBreadcrumb() {
      const routeParts = this.$route.path.split("/").filter(name => name);
      return routeParts.map((part, i) => {
        let url = "";
        for (let x = 0; x < i; x++) {
          url += `/${routeParts[x]}`;
        }
        url += `/${part}`;
        return {
          name: this.$helpers.formatTitle(part),
          path: url
        };
      });
    },
    navActive() {
      return this.$store.state.sidebars.nav;
    },

    // The last part of the breadcrumb, rendered as a bigger title
    currentPage() {
      const breadcrumb = this.breadcrumb || this.defaultBreadcrumb;
      return breadcrumb[breadcrumb.length - 1];
    },

    // The parts of the breadcrumb that make up the navigation. Does not include the last item, as
    // that's being returned by this.currentPage()
    navBreadcrumb() {
      const breadcrumb = this.breadcrumb || this.defaultBreadcrumb;
      // We need to clone the array, otherwise the pop from below will modify the original passed
      // in array
      const breadcrumbClone = [...breadcrumb];
      breadcrumbClone.pop();
      return breadcrumbClone.length > 0 ? breadcrumbClone : null;
    }
  },
  methods: {
    activateNav() {
      this.$store.commit(TOGGLE_NAV, true);
    },
    toggleInfo() {
      this.$store.commit(TOGGLE_INFO);
    },

    checkIfScrolled() {
      const scrollPos = window.scrollY;
      this.scrolled = scrollPos > 0;
    }
  },

  created() {
    window.addEventListener("scroll", this.checkIfScrolled);
  },

  beforeDestroy() {
    window.removeEventListener("scroll", this.checkIfScrolled);
  }
};
</script>

<style lang="scss">
body.info-active .v-header {
  padding-right: 316px !important;
}

body.info-wide-active .v-header {
  padding-right: 316px !important;
}
</style>

<style scoped lang="scss">
.v-header {
  background-color: var(--white);
  position: fixed;
  width: 100%;
  right: 0;
  top: 0;
  height: 4.286rem;
  color: var(--black);
  display: flex;
  align-items: center;
  z-index: 20;
  padding-left: 32px;
  padding-right: 32px;

  @media (min-width: 800px) {
    padding-left: calc(var(--nav-sidebar-width) + 20px);
  }

  .title {
    flex-grow: 1;
  }

  .nav-toggle {
    background-color: transparent;
    border: none;
    border-radius: 0;
    padding: 0;
    margin-right: 20px;
    cursor: pointer;
    transition: opacity 140ms var(--transition);

    &:hover {
      opacity: 0.6;
    }

    @media (min-width: 800px) {
      display: none;
    }
  }

  h1 {
    color: var(--black);
    font-size: 22px;
  }

  .breadcrumb {
    color: var(--black);
    list-style: none;
    padding: 0;

    li {
      display: inline-block;
    }

    a {
      text-decoration: none;
      color: var(--light-gray);
      transition: color var(--fast) var(--transition);
    }

    a:hover {
      color: var(--dark-gray);
    }
  }

  .breadcrumb-item + .breadcrumb-item::before {
    content: "chevron_right";
    color: var(--lighter-gray);
    font-family: "Material Icons";
    font-weight: normal;
    font-style: normal;
    font-size: 12px;
    display: inline-block;
    line-height: 1;
    text-transform: none;
    letter-spacing: normal;
    word-wrap: normal;
    white-space: nowrap;
    font-feature-settings: "liga";
    vertical-align: middle;
  }

  .flex {
    display: flex;
  }
}

.scrolled {
  border-bottom: 2px solid var(--lightest-gray);
  height: calc(4.286rem + 2px);
}

.info-mobile {
  @media (min-width: 1235px) {
    visibility: hidden;
  }
}
</style>

<style>
body {
  padding-top: var(--header-height);
}
</style>
