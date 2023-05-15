<template>
  <div class="flex min-h-full">
    <div class="flex-1">
      <div class="flex min-h-full flex-col justify-center items-center px-6 py-12 lg:px-8">
        <template v-for="(data, index) in cardData" :key="index">
          <Card :name="data.name" :review="data.review" />
        </template>
      </div>
    </div>
    <div class="flex-1">
      <div class="flex min-h-full flex-col justify-center px-6 py-12 lg:px-8">
        <div class="sm:mx-auto sm:w-full sm:max-w-sm">
          <img
            class="mx-auto h-10 w-auto"
            src="https://tailwindui.com/img/logos/mark.svg?color=indigo&shade=600"
            alt="Your Company"
          />
          <h2 class="mt-10 text-center text-2xl font-bold leading-9 tracking-tight text-gray-900">
            Please Submit Your Review - {{ clientName }}
          </h2>
        </div>

        <div class="mt-10 sm:mx-auto sm:w-full sm:max-w-sm">
          <div>
            <label for="email" class="block text-sm font-medium leading-6 text-gray-900"
              >Name</label
            >
            <div class="mt-2">
              <input
                v-model="name"
                id="name"
                name="name"
                type="text"
                required
                class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
              />
            </div>
          </div>

          <div>
            <div class="flex items-center justify-between">
              <label for="password" class="block text-sm font-medium leading-6 text-gray-900"
                >Review</label
              >
            </div>
            <div class="mt-2">
              <input
                v-model="review"
                id="review"
                name="review"
                type="text"
                required
                class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
              />
            </div>
          </div>

          <div class="pt-10">
            <button
              @click="postReviews"
              class="flex w-full justify-center rounded-md bg-indigo-600 px-3 py-1.5 text-sm font-semibold leading-6 text-white shadow-sm hover:bg-indigo-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600"
            >
              Submit Review
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Card from '../components/Cardlist.vue'
import {
  collection,
  getDocs,
  addDoc,
  serverTimestamp,
  where,
  query,
  orderBy
} from 'firebase/firestore'
import { db } from '../utils/fireabse'
export default {
  components: {
    Card
  },
  data() {
    return {
      name: '',
      review: '',
      clientName: import.meta.env.VITE_NAME,
      cardData: []
    }
  },
  methods: {
    getRequestObject() {
      return {
        name: this.name,
        review: this.review,
        show: true,
        timeStamp: serverTimestamp()
      }
    },
    resetFormData() {
      this.name = ''
      this.review = ''
    },
    async getReviews() {
      const q = query(
        collection(db, 'review'),
        orderBy('timeStamp', 'desc'),
        where('show', '==', true)
      )
      const result = await getDocs(q)
      this.cardData = result.docs.map((doc) => {
        const { name, review } = doc.data()
        return { name, review }
      })
    },
    async postReviews() {
      const paylaod = this.getRequestObject()
      try {
        const docRef = await addDoc(collection(db, 'review'), paylaod)
        this.resetFormData()
        this.getReviews()
      } catch (e) {
        console.error('Error adding document: ', e)
      }
    }
  },
  mounted() {
    this.getReviews()
  }
}
</script>
