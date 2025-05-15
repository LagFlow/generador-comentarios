<script setup lang="ts">
  import { ref, reactive, watch } from 'vue';

  const discord = ref('...');
  const redmine = ref('...');
  const copiedDiscord = ref(false);
  const copiedRedmine = ref(false);

  const redmineData = reactive({});
  const discordData = reactive({});

  function onTaskChange(e: input) {
    discordData.task = e.target.value;
  }

  function onCommitMessageChange(e: input) {
    discordData.commitMessage = e.target.value;
  }

  function onCommitHashChange(e: input) {
    discordData.commitHash = e.target.value;
    redmineData.commitHash = e.target.value;
  }

  function onBackOrFrontChange(e: change) {
    discordData.side = e.target.value;
    redmineData.side = e.target.value;
  }

  function onTaskOrBugChange(e: change) {
    discordData.type = e.target.value;
  }

  async function copy(message, which) {
    await navigator.clipboard.writeText(message);
    switch (which) {
    case 'discord':
      copiedDiscord.value = true;
      setTimeout(() => {
        copiedDiscord.value = false;
      }, 2000);
      break;
    case 'redmine':
      copiedRedmine.value = true;
      setTimeout(() => {
        copiedRedmine.value = false;
      }, 2000);
      break;
    }
  }

  function onReset() {
    window.location.reload();
  }

  watch(discordData, (newData) => {
    discord.value = `Push to dev [AMG-Facturador-${newData.side}] ${newData.type} #${newData.task} - ${newData.commitMessage}\nCommit: ${newData.commitHash}`;
  });

  watch(redmineData, (newData) => {
    redmine.value = `Commit ${newData.side}: ${newData.commitHash}`;
  });

</script>

<template>
  <div class="container">
    <h1>Generador de comentario para Redmine y Discord:</h1>
    <div class="form">
      <label>
        Tarea #:
        <input type="text" name="Task" @input="onTaskChange" />
      </label>
      <label>
        Mensaje del commit:
        <input type="text" name="CommitMessage" @input="onCommitMessageChange" />
      </label>
      <label>
        Hash del commit:
        <input type="text" name="CommitHash" @input="onCommitHashChange"/>
      </label>
      <label>
        Back o Front?
        <label>Back<input type="radio" value="Back" name="BackOrFront" @change="onBackOrFrontChange" /></label>
        <label>Front<input type="radio" value="Front" name="BackOrFront" @change="onBackOrFrontChange" /></label>
      </label>
      <label>
        Task o bug?
        <label>Task<input type="radio" value="Task" name="TaskOrBug" @change="onTaskOrBugChange" /></label>
        <label>Bug<input type="radio" value="Bug" name="TaskOrBug" @change="onTaskOrBugChange" /></label>
      </label>

      <div class="generado" :class="{'copied-effect': copiedDiscord}" @click="copy(discord, 'discord')">
        <div>Discord:</div>
        <div>{{discord}}</div>
      </div>
      <div class="generado" :class="{'copied-effect': copiedRedmine}" @click="copy(redmine, 'redmine')">
        <div>Redmine:</div>
        <div>{{redmine}}</div>
      </div>
      <button type="button" @click="onReset">Reset</button>
    </div>
  </div>
</template>

<style scoped>
  .container {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }

  .form {
    padding: 20px;
    background-color: #333;
    border-radius: 0.4rem;
    display: flex;
    flex-direction: column;
    width: 630px;
  }

  label {
    margin: 0.5rem 0;
  }

  label>label {
    display: block;
    margin-left: 20px;
  }

  label>label>input {
    margin-left: 5px;
  }

  .generado {
    padding: 25px;
    border: 1px solid #fafafa;
    border-radius: 1rem;
  }

  .generado>div {
    white-space: pre-line;
  }

  .generado:last-of-type {
    margin-top: 10px;
    margin-bottom: 20px;
  }

  .copied-effect {
    border-color:#4CAF50;
  }
</style>
