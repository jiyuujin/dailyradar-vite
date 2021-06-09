<template>
  <section>
    <h1>Daily Issues</h1>
    <div v-if="loading">
      {{ `Loading...` }}
    </div>
    <div v-else class="issues">
      <div
        v-for="(label, index) in Object.keys(counts)"
        :key="index"
        class="issue"
      >
        <a
          :href="`https://github.com/jiyuujin/dailyradar/labels/${label}`"
          target="_blank"
          rel="noopener noreferrer"
        >
          {{ `${label} (${counts[label]})` }}
        </a>
      </div>
    </div>
  </section>
</template>

<script lang="ts">
import { computed } from 'vue'
import { useQuery, useResult } from '@vue/apollo-composable'
import { searchQuery } from '../graphql/issue'
import { getLabels, getCounts, sortCounts } from '../services/utilService'
import { IssuesType } from '../services/utilService'

export default {
  setup() {
    const { result, error, loading } = useQuery(searchQuery)
    const issues = useResult(
      result,
      null,
      (data) => data.viewer.repository?.issues?.nodes
    )
    const counts = computed(() => {
      const labels = getLabels(issues.value as IssuesType)
      return sortCounts(getCounts(labels))
    })
    return { loading, error, counts }
  }
}
</script>
