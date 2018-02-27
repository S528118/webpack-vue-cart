<template>
  <div class="hello">
    <div v-if="some>20 ">I Have many phones </div>
    <h5>I have {{products.length}} Phones</h5>
    <div class="row">
      <div class="col-md-6">
          <ul class="list-group">
            <li class="list-group-item" v-for="product in products" :key="product.id">
              {{product.name}}
              
              <a href="#" @click.prevent='addToCart(product)'>add to cart</a>
              ({{product.qty}}) -- {{product.price}}$
            </li>
            
          </ul> 
      </div>
      <div class="col-md-6">
         <div>
            Name : <input type="text" class="form-control" v-model="newItem" placeholder="Enter Name"><br>
            Qty  : <input type="text" class="form-control"  v-model="newQty" placeholder="Enter Quantity"><br>
            Price: <input type="text" v-model="newPrice" class="form-control"  placeholder="Enter Price"><br>
            <button @click='addItem' class="btn btn-primary">Add</button>
            </div>
          <div>
          
            <h1>Cart({{cart.length}})</h1> 
              <ul class="list-group">
                <li class="list-group-item" v-for='value in cart' :key="value.id">
                  {{value.name}} ({{value.quantity}}) --- $({{value.totalItemPrice}})
                </li>
              </ul>
          </div>

          Subtotal Total Cart Price --------- ${{total}} <br><br>

          <input type="text" placeholder="Enter Discount %" @input="discountEditing">
          
            Total Price --------------------- ${{ finalTotal }}
      </div>
    </div>
   
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
     message:'Hello',
    newItem:'',
    disc:0,
    coupon: 0,
    some:200,
    newQty:10,
    newPrice:10,
    totalItemPrice:0,
    products: [
      {
        name:'samsung',
        qty:10,
        price:10,
      },
      {
        name:'iPhone',
        qty:10,
        price:10,
      },
      {
        name:'MI',
        qty:10,
        price:10,
      }
    ],
    cart:[],

    }
  },
  mounted () {
    axios.get('./static/products.json').then(response => {
      this.products = response.data
    })
  },
  methods:{ 
    discountEditing (e) {
      this.disc = e.target.value
    },
    applyDiscount(coupon) {
      // console.log(e.target.value)
      return this.total - this.coupon
    },
    addItem(){
      if(this.newItem == '')
      {
        alert('Enter value')
      }
      else if(this.products.find(product => product.name=== this.newItem))
      {
        alert('Item already exists')
      }
      else
      {
        if(this.newQty == '' || isNaN(this.newQty) == true )
        {
          alert('Enter a numeric value')
        }
        else if(this.newPrice == '' || isNaN(this.newPrice) == true){
          alert('Enter Numberic value in price')
        }
        else{
           this.products.push(
           {
              name:this.newItem,
              qty: this.newQty,
              price: this.newPrice
             
           })
           this.newItem=''
           this.newQty=''
           this.newPrice=''
        }
          
      }
    
    },
    
    addToCart(item){
    
      let existItem = this.cart.find(cartItem => cartItem.id === item.id)
      let productItem = this.products.find(product => product.id === item.id)
      if(existItem)
        {
          if(existItem.quantity < productItem.qty)
          {
            existItem.quantity++
            existItem.totalItemPrice+=parseFloat(existItem.price)
          }
          else{
            alert("Product out of stock")
          }
          
        } 
      
      else
        this.cart.push({
          name: item.name,
          quantity: 1,
          id : item.id,
          price: item.price,
          totalItemPrice: parseFloat(item.price)
        })
    }
 },

computed:{
  finalTotal () {
   return this.total - ((this.total * this.disc)/100);
  },
  total(){
     let totalCartPrice = 0.0
      
      this.cart.forEach(item => {
        totalCartPrice += item.totalItemPrice
      })

    return totalCartPrice;
  },

}

}
</script>

