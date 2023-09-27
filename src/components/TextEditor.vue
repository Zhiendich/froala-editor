<template>
  <div>
    <div @click="toggleShow" v-html="text"></div>

    <div
      class="froala-editor-label"
      v-show="showEditor"
      @click="stopPropagation"
    >
      <froala
        id="froala-editor"
        :tag="'textarea'"
        :config="config"
        v-model="defaultText"
      ></froala>
    </div>
  </div>
</template>

<script>
export default {
  name: "TextEditor",
  data() {
    let self = this;
    return {
      showEditor: false,
      config: {
        charCounterCount: false,
        toolbarVisibleWithoutSelection: false,
        toolbarTop: true,
        toolbarButtons: [
          "bold",
          "underline",
          "strikeThrough",
          "fontFamily",
          "fontSize",
          "textColor",
        ],
        // fontFamily: {
        //   "Roboto,sans-serif": "Roboto",
        //   "Oswald,sans-serif": "Oswald",
        //   "Montserrat,sans-serif": "Montserrat",
        //   "'Open Sans Condensed',sans-serif": "Open Sans Condensed",
        // },
        fontFamilySelection: true,
        events: {
          contentChanged: function () {
            self.text = this.html.get();
          },
        },
      },
      defaultText: "Test text",
      text: "",
    };
  },
  methods: {
    toggleShow(event) {
      this.showEditor = !this.showEditor;

      if (this.showEditor) {
        event.target.classList.add("editing");

        const editingTextBlock = event.target.getBoundingClientRect();
        const froalaEditorBlock = document.getElementsByClassName(
          "froala-editor-label"
        )[0];
        const newPositionY = editingTextBlock.bottom + 5;
        froalaEditorBlock.style.top = `${newPositionY}px`;
      } else {
        event.target.classList.remove("editing");
      }
    },
    stopPropagation(event) {
      event.stopPropagation();
    },
    closeEditorOnClickOutside(event) {
      if (this.showEditor && !event.target.classList.contains("editing")) {
        this.showEditor = false;
      }
    },
  },
  created() {
    this.text = this.defaultText;
    this.showEditor = false;
    document.addEventListener("click", this.closeEditorOnClickOutside);
  },
  beforeDestroy() {
    document.removeEventListener("click", this.closeEditorOnClickOutside);
  },
};
</script>

<style>
.froala-editor-label {
  position: absolute;
  max-width: 480px;
  left: 50%;
  transform: translate(-50%, 0);
}
</style>
