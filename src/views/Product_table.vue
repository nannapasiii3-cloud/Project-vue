<template>
  <div class="container my-5">

    <!-- หัวข้อ -->
    <h2 class="text-center mb-4">
      แสดงข้อมูลสินค้า
    </h2>

    <!-- ปุ่มอัปเดต -->
    <div class="text-center mb-4">
      <button
        class="btn btn-primary"
        @click="fetchProducts"
        :disabled="loading"
      >
        🔄 {{ loading ? "กำลังโหลด..." : "อัปเดต" }}
      </button>
    </div>

    <!-- ตาราง -->
    <table class="table table-bordered table-striped align-middle">

      <!-- หัวตาราง -->
      <thead class="table-dark text-center">
        <tr>
          <th>รหัสสินค้า</th>
          <th>รูปภาพ</th>
          <th>ชื่อสินค้า</th>
          <th>จำนวน</th>
          <th>ราคา</th>
        </tr>
      </thead>

      <tbody>

        <!-- Loading -->
        <tr v-if="loading">
          <td colspan="5" class="text-center">
            กำลังโหลดข้อมูล...
          </td>
        </tr>

        <!-- แสดงสินค้า -->
        <tr
          v-for="item in products"
          :key="item.id"
        >

          <!-- รหัสสินค้า -->
          <td class="text-center">
            {{ item.id }}
          </td>

          <!-- รูปภาพ -->
          <td class="text-center">
            <img
              :src="item.thumbnail"
              :alt="item.title"
              class="product-image"
            />
          </td>

          <!-- ชื่อสินค้า -->
          <td class="product-name">
            {{ item.title }}
          </td>

          <!-- จำนวนสินค้า -->
          <td class="text-center">
            {{ item.stock }}
          </td>

          <!-- ราคา -->
          <td class="text-center text-danger">
            ${{ formatNumber(item.price) }}
          </td>

        </tr>

      </tbody>

    </table>

  </div>
</template>


<script>
import { ref, onMounted } from "vue";

export default {

  setup() {

    // เก็บข้อมูลสินค้า
    const products = ref([]);

    // Loading
    const loading = ref(false);


    // ดึงข้อมูลสินค้า
    const fetchProducts = async () => {

      loading.value = true;

      try {

        const response = await fetch(
          "https://dummyjson.com/products"
        );

        const data = await response.json();

        // DummyJSON เก็บสินค้าไว้ใน products
        products.value = data.products;

      } catch (error) {

        console.error(
          "Error fetching products:",
          error
        );

      } finally {

        loading.value = false;

      }

    };


    // จัดรูปแบบราคา
    const formatNumber = (number) => {

      return Number(number).toLocaleString(
        "en-US",
        {
          minimumFractionDigits: 2,
          maximumFractionDigits: 2
        }
      );

    };


    // โหลดข้อมูลตอนเปิดหน้า
    onMounted(fetchProducts);


    // ส่งไปใช้ใน Template
    return {
      products,
      loading,
      fetchProducts,
      formatNumber
    };

  }

};
</script>


<style scoped>

/* ตาราง */
.table {
  width: 100%;
  font-size: 16px;
}

/* หัวตาราง */
.table th {
  vertical-align: middle;
  padding: 15px;
}

/* ช่องข้อมูล */
.table td {
  vertical-align: middle;
  padding: 12px;
}

/* รูปสินค้า */
.product-image {
  width: 80px;
  height: 70px;
  object-fit: contain;
}

/* ชื่อสินค้า */
.product-name {
  color: #198754;
  font-size: 16px;
}

/* กำหนดความกว้าง Column */
.table th:nth-child(1) {
  width: 12%;
}

.table th:nth-child(2) {
  width: 12%;
}

.table th:nth-child(3) {
  width: 50%;
}

.table th:nth-child(4) {
  width: 12%;
}

.table th:nth-child(5) {
  width: 14%;
}

</style>