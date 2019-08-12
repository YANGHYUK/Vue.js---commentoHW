<template>
  <div>
    <!-- hihi -->

    <div class="row">
      <div class="col-lg-12 col-md-12 col-sm-12">
        <div class="title">
          <nav aria-label="breadcrumb">
            <ol class="breadcrumb">
              <li class="breadcrumb-item">
                <img class="logo" src="../../assets/logo.png" />
                <a href="#">
                  <h2>This Is List</h2>
                </a>
              </li>
            </ol>
          </nav>
        </div>
      </div>
    </div>

    <!-- 리스트 -->
    <div class="row">
      <div class="col-lg-12 col-md-12 col-sm-12">
        <div class="box">
          <!-- Button trigger modal -->

          <div>
            <div>
              <b-button id="show-btn" @click="showModal">카테고리 필터</b-button>

              <b-modal ref="my-modal" hide-footer title="카테고리 필터">
                <div class="d-block text-center">
                  <div class="categoryNum">
                    <span
                      v-for="(ele, idx) in category"
                      v-bind:key="(idx)"
                      v-on:click="clickCategory(ele)"
                    >
                      <span>No.{{ele}}</span>
                      <input type="checkbox" />
                    </span>
                  </div>
                </div>
                <b-button class="mt-3" block @click="hideModal">나가기</b-button>
                <b-button class="mt-2" variant="primary" block @click="setCategory">선택하기</b-button>
              </b-modal>
            </div>

            <!-- 탭 타이틀순에 따른 ( 오름차순, 내림차순) -->

            <b-tabs content-class="mt-3" align="right">
              <b-tab title="전체보기" active v-on:click="resetCategory">
                <ul class="list-group">
                  <div
                    class="list-group-item scrollBox"
                    v-for="(articles, idx) in list"
                    v-bind:key="(idx)"
                  >
                    <div class="list-header">
                      <div class="category">category_No.:{{articles.category_no}}</div>
                      <div class="listNum">No_:{{articles.no}}</div>
                    </div>
                    <div class="list-body">
                      <span class="list-email">Email :{{articles.email}}</span>
                      <span>||</span>
                      <span class="list-date">Date :{{articles.updated_at}}</span>
                    </div>
                    <li class="list-content">
                      title: {{articles.title}}
                      <p>contents:{{articles.contents}}</p>
                    </li>
                  </div>
                </ul>
              </b-tab>
              <b-tab title="오름차순" v-on:click="clickOrderedList">
                <ul class="list-group">
                  <div
                    class="list-group-item scrollBox"
                    v-for="(articles, idx) in orderedList"
                    v-bind:key="(idx)"
                  >
                    <div class="list-header">
                      <div class="category">category:{{articles.category_no}}</div>
                      <div class="listNum">No_:{{articles.no}}</div>
                    </div>
                    <div class="list-body">
                      <span class="list-email">Email :{{articles.email}}</span>
                      <span>||</span>
                      <span class="list-date">Date :{{articles.updated_at}}</span>
                    </div>
                    <li class="list-content">
                      title: {{articles.title}}
                      <p>contents:{{articles.contents}}</p>
                    </li>
                  </div>
                </ul>
              </b-tab>
              <b-tab title="내림차순" v-on:click="clickReversedOrderedList">
                <ul class="list-group">
                  <div
                    class="list-group-item scrollBox"
                    v-for="(articles, idx) in reversedOrderedList"
                    v-bind:key="(idx)"
                  >
                    <div class="list-header">
                      <div class="category">category:{{articles.category_no}}</div>
                      <div class="listNum">No_:{{articles.no}}</div>
                    </div>
                    <div class="list-body">
                      <span class="list-email">Email :{{articles.email}}</span>
                      <span>||</span>
                      <span class="list-date">Data :{{articles.updated_at}}</span>
                    </div>
                    <li class="list-content">
                      title: {{articles.title}}
                      <p>contents:{{articles.contents}}</p>
                    </li>
                  </div>
                </ul>
              </b-tab>
            </b-tabs>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>


<script>
import axios from "axios";

export default {
  //바로 실행하는 함수
  created() {
    this.fetchList();
  },
  updated() {
    this.scroll();
  },

  // by 바로 실행하는 함수
  name: "List",

  data() {
    return {
      updatedPage: 1,
      result: [],
      list: [],
      orderedList: [],
      reversedOrderedList: [],
      category: [],
      clickedCategory: [],
      savedCategory: [],

      flag: "false"
    };
  },
  methods: {
    //scroll Top으로 이동하기
    scrollTop() {
      window.onscroll = () => {
        document.documentElement.scrollTop + window.innerHeight + 2 >=
          document.documentElement.offsetHeight;

        if (bottomOfWindow) {
          this.updatedPage++;
          this.fetchList();
          //의미가 있는지를 모르겠으나 오름차순일 때, 10개씩 새로 추가됨.
          this.clickOrderedList();
          //내림차순일때는 의미가 있다.
          this.clickReversedOrderedList();
        }
      };
    },
    //scroll 내리면 10개 더 가져오기
    scroll() {
      window.onscroll = () => {
        let bottomOfWindow =
          document.documentElement.scrollTop + window.innerHeight + 2 >=
          document.documentElement.offsetHeight;

        if (bottomOfWindow) {
          this.updatedPage++;
          this.fetchList();
          this.clickOrderedList();
          this.clickReversedOrderedList();
        }
      };
    },
    // 모달창
    showModal() {
      this.clickedCategory = [];
      this.savedCategory = [];
      this.category.sort(function(a, b) {
        return a - b;
      });
      this.$refs["my-modal"].show();
    },
    hideModal() {
      this.clickedCategory = [];
      this.savedCategory = [];

      this.$refs["my-modal"].hide();
    },
    setCategory: function() {
      this.savedCategory = this.clickedCategory;
      let newList = [];
      if (this.savedCategory.length === 0) {
        this.$refs["my-modal"].hide();
      } else {
        for (let i = 0; i < this.result.length; i++) {
          for (let j = 0; j < this.savedCategory.length; j++) {
            if (this.result[i].category_no.includes(this.savedCategory[j])) {
              newList.push(this.result[i]);
            }
          }
        }
        this.list = newList;
        this.clickOrderedList();
        this.clickReversedOrderedList();
        this.$refs["my-modal"].hide();
      }
    },
    resetCategory: function() {
      this.list = this.result;
      this.clickedCategory = [];
      this.savedCategory = [];
      this.$refs["my-modal"].hide();
    },
    clickCategory: function(ele) {
      this.clickedCategory.push(ele);
    },
    //by 모달창

    // 리스트
    clickOrderedList: function() {
      this.orderedList = this.orderedList
        .concat(this.list)
        .sort(function(a, b) {
          return a.no - b.no;

          // 한글순 일 때
          // a.title < b.title ? -1 : a.title > b.title ? 1 : 0;
        });
    },
    clickReversedOrderedList: function() {
      this.reversedOrderedList = this.list.sort(function(a, b) {
        return b.no - a.no;
        // 한글순 일 떄
        // a.title > b.title ? -1 : a.title < b.title ? 1 : 0;
      });
    },
    /////by 리스트

    // 데이터 가져오기
    fetchList: function() {
      let page = this.updatedPage;
      axios
        .get("http://comento.cafe24.com/request.php/", { params: { page } })
        .then(response => {
          //list에 데이터 저장하기
          console.log(response.data.list);
          this.result = this.result.concat(
            response.data.list.slice(-response.data.list.length)
          );
          this.list = this.list.concat(
            response.data.list.slice(-response.data.list.length)
          );
          //카테고리 설정하기
          response.data.list.forEach(ele => {
            !this.category.includes(ele.category_no)
              ? this.category.push(ele.category_no)
              : false;
          });
        })
        .catch(err => {
          console.log("Error ", err);
        });
    }
    // by 데이터 가져오기
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.title {
  align-content: center;
  text-align: center;
  align-items: center;
}
.box {
  padding: 30px;
  min-width: 480px;
  border: solid 1px #1abc9c;
}
.scrollBox {
  transition: 2.5s all cubic-bezier(0.39, 0.575, 0.565, 1);
}
.categoryNum {
  display: flex;
  flex: 1;
  justify-content: space-around;
  padding: 30px;
}
.list-group-item {
  margin: 15px;
  padding: 0;
}
.list-header {
  display: flex;
  flex-direction: row;
  justify-content: stretch;
  align-items: stretch;
  flex: 1;
  line-height: 40px;
  border-bottom: solid 1px #bdc3c7;
}
.list-body {
  display: flex;
  flex-direction: row;
  justify-content: flex-start;
  flex: 1;
}
.list-email {
  padding-left: 10px;
  padding-right: 20px;
}
.list-date {
  padding-left: 20px;
}
.list-content {
  line-height: 60px;
  padding: 1px;
}
.category {
  padding-left: 20px;
  align-content: "left";
  flex: 3;
}
.listNum {
  text-align: "right";

  flex: 1;
}

h1,
h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
