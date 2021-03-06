<template>
    <div>
        <v-card v-if="loading">
            <v-layout column fill-height justify-space-between class="ma-0">
                <div>
                    <v-card-text class="pb-3">
                        <v-layout
                            row
                            wrap
                            align-start
                            justify-space-between
                            class="ma-0"
                        >
                            <div style="width: calc(100% - 36px)">
                                <div
                                    class="shimmer title-shimmer"
                                    :class="
                                        getTextColor() === '#ffffff'
                                            ? 'dark--animate'
                                            : 'light--animate'
                                    "
                                ></div>
                                <div
                                    class="shimmer subtitle-shimmer"
                                    :class="
                                        getTextColor() === '#ffffff'
                                            ? 'dark--animate'
                                            : 'light--animate'
                                    "
                                ></div>
                            </div>
                            <div
                                class="shimmer icon-shimmer"
                                :class="
                                    getTextColor() === '#ffffff'
                                        ? 'dark--animate'
                                        : 'light--animate'
                                "
                            ></div>
                        </v-layout>
                    </v-card-text>
                </div>
                <div>
                    <v-card-text class="pb-2 pt-1">
                        <div
                            class="shimmer chip-shimmer"
                            :class="
                                getTextColor() === '#ffffff'
                                    ? 'dark--animate'
                                    : 'light--animate'
                            "
                        ></div>
                    </v-card-text>
                    <v-divider light></v-divider>
                    <v-card-actions
                        class="px-3 mx-1"
                        :class="$vuetify.breakpoint.xsOnly ? 'py-0' : ''"
                    >
                        <div
                            class="shimmer title-shimmer"
                            :class="
                                getTextColor() === '#ffffff'
                                    ? 'dark--animate'
                                    : 'light--animate'
                            "
                            style="width: 50%"
                        ></div>
                        <v-spacer></v-spacer>
                        <div
                            class="shimmer icon-shimmer"
                            :class="
                                getTextColor() === '#ffffff'
                                    ? 'dark--animate'
                                    : 'light--animate'
                            "
                        ></div>
                        <div
                            v-if="!$vuetify.breakpoint.xsOnly"
                            class="shimmer icon-shimmer ml-1"
                            :class="
                                getTextColor() === '#ffffff'
                                    ? 'dark--animate'
                                    : 'light--animate'
                            "
                        ></div>
                    </v-card-actions>
                </div>
            </v-layout>
        </v-card>
        <v-hover v-else #default="{ hover }">
            <v-card
                :elevation="hover && $vuetify.breakpoint.smAndUp ? 12 : 3"
                :light="getTextColor() !== '#ffffff' ? true : false"
                :dark="getTextColor() === '#ffffff' ? true : false"
                :class="$vuetify.breakpoint.xsOnly ? '' : 'mb-2'"
                class="text-left"
                height="100%"
                :color="bg"
            >
                <v-layout column fill-height justify-space-between class="ma-0">
                    <v-card-text
                        :class="
                            $vuetify.breakpoint.xsOnly ? 'pa-3 pb-0' : 'pb-0'
                        "
                    >
                        <v-layout
                            row
                            wrap
                            align-start
                            justify-space-between
                            class="ma-0 task-container"
                            :class="
                                $vuetify.breakpoint.xsOnly ? 'mt-n1 mb-1' : ''
                            "
                        >
                            <div
                                :style="
                                    !isEdit && !isDelete
                                        ? 'width: calc(100% - 36px)'
                                        : 'width: 100%'
                                "
                            >
                                <!-- Editing part -->
                                <div v-if="isEdit">
                                    <v-text-field
                                        filled
                                        v-model="localTask.title"
                                        solo
                                        flat
                                        dense
                                    ></v-text-field>
                                </div>
                                <div
                                    v-if="isEdit"
                                    :class="
                                        $vuetify.breakpoint.xsOnly
                                            ? 'mt-n4'
                                            : 'mt-n3'
                                    "
                                >
                                    <v-textarea
                                        v-model="localTask.description"
                                        auto-grow
                                        filled
                                        solo
                                        flat
                                        dense
                                    ></v-textarea>
                                </div>
                                <div
                                    v-if="isEdit"
                                    :class="
                                        $vuetify.breakpoint.xsOnly
                                            ? 'mt-n4'
                                            : 'mt-n3'
                                    "
                                >
                                    <v-card flat class="px-3 type-card">
                                        <v-chip-group
                                            multiple
                                            return-object
                                            v-model="localType"
                                            v-if="$store.getters.types.length"
                                        >
                                            <v-chip
                                                filter
                                                small
                                                label
                                                :value="tag.id"
                                                :key="tag.id"
                                                :color="tag.color"
                                                v-for="tag in $store.getters
                                                    .types"
                                                @keyup.enter="
                                                    updateTaskModel(tag.id)
                                                "
                                                :style="
                                                    `color: ${getTextColorForBg(
                                                        tag.color
                                                    )} !important`
                                                "
                                                >{{ tag.label }}</v-chip
                                            >
                                        </v-chip-group>
                                        <div
                                            v-else
                                            class="caption py-2 warning--text"
                                        >
                                            No label found
                                        </div>
                                    </v-card>
                                </div>
                                <div
                                    v-if="isEdit"
                                    :class="
                                        $vuetify.breakpoint.xsOnly
                                            ? 'mt-3'
                                            : 'mt-4 mb-n6'
                                    "
                                >
                                    <v-layout
                                        row
                                        wrap
                                        align-center
                                        class="ma-0"
                                    >
                                        <v-flex xs6 py-0 class="pl-0">
                                            <v-menu
                                                :close-on-content-click="false"
                                            >
                                                <template #activator="{ on }">
                                                    <v-text-field
                                                        v-on="on"
                                                        solo
                                                        flat
                                                        dense
                                                        filled
                                                        readonly
                                                        :value="readableTime"
                                                        :disabled="
                                                            localTask.indefinite
                                                        "
                                                        prepend-inner-icon="mdi-clock"
                                                    ></v-text-field>
                                                </template>
                                                <v-card>
                                                    <v-time-picker
                                                        color="primary"
                                                        ref="timePicker"
                                                        class="pickerTime"
                                                        v-model="localTask.time"
                                                        :locale="$i18n.locale"
                                                        :ampm-in-title="
                                                            $vuetify.breakpoint
                                                                .mdAndUp
                                                        "
                                                    ></v-time-picker>
                                                </v-card>
                                            </v-menu>
                                        </v-flex>
                                        <v-flex xs6 py-0 class="pr-0">
                                            <v-menu
                                                :close-on-content-click="false"
                                            >
                                                <template #activator="{ on }">
                                                    <v-text-field
                                                        solo
                                                        flat
                                                        dense
                                                        v-on="on"
                                                        filled
                                                        readonly
                                                        :value="readableDate"
                                                        :disabled="
                                                            localTask.indefinite
                                                        "
                                                        prepend-inner-icon="mdi-calendar"
                                                    ></v-text-field>
                                                </template>
                                                <v-card>
                                                    <v-date-picker
                                                        reactive
                                                        color="primary"
                                                        ref="datePicker"
                                                        v-model="localTask.date"
                                                        :locale="$i18n.locale"
                                                    ></v-date-picker>
                                                </v-card>
                                            </v-menu>
                                        </v-flex>
                                    </v-layout>
                                </div>
                                <div v-if="isEdit" class="mt-n7 ml-1">
                                    <v-layout class="ma-0" align-center>
                                        <div>Indefinte task?</div>
                                        <v-spacer></v-spacer>
                                        <v-switch
                                            class="mr-n1"
                                            color="primary"
                                            v-model="localTask.indefinite"
                                        ></v-switch>
                                    </v-layout>
                                </div>
                                <!-- delete part -->
                                <div
                                    v-if="isDelete"
                                    class="body-2 font-weight-bold px-1"
                                    :class="[
                                        getTextColor() !== '#ffffff'
                                            ? 'black--text'
                                            : 'white--text',
                                        $vuetify.breakpoint.xsOnly
                                            ? 'pb-1'
                                            : 'pb-3',
                                    ]"
                                >
                                    {{ $t('task.delete') }}
                                </div>
                                <!-- Non edit/delete part -->
                                <div
                                    v-if="!isEdit && !isDelete"
                                    class="font-weight-bold overflow-text"
                                    :style="
                                        $vuetify.breakpoint.xsOnly
                                            ? 'min-height: 24px;'
                                            : 'min-height: 28px;'
                                    "
                                    :class="[
                                        $vuetify.breakpoint.xsOnly
                                            ? 'body-2'
                                            : 'body-1 pb-1',
                                        getTextColor() !== '#ffffff'
                                            ? 'black--text'
                                            : 'white--text',
                                    ]"
                                >
                                    <v-tooltip
                                        bottom
                                        max-width="50%"
                                        nudge-top="4"
                                    >
                                        <template #activator="{ on }">
                                            <span
                                                v-on="on"
                                                :class="
                                                    task.status == 'done'
                                                        ? 'line-through-text'
                                                        : ''
                                                "
                                                >{{ task.title }}</span
                                            >
                                        </template>
                                        <span>{{ task.title }}</span>
                                    </v-tooltip>
                                </div>
                            </div>
                            <div
                                v-if="!isEdit && !isDelete"
                                class="mr-n1"
                                style="margin-top: -2px !important;"
                                :style="
                                    $vuetify.breakpoint.xsOnly
                                        ? 'min-height: 24px;'
                                        : 'min-height: 36px;'
                                "
                            >
                                <v-btn
                                    icon
                                    v-show="hover || $vuetify.breakpoint.xsOnly"
                                    :disabled="disabled"
                                    :small="$vuetify.breakpoint.xsOnly"
                                    :color="
                                        getTextColor() === '#ffffff'
                                            ? 'white'
                                            : 'black'
                                    "
                                    @click.stop="
                                        task.status === 'todo'
                                            ? $emit('check', task)
                                            : $emit('uncheck', task)
                                    "
                                >
                                    <v-icon
                                        :small="$vuetify.breakpoint.xsOnly"
                                        >{{
                                            task.status === 'todo'
                                                ? 'mdi-check'
                                                : 'mdi-close'
                                        }}</v-icon
                                    >
                                </v-btn>
                            </div>
                        </v-layout>
                        <div
                            v-if="!isEdit && !isDelete"
                            class="task-desc"
                            :class="[
                                $vuetify.breakpoint.xsOnly
                                    ? 'caption'
                                    : 'body-2 pt-1',
                                task.status == 'done'
                                    ? 'line-through-text'
                                    : '',
                                getTextColor() !== '#ffffff'
                                    ? 'black--text'
                                    : 'white--text',
                            ]"
                        >
                            <vue-markdown
                                :source="task.description"
                            ></vue-markdown>
                        </div>
                    </v-card-text>
                    <div>
                        <v-card-text
                            v-if="!isEdit && !isDelete && task.type.length"
                            :class="
                                $vuetify.breakpoint.xsOnly ? 'pa-3' : 'py-3'
                            "
                        >
                            <div>
                                <v-chip
                                    label
                                    class="mr-2 mb-1"
                                    :key="tag.id"
                                    :value="tag.id"
                                    :title="tag.label"
                                    :disabled="disabled"
                                    :text-color="tag.color"
                                    v-for="tag in task.type"
                                    :small="$vuetify.breakpoint.xsOnly"
                                    @click.stop="$emit('type-select', tag)"
                                    :color="getTextColorForBg(tag.color)"
                                    >{{ tag.label }}</v-chip
                                >
                            </div>
                        </v-card-text>
                        <v-divider light></v-divider>
                        <v-card-actions
                            class="task-card-action"
                            :class="
                                $vuetify.breakpoint.xsOnly
                                    ? 'px-3 py-0'
                                    : 'px-4'
                            "
                            :style="
                                `min-height: ${
                                    $vuetify.breakpoint.xsOnly ? '36px' : '52px'
                                }`
                            "
                        >
                            <div v-if="!isEdit && !isDelete">
                                <v-icon
                                    :small="$vuetify.breakpoint.xsOnly"
                                    :color="
                                        getTextColor() === '#ffffff'
                                            ? 'white'
                                            : 'black'
                                    "
                                    >mdi-clock</v-icon
                                >
                            </div>
                            <v-tooltip v-if="!isEdit && !isDelete" bottom>
                                <template #activator="{ on }">
                                    <div
                                        v-on="on"
                                        class="task-time overflow-text"
                                        :class="[
                                            $vuetify.breakpoint.xsOnly
                                                ? 'pl-1 caption'
                                                : 'pl-2 body-2',
                                            getTextColor() !== '#ffffff'
                                                ? 'black--text'
                                                : 'white--text',
                                        ]"
                                    >
                                        {{ getCalendarDate() }}
                                    </div>
                                </template>
                                <span>{{ getCalendarDate() }}</span>
                            </v-tooltip>
                            <v-spacer v-if="!isEdit && !isDelete"></v-spacer>
                            <template v-if="!isEdit && !isDelete">
                                <v-btn
                                    icon
                                    :disabled="disabled"
                                    v-if="task.status == 'todo'"
                                    :small="$vuetify.breakpoint.xsOnly"
                                    v-show="hover || $vuetify.breakpoint.xsOnly"
                                    @click.stop="
                                        onModalCancel();
                                        isEdit = true;
                                    "
                                    :color="
                                        getTextColor() === '#ffffff'
                                            ? 'white'
                                            : 'black'
                                    "
                                >
                                    <v-icon :small="$vuetify.breakpoint.xsOnly"
                                        >mdi-pencil</v-icon
                                    >
                                </v-btn>
                                <v-btn
                                    icon
                                    :disabled="disabled"
                                    @click.stop="isDelete = true"
                                    :small="$vuetify.breakpoint.xsOnly"
                                    v-show="hover || $vuetify.breakpoint.xsOnly"
                                    :color="
                                        getTextColor() === '#ffffff'
                                            ? 'white'
                                            : 'black'
                                    "
                                >
                                    <v-icon :small="$vuetify.breakpoint.xsOnly"
                                        >mdi-delete</v-icon
                                    >
                                </v-btn>
                            </template>
                            <div v-if="isEdit || isDelete">
                                <v-btn
                                    icon
                                    :disabled="disabled"
                                    :small="$vuetify.breakpoint.xsOnly"
                                    @click.stop="
                                        onModalCancel();
                                        isEdit = false;
                                        isDelete = false;
                                    "
                                    :color="
                                        getTextColor() === '#ffffff'
                                            ? 'white'
                                            : 'black'
                                    "
                                >
                                    <v-icon>mdi-close</v-icon>
                                </v-btn>
                            </div>
                            <v-spacer v-if="isEdit || isDelete"></v-spacer>
                            <div v-if="isEdit || isDelete">
                                <v-btn
                                    icon
                                    :disabled="disabled"
                                    :small="$vuetify.breakpoint.xsOnly"
                                    @click.stop="
                                        onModalCheck();
                                        isEdit = false;
                                        isDelete = false;
                                    "
                                    :color="
                                        getTextColor() === '#ffffff'
                                            ? 'white'
                                            : 'black'
                                    "
                                >
                                    <v-icon>mdi-check</v-icon>
                                </v-btn>
                            </div>
                        </v-card-actions>
                    </div>
                </v-layout>
            </v-card>
        </v-hover>
    </div>
</template>
<script lang="ts">
import Vue from 'vue';
// @ts-ignore
import VueMarkdown from 'vue-markdown';
// @ts-ignore
import {
    getCalendarDate,
    getTextColorByBg,
    getMomentDate,
    get12FormatTime,
    getShortReadableDate,
    // @ts-ignore
} from '@/util';
export default Vue.extend({
    components: {
        VueMarkdown,
    },
    props: {
        task: {
            type: Object,
            required: true,
        },
        loading: {
            type: Boolean,
            default: false,
        },
        disabled: {
            type: Boolean,
            default: false,
        },
    },
    data() {
        return {
            isDelete: false,
            isEdit: false,
            localTask: {
                date: getMomentDate(new Date()).substr(0, 10),
                time: getMomentDate(new Date()).substr(11, 5),
                description: '',
                indefinite: true,
                status: '',
                title: '',
                type: [],
                uid: '',
            },
        };
    },
    computed: {
        bg: {
            get() {
                // @ts-ignore
                return this.getBgColor();
            },
        },
        localType: {
            get() {
                // @ts-ignore
                return this.localTask.type.map((type) => type.id);
            },
            set(val) {
                // @ts-ignore
                this.updateTypes(val);
            },
        },
        readableTime() {
            // @ts-ignore
            return get12FormatTime(this.localTask.time);
        },
        readableDate() {
            // @ts-ignore
            return getShortReadableDate(this.localTask.date);
        },
    },
    methods: {
        // @ts-ignore
        getTextColor() {
            // @ts-ignore
            return this.getTextColorForBg(this.bg);
        },
        // @ts-ignore
        getBgColor() {
            // @ts-ignore
            if (this.loading) {
                // @ts-ignore
                return this.$vuetify.theme.dark ? '#303030' : '#ffffff';
            }
            // @ts-ignore
            if (this.task.type.length) {
                // @ts-ignore
                return this.task.type[0].color;
            } else {
                if (this.$vuetify.theme.dark) {
                    return '#303030';
                } else {
                    return '#ffffff';
                }
            }
        },
        getTextColorForBg(color: any) {
            return getTextColorByBg(color);
        },
        // @ts-ignore
        getCalendarDate() {
            // @ts-ignore
            if (this.task.indefinite) return this.$t('task.no-due');
            // @ts-ignore
            else return getCalendarDate(this.task.date + 'T' + this.task.time);
        },
        onModalCheck() {
            // @ts-ignore
            if (this.isDelete && !this.isEdit) {
                // @ts-ignore
                this.$emit('delete', this.task);
            }
            // @ts-ignore
            if (!this.isDelete && this.isEdit) {
                // @ts-ignore
                this.$emit('edit', this.localTask);
            }
        },
        onModalCancel() {
            // @ts-ignore
            let { indefinite, date, time } = this.task;
            // @ts-ignore
            this.localTask = {
                // @ts-ignore
                ...this.task,
                // @ts-ignore
                time: indefinite ? this.localTask.time : time,
                // @ts-ignore
                date: indefinite ? this.localTask.date : date,
            };
        },
        updateTypes(val: any) {
            let types: any = [];
            val.forEach((element: any) => {
                let type = this.$store.getters.types.find(
                    (e: any) => e.id == element
                );
                if (type) types.push(type);
            });
            // @ts-ignore
            this.localTask.type = [...types];
        },
        updateTaskModel(id: any) {
            //@ts-ignore
            let localT = this.localType;
            let indexVal = localT.indexOf(id);
            if (indexVal !== -1) {
                localT.splice(indexVal, 1);
            } else {
                //@ts-ignore
                localT.push(id);
            }
            // @ts-ignore
            this.updateTypes(localT);
        },
    },
});
</script>
<style scoped>
.done-task {
    filter: grayscale(100%);
    background: red;
}
.task-desc {
    overflow: hidden;
    white-space: pre-wrap;
    line-height: 1.2em !important;
}
.task-desc >>> p {
    margin: 0 !important;
}
.task-desc >>> br {
    display: none;
}
.task-time {
    padding-top: 3px;
}
.task-container >>> textarea {
    font-size: 14px !important;
    margin-top: 4px !important;
}
.task-container >>> input {
    font-size: 16px !important;
}
.pickerTime >>> .v-time-picker-title {
    justify-content: center !important;
}
.pickerTime {
    width: 100%;
}
.task-card-action {
    margin-left: -1px !important;
}
.type-card {
    background-color: rgba(24, 15, 15, 0.06) !important;
    border-color: rgba(0, 0, 0, 0.06) !important;
    transition: all 0.3s ease;
}
.type-card:hover {
    background-color: rgba(0, 0, 0, 0.1) !important;
    border-color: rgba(0, 0, 0, 0.1) !important;
}
@media only screen and (max-width: 600px) {
    .task-container >>> textarea {
        font-size: 12px !important;
        margin-top: 2px !important;
    }
    .task-container >>> input {
        font-size: 14px !important;
    }
}
</style>
