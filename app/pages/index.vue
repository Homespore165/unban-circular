<template>
  <div>
    <UPageHero
      title="Unban Circular"
      description="A multi-tool website for duelists"
    />
    <UPageList class="lg:mx-50 mx-25">
      <UPageCard
        description="Convert your YDK to a JSON importable on official platforms"
        to="./ydk2json"
      >
        <template #title>
          YDK to JSON
          <UIcon
            name="simple-icons:convertio"
            class="ml-2"
          />
        </template>
      </UPageCard>
    </UPageList>
  </div>
</template>
