<template>
    <BaseModal :show-close="false" ref="modal" static body-classes="p-0" header-classes="border-0">
        <template #header>
            <div class="header">
                <div>
                    <BaseButton @click="modal.hide()" v-if="currentStep == 'newGroup' || currentStep == 'groupCreated'">
                        <img :src="closeIcon" alt="close new group modal" class="close">
                    </BaseButton>
                    <BaseButton @click="prevStep" v-else-if="currentStep == 'selectMembers'">
                        <img :src="backIcon" alt="close new group modal" class="back">
                    </BaseButton>
                </div>
                <h2 class="title mb-0">{{ title }}</h2>
                <div class="d-flex">
                    <BaseButton class="btn-primary ms-auto" :disabled="nextDisabled" @click="nextStep">
                        {{ nextButtonText }}
                    </BaseButton>
                </div>
            </div>
        </template>
        <div class="body rounded">
            <div v-if="currentStep === 'newGroup'" class="new-group">
                <DropZone v-model="profile" class="d-flex mb-4">
                    <BaseButton class="d-inline-block m-auto position-relative p-0" @click="profileImgInput.click()">
                        <input type="file" class="d-none" @change="updateProfileImage" ref="profileImgInput">
                        <BaseButton v-show="!!profile" @click.stop="removeProfileImg" class="remove-profile"><img :src="closeIcon" alt="remove profiel image"></BaseButton>
                        <img class="d-block m-auto profile" :src="profileImg" alt="group image">
                    </BaseButton>
                </DropZone>
                <p class="text-secondary text-center mb-4">Drag & drop an image, or click to upload</p>
                <input type="text" name="name" placeholder="Enter group name" class="form-control rounded-pill py-3 px-4" v-model="groupName">
            </div>
            <div v-else-if="currentStep === 'selectMembers'">
                <Members v-model="selectedMembers" />
            </div>
            <div v-else-if="currentStep === 'groupCreated'" class="new-group">
                <img class="d-block m-auto profile mb-4" :src="confirmationIcon" alt="group created">
                <p class="text-secondary text-center mb-0">Group created</p>
            </div>
        </div>
    </BaseModal>
</template>

<script setup>
import { ref, computed, watch } from 'vue';
import { headers, baseUrl } from '@/api.js';
import BaseModal from '@/components/base-components/BaseModal.vue';
import BaseButton from '@/components/base-components/BaseButton.vue';
import DropZone from '@/components/base-components/DropZone.vue';
import Members from '@/components/Members.vue';
import closeIcon from '@/assets/icons/Close.svg';
import groupIcon from '@/assets/icons/Group.svg';
import backIcon from '@/assets/icons/Back.svg';
import confirmationIcon from '@/assets/icons/Confirmation.svg';

const modal = ref();

const show = () => {
    modal.value.show();
}

const nextDisabled = computed(()=>{
    switch (currentStep.value) {
        case 'newGroup':
            if(profile.value && groupName.value.length) {
                return false;
            } else {
                return true;
            }
        case 'selectMembers':
            return selectedMembers.value.length == 0;
        case 'groupCreated':
            return false;
        default:
            return true;
    }
});
const selectedMembers = ref([]);
const profileImgInput = ref();
const profile = ref();
const groupName = ref(''); 

const profileImg = ref(groupIcon);

const updateProfileImage = (e) => {
    let files = e.target.files || e.dataTransfer.files;
    if (!files.length) return;
    profile.value = files[0];
}
const saveGroup = async () => {
    let body = JSON.stringify({
        name: groupName.value,
        members: selectedMembers.value.map(x=>x.id),
        avatar: btoa(profileImg.value)
    });
    try {

        let response = await fetch(`${baseUrl}/createGroup`, {
            method: 'POST',
            headers,
            body: body
        });
        response = await response.json();
        currentStep.value = 'groupCreated';
    } catch (e) {
        // TODO: fix name param bug.
        alert('Failed to create group. Please contact your system admin.');
        console.log(e);
    }
}

const nextButtonText = computed(()=>{
    switch (currentStep.value) {
        case 'newGroup':
            return 'Next';
        case 'selectMembers':
            return 'Save';
        case 'groupCreated':
            return 'Done';
        default:
            return 'Next';
    }
})

const title = computed(() => {
    switch (currentStep.value) {
        case 'newGroup':
            return 'New Group';
        case 'selectMembers':
            return 'Group Members';
        case 'groupCreated':
            return 'Group Created';
        default:
            return '';
    }
})

const removeProfileImg = () => {
    profile.value = null;
    profileImg.value = groupIcon;
}

const reset = () => {
    profile.value = null;
    groupName.value = '';
    selectedMembers.value = [];
    currentStep.value = 'newGroup';
    modal.value.hide();
    console.log(currentStep.value)
}

const currentStep = ref('newGroup');
const nextStep = () => {
    switch (currentStep.value) {
        case 'newGroup':
            currentStep.value = 'selectMembers';
            break;
        case 'selectMembers':
            saveGroup();
            break;
        case 'groupCreated':
            reset();
            break;
        default:
            break;
    }
    if(currentStep.value == 'newGroup') {
        currentStep.value = 'selectMembers';
    }
}
const prevStep = () => {
    if(currentStep.value == 'selectMembers') {
        currentStep.value = 'newGroup';
    }
}

watch(profile, (newVal) => {
    if(newVal) {
        const reader = new FileReader();
        reader.onload = function (e) {
            profileImg.value = e.target.result;
        }
        reader.readAsDataURL(newVal);
    }
});

defineExpose({ show });
</script>

<style lang="scss" scoped>
@import '@/assets/variables.scss';
.header {
    display: grid;
    width: 100%;
    grid-template-columns: 1fr auto 1fr;
    align-items: center;
    .close {
        width: 20px;
        filter: $modal-close-color-filter;
    }
    .back {

    }

    .title {
        font-size: 1.3rem;
    }
}

.new-group {
    padding: 3.6rem;
}
.body {
    background-color: $modal-body-bg;

    img.profile {
        width: 144px;
        height: 144px;
        object-fit: cover;
        border-radius: 50%;
    }

    .remove-profile {
        width: 18px;
        height: 18px;
        padding: 0;
        display: flex;
        justify-content: center;
        align-items: center;
        background-color: $secondary;
        border-radius: 50%;
        position: absolute;
        top: 6px;
        right: 16px;

        img {
            width: 1rem;
        }
    }
}

</style>