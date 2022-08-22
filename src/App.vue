
<script>
const base_URL = 'http://18.177.140.79:8080/';
let imgarray;
let bookschapterdata;
let i = 0;
let j = 0;
//let imgdata;
export default {
  name: "App",
  data() {
    return {
      booksdata: null,
      Chapters: null,
      BooksChapter: null,
      ChapterImg: null,
      totalchapterpages: null,
      imgindex: 0,
      imgsrc: null,
      activeItem: null,
      activeBtn: null,
      activeChItem: null,
    }
  },
  methods: {
    async getBooks() {
      let response = await fetch(`${base_URL}books/`);
      let data = await response.json();
      this.booksdata = data;
      //console.log(this.booksdata);
    },
    async getBooksChapter(bookid) {
      let response = await fetch(`${base_URL}books/${bookid}/`);
      let data = await response.json();
      this.BooksChapter = data;
      this.getChapterImg(this.BooksChapter.chapter_ids[0]);
      bookschapterdata = JSON.stringify(this.BooksChapter.chapter_ids);
      //console.log(this.BooksChapter);
    },
    async getChapterImg(chapterid) {
      let response = await fetch(`${base_URL}chapters/${chapterid}/`);
      let data = await response.json();
      this.ChapterImg = data;
      imgarray = JSON.stringify(this.ChapterImg);
      let k = 0;
      this.switchImage(k);
      //console.log(imgarray);
      //console.log(this.ChapterImg);
    },
    async switchImage(index) {
      i = index;
      let imgdata = await JSON.parse(imgarray);
      //let currentchapter = imgdata.id;
      this.totalchapterpages = await imgdata.pages.length; //total number of pages in chapter
      //let imgfirstpage = imgdata.pages[0].page_index;
      //let imglastpage = imgdata.pages[imgdata.pages.length - 1].page_index;
      //let imgnextpage = imgpages + 1;
      this.imgsrc = await imgdata.pages[i];
    },
    nextImage() {
      // console.log(i);
      let imgdata = JSON.parse(imgarray);
      let totalchapterpages = imgdata.pages.length;
      if (i < totalchapterpages - 1) {
        i++;
        this.switchImage(i);
        //console.log('if ' + i);
        j = imgdata.chapter_index;
        //console.log('if id ' + j);
      }
      else {
        let bookchdata = JSON.parse(bookschapterdata);
        if (j < bookchdata.length - 1) {
          j++;
          this.getChapterImg(bookchdata[j]);
          this.switchImage(0);
          this.selectChitem(j);
          //console.log('else j ' + j);
        }
        else {
          console.log("Last Chapter");
          //this.switchImage(0);
        }
        //console.log(bookchdata);
        //console.log("last page go to next chapter function");
      }
      //console.log(i);
    },
    prevImage() {
      let imgdata = JSON.parse(imgarray);
      let totalchapterpages = imgdata.pages.length;
      j = imgdata.chapter_index;
      //console.log(j);
      if (i > 0) {
        i--;
        this.switchImage(i);
        // console.log('if ' + i);
        // console.log('if id ' + j);
      }
      else {
        let bookchdata = JSON.parse(bookschapterdata);
        if (j > 0) {
          j--;
          // console.log('else j ' + j);
          this.getChapterImg(bookchdata[j]);
          // console.log(bookchdata[j]);
          this.switchImage(imgdata.pages.length - 1);
          this.selectChitem(j);
        }
        else {
          console.log("First Chapter, no more previous chapters.");
        }
        //console.log("first page go to prev chapter function");
      }
      //console.log(i);
    },
    selectitem(i) {
      this.activeItem = i;
      //console.log('Book Item:' + this.activeItem);
      this.selectChitem(0);
    },
    selectChitem(i) {
      this.activeChItem = i;
      //console.log('Ch Item' + this.activeChItem);
    }
  },
  beforeMount() {
    this.getBooks(),
      this.getBooksChapter(1),
      this.getChapterImg(1);
    //this.switchImage();
    this.selectitem(0);
    this.selectChitem(0);
  }
}
</script>

<template>

  <div id="" class="main-box">
    <div class="manga">
      <div class="bookslist">
        <template v-for="(book, i) in booksdata">
          <button class="btn btn-primary books" :class="{ active: i === activeItem }"
            v-on:click="getBooksChapter(book.id); selectitem(i)">
            {{ book.title }}</button>
        </template>
      </div>
      <div v-if="BooksChapter" class="chapterlist">
        <template v-for="(chapter, index) in BooksChapter.chapter_ids">
          <button class="btn btn-primary chapters" :class="{ active: index === activeChItem }"
            v-on:click="getChapterImg(chapter); selectChitem(index)">
            {{ index + 1 }}</button>
        </template>
      </div>

      <!--div>
        <p> Chapter Group {{ BooksChapter.chapter_ids }}</p>
        <!-p> {{ ChapterImg.pages }}</p->
        <p> Current Chapter {{ ChapterImg.id }}</p>
        <p> Total Img id {{ imgsrc.id }}</p>
        <p> Page-Index {{ imgsrc.page_index }}</p>
        <p> Page No. {{ imgsrc.page_index + 1 }}</p>
        <p> total pages {{ totalchapterpages }}</p>
        <br>
      </div-->

      <div v-if="ChapterImg" class="manga-image">
        <img v-if="imgsrc" class="manga-img" v-bind:src="imgsrc.image.file">
        <div class="forward-page overlay" @click="nextImage()"></div>
        <div class="back-page overlay" @click="prevImage()"></div>
        <!--div class="forward-page overlay" @click="nextImage()">Next Page</div>
        <div class="back-page overlay" @click="prevImage()">Prev Page</div>
        <!-img class="manga-img" :src="ChapterImg.pages[1].image.file"-->
      </div>

      <div class="page-no" v-if="imgsrc">
        <p>{{ imgsrc.page_index + 1 }}/{{ totalchapterpages }}</p>
      </div>
    </div>
  </div>
</template>

<style>
* {
  margin: 0;
  padding: 0;
}

#app {
  padding: 5px;
  width: auto;
  height: 100%;
  background-color: #e4e4e4;
}

.main-box {
  background-color: white;
  width: 400px;
  height: auto;
  margin: 10px 0 10px 40%;
  display: block;
  align-items: center;
  outline: 1px;
  outline-color: rgb(107, 107, 107);
  outline-style: solid;
}

.bookslist {
  padding: 10px 0 0 0;
  display: flex;
  justify-content: center;
}

.books {
  padding: 0 3px 0 3px;
  margin: 0 1px 0 1px;
}

.active {
  border-color: rgb(255, 190, 25);
  border-width: 2px;
  border-radius: 2px;
  color: white;
  background-color: rgb(0, 96, 99)
}

.chapters {
  padding: 0 5px 0 5px;
}

.chapterlist {
  padding: 5px 0 10px 0;
  display: flex;
  justify-content: center;
}

.chapters:focus {
  outline: none;
  color: white;
  background-color: rgb(0, 96, 99);
}

.page-no {
  padding: 10px 0 10px 0;
  display: flex;
  justify-content: center;
}

.manga-image {
  position: relative;
}

.manga-img {
  display: block;
  margin: auto;
  max-width: 370px;
  max-height: auto;
  /*outline: 5px;
  outline-color: red;
  outline-style: dashed;*/
}

.overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  /*background-color: rgba(0, 0, 0, 0.5);*/
  z-index: 1;
}

.back-page {
  position: absolute;
  width: 200px;
  height: 100%;
  /*outline: 2px;
  outline-color: rgb(251, 255, 0);
  outline-style: dashed;*/
  left: 50%;
}

.back-page:hover {
  cursor: e-resize;
}

.forward-page {
  width: 200px;
  height: 100%;
  /*outline: 2px;
  outline-color: rgb(255, 0, 234);
  outline-style: dashed;*/
}

.forward-page:hover {
  cursor: w-resize;
}
</style>