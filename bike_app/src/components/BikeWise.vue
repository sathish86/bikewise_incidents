<template>
 <div>
  <b-container class="bv-example-row">
    <b-row>
      <b-form @submit="onSubmit">
         <b-col> 
          <b-form-group id="location_form_group"
                          label="Location:"
                          label-for="Location">
              <b-form-select id="location" class="mb-2"
                            :options="locations"
                            required
                            v-model="form.location">
              </b-form-select>
            </b-form-group>
          </b-col>
          <b-col>

      <b-form-group id="incident_form_group"
                          label="Incident:"
                          label-for="incident">
              <b-form-select id="incident" class="mb-2"
                            :options="incidents"
                            v-model="form.incident">
              </b-form-select>
            </b-form-group>
      </b-col>
      <b-col>
      <b-button type="submit" variant="primary">Submit</b-button>
      </b-col>
      </b-form>
  </b-row>
  <br/>
<b-row>
   <span>Summary</span>
   <b-table hover :items="summary_items" ></b-table>
  </b-row>
  <br/>
  <b-row>
    <span> Data </span>
   <b-table hover :items="items" :fields="fields" :current-page="currentPage" :per-page="perPage"></b-table>
   <b-pagination :total-rows="items.length" v-model="currentPage" :per-page="10" />
  </b-row>
</b-container>

  </div>
</template>

<script>
import axios from 'axios';
import moment from 'moment-timezone' 
const API_URL = "https://bikewise.org:443/api/v2/incidents"

export default {
  data() {
    return {
      form: {
        locations: null,
        incidents: null
      },
      locations: [
        { text: 'Select Location', value: null },
        'Melbourne', 'Sydney', 'Brisbane'
      ],
      incidents: [
        { text: '', value: null },
        'crash', 'hazard', 'theft', 'unconfirmed'
      ],
      items: [],
      summary_items : [],
      currentPage: 1,
      perPage: 10,
      fields: [
        {
          key: 'type',
          label: 'Incident Type',
          sortable: true
        },
        {
          key: 'occurred_at',
          label: 'Occurence Date',
          sortable: true
        },
        {
          key: 'title',
          label: 'Title',
          sortable: false
        },
        {
          key: 'address',
          label: 'Address',
          sortable: false
        }
      ]
    }
  },

  methods: {
    onSubmit (evt) {
      evt.preventDefault();
      if (this.incidents) {
          var param_data = {
          page: 1,
          per_page: 50,
          incident_type: this.form.incident,
          proximity: this.form.location,
          proximity_square: 100
        }
      }
        
      else {
        param_data = {
          page: 1,
          per_page: 50,
          proximity: this.form.location,
          proximity_square: 100
        }

      }

      axios.get(API_URL, {
        params: param_data 
      })
      .then(response => {
      // JSON responses are automatically parsed.
      console.log("Response Data")
      this.items = response.data.incidents
      this.summary_items = this.summary_calc()
      
    })

    },

    summary_calc : function() {
      var incident_occurences = {}, results = [], incident_type, occurred_at, utc_date_obj;
      for (var i = 0; i < this.items.length; i++) {
          incident_type = this.items[i].type;
          if (!(incident_type in incident_occurences)) {
              var incident_counts = {"morning": 0, "afternoon": 0, "evening": 0, "count": 0}
              incident_occurences[incident_type] = incident_counts;
          }
          incident_occurences[incident_type]["count"]++;
          
          occurred_at = this.items[i].occurred_at
          var tmp_time = moment.unix(occurred_at)
          utc_date_obj = moment.utc(tmp_time)
          this.items[i].occurred_at = utc_date_obj.tz('Australia/Sydney').format("YYYY-MM-DD HH:mm:ss ZZ")

          // calculate most common incident 
          utc_date_obj.hour(utc_date_obj.hour() + Math.round(utc_date_obj.minute()/60));
          utc_date_obj.mihour
          var hour_int = parseInt(utc_date_obj.hour())
          if ( hour_int > 0 && hour_int < 12 ){
            incident_occurences[incident_type]["morning"] += 1 
          }
          else if ( hour_int > 12 && hour_int < 18 ){
            incident_occurences[incident_type]["afternoon"] += 1 
          }
          else if ( hour_int > 18 && hour_int < 24 ){
            incident_occurences[incident_type]["evening"] += 1 
          } 
          
          
      }

      for(var element in incident_occurences) {
          results.push({ incident_type: element, count: incident_occurences[element]["count"], 
          common_incident_occurence: JSON.stringify(incident_occurences[element]) });
      }
      return results;
      }


  }
  
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
