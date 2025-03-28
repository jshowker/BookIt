<script>
  import CalendarIcon from "@lucide/svelte/icons/calendar";
  import { CalendarDate, DateFormatter, getLocalTimeZone } from "@internationalized/date";
  import { cn } from "$lib/utils.js";
  import { buttonVariants } from "$lib/components/ui/button/index.js";
  import { RangeCalendar } from "$lib/components/ui/range-calendar/index.js";
  import * as Popover from "$lib/components/ui/popover/index.js";
  import { onMount } from "svelte";

  const df = new DateFormatter("en-US", { dateStyle: "medium" });

  let value = $state({
    start: new CalendarDate(2022, 1, 20),
    end: new CalendarDate(2022, 1, 20).add({ days: 20 })
  });

  let startValue = $state(undefined);
  let isMounted = false;

  onMount(() => {
    isMounted = true;
  });
</script>

<div class="grid gap-2">
  <Popover.Root>
    <Popover.Trigger
      class={cn(
        buttonVariants({ variant: "outline" }),
        !value && "text-muted-foreground"
      )}
    >
      <CalendarIcon class="mr-2 size-4" />
      {#if value && value.start}
        {#if value.end}
          {df.format(value.start.toDate(getLocalTimeZone()))} - {df.format(
            value.end.toDate(getLocalTimeZone())
          )}
        {:else}
          {df.format(value.start.toDate(getLocalTimeZone()))}
        {/if}
      {:else if startValue}
        {df.format(startValue.toDate(getLocalTimeZone()))}
      {:else}
        Pick a date
      {/if}
    </Popover.Trigger>

    {#if isMounted}
      <Popover.Content class="w-auto p-0" align="start">
        <RangeCalendar
          bind:value
          onStartValueChange={(v) => {
            startValue = v;
          }}
          numberOfMonths={2}
        />
      </Popover.Content>
    {/if}
  </Popover.Root>
</div>