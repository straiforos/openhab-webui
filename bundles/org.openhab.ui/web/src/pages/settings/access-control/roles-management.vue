<template>
  <f7-page >
    <f7-navbar :title="'Access Control'" back-link="Back" no-hairline>
      <f7-nav-right>

      </f7-nav-right>
    </f7-navbar>
    <f7-toolbar tabbar position="top">
      <f7-link class="tab-link">
        Design
      </f7-link>
      <f7-link  class="tab-link">
        Code
      </f7-link>
      <f7-link @click="save()">
        Save
      </f7-link>

    </f7-toolbar>
    <f7-row>
      <f7-col-xs-9  class="left_page">
        <form @submit.prevent="addGroup">
          <input v-model="newGroup" placeholder="Creat a new group"> </input>
          <button>Creat</button>
        </form>
        <form @submit.prevent="addRole">
          <input v-model="newRole" placeholder="Creat a new role"> </input>
          <button>Creat</button>
        </form>
      </f7-col-xs-9>
      <f7-col-xs-3  class="right_page">
        <f7-block-title>Available users</f7-block-title>
        <f7-list simple-list title="" v-for="user in this.accessControl.userAccessControlSet">
          <f7-list-item > {{ user.name }} </f7-list-item>
        </f7-list>
        <f7-block-title>Available Groups</f7-block-title>
        <f7-list simple-list title="" v-for="group in this.accessControl.groups">
          <f7-list-item > {{ group.group }} </f7-list-item>
        </f7-list>
        <f7-block-title>Available Roles</f7-block-title>
        <f7-list simple-list title="" v-for="role in this.accessControl.roles">
          <f7-list-item > {{ role.role }} </f7-list-item>
        </f7-list>

      </f7-col-xs-3>
    </f7-row>



  </f7-page>
</template>

<style lang="stylus">

.left_tool_bar
  width

.left_page
  height 100%
  width 70%
.right_page
  height 100%
  width 30%

</style>

<script>


export default {
  data () {
    return {
      accessControl: {
        userAccessControlSet:[
          {
            name: "nico",
            roles: ["role1", "role2"],
            groups: ["group1"]
          }],
        groups: [{group: "group1", roles:["role1", "role2"]}, {group:"group3", roles:["role1","role2"]}],
        roles: [{role:"role1", items:["item1","item2"]},{role:"role2", items:["item3","item4"]},{role:"role3", items:["item3","item2"]}]
      },
      newGroup:'',
      newRole:''
    }
  },
  created () {
    console.log('Access control')
    const promise = this.$oh.api.getPlain('/rest/accessControl', 'text/plain')
    promise.then((data1) => {
      console.log(data1)
      //accessControl = JSON.stringify(data1)

    })
  },
  computed: {

  },

  methods: {
    reviver(key, value){
      console.log(key)
      if (key === 'userAccessControlSet') {
        console.log(value)
        let users = []
        let newUser = {
          name:'',
          roles:{},
          groups:{},
        }
        for(let user in value){
          newUser.name = user.name

        }
        users.push(newUser.name)

        return users
      }
      if(key === 'groups'){
        if(Array.isArray(value)){
          console.log(value)
        }

      }
      if(key === 'roles'){

      }
    },
    addGroup(){
        this.accessControl.groups.push({group:this.newGroup , roles:[]})
        this.newGroup = ''
    },
    addRole(){
      this.accessControl.roles.push({role:this.newRole , items:[]})
      this.newRole = ''
    },
    save (stay) {

      console.log('Access control')
      const promise = this.$oh.api.getPlain('/rest/accessControl', 'text/plain')
      promise.then((data1) => {
        //console.log(data1)
        let accessControlTemp = JSON.parse(data1)
        this.accessControl = accessControlTemp

      })

      let accessControl;
      accessControl = {
        userAccessControlSet:[
          {
            name: "nico",
            roles: ["role1", "role2"],
            groups: ["group1"]
          },
          {
            name: "nico2",
            roles: ["role1", "role2"],
            groups: ["group1"]
          }],
        groups: [{group: "group1", roles:["role1", "role2"]}, {group:["group3"], roles:["role1","role2"]}],
        roles: [{role:"role1", items:["item1","item2"]},{role:"role2", items:["item3","item4"]},{role:"role3", items:["item3","item2"]}]
      }


      console.log('PUT PLAIN THINGS')
      this.$oh.api.putPlain('/rest/accessControl/put', JSON.stringify(this.accessControl)).then( (data) => {
            //console.log(data)
        }
      )
    }
  }
}
</script>
