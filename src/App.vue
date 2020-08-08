<template>
  <div id="app">
    <form class="form" @submit="sendFruit">
      <p class="error" v-if="error">The fields are required</p>
      <label for="fruitName" >Name: </label>
      <input class="inputFruit" id="fruitName" placeholder="Shopping Fruits" type="text" v-model="name">
      <label for="description">Description: </label>
      <textarea class="description"  id="description" placeholder="Description" cols="30" rows="10" v-model="description"></textarea>
      <button type="submit" class="btn-send"> Save </button>
    </form>
    <div class="fruits">
      <div class="fruit" v-for="({ name, description, id }) in fruits" :key="id">
        <h2>{{ name }}</h2>
        <p>{{ description }}</p>
         <div class="containerButtons">
           <button class="button btn-edit" type="button" @click="edit(id)"> Edit </button>
           <button class="button btn-delete" type="button" @click="remove($event, id)"> Delete </button>
         </div>
      </div>
    </div>
  </div>
</template>

<script>
  const dataFruits = localStorage.getItem('FRUITS');
  export default {
    name: 'app',
    data() {
      return {
        fruits: JSON.parse(dataFruits) || [],
        name: '',
        description: '',
        id: 0,
        idTemp: null,
        error: false 
      }
    },
    methods: {
      sendFruit(e){
        e.preventDefault();
        const data = { name: this.name, description: this.description, id: this.id };
        
        if(data.name.trim() === '' || data.description.trim() === '') return this.error = true;
        
        if(this.idTemp === null){
          this.fruits.push(data);
          this.id += 1;
        }else{
          const fruit = this.fruits.find(fruit => fruit.id === this.idTemp);
          fruit.name = this.name;
          fruit.description = this.description;
          this.idTemp = null;
        }
  
        this.name = '';
        this.description = '';
        this.error = false;
      },
  
      edit(id){
        const fruit = this.fruits.find(fruit => fruit.id === id);
        
        this.name = fruit.name;
        this.description = fruit.description;
        this.idTemp = id;
      },
     
      remove(e, id) {
        const fruitElement = e.target.parentNode.parentNode;
  
        fruitElement.classList.add('delete');
        setTimeout(() => {
            if(this.idTemp !== null) this.idTemp = null;
            if(this.fruits.length === 0) this.id = 0;
  
            return this.fruits = this.fruits.filter(fruit => fruit.id !== id);
          }, 500); 
      }
    },
    
    created() {
      const saveProductState = () => {
        localStorage.setItem('FRUITS', JSON.stringify(this.fruits));
      }; 
      
      window.addEventListener('unload', saveProductState);
    }
  };
  

</script>

<style>
  
  html {
    box-sizing: border-box;
  }

  *, *:before, *:after {
    box-sizing: inherit;
  }

  .form,
  .fruits{
    max-width: 800px;
    margin: 0 auto;
  }

  .form{
    padding: 20px;
    background-color: #d3d3d3;
    display: flex;
    flex-direction: column;
  }

  .inputFruit,
  .description{
    padding: 10px;
    margin: 10px 0;
    outline-color: white;
    border: none;
  }
  
  .inputFruit{
    margin-bottom: 20px;
  }
  .btn-send{
    padding: 10px;
    outline: none;
    border: none;
    background-color: white;
    transition: background .5s ease;
  }

  .btn-send:hover{
    cursor: pointer;
    background-color: #f2f2f2;
  }
  
  .fruits{
    margin-top: 20px;
  }
  
  .fruit{
    background-color: #00838f;
    padding: 10px 20px;
    animation-name: appear;
    animation-duration: .5s;
    word-break: break-all;
    color: white;
  }
  
  @keyframes appear{
    from{
      opacity: 0;
    }

    to{
      opacity: 1;
    }
  }
  
  .delete{
    animation-name: delete;
    animation-duration: 0.55s;
  }

  @keyframes delete{
    from{
      transform: translateX(0px);
    }
    
    to{
      transform: translateX(200px);
      opacity: 0;
    }
  }

  .fruit + *{
    margin-top: 15px;
  }
  
  .containerButtons{
    display: flex;
    justify-content: flex-end;
  }

  .button{
    border: none;
    padding: 8px 10px;
    font-size: 16px;
    min-width: 100px;
    border-radius: 4px;
    color: white;
    font-weight: bold;
  }
 
  .button + *{
    margin-left: 10px;
  }

  .btn-edit{
    background-color: #F4C937;
  }

  .btn-delete,
  .error{
    background-color: #E45E5E;
  }

  .error{
    color: white;
    padding: 15px;
    margin-bottom: 20px;
    animation-name: appear;
    animation-duration: .5s;
  }
</style>
