<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-menu-button slot="start" />
        <ion-title>{{ $t("Orders") }}</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content>
      <main>
        <section>
          <ion-card>
            <ion-card-header>
              <ion-card-title>{{ $t("Import") }}</ion-card-title>
            </ion-card-header>
            <ion-item @click="viewJobConfiguration('IMP_NEW_ORDERS', 'New orders', getJobStatus(this.jobEnums['IMP_NEW_ORDERS']))" detail button>
              <ion-label class="ion-text-wrap">{{ $t("New orders") }}</ion-label>
              <ion-label slot="end">{{ getTemporalExpression('IMP_NEW_ORDERS') }}</ion-label>
            </ion-item>
            <ion-item @click="viewJobConfiguration('IMP_CANCELLED_ORDERS', 'Cancelled orders', getJobStatus(this.jobEnums['IMP_CANCELLED_ORDERS']))" detail button>
              <ion-label class="ion-text-wrap">{{ $t("Cancelled orders") }}</ion-label>
              <ion-label slot="end">{{ getTemporalExpression('IMP_CANCELLED_ORDERS') }}</ion-label>
            </ion-item>
            <ion-item @click="viewJobConfiguration('IMP_CANCELLED_ITEMS', 'Cancelled items', getJobStatus(this.jobEnums['IMP_CANCELLED_ITEMS']))" detail button>
              <ion-label class="ion-text-wrap">{{ $t("Cancelled items") }}</ion-label>
              <ion-label slot="end">{{ getTemporalExpression('IMP_CANCELLED_ITEMS') }}</ion-label>
            </ion-item>
            <ion-item @click="viewJobConfiguration('IMP_PAYMENT_STATUS', 'Payment status', getJobStatus(this.jobEnums['IMP_PAYMENT_STATUS']))" detail button>
              <ion-label class="ion-text-wrap">{{ $t("Payment status") }}</ion-label>
              <ion-label slot="end">{{ getTemporalExpression('IMP_PAYMENT_STATUS') }}</ion-label>
            </ion-item>
            <ion-item @click="viewJobConfiguration('IMP_RETURNS', 'Returns', getJobStatus(this.jobEnums['IMP_RETURNS']))" detail button>
              <ion-label class="ion-text-wrap">{{ $t("Returns") }}</ion-label>
              <ion-label slot="end">{{ getTemporalExpression('IMP_RETURNS') }}</ion-label>
            </ion-item>
          </ion-card>

          <ion-card>
            <ion-card-header>
              <ion-card-title>{{ $t("Upload") }}</ion-card-title>
            </ion-card-header>
            <ion-item @click="viewJobConfiguration('UPLD_CMPLT_ORDRS', 'Completed orders', getJobStatus(this.jobEnums['UPLD_CMPLT_ORDRS']))" detail button>
              <ion-label class="ion-text-wrap">{{ $t("Completed orders") }}</ion-label>
              <ion-label slot="end">{{ getTemporalExpression('UPLD_CMPLT_ORDRS') }}</ion-label>
            </ion-item>
            <ion-item @click="viewJobConfiguration('UPLD_CNCLD_ORDRS', 'Cancelled orders', getJobStatus(this.jobEnums['UPLD_CNCLD_ORDRS']))" detail button>
              <ion-label class="ion-text-wrap">{{ $t("Cancelled orders") }}</ion-label>
              <ion-label slot="end">{{ getTemporalExpression('UPLD_CNCLD_ORDRS') }}</ion-label>
            </ion-item>
            <ion-item @click="viewJobConfiguration('UPLD_REFUNDS', 'Refunds', getJobStatus(this.jobEnums['UPLD_REFUNDS']))" detail button>
              <ion-label class="ion-text-wrap">{{ $t("Refunds") }}</ion-label>
              <ion-label slot="end">{{ getTemporalExpression('UPLD_REFUNDS') }}</ion-label>
            </ion-item>
          </ion-card>

          <ion-card>
            <ion-card-header>
              <ion-card-title>{{ $t("Auto cancelations") }}</ion-card-title>
            </ion-card-header>
            <ion-item>
              <ion-label class="ion-text-wrap">{{ $t("Days") }}</ion-label>
              <ion-input :placeholder="$t('before auto cancelation')" />
            </ion-item>
            <ion-item>
              <ion-label class="ion-text-wrap">{{ $t("Check daily") }}</ion-label>
              <ion-toggle :checked="autoCancelCheckDaily" color="secondary" slot="end" @ionChange="updateJob($event['detail'].checked, jobEnums['AUTO_CNCL_DAL'], 'EVERYDAY')" />
            </ion-item>
            <ion-item lines="none">
              <ion-label class="ion-text-wrap"><p>{{ $t("Unfulfilled orders that pass their auto cancelation date will be canceled automatically in HotWax Commerce. They will also be canceled in Shopify if upload for canceled orders is enabled.") }}</p></ion-label>
            </ion-item>
          </ion-card>

          <ion-card>
            <ion-card-header>
              <ion-card-title>{{ $t("Notes") }}</ion-card-title>
            </ion-card-header>
            <ion-item>
              <ion-label class="ion-text-wrap">{{ $t("Promise date changes") }}</ion-label>
              <ion-toggle :checked="promiseDateChanges" color="secondary" slot="end" @ionChange="updateJob($event['detail'].checked, jobEnums['NTS_PRMS_DT_CHNG'])"/>
            </ion-item>
          </ion-card>

          <ion-card>
            <ion-card-header>
              <ion-card-title>{{ $t("Routing") }}</ion-card-title>
            </ion-card-header>
            <ion-item @click="viewJobConfiguration('REJ_ORDR', 'Rejected orders', getJobStatus(this.jobEnums['REJ_ORDR']))" detail button>
              <ion-label class="ion-text-wrap">{{ $t("Rejected orders") }}</ion-label>
              <ion-label slot="end">{{ getTemporalExpression('REJ_ORDR') }}</ion-label>
            </ion-item>
            <ion-item @click="viewJobConfiguration('UNFIL_ORDERS', 'Unfillable orders', getJobStatus(this.jobEnums['UNFIL_ORDERS']))" detail button>
              <ion-label class="ion-text-wrap">{{ $t("Unfillable orders") }}</ion-label>
              <ion-label slot="end">{{ getTemporalExpression('UNFIL_ORDERS') }} </ion-label>
            </ion-item>
            <ion-item-divider>
              <ion-label class="ion-text-wrap">{{ $t("Batches") }}</ion-label>
              <ion-button fill="clear" @click="addBatch()" slot="end">
                {{ $t("Add") }}
                <ion-icon :icon="addCircleOutline" slot="end" />
              </ion-button>
            </ion-item-divider>
            <ion-item v-for="batch in getJob(jobEnums['BTCH_BRKR_ORD'])" :key="batch.id" button detail @click="editBatch(batch.id)" v-show="batch.status === 'SERVICE_PENDING'">
              <ion-label class="ion-text-wrap">{{ batch.jobName }}</ion-label>
              <ion-note slot="end">{{ batch?.runTime ? getTime(batch.runTime) : '' }}</ion-note>
            </ion-item>
          </ion-card>
        </section>

        <aside class="desktop-only" v-show="currentJob">
          <JobConfiguration :title="title" :job="currentJob" :status="currentJobStatus" :type="freqType" :key="currentJob"/>
        </aside>
      </main>
    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import {
  alertController,
  IonButton,
  IonCard,
  IonCardHeader,
  IonCardTitle,
  IonContent,
  IonHeader,
  IonIcon,
  IonInput,
  IonItem,
  IonItemDivider,
  IonLabel,
  IonMenuButton,
  IonNote,
  IonPage,
  IonTitle,
  IonToggle,
  IonToolbar,
  modalController
} from '@ionic/vue';
import { defineComponent } from 'vue';
import { addCircleOutline } from 'ionicons/icons';
import BatchModal from '@/components/BatchModal.vue';
import { useStore } from "@/store";
import { mapGetters } from "vuex";
import JobConfiguration from '@/components/JobConfiguration.vue';
import { DateTime } from 'luxon';
import { isFutureDate } from '@/utils';
import emitter from '@/event-bus';

export default defineComponent({
  name: 'Orders',
  components: {
    IonButton,
    IonCard,
    IonCardHeader,
    IonCardTitle,
    IonContent,
    IonHeader,
    IonIcon,
    IonInput,
    IonItem,
    IonItemDivider,
    IonLabel,
    IonMenuButton,
    IonNote,
    IonPage,
    IonTitle,
    IonToggle,
    IonToolbar,
    JobConfiguration
  },
  data() {
    return {
      jobEnums: JSON.parse(process.env?.VUE_APP_ODR_JOB_ENUMS as string) as any,
      jobFrequencyType: JSON.parse(process.env?.VUE_APP_JOB_FREQUENCY_TYPE as string) as any,
      currentJob: '' as any,
      title: 'New orders',
      currentJobStatus: '',
      freqType: '',
      isJobDetailAnimationCompleted: false
    }
  },
  computed: {
    ...mapGetters({
      getJobStatus: 'job/getJobStatus',
      getJob: 'job/getJob',
      shopifyConfigId: 'user/getShopifyConfigId',
      currentEComStore: 'user/getCurrentEComStore',
      getTemporalExpr: 'job/getTemporalExpr'
    }),
    promiseDateChanges(): boolean {
      const status = this.getJobStatus(this.jobEnums['NTS_PRMS_DT_CHNG']);
      return status && status !== "SERVICE_DRAFT";
    },
    autoCancelCheckDaily(): boolean {
      const status = this.getJobStatus(this.jobEnums["REAL_WBHKS"]);
      return status && status !== "SERVICE_DRAFT";
    },
  },
  methods: {  
    async addBatch() {
      const batchmodal = await modalController.create({
        component: BatchModal,
        componentProps: { enum: this.jobEnums['BTCH_BRKR_ORD'] }
      });
      return batchmodal.present();
    },
    async editBatch(id: string) {
      const batchmodal = await modalController.create({
        component: BatchModal,
        componentProps: { id, enum: this.jobEnums['BTCH_BRKR_ORD'] }
      });
      return batchmodal.present();
    },
    getTime (time: any) {
      return DateTime.fromMillis(time).toLocaleString(DateTime.DATETIME_MED);
    },
    async updateJob(checked: boolean, id: string, status = 'EVERY_15_MIN') {
      const job = this.getJob(id);

      // TODO: added this condition to not call the api when the value of the select automatically changes
      // need to handle this properly
      if (!job || (checked && job?.status === 'SERVICE_PENDING') || (!checked && job?.status === 'SERVICE_DRAFT')) {
        return;
      }

      job['jobStatus'] = status;

      // if job runTime is not a valid date then making runTime as empty
      if (job?.runTime && !isFutureDate(job?.runTime)) {
        job.runTime = ''
      }

      if (!checked) {
        this.store.dispatch('job/cancelJob', job)
      } else if (job?.status === 'SERVICE_DRAFT') {
        this.store.dispatch('job/scheduleService', job)
      } else if (job?.status === 'SERVICE_PENDING') {
        this.store.dispatch('job/updateJob', job)
      }
    },
    viewJobConfiguration(id: string, title: string, status: string) {
      this.currentJob = this.getJob(this.jobEnums[id])
      this.title = title
      this.currentJobStatus = status
      this.freqType = id && this.jobFrequencyType[id]

      // if job runTime is not a valid date then making runTime as empty
      if (this.currentJob?.runTime && !isFutureDate(this.currentJob?.runTime)) {
        this.currentJob.runTime = ''
      }
      if (this.currentJob && !this.isJobDetailAnimationCompleted) {
        emitter.emit('playAnimation');
        this.isJobDetailAnimationCompleted = true;
      }
    },
    getTemporalExpression(enumId: string) {
      return this.getTemporalExpr(this.getJobStatus(this.jobEnums[enumId]))?.description ?
        this.getTemporalExpr(this.getJobStatus(this.jobEnums[enumId]))?.description :
        this.$t('Disabled')
    },
    async runJob(header: string, id: string) {
      const job = this.getJob(id)
      const jobAlert = await alertController
        .create({
          header,
          message: this.$t('This job will be scheduled to run as soon as possible. There may not be enough time to revert this action.', {space: '<br/><br/>'}),
          buttons: [
            {
              text: this.$t("Cancel"),
              role: 'cancel',
            },
            {
              text: this.$t('Run now'),
              handler: () => {
                if (job) {
                  this.store.dispatch('job/runServiceNow', job)
                }
              }
            }
          ]
        });

      return jobAlert.present();
    }
  },
  mounted () {
    this.store.dispatch("job/fetchJobs", {
      "inputFields":{
        "systemJobEnumId": Object.values(this.jobEnums),
        "systemJobEnumId_op": "in"
      }
    });
  },
  setup() {
    const store = useStore();

    return {
      addCircleOutline,
      store
    };
  },
});
</script>
