<template>
    <div class="row">
        <div class="col-md-6 mx-auto">
            <div class="row">
                <div class="col-sm-8">
                    <input v-model="email" type="email" class="form-control" placeholder="Enter Email">
                </div>
                <div class="col-sm-4">
                    <button @click="generateKey()" type="submit" class="btn btn-primary btn-block">Generate Key</button>
                </div>
            </div>
        </div>
        <div class="col-md-10 mx-auto">
            <table class="table table-sm custab">
                <thead class="thead-light">
                    <th>Account</th>
                    <th>Key</th>
                    <th>Date</th>
                    <th>Action</th>
                </thead>
                <tbody>
                    <tr v-for="named_key in named_keys" v-bind:key="named_key.id">
                        <td>{{named_key.account.email}}</td>
                        <td>{{named_key.hash_key}}</td>
                        <td>{{named_key.created}}</td>
                        <td>
                            <button v-on:click="deleteOne(named_key)" class="btn bn-sm btn-danger">Delete</button>
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>

    </div>
</template>

<script>
import axios from 'axios';
const API_URL = 'https://keygen-api-production.herokuapp.com/api/keys/';

export default {
    name: 'Container',
    props: {
        msg: String
    },
    data(){
        return{
            named_keys:null,
            email:''
        }
    },
    mounted(){
        this.getAll()
        console.log(process.env.ROOT_API)
    },
    methods:{
        getAll(){
            axios.get(API_URL)
                .then((res)=>{
                    this.named_keys=res.data;
                    this.email=""
                })
        },
        deleteOne(named_key){
            if (confirm(`Delete ${named_key.account.email} | ${named_key.hash_key}`)){
                axios.delete(`${API_URL}${named_key.id}`)
                .then(()=>{
                    this.getAll();
                    })
                .catch((err) => {
                    console.log(err);
                    alert(`Error deleting key ${named_key.account.email} | ${named_key.hash_key}`)
                })
            }
        },
        generateKey(){
            if(this.email===''){
                alert('Email required')
            }
            else {

                axios.post(API_URL, {'account': {'email': this.email}})
                .then(() => {
                    this.getAll();
                })
                .catch((err) => {
                    console.log(err);
                    alert(`Error Generating key for ${this.email}`)
                })
            }
        }
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.custab{
    border: 1px solid #ccc;
    padding: 5px;
    margin: 5% 0;
    box-shadow: 3px 3px 2px #ccc;
    transition: 0.5s;
    }
.custab:hover{
    box-shadow: 3px 3px 0px transparent;
    transition: 0.5s;
    }
</style>
