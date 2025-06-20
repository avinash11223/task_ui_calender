<!-- Calendar.svelte -->
<script lang="ts">
  interface CalendarEvent {
    id: string;
    title: string;
    time: string;
    color: string;
    textColor?: string;
  }

  interface CalendarDay {
    date: number;
    events: CalendarEvent[];
    isToday?: boolean;
    isPrevMonth?: boolean;
    isNextMonth?: boolean;
  }

  // Sample events data matching the calendar
  const calendarEvents: Record<number, CalendarEvent[]> = {
    
    1: [
      { id: '3', title: 'One-on-one w...', time: '10:00 AM', color: '#DC2626', textColor: '#FEE2E2' }
    ],
    2: [
      { id: '4', title: 'All-hands mee...', time: '4:00 PM', color: '#374151', textColor: '#9CA3AF' },
      { id: '5', title: 'Dinner with...', time: '6:30 PM', color: '#059669', textColor: '#D1FAE5' }
    ],
    
    8: [
      { id: '11', title: 'Deep work', time: '9:00 AM', color: '#1D4ED8', textColor: '#DBEAFE' },
      { id: '12', title: 'Design sync', time: '10:30 AM', color: '#374151', textColor: '#9CA3AF' },
      { id: '13', title: 'SEO planning', time: '1:50 PM', color: '#374151', textColor: '#9CA3AF' },
      { id: '14', title: '3 more...', time: '', color: '#374151', textColor: '#9CA3AF' }
    ],
    9: [
      { id: '15', title: 'Lunch with...', time: '12:00 PM', color: '#059669', textColor: '#D1FAE5' }
    ],
    10: [
      { id: '16', title: 'Friday standup', time: '9:00 AM', color: '#374151', textColor: '#9CA3AF' },
      { id: '17', title: 'Olivia & Riley', time: '10:00 AM', color: '#374151', textColor: '#9CA3AF' },
      { id: '18', title: 'Product demo', time: '1:30 PM', color: '#374151', textColor: '#9CA3AF' }
    ],
    11: [],
    12: [
      { id: '19', title: 'House inspe...', time: '11:00 AM', color: '#DC2626', textColor: '#FEE2E2' },
      { id: '20', title: "Ava's engage...", time: '1:00 PM', color: '#7C3AED', textColor: '#EDE9FE' }
    ],
    13: [
      { id: '21', title: 'Monday standup', time: '9:00 AM', color: '#374151', textColor: '#9CA3AF' }
    ],
    14: [
      { id: '22', title: 'Team lunch', time: '12:15 PM', color: '#374151', textColor: '#9CA3AF' }
    ],
    15: [
      { id: '23', title: 'Product plann...', time: '9:30 AM', color: '#1D4ED8', textColor: '#DBEAFE' }
    ],
    21: [
      { id: '32', title: 'Quarterly revi...', time: '11:30 AM', color: '#374151', textColor: '#9CA3AF' },
      { id: '33', title: 'Deep work', time: '9:15 AM', color: '#1D4ED8', textColor: '#DBEAFE' },
      { id: '34', title: 'Lunch with Zahr', time: '1:00 PM', color: '#374151', textColor: '#9CA3AF' },
      { id: '35', title: 'Dinner with...', time: '7:00 PM', color: '#059669', textColor: '#D1FAE5' }
    ],
    22: [
      { id: '36', title: 'Deep work', time: '9:00 AM', color: '#1D4ED8', textColor: '#DBEAFE' },
      { id: '37', title: 'Design sync', time: '2:30 PM', color: '#374151', textColor: '#9CA3AF' }
    ],
    23: [
      { id: '38', title: 'Amelie coffee', time: '10:00 AM', color: '#DC2626', textColor: '#FEE2E2' }
    ],
    29: [
      { id: '46', title: 'Product plann...', time: '9:30 AM', color: '#1D4ED8', textColor: '#DBEAFE' }
    ],
    30: [
      { id: '47', title: 'All-hands mee...', time: '4:00 PM', color: '#374151', textColor: '#9CA3AF' },
      { id: '48', title: 'Team dinner', time: '6:30 PM', color: '#374151', textColor: '#9CA3AF' }
    ],
    31: [
      { id: '49', title: 'Friday standup', time: '9:00 AM', color: '#374151', textColor: '#9CA3AF' }
    ],
    1: [
      { id: '50', title: 'Monday standup', time: '9:00 AM', color: '#374151', textColor: '#9CA3AF' }
    ]
  };

  // Generate calendar days for January 2025
  const generateCalendarDays = (): CalendarDay[] => {
    const days: CalendarDay[] = [];
    
    // Previous month days (30, 31)
    days.push({ date: 30, events: calendarEvents[30] || [], isPrevMonth: true });
    days.push({ date: 31, events: calendarEvents[31] || [], isPrevMonth: true });
    
    // Current month days (1-31)
    for (let i = 1; i <= 31; i++) {
      days.push({ 
        date: i, 
        events: calendarEvents[i] || [],
        isToday: i === 10 // Today is January 10th
      });
    }
    
    // Next month days (1, 2)
    days.push({ date: 1, events: calendarEvents[1] || [], isNextMonth: true });
    days.push({ date: 2, events: [], isNextMonth: true });
    
    return days;
  };

  const calendarDays = generateCalendarDays();
  const dayNames = ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'];
</script>

<div class="calendar-outer">
  <div class="calendar-header">
    <div class="calendar-title">
      <div class="month-year">
        <span class="month-label">JAN</span>
        <br>
        <span class="day-number">10</span>
      </div>
      <div class="date-range">
        January 2025
        <div class="date-subtitle">Jan 1, 2025 — Jan 31, 2025</div>
      </div>
    </div>
    
    <div class="calendar-controls">
      <button class="search-btn">
        <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <circle cx="11" cy="11" r="8"/>
          <path d="m21 21-4.35-4.35"/>
        </svg>
      </button>
      
      <div class="nav-controls">
        <button class="nav-btn">
          <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
            <polyline points="15,18 9,12 15,6"/>
          </svg>
        </button>
        <button class="today-btn">Today</button>
        <button class="nav-btn">
          <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
            <polyline points="9,18 15,12 9,6"/>
          </svg>
        </button>
      </div>
      
      <button class="view-btn">
        Month view
        <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <polyline points="6,9 12,15 18,9"/>
        </svg>
      </button>
      
      <button class="add-event-btn">
        <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <line x1="12" y1="5" x2="12" y2="19"/>
          <line x1="5" y1="12" x2="19" y2="12"/>
        </svg>
        Add event
      </button>
    </div>
  </div>
  <div class="calendar-scrollable">
    <div class="calendar-grid">
      <div class="day-headers">
        {#each dayNames as dayName}
          <div class="day-header">{dayName}</div>
        {/each}
      </div>

      <div class="calendar-days">
        {#each calendarDays as day, index}
          <div class="calendar-day" class:today={day.isToday} class:other-month={day.isPrevMonth || day.isNextMonth}>
            <div class="day-number">{day.date}</div>
            <div class="day-events">
              {#each day.events as event}
                <div class="event" style="background-color: {event.color}; color: {event.textColor || '#ffffff'};">
                  {#if event.time}
                    <span class="event-time">{event.time}</span>
                  {/if}
                  <span class="event-title">{event.title}</span>
                </div>
              {/each}
            </div>
          </div>
        {/each}
      </div>
    </div>
  </div>
</div>

<style>
  .calendar-outer {
    display: grid;
    grid-template-rows: auto 1fr;
    height: 100vh;
    background: #1a1a1a;
  }

  .calendar-header {
    position: sticky;
    top: 0;
    z-index: 10;
    background: #1a1a1a;
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 20px 24px;
    border-bottom: 1px solid #333333;
  }

  .calendar-title {
    
    display: flex;
    align-items: center;
    gap: 20px;
  }

  .month-year {
    display: flex;
    flex-direction: column;
    align-items: center;
    background: #333333;
    padding: 8px 12px;
    border-radius: 8px;
  }

  .month-label {
    font-size: 11px;
    font-weight: 600;
    color: #9CA3AF;
    letter-spacing: 0.5px;
  }

  .day-number {
    font-size: 18px;
    font-weight: 600;
    color: #ffffff;
    margin-top: 2px;
  }

  .date-range {
    color: #ffffff;
    font-size: 24px;
    font-weight: 600;
  }

  .date-subtitle {
    font-size: 14px;
    color: #9CA3AF;
    font-weight: 400;
    margin-top: 4px;
  }

  .calendar-controls {
    display: flex;
    align-items: center;
    gap: 16px;
   
  }

  .search-btn, .nav-btn {
    background: transparent;
    border: 1px solid #333333;
    color: #9CA3AF;
    padding: 8px;
    border-radius: 6px;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .search-btn:hover, .nav-btn:hover {
    background: #333333;
    color: #ffffff;
  }

  .nav-controls {
    display: flex;
    align-items: center;
    gap: 8px;
  }

  .today-btn {
    background: transparent;
    border: 1px solid #333333;
    color: #ffffff;
    padding: 8px 16px;
    border-radius: 6px;
    cursor: pointer;
    font-size: 14px;
    font-weight:bold;
  }

  .today-btn:hover {
    background: #333333;
  }

  .view-btn {
    background: transparent;
    border: 1px solid #333333;
    color: #ffffff;
    padding: 8px 16px;
    border-radius: 6px;
    cursor: pointer;
    display: flex;
    align-items: center;
    gap: 8px;
    font-size: 14px;
    font-weight:bold;
  }

  .view-btn:hover {
    background: #333333;
  }

  .add-event-btn {
    background: #333333;
    border: 1px solid #444444;
    color: #ffffff;
    padding: 8px 16px;
    border-radius: 6px;
    cursor: pointer;
    display: flex;
    align-items: center;
    gap: 8px;
    font-size: 14px;
    font-weight:bold;
  }

  .add-event-btn:hover {
    background: #444444;
  }

  .calendar-scrollable {
    overflow-y: auto;
    scrollbar-width: none;
  }

  .calendar-grid {
    padding: 0;
  }

  .day-headers {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    border-bottom: 1px solid #333333;
  }

  .day-header {
    padding: 16px 8px;
    text-align: center;
    font-size: 12px;
    font-weight: 600;
    color: #9CA3AF;
    letter-spacing: 0.5px;
  }

  .calendar-days {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    grid-template-rows: repeat(6, 1fr);
    min-height: calc(100vh - 140px);
  }

  .calendar-day {
    border-right: 1px solid #333333;
    border-bottom: 1px solid #333333;
    padding: 12px 8px 8px 8px;
    min-height: 120px;
    position: relative;
  }

  .calendar-day:nth-child(7n) {
    border-right: none;
  }

  .calendar-day.today {
    background: #1e293b;
  }

  .calendar-day.other-month .day-number {
    color: #4B5563;
  }

  .calendar-day .day-number {
    font-size: 14px;
    font-weight: 500;
    color: #ffffff;
    margin-bottom: 8px;
  }

  .day-events {
    display: flex;
    flex-direction: column;
    gap: 2px;
  }

  .event {
    padding: 2px 6px;
    border-radius: 3px;
    font-size: 11px;
    
    line-height: 1.3;
    display: flex;
    flex-direction: column;
    min-height: 28px;
    justify-content: center;
  }

  .event-time {
    font-weight: 400;
    opacity: 0.9;
  }

  .event-title {
    font-weight: 500;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }

  
  @media (max-width: 768px) {
    .calendar-header {
      flex-direction: column;
      gap: 16px;
      align-items: flex-start;
    }

    .calendar-controls {
      width: 100%;
      justify-content: space-between;
    }

    .event {
      font-size: 10px;
      min-height: 24px;
      padding: 1px 4px;
    }
  }
</style>