<template>
<div class="checkListWrapper">
  <div class="listTyping">
    <!-- priority input group -->
    <div class="inputGroup">
      <div class="inputTitle">Priority: </div>
      <input
        type="number"
        class="priority"
        min="1"
        max="50"
        v-model="priority"
        @change="updateNumberResult"
      >
    </div>

    <!-- name input group -->
    <div class="inputGroup">
      <div class="inputTitle">Name: </div>
      <input
        type="text"
        class="name"
        v-model="name"
      >
    </div>

    <!-- content input group -->
    <div class="inputGroup">
      <div class="inputTitle">Content: </div>
      <input
        type="text"
        class="content"
        v-model="content"
        @keyup.enter="addNewItem"
      >
    </div>

    <!-- addNewItem button -->
    <div
      class="button addList"
      :class="{ 'disabled': isButtonDisabled }"
      @click="addNewItem"
    >
      add new
    </div>
  </div>

  <div class="contentWrapper">
    <!-- lists -->
    <div class="listContent" v-if="checkList.length !== 0">
      <div class="listTitleGroup mb-20">
        <p class="fz-24">Check List</p>
        <!-- sort bottons -->
        <div class="flex-center">
          <div class="sortButtons mx-4" v-if="checkList.length > 1">
            <!-- topSort and bottomSort methods -->
            <div
              class="topArrow"
              :class="{ 'active': isSortDirection === 'top' }"
              @click="sortTopToBottom"></div>
            <div
              class="bottomArrow"
              :class="{ 'active': isSortDirection === 'bottom'}"
              @click="sortBottomToTop"></div>
          </div>
          <div
            class="button mx-4"
            :class="{ 'disabled': isClearAlldone }"
            @click="delAllDoneItem"
          >
            clear all done
          </div>
        </div>
      </div>
      <div
        class="listItem"
        :class="{ 'done': item.isDone }"
        v-for="(item, idx) in checkList"
        :key="item.id"
      >
        <p>{{ idx + 1 }}.</p>

        <!-- show or edit priority -->
        <p v-if="!item.isEdit">Priority: {{ item.priority }}</p>
        <div class="inputGroup mr-16" v-else>
          <div class="inputTitle">priority</div>
          <input
            type="number"
            class="priority"
            min="1"
            max="50"
            v-model="editPriority"
          >
        </div>

        <!-- show or edit name -->
        <p v-if="!item.isEdit">Name: {{ item.name }}</p>
        <div class="inputGroup mr-16" v-else>
          <div class="inputTitle">name</div>
          <input
            type="text"
            class="name"
            v-model="editName"
          >
        </div>

        <!-- show or edit content -->
        <p v-if="!item.isEdit">Content: {{ item.content }}</p>
        <div class="inputGroup mr-16" v-else>
          <div class="inputTitle">content</div>
          <input
            type="text"
            class="content"
            v-model="editContent"
          >
        </div>

        <div class="buttonItems">
          <!-- editItem or updateItem methods-->
          <div class="button" :class="{ 'disabled': item.isDone }">
            <p v-if="!item.isEdit" @click="editItem(item)">edit</p>
            <p v-else @click="updateItem(item)">update</p>
          </div>

          <!-- removeItem methods -->
          <div class="button delItem" @click="removeItem(idx)">delete</div>
          <div
            class="button"
            :class="{ 'disabled': item.isDone }"
            @click="itemDone(item)"
          >
            done
          </div>
        </div>
      </div>
    </div>
    <p class="fz-24" v-else>List is empty, please add new item</p>
  </div>

</div>
</template>

<script>
export default {
  name: 'CheckList',
  data: () => ({
    priority: 1,
    name: "",
    content: "",
    checkList: [],
    editPriority: "",
    editName: "",
    editContent: "",
    isSortDirection: "",
    item: null
  }),
  computed: {
    isClearAlldone() {
      return this.checkList.every(item => !item.isDone)
    },
    isButtonDisabled() {
      return (
        !this.priority ||
        !this.name ||
        !this.content
      ) ? true : false
    }
  },
  methods: {
    delAllDoneItem() {
      this.checkList = this.checkList.filter(item => !item.isDone)
    },
    itemDone(item) {
      item.isDone = true
    },
    editItem(item) {
      item.isEdit = true

      this.editPriority = item.priority
      this.editName = item.name
      this.editContent = item.content
    },
    updateItem(item) {
      item.priority = this.editPriority
      item.name = this.editName
      item.content = this.editContent

      this.editPriority = ""
      this.editName = ""
      this.editContent = ""

      item.isEdit = false
    },
    updateNumberResult() {
      this.priority > 50
        ? this.priority = 50 : this.priority < 1
        ? this.priority = 1 : this.priority
    },
    clearTyping() {
      this.priority = ""
      this.name = ""
      this.content = ""
    },
    validationItem() {
      return (
        this.priority &&
        this.name &&
        this.content
      ) ? true : false
    },
    addNewItem() {
      const isSendItem = this.validationItem()

      if (isSendItem) {
        this.item = {
          priority: this.priority,
          name: this.name,
          content: this.content,
          isEdit: false,
          isDone: false
        }

        this.checkList.push(this.item)
        this.clearTyping()
      } else return
    },
    removeItem(idx) {
      this.checkList = this.checkList.filter(
        (_, index) => { return idx !== index }
      )
    },
    sortTopToBottom() {
      this.isSortDirection = "top"
      this.checkList = this.checkList.sort(
        (a, b) => a.priority - b.priority
      )
    },
    sortBottomToTop() {
      this.isSortDirection = "bottom"
      this.checkList = this.checkList.sort(
        (a, b) => b.priority - a.priority
      )
    }
  }
}
</script>

<style>
input {
  text-align: center;
  outline: none;
  border: none;
  border-bottom: 2px solid #000;
  font-size: 16px;
  transition: 0.3s;
}

input.priority {
  width: 40px;
}

input[type=number]::-webkit-inner-spin-button,
input[type=number]::-webkit-outer-spin-button {
  opacity: 0;
  visibility: hidden;
}

p {
  margin: 0;
}

.mb-20 {
  margin-bottom: 20px;
}

.fz-24 {
  font-size: 24px;
}

.mr-16 {
  margin-right: 16px;
}

.mx-4 {
  margin: 0 4px;
}

.checkListWrapper {
  display: inline-block;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%,-50%);
  max-width: 840px;
  width: 100%;
}

.contentWrapper {
  border: 4px solid #000;
  text-align: center;
  font-weight: bold;
  padding: 20px;
  border-radius: 20px;
  width: 100%;
}

.listTyping {
  border: 4px solid #000;
  padding: 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  margin-bottom: 20px;
  border-radius: 20px;
}

.inputGroup {
  display: flex;
  justify-content: center;
  align-items: center;
}

.inputTitle {
  margin-right: 8px;
  font-weight: bold;
  font-size: 18px;
}

.button {
  background: #56ADFF;
  border-radius: 16px;
  color: #fff;
  padding: 4px 18px;
  cursor: pointer;
  transition: 0.3s;
}

.flex-center {
  display: flex;
  justify-content: center;
  align-items: center;
}

.button.disabled {
  background: #B4C0CC;
  color: #DFDEE0;
  pointer-events: none;
}

.buttonItems {
  display: flex;
  position: absolute;
  right: 0;
  top: 50%;
  transform: translateY(-50%);
}

.buttonItems > div {
  margin: 0 4px;
}

.listContent {
  text-align: center;
}

.listTitleGroup {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.listItem {
  position: relative;
  display: flex;
  align-items: center;
  width: 100%;
  margin: 0 auto 20px;
  font-size: 16px;
}

.listItem.done:after {
  content: "";
  position: absolute;
  width: 64%;
  height: 2px;
  background: #000;
  top: 50%;
  left: 0;
  transform: translateY(-50%);
}

.listItem:last-of-type {
  margin-bottom: 0;
}

.listItem > p {
  margin-right: 10px;
}

.topArrow {
  width: 0;
  height: 0;
  border-style: solid;
  border-width: 0 6px 12px 6px;
  border-color: transparent transparent #8f8f8f transparent;
  margin: 2px 0;
}

.bottomArrow {
  width: 0;
  height: 0;
  border-style: solid;
  border-width: 12px 6px 0 6px;
  border-color: #8f8f8f transparent transparent transparent;
  margin: 2px 0;
}

.topArrow.active {
  border-color: transparent transparent #2e87e6 transparent;
}

.bottomArrow.active {
  border-color: #2e87e6 transparent transparent transparent;
}

</style>
