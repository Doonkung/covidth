<script>
  import { onMount } from "svelte"
  import dayjs from "dayjs"
  import covtest from "./covtest.json"

  const latestTestRecord = covtest.result.records.slice(-1)[0]
  const latestTestRecordDate = dayjs(latestTestRecord.Date)

  let latestData = {}
  let latestDataDate
  let latestDataDateWithTest = {}

  onMount(async () => {
    const briefingsRes = await fetch(
      "https://raw.githubusercontent.com/wiki/djay/covidthailand/cases_briefings",
    )
    const briefingsData = await briefingsRes.json()
    latestData = briefingsData.slice(-1)[0]
    latestDataDate = dayjs(latestData.Date)

    latestDataDateWithTest = briefingsData
      .slice(-30)
      .find(
        (briefing) =>
          briefing["Date"].split(" ")[0] == latestTestRecord.Date.split(" ")[0],
      )
  })
</script>

<!-- <p>ข้อมูลล่าสุดวันที่ : {latestDataDate?.format("DD/MM/YYYY")}</p>
<p>ข้อมูลตรวจล่าสุดวันที่ : {latestTestRecordDate.format("DD/MM/YYYY")}</p>
<p>Diff : {Math.abs(latestTestRecordDate.diff(latestDataDate, "day"))}</p>
<p>(ใช้ข้อมูลจากวันที่ : {latestDataDateWithTest?.Date})</p> -->

<div class="row">
  <div class="card-group text-white card-stat mt-4">
    <div class="card bg-pos">
      <div class="card-body">
        <h5 class="card-title">ตรวจ</h5>
        <p class="card-text" id="tests">{latestTestRecord.Total}</p>
      </div>
    </div>
    <div class="card bg-death">
      <div class="card-body">
        <h5 class="card-title">ติด</h5>
        <p class="card-text" id="cases">{latestTestRecord.Pos}</p>
      </div>
    </div>
  </div>
</div>
<div class="row">
  <div class="card-group text-white card-stat">
    <div class="card bg-hos">
      <div class="card-body">
        <h5 class="card-title">% การตรวจพบ</h5>
        <p class="card-text" id="deaths">
          {((latestTestRecord.Pos / latestTestRecord.Total) * 100).toFixed(3)}%
        </p>
      </div>
    </div>
    <div class="card bg-rec">
      <div class="card-body">
        <h5 class="card-title">อัพเดตล่าสุด</h5>
        <p class="card-text" id="recs">
          {latestDataDate
            ? `${Math.abs(
                latestTestRecordDate.diff(latestDataDate, "day"),
              )} วันที่แล้ว`
            : "..."}
        </p>
      </div>
    </div>
  </div>
</div>

<style>
  .bg-pos {
    background: rgba(202, 13, 13, 0.85);
    border-radius: 15px 0 0 0;
  }
  .bg-death {
    background: rgba(0, 0, 42, 0.85);
    border-radius: 0 15px 0 0;
  }
  .bg-hos {
    background: rgba(3, 177, 252, 0.85);
    border-radius: 0 0 0 15px;
  }
  .bg-rec {
    background: rgba(17, 173, 82, 0.85);
    border-radius: 0 0 15px 0;
  }
  .bg-dark-10 {
    background: rgba(0, 0, 0, 0.2);
  }
  .card-stat {
    font-family: "Prompt", sans-serif;
    min-width: 60%;
    max-width: 90%;
    margin: 0 auto;
  }
  .card-stat .card-title {
    font-size: 1em;
  }
  .card-stat .card-text {
    font-weight: bolder;
    font-size: 2.5em;
  }
  .card-footer > small > span {
    font-weight: bolder;
    font-size: 1.2em;
  }

  * {
    font-family: "Anakotmai", -apple-system, BlinkMacSystemFont, "Segoe UI",
      Roboto, Oxygen, Ubuntu, Cantarell, "Open Sans", "Helvetica Neue",
      sans-serif;
  }

  @media (max-width: 576px) {
    .bg-pos {
      background: rgba(202, 13, 13, 0.85);
      border-radius: 15px;
    }
    .bg-death {
      background: rgba(0, 0, 42, 0.85);
      border-radius: 15px;
    }
    .bg-hos {
      background: rgba(3, 177, 252, 0.85);
      border-radius: 15px;
    }
    .bg-rec {
      background: rgba(17, 173, 82, 0.85);
      border-radius: 15px;
    }
  }
</style>
