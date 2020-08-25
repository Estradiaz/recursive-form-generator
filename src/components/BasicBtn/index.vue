<template>
    <div>
        <input type="text" v-on="on"/>
    </div>
</template>

<script lang="ts">
import { Component, Mixins } from 'vue-property-decorator'

@Component<BasicBtn>({

})
export default class BasicBtn extends Mixins (){

    on = {
        input: (event: Event) => this.$emit('input', event && event.target && (event.target as HTMLInputElement).value || undefined)
    }
}
</script>