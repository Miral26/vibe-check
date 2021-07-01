<template>
  <div class="main-content" v-if="!loading">
    <b-row>
      <b-col lg="12" xl="12" md="12">
        <div class="d-flex justify-content-between">
          <div class="d-flex align-items-center mb-4">
            <div class="mr-3 page-title">
              <h3 class="font-weight-bold m-0">Patients</h3>
            </div>
          </div>
        </div>

        <Table
          :patients="patients"
          :onPatientEdit="onPatientEdit"
          :onPatientDelete="onPatientDelete"
        />

        <b-modal
          id="add-patient"
          size="lg"
          hide-header
          hide-footer
          no-close-on-backdrop
        >
          <div>
            <b-col>
              <b-form @submit.prevent="submit">
                <div class="row">
                  <div class="col-md-4">
                    <b-form-group class="mb-3" label="First Name">
                      <b-form-input
                        type="text"
                        v-model.trim="$v.patientForm.first_name.$model"
                      ></b-form-input>
                    </b-form-group>
                  </div>
                  <div class="col-md-4">
                    <b-form-group class="mb-3" label="Middle Name">
                      <b-form-input
                        type="text"
                        v-model.trim="$v.patientForm.middle_name.$model"
                      ></b-form-input>
                    </b-form-group>
                  </div>
                  <div class="col-md-4">
                    <b-form-group class="mb-3" label="Last Name">
                      <b-form-input
                        type="text"
                        v-model.trim="$v.patientForm.last_name.$model"
                      ></b-form-input>
                    </b-form-group>
                  </div>
                </div>

                <div class="row">
                  <div class="col-md-4">
                    <b-form-group class="mb-3" label="Email Address">
                      <b-form-input
                        type="text"
                        v-model.trim="$v.patientForm.email_address.$model"
                      ></b-form-input>
                    </b-form-group>
                  </div>
                  <div class="col-md-4">
                    <b-form-group class="mb-3" label="Date of Birth">
                      <b-form-datepicker
                        :date-format-options="{
                          year: 'numeric',
                          month: 'numeric',
                          day: 'numeric',
                        }"
                        :max="new Date()"
                        v-model="$v.patientForm.date_of_birth.$model"
                      ></b-form-datepicker>
                    </b-form-group>
                  </div>
                  <div class="col-md-4">
                    <b-form-group class="mb-3" label="Gender">
                      <b-dropdown
                        class="default-dropdown"
                        :text="patientForm.gender || 'Select a gender'"
                      >
                        <b-dropdown-item @click="patientForm.gender = ''"
                          >Select a gender</b-dropdown-item
                        >
                        <b-dropdown-item @click="patientForm.gender = 'MALE'"
                          >Male</b-dropdown-item
                        >
                        <b-dropdown-item @click="patientForm.gender = 'FEMALE'"
                          >Female</b-dropdown-item
                        >
                      </b-dropdown>
                    </b-form-group>
                  </div>
                </div>

                <div class="row">
                  <div class="col-md-4">
                    <b-form-group class="mb-3" label="Nick Name">
                      <b-form-input
                        type="text"
                        v-model.trim="$v.patientForm.nick_name.$model"
                      ></b-form-input>
                    </b-form-group>
                  </div>
                  <div class="col-md-4">
                    <b-form-group class="mb-3" label="Phone">
                      <b-form-input
                        type="text"
                        v-model.trim="$v.patientForm.phone.$model"
                      ></b-form-input>
                    </b-form-group>
                  </div>
                  <div class="col-md-4">
                    <b-form-group class="mb-3" label="Cell">
                      <b-form-input
                        type="text"
                        v-model.trim="$v.patientForm.cell.$model"
                      ></b-form-input>
                    </b-form-group>
                  </div>
                </div>

                <div class="row">
                  <div class="col-md-4">
                    <b-form-group class="mb-3" label="Foreign Id">
                      <b-form-input
                        type="text"
                        v-model.trim="$v.patientForm.foreign_id.$model"
                      ></b-form-input>
                    </b-form-group>
                  </div>
                  <div class="col-md-4">
                    <b-form-group class="mb-3" label="Guardian">
                      <b-form-input
                        type="text"
                        v-model.trim="$v.patientForm.guardian.$model"
                      ></b-form-input>
                    </b-form-group>
                  </div>
                  <div class="col-md-4">
                    <b-form-group class="mb-3" label="SSN">
                      <b-form-input
                        type="text"
                        v-model.trim="$v.patientForm.ssn.$model"
                      ></b-form-input>
                    </b-form-group>
                  </div>
                </div>

                <div class="row">
                  <div class="col-md-4">
                    <b-form-group class="mb-3" label="Home Office">
                      <b-form-input
                        type="text"
                        v-model.trim="$v.patientForm.home_office.$model"
                      ></b-form-input>
                    </b-form-group>
                  </div>
                  <div class="col-md-4">
                    <b-form-group class="mb-3" label="Other Notes">
                      <b-form-input
                        type="text"
                        v-model.trim="$v.patientForm.other_notes.$model"
                      ></b-form-input>
                    </b-form-group>
                  </div>
                  <div class="col-md-4">
                    <b-form-group class="mb-3" label="Insurance Policy#">
                      <b-form-input
                        type="text"
                        v-model.trim="$v.patientForm.insurance_policy.$model"
                      ></b-form-input>
                    </b-form-group>
                  </div>
                </div>

                <div class="row">
                  <div class="col-md-12">
                    <b-button
                      type="submit"
                      size="sm"
                      class="btn-radius"
                      variant="primary"
                      :disabled="$v.$invalid || actionLoading"
                    >
                      <div class="d-flex">
                        <span :class="actionLoading ? 'mr-3' : ''">{{
                          actionLoading
                            ? "Saving..."
                            : patientForm.id
                            ? "Update"
                            : "Save"
                        }}</span>
                        <span class="spinner" v-if="actionLoading"></span>
                      </div>
                    </b-button>
                    <b-button
                      size="sm"
                      class="btn-radius ml-2"
                      variant="outline-primary"
                      @click="
                        patientForm = {};
                        $bvModal.hide('add-patient');
                      "
                    >
                      Cancel
                    </b-button>
                  </div>
                </div>
              </b-form>
            </b-col>
          </div>
        </b-modal>

        <b-modal id="patient-card-add" size="md" hide-header hide-footer>
          <div>
            <b-row>
              <b-col>
                <div class="row">
                  <div class="col-md-12">
                    <b-form-group class="mb-3">
                      <b-form-input
                        type="text"
                        v-model="customerCardForm.card_number"
                        required
                        placeholder="Card Number"
                      ></b-form-input>
                    </b-form-group>
                  </div>
                </div>
                <div class="row">
                  <div class="col-md-6 col-lg-6">
                    <b-form-group class="mb-3">
                      <b-form-input
                        type="text"
                        v-model="customerCardForm.expiry_date"
                        required
                        placeholder="MM/YY"
                      ></b-form-input>
                    </b-form-group>
                  </div>
                  <div class="col-md-6 col-lg-6">
                    <b-form-group class="mb-3">
                      <b-form-input
                        type="password"
                        v-model="customerCardForm.cvv"
                        required
                        placeholder="CVV"
                      ></b-form-input>
                    </b-form-group>
                  </div>
                </div>
              </b-col>
            </b-row>
            <b-row class="mt-3">
              <b-col>
                <b-button
                  size="sm"
                  class="btn-radius"
                  variant="primary"
                  @click="
                    () => {
                      savePatientCard(customerCardForm);
                      $bvModal.hide('patient-card-add');
                    }
                  "
                >
                  Submit
                </b-button>
                <b-button
                  size="sm"
                  class="btn-radius ml-2"
                  variant="primary"
                  @click="$bvModal.hide('patient-card-add')"
                >
                  Cancel
                </b-button>
              </b-col>
            </b-row>
          </div>
        </b-modal>

        <b-modal id="patient-card-view" size="lg" hide-header hide-footer>
          <div class="wrapper">
            <vue-good-table
              :columns="card_list_table_columns"
              :line-numbers="true"
              :search-options="{
                enabled: true,
                placeholder: 'Search Card',
                selectionInfoClass: ' flex-column flex-sm-row',
              }"
              :pagination-options="{
                enabled: true,
                mode: 'records',
              }"
              styleClass="tableOne vgt-table"
              :selectOptions="{
                enabled: false,
                selectionInfoClass: 'table-alert__box',
              }"
              :rows="[]"
            >
              <template slot="table-row" slot-scope="props">
                <span v-if="props.column.field == 'action'">
                  <a
                    v-b-tooltip.hover
                    class="o-hidden d-inline-block c-pointer"
                    title="Delete"
                  >
                    <i
                      class="i-Close-Window text-25 text-danger"
                      @click="
                        confirmationPopup().then((result) => {
                          if (result.value) removePatientCard(props.row);
                        })
                      "
                    ></i>
                  </a>
                </span>
              </template>
            </vue-good-table>
          </div>
        </b-modal>
      </b-col>
    </b-row>
  </div>
  <Loader v-else />
</template>
<script>
import Table from "./table";
import { required, minLength, email } from "vuelidate/lib/validators";
import { mapActions, mapGetters } from "vuex";
import Loader from "../../../components/loader/loader";
import {
  getPatients,
  createPatient,
  updatePatient,
  deletePatient,
} from "./APICalls";

export default {
  components: {
    Table,
    Loader,
  },
  data() {
    return {
      loading: false,
      actionLoading: false,
      patientForm: {
        first_name: "",
        middle_name: "",
        last_name: "",
        email_address: "",
        date_of_birth: null,
        gender: "",
        nick_name: "",
        phone: "",
        cell: "",
        foreign_id: "",
        guardian: "",
        ssn: "",
        home_office: "",
        other_notes: "",
        insurance_policy: "",
      },
      patients: [],
      customerCardForm: {
        card_number: "",
        expiry_date: "",
        cvv: "",
      },
      card_list_table_columns: [
        {
          label: "Card Brand",
          field: "card_brand",
        },
        {
          label: "Card Last 4 Digits",
          field: "card_number",
        },
        {
          label: "Exp Month",
          field: "expiry_date",
        },
        {
          label: "Exp Year",
          field: "expiry_date",
        },
        {
          label: "Action",
          field: "action",
        },
      ],
    };
  },
  validations: {
    patientForm: {
      first_name: { required, minLength: minLength(4) },
      last_name: { required, minLength: minLength(4) },
      middle_name: { required, minLength: minLength(4) },
      email_address: { required, email: email() },
      date_of_birth: { required },
      gender: { required },
      nick_name: { required },
      phone: { required, minLength: minLength(10) },
      home_office: { required, minLength: minLength(5) },
      cell: { required },
      foreign_id: { required },
      guardian: { required },
      ssn: { required },
      other_notes: { required },
      insurance_policy: { required },
    },
  },
  computed: {
    ...mapGetters([]),
  },
  created: function () {
    // this.items = this.getItems;
  },
  methods: {
    ...mapActions([]),
    submit() {
      if (this.patientForm) {
        this.actionLoading = true;
        if (this.patientForm.id) {
          const updatedObj = { ...this.patientForm };
          const patientId = updatedObj.id;
          updatedObj.id;
          updatePatient(updatedObj, patientId).then((result) => {
            const patientsData = this.patients.slice(0);
            const patientIndex = patientsData.findIndex(
              (p) => p.id === result.id
            );
            if (patientIndex > -1) {
              patientsData[patientIndex] = { ...result, id: patientId };
              this.patients = patientsData;
            }
            this.actionLoading = false;
            this.$bvModal.hide("add-patient");
            this.makeToast("success", "Patient updated successfully!");
          });
        } else {
          createPatient(this.patientForm).then((result) => {
            this.patients.push(result);
            this.makeToast("success", "Patient added successfully!");
            this.$bvModal.hide("add-patient");
            this.actionLoading = false;
          });
        }
      }
    },
    onPatientEdit(data) {
      console.log(`data`, data);
      this.patientForm = data;
      this.$bvModal.show("add-patient");
    },
    onPatientDelete(data) {
      this.actionLoading = true;
      if (data && data.id) {
        deletePatient(data.id).then((result) => {
          if (result) {
            const patientIndex = this.patients.findIndex(
              (p) => p.id === data.id
            );
            if (patientIndex > -1) {
              this.patients.splice(patientIndex, 1);
            }
          }
          this.actionLoading = false;
        });
      }
    },
    makeToast(variant = null, msg) {
      this.$bvToast.toast(msg, {
        title: ` ${variant || "default"}`,
        variant: variant,
        solid: true,
      });
    },
    confirmationPopup() {
      return this.$swal({
        title: "Are you sure?",
        text: "You won't be able to revert this!",
        type: "warning",
        showCancelButton: true,
        confirmButtonColor: "#3085d6",
        cancelButtonColor: "#d33",
        confirmButtonText: "Yes, delete it!",
      });
    },
  },
  async mounted() {
    this.loading = true;
    await getPatients().then((result) => {
      this.patients = (result && result.results) || [];
      this.loading = false;
    });
  },
};
</script>
<style>
.delta-dental-tab span {
  color: #9badbf;
  font-weight: normal;
}
.delta-dental-tab span.active-tab {
  color: #355677;
}
.huddle-tab {
  background-color: #eaf4fb;
  padding: 20px;
  border-radius: 10px;
}
.tabs .nav-tabs {
  border: 0;
}
.tabs .nav-tabs .nav-item .nav-link {
  border: 0;
  background-color: transparent;
  position: relative;
  font-weight: bold;
  color: #05070b;
  padding: 10px 5px 5px;
}
.tabs .nav-tabs .nav-item .nav-link.active:before {
  content: "";
  position: absolute;
  right: 0;
  bottom: 0;
  left: 0;
  width: 20px;
  height: 2px;
  background-color: #6cdcd4;
  margin: 0 auto;
}
.tabs .nav-tabs .nav-item .nav-link.active {
  color: #6cdcd4;
  border: 0;
  background-color: transparent;
}

#notes-view .form-control-plaintext,
#appointment-location-view .form-control-plaintext {
  border: 2px solid #e5f4f8;
  border-radius: 5px;
  border-width: 1px;
}
#add-delta-dental .b-form-datepicker.focus {
  box-shadow: none;
}
#add-delta-dental .b-form-datepicker > .btn {
  padding: 7px 10px;
}
</style>
