<template>
  <div class="container my-5">

    <!-- หัวข้อ -->
    <h2 class="text-center mb-4">
      💰 ราคาทองวันนี้
    </h2>

    <!-- ปุ่มอัปเดต -->
    <div class="text-center mb-3">
      <button
        class="btn btn-primary"
        @click="fetchGold"
        :disabled="loading"
      >
        {{ loading ? 'กำลังโหลด...' : '🔄 อัปเดตราคาทอง' }}
      </button>
    </div>

    <!-- แสดงข้อความ Error -->
    <div
      v-if="errorMessage"
      class="alert alert-danger text-center"
    >
      {{ errorMessage }}
    </div>

    <!-- ตาราง -->
    <table class="table table-bordered table-striped text-center align-middle">

      <thead class="table-dark">
        <tr>
          <th>ประเภททอง</th>
          <th>ราคารับซื้อ</th>
          <th>ราคาขายออก</th>
        </tr>
      </thead>

      <tbody>

        <!-- กำลังโหลด -->
        <tr v-if="loading">
          <td colspan="3">
            กำลังโหลดข้อมูล...
          </td>
        </tr>

        <!-- แสดงข้อมูล -->
        <tr
          v-for="item in golds"
          :key="item.name"
        >
          <td>
            {{ item.name }}
          </td>

          <td class="text-success fw-bold">
            {{ formatNumber(item.buy) }} บาท
          </td>

          <td class="text-danger fw-bold">
            {{ formatNumber(item.sell) }} บาท
          </td>
        </tr>

      </tbody>

    </table>

  </div>
</template>


<script setup>

import { ref, onMounted } from 'vue'

// เก็บข้อมูลราคาทอง
const golds = ref([])

// สถานะ Loading
const loading = ref(false)

// เก็บข้อความ Error
const errorMessage = ref('')


// ดึงข้อมูล API
const fetchGold = async () => {

  loading.value = true
  errorMessage.value = ''

  try {

    const res = await fetch(
      'https://api.chnwt.dev/thai-gold-api/latest'
    )

    if (!res.ok) {
      throw new Error('ไม่สามารถโหลดข้อมูลได้')
    }

    const data = await res.json()

    const price = data.response.price

    golds.value = [

      // ทองรูปพรรณ
      {
        name: 'ทองรูปพรรณ',

        buy: parseFloat(
          price.gold.buy.replace(/,/g, '')
        ),

        sell: parseFloat(
          price.gold.sell.replace(/,/g, '')
        )
      },

      // ทองคำแท่ง
      {
        name: 'ทองคำแท่ง',

        buy: parseFloat(
          price.gold_bar.buy.replace(/,/g, '')
        ),

        sell: parseFloat(
          price.gold_bar.sell.replace(/,/g, '')
        )
      }

    ]

  } catch (error) {

    console.error(
      'โหลดข้อมูลผิดพลาด:',
      error
    )

    errorMessage.value =
      'ไม่สามารถโหลดราคาทองได้ กรุณาลองใหม่อีกครั้ง'

  } finally {

    loading.value = false

  }
}


// จัดรูปแบบตัวเลข
const formatNumber = (num) => {

  if (num === null || num === undefined || Number.isNaN(num)) {
    return '-'
  }

  return num.toLocaleString('th-TH')

}


// โหลดข้อมูลทันทีเมื่อเปิดหน้า
onMounted(() => {
  fetchGold()
})

</script>