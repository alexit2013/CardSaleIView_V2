<style scoped>

</style>

<template>

<div>
  
  <Modal
    v-model="IsModalShow"
    :title="modalFormTitle"
    :mask-closable="false"
    @on-cancel="cancel"
    width="600">
    <Form ref="modalForm" :model="modalForm" :label-width="110"  value=true  style="padding: 3px 30px">
      <Row>
        <Form-item label="运营商" prop="OperType" :rules="{required: true, message: '必填', trigger:'blur'}" >
          <RadioGroup v-model="modalForm.OperType" type="button" size="large" >
              <Radio label="中国电信"></Radio>
              <Radio label="中国移动"></Radio>
              <Radio label="中国联通"></Radio>
            </RadioGroup>
        </Form-item>
      </Row>
      <Row>
          <Form-item label="SIM卡类型" prop="CardType" :rules="{required: true, message: '必填', trigger:'blur'}" >
             <RadioGroup v-model="modalForm.CardType" type="button">
              <Radio label='单卡'></Radio>
              <Radio label='流量池'></Radio>
            </RadioGroup>
          </Form-item>

      </Row>
      <Row>
          <Form-item label="资费名称" prop="ExpName" :rules="{required: true, message: '必填', trigger:'blur'}" >
            <Input v-model="modalForm.ExpName" style="width:300px"> </Input> 
          </Form-item>

      </Row>
      <Row>

          <Form-item label="包含流量" prop="FlowSize" :rules="{required: true, message: '必填', trigger:'blur',type:'number'}" >
            <InputNumber v-model="modalForm.FlowSize" :precision=0></InputNumber>&nbsp
            <RadioGroup v-model="modalForm.FLowTypeRadio" type="button">
              <Radio label="MB"></Radio>
              <Radio label="GB"></Radio>
            </RadioGroup>
          </Form-item>

      </Row>
      <Row>
        <Form-item label="标准价格" prop="OfficialPirce" :rules="{required: true, message: '必填', trigger:'blur',type:'number'}" >
          <InputNumber v-model="modalForm.OfficialPirce" :max="9999" :min="0"></InputNumber>&nbsp元
        </Form-item>

      </Row>

       <Row>
        <Form-item label="有效时长" prop="ValidMonth" :rules="{required: true, message: '必填', trigger:'blur',type:'number'}" >
        <InputNumber v-model="modalForm.ValidMonth" :max="9999" :min="1" ></InputNumber>&nbsp月
        </Form-item>
      </Row>
    </Form>
    <div slot="footer">
      <Button type="ghost"  @click="cancel" >取消</Button>
      <Button type="primary"  :loading="modalForm_loading" @click="saveForm('modalForm')">保存</Button>
    </div>
  </Modal>
</div>

</template>

<script>
import {addRes_Expenses,editRes_Expenses} from './../../../api/getData'
export default {
    props:{
      parentForm: {
        type: Object,
        default: function () {
          return {
          Id:'',
          OperType: '1',
          CardType:'1',
          FLowTypeRadio:'1',
          ExpName:'',
          FlowSize:0,
          OfficialPirce:0,
          AgentPrice:0
          }
        }
      },
      modalShow:{
        type: Boolean,
        default: true,
      },
      modalFormTitle:{
        type: String,
        default: '添加资费配置',
      },
    },
    data() {
        return {
          IsModalShow:false,
          modalForm:{
          },
          modalForm_loading:false,

        }
    },
    watch:{
      modalShow(curVal,oldVal){
        this.modalForm=Object.assign(this.parentForm);
        this.IsModalShow = curVal;
      }
    },
    created(){
    },
    mounted(){
    
    },
    methods: {
      cancel() {
          this.$emit('listenModalForm');
      },
      saveForm(name) {
        console.log(this.modalForm)
        this.$refs[name].validate( async (valid) => {
          if (valid) {
            this.modalForm_loading=true;
            const params = this.modalForm;
            console.log(params)
            try{
              let result;
              if (this.modalFormTitle ==='添加资费配置'){
                 result = await addRes_Expenses(params);
              }else{
                 result = await editRes_Expenses(params);
              }
              if (result.success) {
                this.$Message.success('提交成功!');
                this.$emit('listenModalForm');
                this.$emit('refreshTableList');
              }else{
                this.$Message.error(result.msg);
              }
            }catch(err){
              console.log(err);
              this.$Message.error('服务器异常，稍后再试');
            }
            this.modalForm_loading=false;
          } else {
            this.$Message.error('表单验证失败!');
          }
        })
      },
    }
}

</script>
