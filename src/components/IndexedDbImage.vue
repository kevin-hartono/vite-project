<template>
  <div>
    <p>Data added: {{ addedData }}</p>
    <p>Data updated: {{ updatedData }}</p>
    <p>Data deleted: {{ deletedData }}</p>
    <button @click="addData">Add Data</button>
    <button @click="readData">Read Data</button>
    <button @click="updateData">Update Data</button>
    <button @click="deleteData">Delete Data</button>
    <ul>
      <li v-for="customer in customers" :key="customer.ssn">
        {{ customer.name }}
      </li>
    </ul>
    <h1>rendered</h1>
    <img :src="dataUrl">
  </div>
</template>

<script setup>
import {ref, onMounted, computed} from 'vue';

const dbName = "image";
const updatedCustomerData = {ssn: "666-66-6666", name: "Alice Updated", age: 30, email: "alice.updated@example.com"};
const keyToDelete = "666-66-6666";

const addedData = ref(null);
const updatedData = ref(null);
const deletedData = ref(null);
const customers = ref([]);

const dataUrl = ref("")

let count = 0;

const customerToAdd = computed(() => {
  return {
    ssn: `666-66-6666${count}`,
    name: "Alice",
    age: 28,
    email: `alice${count}@example.com`
  }
});

onMounted(() => {
  // readData()
})


function toDataURL(url, callback) {
  var xhr = new XMLHttpRequest();
  xhr.onload = function() {
    var reader = new FileReader();
    reader.onloadend = function() {
      callback(reader.result);
    }
    reader.readAsDataURL(xhr.response);
  };
  xhr.open('GET', url);
  xhr.responseType = 'blob';
  xhr.send();
}

const addData = () => {
  toDataURL('/pwa-192x192.png', function(dataUrl1) {
    console.log('RESULT:', dataUrl1)
    const request = indexedDB.open(dbName, 2);

    request.onerror = (event) => {
      console.error("Error opening database:", event.target.error);
    };

    request.onupgradeneeded = (event) => {
      const db = event.target.result;

      const objectStore = db.createObjectStore("images", {keyPath: "name"});
    };

    request.onsuccess = (event) => {
      const db = event.target.result;

      const addTransaction = db.transaction("images", "readwrite");
      const customerObjectStore = addTransaction.objectStore("images");

      const data = {
        "name" : "image1",
        "blob" : dataUrl1
      }

      const addRequest = customerObjectStore.put(data);

      addRequest.onsuccess = (event) => {
        console.log("Data added successfully");
        addedData.value = "Yes";
      };

      addRequest.onerror = (event) => {
        console.error("Error adding data", event.target.error);
        addedData.value = "No";
      };

      addTransaction.oncomplete = () => {
        console.log("Add transaction completed");
        db.close();
      };
    };
  })
};

const readData = () => {
  const request = indexedDB.open(dbName, 2);

  request.onerror = (event) => {
    console.error("Error opening database:", event.target.error);
  };

  request.onsuccess = (event) => {
    const db = event.target.result;

    const readTransaction = db.transaction("images", "readonly");
    const customerObjectStore = readTransaction.objectStore("images");

    const customersCursor = customerObjectStore.openCursor();

    customersCursor.onsuccess = (event) => {
      const cursor = event.target.result;

      if (cursor) {
        // customers.value.push(cursor.value);
        console.log('customers.value:', customers.value)
        customers.value.splice(cursor.value.index, 1, cursor.value);
        dataUrl.value = cursor.value.blob
        cursor.continue();
      } else {
        count = customers.value.length
        console.log("Data read successfully");
        // customers.value.splice(0, customers.value.length);
        db.close();
      }
    };

    customersCursor.onerror = (event) => {
      console.error("Error reading data", event.target.error);
      db.close();
    };
  };
};

const updateData = () => {
  toDataURL('./vue.svg', function(dataUrl1) {
    const request = indexedDB.open(dbName, 2);

    request.onerror = (event) => {
      console.error("Error opening database:", event.target.error);
    };

    request.onsuccess = (event) => {
      const db = event.target.result;

      const updateTransaction = db.transaction("images", "readwrite");
      const customerObjectStore = updateTransaction.objectStore("images");

      const data = {
        "name" : "image1",
        "blob" : dataUrl1
      }

      const updateRequest = customerObjectStore.put(data);

      updateRequest.onsuccess = (event) => {
        console.log("Data updated successfully");
        updatedData.value = "Yes";
      };

      updateRequest.onerror = (event) => {
        console.error("Error updating data", event.target.error);
        updatedData.value = "No";
      };

      updateTransaction.oncomplete = () => {
        console.log("Update transaction completed");
        db.close();
      };
    };
  })
};

const deleteData = () => {
  const request = indexedDB.open(dbName, 2);

  request.onerror = (event) => {
    console.error("Error opening database:", event.target.error);
  };

  request.onsuccess = (event) => {
    const db = event.target.result;

    const deleteTransaction = db.transaction("images", "readwrite");
    const deleteObjectStore = deleteTransaction.objectStore("images");

    const deleteRequest = deleteObjectStore.delete("image1");

    deleteRequest.onsuccess = (event) => {
      console.log("Data deleted successfully");
      deletedData.value = "Yes";
    };

    deleteRequest.onerror = (event) => {
      console.error("Error deleting data", event.target.error);
      deletedData.value = "No";
    };

    deleteTransaction.oncomplete = () => {
      console.log("Delete transaction completed");
      db.close();
    };
  };
};
</script>