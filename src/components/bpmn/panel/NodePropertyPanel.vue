<template>
  <div>
    <el-form :inline="false"
             label-width="100px"
             size="small"
             label-position="right">
      <el-form-item label="节点类型">
        <el-input v-model="formData.type" disabled></el-input>
      </el-form-item>
      <el-form-item label="节点ID">
        <el-input v-model="formData.id" @input="updateId"></el-input>
      </el-form-item>
      <el-form-item label="节点名称">
        <el-input v-model="formData.name" @input="updateName"></el-input>
      </el-form-item>

      <!--usertask-->
      <el-form-item v-if="formData.type=='bpmn:UserTask'" label="节点人员">
        <el-select v-model="formData.userType" placeholder="请选择" @change="changeUserType">
          <el-option value="assignee" label="指定人员"></el-option>
          <el-option value="candidateUsers" label="候选人员"></el-option>
          <el-option value="candidateGroups" label="角色/岗位"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="指定人员" v-if="formData.type=='bpmn:UserTask' && formData.userType === 'assignee'">
        <el-select
            v-model="formData.assignee"
            placeholder="请选择"
            key="1"
            @change="(value) => addUser({assignee: value})"
        >
          <el-option
              v-for="item in bpmnData.assignees"
              :key="item.value"
              :label="item.label"
              :value="item.value"
          ></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="候选人员" v-else-if="formData.type=='bpmn:UserTask' && formData.userType === 'candidateUsers'">
        <el-select
            v-model="formData.candidateUsers"
            placeholder="请选择"
            key="2"
            multiple
            @change="(value) => addUser({candidateUsers: value.join(',') || value})"
        >
          <el-option
              v-for="item in bpmnData.candidateUsers"
              :key="item.value"
              :label="item.label"
              :value="item.value"
          ></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="角色/岗位" v-else-if="formData.type=='bpmn:UserTask' && formData.userType === 'candidateGroups'">
        <el-select
            v-model="formData.candidateGroups"
            placeholder="请选择"
            @change="(value) => addUser({candidateGroups: value})"
        >
          <el-option
              v-for="item in bpmnData.roles"
              :key="item.value"
              :label="item.label"
              :value="item.value"
          ></el-option>
        </el-select>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
  export default {
    name: "NodePropertyPanel",
    data() {
      return {
        bpmnData: {
          assignees: [{
            value: "zhangsan",
            label: "张三"
          }, {
            value: "lisi",
            label: "李四"
          }, {
            value: "wangwu",
            label: "王五"
          }],
          candidateUsers:[{
            value: "zhangsan",
            label: "张三"
          }, {
            value: "lisi",
            label: "李四"
          }, {
            value: "wangwu",
            label: "王五"
          }],
          roles: [
            {
              value: "manager",
              label: "经理"
            },
            {
              value: "personnel",
              label: "人事"
            },
            {
              value: "charge",
              label: "主管"
            }
          ]
        }
      }
    },
    props: {
      modeler: {
        type: Object,
        required: true
      },
      nodeElement: {
        type: Object,
        required: true
      },
      formData:{
        type: Object,
        required: true
      }
    },
    methods: {
      updateProperties(properties){
        this.modeler.get("modeling").updateProperties(this.nodeElement, properties);
      },
      updateId(name) {
        this.updateProperties({id: name});
      },
      updateName(name) {
        // this.modeler.get("modeling").updateProperties(this.nodeElement, {name: name});
        this.updateProperties({name: name});
      },
      changeUserType() {
        // console.log(val)
      },
      addUser(properties){
        console.log(properties)
        this.updateProperties(
          Object.assign(properties, {
            userType: Object.keys(properties)[0]
          })
        );
      }
    }
  }
</script>

<style scoped>

</style>
