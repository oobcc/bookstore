<script setup lang="ts">
import type { Book } from "@/api/books";
import { searchBook } from "@/api/books";
import { useRoute } from "vue-router";
import { computedAsync } from "@vueuse/core";
import { ref, watch } from "vue";
import { imgPath } from "@/api/index";

const route = useRoute();
const searchStr = ref(route.params.search as string);
const bookPath = "/book/";

const books = computedAsync(async () => {
    const res = await searchBook(searchStr.value);
    return res.data.data as Array<Book>;
}, []);

watch(
    () => route.params.tag,
    (newtag) => {
        searchStr.value = newtag as string;
        document.title = searchStr.value;
    }
);

function init() {
    document.title = searchStr.value;
}
init();
</script>

<template>
    <div class="main">
        <ul>
            <p style="text-align: center">查询出{{ books.length }}个结果</p>
            <li v-for="(item, index) in books">
                <routerLink :to="bookPath + item.id">
                    <el-tooltip placement="top-start">
                        <template #content>
                            <p>{{ item.name }}</p>
                            <p v-for="line in item.introduction.split('\n')">
                                {{ line }}
                            </p>
                        </template>
                        <el-image
                            style="width: 150px; height: 200px"
                            :src="imgPath + item.cover"
                            fit="cover"
                        >
                        </el-image>
                    </el-tooltip>
                </routerLink>
            </li>
        </ul>
    </div>
</template>
<style scoped>
.main {
    width: 1500px;
    margin: auto;
    margin-top: 30px;
}

li {
    list-style: none;
    display: inline;
    width: 250px;
    margin-bottom: 50px;
}

.el-image {
    margin: 90px 20px 20px 90px;
}
</style>
