<script lang="ts">
  import { ComponentCore, SingleContainer, SingleContainerConfigInterface, Tooltip } from '@unovis/ts'
  import { onMount, onDestroy, setContext } from 'svelte'

  type Data = $$Generic

  export let data: Data

  let chart: SingleContainer<Data>
  let config: SingleContainerConfigInterface<Data> = {}
  let ref: HTMLDivElement

  $: chart?.setData(data, true)
  $: if (chart?.component) chart.updateContainer({ ...config, ...($$restProps as SingleContainerConfigInterface<Data>) })

  onMount(() => {
    chart = new SingleContainer<Data>(ref)
  })

  onDestroy(() => {
    chart?.destroy()
  })

  const updateConfig = (c: SingleContainerConfigInterface<Data>) => {
    config = { ...config, ...c }
    chart?.update(config)
  }

  setContext('container', {
    setTooltip: (t: Tooltip) => updateConfig({ tooltip: t }),
    setComponent: (c: ComponentCore<Data>) => {
      updateConfig({ component: c })
      if (data) chart.setData(data)
    },
    removeComponent: (_: ComponentCore<Data>) => {
      updateConfig({ component: undefined })
    },
  })

</script>

<vis-single-container bind:this={ref} class='unovis-single-container'>
  {#if chart}
    <slot/>
  {/if}
</vis-single-container>

<style>
  .unovis-single-container {
    display: block;
    position: relative;
    width: 100%;
    height: 100%;
  }
</style>
