<template>
    <div>
        <div>
            <input v-on:input="evenM(message = $event.target.value)" placeholder="Поиск" class="inp">
        </div>
        <div v-if="nfeeds.length > 0">
            <div v-for="feed in nfeeds" class="container">
                <div v-html:title="feed.title" class="topic"></div>
                <div v-html:img="feed.img" class="img"></div>
                <div v-html:description="feed.contentSnippet"></div>
            </div>
        </div>
        <div v-else>
            <p>Нет результатов</p>
        </div>
    </div>
</template>
<script>
    const CORS_PROXY = "https://cors-anywhere.herokuapp.com/";
    require('../../node_modules/rss-parser/dist/rss-parser.min.js');
    let parser = new RSSParser();
    export default {
        name: "news",
        components: {

        },
        data: () => ({
            feeds: [],
            cont: [],
            nfeeds: [],
            message: ''
        }),
        created() {
            parser.parseURL(CORS_PROXY + 'https://www.reg.ru/company/news/rss', (err, feed) => {
                for (let i = 0; i < feed.items.length; i++) {
                    this.feeds.push({
                        title: feed.items[i].title,
                        content: feed.items[i].content,
                        contentSnippet: feed.items[i].contentSnippet
                    });
                    this.cont.push({
                        content: feed.items[i].content
                    })
                }
                ;
                for (let i = 0; i < this.cont.length; i++) {
                    let m = this.cont[i].content.match(/<img[^>]+>/);
                    if (m != null) {
                        this.feeds[i].img = this.cont[i].content.substring(m.index, m.index + m[0].length)
                    }
                    else {
                        this.feeds[i].img = null
                    }
                }
                ;
                for (let i = 0; i < this.feeds.length; i++) {
                    this.nfeeds.push({
                        title: this.feeds[i].title,
                        img: this.feeds[i].img,
                        contentSnippet: this.feeds[i].contentSnippet
                    });


                }
            })

        },
        methods: {
            evenM: function (wsearch) {
                this.nfeeds = this.feeds.filter((its => (its.title.search(this.message ) !== -1) || (its.contentSnippet.search(this.message ) !== -1)));
            }
        },
    }
</script>

<style>
    .topic {
        font-size: 30px;
        font-weight: bold;
    }
    .container {
        margin-bottom: 350px;
    }

    .img {
        text-align: center;
    }

    .inp {
        width: 90vw;
        height: 50px;
        font-size: 30px;
        font-weight: bold;
    }

    @media  (min-width: 1223px){
        .b-news-article__right {
            float: right;
        }
    }
    @media  (max-width: 481px){
        .img {
            display: none;
        }
    }
</style>