<script lang="ts">
  import CalendarRange from '$lib/CalendarRange.svelte';
  import In_app_calender from '$lib/In_app_calender.svelte';
  import dayjs from 'dayjs';

  let showCalendar = false;
  let showInAppCalendar = false;
  let selectedRange = { start: null, end: null };

  function openCalendar() {
    showCalendar = true;
  }

  function openInAppCalendar() {
    showInAppCalendar = true;
  }

  function closeInAppCalendar() {
    showInAppCalendar = false;
  }

  function handleApply(event) {
    selectedRange = event.detail;
    showCalendar = false;
  }
</script>

<main>
  <button on:click={openCalendar}>Open Date Range Picker</button>
  <button on:click={openInAppCalendar} class="in-app-calendar-btn">Open In-App Calendar</button>
  {#if selectedRange.start && selectedRange.end}
    <p>Selected: {selectedRange.start.format('MMM D, YYYY')} - {selectedRange.end.format('MMM D, YYYY')}</p>
  {/if}
  <CalendarRange open={showCalendar} on:apply={handleApply} on:cancel={() => showCalendar = false} />
</main>

{#if showInAppCalendar}
  <div class="in-app-calendar-overlay">
    <div class="in-app-calendar-container">
      <button class="close-btn" on:click={closeInAppCalendar}>×</button>
      <div class="in-app-calendar-scroll-area">
        <In_app_calender />
      </div>
    </div>
  </div>
{/if}

<style>
main { display: flex; flex-direction: column; align-items: center; margin-top: 80px; gap: 16px; }
button { padding: 12px 24px; font-size: 1.1rem; border-radius: 8px; background: #6366f1; color: #fff; border: none; cursor: pointer; }
button:hover { background: #4f46e5; }
.in-app-calendar-btn { background: #059669; }
.in-app-calendar-btn:hover { background: #047857; }
p { margin-top: 24px; font-size: 1.1rem; }

.in-app-calendar-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 0.8);
  z-index: 1000;
  display: flex;
  align-items: center;
  justify-content: center;
}

.in-app-calendar-container {
  position: relative;
  width: 100%;
  height: 100%;
  max-width: 1200px;
  max-height: 90vh;
  background: #1a1a1a;
  border-radius: 12px;
  overflow: hidden;
  display: flex;
  flex-direction: column;
}

.in-app-calendar-scroll-area {
  overflow-y: auto;
  height: 100%;
  -ms-overflow-style: none;  
  
}

.in-app-calendar-scroll-area::-webkit-scrollbar {
  display: none;
}

.close-btn {
  position: absolute;
  top: 4px;
  right: 4px;
  width: 10px;
  height: 10px;
  border-radius: 8px;
  background: rgba(255, 255, 255, 0.1);
  color: #ffffff;
  cursor: pointer;
  font-size: 18px;
  display: flex;
  align-items: center;
  justify-content: center;
  
}

.close-btn:hover {
  background: rgba(255, 255, 255, 0.2);
}
</style>