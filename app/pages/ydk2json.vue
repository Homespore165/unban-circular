<script setup lang="ts">
import appConfig from '~/app.config'

interface DeckJSON {
  name: string
  main: string[]
  side: string[]
  extra: string[]
}

interface DeckJSONOfficial {
  Name: string
  Monsters: Array<Map<string, number>>
  Spells: Array<Map<string, number>>
  Traps: Array<Map<string, number>>
  Side: Array<Map<string, number>>
  Extra: Array<Map<string, number>>
}

const file = ref<File | null>(null)
const json = ref<DeckJSONOfficial | null>(null)
const converted = computed(() => {
  if (json.value != null) {
    return JSON.stringify(json.value)
  } else {
    return null
  }
})

async function onConvert() {
  if (!file.value) {
    alert('Please choose a file first')
    return
  }

  const name = file.value.name.replace(/\.[^.]*$/, '')
  const text = await file.value.text()
  const lines = text.split(/\r?\n/)
  const mainLine = lines.findIndex(line => line === '#main')
  const extraLine = lines.findIndex(line => line === '#extra')
  const sideLine = lines.findIndex(line => line === '!side')
  const main = lines.slice(mainLine + 1, extraLine)
  const extra = lines.slice(extraLine + 1, sideLine)
  const side = lines.slice(sideLine + 1, lines.length - 1)

  const deck: DeckJSON = { name, main, side, extra }

  const response = await $fetch(`${appConfig.api.ydk2json}/api/ydk2json`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: deck
  })
  json.value = response as DeckJSONOfficial
  console.log(response)
}

function onSave() {
  if (!json.value) {
    alert('Please convert a file first')
    return
  }

  const blob = new Blob([JSON.stringify(json.value)], { type: 'application/json' })
  const url = URL.createObjectURL(blob)
  const link = document.createElement('a')
  link.href = url
  link.download = `${json.value!.Name}.json`
  link.click()
  URL.revokeObjectURL(url)
}
</script>

<template>
  <UContainer class="flex flex-col items-center gap-4 py-10">
    <UFileUpload
      v-model="file"
      label="Upload your YDK file"
      accept=".ydk"
      class="w-96 min-h-48"
    />
    <UButton @click="onConvert">
      Convert
      <UIcon
        name="simple-icons:convertio"
      />
    </UButton>

    <UTextarea
      ref="output"
      v-model="converted"
      label="Output JSON"
      placeholder="Your converted JSON will appear here"
      class="w-96"
      autoresize
      readonly
    />

    <UButton @click="onSave">
      Save JSON
      <UIcon
        name="material-symbols:download-for-offline"
      />
    </UButton>
  </UContainer>
</template>

<style scoped>

</style>
