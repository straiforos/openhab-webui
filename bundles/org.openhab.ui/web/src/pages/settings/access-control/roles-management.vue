<template>
  <f7-page>
    <f7-navbar :title="'Access Control'" back-link="Back" no-hairline>
      <f7-nav-right />
    </f7-navbar>
    <f7-toolbar tabbar position="top">
      <f7-link class="tab-link">
        Design
      </f7-link>
      <f7-link class="tab-link">
        Code
      </f7-link>
      <f7-link @click="save()">
        Save
      </f7-link>
    </f7-toolbar>
    <f7-row>
      <f7-col width="75">
        <f7-block-title>Users</f7-block-title>
        <f7-list v-for="user in this.accessControl.userAccessControlSet" :key="user.name">
          <f7-block-title>{{ user.name }}</f7-block-title>
          <f7-list-item>
            <li>
              Available groups for {{ user.name }}:
              <f7-list-item v-for="group in user.groups" :key="group">
                {{ group }}
              </f7-list-item>
              <f7-list-input
                type="text"
                placeholder="Add a new group"
                clear-button
                :value="getGroupToUserInput(group)"
                @input="setGroupToUserInput($event.target.value)" />
              <f7-button @click="addGroupToUser(user.name)">
                Add The a new group to the user {{ user.name }}
              </f7-button>
            </li>
          </f7-list-item>
          <f7-list-item>
            <li>
              Available roles for {{ user.name }}:
              <f7-list-item v-for="role in user.roles" :key="role">
                {{ role }}
              </f7-list-item>
              <f7-list-input
                type="text"
                placeholder="Add a new role"
                clear-button
                :value="newRoleToUser"
                @input="newRoleToUser = $event.target.value" />
              <f7-button @click="addRoleToUser(user.name)">
                Add The a new role to the user {{ user.name }}
              </f7-button>
            </li>
          </f7-list-item>
        </f7-list>

        <f7-block-title>Groups</f7-block-title>
        <f7-list v-for="group in accessControl.groups" :key="group">
          <f7-block-title>{{ group.group }}</f7-block-title>
          <f7-list-item>
            <li>
              Available roles for {{ group.group }}:
              <f7-list-item v-for="role in group.roles" :key="role">
                {{ role }}
              </f7-list-item>
              <f7-list-input
                type="text"
                placeholder="Add a new role"
                clear-button
                :value="newRoleToGroup"
                @input="newRoleToGroup = $event.target.value" />
              <f7-button @click="addRoleToGroup(group.group)">
                Add The a new role to the group {{ group.group }}
              </f7-button>
            </li>
          </f7-list-item>
        </f7-list>
      </f7-col>

      <f7-col width="25">
        <f7-block-title>Available users</f7-block-title>
        <f7-list simple-list v-for="user in this.accessControl.userAccessControlSet" :key="user.name">
          <f7-list-item> {{ user.name }} </f7-list-item>
        </f7-list>
        <f7-block-title>Available Groups</f7-block-title>
        <f7-list simple-list v-for="group in this.accessControl.groups" :key="group.group">
          <f7-list-item> {{ group.group }} </f7-list-item>
        </f7-list>
        <f7-block-title>Available Roles</f7-block-title>
        <f7-list simple-list v-for="role in this.accessControl.roles" :key="role.role">
          <f7-list-item> {{ role.role }} </f7-list-item>
        </f7-list>
      </f7-col>
    </f7-row>

    <f7-row>
      <f7-col-xs-9 class="left_page">
        <form @submit.prevent="addGroup">
          <input v-model="newGroup" placeholder="Create a new group"> </input>
          <button>Create</button>
        </form>
        <form @submit.prevent="addRole">
          <input v-model="newRole" placeholder="Create a new role"> </input>
          <button>Create</button>
        </form>
      </f7-col-xs-9>
      <f7-col-xs-3 class="right_page" />
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
        userAccessControlSet: [
          {
            name: 'nico',
            roles: ['role1', 'role2'],
            groups: ['group1']
          }],
        groups: [{ group: 'group1', roles: ['role1', 'role2'] }, { group: 'group3', roles: ['role1', 'role2'] }],
        roles: [{ role: 'role1', items: ['item1', 'item2'] }, { role: 'role2', items: ['item3', 'item4'] }, { role: 'role3', items: ['item3', 'item2'] }]
      },
      newGroupToUser: [{ groupName: '', value: '' }],
      newRoleToUser: [{ roleName: '', value: '' }],
      newRoleToGroup: '',
      newGroup: '',
      newRole: ''
    }
  },
  created () {
    console.log('Access control')
    const promise = this.$oh.api.getPlain('/rest/accessControl', 'text/plain')
    promise.then((data1) => {
      console.log(data1)
      this.accessControl = JSON.parse(data1)
    })
  },
  computed: {

  },

  methods: {

    addGroup () {
      this.accessControl.groups.push({ group: this.newGroup, roles: [] })
      this.newGroup = ''
    },
    addRole () {
      this.accessControl.roles.push({ role: this.newRole, items: [] })
      this.newRole = ''
    },
    addGroupToUser (name) {
      const newGroup = this.newGroupToUser
      let userTemp = this.accessControl.userAccessControlSet.find(user => user.name === name)
      userTemp.groups.push(newGroup)
    },
    getGroupToUserInput (group) {
      return this.newGroupToUser.find(groupName => group === groupName.groupName).value
    },
    setGroupToUserInput (group) {
      this.newGroupToUser.find(groupName => group === groupName.groupName).value = group;
    },
    addRoleToUser (name) {
      const newRole = this.newRoleToUser
      let userTemp = this.accessControl.userAccessControlSet.find(user => user.name === name)
      userTemp.roles.push(newRole)

      this.accessControl.userAccessControlSet.splice(name, 1)
      this.accessControl.userAccessControlSet.push(userTemp)
      this.newRoleToUser = ''
    },
    addRoleToGroup (group) {
      const getGroup = this.accessControl.groups.find(group => group.group === group)
      getGroup.push(group)
      this.newRoleToGroup = ''
    },
    save (stay) {
      let accessControl
      accessControl = {
        userAccessControlSet: [
          {
            name: 'nico',
            roles: ['role1', 'role2'],
            groups: ['group1']
          },
          {
            name: 'nico2',
            roles: ['role1', 'role2'],
            groups: ['group1']
          }],
        groups: [{ group: 'group1', roles: ['role1', 'role2'] }, { group: ['group3'], roles: ['role1', 'role2'] }],
        roles: [{ role: 'role1', items: ['item1', 'item2'] }, { role: 'role2', items: ['item3', 'item4'] }, { role: 'role3', items: ['item3', 'item2'] }]
      }

      console.log('PUT PLAIN THINGS')
      this.$oh.api.putPlain('/rest/accessControl/put', JSON.stringify(this.accessControl)).then((data) => {
        console.log(data)
      }
      )
    }
  }
}
</script>
