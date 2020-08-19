<template>
  <div class="about">
   <div>
        <h2>Permission List</h2>
        <b-table striped hover :items="permissions" :fields="fields">
            <template v-slot:cell(actions)="row">
                <b-button size="sm" variant="danger" @click="notification(row)"  class="mr-2">
                      Delete
                </b-button>
                <b-button size="sm" variant="success" @click="showEdit(row)">
                    Edit
                </b-button>
            </template>
        </b-table>       
           
            <b-modal id="modal-1" ref="my-modal" hide-footer title="Permission">
               <div><p class="my-4">Are you sure, the eliminate this permission? {{permissonId}}
                    {{message}}
                   </p>
                </div>
                    <b-button class="mt-3" variant="outline-danger" block @click="deleted">Ok</b-button>
                    <b-button class="mt-2" variant="outline-warning" block @click="toggleModal">Close</b-button>
            </b-modal>

             <b-modal id="modal-edit" ref="my-modal-edit" hide-footer title="Permission">
               <div>
                   <b-form >
            <b-form-group
              id="input-group-1"
              label="Employee Name:"
              label-for="input-1"
            >
              <b-form-input
                id="input-1"
                v-model="form.employeeName"         
                required          
                placeholder="Employee Name"
              ></b-form-input>
            </b-form-group>

            <b-form-group id="input-group-2" label="Employee Last Name:" label-for="input-2" >
              <b-form-input
                id="input-2"
                v-model="form.lastNameEmployee"
                required
                placeholder="Employee Last Name"
              ></b-form-input>
            </b-form-group>

            <b-form-group id="input-group-3" label="Permission Type:" label-for="input-3">
              <b-form-select
                id="input-3"
                v-model="form.permissionTypeId"
                :options="permissionType"
                required
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

            <b-button variant="success" @click="updatePermission()" >save</b-button>
            
    </b-form>
                </div>
                    
            </b-modal>
    </div>
  </div>
</template>
<script>
export default {
    name:"Permission",
    data(){
        return{
            fields: ['id', 'employeeName', 'lastNameEmployee','permitDate','actions'],
            permissions:[],
            permissonId:0,
            message:"",
            form: {
                    employeeName: '',
                    lastNameEmployee: '',
                    permissionTypeId: null,
                    permitDate: ''
        },
       
        permissionType:[{ text: 'Select One', value: null }],
        }        
    },
    created(){
        this.getPermission();
        this.getAllPermissionType();
    },
    methods:{
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
        async getPermission(){
            try{

                 const getPermissionDB =  await this.axios.get("api/Permission/getAllPermissions");
                //console.log(getPermissionDB.data);
                await getPermissionDB.data.forEach(element => {
                    let item = {};
                    item.id = element.id;
                    item.employeeName = element.employeeName;
                    item.lastNameEmployee = element.lastNameEmployee;
                    item.permissionTypeId = element.permissionTypeId;
                    item.permitDate = element.permitDate;
                    this.permissions.push(item);
                });
            }catch(error){
                console.log(error);
            }
        },
       async showEdit(row){
            console.log(row.item);
             this.form.id = row.item.id;
             this.form.employeeName = row.item.employeeName;
             this.form.lastNameEmployee = row.item.lastNameEmployee;
             this.form.permissionTypeId = row.item.permissionTypeId;
             this.form.permitDate = row.item.permitDate

             this.$refs['my-modal-edit'].show()
        },
        async updatePermission(){
          try{
              const inputDB = await this.axios.put("api/Permission/updatePermission",this.form);
                
            console.log(inputDB.data); 
             this.$refs['my-modal-edit'].hide()
             this.permissions = [];
             this.getPermission();

             this.form.employeeName = '';
             this.form.lastNameEmployee = '';
             this.form.permissionTypeId = null;
             this.permitDate = ''

          }catch(error){
              console.log(error);
          }
          
        },
        notification(row){            
            this.$refs['my-modal'].show()
            console.log(row.item);
            this.permissonId = row.item.id;
        },
        async deleted(){
            try{
                    const getPermissionDB =  await this.axios.delete("api/Permission/deletePermission?id="+this.permissonId);
                    this.message = getPermissionDB.data;
                    this.$refs['my-modal'].hide()
                    this.permissions = [];
                    this.getPermission();                    
            }catch(error){
                console.log(error);
            }
        },
        toggleModal(){
            this.$refs['my-modal'].hide()
        }
    }
}
</script>