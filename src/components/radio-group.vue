<template>
    <div class="grid">
        <div v-for="n in options" :key="n">
            <input
                :id="n"
                v-model="selected"
                :name="property"
                type="radio"
                :value="n"
                :checked="storedValue == n"
            />
            <div class="column" role="“radiogroup”">
                <label
                    :for="n"
                    :class="n"
                    tabindex="0"
                    role="“radio”"
                    aria-checked="selected == n ? 'true' : 'false'"
                    @keyup.enter="selected = n"
                    @keyup.space="selected = n"
                >
                    <div
                        class="dot"
                        :class="selected == n ? 'filled' : null"
                        :alt="selected == n ? 'selected' : 'not-selected'"
                    />
                    <slot :name="n" />
                </label>
                <div :class="selected == n ? text.label : text.base">
                    {{ n }}
                </div>
            </div>
        </div>
    </div>
</template>
<script>
import { ref, watch, computed } from 'vue'
import { useStore } from 'vuex'
import text from './text.module.css'
export default {
    props: {
        property: { type: String, required: true },
        options: { type: Array, required: true },
    },
    setup(props) {
        let selected = ref('')
        let store = useStore()
        let storedValue = ref('')
        let storeInitialized = computed(() => store.state.init)

        watch(storeInitialized, () => {
            storedValue.value = store.state[props.property]
            selected.value = store.state[props.property]
        })

        watch(selected, () => {
            store.commit('update', {
                key: props.property,
                value: selected.value,
            })
        })

        return { selected, storedValue, text }
    },
}
</script>

<style scoped>
input {
    position: absolute;
    visibility: hidden;
    opacity: 0;
    width: 1px;
    height: 1px;
    left: -1;
    z-index: -1;
}

label {
    position: relative;
    width: 96px;
    height: 124px;
    border: var(--border);
    border-radius: var(--rounded);
    background: var(--theme-bg-surface);
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
}

.dot {
    position: absolute;
    top: 8px;
    left: 8px;
    height: 16px;
    width: 16px;
    border: var(--border);
    border-radius: var(--rounded-full);
    background: var(--theme-bg-surface);
    cursor: pointer;
}

.filled {
    background: var(--theme-accent);
}

.grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(96px, max-content));
    justify-content: center;
    grid-gap: var(--space-sm);
    padding: 6px 0px;
}

input:focus + label,
input:active + label {
    border: var(--border);
}
</style>
