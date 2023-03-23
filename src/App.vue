<template>
  <main id="main">
    <div class="calc">
      <div class="calc__header">
        <h1>calc</h1>
        <div class="header-left">
          <h2>THEME</h2>
          <div class="toggle-wrap">
            <div class="toggle-numbers">
              <span>1</span>
              <span>2</span>
              <span>3</span>
            </div>
            <div class="toggle-theme">
              <input class="toggle-theme__radio" type="radio" name="toggle"
                     value="theme-blue" v-model="colorTheme" data-toggle="1">
              <input class="toggle-theme__radio" type="radio" name="toggle"
                     value="theme-white" v-model="colorTheme" data-toggle="2">
              <input class="toggle-theme__radio" type="radio" name="toggle"
                     value="theme-dark" v-model="colorTheme" data-toggle="3">

              <span class="toggle-theme__switch"></span>
            </div>
          </div>
        </div>
      </div>
      <div class="calc__screen">
        <p>{{ screenDisplay }}</p>
      </div>
      <div class="calc__numpad">
        <button v-for="pad_key in pad_keys" :class="{'del-button': pad_key === 'DEL'}" @click="addToString(pad_key)">
          {{ pad_key }}
        </button>

        <button class="reset-button" @click="reset(true)">RESET</button>
        <button class="equal-button" @click="doMath">=</button>
      </div>
    </div>
  </main>
</template>

<script>
export default {
  data() {
    return {
      pad_keys: ["7", "8", "9", "DEL", "4", "5", "6", "+", "1", "2", "3", "-", ".", "0", "/", "x"],
      operations:
          {
            "+": (a, b) => a + b
            , "-": (a, b) => a - b
            , "x": (a, b) => a * b
            , "/": (a, b) => a / b
          },
      colorTheme: "theme-blue",
      screenDisplay: "0",
      operator: "",
      firstNum: "",
      secondNum: "",
      errorBlock: false,
    }
  },
  watch: {
    colorTheme: {
      handler(newQuestion) {
        document.getElementsByTagName("body")[0].setAttribute("data-theme", this.colorTheme);
      },
      immediate: true
    }
  },
  methods: {
    addToString(char) {
      if(this.errorBlock)
        return;

      switch (char) {
        case "DEL":
          if (this.firstNum !== "" && this.operator === "") {
            this.firstNum = this.deleteChar(this.firstNum);
            this.showScreen(this.firstNum);
            break;
          }
          if (this.secondNum !== "") {
            this.secondNum = this.deleteChar(this.secondNum);
            this.showScreen(this.secondNum);
            break;
          }
          break;
        case "+":
        case "-":
        case "x":
        case "/":
          if(this.firstNum === "") {
            this.error("Erreur : utilisation d'un opérateur sans valeur")
            break;
          }

          if (this.operator !== "") {
            if(this.secondNum === "")
              this.error("Erreur : deux opérateurs à la suite")
            this.firstNum = this.operations[this.operator](parseFloat(this.firstNum), parseFloat(this.secondNum)).toString();
            this.secondNum = "";
            this.showScreen(this.firstNum);
          }
          this.operator = char;
          break;
        default:
          if(this.operator !== "") {
            this.secondNum += char;
            this.showScreen(this.secondNum);
          }
          else {
            this.firstNum += char;
            this.showScreen(this.firstNum);
          }
      }

    },
    reset(withScreen = false) {
      if(withScreen) {
        this.showScreen("0");
      }
      this.firstNum = "";
      this.secondNum = "";
      this.operator = "";
      this.errorBlock = false;
    },
    doMath() {
      if(this.secondNum === "")
        return;
      this.showScreen(this.operations[this.operator](parseFloat(this.firstNum), parseFloat(this.secondNum)).toString());
      this.reset();
    },
    deleteChar(num) {
      let str = num.toString();
      return str.slice(0, str.length - 1);
    },
    showScreen(value) {
      let sliced = value ? value.slice(0,13) : "0";
      this.screenDisplay = sliced;
    },
    error(str) {
      this.screenDisplay = "ERROR";
      this.errorBlock = true;
      alert(str);
    }
  },
}
</script>