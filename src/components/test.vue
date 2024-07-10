<template>
  <a-table :loading="loading" :data-source="data" :columns="columns">

    <template #bodyCell="{ column, record }">
      <template v-if="column.key === 'status_name'">
        <a-tag
            :color="record.status_color"
            style="color: black"
        >
          {{record.status_name}}
        </a-tag>
      </template>

      <template v-else-if="column.key === 'contacts'">
        <a-collapse
            v-for="contact in record._embedded.contacts"
        >
          <a-collapse-panel :header="contact.name">
            <template v-for="field in contact.custom_fields_values">
              <p>{{field.field_name}}</p>
              <ul><li v-for="value in field.values">{{value.value}}</li></ul>
            </template>
          </a-collapse-panel>
        </a-collapse>
      </template>

    </template>
  </a-table>
</template>

<script lang='ts'>
import { API_URL } from '@/consts/app.consts';
import { LeadsResponse } from '@/responses/leads.response';

export default {
  data() {
    let data: LeadsResponse[] = [];
    return {
      data,
      columns: [
        {
          title: 'Created By',
          dataIndex: 'created_by_name',
          key: 'created_by_name',
        },
        {
          title: 'Status',
          dataIndex: 'status_name',
          key: 'status_name',
        },
        {
          title: 'Contacts',
          dataIndex: '_embedded["contacts"]',
          key: 'contacts'
        }
      ],
      loading: true
    }
  },
  methods: {
    async getData() {
      this.loading = true;
      const data = await fetch(`${API_URL}/api/leads`);
      this.data = await data.json();
      this.loading = false;
    }
  },
  beforeMount() {
    this.getData();
  },
}
</script>