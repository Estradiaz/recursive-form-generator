<script lang="ts">

import { Component, Mixins, Prop } from "vue-property-decorator"
import { VNode } from 'vue/types/umd'
import BaseInput from "../BasicBtn/index.vue"

export interface Form {
    component: string
    model: string
}
export type FromTree = Array<Form | Schema>
export interface Schema {
    name?: string
    children: FromTree
}
function isSchema(item: Schema | Form) {
    if(item.hasOwnProperty("children")){
        return true
    } else {
        return false
    }
}

@Component<FormGroup>({
    name: "FormGroup",
    components: {
        BaseInput
    },
    render(h){
        const children : VNode[] = [];
        if(this.schema.hasOwnProperty("children")){
            children.push(...(this.schema as Schema).children.map(
                child => {
                    if(isSchema(child)){
                        return this.createFormGroupNode(child as Schema)
                    } else {
                        return this.createFormNode(child as Form)
                    }
                }
            ))
        }
        console.log(children, this.schema)
        return h(
            "div",
            {},
            children
        )
    }
})
export default class FormGroup extends Mixins (){
    @Prop(Object) readonly schema!: Schema | Form
    createFormGroupNode(child: Schema){

        return this.$createElement(
            "FormGroup",
            {
                props: {
                    schema: child,
                }, 
                on: this.$listeners
            }
        )
    }
    createFormNode(child: Form){
        return this.$createElement(
            child.component,
            {
                attrs: {...child},
                key: child.model,
                on: {
                    ...this.$listeners,
                    input: (value: string) => {
                        const input = this.$listeners.input;
                        console.log(input)
                        Array.isArray(input)
                        ? input.forEach(fn => fn({key: child.model, value}))
                        : input({key: child.model, value})
                    }
                }
            }
        )
    }
    
}

</script>


<docs>
```js
new Vue({
    template: `
        <FormGroup :schema="schema" @input="update"/>
    `
    ,methods: {
        update(val){
            console.log("input", val)
        }
    }
    ,data(){
        return {
            schema: {
        "name": "section 1",
        "children": [
            {
                "component": "BaseInput",
                "model": "firstName"
            },
            {
                "component": "BaseInput",
                "model": "lastName"
            },
            {
                "children": [
                    {
                        "component": "BaseInput",
                        "model": "email"
                    },
                    {
                        "children": [
                            {
                                "component": "BaseInput",
                                "model": "website"
                            }
                        ]
                    }
                ]
            }
        ]
    }
        }
    }
})
```
</docs>
