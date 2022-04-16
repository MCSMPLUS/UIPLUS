<template>

  <div class="system-icon-container">

    <div class="system-icon" :class="platformClass" :style="`background-image: url(${path})`">
    </div>
    <slot/>

  </div>

</template>

<script setup>
import { defineProps, ref, onMounted } from 'vue'

const props = defineProps({

  platform: String

})

const platformClass = ref('unknown')
const path = ref('https://www.logo.wine/a/logo/Windows_3.1x/Windows_3.1x-Logo.wine.svg')

onMounted(() => {

  const value = (props.platform + '').toLowerCase()

  const platforms = [ { key: 'windows 7', name: 'windows-7', path: 'https://www.logo.wine/a/logo/Windows_7/Windows_7-Logo.wine.svg' },
    { key: 'windows 10 home china', name: 'windows-11' },
    { key: 'windows', name: 'windows-10', path: 'https://www.logo.wine/a/logo/Windows_10/Windows_10-Logo.wine.svg' },
    { key: 'centos', name: 'centos', path: 'https://www.logo.wine/a/logo/CentOS/CentOS-Logo.wine.svg' },
    { key: 'ubuntu', name: 'ubuntu', path: 'https://cdn.worldvectorlogo.com/logos/ubuntu-4.svg' },
    { key: 'debian', name: 'debian', path: 'https://cdn.worldvectorlogo.com/logos/debian-2.svg' },
    { key:  'macos', name: 'apple-macos', path: 'https://cdn.worldvectorlogo.com/logos/mac-os-2.svg' },

  ]

  let complete = false;

  platforms.forEach(item => {

    if( complete ) return;

    if ((value + '').indexOf(item.key) !== -1) {

      complete = true;
      platformClass.value = item.name

      if( item.path ) {

        path.value = item.path

      }

    }

  })

})

</script>

<style scoped>

.system-icon-container {

  display: flex;
  margin: 14px 0;

  min-width: 512px;

}

.system-icon {

  position: relative;
  display: inline-block;

  width: 2em;
  height: 2em;

  background-size: contain;
  background-repeat: no-repeat;

  transform: translate(0, -0.1em);

}

.windows-10 {

  left: -2px;

  background-size: 350%;
  background-position: -.1em -1.65em;

}

.windows-11 {

  left: 3px;
  margin-right: 7px;

  width: 1.13em;
  height: 1.13em;

  background-image: linear-gradient(to bottom right, #65c4dc, #0c8ce6) !important;
  border-radius: 2px;

  transform: translate(0, 0);

}
.windows-11::before {

  content: '';
  position: absolute;
  display: inline-block;

  width: 1px;
  height: 100%;

  left: 50%;

  background-color: white;
  transform: translate(-50%, 0);

}
.windows-11::after {

  content: '';
  position: absolute;
  display: inline-block;

  width: 100%;
  height: 1px;

  top: 50%;

  background-color: white;
  transform: translate(0, -50%);

}

.apple-macos {

  background-size: 200%;
  background-position: -1em -.4em;

  transform: scale(.75) translate(0, -.5em);

}

.ubuntu, .debian {

  left: 3px;

  margin-right: 7px;

  width: 1.25em;
  height: 1.25em;

}

@keyframes unknown-frame {

  0%, 95% {

    opacity: 1;
    transform: rotateY(0);

  }

  100% {

    opacity: .7;
    transform: rotateY(360deg);

  }

}

.unknown {

  left: 5px;

  margin-right: 5px;

  animation: unknown-frame 15s linear infinite;

}

.unknown::before {

  content: '?';
  position: absolute;

  left: 2px;
  bottom: 6px;

  color: #a60303;

  transform: scale(.95);

}

</style>
