<template>
  <div id="app">
    <div id = "column1"  style="width: 300px">
      <div v-show="running"  class="infobox" id="stats" style="top: 100px; left: 100px; width: 300px; height: 250px;">
        <div style="color: darkred; text-decoration: underline">Stats:</div>
      </div>
      <div v-show="running"  class="infobox" id="inv" style="top: 400px; left: 100px; width: 300px;">
        <div style="color: darkred; text-decoration: underline">Inventory:</div>
      </div>
    </div>
    <div id = "column2">
      <div v-show="!running" style="font-size: 2.0em; color: red">Blood and Thunder 2: <br> Electric Doom <br> </div>
      <div v-show="running" style="color: lightsalmon; margin: 0 auto; width: 600px">{{ desc }} </div> <br>
      <div style="margin: 0 auto; width: 600px">{{ text }} </div> <br>
      <input style="font-size: 0.8em; color: cyan" id="input" @keydown.enter.prevent="getInput()" @keydown.tab.prevent="autoComplete()" @keydown.esc.prevent="clearInput()" autofocus=""> <br><br>
      <div v-show="showCommands" style="color: darkgreen; font-size: 0.5em">{{ commandText }}</div> <br>
    </div>
    <div id = "column3" style="width: 300px;">
      <div v-show="running" id="log" class="infobox" style="top: 100px; right: 75px; width: 300px; height: 550px;">
      <div style="color: darkred; text-decoration: underline">Log:</div>
      <div id="entry" style="font-size: 0.6em"></div>
    </div>
    </div>

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
      desc: "Default_Text",
      text: "Ready to Start?",
      commandText: "$Start",
      lastInput: "",

      commands: [
        {
          value: "north",
          condition: function (state) {
            if (state.room.northDoor) {
              return true
            }
            return false
          },
          function: function (state) {
            state.text = "You have entered a new room."
            state.player.y++
          },
        },
        {
          value: "west",
          condition: function (state) {
            if (state.room.westDoor) {
              return true
            }
            return false
          },
          function: function (state) {
            state.text = "You have entered a new room."
            state.player.x--
          },
        },
        {
          value: "east",
          condition: function (state) {
            if (state.room.eastDoor) {
              return true
            }
            return false
          },
          function: function (state) {
            state.text = "You have entered a new room."
            state.player.x++
          },
        },
        {
          value: "south",
          condition: function (state) {
            if (state.room.southDoor) {
              return true
            }
            return false
          },
          function: function (state) {
            state.text = "You have entered a new room."
            state.player.y--
          },
        },

        {
          value: "start",
          condition: function (state) {
            if (!state.running) {
              return true
            }
            return false
          },
          function: function (state) {
            state.running = true
            state.text = "You have started the game!"
            state.loadGame()
          },
        },
        {
          value: "quit",
          condition: function (state) {
            if (state.running) {
              return true
            }
            return false
          },
          function: function (state) {
            state.running = false
            state.text = "You have exited the game!"
          },
        },
        {
          value: "commands",
          condition: function () {
            return true
          },
          function: function (state) {
            if (state.showCommands) {state.text = "Command hints disabled."}
            else {state.text = "Command hints enabled."}

            state.showCommands = !state.showCommands
          },
        },
      ],

      player: {
        health: 100,
        x: 1,
        y: 1,
      },

      floorSize: 10,
      rooms: null,
      room: null,
      log: [],
      logText: "",
      logLimit: 10,

      showCommands: true,
      running: false,

    }
  },
  methods: {
    autoFocus() {
      document.getElementById('input').focus()
    },
    logEntry(text, color) {
      let newEntry = {
        text: text,
        color: color,
      }
      this.log.unshift(newEntry)
      while(this.log.length > this.logLimit) {
        this.log.pop()
      }
    },
    updateLog() {
      if (this.running) {
        let entry = document.getElementById('entry')
        while (entry.firstChild) {
          entry.removeChild(entry.firstChild)
        }
        for (let i = 0; i < this.log.length; i++) {
          let br = document.createElement("br");
          document.body.appendChild(br);

          let text = document.createElement("div");
          text.style.color = this.log[i].color
          text.innerText = this.log[i].text
          entry.appendChild(text)

        }
      }
    },
    loadGame() {
      this.log = []
      this.rooms = new Array(this.floorSize).fill(0).map(() => new Array(this.floorSize).fill(0));
      this.player = {
        health: 100,
        x: 1,
        y: 1,
      }

      //generate terrain
      for (let x = 0; x < this.rooms[0].length; x++) {
        for (let y = 0; y < this.rooms.length; y++) {

          this.rooms[x][y] = this.getNewRoom(x, y)

        }
      }

      //trim doors
      for (let x = 0; x < this.rooms[0].length; x++) {
        for (let y = 0; y < this.rooms.length; y++) {
          if (y === 0) {
            this.rooms[x][y].southDoor = false
          }
          if (y === this.floorSize - 1) {
            this.rooms[x][y].northDoor = false
          }
          if (x === 0) {
            this.rooms[x][y].westDoor = false
          }
          if (x === this.floorSize - 1) {
            this.rooms[x][y].eastDoor = false
          }

        }
      }

      this.setText()
      this.updateLog()
    },
    getNewRoom(x, y) {
      let newRoom = {
        x: x,
        y: y,
        name: "default_name",

        westDoor: Math.random() > 0.0,
        northDoor: Math.random() > 0.0,
        eastDoor: Math.random() > 0.0,
        southDoor: Math.random() > 0.0,

        //npc

        //loot

        //textures

        //feature

        //lighting
      }
      newRoom.name = ("Room[" + newRoom.x + ", " + newRoom.y + "]")

      return newRoom
    },
    getRandomInt(max) {
      return Math.floor(Math.random() * max);
    },
    getInput() {
      let input = document.getElementById('input').value
      this.lastInput = input
      document.getElementById('input').value = ""

      let color = this.runCommand(input)
      color.length
      let text = input
      this.logEntry(text, color)
      this.setText()
    },
    setText() {
      this.updateLog()
      this.desc = "You are in " + this.rooms[this.player.x][this.player.y].name
    },
    autoComplete() {
      let text = document.getElementById('input').value

      for (let i = 0; i < this.commands.length; i++) {
        let inputMatches = false
        let commandAllowed = false
        for (let j = 0; j < this.commands[i].value.length; j++) {
          if (text.toLowerCase() === this.commands[i].value.substring(0, j)) {
            inputMatches = true

            try {
              if (this.commands[i].condition(this)) {
                commandAllowed = true
              }
            } catch (e) {
              e.ignore
            }
          }
        }

        if (inputMatches && commandAllowed) {
          document.getElementById('input').value = this.commands[i].value
        }
      }

    },
    clearInput() {
      document.getElementById('input').value = ""
    },
    runCommand(input) {
      let worked = false
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
            worked = true
          } else {
            this.text = "You cannot do that now."
          }
        }
      }
      if (!commandExists) {
        this.text = "This command does not exist."
      }

      this.room = this.rooms[this.player.x][this.player.y]

      this.updatePossibleCommands()

      return worked ? "green" : "red"
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
.infobox {
  border: 0px solid darkblue;
  border-radius: 13px;padding: 5px;
  text-align: center;
}
body {
  background-color: black;
  font-size: 1.6em;
  font-weight: 0;
  letter-spacing: 0.04em;
  margin-top: 5%;
  overflow: hidden; /* Hide scrollbars */
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
  display: grid;
  grid-template-columns: repeat(3, auto);
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
