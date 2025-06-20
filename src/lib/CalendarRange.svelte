<script lang="ts">
  import { createEventDispatcher, onMount } from 'svelte';
  import dayjs from 'dayjs';

  const today = dayjs();
  const presets = [
    { label: 'Today', range: [today, today] },
    { label: 'Yesterday', range: [today.subtract(1, 'day'), today.subtract(1, 'day')] },
    { label: 'This week', range: [today.startOf('week'), today.endOf('week')] },
    { label: 'Last week', range: [today.subtract(1, 'week').startOf('week'), today.subtract(1, 'week').endOf('week')] },
    { label: 'This month', range: [today.startOf('month'), today.endOf('month')] },
    { label: 'Last month', range: [today.subtract(1, 'month').startOf('month'), today.subtract(1, 'month').endOf('month')] },
    { label: 'This year', range: [today.startOf('year'), today.endOf('year')] },
    { label: 'Last year', range: [today.subtract(1, 'year').startOf('year'), today.subtract(1, 'year').endOf('year')] },
    { label: 'All time', range: [dayjs('2000-01-01'), today] }
  ];

  export let open = false;
  export let initialStart: dayjs.Dayjs = today.startOf('week');
  export let initialEnd: dayjs.Dayjs = today.endOf('week');

  const dispatch = createEventDispatcher();

  let start = initialStart;
  let end = initialEnd;
  let currentMonth = start.clone().startOf('month');
  let show = open;

  $: if (open !== show) show = open;

  function selectPreset(range) {
    start = range[0];
    end = range[1];
    currentMonth = start.clone().startOf('month');
  }

  function selectDate(date: dayjs.Dayjs) {
    if (!start || (start && end)) {
      start = date;
      end = null;
    } else if (date.isBefore(start)) {
      end = start;
      start = date;
    } else {
      end = date;
    }
  }

  function isInRange(date: dayjs.Dayjs) {
    return start && end && date.isAfter(start.subtract(1, 'day')) && date.isBefore(end.add(1, 'day'));
  }

  function isSelected(date: dayjs.Dayjs) {
    return (start && date.isSame(start, 'day')) || (end && date.isSame(end, 'day'));
  }

  function prevMonth() {
    currentMonth = currentMonth.subtract(1, 'month');
  }
  function nextMonth() {
    currentMonth = currentMonth.add(1, 'month');
  }

  function apply() {
    dispatch('apply', { start, end });
    show = false;
  }
  function cancel() {
    dispatch('cancel');
    show = false;
  }

  function getCalendar(month: dayjs.Dayjs) {
    const startOfMonth = month.startOf('month');
    const endOfMonth = month.endOf('month');
    const startDate = startOfMonth.startOf('week');
    const endDate = endOfMonth.endOf('week');
    const days = [];
    let date = startDate.clone();
    while (date.isBefore(endDate) || date.isSame(endDate, 'day')) {
      days.push(date.clone());
      date = date.add(1, 'day');
    }
    return days;
  }
</script>

<svelte:head>
  <link href="https://api.fontshare.com/v2/css?f[]=pilcrow-rounded@400,500,700,1&display=swap" rel="stylesheet">
</svelte:head>

{#if show}
  <div class="modal-backdrop" on:click={cancel}></div>
  <div class="calendar-modal">
    <div class="sidebar">
      {#each presets as preset}
        <div class="preset" on:click={() => selectPreset(preset.range)}>
          {preset.label}
        </div>
      {/each}
    </div>
    <div class="calendar-content">
      <div class="calendars">
        {#each [0, 1] as i}
          <div class="calendar">
            <div class="header">
              {#if i === 0}
                <button on:click={prevMonth}>&lt;</button>
              {/if}
              <span>{currentMonth.add(i, 'month').format('MMMM YYYY')}</span>
              {#if i === 1}
                <button on:click={nextMonth}>&gt;</button>
              {/if}
            </div>
            <div class="days">
              {#each ['Mo','Tu','We','Th','Fr','Sa','Su'] as d}
                <div class="day">{d}</div>
              {/each}
              {#each getCalendar(currentMonth.add(i, 'month')) as date}
                <div
                  class="date {isSelected(date) ? 'selected' : ''} {isInRange(date) ? 'in-range' : ''} {date.month() !== currentMonth.add(i, 'month').month() ? 'other-month' : ''}"
                  on:click={() => selectDate(date)}
                >
                  {date.date()}
                </div>
              {/each}
            </div>
          </div>
        {/each}
      </div>
      <div class="footer">
           <input type="text" readonly value={start ? start.format('MMM D, YYYY') : ''} />
           <span> - </span>
           <input type="text" readonly value={end ? end.format('MMM D, YYYY') : ''} />
           <button on:click={cancel}>Cancel</button>
           <button class="apply" on:click={apply}>Apply</button>
      </div>
    </div>
  </div>
{/if}

<style>
:global(html) {
  font-family: 'Satoshi', sans-serif;
}
.calendar-modal, .sidebar, .preset, .calendar-content, .header, .day, .date, .footer, input[type="text"], button {
  font-family: 'Satoshi', sans-serif;
}
.modal-backdrop {
  position: fixed; inset: 0; background: rgba(0,0,0,0.12); z-index: 10;
}
.calendar-modal {
  position: fixed; top: 50%; left: 50%; transform: translate(-50%,-50%);
  background: #fff; border-radius: 14px; box-shadow: 0 4px 32px #0001;
  display: flex; z-index: 11; 
  padding: 0;
  overflow: hidden;
}
.sidebar {
  background: #fafbfc;
  padding: 0;
  border-radius: 14px 0 0 14px;
  display: flex; flex-direction: column;
  min-width: 110px;
  height: 100%;
  justify-content: flex-start;
  border-right: 1px solid #f0f0f0;
}
.preset {
  padding: 2px 12px;
  border-radius: 0;
  cursor: pointer;
  font-size: 1rem;
  color: #222;
  text-align: left;
  transition: background 0.15s, color 0.15s;
  min-height: 37px;
  display: flex;
  align-items: center;
}
.preset:hover, .preset.selected {
  background: #e0e7ff;
  color: #4f46e5;
  font-weight: 500;
}
.calendar-content {
  padding: 5px;
  display: flex;
  flex-direction: column;
  flex: 1;
  box-sizing: border-box;
  min-width: 500px;
}
.calendars {
  display: flex;
  gap: 24px;
  margin: 3px;
  width: 100%;
  box-sizing: border-box;
  justify-content: flex-start;
}
.calendar {
  min-width: 260px;
  padding: 3px;
  background: none;
.calendar:hover{
     background: #e0e7ff;
}
}
.header {
  display: flex; align-items: center; justify-content: space-between;
  margin-bottom: 4px;
  font-size: 1.1rem;
  font-weight: 500;
  height: 44px;
}
.header span {
  flex: 1;
  text-align: center;
  font-weight: 500;
  color: #222;
}
.header button {
  background: none;
  border: none;
  font-size: 1.3rem;
  color: #6366f1;
  cursor: pointer;
  padding: 0 8px;
  border-radius: 4px;
  transition: background 0.15s;
  height: 36px;
  width: 36px;
  display: flex;
  align-items: center;
  justify-content: center;
}
.header button:hover {
  background: #e0e7ff;
}
.days {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: 0;
}
.day, .date {
  width: 38px;
  height: 38px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1rem;
  margin: 0;
}
.day {
  font-weight: 400;
  color: #b0b0b0;
  background: none;
  margin-bottom: 0;
  user-select: none;
}
.date {
  border-radius: 1px;
  cursor: pointer;
  transition: background 0.15s, color 0.15s;
  color: #222;
  margin: 1px;
}
.date.selected {
  background: #6366f1;
  color: #fff;
  font-weight: 500;
}
.date.in-range {
  background: #e0e7ff;
  color: #4f46e5;
}
.date.other-month {
  color: #d0d0d0;
}
.footer {
  width: 100%;
  display: flex;
  align-items: center;
  gap: 6px;
  padding: 6px 0 6px 0;
  margin: 5px;
  box-sizing: border-box;
  justify-content: flex-start;
}
input[type="text"] {
  border: none;
  background: #f3f4f6;
  border-radius: 8px;
  padding: 1px 1px;
  font-size: 1rem;
  min-width: 160px;
  text-align: center;
  color: #222;
  height: 40px;
  box-sizing: border-box;
}
button {
  padding: 0 22px;
  border-radius: 8px;
  border: none;
  background: #f3f4f6;
  cursor: pointer;
  font-size: 1rem;
  transition: background 0.15s, color 0.15s;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
}
button.apply {
  background: #6366f1;
  color: #fff;
  font-weight: 500;
}
button.apply:hover {
  background: #4f46e5;
}
button:not(.apply):hover {
  background: #e0e7ff;
}
</style>