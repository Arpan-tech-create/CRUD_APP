# CRUD_APP
Using FLASK,CSS,SQLITE3


#CSS

.logo {
  width: 50px;
  float: left;
  margin-right: 15px;
}

.form-group {
  max-width: 500px;
}

.actions {
  padding: 10px 0;
}

.glyphicon-euro {
  font-size: 12px;
}





#js 


var products = [
  {id: 1, name: 'Angular', description: 'Superheroic JavaScript MVW Framework.', price: 100},
  {id: 2, name: 'Ember', description: 'A framework for creating ambitious web applications.', price: 100},
  {id: 3, name: 'React', description: 'A JavaScript Library for building user interfaces.', price: 100}
];

function findProduct (productId) {
  return products[findProductKey(productId)];
};

function findProductKey (productId) {
  for (var key = 0; key < products.length; key++) {
    if (products[key].id == productId) {
      return key;
    }
  }
};

var List = Vue.extend({
  template: '#product-list',
  data: function () {
    return {products: products, searchKey: ''};
  },
  computed : {
    filteredProducts: function () {
    var self = this;
    console.log()
    return self.products.filter(function (product) {
      return product.name.indexOf(self.searchKey) !== -1
    })
  }
}
});

var Product = Vue.extend({
  template: '#product',
  data: function () {
    return {product: findProduct(this.$route.params.product_id)};
  }
});

var ProductEdit = Vue.extend({
  template: '#product-edit',
  data: function () {
    return {product: findProduct(this.$route.params.product_id)};
  },
  methods: {
    updateProduct: function () {
      var product = this.product;
      products[findProductKey(product.id)] = {
        id: product.id,
        name: product.name,
        description: product.description,
        price: product.price
      };
      router.push('/');
    }
  }
});

var ProductDelete = Vue.extend({
  template: '#product-delete',
  data: function () {
    return {product: findProduct(this.$route.params.product_id)};
  },
  methods: {
    deleteProduct: function () {
      products.splice(findProductKey(this.$route.params.product_id), 1);
      router.push('/');
    }
  }
});

var AddProduct = Vue.extend({
  template: '#add-product',
  data: function () {
    return {product: {name: '', description: '', price: ''}
    }
  },
  methods: {
    createProduct: function() {
      var product = this.product;
      products.push({
        id: Math.random().toString().split('.')[1],
        name: product.name,
        description: product.description,
        price: product.price
      });
      router.push('/');
    }
  }
});

var router = new VueRouter({
  routes: [{path: '/', component: List},
    {path: '/product/:product_id', component: Product, name: 'product'},
    {path: '/add-product', component: AddProduct},
    {path: '/product/:product_id/edit', component: ProductEdit, name: 'product-edit'},
  {path:   '/product/:product_id/delete', component: ProductDelete, name: 'product-delete'}
]});

new Vue({
  el: '#app',
  router: router,
  template: '<router-view></router-view>'
});



#html


<!DOCTYPE html>
<html >
<head>
  <meta charset="UTF-8">
  <title>Vue.js v2.0 - CRUD application</title>


  <link rel='stylesheet prefetch' href='https://cdnjs.cloudflare.com/ajax/libs/twitter-bootstrap/3.3.7/css/bootstrap.min.css'>
  <link rel='stylesheet prefetch' href='https://cdnjs.cloudflare.com/ajax/libs/twitter-bootstrap/3.3.7/css/bootstrap-theme.min.css'>
  <link rel="stylesheet" href="src/css/style.css">


</head>

<body>
<div class="container">
  <header class="page-header">
    <div class="branding">
      <img src="https://vuejs.org/images/logo.png" alt="Logo" title="Home page" class="logo"/>
      <h1>Vue.js v2 CRUD</h1>
    </div>
  </header>
  <main id="app"></main>
</div>

<template id="product-list">
  <section>
  <div class="actions">
    <router-link class="btn btn-default" :to="{path: '/add-product'}">
      <span class="glyphicon glyphicon-plus"></span>
      Add product
    </router-link>
  </div>
  <div class="filters row">
    <div class="form-group col-sm-3">
      <label for="search-element">Product name</label>
      <input v-model="searchKey" class="form-control" id="search-element" requred/>
    </div>
  </div>
  <table class="table">
    <thead>
    <tr>
      <th>Name</th>
      <th>Description</th>
      <th>Price</th>
      <th class="col-sm-2">Actions</th>
    </tr>
    </thead>
    <tbody>
    <tr v-for="product in filteredProducts">
      <td>
        <router-link :to="{name: 'product', params: {product_id: product.id}}">{{ product.name }}</router-link>
      </td>
      <td>{{ product.description }}</td>
      <td>
        {{ product.price }}
        <span class="glyphicon glyphicon-euro" aria-hidden="true"></span>
      </td>
      <td>
        <router-link class="btn btn-warning btn-xs" :to="{name: 'product-edit', params: {product_id: product.id}}">Edit</router-link>
        <router-link class="btn btn-danger btn-xs" :to="{name: 'product-delete', params: {product_id: product.id}}">Delete</router-link>
      </td>
    </tr>
    </tbody>
  </table>
  </section>
</template>

<template id="add-product">
    <section>
  <h2>Add new product</h2>
  <form v-on:submit="createProduct">
    <div class="form-group">
      <label for="add-name">Name</label>
      <input class="form-control" id="add-name" v-model="product.name" required/>
    </div>
    <div class="form-group">
      <label for="add-description">Description</label>
      <textarea class="form-control" id="add-description" rows="10" v-model="product.description"></textarea>
    </div>
    <div class="form-group">
      <label for="add-price">Price, <span class="glyphicon glyphicon-euro"></span></label>
      <input type="number" class="form-control" id="add-price" v-model="product.price"/>
    </div>
    <button type="submit" class="btn btn-primary">Create</button>
    <router-link to="'/'" class="btn btn-default">Cancel</router-link>
  </form>
</section>
</template>

<template id="product">
    <section>
  <h2>{{ product.name }}</h2>
  <b>Description: </b>
  <div>{{ product.description }}</div>
  <b>Price:</b>
  <div>{{ product.price }}<span class="glyphicon glyphicon-euro"></span></div>
  <br/>
  <span class="glyphicon glyphicon-arrow-left" aria-hidden="true"></span>
  <router-link to="'/'">Back to product list</router-link>
</section>
</template>

<template id="product-edit">
    <section>
  <h2>Edit product</h2>
  <form v-on:submit="updateProduct">
    <div class="form-group">
      <label for="edit-name">Name</label>
      <input class="form-control" id="edit-name" v-model="product.name" required/>
    </div>
    <div class="form-group">
      <label for="edit-description">Description</label>
      <textarea class="form-control" id="edit-description" rows="3" v-model="product.description"></textarea>
    </div>
    <div class="form-group">
      <label for="edit-price">Price, <span class="glyphicon glyphicon-euro"></span></label>
      <input type="number" class="form-control" id="edit-price" v-model="product.price"/>
    </div>
    <button type="submit" class="btn btn-primary">Save</button>
    <router-link to="'/'" class="btn btn-default">Cancel</router-link>
  </form>
</section>
</template>

<template id="product-delete">
    <section>
  <h2>Delete product {{ product.name }}</h2>
  <form v-on:submit="deleteProduct">
    <p>The action cannot be undone.</p>
    <button type="submit" class="btn btn-danger">Delete</button>
    <router-link to="'/'" class="btn btn-default">Cancel</router-link>
  </form>
</section>
</template>


<script src='node_modules/vue/dist/vue.js'></script>
<script src='node_modules/vue-router/dist/vue-router.js'></script>
<script src="src/js/index.js"></script>

</body>
</html>
