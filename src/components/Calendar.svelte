<script>
  import { createEventDispatcher } from "svelte";

  import Icon from "./svg/Icon.svelte";
  import chevronleftsvg from "./svg/icons/chevron.left.svg";
  import chevronrightsvg from "./svg/icons/chevron.right.svg";

  const dispatch = createEventDispatcher();

  export let maxMonths = 2;

  export let selected = null;

  $: selectedDay = (selected && new Date(selected)) || null;

  const today = new Date();

  const weekDays = ["SUN", "MON", "TUE", "WED", "THU", "FRI", "SAT"];

  $: selectedMonth = selectedDay
    ? new Date(selectedDay.getFullYear(), selectedDay.getMonth(), 1)
    : new Date(today.getFullYear(), today.getMonth(), 1);

  $: monthDays = getCalendarDays(selectedMonth);
  $: backMonthDisabled =
    selectedMonth.getFullYear() <= today.getFullYear() &&
    selectedMonth.getMonth() <= today.getMonth();
  $: forwardMonthDisabled = monthDiff(today, selectedMonth) >= maxMonths;
  const getCalendarDays = month => {
    // Get previous Sunday if today (the 1st) isn't a Sunday
    const cursor =
      month.getDay() === 0
        ? new Date(month)
        : new Date(new Date(month).setDate(month.getDate() - month.getDay()));
    const days = [];
    let hasMoredays = true;
    // i is a safety net for infinite loops crashing script in
    // case my logic is bad.
    let i = 0;
    do {
      if (cursor.getMonth() === month.getMonth()) {
        days.push(new Date(cursor));
      } else {
        days.push(null);
      }
      cursor.setDate(cursor.getDate() + 1);
      i++;
      const cmonth = cursor.getMonth();
      const cyear = cursor.getFullYear();
      const mmonth = month.getMonth();
      const myear = month.getFullYear();
      hasMoredays = !(cyear > myear) || (cyear <= myear && cmonth <= mmonth);
    } while (hasMoredays && i < 40);
    return days;
  };
  const selectDay = d => {
    const day = d === selectedDay ? null : d;
    // dispatch("select", day);
    // selectedDay = day;
    selected = day.getTime();
  };
  const changeMonth = direction => {
    if (backMonthDisabled && direction === -1) return;
    if (forwardMonthDisabled && direction === 1) return;
    const tmpDate = new Date(selectedMonth);
    tmpDate.setMonth(tmpDate.getMonth() + direction);
    selectedMonth = tmpDate;
  };
  const monthDiff = (from, to) =>
    to.getMonth() -
    from.getMonth() +
    12 * (to.getFullYear() - from.getFullYear());
  const datesEqual = (a, b) =>
    a.getFullYear() === b.getFullYear() &&
    a.getMonth() === b.getMonth() &&
    a.getDate() === b.getDate();
  const dateLessThan = (a, b) =>
    new Date(a.getFullYear(), a.getMonth(), a.getDate()) <
    new Date(b.getFullYear(), b.getMonth(), b.getDate());
  const isDisabled = d =>
    d === null ||
    dateLessThan(d, today) ||
    d.getDay() === 0 ||
    d.getDay() === 6;
</script>

<div class="parent {$$props.class}">

  <!-- Month Switcher -->
  <section class="flex justify-between items-center">

    <div class="month w-1/2">
      {selectedMonth.toLocaleDateString('en-US', {
        year: 'numeric',
        month: 'long'
      })}
    </div>

    <nav class="flex w-1/2 justify-end">
      <button
        class="icon mr-3 border-none"
        class:disabled={backMonthDisabled}
        on:click={() => changeMonth(-1)}>
        <Icon data={chevronleftsvg} />
      </button>

      <button
        class="icon border-none"
        class:disabled={forwardMonthDisabled}
        on:click={() => changeMonth(1)}>
        <Icon data={chevronrightsvg} />
      </button>
    </nav>
  </section>

  <main>
    {#each weekDays as day}
      <h4>{day}</h4>
    {/each}

    {#each monthDays as day}
      <div
        class:disabled={isDisabled(day)}
        class:selected={day !== null && selectedDay !== null && datesEqual(selectedDay, day)}>
        {#if day !== null}
          <span
            class:today={datesEqual(today, day)}
            on:click={() => !isDisabled(day) && selectDay(day)}>
            {day.getDate()}
          </span>
        {/if}
      </div>
    {/each}

  </main>
</div>

<style>
  .month {
    @apply text-gray-800;
    @apply text-2xl;
    @apply font-medium;
    @apply select-none;
  }

  nav .icon {
    @apply bg-blue-100;
    @apply rounded-md;
    @apply p-1;
    @apply text-blue-500;
    @apply cursor-pointer;
    @apply select-none;
  }
  nav .icon:hover {
    @apply bg-blue-500;
    @apply text-white;
  }
  nav .icon.disabled {
    @apply bg-gray-100;
    @apply text-gray-400;
    @apply cursor-default;
  }

  main {
    @apply mt-6;
    @apply grid;
    @apply gap-1;
    @apply grid-cols-7;
    justify-items: center;
    align-items: center;
  }

  main h4 {
    @apply text-gray-800;
    @apply font-medium;
    @apply select-none;
    @apply text-xs;
    @apply mb-3;
  }

  main div {
    @apply flex;
    @apply items-center;
    @apply justify-center;
  }

  main div span {
    @apply transition-transform;
    @apply duration-200;
    @apply ease-out;
    @apply p-3;
    @apply cursor-pointer;
    @apply select-none;
    @apply rounded-lg;
    @apply leading-none;
    @apply text-gray-600;
  }

  .today {
    @apply bg-blue-100;
    @apply text-blue-500;
    @apply font-semibold;
  }
  /* .selected span.today {
    @apply bg-indigo-500;  
  } */

  main div.disabled span {
    @apply cursor-default;
    @apply text-gray-400;
  }
  main div span:active {
    font-weight: 700;
  }
  main div.selected span {
    @apply text-white;
    @apply font-semibold;
    @apply relative;
    z-index: 2;
  }

  main div.selected span::after {
    content: "";
    @apply absolute;
    width: 100%;
    height: 100%;
    @apply top-0;
    @apply left-0;
    @apply bg-blue-500;
    @apply rounded-lg;
    z-index: -1;
    animation-name: zoom-in;
    animation-duration: 0.3s;
    animation-timing-function: ease-out;
  }

  @keyframes zoom-in {
    0% {
      transform: scale(0.5);
    }

    50% {
      transform: scale(1.15);
    }

    100% {
      transform: scale(1);
    }
  }
</style>
