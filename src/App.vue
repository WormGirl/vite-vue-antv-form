<script setup lang="ts">
  import { reactive, toRaw, computed } from 'vue';
  import { toArray, cloneDeep } from 'lodash-es';
  import { Form } from 'ant-design-vue';

  const useForm = Form.useForm;

  interface FormItem {
    key: number
    value: string
  }
  const modelRef = reactive({
    name: '',
    region: undefined,
    type: [],
    list: [] as FormItem[]
  });
  const rulesRef = reactive({
    name: [
      {
        required: true,
        message: 'Please input name',
      },
    ],
    region: [
      {
        required: true,
        message: 'Please select region',
      },
    ],
    type: [
      {
        required: true,
        message: 'Please select type',
        type: 'array',
      },
    ],
  });

  // const initialModel = cloneDeep(modelRef);

  // const resetFields = () => {
  //   Object.assign(modelRef, {
  //     ...cloneDeep(initialModel)
  //   });
  // }
  
  const { validate, validateInfos, mergeValidateInfo, resetFields } = useForm(modelRef, rulesRef);

  const onSubmit = () => {
    validate()
      .then(() => {
        console.log(toRaw(modelRef));
      })
      .catch(err => {
        console.log('error', err);
      });
  };
  const errorInfos = computed(() => {
    return mergeValidateInfo(toArray(validateInfos));
  });
  const labelCol = { span: 4 };
  const wrapperCol = { span: 14 };

  // 添加表单
  const add = () => {
    modelRef.list.push({
      value: '',
      key: Date.now(),
    });
  }
</script>

<template>
  <a-form :label-col="labelCol" :wrapper-col="wrapperCol">
    <a-form-item label="Activity name" required>
      <a-input v-model:value="modelRef.name" />
    </a-form-item>
    <a-form-item label="Activity zone" required>
      <a-select v-model:value="modelRef.region" placeholder="please select your zone">
        <a-select-option value="shanghai">Zone one</a-select-option>
        <a-select-option value="beijing">Zone two</a-select-option>
      </a-select>
    </a-form-item>
    <a-form-item label="Activity type" required>
      <a-checkbox-group v-model:value="modelRef.type">
        <a-checkbox value="1" name="type">Online</a-checkbox>
        <a-checkbox value="2" name="type">Promotion</a-checkbox>
        <a-checkbox value="3" name="type">Offline</a-checkbox>
      </a-checkbox-group>
    </a-form-item>
    <a-form-item :label="modelRef.list.length > 0 ? '动态表单' : ''" required>
      <a-form-item
        v-for="(item, index) in modelRef.list"
        :key="item.key"
        :name="['list', index, 'value']"
        :rules="{
          required: true,
          message: 'name can not be null',
          trigger: 'change',
        }"
      >
        <a-input v-model:value="item.value" />
      </a-form-item>
    </a-form-item>
    <a-form-item class="error-infos" :wrapper-col="{ span: 14, offset: 4 }" v-bind="errorInfos">
      <a-button type="link" @click="add">添加表单</a-button>
      <a-button style="margin-left: 10px" type="primary" @click.prevent="onSubmit">Create</a-button>
      <a-button style="margin-left: 10px" @click="resetFields">Reset</a-button>
    </a-form-item>
  </a-form>
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
