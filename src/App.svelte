<script>
  import { onMount } from 'svelte'
  import projectsDetail from './assets/projects.json'
  import jobsList from './assets/jobList.json'
  import usersList from './assets/users.json'
  import robustestImage from "./assets/img/logo-48x48.png";
import darwinImage from "./assets/img/darwin.png";
import espressoImage from "./assets/img/Espresso.png";
import appiumImage from "./assets/img/Appium.png";

  let projectNames = {};
  let projects = projectsDetail;
  let users = {};
 function getDuration(end, start) {
      let millisec = Math.abs(new Date(end) - new Date(start));
      let seconds = Math.round(millisec / 1000);
      return getValueInHHMMSS(seconds);
    };
  function  getValueInHHMMSS(seconds) {
  // Calculating hours
  let hours = parseInt(seconds / 3600);
  let hoursRemainder = Math.round(seconds % 3600);

  // Calculating minutes
  let minutes = parseInt(hoursRemainder / 60);

  // Calculating seconds
  seconds = Math.round(hoursRemainder % 60);

  if (hours > 0) {
    return (
      checkValue(hours) + ":" + checkValue(minutes) + ":" + checkValue(seconds)
    );
  } else if (minutes > 0) {
    return "00:" + checkValue(minutes) + ":" + checkValue(seconds);
  } else {
    return "00:00:" + checkValue(seconds);
  }
};
function getPercentage(value, total) {
  let percentage;
  if (total > 0) {
    percentage = (value / total) * 100;
    return percentage.toFixed(2) + "%";
  } else {
    return "-";
  }
};
function getIdleTime(seconds) {
  let hours = parseInt((seconds / 3600).toFixed(2));
  let hoursRemainder = Math.round(seconds % 3600);

  // Calculating minutes
  let minutes = parseInt((hoursRemainder / 60).toFixed(2));

  // Calculating seconds
  seconds = Math.round(hoursRemainder % 60);

  if (hours > 0) {
    return (
      checkValue(hours) + ":" + checkValue(minutes) + ":" + checkValue(seconds)
    );
  } else if (minutes > 0) {
    return "00:" + checkValue(minutes) + ":" + checkValue(seconds);
  } else {
    return "00:00:" + checkValue(seconds);
  }
};
function checkWaitTime(isStarted, isPaused, reRunAt, created, waitTime) {
      // If started and paused is false (If paused is true wait time is provided)
      if (!isStarted && !isPaused) {
        if (checkValidDate(reRunAt)) {
          return getDuration(Date(), reRunAt);
        } else {
          return getDuration(Date(), created);
        }
      } else {
        return getIdleTime(waitTime);
      }
    };
function checkValidDate(date) {
  if (
    new Date(date).toLocaleString("en-GB", options).split(",")[0] == "1 Jan 1"
  ) {
    return false;
  }
  return true;
};
 function checkValue(val) {
      if (val < 10) {
        return "0" + val;
      } else {
        return val;
      }
    };

  function  calculateDuration(jobDetail) {
      if (jobDetail.isStarted) {
        return "-";
      } else if (
        jobDetail.isStarted &&
        jobDetail.isCompleted &&
        jobDetail.rerunAt &&
        !checkValidDate(jobDetail.rerunAt)
      ) {
        let millisec = Math.abs(
          new Date(Date()) - new Date(jobDetail.started)
        );
        let seconds = Math.round(millisec / 1000);
        return getIdleTime(seconds);
      } else if (
        jobDetail.isStarted &&
        !jobDetail.isCompleted &&
        jobDetail.rerunAt &&
        checkValidDate(jobDetail.rerunAt)
      ) {
        return getIdleTime(jobDetail.duration);
      } else if (jobDetail.isStarted && jobDetail.isCompleted) {
        if (jobDetail.duration > 0) {
          return getIdleTime(jobDetail.duration);
        } else {
          if (
            checkValidDate(jobDetail.completed) &&
            checkValidDate(jobDetail.started)
          ) {
            return getDuration(
              jobDetail.completed,
             jobDetail.started
            );
          } else {
            return "-";
          }
        }
      }
    };
  onMount(async () => {
   for(let i=0;i<projects.length;i++){
    if(projects[i].name){
     projectNames[projects[i]._id] = projects[i].name
    }
   }
   for(let i=0;i<usersList.length;i++){
    if(usersList[i].name){
     users[usersList[i]._id] = usersList[i].name
    }
   }
  })
</script>
<main>
  <div>
    <table class="table-fixed border-separate">
  <thead>
    <tr>
    <th class="bg-blue-100 border text-left px-8 py-4">
          #
        </th>
        <th class="bg-blue-100 border text-left px-8 py-4">
          Identifier
        </th>
          <th class="bg-blue-100 border text-left px-8 py-4">
          Run by
        </th>
          <th class="bg-blue-100 border text-left px-8 py-4">
          Project
        </th>
          <th class="bg-blue-100 border text-left px-8 py-4">
          Waited
        </th>
          <th class="bg-blue-100 border text-left px-8 py-4">
          Duration
        </th>
          <th class="bg-blue-100 border text-left px-8 py-4">
          Devices
        </th>
          <th class="bg-blue-100 border text-left px-8 py-4">
          Pass%
        </th>
          <th class="bg-blue-100 border text-left px-8 py-4">
          Total
        </th>
          <th class="bg-blue-100 border text-left px-8 py-4">
          P
        </th>
          <th class="bg-blue-100 border text-left px-8 py-4">
          F
        </th>
          <th class="bg-blue-100 border text-left px-8 py-4">
          E
        </th>
          <th class="bg-blue-100 border text-left px-8 py-4">
          S
        </th>
    </tr>
  </thead>
  <tbody>
    {#each jobsList as jobs,index}
      <tr>
        <td class="border px-8 py-4">{index+1}</td>
        <td style="display: flex;
    margin-top: 10px;">
        {#if jobs.type == 'espresso'}
        <img
          style="width: 20px;height:25px"
                        src={espressoImage}
                        />
                        {/if}
                        {#if jobs.type == 'xcuitest'}

                          <img

                        src={darwinImage}
                        style="width: 20px;height:25px"/>
                        {/if}
                         {#if jobs.type == 'appium'}
                          <img
                          style="width: 20px;height:25px"

                        src={appiumImage}/>
                        {/if}
                          <span>{jobs.identifier}</span>
                          </td>
                          <td> <span>{users[jobs.user]}</span></td>
                          <td>{projectNames[jobs.project]}</td>
                          <td>
                      {checkWaitTime(
                        jobs.isStarted,
                        jobs.isPaused,
                        jobs.rerunAt,
                        jobs.created,
                        jobs.waitTime
                      )
                 }</td>
  <td>
          <span>{calculateDuration(jobs)}</span>
        </td>
<td>
          <span> -</span>
        </td>
        <td>
          <span>{getPercentage(jobs.pass, jobs.total - jobs.ignore)}</span>
        </td>
        <td>
          {#if jobs.isCompleted && jobs.totalRunTest == 0}
                  <span>{jobs.total - (jobs.skipped + jobs.ignore)}
                  </span>
               {/if}
                {#if jobs.isCompleted && jobs.totalRunTest == 0}
                  <span>{jobs.totalRunTest}</span>
                {/if}

                  /
                  <span>{jobs.total - jobs.ignore}</span>

        </td>
        <td>
          <span>{jobs.pass}</span>
        </td>
        <td>
          <span>{jobs.fail}</span>
        </td>
        <td>
          <span>{jobs.err}</span>
        </td>
        <td>
          <span>{jobs.skipped}</span>
        </td>
      </tr>
    {/each}
  </tbody>
</table>
  </div>
</main>

<style>
</style>
