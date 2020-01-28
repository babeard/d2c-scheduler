<script>
  import { fly, fade, scale } from "svelte/transition";
  import Tailwindcss from "./Tailwindcss.svelte";
  import Calendar from "./components/Calendar.svelte";
  import CheckAnimated from "./components/svg/CheckAnimated.svelte";
  import Icon from "./components/svg/Icon.svelte";
  import watchsvg from "./components/svg/icons/watch.svg";

  let page = 1;
  let pageDirection = 1;

  const times = [
    { disabled: false, time: "9:00" },
    { disabled: false, time: "9:30" },
    { disabled: false, time: "10:00" },
    { disabled: false, time: "10:30" },
    { disabled: false, time: "11:00" },
    { disabled: true, time: "11:30" },
    { disabled: false, time: "12:00" },
    { disabled: false, time: "12:30" },
    { disabled: true, time: "13:00" },
    { disabled: false, time: "13:30" },
    { disabled: false, time: "14:00" },
    { disabled: false, time: "14:30" },
    { disabled: false, time: "15:00" },
    { disabled: false, time: "15:30" },
    { disabled: false, time: "16:00" },
    { disabled: false, time: "16:30" },
    { disabled: false, time: "17:00" },
    { disabled: false, time: "17:30" }
  ];
  let selectedDateT;
  let selectedTimeIdx;

  $: selectedDate = (selectedDateT && new Date(selectedDateT)) || null;
  $: hasSelectedTime = selectedTimeIdx !== null && selectedTimeIdx >= 0;
  $: selectedTime = hasSelectedTime ? times[selectedTimeIdx].time : null;

  $: selectedTimeOffset = selectedTime
    ? `${selectedTime.split(":")[0]}:${parseInt(selectedTime.split(":")[1]) +
        15}`
    : null;

  $: page1Valid = selectedDate && hasSelectedTime;

  let name;
  $: nameValid = name && name.length > 2;

  let email;
  const emailRe = /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
  $: emailValid = email ? emailRe.test(email.toLowerCase()) : false;

  let phone;
  const phoneRe = /^[\+]?[(]?[0-9]{3}[)]?[-\s\.]?[0-9]{3}[-\s\.]?[0-9]{4,6}$/im;
  $: phoneValid = phone ? phoneRe.test(phone) : false;

  $: page2Valid = nameValid && emailValid && phoneValid;

  const changePage = p => {
    pageDirection = p < page ? -1 : 1;

    if (page === 1 && !page1Valid) return;
    if (page === 2 && pageDirection === 1 && !page2Valid) return;
    if (page === p) return;
    if (p < 1) return;

    page = p;
  };
</script>

<Tailwindcss />

<div
  class="flex justify-center p-3 bg-gray-100 items-center"
  style="min-height: 100vh;">
  {#if page !== 3}
    <main
      class="max-w-screen-lg w-full bg-white rounded-lg shadow-lg flex flex-col">

      <div>
        <div class="page-1-2-parent w-full flex flex-col md:flex-row">

          <aside
            class="w-full flex flex-row md:flex-col justify-between pt-10 pb-10
            md:pb-5 px-5 border-b md:border-b-0 md:border-r border-gray-200">
            <div>
              <h3>Walkthrough</h3>

              <h1>Schedule a demo</h1>

              <div
                class="rounded-lg px-3 py-1 bg-blue-100 text-blue-500
                font-medium inline-flex items-center">
                <Icon data={watchsvg} class="mr-1" size="18px" />
                10-20min
              </div>
            </div>

            {#if selectedDate}
              <div
                class="hidden md:block bg-gray-100 px-5 py-6 rounded-lg"
                in:fly={{ y: 20 }}>
                <h5>Date</h5>
                <span class="font-bold">
                  {new Intl.DateTimeFormat('en-US', {
                    month: 'long',
                    day: '2-digit',
                    year: 'numeric'
                  }).format(selectedDate)}
                </span>

                <h5 class="mt-5">Time</h5>

                {#if selectedTime}
                  <div
                    class="block font-bold"
                    in:fade|local={{ duration: 300 }}>
                    {selectedTime} - {selectedTimeOffset ? selectedTimeOffset : ''}
                    <span class="text-gray-500 text-xs font-normal ml-1">
                      (GMT +1)
                    </span>
                  </div>
                {:else}
                  <span class="text-gray-400">Please select</span>
                {/if}

              </div>
            {/if}

          </aside>

          {#if page === 1}
            <div class="overflow-hidden w-full">
              <div
                class="w-full flex flex-col md:flex-row"
                in:fly|local={{ x: 200 * pageDirection }}>
                <section class="flex-1 py-10 px-5">
                  <h2 class="font-semibold text-gray-500 mb-1">
                    Select a Date & Time
                  </h2>
                  <Calendar bind:selected={selectedDateT} />
                </section>

                <div class="md:pt-32 md:pr-5">
                  <section
                    class="select-time flex flex-col px-5 overflow-y-scroll"
                    style="">
                    {#each times as time, i}
                      <div
                        class="cursor-pointer transition-colors duration-200
                        ease-out flex justify-center items-center w-full
                        rounded-lg border-2 py-2 mb-2 font-medium select-none {time.disabled ? 'text-gray-300 border-gray-100' : selectedTimeIdx === i ? 'text-white border-blue-500 bg-blue-500 dip' : 'border-gray-300 text-blue-500'}
                        "
                        on:click={() => (selectedTimeIdx = !time.disabled ? i : null)}>
                        {time.time}
                      </div>
                    {/each}

                  </section>
                </div>
              </div>
            </div>
          {:else if page === 2}
            <div class="w-full overflow-hidden">
              <div
                class="w-full py-10 px-5"
                in:fly={{ x: 200 * pageDirection }}>
                <h2 class="font-medium text-gray-500 mb-1">
                  Enter your information
                </h2>
                <div class="text-gray-800 text-xl font-medium select-none mb-8">
                  Personal data
                </div>

                <label class="mb-6">
                  Your Name
                  <div
                    class="mt-1 md:w-4/5 rounded-lg border border-gray-400 p-2
                    flex">
                    <input
                      bind:value={name}
                      class="pl-3 flex-1 border-none bg-transparent"
                      type="text"
                      placeholder="Please enter your full name" />
                    {#if nameValid}
                      <span
                        class="h-8 w-8 block p-1 flex justify-center
                        items-center bg-green-100 text-green-500 rounded-full"
                        in:scale={{ start: 0.5 }}>
                        <CheckAnimated
                          delay={200}
                          speed={0.5}
                          strokeWidth="3"
                          size="18px" />
                      </span>
                    {:else}
                      <span class="h-8" />
                    {/if}
                  </div>
                </label>

                <label class="mb-6">
                  Your work e-mail address
                  <div
                    class="mt-1 md:w-4/5 rounded-lg border border-gray-400 p-2
                    flex">
                    <input
                      bind:value={email}
                      class="pl-3 flex-1 border-none bg-transparent"
                      type="email"
                      placeholder="Please enter your e-mail address" />
                    {#if emailValid}
                      <span
                        class="h-8 w-8 block p-1 flex justify-center
                        items-center bg-green-100 text-green-500 rounded-full"
                        in:scale={{ start: 0.5 }}>
                        <CheckAnimated
                          delay={200}
                          speed={0.5}
                          strokeWidth="3"
                          size="18px" />
                      </span>
                    {:else}
                      <span class="h-8" />
                    {/if}
                  </div>
                </label>

                <label>
                  Phone number
                  <div
                    class="mt-1 md:w-4/5 rounded-lg border border-gray-400 p-2
                    flex">
                    <input
                      bind:value={phone}
                      class="pl-3 flex-1 border-none bg-transparent"
                      type="text"
                      placeholder="Please enter phone number" />
                    {#if phoneValid}
                      <span
                        class="h-8 w-8 block p-1 flex justify-center
                        items-center bg-green-100 text-green-500 rounded-full"
                        in:scale={{ start: 0.5 }}>
                        <CheckAnimated
                          delay={200}
                          speed={0.5}
                          strokeWidth="3"
                          size="18px" />
                      </span>
                    {:else}
                      <span class="h-8" />
                    {/if}
                  </div>
                </label>

              </div>
            </div>
          {/if}
        </div>

        <footer
          class="w-full py-5 px-5 flex justify-between items-center border-t
          border-gray-200">
          <button
            class="font-medium py-2 px-5 md:px-8 rounded-md border-none {page !== 1 ? 'bg-blue-100 text-blue-500' : 'bg-gray-100 text-blue-300'}"
            on:click={() => changePage(page - 1)}>
            &larr; Back
          </button>

          {#if page === 1}
            <button
              class="next-btn font-medium py-2 px-5 md:px-8 rounded-md
              border-none {page1Valid ? 'valid bg-blue-500 text-white' : 'bg-blue-300 text-blue-100'}"
              on:click={() => changePage(page + 1)}>
              Next Step
            </button>
          {:else if page === 2}
            <button
              class="next-btn font-medium py-2 px-5 md:px-8 rounded-md
              border-none {page2Valid ? 'bg-blue-500 text-white' : 'bg-blue-300 text-blue-100'}"
              on:click={() => changePage(page + 1)}>
              Schedule demo
            </button>
          {/if}

        </footer>
      </div>

    </main>
  {:else}
    <main
      class="page-3 max-w-screen-lg w-full shadow-lg flex flex-col
      justify-center items-center my-10 py-10 bg-white rounded-lg px-5 md:px-0">
      <div
        class="bg-blue-200 text-blue-600 p-3 inline-flex justify-center
        items-center rounded-full mb-10 md:mb-0"
        in:scale|local={{ start: 0.5, delay: 200 }}>
        <CheckAnimated delay={400} size="40px" strokeWidth="3" />
      </div>

      <h1 class="mt-5 text-center md:text-left">
        We just scheduled a demo for you!
      </h1>

      <div class="mt-3 text-center text-gray-500" style="max-width: 500px;">
        A calendar invitation for your upcoming demo session has been sent to
        your email
        <span class="font-semibold">({email}).</span>
      </div>

      <div
        class="mt-10 bg-gray-100 px-5 py-6 rounded-lg w-full flex flex-col
        md:flex-row justify-between md:items-center"
        style="max-width: 450px;">
        <div class="flex-1">
          <h5>Date</h5>
          <span class="font-bold">
            {new Intl.DateTimeFormat('en-US', {
              month: 'long',
              day: '2-digit',
              year: 'numeric'
            }).format(selectedDate)}
          </span>

        </div>

        <div class="flex-1 mt-5 md:mt-0">
          <h5>Time</h5>
          {#if selectedTime}
            <span class="inline-block font-bold">
              {selectedTime} - {selectedTimeOffset ? selectedTimeOffset : ''}
              <span class="text-gray-500 text-xs font-normal ml-1">
                (GMT +1)
              </span>
            </span>
          {:else}
            <span class="text-gray-400">Please select</span>
          {/if}
        </div>

      </div>

      <button
        class="mt-10 next-btn font-medium py-2 px-8 rounded-md border-none valid
        bg-blue-500 text-white"
        on:click={() => changePage(1)}>
        Get back home
      </button>

      <div class="mt-5 font-bold text-blue-600">Resend E-Mail</div>
    </main>
  {/if}
</div>

<style>
  h1 {
    @apply font-bold;
    @apply text-3xl;
    @apply text-gray-800;
    @apply mb-3;
  }

  h3 {
    @apply text-gray-500;
    @apply font-medium;
    @apply mb-1;
  }

  h5 {
    @apply uppercase;
    @apply font-medium;
    @apply text-gray-600;
    @apply text-xs;
    @apply mb-2;
  }

  .dip {
    animation-name: dip;
    animation-duration: 0.3s;
    animation-timing-function: ease-out;
  }

  input {
    outline: none;
  }

  aside {
    @apply w-full;
  }

  .select-time {
    max-height: 200px;
    scrollbar-color: #cbd5e0 transparent;
    scrollbar-width: thin;
  }

  .page-1-2-parent {
    min-height: auto;
  }

  .page-3 {
    animation-name: scale-up;
    animation-duration: 0.4s;
    animation-timing-function: ease-out;
  }

  @screen md {
    .page-1-2-parent {
      min-height: 515px;
    }

    .page-3 {
      animation-name: scale-down;
    }

    .select-time {
      min-width: 200px;
      max-height: 385px;
      scrollbar-color: #cbd5e0 transparent;
      scrollbar-width: thin;
    }

    aside {
      max-width: 325px;
    }
  }

  @keyframes dip {
    0% {
      transform: scale(1);
    }

    50% {
      transform: scale(0.95);
    }

    100% {
      transform: scale(1);
    }
  }

  @keyframes scale-down {
    0% {
      transform: scale(1.5);
    }

    100% {
      transform: scale(1);
    }
  }

  @keyframes scale-up {
    0% {
      transform: scale(0.7);
    }

    100% {
      transform: scale(1);
    }
  }
</style>
