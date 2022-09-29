<template>
  <div id="app">
    <div style="font-size: 2.0em; color: red">Blood and Thunder 2: <br> Electric Doom</div> <br>
    <div>{{ desc }}</div> <br>
    <div>{{ text }}</div> <br>
    <input style="font-size: 0.8em; color: cyan" id="input" @keydown.enter="getInput()" autofocus=""> <br><br>
    <div style="color: darkgreen; font-size: 0.5em">{{ commandText }}</div> <br>

    <div id="stats" style="position: absolute; top: 100px; left: 100px; width: 300px; height: 250px; border: 3px solid darkblue; border-radius: 13px;padding: 5px">Hello</div>
    <div id="inv" style="position: absolute; top: 400px; left: 100px; width: 300px; height: 250px; border: 3px solid darkblue; border-radius: 13px;padding: 5px">Hello</div>
    <div id="log" style="position: absolute; top: 100px; right: 100px; width: 300px; height: 550px; border: 3px solid darkblue; border-radius: 13px;padding: 5px">Hello</div>

  </div>
</template>

<script>
function autoFocus() {
  document.getElementById('input').focus()
}
document.addEventListener("keydown", autoFocus)

export default {
  data() {
    return {
      desc: "Your surroundings!",
      text: "This just happened.",
      commandText: "",
      lastInput: "",
      player: {
        health: 100,

      },
      commands: [
        {
          value: "help",
          condition: function (state) {
            if (state.player.health > 0) {
              return true
            }
            return false
          },
          function: function (state) {
            state.text = "Type what you want to do."
          },
        },
        {
          value: "stats",
          condition: function (state) {
            if (state.player.health > 0) {
              return true
            }
            return false
          },
          function: function (state) {
            state.text = "Here are your character's stats."
          },
        }
      ]
    }
  },
  methods: {
    autoFocus() {
      document.getElementById('input').focus()
    },
    getInput() {
      let input = document.getElementById('input').value
      document.getElementById('input').value = ""
      this.lastInput = input
      this.runCommand(input)
    },
    runCommand(input) {

      let inputMatches = false //does the input match the command
      let commandAllowed = false //does the commands "condition" function return true
      let commandExists = false

      let pInput = input.split(" ") //parsed Input
      for (let i = 0; i < pInput.length; i++) {
        if (pInput[i] === "") {
          pInput.splice(i, 1)
          i--
        }
      } //remove empty words from input
      input = pInput[0]

      for (let i = 0; i < this.commands.length; i++) {
        inputMatches = (this.commands[i].value === input.toLowerCase())
        if (inputMatches) {
          commandExists = true
          commandAllowed = this.commands[i].condition(this)
          if (commandAllowed) {
            this.commands[i].function(this)
          } else {
            this.text = "You cannot do that now."
          }
        }
      }
      if (!commandExists) {
        this.text = "This command does not exist."
      }


      this.updatePossibleCommands()
    },
    updatePossibleCommands() {
      //print every command's value which has a "condition" function that returns True
      let string = ""
      for (let i = 0; i < this.commands.length; i++) {
        let commandAllowed = this.commands[i].condition(this)
        if (commandAllowed) {
          let t = this.commands[i].value
          t = '$' + t[0].toUpperCase() + t.substring(1) + ", " //uppercase first letter
          string = string.concat(t)
        }
      }
      this.commandText = string
    }
  },
}

</script>

<style>

body {
  background-color: black;
  font-size: 1.6em;
  font-weight: 0;
  letter-spacing: 0.04em;
  margin: 5%;
}

#app {
  -webkit-user-select: none; /* Safari */
  -ms-user-select: none; /* IE 10 and IE 11 */
  user-select: none; /* Standard syntax */
  font-family: "Rubik Dirt", sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  background-color: black;
  color: white;
}
#input {
  background-color: black;
  color: white;
  border: none;
  outline: none;
  font: inherit;
  text-align: inherit;
}
</style>
