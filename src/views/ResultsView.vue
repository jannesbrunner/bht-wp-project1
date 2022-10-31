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
                <tr v-for="test in tests" :key="test">
                    <td>{{test.name}}</td>
                    <td>{{test.age}}</td>
                    <td>
                        <span v-for="result in getPassedResults(test)" :key="result">
                            {{result}} &nbsp;
                        </span>
                    </td>
                    <td>
                        <span v-for="result in getFailedResults(test)" :key="result">
                            {{result}} &nbsp;
                        </span>
                    </td>
                </tr>
            </table>
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
    }
  }

</script>


  <style>
  @media (min-width: 1024px) {
    .about {
      display: flex;
      flex-direction: column;
    }
    table th {
    
        padding: 10px;
        font-weight: bold;
    }
  }
  </style>
  