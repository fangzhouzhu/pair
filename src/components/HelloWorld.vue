<template>
  <div class="out">
    <div class="main" v-show="!show">
      <div class="machine">
        <div class="box">
          <img src="../assets/poiner.png" class="pointer" alt="">
          <div class="gan">
            <img src="../assets/ganMove.png" class="img_gan_top" alt="点击我"  @click="start">
            <img src="../assets/ganBottom.png" class="img_gan_bottom" alt="">
          </div>

          <div class="con">
            <div class="up">
              <ul >
                <li v-for="item in peopleList1">{{item.name}}</li>
              </ul>
            </div>
            <div class="center">
              <h3>组  队</h3>
            </div>
            <div class="down">
              <ul >
                <li v-for="item in peopleList2">{{item.name}}</li>
              </ul>
            </div>
          </div>
        </div>

      </div>
      <div class="listbox">
          <div class="list">
            <h2>已组队清单</h2>
            <button @click="expand">全屏</button>
            <div class="content">

              <div style="padding: 0 20px" class="peiduiList">
                <li v-for="(item,index) in peiduilist" style="font-size: 16px">
                 <span class="index">{{index+1}}</span> {{item.name1}} <span>&</span> {{item.name2}}
                </li>
              </div>
            </div>
          </div>
      </div>
    </div>
    <div class="expand" v-show="show">

        <div class="expandcontent">
          <button @click="show=false" class="btn-return">还原</button>
          <h2>已组队清单</h2>
          <div >
            <li v-for="(item,index) in newpeiduilist"  style="font-size: 16px">
              <span class="index">{{index+1}}</span> {{item.name1}} <span>&</span> {{item.name2}}
            </li>
          </div>
      </div>
    </div>
  </div>

</template>

<script>
//  import peopleList from "../../static/peopleList.js"
export default {
  name: 'HelloWorld',
  data () {
    return {
        list:[1,1,1,1,1,1,1,1],
      show:false,
      posArr1:[],
      copyposArr1:[],
      copyposArr2:[],
      posArr2:[],
      dot1:require('../assets/dot1.png'),
      dot2:require('../assets/dot2.png'),
      scrollTop1:1,
      scrollTop2:1,
     peopleList1:window.g.peopleList1,//黄色标注
     peopleList2:window.g.peopleList2,
     peiduilist:[],
      newpeiduilist:[],
      random1:99,
      random2:99,
      height:100,
      rat:1,
    }
  },
  methods:{
    start(){
      $(".img_gan_top").animate({"top":33},300,"linear",function () {
        $(this).css("top",33).animate({"top":12},400,"linear");
      });
      if(this.random1!=99){
          this.posArr1.splice(this.random1,1)
          this.posArr2.splice(this.random2,1)
        }
        var length1 =this.posArr1.length;
        var length2=this.posArr2.length;
        this.random1=Math.floor(Math.random()*length1);//>1
        this.random2=Math.floor(Math.random()*length2);
        if(length1==0||length2==0){
          this.show=true;
          return
        }
        this.scrollTop1=this.posArr1[this.random1].scrollTop;//top
        var index1 = this.posArr1[this.random1].xiabiao;
        var index2
      if(this.peopleList1[index1].flag!=""){
        this.peopleList2.forEach((ele,index)=>{
          if(ele.flag==this.peopleList1[index1].flag){
            var peidui = index;
            this.posArr2.forEach((item,Index)=>{
                if(item.scrollTop==Math.round((this.height*peidui-50)*this.rat)){
                    alert(999)
                    console.log('配对')
                   this.random2=Index;
                }
            })
          }
        })
      }
      this.scrollTop2=this.posArr2[this.random2].scrollTop;
      index2 = this.posArr2[this.random2].xiabiao;
      if(this.peopleList1[index1].flag==""){
          this.random2=this.getRandom()
          index2 = this.posArr2[this.random2].xiabiao;
          this.scrollTop2=this.posArr2[this.random2].scrollTop;
      }
      var th = this;
      $(".up ul").animate({"scrollTop":th.height*(th.peopleList1.length)},1000,"linear", function () {
        $(this).css("scrollTop",0).animate({"scrollTop":th.scrollTop1},1000,"linear");
      });
      $(".down ul").animate({"scrollTop":th.height*(th.peopleList2.length)},1000,"linear", function () {
        $(this).css("scrollTop",0).animate({"scrollTop":th.scrollTop2},1000,"linear");
      });

      setTimeout(()=>{
          if(this.peopleList1[index1]&&this.peopleList2[index2]){
            this.peiduilist.push({
              name1:this.peopleList1[index1].name,
              name2:this.peopleList2[index2].name,
            });
            localStorage.setItem("peiduilist",JSON.stringify(this.peiduilist))
            this.newpeiduilist =  JSON.parse(localStorage.getItem('peiduilist'))
          }
      },2000)

    },
    expand(){
        this.show=true;
    },
    getRandom(){
      var arr = []
      this.peopleList2.forEach((ele,index)=>{
          this.posArr2.forEach((ele2,Index)=>{
              if(ele.flag==''&&ele2.scrollTop==Math.round((this.height*index-50)*this.rat)){
                arr.push(Index)
              }
          })
//          if(ele.flag==''){
//
//          }
      })
      var length = arr.length;
      var random2=arr[Math.floor(Math.random()*length)];//>1
      var xiabiao=0
//      this.posArr2.forEach((item,index)=>{
//          if(item.scrollTop==Math.round((this.height*random2-50)*this.rat)){
//              console.log("random2="+index)
//              console.log("scrollTop"+item.scrollTop)
//            xiabiao = index
//      }
//      })
      return random2
    },
    getLength(){
      this.posArr1=[];
      this.posArr2=[];
      this.copyposArr1=[];
      this.copyposArr2=[];
      var length1 =this.peopleList1.length;
      var length2=this.peopleList2.length;
      for(var i = 0;i<length1;i++){
        this.posArr1.push({"scrollTop":Math.round((this.height*i-50)*this.rat),"xiabiao":i})
        this.copyposArr1.push({"scrollTop":Math.round((this.height*i-50)*this.rat),"xiabiao":i})
      }
      for(var i = 0;i<length2;i++){
        this.posArr2.push({"scrollTop":Math.round((this.height*i-50)*this.rat),"xiabiao":i})
        this.copyposArr2.push({"scrollTop":Math.round((this.height*i-50)*this.rat),"xiabiao":i})
      }
    console.log(this.posArr1)
    }
  },
  created(){
    var availWidth = window.screen.availWidth;
    if(availWidth>=1920){
        this.rat=1
    }if(availWidth<=1366){
        this.rat=0.714
    }
      this.getLength()
    localStorage.setItem("peiduilist",'')
  },
  mounted(){

    window.onload=function () {
      localStorage.removeItem("peiduilist")
    };
    $('.up ul').css("scrollTop",20).animate({"scrollTop":50*this.rat},0,"linear");
    $('.down ul').css("scrollTop",20).animate({"scrollTop":50*this.rat},0,"linear");
  },
  watch:{
    peiduilist(val){
        if(val.length>8){
            var th = this
          $('.content').css("scrollTop",20).animate({"scrollTop":(val.length-8)*69*th.rat},0,"linear");

        }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  li{
    list-style: none;
  }
  .out{
    height: 100%;
    width: 100%;
    display: flex;
    background: url("../assets/bg.png");
    background-size: 100%;
  }
.main{
  height: 100%;
  max-height: 800px;
  align-self: center;
  width: 100%;
  display: flex;
  background: url("../assets/bg.png");
  background-size: 100%;
}
  .machine{
    height: 86%;
    width: 50%;
    /*background: #ff4634;*/
    align-self: center;
    display: flex;
    justify-content: center;
    position: relative;
  }
  .box{
    /*background: #f45943;*/
    border-radius: 14px;
    background: url("../assets/box_bg.png");
    background-size: 100% 100%;
    width: 8.2rem;
    height: 7.2rem;
    align-self: center;
    position: relative;
    display: flex;
  }
  .gan{
    position: absolute;
    right:-61px;
    bottom: 60px;
    width: 61px;
    height: 144px;
  }
  .pointer{
    width: 47px;
    height:122px ;
    position: absolute;
    bottom:220px;
    right:-75px;
    animation:mymove 0.4s infinite alternate;
  }
  @keyframes mymove
  {
    from {bottom:222px;}
    to {bottom:260px;}
  }

  .img_gan_top{
    position: absolute;
    top: 13px;
    right:1px;
    width: 35px;
    height: 74px;
  }
  .img_gan_bottom{
    position: absolute;
    bottom: 0;
    width: 61px;
    height: 64px;
  }
  .pos{
    position: absolute;
    background: #f45943;
    align-self: center;
    display: flex;
    overflow: hidden;
    justify-content: space-between;
  }
.pos img{
  width: 20px;
  height: 20px;
  align-self: center;
  margin: 0 4px;
}
.pos span{
  display: flex;
  height: 30px;
}
  .top{
    top:0;
    width: 90%;
    height: 30px;
    margin-left: 5%;
  }
  .bottom{
    bottom:0;
    width: 90%;
    height: 30px;
    margin-left: 5%;
  }
  .left{
    left:0;
    height: 90%;
    width: 30px;
    flex-direction: column;
  }
  .left span,.right span{
    display: inline-block;
    height: 50px!important;
  }
  .right{
    right:0;
    height: 90%;
    width:30px;
    flex-direction: column;
  }
  .con{
    width:calc(100% - 110px);
    height:calc(100% - 110px);
    background: -webkit-linear-gradient(to right,#d3700b, #f4e5aa,#d3700b); /* Safari 5.1 - 6.0 */
    background: -o-linear-gradient(to right,#d3700b, #f4e5aa,#d3700b); /* Opera 11.1 - 12.0 */
    background: -moz-linear-gradient(to right,#d3700b, #f4e5aa,#d3700b); /* Firefox 3.6 - 15 */
    background: linear-gradient(to right,#d3700b, #f4e5aa,#d3700b); /* 标准的语法 */
    margin: 0 auto;
    align-self: center;
    border-radius: 12px;
    box-sizing: border-box;
    border: 5px solid #780500;
  }
  .up,.down{
    width: 100%;
    height: 45%;
    display: flex;
  }
  ul li{
    list-style: none;
    height: 1rem;
    line-height: 1rem;
    text-align: center;
    font-size: 26px;
    border-bottom: 2px solid #d2851d;
    box-sizing: border-box;
    color: #e23a2f;
    background: -webkit-linear-gradient(to bottom, #eeb274, #fff,#eeb274); /* Safari 5.1 - 6.0 */
    background: -o-linear-gradient(to bottom,#eeb274, #fff,#eeb274); /* Opera 11.1 - 12.0 */
    background: -moz-linear-gradient(to bottom,#eeb274, #fff,#eeb274); /* Firefox 3.6 - 15 */
    background: linear-gradient(to bottom, #eeb274, #fff,#eeb274); /* 标准的语法 */
  }
  .up ul, .down ul{
    width: 96%;
    height: 2rem;
    padding: 0;
    align-self: center;
    margin:0 2% ;
    border-radius: 12px;
    overflow: hidden;
    border: 2px solid #ffe32a;
  }
  .con .center{
    width: 100%;
    color: #950123;
    display: flex;
    font-size: 26px;
    height: 10%;
    margin: 0;
  }
  .center h3{
    width: 100%;
    align-self: center;
    text-align: center;
  }

  .listbox{
    height: 86%;
    width: 50%;
    align-self: center;
    display: flex;
  }
  .list{
    width: 6.2rem;
    height: 6rem;
    box-sizing: content-box;
    padding: 5% 10%;

    align-self: center;
    margin: 0 auto;
    background: url("../assets/list_bg.png");
    /*background-color: ;*/
    background-size: 100% 100%;
    position: relative;
  }
  .list .content{
    width: calc(100% );
    height:5.4rem;
    /*max-height: 5.8rem;*/
    overflow: hidden;
    position: relative;
  }
  .list  button{
    position: absolute;
    top: 0.6rem;
    right:1.1rem;
    border: 0;
    outline: none;
    border-radius: 5px;
    background: #fc8a72;
    height: 24px;
    width: 50px;
    color: #fff;
  }
  .list h2{
    font-size: 18px;
    height: 30px;
    line-height: 30px;
    margin: 0;
    padding-left: 20px;
    font-weight: 100;
    color: #fffd2a;
  }
  .content li{
    position: relative;
    background: -webkit-linear-gradient(to right, #fb956d, #f25045); /* Safari 5.1 - 6.0 */
    background: -o-linear-gradient(to right,#fb956d,#f25045); /* Opera 11.1 - 12.0 */
    background: -moz-linear-gradient(to right,#fb956d,#f25045); /* Firefox 3.6 - 15 */
    background: linear-gradient(to right, #fb956d,#f25045); /* 标准的语法 */
    height: 0.54rem;
    color: #fff;
    line-height: 0.54rem;
    width: 90%;
    border-radius: 30px;
    margin-bottom: 0.15rem;
  }
  .content li span{
    font-size: 16px;
    color: #fcfc12;
  }
  .content li .index{
    display: inline-block;
    width: 50px;
    font-size: 22px;
    font-weight: 900;
    font-style: italic;
    text-align: center;
  }
  .expand{
    width: 100%;
    height:100%;
    background: url("../assets/bg2.jpg");
    background-size: 100% 100%;
    position: relative;
    align-self: center;
    display: flex;
  }
  .btn-return{
    position: absolute;
    top: -10px;
    right:-20px;
    border: 0;
    outline: none;
    border-radius: 5px;
    background: #fc8a72;
    height: 24px;
    width: 50px;
    color: #fff;
  }
  .expand .expandcontent{
    width: 80%;
    height: 76%;
    max-height: 800px;
    align-self: center;
    margin: 0 auto;
    position: relative;
    /*background: darkturquoise;*/
  }
  .expandcontent h2{
    font-size: 18px;
    height: 30px;
    line-height: 30px;
    margin: 0;
    font-weight: 100;
    color: #fffd2a;
  }
  .expandcontent li{
    width: 30%;
    position: relative;
    background: -webkit-linear-gradient(to right, #fb956d, #f25045); /* Safari 5.1 - 6.0 */
    background: -o-linear-gradient(to right,#fb956d,#f25045); /* Opera 11.1 - 12.0 */
    background: -moz-linear-gradient(to right,#fb956d,#f25045); /* Firefox 3.6 - 15 */
    background: linear-gradient(to right, #fb956d,#f25045); /* 标准的语法 */
    height: 44px;
    color: #fff;
    line-height: 44px;
    border-radius: 30px;
    margin-bottom: 10px;
    float: left;
    margin-right: 3%;
  }
  .expandcontent li .index{
    display: inline-block;
    width: 40px;
    text-align: center;
    font-weight: 900;
    font-style: italic;
    font-size: 20px;
  }
  .expandcontent li span{
    font-size: 16px;
    color: #fcfc12;
  }
</style>
