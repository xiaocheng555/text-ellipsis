<template>
  <div>
    <h1>文本省略</h1>
    <br><br><hr><br>
    <section>
      <h2>实现简单文本省略</h2>
      <br>
      文本内容:<el-input type="textarea" v-model="text" :rows="5" />
      省略行数:<el-input-number v-model="rows"></el-input-number><br>
      <br>
      <h4>效果:</h4>
      <br>
      <SimpleEllipsis :rows="rows" style="max-width: 800px;" :content="text"></SimpleEllipsis>
      <br><hr><br>
    </section>
    <section>
      <h2>实现facebook的文本省略</h2>
      <br>
      文本内容:<el-input type="textarea" v-model="text2" :rows="5" />
      不能超过换行数:<el-input-number v-model="newline"></el-input-number><br>
      不能超过字数:<el-input-number v-model="maxLen"></el-input-number><br>
      <br>
      <h4>效果:</h4>
      <br>
      <FackbookEllipsis :newline="newline" :maxLen="maxLen" style="max-width: 800px;" :content="text2"></FackbookEllipsis>
      <br><hr><br>
    </section>
    <section>
      <h2>实现ant-design-mobile的文本省略</h2>
      <br>
      文本内容:<el-input type="textarea" v-model="text3" :rows="5" /><br>
      省略行数:<el-input-number v-model="rows2"></el-input-number><br>
      省略位置:
      <el-radio-group v-model="direction">
        <el-radio label="start">start</el-radio>
        <el-radio label="middle">middle</el-radio>
        <el-radio label="end">end</el-radio>
      </el-radio-group><br>
      <div v-if="direction === 'end'">
        展开收起单独一行:<el-switch v-model="single"></el-switch><br>
      </div>
      <br>
      <h4>效果:</h4>
      <TextEllipsis 
        style="max-width: 800px; padding: 10px;" 
        :rows="rows2" 
        :direction="direction"
        :content="text3" 
        :single="single"
        dot="... ">
      </TextEllipsis>
    </section>
  </div>
</template>

<script setup lang="ts">
import  SimpleEllipsis from './SimpleEllipsis.vue'
import  FackbookEllipsis from './FackbookEllipsis.vue'
import  TextEllipsis from './TextEllipsis.vue'
import { ref } from 'vue'

const text = ref(`永和九年，岁在癸丑，暮春之初，会于会稽山阴之兰亭，修禊事也。群贤毕至，少长咸集。此地有崇山峻岭，茂林修竹，又有清流激湍，映带左右，引以为流觞曲水，列坐其次。虽无丝竹管弦之盛，一觞一咏，亦足以畅叙幽情。是日也，天朗气清，惠风和畅。仰观宇宙之大，俯察品类之盛，所以游目骋怀，足以极视听之娱，信可乐也。夫人之相与，俯仰一世。或取诸怀抱，悟言一室之内；或因寄所托，放浪形骸之外。虽趣舍万殊，静躁不同，当其欣于所遇，暂得于己，快然自足，不知老之将至；及其所之既倦，情随事迁，感慨系之矣。向之所欣，俯仰之间，已为陈迹，犹不能不以之兴怀，况修短随化，终期于尽！古人云：“死生亦大矣。”岂不痛哉！每览昔人兴感之由，若合一契，未尝不临文嗟悼，不能喻之于怀。固知一死生为虚诞，齐彭殇为妄作。后之视今，亦犹今之视昔，悲夫！故列叙时人，录其所述，虽世殊事异，所以兴怀，其致一也。后之览者，亦将有感于斯文。`)
const text2 = ref(`先帝创业未半而中道崩殂，今天下三分，益州疲弊，此诚危急存亡之秋也。然侍卫之臣不懈于内，忠志之士忘身于外者，盖追先帝之殊遇，欲报之于陛下也。诚宜开张圣听，以光先帝遗德，恢弘志士之气，不宜妄自菲薄，引喻失义，以塞忠谏之路也。
宫中府中，俱为一体，陟罚臧否，不宜异同。若有作奸犯科及为忠善者，宜付有司论其刑赏，以昭陛下平明之理，不宜偏私，使内外异法也。
侍中、侍郎郭攸之、费祎、董允等，此皆良实，志虑忠纯，是以先帝简拔以遗陛下。愚以为宫中之事，事无大小，悉以咨之，然后施行，必能裨补阙漏，有所广益。
将军向宠，性行淑均，晓畅军事，试用于昔日，先帝称之曰能，是以众议举宠为督。愚以为营中之事，悉以咨之，必能使行阵和睦，优劣得所。
亲贤臣，远小人，此先汉所以兴隆也；亲小人，远贤臣，此后汉所以倾颓也。先帝在时，每与臣论此事，未尝不叹息痛恨于桓、灵也。侍中、尚书、长史、参军，此悉贞良死节之臣，愿陛下亲之信之，则汉室之隆，可计日而待也。
臣本布衣，躬耕于南阳，苟全性命于乱世，不求闻达于诸侯。先帝不以臣卑鄙，猥自枉屈，三顾臣于草庐之中，咨臣以当世之事，由是感激，遂许先帝以驱驰。后值倾覆，受任于败军之际，奉命于危难之间，尔来二十有一年矣。
先帝知臣谨慎，故临崩寄臣以大事也。受命以来，夙夜忧叹，恐托付不效，以伤先帝之明，故五月渡泸，深入不毛。今南方已定，兵甲已足，当奖率三军，北定中原，庶竭驽钝，攘除奸凶，兴复汉室，还于旧都。此臣所以报先帝而忠陛下之职分也。至于斟酌损益，进尽忠言，则攸之、祎、允之任也。
愿陛下托臣以讨贼兴复之效，不效，则治臣之罪，以告先帝之灵。若无兴德之言，则责攸之、祎、允等之慢，以彰其咎；陛下亦宜自谋，以咨诹善道，察纳雅言。深追先帝遗诏，臣不胜受恩感激。
今当远离，临表涕零，不知所言。`)
const text3 = ref(`永和九年，岁在癸丑，暮春之初，会于会稽山阴之兰亭，修禊事也。群贤毕至，少长咸集。此地有崇山峻岭，茂林修竹，又有清流激湍，映带左右，引以为流觞曲水，列坐其次。虽无丝竹管弦之盛，一觞一咏，亦足以畅叙幽情。是日也，天朗气清，惠风和畅。仰观宇宙之大，俯察品类之盛，所以游目骋怀，足以极视听之娱，信可乐也。夫人之相与，俯仰一世。或取诸怀抱，悟言一室之内；或因寄所托，放浪形骸之外。虽趣舍万殊，静躁不同，当其欣于所遇，暂得于己，快然自足，不知老之将至；及其所之既倦，情随事迁，感慨系之矣。向之所欣，俯仰之间，已为陈迹，犹不能不以之兴怀，况修短随化，终期于尽！古人云：“死生亦大矣。”岂不痛哉！每览昔人兴感之由，若合一契，未尝不临文嗟悼，不能喻之于怀。固知一死生为虚诞，齐彭殇为妄作。后之视今，亦犹今之视昔，悲夫！故列叙时人，录其所述，虽世殊事异，所以兴怀，其致一也。后之览者，亦将有感于斯文。`)

const rows = ref(5)
const rows2 = ref(5)
const direction = ref('end')
const single = ref(false)

const newline = ref(5)
const maxLen = ref(400)
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
}
.el-radio {
  margin-right: 15px;
}
</style>