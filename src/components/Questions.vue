<template>
    <div>
        <div>
            <el-button @click="dialogVisible = true" icon="el-icon-plus" style="margin-bottom: 10px;" type="primary">Add
                question
            </el-button>
        </div>
        <el-table
                border
                :data="questions"
                :row-class-name="tableRowClassName"
                style="width: 100%">
            <el-table-column
                    align="center"
                    header-align="center"
                    prop="question"
                    label="Question">
            </el-table-column>
            <el-table-column
                    align="center"
                    header-align="center"
                    label="Status"
                    width="180">
                <template slot-scope="scope">
                    <el-tag v-if="scope.row.status" type="success">Active</el-tag>
                    <el-tag v-else type="danger">No active</el-tag>
                </template>
            </el-table-column>
            <el-table-column
                    align="center"
                    header-align="center"
                    prop="date"
                    label="Date"
                    width="180">
            </el-table-column>
            <el-table-column
                    align="center"
                    header-align="center"
                    label="Notifications">
                <template slot-scope="scope">
                    <el-tag>{{scope.row.notifications}}</el-tag>
                </template>
            </el-table-column>
            <el-table-column
                    align="center"
                    header-align="center"
                    label="Operations">
                <template slot-scope="scope">
                    <el-button
                            :disabled="!scope.row.status"
                            size="mini">View
                    </el-button>
                    <el-button
                            :disabled="!scope.row.status"
                            size="mini"
                            type="danger"
                            @click="closeQuestion(scope.$index)">Close question
                    </el-button>
                </template>
            </el-table-column>
        </el-table>
        <el-dialog
                title="Питання"
                :visible.sync="dialogVisible"
                width="30%"
                center>
            <div>
                <el-form :model="formAsk" :rules="rules" ref="formAskRules" label-width="120px" class="demo-ruleForm">
                    <el-form-item label="Name" prop="name">
                        <el-input v-model="formAsk.name"/>
                    </el-form-item>
                    <el-form-item label="Email" prop="email">
                        <el-input v-model="formAsk.email"/>
                    </el-form-item>
                    <el-form-item :class="phoneErr && 'is-error'" label="Phone">
                        <el-row type="flex">
                            <el-col :span="12">
                                <el-select v-model="formAsk.codePhone" placeholder="Select">
                                    <el-option
                                            v-for="item in codePhone"
                                            :key="item"
                                            :label="item"
                                            :value="item">
                                    </el-option>
                                </el-select>
                            </el-col>
                            <el-col :span="12">
                                <masked-input v-model="formAsk.phone" mask="(111) 111-11-11" class="el-input__inner"
                                              placeholder="Phone"/>
                                <div v-if="phoneErr" class="el-form-item__error">
                                    Please input correct phone
                                </div>
                            </el-col>
                        </el-row>
                    </el-form-item>
                    <el-form-item label="Question" prop="question">
                        <el-input type="textarea" v-model="formAsk.question"/>
                    </el-form-item>
                </el-form>
            </div>
            <span slot="footer" class="dialog-footer">
                <el-button @click="dialogVisible = false">Cancel</el-button>
                <el-button type="primary" @click="addQuestions('formAskRules')">Add question</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
    import MaskedInput from 'vue-masked-input'

    export default {
        name: 'Questions',
        components: {
            MaskedInput
        },
        data() {
            return {
                codePhone: ['+38', '+07', '+21'],
                dialogVisible: false,
                questions: [],
                phoneErr: false,
                formAsk: {
                    email: '',
                    name: '',
                    phone: '',
                    question: '',
                    codePhone: '+38'
                },
                rules: {
                    name: [
                        {required: true, message: 'Please input activity name', trigger: 'blur'},
                        {min: 1, max: 10, message: 'Length should be 3 to 5', trigger: 'blur'}
                    ],
                    email: [
                        {required: true, message: 'Please input email', trigger: 'blur'},
                        {type: 'email', message: 'Please input correct email address', trigger: ['blur', 'change']}
                    ],
                    question: [
                        {required: true, message: 'Please input question', trigger: 'blur'},
                    ]
                }
            }
        },
        methods: {
            addQuestions(formName) {
                const phone = this.formAsk.phone.replace(/[^0-9]/gim, '');
                this.$refs[formName].validate((valid) => {
                    if (!valid) return null;
                    if(phone.length != 0) {
                        if (phone.length != 10) {
                            this.phoneErr = true;
                            return null
                        } else {
                            this.phoneErr = false;
                        }
                    }

                    const questions = this.questions.slice();
                    questions.push({
                        ...this.formAsk,
                        status: true,
                        date: new Date().toString(),
                        notifications: Math.ceil(Math.random() * 20)
                    });
                    this.questions = questions;
                    this.resetformAsk();
                    this.dialogVisible = false
                });
            },
            closeQuestion(index) {
                const questions = this.questions.slice();
                const question = questions[index];
                question.status = false;
                this.questions = questions;

            },
            resetformAsk() {
                this.formAsk = {
                    email: '',
                    name: '',
                    phone: '',
                    question: '',
                    codePhone: '+38'
                };
                this.$refs['formAskRules'].resetFields();
            },
            tableRowClassName({row}) {
                if (!row.status) return 'disabled';
                return '';
            }
        }
    }
</script>

<style>
    .el-table .disabled {
        background-color: #ccc !important;
        cursor: not-allowed;
    }

    .el-table .disabled:hover td {
        background-color: #ccc !important;
    }
</style>