<template>
  <v-navigation-drawer mini-variant permanent app class="d-print-none">
    <v-list-item class="px-2">
      <v-list-item-avatar>
        <v-img src="favicon.png"></v-img>
      </v-list-item-avatar>
    </v-list-item>

    <v-divider></v-divider>

    <v-list dense>
      <v-list-item
        v-for="item in items"
        :key="item.component"
        @click="$root.$emit('toolbarEvent', item.type)"
      >
        <v-list-item-icon>
          <v-tooltip bottom>
            <template v-slot:activator="{ on, attrs }">
              <v-icon v-bind="attrs" v-on="on">{{ item.icon }}</v-icon>
            </template>
            <span>{{ item.tooltip }}</span>
          </v-tooltip>
        </v-list-item-icon>
      </v-list-item>
    </v-list>
  </v-navigation-drawer>
</template>
<script>
export default {
  data() {
    return {
      items: [
        { tooltip: "Add Circle", icon: "mdi-circle", type: "circle" },
        { tooltip: "Add Rectangle", icon: "mdi-rectangle", type: "rect" },
        { tooltip: "Add Text", icon: "mdi-format-text", type: "text" },
        { tooltip: "Delete Shape", icon: "mdi-delete", type: "delete" },
        {
          tooltip: "Load Last Saved Overlay",
          icon: "mdi-page-layout-body",
          type: "load",
        },
        { tooltip: "Print", icon: "mdi-printer", type: "print" },
      ],
    };
  },
};
</script>