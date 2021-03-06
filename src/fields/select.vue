<template>
    <select
        :class="schema.classes"
        :id="schema.id"
        :autofocus="schema.autofocus"
        :disabled="schema.disabled"
        :form="schema.form"
        :multiple="schema.multiple"
        :name="schema.name"
        :required="schema.required"
        :size="schema.size"

        v-bind="schema.attrs"
        v-model="value"
        v-on="schema.events"

        @blur="onEvent"
        @change="onEvent"
        @focus="onEvent"
    >
        <option
            v-if="!config.noneSelectedText.hide"

            :class="config.noneSelectedText.classes"
            :disabled="config.noneSelectedText.disabled"
            :value="config.noneSelectedText.value"

            v-bind="config.noneSelectedText.attrs"
            v-text="config.noneSelectedText.name"
        />
        <template v-for="(item, index) in items">
            <optgroup
                v-if="item.options"

                :class="item.classes"
                :disabled="item.disabled"
                :key="'optgroup'+index"
                :label="item.name"

                v-bind="item.attrs"
            >
                <option
                    v-for="(option, key) in item.options"

                    :class="option.classes"
                    :disabled="option.disabled"
                    :key="'optgroup'+index+'option'+key"
                    :value="option.value"

                    v-bind="option.attrs"
                    v-text="option.name"
                />
            </optgroup>
            <option
                v-else

                :class="item.classes"
                :disabled="item.disabled"
                :key="'option'+index"
                :value="item.value"

                v-bind="item.attrs"
                v-text="item.name"
            />
        </template>
    </select>
</template>

<script>
    import isFunction from 'lodash/isFunction';
    import isObject from 'lodash/isObject';
    import merge from 'lodash/merge';

    import base from './base';

    export default {
        mixins: [base],

        computed: {
            config() {
                return merge({
                    optionsKey: {
                        value: 'id',
                        name: 'name'
                    },
                    noneSelectedText: {
                        disabled: this.schema.required,
                        value: null,
                        name: '---'
                    }
                }, this.schema.config);
            },

            items() {
                const result = [];
                let items = this.schema.items;

                if (isFunction(items)) {
                    items = items.call(this);
                }

                items = Array.isArray(items) ? items : [];

                items.forEach(item => {
                    if (isObject(item)) {
                        const { name, value } = merge(this.config.optionsKey, {});
                        let options = null;

                        if (Array.isArray(item.options)) {
                            options = [];

                            item.options.forEach(option => {
                                if (isObject(option)) {
                                    options.push({
                                        name: option[name],
                                        value: option[value],
                                        attrs: option.attrs,
                                        classes: option.classes,
                                        disabled: option.disabled
                                    });
                                } else {
                                    options.push({
                                        name: option,
                                        value: option
                                    });
                                }
                            });
                        }

                        result.push({
                            name: item[name],
                            value: item[value],
                            attrs: item.attrs,
                            classes: item.classes,
                            disabled: item.disabled,
                            options
                        });
                    } else {
                        result.push({
                            name: item,
                            value: item
                        });
                    }
                });

                return result;
            }
        }
    };
</script>
