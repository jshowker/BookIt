<script lang="ts">
  import * as DropdownMenu from "$lib/components/ui/dropdown-menu/index.js";
  import { buttonVariants } from "$lib/components/ui/button/index.js";
  import { browser } from "$app/environment"; // Импортируем для проверки на SSR
  import { ChevronDown } from "lucide-svelte"; // Импортируем иконку стрелки

  // Состояние для выбранного типа поездки
  let tripType = "roundTrip"; // roundTrip, oneWay, multiCity

  // Данные для формы
  let fromCity = "";
  let toCity = "";
  let departureDate = "";

  // Пассажиры
  let adults = 1;
  let children = 0;
  let infants = 0;

  // Состояние для выпадающих меню
  let travellerMenuOpen = false;
  let classMenuOpen = false;

  // Класс обслуживания
  let economySelected = true;
  let comfortSelected = false;
  let businessSelected = false;
  let firstClassSelected = false;

  // Функция для установки типа поездки
  function setTripType(type) {
    tripType = type;
  }

  // Функции для управления количеством пассажиров
  function increasePassengers(type) {
    const totalPassengers = adults + children + infants;
    if (totalPassengers < 9) {
      if (type === 'adults') adults++;
      if (type === 'children') children++;
      if (type === 'infants' && infants < adults) infants++; // Ограничение: не больше младенцев, чем взрослых
    }
  }

  function decreasePassengers(type) {
    if (type === 'adults' && adults > 1) adults--;
    if (type === 'children' && children > 0) children--;
    if (type === 'infants' && infants > 0) infants--;
  }

  // Переключение состояния меню
  function toggleTravellerMenu() {
    if (browser) {
      travellerMenuOpen = !travellerMenuOpen;
      if (travellerMenuOpen) classMenuOpen = false;
    }
  }

  function toggleClassMenu() {
    if (browser) {
      classMenuOpen = !classMenuOpen;
      if (classMenuOpen) travellerMenuOpen = false;
    }
  }

  // Закрытие меню при клике вне
  function handleClickOutside(event) {
    if (browser) {
      const travellerButton = document.getElementById('traveller-button');
      const travellerMenu = document.getElementById('traveller-menu');
      const classButton = document.getElementById('class-button');
      const classMenu = document.getElementById('class-menu');

      if (travellerMenuOpen &&
          !travellerButton.contains(event.target) &&
          !travellerMenu.contains(event.target)) {
        travellerMenuOpen = false;
      }

      if (classMenuOpen &&
          !classButton.contains(event.target) &&
          !classMenu.contains(event.target)) {
        classMenuOpen = false;
      }
    }
  }

  if (browser) {
    document.addEventListener('click', handleClickOutside);
  }

  // Получение строки с количеством пассажиров для отображения
  function getPassengersText() {
    let text = adults === 1 ? "1 Adult" : `${adults} Adults`;
    if (children > 0) {
      text += children === 1 ? ", 1 Child" : `, ${children} Children`;
    }
    if (infants > 0) {
      text += infants === 1 ? ", 1 Infant" : `, ${infants} Infants`;
    }
    return text;
  }

  // Получение строки с выбранными классами обслуживания
  function getClassText() {
    const classes = [];
    if (economySelected) classes.push("Economy");
    if (comfortSelected) classes.push("Comfort");
    if (businessSelected) classes.push("Business");
    if (firstClassSelected) classes.push("First");

    return classes.join(", ");
  }
</script>

<div class="w-full px-8 space-y-6 max-w-2xl mx-auto">
  <!-- Кнопки типа поездки -->
  <div class="bg-blue-700 px-4 py-3 flex justify-between w-1/2 mx-auto rounded-b-3xl">
    <button
      class="flex-1 text-center font-medium {tripType === 'roundTrip' ? 'text-white' : 'text-gray-300'}"
      on:click={() => setTripType('roundTrip')}
    >
      Round<br>Trip
    </button>
    <button
      class="flex-1 text-center font-medium {tripType === 'oneWay' ? 'text-white' : 'text-gray-300'}"
      on:click={() => setTripType('oneWay')}
    >
      One-<br>Way
    </button>
    <button
      class="flex-1 text-center font-medium {tripType === 'multiCity' ? 'text-white' : 'text-gray-300'}"
      on:click={() => setTripType('multiCity')}
    >
      Multi<br>City
    </button>
  </div>

  <!-- From field -->
  <div class="relative w-full mx-auto">
    <label class="absolute -top-2 left-3 px-1 bg-gray-200 text-gray-500 text-xs font-bold z-10">From:</label>
    <input
      type="text"
      placeholder="Enter city or airport"
      bind:value={fromCity}
      class="w-full px-3 py-2.5 text-xs border-2 border-black rounded-md bg-gray-200 text-black focus:outline-none"
    />
  </div>

  <!-- To field -->
  <div class="relative w-full mx-auto">
    <label class="absolute -top-2 left-3 px-1 bg-gray-200 text-gray-500 text-xs font-bold z-10">To:</label>
    <input
      type="text"
      placeholder="Enter city or airport"
      bind:value={toCity}
      class="w-full px-3 py-2.5 text-xs border-2 border-black rounded-md bg-gray-200 text-black focus:outline-none"
    />
  </div>

  <!-- Date field -->
  <div class="relative w-full mx-auto">
    <label class="absolute -top-2 left-3 px-1 bg-gray-200 text-xs font-bold z-10 text-black">Date:</label>
    <input
      type="text"
      placeholder="Select departure date"
      bind:value={departureDate}
      class="w-full px-3 py-2.5 text-xs border-2 border-black rounded-md bg-gray-200 text-black focus:outline-none"
    />
  </div>

  <!-- Horizontal container for Travellers and Class -->
  <div class="flex flex-row space-x-4">
    <!-- Travellers dropdown (custom) -->
    <div class="relative w-1/2">
      <label class="absolute -top-2 left-3 px-1 bg-gray-200 text-xs font-bold z-10 text-black">Travellers:</label>
      <button
        id="traveller-button"
        class="w-full px-3 py-2.5 text-xs border-2 border-black rounded-md bg-gray-200 text-black text-left flex justify-between items-center"
        on:click={toggleTravellerMenu}
      >
        <span>{getPassengersText()}</span>
        <ChevronDown size={16} />
      </button>

      {#if browser && travellerMenuOpen}
        <div
          id="traveller-menu"
          class="absolute top-full left-0 w-72 bg-gray-800 text-white p-4 rounded-md mt-1 z-50"
        >
          <div class="space-y-4">
            <h3 class="text-md font-bold">Number of passengers</h3>

            <!-- Adults -->
            <div class="flex justify-between items-center">
              <div>
                <div class="font-medium">Adults</div>
                <div class="text-sm text-gray-400">12 years and older</div>
              </div>
              <div class="flex items-center space-x-3">
                <button
                  class="w-8 h-8 rounded-full bg-gray-700 flex items-center justify-center"
                  on:click={() => decreasePassengers('adults')}
                  disabled={adults <= 1}
                >
                  <span class="text-lg">−</span>
                </button>
                <span class="w-4 text-center">{adults}</span>
                <button
                  class="w-8 h-8 rounded-full bg-blue-500 flex items-center justify-center"
                  on:click={() => increasePassengers('adults')}
                  disabled={adults + children + infants >= 9}
                >
                  <span class="text-lg">+</span>
                </button>
              </div>
            </div>

            <!-- Children -->
            <div class="flex justify-between items-center">
              <div>
                <div class="font-medium">Children</div>
                <div class="text-sm text-gray-400">2-11 years old</div>
              </div>
              <div class="flex items-center space-x-3">
                <button
                  class="w-8 h-8 rounded-full bg-gray-700 flex items-center justify-center"
                  on:click={() => decreasePassengers('children')}
                  disabled={children <= 0}
                >
                  <span class="text-lg">−</span>
                </button>
                <span class="w-4 text-center">{children}</span>
                <button
                  class="w-8 h-8 rounded-full bg-blue-500 flex items-center justify-center"
                  on:click={() => increasePassengers('children')}
                  disabled={adults + children + infants >= 9}
                >
                  <span class="text-lg">+</span>
                </button>
              </div>
            </div>

            <!-- Infants -->
            <div class="flex justify-between items-center">
              <div>
                <div class="font-medium">Infants</div>
                <div class="text-sm text-gray-400">Under 2 y.o., on lap</div>
              </div>
              <div class="flex items-center space-x-3">
                <button
                  class="w-8 h-8 rounded-full bg-gray-700 flex items-center justify-center"
                  on:click={() => decreasePassengers('infants')}
                  disabled={infants <= 0}
                >
                  <span class="text-lg">−</span>
                </button>
                <span class="w-4 text-center">{infants}</span>
                <button
                  class="w-8 h-8 rounded-full bg-blue-500 flex items-center justify-center"
                  on:click={() => increasePassengers('infants')}
                  disabled={infants >= adults || adults + children + infants >= 9}
                >
                  <span class="text-lg">+</span>
                </button>
              </div>
            </div>
          </div>
        </div>
      {/if}
    </div>

    <!-- Class dropdown (custom) -->
    <div class="relative w-1/2">
      <label class="absolute -top-2 left-3 px-1 bg-gray-200 text-xs font-bold z-10 text-black">Class:</label>
      <button
        id="class-button"
        class="w-full px-3 py-2.5 text-xs border-2 border-black rounded-md bg-gray-200 text-black text-left flex justify-between items-center"
        on:click={toggleClassMenu}
      >
        <span>{getClassText() || "Select class"}</span>
        <ChevronDown size={16} />
      </button>

      {#if browser && classMenuOpen}
        <div
          id="class-menu"
          class="absolute top-full left-0 w-56 bg-white p-3 rounded-md shadow-md mt-1 z-50"
        >
          <div class="space-y-3">
            <div class="flex items-center">
              <input
                type="checkbox"
                checked={economySelected}
                on:change={() => economySelected = !economySelected}
                class="checkbox checkbox-neutral mr-2"
              />
              <span class="text-black">Economy</span>
            </div>
            <div class="flex items-center">
              <input
                type="checkbox"
                checked={comfortSelected}
                on:change={() => comfortSelected = !comfortSelected}
                class="checkbox checkbox-neutral mr-2"
              />
              <span class="text-black">Comfort</span>
            </div>
            <div class="flex items-center">
              <input
                type="checkbox"
                checked={businessSelected}
                on:change={() => businessSelected = !businessSelected}
                class="checkbox checkbox-neutral mr-2"
              />
              <span class="text-black">Business</span>
            </div>
            <div class="flex items-center">
              <input
                type="checkbox"
                checked={firstClassSelected}
                on:change={() => firstClassSelected = !firstClassSelected}
                class="checkbox checkbox-neutral mr-2"
              />
              <span class="text-black">First Class</span>
            </div>
          </div>
        </div>
      {/if}
    </div>
  </div>

  <!-- Search button -->
  <div class="pt-2">
    <button class="w-full py-3 bg-gradient-to-r from-orange-500 to-orange-600 text-white font-medium rounded-md hover:from-orange-600 hover:to-orange-700">
      Search Flights
    </button>
  </div>
</div>