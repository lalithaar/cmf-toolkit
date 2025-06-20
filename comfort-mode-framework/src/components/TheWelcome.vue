<script setup>
import WelcomeItem from './WelcomeItem.vue'
import DocumentationIcon from './icons/IconDocumentation.vue'
import ToolingIcon from './icons/IconTooling.vue'
import InputForm from './InputForm.vue'
</script>

<template>


<InputForm />

  <WelcomeItem>
    <template #icon>
      <DocumentationIcon />
    </template>
    <template #heading>Documentation</template>
    <p>
    Our
    <RouterLink to="/info">official documentation</RouterLink>
     provides you with detailed information of calcuations and scientific backing for those who are interested</p>
  </WelcomeItem>  

</template>
