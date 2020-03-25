<template lang="html">
  <section>
    <!-- スライドの大枠を決める -->
    <div class="slider-outer">
      <!-- v-for文の適用　以下の部分が繰り返し表示される　:keyはslides[index].altでも良い -->
      <div class="slider-inner" :key="index" v-for="(slide,index) in slides">
        <!-- transitionの適用　ここより上だと正しく反映されない -->
        <transition :name="slide_name">
          <!-- v-if文の適用　index毎に表したいのでslides[index].hogeにする　:keyはslides[index].hogeでも良い -->
          <img class="slide-img" :src="slides[index].img" :key="index" :alt="slides[index].alt" v-if="current_slide === index">
          <!--
          ex)
          <a class="slide-a" :href="slides[index].href" :key="slides[index].text" v-if="current_slide === index">{{ slides[index].text }}</a>
         -->
        </transition>
      </div>
    </div>
    <div class="button">
      <button type="button" @click="prev()">前へ</button> <!-- @click=""はv-on:click=""でも良い -->
      <button type="button" @click="autoSlide()" v-if="slide === false">|></button><!-- 自動スライドON -->
      <button type="button" @click="stopSlide()" v-else>||</button><!-- 自動スライドOFF -->
      <button type="button" @click="next()">次へ</button>
    </div>
  </section>
</template>
<script>
//.vue専用の記法
export default {
  name: 'Slider',
  data () {
    return {
//ここまで
      slide: false,//ボタンのオンオフ切り替え
      autoPlay: null, //変数の中身を空にする
      slide_name: 'next', //自動でスライドする時は右から左へ流すトランジションを適用する
      current_slide: 0, //最初に表示される時は配列の一番最初の物を表示させる
      slides: [
        //require()はVueCLI限定の記法
        { img: require('#'), alt: 'noimage' }, // '#'部分に相対パスを入れよう
        { img: require('#'), alt: 'noimage' }, // ex) require('../assets/images/hoge.png')
        { img: require('#'), alt: 'noimage' }
      ]
    }
  },
  methods: {
    //スライダーの自動化
    autoSlide(){
      this.autoPlay = setInterval(() => {
        this.current_slide = this.current_slide < this.slides.length - 1 ? this.current_slide + 1 : 0 ;
        /*
        if(this.current_slide < this.slides.length - 1){
        this.current_slide + 1
        }else{
        this.current_slide = 0
        }
        */
      },3000) //自動でスライドするまでの時間
      this.slide = true //ボタン切り替えの指標
    },
    //スライダーを止める
    stopSlide(){
      clearInterval(this.autoPlay)
      this.slide = false //ボタン切り替えの指標
    },
    //「前へ」ボタンの関数
    prev () {
      this.slide_name = 'prev' //スライドを左から右へ流すトランジションへ変更
      if (this.current_slide === 0) { //current_slideが0のとき
        this.current_slide = this.slides.length - 1 //current_slideを配列の一番最後へ戻す
      } else { //current_slideが0ではない時
        this.current_slide-- //current_slideから１を引く
      }
    },
    //「次へ」ボタンの関数
    next () {
      this.slide_name = 'next' //スライドを右から左へ流すトランジションへ変更
      if (this.current_slide === this.slides.length - 1) { //current_slideが配列の数より多いとき
        this.current_slide = 0 //current_slideを0へ戻す
      } else { //current_slideが配列の数より少ない時
        this.current_slide++ //current_slideに1を足す
      }
      if (this.slide === true) { //自動スライドがオンの場合
        clearInterval(this.autoPlay) //stopSlideと同じ挙動
        this.slide = false
      }
    }
  }
}
</script>

<style lang="css" scoped>
  .slider-outer{
    position: relative;
    /* お好みで変更してください */
    width: 450px;
    height: 300px;
    margin: 0 auto 20px;
    /* heightを指定しているため、はみ出た部分は表示させない設定にしています。こちらもお好みで */
    overflow: hidden;
  }
  .slider-inner{
    position: absolute;
    /* お好みで変更してください　slide-outer以上にwidth, heightを設定することは推奨しておりません */
    width: 450px;
    height: 300px;
  }
  .slide-img {
    /* お好みで変更してください　slide-outer以上にwidth, heightを設定することは推奨しておりません */
    width: 450px;
    height: 300px;
    /* 画像のサイズを変更しています。詳しい説明は省略させて頂きます。 */
    object-fit: cover;
  }
  /* ボタンが横並びになるように配置しております。　slider.htmlでは別の方法をご紹介しております。 */
  .button {
    display: flex;
    justify-content: space-around;
  }
  /* transition */
  .prev-enter-active, .prev-leave-active,
  .next-enter-active, .next-leave-active {
    transform: translate(0px, 0px);
    transition: transform 225ms cubic-bezier(0, 0, 0.2, 1) 0ms;
  }
  /* 左から右へ */
  .prev-enter, .prev-leave-to {
    transform: translateX(-100vw);
  }
  /* 右から左へ */
  .next-enter, .next-leave-to {
    transform: translateX(100vw);
  }
</style>
