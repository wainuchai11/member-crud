<template>
  <q-page padding>
    <div class="text-subtitle1 text-bold">
      Member Information
    </div>

    <q-table
      :data="data"
      :columns="columns"
      :loading="loading"
      separator="cell"
    >
      <template v-slot:top-right>
        <div class='q-gutter-sm flex'>
          <q-input
            autofocus
            borderless
            outlined
            v-model="name"
            dense
            :debounce='1000'
            @input='getMemberInfo'
            placeholder="Search Name"
            :loading="loading"
            :disable="loading"
          >
            <template v-slot:prepend>
              <q-icon name="search" />
            </template>
          </q-input>
          <q-btn label='Add member' icon='add' color='primary' text-color='white' @click="addDialog = true">

          </q-btn>
        </div>
      </template>
      <template v-slot:loading>
        <q-inner-loading showing color="primary" />
      </template>
      <template v-slot:body-cell-no='props'>
        <q-td :props='props'>
          {{props.rowIndex + 1}}
        </q-td>
      </template>
      <template v-slot:body-cell-action='props'>
        <q-td :props='props'>
          <div class='q-gutter-sm'>
            <q-btn outline text-color='blue-6' label='Edit' icon='edit' class='text-capitalize' @click='onEditBtn($event,props.row)'></q-btn>
            <q-btn icon='delete' color='red-6' flat dense @click='onDeleteBtn($event,props.row)'></q-btn>
          </div>
        </q-td>
      </template>
    </q-table>
    <q-dialog v-model="editDialog">
      <q-card bordered style="width: 80vw; max-width: 650px; height: auto;">
        <q-card-section class="row items-center q-pa-sm">
          <div class="flex justify-center text-center" style="margin: 0 auto;">
            <div class="text-h6 text-capitalize">Edit Member</div>
          </div>

          <q-btn icon="close" flat round dense v-close-popup />
        </q-card-section>
        <q-separator />
        <div class="flex full-width justify-around q-pa-md q-gutter-sm">
          <div class="full-width flex q-gutter-sm q-pa-sm items-center">
            <span
              style="width: 20%; max-width: 350px;"
              class="text-bold text-body1"
            >
              First Name
            </span>
            <q-input
              style="width: 50%;"
              outlined
              v-model="details.firstName"
              dense
              borderless
            >
            </q-input>
          </div>
          <div class="full-width flex q-gutter-sm q-pa-sm items-center">
            <span
              style="width: 20%; max-width: 350px;"
              class="text-bold text-body1"
            >
              Last Name
            </span>
            <q-input
              style="width: 50%;"
              outlined
              v-model="details.lastName"
              dense
              borderless
            >
            </q-input>
          </div>
        </div>
        <q-separator />
        <div class="full-width flex justify-center q-pa-md q-gutter-sm">
          <q-btn
            label="Confirm"
            text-color="white"
            color="positive"
            class="text-capitalize"
            @click='onEditSubmit'
            type="submit"
          ></q-btn>
          <q-btn
            v-close-popup
            label="Cancel"
            text-color="negative"
            outline
            class="text-capitalize"
          ></q-btn>
        </div>
      </q-card>
    </q-dialog>
    <q-dialog v-model="addDialog">
      <q-card bordered style="width: 80vw; max-width: 650px; height: auto;">
        <q-card-section class="row items-center q-pa-sm">
          <div class="flex justify-center text-center" style="margin: 0 auto;">
            <div class="text-h6 text-capitalize">Add Member</div>
          </div>

          <q-btn icon="close" flat round dense v-close-popup />
        </q-card-section>
        <q-separator />
        <div class="flex full-width justify-around q-pa-md q-gutter-sm">
          <div class="full-width flex q-gutter-sm q-pa-sm items-center">
            <span
              style="width: 20%; max-width: 350px;"
              class="text-bold text-body1"
            >
              First Name
            </span>
            <q-input
              style="width: 50%;"
              outlined
              v-model="createDetails.firstName"
              dense
              borderless
            >
            </q-input>
          </div>
          <div class="full-width flex q-gutter-sm q-pa-sm items-center">
            <span
              style="width: 20%; max-width: 350px;"
              class="text-bold text-body1"
            >
              Last Name
            </span>
            <q-input
              style="width: 50%;"
              outlined
              v-model="createDetails.lastName"
              dense
              borderless
            >
            </q-input>
          </div>
        </div>
        <q-separator />
        <div class="full-width flex justify-center q-pa-md q-gutter-sm">
          <q-btn
            label="Confirm"
            text-color="white"
            color="positive"
            class="text-capitalize"
            :loading='loading'
            @click='addMember'
          ></q-btn>
          <q-btn
            v-close-popup
            label="Cancel"
            text-color="negative"
            outline
            class="text-capitalize"
          ></q-btn>
        </div>
      </q-card>
    </q-dialog>

    <q-dialog v-model="deleteDialog">
      <q-card bordered style="width: 80vw; max-width: 500px; height: auto;">
        <q-card-section class="row items-center q-pa-none">
          <div class="text-h6"></div>
          <q-space />
          <q-btn icon="close" flat round dense v-close-popup />
        </q-card-section>
        <div class="full-width items-center flex justify-center q-gutter-sm">
          <q-icon size="100px" class="full-width">
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100.02">
              <path
                fill="#d20000"
                class="cls-1"
                d="M77.7,8.37A49.77,49.77,0,0,0,42.12.64a3.57,3.57,0,0,0-.65.06A50,50,0,0,0,.75,58.51,50.52,50.52,0,0,0,2.32,65,37.35,37.35,0,0,1,.41,92a4.17,4.17,0,0,0,4.87,5.79L24,92.68A50,50,0,0,0,77.7,8.37Zm7,64.71A41.66,41.66,0,0,1,27,84.7,4.25,4.25,0,0,0,24.64,84a4.16,4.16,0,0,0-1.1.15L10.83,87.63a45.92,45.92,0,0,0-.55-25.11A42.62,42.62,0,0,1,9,57.08,41.67,41.67,0,0,1,42.34,9a3.05,3.05,0,0,0,.6-.06A41.67,41.67,0,0,1,84.72,73.08Z"
              />
              <path
                fill="#d20000"
                class="cls-1"
                d="M50,24.6a4.16,4.16,0,0,0-4.17,4.16v25a4.17,4.17,0,1,0,8.33,0v-25A4.16,4.16,0,0,0,50,24.6Z"
              />
              <path
                fill="#d20000"
                class="cls-1"
                d="M53.85,69.26A3.93,3.93,0,0,0,53,67.88a4.87,4.87,0,0,0-.67-.5,2.47,2.47,0,0,0-.7-.37,2.34,2.34,0,0,0-.8-.25,4.16,4.16,0,0,0-3.74,1.12,3.93,3.93,0,0,0-.88,1.38,4.3,4.3,0,0,0-.33,1.58,4.35,4.35,0,0,0,.33,1.59,4.78,4.78,0,0,0,.88,1.37,4.65,4.65,0,0,0,1.37.88,4,4,0,0,0,3.17,0,3.83,3.83,0,0,0,2.25-2.25,4.35,4.35,0,0,0,.33-1.59A4.3,4.3,0,0,0,53.85,69.26Z"
              />
            </svg>
          </q-icon>
          <div
            class="text-bold text-h6 text-capitalize full-width items-center flex justify-center text-center"
          >
            delete Member
          </div>
          <div
            class="text-bold text-subtitle1 text-capitalize full-width items-center flex justify-center text-center"
          >
            Are you sure you want to delete this member
          </div>
          <div
            class="text-bold text-subtitle1 text-capitalize full-width items-center flex justify-center q-gutter-md q-pa-md"
          >
            <q-btn
              class="text-capitalize text-subtitle1"
              color="positive"
              text-color="white"
              @click="onDeleteMember"
            >confirm</q-btn
            >
            <q-btn
              class="text-capitalize text-subtitle1"
              color="negative"
              outline
              v-close-popup
            >cancel</q-btn
            >
          </div>
        </div>
      </q-card>
    </q-dialog>
  </q-page>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';

interface GetMemberInfo {
  error: boolean,
  data: [
    {
      id: number,
      firstName: string,
      lastName: string,
      createdTime: string,
      updatedTime: string
    }
  ],
  message: string
}

interface MemberDetails {
  id: number,
  firstName: string,
  lastName: string,
  createdTime: string,
  updatedTime: string
}
@Component
export default class Member extends Vue {
  private loading = false;
  private data = [];
  private columns = [
    {
      name: 'no',
      field: 'no',
      label: '#',
      align: 'center',
    },
    {
      name: 'firstName',
      field: 'firstName',
      label: 'First Name',
      align: 'left',
    },
    {
      name: 'lastName',
      field: 'lastName',
      label: 'Last Name',
      align: 'left',
    },
    {
      name: 'createdTime',
      field: 'createdTime',
      label: 'Created Time',
      align: 'left',
    },
    {
      name: 'action',
      field: 'action',
      label: 'Action',
      align: 'center',
    },
  ];
  private name = ''
  private details= {} as MemberDetails
  private editDialog = false
  private deleteDialog = false
  private addDialog = false
  private createDetails ={
    firstName : '',
    lastName : ''
  }


  async created() {
    await this.getMemberInfo()
  }
  async getMemberInfo(){
    this.loading = true
    let url ='http://localhost:3000/Api/member/memberInfo';
    if(this.name){
      url += `/${this.name}`
    }
    try {
      const response = await this.$axios.get<GetMemberInfo>(url)
      console.log(response)
      this.$set(this,'data',response.data.data)
    }catch (e) {
      console.error(e)
    }finally {
      this.loading  = false
    }
  }

  onEditBtn($event:never,row:never){
    this.$set(this,'details',row)
    this.editDialog = true
  }
  onDeleteBtn($event:never,row:never){
    this.$set(this,'details',row)
    this.deleteDialog = true
  }
 async addMember() {
    try {
      await this.$axios.post('http://localhost:3000/Api/member/addMember',{
        firstName : this.createDetails.firstName,
        lastName : this.createDetails.lastName
      })
      // console.log('>>>>>>>>>>>>>>>>')
    }catch (e) {
      console.error(e)
    }finally {
      this.addDialog = false
      this.createDetails.firstName = ''
      this.createDetails.lastName = ''
      await this.getMemberInfo()
    }
}

async onEditSubmit(){
    try {
      await this.$axios.put('http://localhost:3000/Api/member/updateMember',{
        id : this.details.id,
        firstName : this.details.firstName,
        lastName : this.details.lastName
      })
    }catch (e) {
      console.error(e)
    }finally {
      this.loading = false
      this.editDialog = false
      await this.getMemberInfo()
    }
}

async onDeleteMember(){
    try {
      await  this.$axios.delete('http://localhost:3000/Api/member/deleteMember',{
      data : {
        id :this.details.id

      }
      })
    }catch (e) {
      console.error(e)
    }
    finally {
      this.deleteDialog = false
      await this.getMemberInfo()
    }
}
}
</script>

<style scoped></style>
