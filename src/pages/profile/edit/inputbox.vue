<template>
    <a-form @submit="handleSubmit" :form="form" class="form">
        <a-row class="form-row">
            <a-col :lg="18" :md="18" :sm="24">

                <a-form-item>
                    <a-row class="form-row">
                        <a-col :lg="24" :md="24" :sm="24">
                            <a-form-item :label="$t('name')">
                                <a-input :value="data.name" name="name" :placeholder="$ta('name')" @change="onChanged" />
                            </a-form-item>
                        </a-col>
                        <a-col :lg="24" :md="24" :sm="24">
                            <a-form-item :label="$t('email')">
                                <a-input :value="data.email" name="email" :placeholder="$ta('email')" @change="onChanged" />
                            </a-form-item>
                        </a-col>
                        <a-col :lg="24" :md="24" :sm="24">
                            <a-form-item :label="$t('phone')">
                                <a-input :value="data.phone" name="phone" :placeholder="$ta('phone')" @change="onChanged" />
                            </a-form-item>
                        </a-col>
                        <a-col :lg="24" :md="24" :sm="24">
                            <a-form-item :label="$t('password')">
                                <a-input :value="data.password" name="password" :placeholder="$ta('password')"
                                    @change="onChanged" />
                            </a-form-item>
                        </a-col>
                        <a-col :lg="24" :md="24" :sm="24">
                            <a-form-item :label="$t('gender')">
                                <a-select @change="changeGender" name="gender" :value="data.gender" style="width: 120px"
                                    ref="select">
                                    <a-select-option value="female">Female</a-select-option>
                                    <a-select-option value="male">Male</a-select-option>

                                </a-select>
                            </a-form-item>
                        </a-col>
                        <a-col :lg="24" :md="24" :sm="24">
                            <a-form-item :label="$t('bank_name_account')">
                                <a-input :value="data.bank_name_account" name="bank_name_account"
                                    :placeholder="$ta('bank_name_account')" @change="onChanged" />
                            </a-form-item>
                        </a-col>
                        <a-col :lg="24" :md="24" :sm="24">
                            <a-form-item :label="$t('bank_name')">
                                <a-input :value="data.bank_name" name="bank_name" :placeholder="$ta('bank_name')"
                                    @change="onChanged" />
                            </a-form-item>
                        </a-col>
                        <a-col :lg="24" :md="24" :sm="24">
                            <a-form-item :label="$t('bank_account')">
                                <a-input :value="data.bank_account" name="bank_account" :placeholder="$ta('bank_account')"
                                    @change="onChanged" />
                            </a-form-item>
                        </a-col>
                        <a-col :lg="24" :md="24" :sm="24">
                            <a-form-item :label="$t('referral_code')">
                                <a-input :value="data.referral_code" name="referral_code"
                                    :placeholder="$ta('referral_code')" @change="onChanged" v-if="!data.id" />
                                <span v-if="data.id">{{ data.referral_code }}</span>
                            </a-form-item>
                        </a-col>

                    </a-row>

                </a-form-item>
            </a-col>


        </a-row>

        <a-form-item v-if="showSubmit">
            <a-button htmlType="submit" @click="send">Submit</a-button>
        </a-form-item>
    </a-form>
</template>

<script>
import { METHOD, request } from '../../../utils/request';

export default {
    name: 'inputBox',
    props: ['showSubmit', "langData", "type"],
    i18n: require('./i18n-inputBox.js'),
    data() {
        const data = { ... this.langData };
        Object.keys(data).forEach(key => {
            if (this.isJson(data[key])) {
                data[key] = JSON.parse(data[key])
                if (data[key][this.type.toLowerCase()]) {
                    data[key] = data[key][this.type.toLowerCase()]
                }
            }
        });
        return {
            data,
            form: this.$form.createForm(this)
        }
    },
    watch: {
        langData: {
            handler: function (val) {
                this.data = val;
                console.log(this.data)
            },
            deep: true
        }
    },
    methods: {
        changeGender(value) {
            this.data.gender = value;
        },
        isJson(item) {
            let value = typeof item !== "string" ? JSON.stringify(item) : item;
            try {
                value = JSON.parse(value);
            } catch (e) {
                return false;
            }

            return typeof value === "object" && value !== null;
        },
        handleSubmit(e) {
            e.preventDefault()
            this.form.validateFields((err, values) => {
                if (!err) {
                    console.log('Received values of form: ', values)
                }
            })
        },
        send() {
            const data = {
                ...this.data,
            }
            
            request(
                process.env.VUE_APP_API_BASE_URL + '/agency',
                METHOD.POST,
                data).then(() => {
                    this.data = {}
                    this.$message.success(`Update successfully`);
                })
        },

        setNestedProperty(event) {
            // Split the property path by dots to get an array of property names
            const properties = event.target.name.split(".");

            // Loop through the properties to access the nested property
            let nestedObj = this.data;
            for (let i = 0; i < properties.length - 1; i++) {
                const property = properties[i];
                nestedObj = nestedObj[property];
            }

            // Set the value of the final property
            nestedObj[properties[properties.length - 1]] = event.target.value;
        },
        setNestedPropertySelect(event, op) {
            // Split the property path by dots to get an array of property names
            const properties = op.data.attrs.dropdownClassName.split(".");

            // Loop through the properties to access the nested property
            let nestedObj = this.data;
            for (let i = 0; i < properties.length - 1; i++) {
                const property = properties[i];
                nestedObj = nestedObj[property];
            }

            // Set the value of the final property
            nestedObj[properties[properties.length - 1]] = event;
        },

        onChanged(event) {
            console.log(event);
            const fieldName = event.target.name;
            const value = event.target.value;
            this.data[fieldName] = value;
        },
        onChangedKey(event) {

            const _value = event.target.name;
            const newKeys = event.target.value;
            const oldKey = Object.keys(this.value).find(key => this.value[key] === _value);
            delete this.value[oldKey];
            this.value[newKeys] = _value;
        },
        onchangeValue(event) {

            const fieldName = event.target.name;
            const _value = event.target.value;
            this.value[fieldName] = _value;
        },
        onChangedSelect(event, op) {
            console.log(event);
            console.log(op);
            const fieldName = op.data.attrs.dropdownClassName;
            const value = event;
            this.data[fieldName] = value;
        },
    },
}
</script>

<style lang="less" scoped>
.form {
    .form-row {
        margin: 0 -8px
    }

    .ant-col-md-12,
    .ant-col-sm-24,
    .ant-col-lg-6,
    .ant-col-lg-8,
    .ant-col-lg-10,
    .ant-col-xl-8,
    .ant-col-xl-6 {
        padding: 0 8px
    }
}
</style>
