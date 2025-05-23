<script setup>
import { ref } from 'vue'
import AdminListTemplate from '@/features/admin/components/AdminListTemplate.vue'
import { fetchAccount } from '@/api/admin.js'  // 실제 API 연동

// 페이지 제목을 props로 전달받음
const props = defineProps({ pageTitle: String })

// 필터 상태 관리
const filters = ref({
  authority: '', // 권한 필터 (MEMBER, OWNER 등)
  status: ''     // 상태 필터 (PENDING, APPROVED 등)
})

const page = ref(1) // 현재 페이지 번호
const rows = ref([]) // 테이블에 표시될 데이터
const totalPages = ref(1) // 전체 페이지 수


// 계좌 내역 조회 함수
const fetchList = async ({ page = 1 }) => {
  try {
    const params = {
      page: page.value || 1,
      XUserRole: filters.value.authority || null,
      statusType: filters.value.status || null
    }

// 불필요한 파라미터 제거
    Object.keys(params).forEach(key => {
      if (params[key] === '' || params[key] === null || params[key] === undefined) {
        delete params[key]
      }
    })
    const res = await fetchAccount(params)
    console.log('응답 데이터:', res)

    return {
      data: res.data?.data?.content || [],
      totalPages: res.data?.data?.totalPages || 1
    }
  } catch (e) {
    console.error('API 요청 실패:', e)
    return { data: [], totalPages: 1 }
  }
}


// 테이블 컬럼 설정
const columns = [
  { key: 'accountId', label: '계좌 ID' },
  { key: 'userId', label: '사용자 ID' },
  { key: 'roleName', label: '권한' },
  {
    key: 'statusType',
    label: '상태',
    format: (v) => {
      switch (v) {
        case 'PENDING': return '대기'
        case 'ACCEPTED': return '승인'
        case 'REJECTED': return '거절'
        default: return v
      }
    }
  },
  { key: 'bankName', label: '은행명' },
  { key: 'accountNumber', label: '계좌번호' },
  { key: 'holderName', label: '예금주명' },
  { key: 'createdAt', label: '등록일' }
]
</script>

<template>
  <AdminListTemplate
    :fetchFn="fetchList"
    :columns="columns"
    :initFilters="filters"
    :pageTitle="props.pageTitle"
    :enableModal="false"
  >
    <!-- 필터 영역 -->
    <template #filters="{ filters }">
      <label class="filter-label">
        권한:
        <select v-model="filters.authority" class="select-box">
          <option value="">전체</option>
          <option value="USER">회원</option>
          <option value="OWNER">사업자</option>
        </select>
      </label>

      <label class="filter-label">
        상태:
        <select v-model="filters.status" class="select-box">
          <option value="">전체</option>
          <option value="PENDING">대기</option>
          <option value="ACCEPTED">승인</option>
          <option value="REJECTED">거절</option>
        </select>
      </label>
    </template>
  </AdminListTemplate>
</template>


<style scoped>
.filter-wrapper {
  display: flex;
  margin-bottom: 12px;
  justify-content: space-between;
}

.filter-box {
  display: flex;
  justify-content: flex-end;
  align-items: center;
  gap: 15px;
}

.filter-label {
  display: flex;
  align-items: center;
  font-size: 14px;
  gap: 6px;
}

.select-box {
  margin-left: 12px;
  padding: 6px 10px;
  font-size: 14px;
  border: 1px solid #ccc;
  border-radius: 6px;
  background-color: #fff;
  color: #333;
}

.filter-fields {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
  align-items: center;
  border: 0;
  padding: 0;
  margin: 0;
}

.input-box {
  min-width: 160px;
}

select,
input[type="text"] {
  height: 32px;
  padding: 4px 10px;
  font-size: 14px;
  border: 1px solid #ccc;
  border-radius: 6px;
  background-color: #fff;
  color: #333;
}

select:focus,
input[type="text"]:focus {
  outline: none;
  border-color: #7d6fb3;
  box-shadow: 0 0 0 2px rgba(125, 111, 179, 0.2);
}

.id-input {
  width: 50px;
}

/* 스크린리더 전용 */
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}
</style>
