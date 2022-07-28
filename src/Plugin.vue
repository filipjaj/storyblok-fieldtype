<template>
  <div class="wrapper">
    <button @click=openFullscreen v-if="!isFullscreen">Open</button>
    <div  v-else-if="isFullscreen">
     <button @click=closeFullscreen>Close</button>
    <div>
      <div>
        <h4>SubGrid controls</h4>
    <select v-model="model.columns">
      <option :key="option" v-for="option in columnOptions" :value="option" >
        {{ option }}
      </option>
    </select>
    <div>
      
      <button @click=moveLeft>
        ←
      </button>
        <button @click="cardPosition('center')">
          -
        </button>
         <button @click=moveRight>
        →
      </button>
    </div>
    </div>
    <div >
      <h4>Card controls</h4>
      <select v-model="model.cardSpan">
      <option :key="option" v-for="option in model.columns" :value="option" >
        {{ option }}
      </option>
    </select>
    <div class="cardPosition">
       <div></div>
        <button @click="cardPosition('flex-start', model.cardColumnStart)">
          ↑
        </button>
        <div></div>
         <button @click="cardPosition(model.cardPositionAlign,'-')">
        ←
      </button>
        <button @click="cardPosition('center', model.cardColumnStart)">
          -
        </button>
         <button @click="cardPosition(model.cardPositionAlign, '+')">
        →
      </button>
         <div></div>
        <button @click="cardPosition('flex-end', model.cardColumnStart)">
          ↓
        </button>
         <div></div>
        

    </div>
    </div>
    <div>

    </div>
  </div>
  <div class="container">
  <div class="mainGrid" >
   <div class="gridColumn" :id="column" v-for="column in mainGridSize - model.columns" :key="column" @drop="onDrop($event, column)"
  @dragover.prevent
  @dragenter.prevent></div>
  <div :style="gridStyle" class="subGrid" draggable
  @dragstart="startDrag($event)">
  <div :style="cardStyle">
    <p>Card</p>
  </div>

  </div>
  </div>
  </div>
  </div>
  </div>
</template>

<script>
export default {
  mixins: [window.Storyblok.plugin],
  methods: {
    initWith() {
      return {
        // needs to be equal to your storyblok plugin name
        plugin: 'columnSizing',
        columns: 1,
        columnStart: 1,
        cardSpan: 1,
        cardPositionAlign: 'center',
        cardColumnStart: 1,
       


        
      }
    },
    startDrag(evt) {
      
      evt.dataTransfer.dropEffect = 'move'
      evt.dataTransfer.effectAllowed = 'move'
     
    },
    onDrop(evt, column) {
      console.log(evt, column)
      this.model.columnStart = column
     
    },
    pluginCreated() {
      // eslint-disable-next-line
      console.log('View source and customize: https://github.com/storyblok/storyblok-fieldtype')
    },
     openFullscreen() {
      this.isFullscreen = true
      this.$emit('toggle-modal', true)
    },
    closeFullscreen() {
      this.isFullscreen = false
      this.$emit('toggle-modal', false)
    },
    moveRight() {
      if(this.model.columnStart + this.model.columns  <= 12) {
        this.model.columnStart++
      }
    },
    moveLeft() {
      if(this.model.columnStart > 1) {
        this.model.columnStart = this.model.columnStart - 1
      }
     
     
    },
    cardPosition(vertical, horizontal) {
      this.model.cardPositionAlign = vertical

       if(this.model.cardColumnStart + this.model.cardSpan  <= this.model.columns && horizontal == '+') {
        this.model.cardColumnStart++
      }
       if(this.model.cardColumnStart > 1 && horizontal == '-') {
        this.model.cardColumnStart--
      }
    },
     
      
    
  
  },
   data() {
    return {
      columnOptions: [1,2,3,4,5],
       isFullscreen: false,
       mainGridSize: 12,
       draged: false,
    }
  },
  computed: {
    gridStyle() {
      return {
        height: '100%',
        display: 'grid',
        gap: '30px',
        gridColumn: ` ${this.model.columnStart} / span ${this.model.columns}`,
        gridRow: '1 / span 1',
        gridTemplateColumns: `repeat(${this.model.columns}, 1fr)`,
        alignItems: this.model.cardPositionAlign,
        
      }
    },
    cardStyle() {
      return {
        display: 'flex',
        flexDirection: 'column',
        justifyContent: 'center',
        alignItems: 'center',
        aspectRatio: '4 / 5',
        backgroundColor: '#fff',
        borderRadius: '5px',
        boxShadow: '0px 0px 5px rgba(0, 0, 0, 0.1)',
         gridColumn: ` ${this.model.cardColumnStart} / span ${this.model.cardSpan}`,
        alignSelf: this.model.cardPositionAlign,
      }
    }
  },
  watch: {
    'model': {
      handler: function (value) {
        this.$emit('changed-model', value);
      },
      deep: true
    }
  }
}
</script>

<style>
  .subGrid{
   
    padding: 0;
    margin: 0;
    border: 1px solid rgb(0, 0, 0);
    height: 100%;
    background: #f5f4f4;
   
  }

  .mainGrid{
    display: grid;
    grid-template-columns: repeat(12, 1fr);
    gap: 30px;
   
    width: 100%;
    height: 100%;
   
  }

  .gridColumn{
    background: #f5f4f4;
    height: 100%;
    grid-column: span 1;
  }

  .wrapper{
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100%;
    width: 100%;
  }

  .container{
    margin-top: 25px;
    position: relative;
    width: 768px;
    height: 432px;
  }

  .cardPosition{
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    width: 50px;
    height: 50px;
  }

  

</style>
