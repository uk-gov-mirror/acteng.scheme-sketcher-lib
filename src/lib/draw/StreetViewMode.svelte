<script lang="ts" generics="F, S">
  import { StreetViewHelp, StreetViewTool } from "$lib/common";
  import { IconButton } from "govuk-svelte";
  import streetViewIcon from "$lib/assets/street_view.svg";
  import { mode } from "$lib/draw/stores";

  import { type Config, map } from "$lib/config";

  export let cfg: Config<F, S>;

  let enabled = false;
  function toggleEnabled() {
    enabled = !enabled;
  }

  $: $mode = enabled ? { mode: "list" } : $mode;
</script>

<div class="street-view">
  <StreetViewTool {cfg} map={$map} bind:enabled showControls={false} />

  <IconButton on:click={toggleEnabled}>
    <img src={streetViewIcon} alt="StreetView" />
    StreetView
  </IconButton>
  <StreetViewHelp />
</div>

<style>
  .street-view {
    position: absolute;
    bottom: 10px;
    right: 10px;
    width: 90%;
    background-color: white;
    border: 1px solid black;
    padding: 16px;
  }
</style>
