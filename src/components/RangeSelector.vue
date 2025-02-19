<template>
  <v-card outlined>
    <div class="text-caption">RANGE</div>
    <v-card flat>
      <v-chip
        v-for="(range, i) in item.range"
        :key="`range_chip_${item.id}-${i}`"
        small
        class="mx-1"
        @click="edit(range, i)"
      >
        {{ range.val }} {{ range.type }}
      </v-chip>
      <v-menu
        v-model="menu"
        width="20em"
        :close-on-click="false"
        :close-on-content-click="false"
      >
        <template v-slot:activator="{ props }">
          <v-btn icon size="small" flat @click="addNew()" v-bind="props"
            ><v-icon>mdi-plus</v-icon></v-btn
          >
        </template>
        <v-card>
          <v-toolbar density="compact" color="pink darken-4" title="Add Range">
            <v-btn icon @click="menu = false"
              ><v-icon icon="mdi-close"
            /></v-btn>
          </v-toolbar>
          <v-card-text>
            <v-row justify="space-around" align="center">
              <v-col cols="7">
                <v-select
                  v-model="range.type"
                  item-text="name"
                  item-value="id"
                  label="Range"
                  :items="
                    rangeTypes.filter(
                      (x) => !item.range.map((y) => y.type).some((z) => z === x)
                    )
                  "
                  hide-details
                />
              </v-col>
              <v-col>
                <tiered-stat-input
                  v-if="npc"
                  v-model="range.val"
                  title="Value"
                />
                <v-text-field
                  v-else
                  v-model="range.val"
                  label="Value"
                  hide-details
                />
              </v-col>
            </v-row>
          </v-card-text>
          <v-divider />
          <v-card-actions>
            <v-btn
              v-show="isEdit"
              variant="tonal"
              color="error"
              @click="remove()"
              >delete</v-btn
            >
            <v-spacer />
            <v-btn
              color="success darken-2"
              :disabled="!range.type || !range.type"
              @click="submit"
            >
              {{ isEdit ? 'save' : 'confirm' }}
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-menu>
    </v-card>
  </v-card>
</template>

<script lang="ts">
import { rangeType } from '../assets/enums';
import TieredStatInput from './TieredStatInput.vue';

export default {
  components: { TieredStatInput },
  name: 'range-selector',
  props: { item: { type: Object, required: true }, npc: { type: Boolean } },
  data: () => ({
    menu: false,
    range: {} as any,
    isEdit: false,
    editIndex: -1,
    rangeTypes: rangeType,
  }),
  methods: {
    submit() {
      if (!this.range) return;
      if (!isNaN(this.range.val)) {
        this.range.val = Number(this.range.val);
      }
      if (this.isEdit) {
        this.item.range[this.editIndex] = this.range;
      } else {
        if (!this.item.range) this.item['range'] = [];
        this.item.range.push(this.range);
      }
      this.range = {};
      this.isEdit = false;
      this.editIndex = -1;
      this.menu = false;
    },
    addNew() {
      this.range = {};
      this.isEdit = false;
      this.editIndex = -1;
      this.menu = true;
    },
    edit(range: any, index: number) {
      if (!isNaN(this.range.val)) {
        this.range.val = Number(this.range.val);
      }
      this.range = { ...range };
      this.isEdit = true;
      this.editIndex = index;
      this.menu = true;
    },
    remove() {
      this.item.range.splice(this.editIndex, 1);
      this.menu = false;
      this.isEdit = false;
      this.editIndex = -1;
    },
  },
};
</script>
