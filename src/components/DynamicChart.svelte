<script>
  import LdrMap from "./LdrMap.svelte";
  import AvgdeptMap from "./AvgdeptMap.svelte";
  import HighLoanMap from "./HighLoanMap.svelte";
  import HighDepositMap from "./HighDepositMap.svelte";
  import EmployMap from "./EmployMap.svelte";

  let radioFilter;

  let employ = false;
</script>

<div id="multiple-chart-container">
  <div>
    {#if employ && radioFilter === "high-deposit"}
      <HighDepositMap width="400" height="300" />
    {:else if employ && radioFilter === "high-loan"}
      <HighLoanMap width="400" height="300" />
    {:else if employ && radioFilter === "deposit-average"}
      <AvgdeptMap width="400" height="300" />
    {:else if radioFilter === "high-deposit"}
      <HighDepositMap />
    {:else if radioFilter === "high-loan"}
      <HighLoanMap />
    {:else if radioFilter === "deposit-average"}
      <AvgdeptMap />
    {:else if employ}
      <LdrMap width="400" height="300" />
    {:else}
      <LdrMap />
    {/if}
  </div>
  {#if employ}
    <div>
      <EmployMap width="200" height="300" />
    </div>
  {/if}
  <div id="multiple-filter-container">
    <div>
      <input
        type="radio"
        value=""
        name="map"
        bind:group={radioFilter}
        checked
      />
      <label for="no-filter">ไม่มี filter</label>
    </div>
    <div>
      <input
        type="radio"
        value="high-deposit"
        name="map"
        bind:group={radioFilter}
      />
      <label for="high-deposit">จังหวัดที่มีเงินฝากมากกว่าเงินกู้</label>
    </div>
    <div>
      <input
        type="radio"
        value="high-loan"
        name="map"
        bind:group={radioFilter}
      />
      <label for="high-loan">จังหวัดที่เงินกู้มากกว่าเงินฝาก</label>
    </div>
    <div>
      <input type="radio" value="deposit-average" bind:group={radioFilter} />
      <label for="deposit-average"
        >จังหวัดที่เงินกู้สูงกว่าเงินฝากและเงินฝากสูงกว่าค่าเฉลี่ยของประเทศ</label
      >
    </div>
    <div>
      <input type="checkbox" bind:checked={employ} />
      <label for="employ-chart">กราฟจ้างงาน</label>
    </div>
  </div>
</div>

<style>
  #multiple-chart-container {
    display: flex;
  }
  #multiple-filter-container {
    display: flex;
    flex-direction: column;
    width: 50vh;
  }
</style>
