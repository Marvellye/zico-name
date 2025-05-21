<template>
  <div class="min-h-screen flex flex-col items-center justify-center px-4 py-12">
    <div class="glass p-8 md:p-12 max-w-2xl w-full text-center">
      
      <div v-if="!submitted">
        <h1 class="text-4xl md:text-6xl font-bold mb-4 text-white">
          Join <span class="text-highlight">Us</span>
        </h1>
        
        <p class="text-lg md:text-xl mb-8 text-white">
          We're building something exciting! Fill out the form below to get involved and stay updated.
        </p>
        
        <form @submit.prevent="handleSubmit" class="space-y-6 text-left">
          <div>
            <label for="name" class="block text-sm font-medium text-white mb-1">Full Name</label>
            <input 
              type="text" 
              id="name"
              v-model="formData.name"
              placeholder="Enter your name"
              required
              class="w-full bg-secondary bg-opacity-50 border border-white/10 rounded-lg px-4 py-2 text-white focus:outline-none focus:ring-2 focus:ring-highlight"
            />
          </div>
          
          <div>
            <label for="referralPlatform" class="block text-sm font-medium text-white mb-1">Your Email</label>
            <input 
              type="email" 
              id="email"
              v-model="formData.email"
              placeholder="myname@gmail.com"
              required
              class="w-full bg-secondary bg-opacity-50 border border-white/10 rounded-lg px-4 py-2 text-white focus:outline-none focus:ring-2 focus:ring-highlight"
            />
          </div>
          
          <div>
            <label for="referralPlatform" class="block text-sm font-medium text-white mb-1">Which platform are you coming from?</label>
            <input 
              type="text" 
              id="referralPlatform"
              v-model="formData.referralPlatform"
              placeholder="e.g., Facebook, TikTok, Instagram, a Friend"
              required
              class="w-full bg-secondary bg-opacity-50 border border-white/10 rounded-lg px-4 py-2 text-white focus:outline-none focus:ring-2 focus:ring-highlight"
            />
          </div>
          
          <div>
            <label for="fileUpload" class="block text-sm font-medium text-white mb-1">Upload your video file </label>
            <input 
              type="file" 
              id="fileUpload"
              @change="handleFileUpload"
              required
              class="block w-full text-sm text-white/80
                     file:mr-4 file:py-2 file:px-4
                     file:rounded-lg file:border-0
                     file:text-sm file:font-semibold
                     file:bg-highlight file:text-white
                     hover:file:bg-highlight/80
                     bg-secondary bg-opacity-50 border border-white/10 rounded-lg
                     focus:outline-none focus:ring-2 focus:ring-highlight cursor-pointer
                     pr-4"
            />
            <p v-if="selectedFileName" class="text-xs text-white/70 mt-1">
              Selected file: {{ selectedFileName }}
            </p>
          </div>
          
          <button 
            type="submit"
            class="w-full bg-highlight hover:bg-highlight/80 text-white font-medium px-6 py-3 rounded-lg transition-colors text-lg"
          >
            Submit
          </button>
        </form>
      </div>
      
      <div v-else>
        <h1 class="text-4xl md:text-6xl font-bold mb-4 text-white">
          Thank <span class="text-highlight">You!</span>
        </h1>
        <p class="text-lg md:text-xl mb-8 text-white">
          Your information has been submitted. We're excited to have you with us! 
        </p>
        <button 
          @click="redirectToCommunity"
          class="bg-highlight hover:bg-highlight/80 text-white font-medium px-8 py-3 rounded-lg transition-colors text-lg"
        >
          Join Our Community
        </button>
      </div>

    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const formData = ref({
  name: '',
  referralPlatform: '', // Changed from 'location'
  file: null,
});

const submitted = ref(false);
const selectedFileName = ref('');

// Community URL - REPLACE THIS WITH YOUR ACTUAL COMMUNITY URL
const communityUrl = 'https://discord.gg/example'; // Example URL

const handleFileUpload = (event) => {
  const file = event.target.files[0];
  if (file) {
    formData.value.file = file;
    selectedFileName.value = file.name;
  } else {
    formData.value.file = null;
    selectedFileName.value = '';
  }
};

const handleSubmit = async () => {
  // In a real application, you would send the formData to a server.
  const dataToLog = { ...formData.value };
  if (dataToLog.file) {
    dataToLog.file = dataToLog.file.name; 
  }
  console.log('Form data submitted:', dataToLog);

  // Example:
  // const submissionPackage = new FormData();
  // Object.keys(formData.value).forEach(key => {
  //   if (formData.value[key] !== null) {
  //     submissionPackage.append(key, formData.value[key]);
  //   }
  // });
  // try {
  //   const response = await fetch('/api/your-endpoint', {
  //     method: 'POST',
  //     body: submissionPackage,
  //   });
  //   if (response.ok) {
  //     submitted.value = true;
  //   } else {
  //     console.error('Submission failed:', await response.text());
  //     alert('There was an error submitting your form. Please try again.');
  //   }
  // } catch (error) {
  //   console.error('Network error:', error);
  //   alert('A network error occurred. Please try again.');
  // }

  submitted.value = true;
  // Reset form fields after successful submission if not redirecting immediately
  // formData.value = { name: '', referralPlatform: '', file: null };
  // selectedFileName.value = '';
};

const redirectToCommunity = () => {
  window.location.href = communityUrl;
};
</script>

<style scoped>
/* Styles are inherited or defined in your global CSS / Tailwind config */
</style>
