<template>
    <div>
        <h2>Permission create/update</h2>    
       <b-alert :show="showDismissibleAlert" fade variant="danger">
          <ul >
            <li v-for="(item,index) of errors" :key="index">{{item}}</li>
          </ul>
       </b-alert>
        
        
      <b-form @submit="onSubmit" @reset="onReset">
            <b-form-group
              id="input-group-1"
              label="Employee Name:"
              label-for="input-1"
            >
              <b-form-input
                id="input-1"
                v-model="form.employeeName"                                
                placeholder="Employee Name"
              ></b-form-input>
            </b-form-group>

            <b-form-group id="input-group-2" label="Employee Last Name:" label-for="input-2" >
              <b-form-input
                id="input-2"
                v-model="form.lastNameEmployee"
                placeholder="Employee Last Name"
              ></b-form-input>
            </b-form-group>

            <b-form-group id="input-group-3" label="Permission Type:" label-for="input-3">
              <b-form-select
                id="input-3"
                v-model="form.permissionTypeId"
                :options="permissionType"
              ></b-form-select>
            </b-form-group>

            <b-form-group id="input-group-4" label="Permit Date:">
              <b-form-datepicker
                id="input-4"
                v-model="form.permitDate"
                placeholder="Choose a date"
                :date-format-options="{ year: 'numeric', month: 'short', day: '2-digit', weekday: 'short' }"
              ></b-form-datepicker>
            </b-form-group>

            <b-button type="submit" variant="primary">Submit</b-button>
            <b-button type="reset" variant="danger">Reset</b-button>
    </b-form>
    
    </div>
</template>
<script>
export default {
    name:"Permission",
    data(){
        return{
            form: {
                    employeeName: '',
                    lastNameEmployee: '',
                    permissionTypeId: null,
                    permitDate: ''
        },
       
        permissionType:[{ text: 'Select One', value: null }],
        showDismissibleAlert: false,
        errors:[]
        }                
    },
    created(){
        this.getAllPermissionType();
    },
    methods: {
        async getAllPermissionType(){
            try{
                 const getPermissionDB =  await this.axios.get("api/PermissionType/getAllPermissionType");
                 await getPermissionDB.data.forEach(element => {
                    let item = {};
                    item.value = element.id;
                    item.text = element.description;
                    this.permissionType.push(item);
                });

            }catch(error){
                console.log(error);
            }

        },
      async onSubmit(evt) {
        this.errors = [];
        this.showDismissibleAlert = false;
        if(!this.form.employeeName){
          this.errors.push("Employee Name is required.");          
        }

        if(!this.form.lastNameEmployee){
          this.errors.push("Employee last name is required.");           
        }

        if(this.form.permissionTypeId <= 0){
          this.errors.push("Select the permission type.");        
        }

        if(!this.form.permitDate){
          this.errors.push("Permit Date is required.");           
        }

        if(this.errors.length > 0){
          this.showDismissibleAlert = true;
          return;
        }        
        evt.preventDefault()
        //alert(JSON.stringify(this.form))
        // let formInput = JSON.stringify(this.form);
        const inputDB = await this.axios.post("api/Permission/registerPermission",this.form);
        alert(inputDB.data);

        this.form.employeeName = '';
        this.form.lastNameEmployee = '';
        this.form.permissionTypeId = null;
        this.permitDate = ''

      },
      onReset(evt) {
        evt.preventDefault()
        // Reset our form values
        this.form.employeeName = '';
        this.form.lastNameEmployee = '';
        this.form.permissionTypeId = null;
        this.permitDate = '';
        // Trick to reset/clear native browser form validation state
        this.show = false
       
      }
    }
}
</script>