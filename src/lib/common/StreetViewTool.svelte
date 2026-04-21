<script lang="ts" generics="F, S">
  import cameraCursorUrl from "$lib/assets/camera_cursor.svg?url";
  import type { Map, MapMouseEvent } from "maplibre-gl";
  import { CollapsibleCard, Radio, SecondaryButton } from "govuk-svelte";
  import { onDestroy } from "svelte";
  import StreetViewHelp from "./StreetViewHelp.svelte";

  export let cfg: { getStreetViewRoadLayerNames: (map: Map) => string[] };
  export let map: Map | null;
  export let enabled: boolean;
  export let showControls = true;

  let defaultLineColorPerLayer: [string, any][] = [];

  function on() {
    if (!map) {
      return;
    }
    map.on("click", onClick);
    map.getCanvas().style.cursor = `url(${cameraCursorUrl}), auto`;

    for (let layer of cfg.getStreetViewRoadLayerNames(map)) {
      defaultLineColorPerLayer.push([
        layer,
        map.getPaintProperty(layer, "line-color"),
      ]);
      map.setPaintProperty(layer, "line-color", "cyan");
    }
  }

  function off() {
    if (map) {
      map.off("click", onClick);
      map.getCanvas().style.cursor = "inherit";

      for (let [layer, value] of defaultLineColorPerLayer) {
        map.setPaintProperty(layer, "line-color", value);
      }
      defaultLineColorPerLayer = [];
    }
  }
  onDestroy(off);

  $: if (enabled) {
    on();
  } else {
    off();
  }

  function onClick(e: MapMouseEvent) {
    if (!enabled) {
      return;
    }
    let lon = e.lngLat.lng;
    let lat = e.lngLat.lat;
    window.open(
      `http://maps.google.com/maps?q=&layer=c&cbll=${lat},${lon}&cbp=11,0,0,0,0`,
      "_blank",
    );
  }

  function onKeyDown(e: KeyboardEvent) {
    if (enabled && e.key == "Escape") {
      e.stopPropagation();
      enabled = false;
    }
  }
</script>

<svelte:window on:keydown={onKeyDown} />

{#if showControls}
  {#if enabled}
    <SecondaryButton on:click={() => (enabled = false)}>
      Disable Street View
    </SecondaryButton>

    <CollapsibleCard label="Help">
      <StreetViewHelp />
    </CollapsibleCard>
  {:else}
    <SecondaryButton on:click={() => (enabled = true)}>
      Enable Street View
    </SecondaryButton>
  {/if}
{/if}
