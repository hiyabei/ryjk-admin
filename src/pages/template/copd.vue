<template>
  <div id="template">
    <tab-header :personInfo="personInfo"></tab-header>
    <el-form ref="form" :model="form" label-width="135px" class="createVisit_form">
      <el-card>
        <el-row :gutter="0">
          <el-col :span="8">
            <el-form-item label="随访状态">
              <el-select v-model="form.status" placeholder="请选择">
                <el-option
                  v-for="item in sfstatus"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="随访结果">
              <el-select v-model="form.result" placeholder="请选择">
                <el-option
                  v-for="item in sfresult"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="随访方式">
              <el-select v-model="form.type" placeholder="请选择">
                <el-option
                  v-for="item in sftype"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>

        <el-row :gutter="0">
          <el-col :span="8">
            <el-form-item label="出院/转出情况">
              <el-select v-model="form.dischargeStatus" placeholder="请选择">
                <el-option
                  v-for="item in sfdischargeStatus"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="本次随访评估">
              <el-cascader
                v-model="form.assessment"
                :options="sfassessment"
                :show-all-levels="false"
              ></el-cascader>
            </el-form-item>
          </el-col>
        </el-row>
      </el-card>
      <el-card>
        <div slot="header">
          <h2>目前症状</h2>
        </div>
        <el-row :gutter="0">
          <el-col :span="8">
            <el-form-item label="有无症状">
              <el-select
                v-model="form.visitRecordContent.issymptom"
                @change="sfsymptomChange($event,4)"
                placeholder="请选择"
              >
                <el-option
                  v-for="item in sfsymptom"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="8" v-show="issfsymptomName">
            <el-form-item label="请选择症状">
              <el-select v-model="form.symptom" multiple placeholder="请选择">
                <el-option
                  v-for="item in sfsymptomName"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>
      </el-card>

      <el-card>
        <div slot="header">
          <h2>体征</h2>
        </div>
        <el-row>
          <h3>体重</h3>
        </el-row>
        <el-row :gutter="0">
          <el-col :span="8">
            <el-form-item label="体重范围">
              <el-select v-model="form.visitRecordContent.weightType" placeholder="请选择">
                <el-option
                  v-for="item in sfweight"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="具体体重数值">
              <el-input-number v-model="form.visitRecordContent.weightNum"></el-input-number>
            </el-form-item>
            <span class="unit">KG</span>
          </el-col>
        </el-row>
        <el-row>
          <h3>血压</h3>
        </el-row>
        <el-row :gutter="0">
          <el-col :span="8">
            <el-form-item label="高压">
              <el-input-number v-model="form.visitRecordContent.hypertension" :min="0" :max="9999"></el-input-number>
            </el-form-item>
            <span class="unit">mmHg</span>
          </el-col>
          <el-col :span="4"></el-col>
          <el-col :span="8">
            <el-form-item label="低压">
              <el-input-number v-model="form.visitRecordContent.hypotension" :min="0" :max="9999"></el-input-number>
            </el-form-item>
            <span class="unit">mmHg</span>
          </el-col>
        </el-row>
        <el-row>
          <h3>化验数值</h3>
        </el-row>
        <el-row :gutter="0">
          <el-col :span="8">
            <el-form-item label="白细胞(WBC)">
              <el-input-number v-model="form.visitRecordContent.hemameba" :min="0" :max="9999">/L</el-input-number>
            </el-form-item>
            <span class="unit">/L</span>
          </el-col>
        </el-row>
        <el-row>
          <h3>体温</h3>
        </el-row>
        <el-row :gutter="0">
          <el-col :span="8">
            <el-form-item label="体温">
              <el-input-number v-model="form.visitRecordContent.animalheat" :min="0" :max="9999"></el-input-number>
            </el-form-item>
            <span class="unit">℃</span>
          </el-col>
        </el-row>
        <el-row>
          <h3>异常指标</h3>
        </el-row>
        <el-row :gutter="0">
          <el-col :span="8">
            <el-form-item label="有无异常">
              <el-select v-model="form.visitRecordContent.anomalyIndex" placeholder="请选择">
                <el-option
                  v-for="item in sfanomalyIndex"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="指标值">
              <el-input v-model="form.visitRecordContent.anomalyIndexValue"></el-input>
            </el-form-item>
          </el-col>
        </el-row>
      </el-card>
      <el-card>
        <div slot="header">
          <h2>生活方式</h2>
        </div>
        <el-row :gutter="0">
          <el-col :span="8">
            <el-form-item label="吸烟史">
              <el-select v-model="form.visitRecordContent.smokingHistory" placeholder="请选择">
                <el-option
                  v-for="item in sfsmokingHistory"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="是否戒烟">
              <el-select
                v-model="form.smokingVolume"
                @change="smokingVolumeChange"
                placeholder="请选择"
              >
                <el-option
                  v-for="item in sfsmokingVolume"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="8" v-show="isSmokingAmount">
            <el-form-item label="吸烟量">
              <el-input-number v-model="form.visitRecordContent.smokingAmount" :min="0" :max="9999"></el-input-number>
            </el-form-item>
            <span class="unit">支/天</span>
          </el-col>
        </el-row>

        <el-row :gutter="0">
          <el-col :span="8">
            <el-form-item label="饮酒史">
              <el-select v-model="form.visitRecordContent.drinkingHistory" placeholder="请选择">
                <el-option
                  v-for="item in sfdrinkingHistory"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="是否戒酒">
              <el-select
                v-model="form.alcoholConsumption"
                @change="alcoholConsumptionChange"
                placeholder="请选择"
              >
                <el-option
                  v-for="item in sfalcoholConsumption"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="8" v-show="isAlcoholConsumptionAmount">
            <el-form-item label="饮酒量">
              <el-input-number
                v-model="form.visitRecordContent.alcoholConsumptionAmount"
                :min="0"
                :max="9999"
              ></el-input-number>
            </el-form-item>
            <span class="unit">ML/天</span>
          </el-col>
        </el-row>
        <el-row :gutter="0">
          <el-col :span="8">
            <el-form-item label="睡眠情况">
              <el-select v-model="form.visitRecordContent.sleepQuality" placeholder="请选择">
                <el-option
                  v-for="item in sfsleep"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row :gutter="0">
          <el-col :span="8">
            <el-form-item label="心理调整">
              <el-select v-model="form.visitRecordContent.psychologicalRecovery" placeholder="请选择">
                <el-option
                  v-for="item in sfregion"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row :gutter="0">
          <el-col :span="8">
            <el-form-item label="饮食情况">
              <el-select v-model="form.visitRecordContent.biteAndSup" placeholder="请选择">
                <el-option
                  v-for="item in sfbiteAndSup"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row :gutter="0">
          <el-col :span="8">
            <el-form-item label="运动情况">
              <el-select v-model="form.visitRecordContent.sportSituation" placeholder="请选择">
                <el-option
                  v-for="item in sfsportSituation"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
                    <el-col :span="2"></el-col>
          <el-col :span="8">
            <el-form-item label="运动强度">
              <el-select v-model="form.visitRecordContent.exerciseIntensity" placeholder="请选择">
                <el-option
                  v-for="item in sfexerciseIntensity"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row :gutter="0">
          <el-col :span="8">
            <el-form-item label="运动">
              <el-input-number v-model="form.visitRecordContent.motionNum" :min="0" :max="9999"></el-input-number>
            </el-form-item>
            <span class="unit">次/周</span>
          </el-col>
                    <el-col :span="2"></el-col>
          <el-col :span="8">
            <el-form-item label>
              <el-input-number v-model="form.visitRecordContent.otionLength" :min="0" :max="9999"></el-input-number>
            </el-form-item>
            <span class="unit">分钟/次</span>
          </el-col>
        </el-row>
        <el-row :gutter="0">
          <el-col :span="8">
            <el-form-item label="心理状况">
              <el-select v-model="form.visitRecordContent.psychologicSituation" placeholder="请选择">
                <el-option
                  v-for="item in sfregion"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>
      </el-card>
      <el-card>
        <div slot="header">
          <h2>用药情况</h2>
        </div>
        <el-row :gutter="0">
          <el-col :span="8">
            <el-form-item label="依从性">
              <el-select v-model="form.medicationCompliance">
                <el-option
                  v-for="item in sfmedicationCompliance"
                  :key="item.value"
                  :value="item.value"
                  :label="item.label"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
                    <el-col :span="2"></el-col>
          <el-col :span="8">
            <el-form-item label="是否需要用药">
              <el-select v-model="form.visitRecordContent.isNeed">
                <el-option
                  v-for="item in sfisNeed"
                  :key="item.value"
                  :value="item.value"
                  :label="item.label"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row
          :gutter="0"
          v-for="(dosage, index) in  form.visitRecordContent.dosages"
          :key="index"
        >
          <el-col :span="8">
            <el-form-item label="药物名称">
              <el-input v-model="dosage.value"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="4">
            <el-form-item>
              <el-input-number v-model="dosage.frequency" :min="0" :max="9999" label="次"></el-input-number>
            </el-form-item>
            <span class="unit">次/日</span>
          </el-col>
          <el-col :span="4">
            <el-form-item>
              <el-input-number v-model="dosage.dose" :min="0" :max="9999" label="mg"></el-input-number>
            </el-form-item>
            <span class="unit">mg/次</span>
          </el-col>
          <el-col :span="4">
            <el-form-item>
              <el-button @click.prevent="removeDosage(dosage)">删除</el-button>
            </el-form-item>
          </el-col>
          <el-col :span="4">
            <el-form-item>
              <el-button @click.prevent="addDosage">新增</el-button>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row></el-row>
        <!-- 药物不良反应 -->
        <select-input
          :selectInputData="reactionsData"
          @listenSelect="reactionsSelect"
          @listenInput="reactionsInput"
        ></select-input>
      </el-card>
      <el-card>
        <div slot="header">
          <h2>健康教育知晓</h2>
        </div>

        <el-row :gutter="0">
          <el-col :span="8">
            <el-form-item label="健康指导">
              <el-select
                v-model="form.visitRecordContent.healthGuidance"
                @change="healthGuidanceChange"
              >
                <el-option
                  v-for="item in sfhealthGuidance"
                  :key="item.value"
                  :value="item.value"
                  :label="item.label"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="8" v-show="ishealthGuidanceContent">
            <el-form-item label="健康指导内容">
              <el-select v-model="form.visitRecordContent.healthGuidanceContent" multiple>
                <el-option
                  v-for="item in sfhealthGuidanceContent"
                  :key="item.value"
                  :value="item.value"
                  :label="item.label"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>
      </el-card>
      <el-card>
        <div slot="header">
          <h2>随访记录</h2>
        </div>
        <el-row :gutter="0">
          <el-col :span="8">
            <el-form-item label="已提醒复诊">
              <el-select v-model="form.reminderRevisit">
                <el-option
                  v-for="item in sfreminderRevisit"
                  :key="item.value"
                  :value="item.value"
                  :label="item.label"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="已预约复诊">
              <el-select v-model="form.appointmentRevisit" @change="appointmentRevisitChange">
                <el-option
                  v-for="item in sfappointmentRevisit"
                  :key="item.value"
                  :value="item.value"
                  :label="item.label"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>

        <el-row :gutter="0" v-show="isAppointmentRevisit">
          <el-col :span="8">
            <el-form-item label="预约科室">
              <el-select v-model="form.department">
                <el-option
                  v-for="item in sfdepartment"
                  :key="item.value"
                  :value="item.value"
                  :label="item.label"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="复诊时间">
              <el-date-picker
                v-model="form.revisitTime"
                type="date"
                placeholder="选择日期"
                format="yyyy-MM-dd"
                value-format="yyyy-MM-dd"
              ></el-date-picker>
            </el-form-item>
          </el-col>
        </el-row>

        <el-row :gutter="0">
          <el-col :span="8">
            <el-form-item label="健康知晓度">
              <el-select v-model="form.healthAwareness">
                <el-option
                  v-for="item in sfhealthAwareness"
                  :key="item.value"
                  :value="item.value"
                  :label="item.label"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="满意度调研">
              <el-select v-model="form.satisfactionSurvey">
                <el-option
                  v-for="item in sfsatisfactionSurvey"
                  :key="item.value"
                  :value="item.value"
                  :label="item.label"
                ></el-option>
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>
        <el-form-item label="随访备注">
          <el-input type="textarea" v-model="form.remark"></el-input>
        </el-form-item>
      </el-card>
      <el-form-item style="text-align: center">
        <el-button type="success" @click="onSubmit(4)">完成随访</el-button>
        <el-button @click="cancelBtn">取消</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>
<script>
import template from "./index";
export default {
  components: template.components,
  data: template.data,
  created: template.created,
  methods: template.methods
};
</script>