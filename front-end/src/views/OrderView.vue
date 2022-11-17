<template>
  <div class="orders">
    <h1 id="title1">Order Information</h1>
  </div>
  <div class="form-group">
    <form id="searching">
      <div class="form-group">
        <input
          id="orderId"
          v-model="orderId"
          type="number"
          class="form-control"
          placeholder="Order ID"
        >
        <!--Search button for the Order Id field-->
        <button
          type="button"
          class="btn btn-outline-danger"
          @click="getOrderId"
        >
          Search
        </button>
        <button
          type="button"
          class="btn btn-outline-danger"
          @click="getOrders"
        >
          Get All Orders
        </button>
      </div>
    </form>
  </div>

  <button
    id="show-modal1"
    class="btn btn-outline-danger"
    @click="showModal = true"
  >
    Create Order
  </button>

  <Teleport to="body">
    <!-- use the modal component, pass in the prop -->

    <OrderModal
      :show="showModal"
      @close="showModal = false"
      @form-submit="createOrder()"
    >
      <template #header>
        <h3>Create a new Order</h3>
      </template>

      <template #body>
        <div>
          <p>
            Customer Id:
            <input
              id="customerIDValue"
              v-model="customerIdValue"
              type="number"
              class="form-control"
              placeholder="Customer Id"
              required
            >
          </p>
          <p>
            Order Id:
            <input
              id="orderIdValue"
              v-model="orderIDValue"
              type="number"
              class="form-control"
              placeholder="Order Id"
              required
            >
          </p>
          <p>
            Order Status:
            <input
              id="orderStatusCode"
              v-model="orderStatusCode"
              type="number"
              class="form-control"
              placeholder="orderStatusCode(1 to 5)"
              required
            >
          </p>
          <p>
            Shipping Address Id:
            <input
              id="shippingAddressID"
              v-model="shippingAddressID"
              type="number"
              class="form-control"
              placeholder="shippingAddressId"
            >
          </p>
          <p>
            Order Notes:
            <input
              id="orderNotes"
              v-model="orderNotes"
              type="text"
              class="form-control"
              placeholder="Order Notes"
            >
          </p>
        </div>
      </template>
    </OrderModal>
  </Teleport>
  <!--Data Table-->
  <table
    id="orders-table"
    class="table table-striped"
  >
    <thead>
      <tr>
        <th scope="col">Order ID</th>
        <th scope="col">Customer ID</th>
        <th scope="col">Order Placed</th>
        <th scope="col">Order Status Code</th>
      </tr>
    </thead>
    <tbody>
      <tr
        v-for="order in orders"
        :key="order.id"
      >
        <!--Change these to proper fields-->
        <td scope="row">{{ order.orderId }}</td>
        <td>{{ order.customerId }}</td>
        <td>{{ order.orderPlaced }}</td>
        <td>{{ order.orderStatusCode }}</td>
      </tr>
    </tbody>
  </table>
</template>

<script>
import axios from 'axios';
import OrderModal from '../components/CreateOrderModal.vue';
export default {
  name: 'App',
  components: {
    OrderModal,
  },
  data() {
    return {
      orders: [],
      customerIdValue: '',
      orderIDValue: '',
      orderStatusCode: '',
      shippingAddressID: '',
      orderNotes: '',
      showModal: false,
    };
  },

  mounted: async function () {
    let orders = await axios.get('http://localhost:3000/api/v1/orders/')
      .catch((errors) => {
        console.log(errors); // Errors
      });
    this.orders = orders.data;
  },
  methods: {
    // The get method called by the function

    async getOrders() {
      let orders = await axios.get('http://localhost:3000/api/v1/orders/')
        .catch((errors) => {
          console.log(errors); // Errors
        });
      this.orders = orders.data;
    },

    async getOrderId() {
      let orders = await axios.get(`http://localhost:3000/api/v1/orders/${this.orderId}`)
        .catch((errors) => {
          console.log(errors); // Errors
        });
      this.orders = [orders.data];
    },
    async createOrder() {
      await axios
        .post('http://localhost:3000/api/v1/orders', {
          orderId: this.orderIDValue,
          customerId: this.customerIdValue,
          orderStatusCode: this.orderStatusCode,
          shippingAddressId: this.shippingAddressID,
        })
        .catch((errors) => {
          console.log(errors); // Errors
        });
      this.orders = this.getOrders().data;
    },
  },
};
</script>

<style>

#title1{
  font-size: 5rem;
  font-weight: bolder;
  margin-top: 100px;
  color: #9b0c23;
}
#orders-table{
  border: 2px solid;
  border-radius: 10px;

}
</style>
