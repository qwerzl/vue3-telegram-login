<template>
  <div ref="telegram"></div>
</template>
<script lang="ts" setup>
  import { ref, onMounted } from 'vue';
  const props = defineProps({
    mode: {
      type: String,
      required: true,
      validator(value: string) {
        return ['callback', 'redirect'].includes(value);
      },
    },
    telegramLogin: {
      type: String,
      required: true,
      validator(value: string) {
        return value.endsWith('bot') || value.endsWith('Bot');
      },
    },
    redirectUrl: {
      type: String,
      default: '',
    },
    requestAccess: {
      type: String,
      default: 'read',
      validator(value: string) {
        return ['read', 'write'].includes(value);
      },
    },
    size: {
      type: String,
      default: 'large',
      validator(value: string) {
        return ['small', 'medium', 'large'].includes(value);
      },
    },
    userpic: {
      type: String,
      default: 'true',
    },
    radius: {
      type: String,
    },
  });
  const emit = defineEmits(['callback']);
  const onTelegramAuth = (user) => {
    console.log('user-----', user);
    emit('callback', user);
  };
  const telegram = ref();
  onMounted(() => {
    // create script with given params
    const script = document.createElement('script');
    script.async = true;
    script.src = 'https://telegram.org/js/telegram-widget.js?3';

    script.setAttribute('data-size', props.size);
    script.setAttribute('data-userpic', props.userpic);
    script.setAttribute('data-telegram-login', props.telegramLogin);
    script.setAttribute('data-request-access', props.requestAccess);
    if (props.radius) {
      script.setAttribute('data-radius', props.radius);
    }

    if (props.mode === 'callback') {
      window.onTelegramAuth = onTelegramAuth;
      script.setAttribute('data-onauth', 'window.onTelegramAuth(user)');
    } else {
      script.setAttribute('data-auth-url', props.redirectUrl);
    }
    telegram.value.appendChild(script);
  });
</script>
