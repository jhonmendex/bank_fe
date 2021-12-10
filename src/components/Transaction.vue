<template>


<div class="wrapper-transaction">
<div class="container_logIn_user container-transaction">
  <h1>Realice una transacción</h1>
  <form v-on:submit.prevent="createTransaction">
   <input type="text" v-model="transaction.usernameDestiny" placeholder="Digite la cuenta destino" />
   <input type="number" v-model="transaction.value" placeholder="Digite el valor a enviar" />
   <input type="submit" value="Crear Transacción" />
  </form>
  </div>
  </div>
</template>

<script>
import ggl from "graphql-tag";
export default {
 name: "Transaction",
 data: function() {
   return {
     transaction:{
        usernameOrigin: localStorage.getItem("username"),
        usernameDestiny: "",
        value: "",
     }
   }
 },
 
 methods: {
   createTransaction: async function(){
     
  localStorage.setItem("token_access", "")
 
    await this.$apollo.mutate({
      mutation:  ggl `
      mutation RefreshToken($refresh: String!) {
      refreshToken(refresh: $refresh) {
      access
      }
      }`,
      variables:{
        refresh: localStorage.getItem("token_refresh"),
        }
    }).then((response)=>{
      let tokenaccess = response.data.refreshToken.access;
      localStorage.setItem("token_access",tokenaccess)
       console.log(response)

    }).catch((error)=>{
      console.log(error)
    })


     await this.$apollo.mutate({
       mutation: ggl `
       mutation CreateTransaction($transaction: TransactionInput!) {
        createTransaction(transaction: $transaction) {
          id
          usernameOrigin
          usernameDestiny
          value
          date
          }
        }
       `,
       variables:{
          transaction: this.transaction,
       }
     })
     .then(()=>{
       alert("transacción realizada con éxito");
     })
     .catch((error)=>{
       console.log(error)
       alert("Transacción fallida, saldo insuficiente o no existe la cuenta destino ")
     })
   }
 }
}

/*
 
{
  "transaction": {
     "usernameDestiny": "yeisonr",
      "usernameOrigin": "jhonmendex",
       "value": 20000
  }
}
*/
</script>

<style>
  .wrapper-transaction {
    width: 100%;
  }

  .container-transaction{
    margin: 0 auto;
    width: 50%;
    padding: 10px;
    margin-top: 50px;
  }

  .container-transaction input{
    padding: 10px;
    margin: 5px;
    width: 80%
  }
</style>