<script lang="ts">
  import * as DropdownMenu from "$lib/components/ui/dropdown-menu/index.js";
  import { buttonVariants } from "$lib/components/ui/button/index.js";
  import { browser } from "$app/environment";
  import { onMount } from "svelte";

  let position = $state("bottom");
  let mounted = false;

  onMount(() => {
    mounted = true;
  });
</script>

<DropdownMenu.Root>
  <DropdownMenu.Trigger class={buttonVariants({ variant: "outline" })}>
    Open
  </DropdownMenu.Trigger>

  {#if mounted}
    <DropdownMenu.Portal>
      <DropdownMenu.Content class="w-56">
        <DropdownMenu.Group>
          <DropdownMenu.GroupHeading>Panel Position</DropdownMenu.GroupHeading>
          <DropdownMenu.Separator />
          <DropdownMenu.RadioGroup bind:value={position}>
            <DropdownMenu.RadioItem value="top">Top</DropdownMenu.RadioItem>
            <DropdownMenu.RadioItem value="bottom">Bottom</DropdownMenu.RadioItem>
            <DropdownMenu.RadioItem value="right">Right</DropdownMenu.RadioItem>
          </DropdownMenu.RadioGroup>
        </DropdownMenu.Group>
      </DropdownMenu.Content>
    </DropdownMenu.Portal>
  {/if}
</DropdownMenu.Root>