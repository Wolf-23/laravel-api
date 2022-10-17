<template>
        <main class="container">
            <h1 class="mt-3">Liste dei post:</h1>
            <div v-if="loading" class="d-flex justify-content-center">
                <div class="spinner-border" role="status">
                    <span class="sr-only">Caricamento...</span>
                </div>
            </div>
            <div v-else>
                <div class="card my-3 p-2 rounded" v-for="(post, index) in posts" :key="index">
                    <img :src="post.media" class="card-img-top" :alt="post.title">
                    <div class="card-body">
                        <h5 class="card-title">Titolo: {{post.title}}</h5>
                        <h5 class="card-title">Scritto da: {{post.author}}</h5>
                        <p class="card-text">Contenuto: {{substringText(post.content)}}</p>
                        <p class="card-text">Categoria: {{post.category?post.category.name:'Nessuna Categoria'}}</p>
                        <h5>Tags:</h5>
                        <div v-if="(post.tags.length > 0)">
                            <ul v-for="(tag, x) in post.tags" :key="x">
                                <li class="card-text">
                                    {{  tag.name }}
                                </li>
                            </ul>
                        </div>
                        <div v-else>
                            <strong>Nessun Tag</strong>
                        </div>
                        <a href="#" class="btn btn-primary">Show More</a>
                    </div>
                </div>
            </div>
            <nav>
                <ul class="pagination">
                    <li class="page-item" :class="(currentPage==1)?'disabled':''"><a class="page-link"  href="#" @click.prevent="getPosts(currentPage - 1)">Precedente</a></li>
                    <li class="page-item" :class="(currentPage==lastPage)?'disabled':''"><a class="page-link"  href="#" @click.prevent="getPosts(currentPage + 1)">Successiva</a></li>
                </ul>
            </nav>
        </main>
</template>

<script>
    import Axios from 'axios';

    export default {
        name: 'MyMain',
        data() {
            return {
                posts: [],
                loading: true,
                currentPage: 1,
                lastPage: null
            }
        },
        methods: {
            getPosts(numPage) {
                this.loading = true;

                Axios.get('/api/posts', { params: {page: numPage} })
                .then((response) => {
                    this.posts = response.data.results.data;
                    this.loading = false;
                    this.currentPage = response.data.results.current_page;
                    this.lastPage = response.data.results.last_page;
                });
            },
            substringText(text) {
                if(text.length < 100) {
                    return text;
                } else {
                    return text.substring(0, 100) + '..';
                }
            }
        },
        mounted() {
            this.getPosts(1);
        }
    }
</script>
