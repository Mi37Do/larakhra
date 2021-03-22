<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-8">
                <h1>Articles</h1>
                <form @submit.prevent="addArticle" method="post" action="./api/article" class="mb-2">
                    <div class="form-group">
                        <input type="text" class="form-control mb-2" placeholder="title" v-model="article.title">
                    </div>
                    <div class="form-group">
                        <textarea class="form-control" placeholder="body" v-model="article.body"></textarea>
                    </div>
                    <button type="submit" class="btn btn-light btn-block">save</button>
                </form>
                <button @click="clearForm()" class="btn btn-danger btn-block">Cancel</button>
                <hr>
                <nav aria-label="Page navigation example">
                    <pagination :data="articles" @pagination-change-page="makePagination"></pagination>
                </nav>
                <div class="card mb-2" v-for="(article,index) in articles.data" v-bind:key="article.id">
                    <div class="card-header">{{article.id}} . {{article.title}}</div>

                    <div class="card-body">
                        {{article.body}}
                    </div>
                    <hr>
                    <button @click="deleteArticle(article.id,index)" class="btn btn-danger mb-2">delete</button>
                    <button @click="editArticle(article)" class="btn btn-warning">edit</button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>

    export default {
        components: {},
        data() {
            return {
                articles: [],
                article : {
                    id :'',
                    title :'',
                    body : ''
                },
                article_id: '',
                pagination: {},
                edit: false,
            }
        },
        created() {
            this.getArticles();
        },
        methods: {
            getArticles() {
                axios.get('api/articles')
                    .then(response => {
                        console.log(response);
                        this.articles = response.data;

                    })
                    .catch(function (error) {
                        console.log(error);
                    })
            },

            makePagination(page = 1) {
                axios.get('api/articles?page=' + page)
                    .then(response => {
                        this.articles = response.data;
                    });
            },
            deleteArticle(id,index){
                if(confirm('are you sure')){
                    axios.delete('api/article/'+id)
                        .then(response => {
                            this.articles.splice(index,1)
                            console.log(this.articles);
                        }).catch(function (error) {
                        console.log(error);
                    }).then(response =>{
                    alert('article removed');
                    this.getArticles();});
                }

            },
            makePagination(page = 1) {
                axios.get('api/articles?page=' + page)
                    .then(response => {
                        this.articles = response.data;
                    });
            },
            addArticle(){
                if( this.edit === false){
                    //add
                    axios.post('api/article', {
                        title: this.article.title,
                        body: this.article.body,
                    })
                        .then(function (response) {
                            console.log(response);
                            alert('data added');
                            this.getArticles();
                            this.clearForm();
                        })
                        .catch(function (error) {
                            console.log(error);
                        });
                }else{
                    //update
                    axios.put('api/article', {
                        title: this.article.title,
                        body: this.article.body,
                    })
                        .then(function (response) {
                            console.log(response);
                            alert('data updated');
                            this.getArticles();
                            this.clearForm();
                        })
                        .catch(function (error) {
                            console.log(error);
                        });

                }
            },
            editArticle(article){
                this.edit = true;
                this.article.id = article.id;
                this.article.article_id = article.id;
                this.article.title = article.title;
                this.article.body = article.body;
            },
            clearForm() {
                this.edit = false;
                this.article.id = null;
                this.article.article_id = null;
                this.article.title = '';
                this.article.body = '';
            }
        }
    }
</script>
