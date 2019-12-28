<script>
  import Icon from "./Icon.svelte";
  import moment from "moment";
  import choresData from "./chores.js";

  const DAYS_IN_WEEK = 7;
  const dateFormat = "Do MMM";
  const dayFormat = "ddd";
  const currentDayIndex = 3;

  const daysMoment = new Array(DAYS_IN_WEEK)
    .fill(undefined)
    .map((_, index) =>
      moment()
        .startOf("week")
        .add(DAYS_IN_WEEK - index - 1, "days")
    )
    .reverse();

  let data = JSON.parse(localStorage.getItem("data") || "{}");
  $: data;

  //reset data if they were set for different week
  const currentWeek = moment().week();
  if (!data || data.week !== currentWeek) data = {};

  const chores = choresData.map(chore => ({
    ...chore,
    days: chore.days.reduce(
      (total, dayIndex) => ({
        ...total,
        [dayIndex]: true
      }),
      {}
    )
  }));

  function handleIconClick(choreTitle, dayIndex) {
    const newChores = { ...data };
    const currentVal = data[dayIndex] ? data[dayIndex][choreTitle] : undefined;
    newChores[dayIndex] = {
      ...(data[dayIndex] ? data[dayIndex] : {}),
      [choreTitle]: !currentVal
    };
    newChores.week = moment().week();
    localStorage.setItem("data", JSON.stringify(newChores));
    data = newChores;
  }
</script>

<style>
  .date {
    font-size: 12px;
    color: #777b7b;
    letter-spacing: 1px;
  }
  .date-day {
    text-transform: uppercase;
    letter-spacing: 1px;
    margin-top: 5px;
  }
  .page-all {
    display: flex;
    flex-direction: column;
    min-height: 100vh;
  }
  .page-content {
    display: flex;
    flex-direction: column;
    flex-grow: 1;
  }
  .page-footer {
    background-color: #242b2d;
    border-top: 1px solid rgba(255, 255, 255, 0.08);
    box-shadow: 0 -2px 5px rgba(0, 0, 0, 0.2);
    padding-top: 3px;
    padding-bottom: 5px;
  }
  .page-header {
    text-align: center;
    background-color: #1f2627;
    border-bottom: 1px solid rgba(255, 255, 255, 0.08);
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
    text-shadow: 2px 2px 2px rgba(0, 0, 0, 0.3);
  }
  .row-items {
    display: flex;
    flex-grow: 1;
    text-align: center;
  }
  .row-items:nth-child(2n) {
    background-color: #1e2526;
  }
  .row-items:not(:last-of-type) {
    border-bottom: 1px solid rgba(255, 255, 255, 0.08);
  }
  .row-items__item {
    position: relative;
    display: flex;
    justify-content: center;
    flex-direction: column;
    flex-grow: 1;
    padding: 12px 15px;
    width: 10%;
  }
  .row-items--border .row-items__item:not(:last-of-type) {
    border-right: 1px solid rgba(255, 255, 255, 0.08);
  }
  .row-items__item--title {
    text-align: left;
    width: 20%;
  }
</style>

<div class="page-all">
  <header class="page-header">
    <h1>My Chores</h1>
  </header>
  <main class="page-content">
    {#each chores as chore}
      <div class="row-items row-items--border">
        <div class="row-items__item row-items__item--title">{chore.label}</div>
        {#each daysMoment as _, index}
          <div class="row-items__item">
            {#if chore.days[index]}
              {#if data[index] && data[index][chore.label]}
                <Icon
                  onClick={() => {
                    handleIconClick(chore.label, index);
                  }}
                  className="icon--check icon--green" />
              {:else}
                <Icon
                  onClick={() => {
                    handleIconClick(chore.label, index);
                  }}
                  className="icon--cancel {currentDayIndex > index ? 'icon--orange' : ''}" />
              {/if}
            {:else}
              <Icon className="icon--empty" />
            {/if}
          </div>
        {/each}
      </div>
    {/each}
  </main>
  <footer class="page-footer">
    <div class="row-items">
      <div class="row-items__item row-items__item--title" />
      {#each daysMoment as day}
        <div class="row-items__item">
          <div class="date">{day.format(dateFormat)}</div>
          <div class="date-day">{day.format(dayFormat)}</div>
        </div>
      {/each}
    </div>
  </footer>
</div>
