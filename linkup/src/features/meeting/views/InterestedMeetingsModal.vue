<script setup>

import { onMounted, ref } from 'vue';
import { useAuthStore } from '@/stores/auth.js';
import api from '@/api/axios.js';

const userStore = useAuthStore();
const meetings = ref([]);

const isLoading = ref(true);

meetings.value = [{
  meetingTitle: "제목",
  placeName: "종합운동장",
  statusId: 1
}
];

onMounted(async() => {
  try {
    const userId = userStore.userId;
    const response = await api.get(`/common-service/meetings/interested/${userId}`);
    meetings.value = response.data.data.meetings;
  } catch (e) {
    console.error('개설 모임 조회 실패', e);
  } finally {
    isLoading.value = false;
  }
})

const statusName = (id) => {
  switch(id) {
    case 1:
      return '모집중';
    case 2:
      return '최소 인원 모집 완료';
    case 3:
      return '모집 완료';
    case 4:
      return '모집 취소';
    case 5:
      return '모임 진행 완료';
  }
}

const emit = defineEmits(['close', 'select']);
</script>

<template>
  <template v-if="isLoading">
    로딩 중
  </template>
  <template v-else>
  <div class="assignment-modal">
    <div class="modal-box">
      <!-- 모달 헤더 -->
      <div class="modal-header">
        <img src="@/assets/icons/meeting_and_place/sidebar-interested_meetings.svg" alt="찜 모임 목록" class="icon-img"/>
        <h2>찜한 모임 목록</h2>
        <button class="close-btn" @click="$emit('close')">×</button>
      </div>

      <div class="assignment-list">
        <!-- 예시 카드 -->
        <div class="assignment-card"
             v-for="meeting in meetings"
             :key="meeting.meetingId"
             @click="$emit('select', meeting)"
        >
          <img src="https://cdn.pixabay.com/photo/2019/03/10/14/04/table-tennis-4046278_640.jpg" alt="썸네일" class="assignment-thumb" />
          <div class="assignment-content">
            <div class="assignment-title"> {{meeting.meetingTitle}} </div>
            <div class="assignment-address"> {{ meeting.placeName || meeting.customPlaceAddress }} </div>
          </div>
          <div class="assignment-status"> {{ statusName(meeting.statusId) }} </div>
          <button class="bookmark">
            <img src="/src/assets/icons/community/heart.svg" alt="찜 취소" class="heart"/>
          </button>
        </div>

      </div>

    </div>
  </div>
  </template>
</template>

<style scoped>
.modal-header {
  display: flex;
  justify-content: space-between;
}

.modal-header .icon-img {
  width: 28px;
  height: 28px;
  margin-top: 2px;
}

.assignment-modal .close-btn {
  background: none;
  border: none;
  cursor: pointer;
  font-size: 22px;
  color: #94a3b8;
}

.heart {
  width: 24px;
  height: 24px;
}

.bookmark {
  font-size: 20px;
}
/* ---------------- 모임 목록 모달 ---------------- */
.assignment-modal {
  position: fixed;
  top: 300px; /* 정확히 두 번째 버튼과 정렬 */
  right: 90px; /* 버튼 오른쪽 여백 기준 */
  width: 27%; /* 모달 너비 확장 */
  min-width: 500px; /* 최소 너비 설정 */
  max-width: 600px; /* 최대 너비 설정 */
  background-color: #ffffff;
  border-radius: 24px;
  padding: 40px;
  box-shadow: 0 12px 40px rgba(0, 0, 0, 0.1);
  z-index: 1000;
  display: block;
}
.assignment-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 32px;
  font-size: 22px; /* 헤더 폰트 크기 증가 */
  font-weight: 700;
  color: #111827;
}
.assignment-header h2 {
  font-size: 18px;
  font-weight: 700;
  color: #1f2937;
  margin: 0;
}
.assignment-close {
  background: none;
  border: none;
  font-size: 20px;
  color: #94a3b8;
  cursor: pointer;
}
.assignment-list {
  display: flex;
  flex-direction: column;
  gap: 20px;
}


/* 개설 모임 카드 */
.assignment-card {
  background-color: #f4f6fb;
  border-radius: 16px;
  padding: 12px 14px;
  display: flex;
  align-items: center;
  gap: 16px;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.04);
}
.assignment-thumb {
  width: 60px;
  height: 60px;
  border-radius: 12px;
  object-fit: cover;
}
.assignment-content {
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: 8px;
  overflow: hidden;
}
.assignment-title {
  font-size: 16px;
  font-weight: 600;
  color: #111827;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
.assignment-address {
  font-size: 14px;
  color: #6b7280;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
.assignment-status {
  font-size: 14px;
  font-weight: 600;
  white-space: nowrap;
}
.assignment-status.모집중 {
  color: #3b82f6;
}
.assignment-status.진행완료 {
  color: #7c3aed;
}
</style>