<template>
    <div class="overflow-hidden">
        <div class="input-group">
            <span class="input-group-text"><img :src="searchIcon"></span>
            <input type="text" class="form-control py-3 pe-4" placeholder="Search" v-model="searchText">
        </div>
        <ul class="list-group">
            <li class="list-group-item" v-for="member in members" :key="`member${member.id}`">
                <BaseButton @click="toggleMember(member)">
                    <img class="avatar" :src="member.avatar" alt="picture">
                    <div>{{ member.first_name }} {{ member.last_name }}</div>
                    <img class="icon" :class="{'added': member.added}" :src="member.added ? checkIcon : addIcon" alt="add member">
                </BaseButton>
            </li>
        </ul>
    </div>
</template>
<script setup>
import { ref, onMounted, watch } from 'vue';
import { useDebouncedRef } from '@/helpers.js';
import { headers, baseUrl } from '@/api.js';
import BaseButton from '@/components/base-components/BaseButton.vue';
import searchIcon from '@/assets/icons/Search.svg';
import addIcon from '@/assets/icons/Add.svg';
import checkIcon from '@/assets/icons/Check.svg';

const props = defineProps({
    modelValue: Array
})
const emit = defineEmits(['update:modelValue']);

const searchText = useDebouncedRef('', 500);
const members = ref([]);

const toggleMember = (member) => {
    if(props.modelValue.find(x=>x.id === member.id)) {
        member.added = false;
        emit('update:modelValue', [
            ...props.modelValue
        ].filter(x=>x.id !== member.id))
    } else {
        member.added = true;
        emit('update:modelValue', [
            ...props.modelValue,
            member
        ]);
    }
}

const memberIsAdded = (member) => {
    if(props.modelValue.findIndex(x=>x.id === member.id) >= 0) {
        return true;
    } else {
        return false;
    }
}

const getContacts = async () => {
    let response = await fetch(`${baseUrl}/getContacts?search=${searchText.value}`, {
        method: 'get',
        headers
    });
    response = await response.json();
    members.value = response.contacts.map(x=>{
        return {
            ...x,
            added: memberIsAdded(x)
        }
    });
}

watch(searchText, ()=>{
    getContacts();
});

onMounted(()=>{
    getContacts();
})
</script>

<style lang="scss" scoped>
@import '@/assets/variables.scss';
.form-control {
    border-top-right-radius: var(--fc-border-radius-pill);
    border-bottom-right-radius: var(--fc-border-radius-pill);
    border-left: 0;
    padding-left: 0;
    &:focus {
        box-shadow: none;
        border-color: var(--fc-gray-400);
    }
}
.input-group {
    padding:  1.4rem;
    box-shadow: 0 10px 40px -10px rgba(223,231,242,0.5);
}
.input-group-text {
    background-color: $white;
    border-right: 0;
    border-bottom-left-radius: var(--fc-border-radius-pill);
    border-top-left-radius: var(--fc-border-radius-pill);
    padding: 0 1rem;
}

.list-group {
    padding-top: 1rem;
    max-height: 370px;
    overflow-y: scroll;
    &::-webkit-scrollbar {
        display: none;
    }
}
.list-group-item {
    background: transparent;
    border: 0;
    padding: 0;
    button {
        width: 100%;
        display: flex;
        align-items: center;
        padding:  6px 1.4rem;

        &:hover {
            background-color: $white;
        }
    }

    .avatar {
        border-radius: 50%;
        width: 3.4rem;
        height: 3.4rem;
        object-fit: cover;
        margin-right: 1rem;
    }
    .icon {
        width: 1.75rem;
        height: 1.75rem;
        background-color: $secondary;
        padding: 0.4rem;
        border-radius: 50%;
        margin-left: auto;
        transition: $transition-base;
        &.added {
            background-color: $green;

        } 
    }
}
</style>