<template>
  <div class="section">
    <div class="container">
      <div class="columns is-centered">
        <div class="column is-three-quarters-tablet">
          <div class="field has-addons has-addons-centered">
            <div class="control">
              <div class="select is-large">
                <select v-model="selected">
                  <option
                    v-for="(option, i) in options"
                    :key="i"
                  >
                    {{ option }}
                  </option>
                </select>
              </div>
            </div>
            <div class="control is-expanded">
              <input
                class="input is-large"
                type="text"
                v-model="inputContent"
                placeholder="Message"
              >
            </div>
            <div class="control">
              <button
                :class="btnClass"
                @click="encrypt"
              >
                Encrypt
              </button>
            </div>
          </div>
        </div>
      </div>
      <div class="columns is-centered">
        <div class="column is-three-quarters-tablet">
          <textarea
            class="textarea"
            readonly
            v-model="encryptedMsg"
          ></textarea>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import axios from 'axios';
import { defineComponent, ref, computed } from 'vue';

export default defineComponent({
  name: 'App',
  setup() {
    const inputContent = ref('');
    const encryptedMsg = ref('');
    const selected = ref('MD4');
    const isLoading = ref(false);
    const options = ref([
      'MD4',
      'MD5',
      'SHA1',
      'SHA256',
      'SHA384',
      'SHA512',
    ]);

    const encrypt = async () => {
      const url = `https://api.hashify.net/hash/${selected.value.toLowerCase()}/hex`;
      try {
        isLoading.value = true;
        const res = await axios.post(url, inputContent.value);
        if (res.data.Digest) {
          encryptedMsg.value = res.data.Digest;
        } else {
          console.log('Invalid input');
        }
      } catch (err) {
        console.log(err);
      }
      isLoading.value = false;
    };

    const btnClass = computed(() => ({
      button: true,
      'is-primary': true,
      'is-large': true,
      'is-loading': isLoading.value,
    }));

    return {
      inputContent,
      encryptedMsg,
      encrypt,
      btnClass,
      options,
      selected,
    };
  },
});
</script>
