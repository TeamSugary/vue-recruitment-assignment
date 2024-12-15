<template>
  <div class="wrapper">
    <h2>Submit a Complaint</h2>

    <!-- Form to add a new complaint -->
    <div class="complain-form">
      <!-- 💡 Input for title -->
      <input v-model="title" type="text" placeholder="Title" />
      <!-- 📝 Textarea for complaint body -->
      <textarea v-model="body" placeholder="Enter your complaint"></textarea>

      <!-- ✉️ Submit button -->
      <button @click="saveComplain" :disabled="isSaving">
        Submit Complaint
      </button>

      <!-- ⏳ Simple text loader when saving -->
      <div v-if="isSaving">Saving...</div>

      <!-- ❌ Error message -->
      <div v-if="errorMessage" class="error-message">{{ errorMessage }}</div>
    </div>

    <h2>Complaints List</h2>

    <!-- ⏳ Simple text loader when loading -->
    <div v-if="isLoading">Loading...</div>
    <!-- 🗂️ List of complaints -->
    <template v-else-if="complains.length">
      <div
        v-for="complain in complains"
        :key="complain.Id"
        class="complain-item"
      >
        <!-- 🏷️ Complaint title -->
        <h3>{{ complain.Title }}</h3>
        <!-- 📜 Complaint body -->
        <p>{{ complain.Body }}</p>
      </div>
    </template>
    <div v-else>
      <!-- 🚫 No complaints available message -->
      <p>No complaints available.</p>
    </div>
  </div>
</template>

<script setup>
const baseUrl = "https://sugarytestapi.azurewebsites.net/";
const listPath = "TestApi/GetComplains";
const savePath = "TestApi/SaveComplain";

const complains = ref([]);
const title = ref("");
const body = ref("");
const isSaving = ref(false);
const errorMessage = ref("");

const isLoading = ref(false);

// 📥 Fetch complaints from the API
const fetchComplains = async () => {
  try {
    isLoading.value = true;
    const response = await fetch(`${baseUrl}${listPath}`);
    const data = await response.json();
    complains.value = data;
  } catch (e) {
    // 🚨 Error handling for fetch
    errorMessage.value = "Failed to load complaints.";
    console.error(e);
  } finally {
    isLoading.value = false;
  }
};

// 💾 Save a new complaint
const saveComplain = async () => {
  if (isSaving.value) return;
  errorMessage.value = ""; // Clear any previous error message
  try {
    isSaving.value = true;
    const response = await fetch(`${baseUrl}${savePath}`, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        Title: title.value,
        Body: body.value,
      }),
    });
    if (!response.ok) {
      throw new Error("Failed to save complaint.");
    }
    const data = await response.json();
    if (!data.Success) throw new Error("Failed to save complaint.");
    title.value = "";
    body.value = "";
    fetchComplains(); // 🔄 Refresh complaints list
  } catch (e) {
    // 🚨 Error handling for save
    errorMessage.value = "Failed to save complaint.";
    console.error(e);
  } finally {
    isSaving.value = false;
  }
};

// 🗓️ Fetch complaints on component mount
onMounted(() => {
  fetchComplains();
});
</script>

<style>
/* 🌈 General body styles for gradient background */
body {
  margin: 0;
  font-family: Arial, sans-serif;
  background-image: linear-gradient(
    to right top,
    #051937,
    #004d7a,
    #008793,
    #00bf72,
    #a8eb12
  ); /* Darker gradient background */
  min-height: 100vh;
  color: #f0f0f0;
  display: flex;
  justify-content: center;
}

h2 {
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
}

.wrapper {
  width: 100%;
  max-width: 600px;
  padding: 20px;
}

.complain-form {
  margin-bottom: 20px;
  background: rgba(255, 255, 255, 0.2); /* Semi-transparent background */
  border-radius: 25px;
  padding: 20px;
  backdrop-filter: blur(10px); /* Glass effect */
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
}

.complain-form input,
.complain-form textarea {
  width: 100%;
  margin-bottom: 10px;
  padding: 15px;
  border: 1px solid rgba(255, 255, 255, 0.3); /* Light border */
  border-radius: 25px; /* Fully rounded corners */
  background: rgba(255, 255, 255, 0.2);
  color: #fff; /* Light text color */
  font-size: 16px;
  font-family: Arial, sans-serif;
  box-sizing: border-box; /* Ensure padding is included in the element's width and height */
}

.complain-form textarea {
  height: 100px; /* Set a fixed height for the textarea */
}

.complain-form input:focus,
.complain-form textarea:focus {
  outline: none;
  border-color: #ffffff; /* Border color change on focus */
}

.complain-form input::placeholder,
.complain-form textarea::placeholder {
  color: rgba(
    255,
    255,
    255,
    0.6
  ); /* Light, semi-transparent text for placeholders */
  opacity: 1; /* Ensure placeholder is visible */
}

.complain-form button {
  width: 100%;
  padding: 16px;
  border: none;
  border-radius: 100px;
  background: linear-gradient(135deg, #ff5f6d, #ffc371); /* Gradient button */
  color: #fff;
  font-weight: bold;
  font-size: 16px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.complain-form button:hover {
  box-shadow: 0 4px 10px rgba(255, 255, 255, 0.2);
  transform: translateY(-2px);
}

.error-message {
  color: #ff6f6f;
  margin-top: 10px;
}

.complain-item {
  background: rgba(255, 255, 255, 0.2); /* Glass effect for cards */
  border-radius: 30px;
  padding: 20px 25px;
  margin-bottom: 10px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
  border: 1px solid rgba(255, 255, 255, 0.3);
  transition: all 0.3s ease;
}

.complain-item:hover {
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.4);
  transform: translateY(-2px);
  cursor: pointer;
}

.complain-item h3 {
  margin-top: 0;
}

.loader {
  border: 5px solid rgba(255, 255, 255, 0.2);
  border-radius: 50%;
  border-top: 5px solid #fff;
  width: 30px;
  height: 30px;
  animation: spin 1s linear infinite;
  margin: 10px auto;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>
