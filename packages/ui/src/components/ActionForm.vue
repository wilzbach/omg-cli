<template>
  <div class="action-form-container">
    <form class="action" @submit.prevent="processData" @change="changeHandler">
      <div class="inputs" v-if="getMicroservice.actions[actionName].arguments">
        <div
          class="input"
          :key="`arg-${argName}`"
          v-for="(arg, argName) of getMicroservice.actions[actionName]
            .arguments"
        >
          <div class="label-line">
            <label class="form-row">
              {{ argName }}
              <input
                :name="`arg-${argName}`"
                :required="
                  arg.hasOwnProperty('required') && arg.required ? true : false
                "
                :value="query && query.args ? query.args[argName] : ''"
                :placeholder="arg.type"
                :type="arg.type === 'number' ? 'number' : 'string'"
              />
            </label>
            <span class="required-hint">{{ arg.required ? "* Required" : "" }}</span>
          </div>
          <span
            :class="{ help: arg.help && arg.help.length > 0 }"
            class="hint"
          >{{ arg.help ? arg.help : "No help provided" }}</span>
        </div>
      </div>
      <div
        v-else-if="
          getMicroservice.actions[actionName].events &&
            getMicroservice.actions[actionName].events[eventName]
        "
        class="inputs"
      >
        <input type="hidden" name="type" value="event" />
        <div
          class="input"
          :key="`arg-${argName}`"
          v-for="(arg, argName) of getMicroservice.actions[actionName].events[
            eventName
          ].arguments"
        >
          <label class="form-row">
            {{ argName }} {{ arg.required ? "*" : "" }}
            <input
              :name="`arg-${argName}`"
              :required="
                arg.hasOwnProperty('required') && arg.required ? true : false
              "
              :value="query && query.args ? query.args[argName] : ''"
              :placeholder="arg.type"
              :type="arg.type === 'number' ? 'number' : 'string'"
            />
          </label>
          <span
            :class="{ help: arg.help && arg.help.length > 0 }"
            class="hint"
          >{{ arg.help ? arg.help : "No help provided" }}</span>
        </div>
      </div>
      <div class="btn-container form-row">
        <div class="spacer"></div>
        <button class="run-btn" :disabled="loading">
          <clip-loader :color="'white'" :size="'20px'" class="loader" v-if="loading"></clip-loader>
          <span v-else>{{ parseError ? "Cannot parse JSON" : "Run Action" }}</span>
        </button>
      </div>
    </form>
  </div>
</template>

<script>
import { mapGetters, mapMutations } from 'vuex'
import ClipLoader from 'vue-spinner/src/ClipLoader'

export default {
  name: 'action-form',
  components: {
    ClipLoader
  },
  data: () => ({
    open: '',
    obj: {}
  }),
  props: {
    actionName: {
      type: String,
      default: '',
      required: true
    },
    eventName: {
      type: String,
      default: '',
      required: false
    },
    loading: {
      type: Boolean,
      default: false,
      required: true
    },
    query: {
      type: Object,
      default: null,
      required: false
    }
  },
  computed: {
    ...mapGetters(['getMicroservice'])
  },
  mounted () {
    if (this.query && this.query.action) {
      this.open = this.query.action
    }
  },
  methods: {
    ...mapMutations(['updateActionContinuousArgs', 'addActionCurlArgs']),
    processData (data) {
      this.obj['action'] = this.actionName
      this.obj['args'] = {}
      for (let i = 0; i < data.srcElement.length - 1; i += 1) {
        const key = data.srcElement[i].name.substr(4)
        const value = data.srcElement[i].value

        this.obj.args[key] = value
        if (this.event && this.event.length > 0) {
          this.obj['subscribe'] = true
        }
      }
      this.$emit('argsEdited', this.obj)
    },
    changeHandler (data) {
      const key = data.srcElement.name.substr(4)
      const value = data.srcElement.value

      this.addActionCurlArgs({ key, value })
    }
  }
}
</script>

<style lang="scss" scoped>
form.action {
  display: flex;
  flex-flow: column nowrap;
  justify-content: space-evenly;
  align-items: center;

  .form-row {
    display: flex;
    justify-content: space-between;
    align-items: center;
    width: 100%;
  }

  .inputs {
    display: flex;
    flex-flow: column;

    .input {
      margin-bottom: 20px;

      .label-line {
        align-items: flex-start;
        display: flex;

        .required-hint {
          width: 100px;
          height: 44px;
          align-items: center;
          display: flex;
          color: #ba2525;
          font-family: Graphik;
          font-size: 14px;
          line-height: 22px;
          font-style: italic;
        }

        label {
          color: #616e7c;
          font-family: Graphik;
          font-size: 14px;
          font-weight: 500;
          letter-spacing: 0.5px;
          line-height: 21px;
          text-align: right;
          margin-bottom: 8px;
        }

        input {
          height: 40px;
          width: 420px;
          border: 1px solid #dfe0e8;
          border-radius: 2px;
          background-color: #ffffff;
          margin: 0 24px;
          color: #1f2933;
          font-family: Graphik;
          font-size: 14px;
          line-height: 21px;
          padding-left: 12px;

          &::placeholder {
            height: 18px;
            color: #9aa5b1;
            font-family: Graphik;
            font-size: 14px;
            line-height: 21px;
          }
        }
      }

      span.hint {
        height: 51px;
        color: #616e7c;
        font-family: Graphik;
        font-size: 14px;
        line-height: 22px;
        font-style: italic;
        display: flex;

        &.help {
          font-style: inherit;
        }
      }
    }
  }

  .btn-container {
    button.run-btn {
      height: 35px;
      width: 435px;
      border-radius: 2px;
      color: #ffffff;
      font-family: Graphik;
      font-size: 16px;
      font-weight: 500;
      line-height: 21px;
      text-align: center;
      background-color: #17b897;
      margin-right: 109px;

      &:hover {
        background-color: #2dcca7;
      }

      &:focus {
        background-color: #079a82;
      }

      .loader {
        margin-top: 4px;
      }
    }
  }
}
</style>
