<template>
<div class="debug">
    <div class="tip" v-if="defaultConfig.enableFullRemoteRules">
        <p>This area is used for debugging rules in development</p>
        <p>Edited content may be overwritten by automatically updated rules at any time, please ensure that there is a local backup</p>
        <p>Use Settings - Update Now to immediately restore remote rules</p>
    </div>
    <div class="tip" v-if="!defaultConfig.enableFullRemoteRules">
        <p>This area is used for debugging rules in development</p>
        <p>Remote updates and debug function is not available due to browser limitations</p>
    </div>
    <el-input
        type="textarea"
        :rows="100"
        v-model="rulesText"
        @change="updateRules"
        :disabled="!defaultConfig.enableFullRemoteRules"
    >
    </el-input>
</div>
</template>

<script>
import { getRules, updateRules } from '../../common/rules';
import { secondToHoursMinutes, commandSandbox } from '../../common/utils';
import { defaultConfig } from '../../common/config';

export default {
    name: 'debugging-rules',
    data: () => ({
        rulesText: '',
        defaultConfig,
    }),
    created() {
        getRules((rules) => {
            if (typeof rules === 'string') {
                this.rulesText = rules;
                commandSandbox('getList', {
                    rules,
                }, (rules) => {
                    this.rules = rules;
                    this.loading = false;
                });
            } else {
                this.rulesText = JSON.stringify(rules, null, 4);
                this.rules = rules;
                this.loading = false;
            }
        });
    },
    methods() {
        updateRules(this.rulesText, () => {
            this.$message({
                message: this.$i18n.t('successfully saved'),
                type: 'success'
            });
        });
    }
}
</script>

<style>
.debug {
    width: 100%;
    max-width: 800px;
    margin: 0 auto;
    padding: 40px 10px;
}
</style>