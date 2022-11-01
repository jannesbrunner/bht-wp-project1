<template>
    <div class="about">
      <h1>Test Results</h1>
        <div v-if="tests.length > 0">
            <table style="text-align:center">
                <th width=100>Name</th>
                <th width=100>Age</th>
                <th width=100>Able</th>
                <th width=100>Unable</th>
                <th width=100>Score</th>
                <tr v-for="(test, index) in tests" :key="test">
                    
                    <td>{{test.name}}</td>
                    <td>{{test.age}}</td>
                    <td>
                        <div class="toggleButton" v-if="getPassedResults(test).length > 0">
                          <button @click="toggleVisibility(index+'passed')">
                            Toggle
                        </button>
                        </div>
                        <div v-else>
                        None
                      </div>
                        <div :id="index+'passed'" class="hide">
                        <span v-for="result in getPassedResults(test)" :key="result">
                            {{result}} &nbsp;
                        </span>
                      </div>
                    </td>
                    <td>
                      <div class="toggleButton" v-if="getFailedResults(test).length > 0">
                        <button @click="toggleVisibility(index+'failed')">
                          Toggle
                        </button>
                      </div>
                      <div v-else>
                        None
                      </div>
                        <div :id="index+'failed'" class="hide">
                        <span v-for="result in getFailedResults(test)" :key="result">
                            {{result}} &nbsp;
                        </span>
                      </div>
                    </td>
                    <td>{{getResult(index)}}</td>
                </tr>
            </table>
            <button @click="delData">Delete All Data</button>
        </div>
    <div v-else>
        <p>No tests collected yet</p>
    </div>
    </div>
</template>
  
<script>
export default {
  name: 'ResultsView',
  data() {
    return {
      tests: [],
    }
  },
  created() {
    console.log('mounted');
    // get data from localstorage if exists
    let data = localStorage.getItem('testResults');
      if(data) {
        data = JSON.parse(data);
      } else {
        data = [];
      }
      this.tests = data;


     
  },
  methods: {
    getPassedResults(test) {
        let passed = [];
        test.answers.forEach(answer => {
            if (answer.isYes) {
            passed.push(answer.frequency);
            }
        });
        return passed;
        },
    getFailedResults(test) {
        let failed = [];
        test.answers.forEach(answer => {
            if (!answer.isYes) {
            failed.push(answer.frequency);
            }
        });
        return failed;
        },
      toggleVisibility(ref) {
        document.getElementById(ref).classList.toggle('hide');
      },
      getResult(testId) {
        let passed = 0;
        let failed = 0;
        this.tests[testId].answers.forEach(answer => {
          if (answer.isYes) {
            passed++;
          } else {
            failed++;
          }
        });

        // limit number to 2 decimal places
        return (passed / (passed + failed) * 100).toFixed(2) + '%';
      },
      delData() {
        let ok = confirm('All data will be deleted! Are you sure?');
        if(ok) {
          localStorage.removeItem('testResults');
          this.tests = [];
        }
      }
    },
    computed: {
      
    }
    
  }

</script>


<style>
    @media (min-width: 1024px) {
    .about {
      display: flex;
      flex-direction: column;
    }
    table tr:nth-child(odd) td{
    border: 1px solid red;
    
    }
    table tr:nth-child(even) td{
    border: 1px solid green;
    
    }

    table th {
    
        padding: 10px;
        font-weight: bold;
    }
    td span {
        font-size: 10px;
    }

    table td {
        padding: 10px;
        border: 1px solid black;
    }

    .show {
        display: block;
    }
    .hide {
      display: none;
    }
    button {
      background-color: #4CAF50; /* Green */
      border: none;
      color: white;
      padding: 5px 10px;
      text-align: center;
      text-decoration: none;
      display: inline-block;
      font-size: 12px;
      margin: 4px 2px;
      cursor: pointer;
    }
  }
</style>
  