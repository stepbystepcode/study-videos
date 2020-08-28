<template>
	<div id="warp">
		<div id="fixed">
			<div id="title">知识点讲解</div>
			<hr />

			<div :class="['bar', { barSymbolA }]" @click="barSymbol('A')">
				{{ subjectName }}{{ bookName }}
			</div>
			<div :class="['bar', { barSymbolB }]" @click="barSymbol">{{ fine }}</div>

			<hr />
		</div>
		<div id="menu" :class="{ barStatus }">
			<div class="mainbar">
				<!--subject-->
				<ul v-show="barSymbolA">
					<li
						v-for="(tab, index) in ['高中数学', '高中物理', '高中化学']"
						:class="['tab-li', { select: subject === index }, { active: subject === index }]"
						@click="
							subject = index;
							subjectNameb = tab;
						"
					>
						{{ tab }}
					</li>
				</ul>
				<!--chapter-->
				<ul v-show="barSymbolB" v-if="lists[subject]">
					<li :class="['tab-li', { select: allall }, { active: allall }]" @click="allall = true">
						全部
					</li>
					<li
						:class="[
							'tab-li',
							{ select: chapter === index && !allall },
							{ active: chapter === index && !allall },
						]"
						v-for="(li, index) in lists[subject][book].children"
						@click="
							chapter = index;
							chapter = index;
							allall = false;
						"
					>
						{{ li.title }}
					</li>
				</ul>
			</div>
			<div class="mainbar">
				<ul>
					<!--all book-->
					<template v-for="len in lists.length">
						<li
							:class="['tab-li', { select: book === index }]"
							v-for="(li, index) in lists[len - 1]"
							v-if="barSymbolA && subject === len - 1"
							@click="
								book = index;
								barStatus = !barStatus;
								barSymbolA = !barSymbolA;
								bookName = li.title;
								subjectName = subjectNameb;
							"
						>
							{{ li.title }}
						</li>
					</template>
				</ul>
				<ul v-if="lists[subject]">
					<!--select all-->
					<li
						:class="['tab-li', { select: all }]"
						v-if="barSymbolB"
						@click="
							id = 0;
							barStatus = !barStatus;
							barSymbolB = !barSymbolB;
							all = true;
							sectionName = '全部知识点';
						"
					>
						全部知识点
					</li>
					<!--select section-->
					<li
						:class="['tab-li', { select: section === index && !all }]"
						v-for="(li, index) in lists[subject][book].children[chapter].children"
						v-if="barSymbolB && section != 'all' && !allall"
						@click="
							section = index;
							barStatus = !barStatus;
							all = false;
							barSymbolB = !barSymbolB;
							sectionName = li.title;
						"
					>
						{{ li.title }}
					</li>
				</ul>
			</div>
		</div>
		<transition name="fade">
			<div id="mask" v-if="barStatus || video"></div>
		</transition>
		<div id="content">
			<ul v-if="lists[subject]">
				<!--current-->
				<template
					v-for="(li, index) in lists[subject][book].children[chapter].children[section].children"
					v-if="!all"
				>
					<li
						class="lesson"
						@click="
							video = true;
							id = li.id;
						"
					>
						<div class="lesson-row">
							<img src="../assets/img/1.png" class="icon" />
							<div class="lesson-cell">
								<span class="lesson-title">{{ li.title }}</span
								><br />
								<span class="info">视频 · 讲义 · {{ randomNum() }}万名同学看过</span>
							</div>

							<hr style="margin-top: -5px" v-if="!allall" />
						</div></li
				></template>
				<!--all-->
				<template
					v-else-if="all && !allall"
					v-for="(li, index) in lists[subject][book].children[chapter].children"
					><template v-for="all in li.children">
						<li
							class="lesson"
							@click="
								video = true;
								id = all.id;
							"
						>
							<div class="lesson-row">
								<img src="../assets/img/1.png" class="icon" />
								<div class="lesson-cell">
									<span class="lesson-title">{{ all.title }}</span
									><br />
									<span class="info">视频 · 讲义 · {{ randomNum() }}万名同学看过</span>
								</div>

								<hr style="margin-top: -5px" />
							</div></li></template
				></template>
				<!--allall--><template v-for="(li, index) in lists[subject][book].children" v-else>
					<template v-for="all in li.children"
						><li
							v-for="lesson in all.children"
							class="lesson"
							@click="
								video = true;
								id = lesson.id;
							"
						>
							<div class="lesson-row">
								<img src="../assets/img/1.png" class="icon" />
								<div class="lesson-cell">
									<span class="lesson-title">{{ lesson.title }}</span
									><br />
									<span class="info">视频 · 讲义 · {{ randomNum() }}万名同学看过</span>
								</div>
								<hr style="margin-top: -5px" />
							</div>
						</li> </template
				></template>
			</ul>
		</div>
		<div id="player">
			<div id="close" v-if="video" @click="video = false">×</div>
			<video
				:src="'http://v.leleketang.com/dat/hs/ma/k/video/' + id + '.mp4'"
				type="video/mp4"
				v-if="video"
				controls
			></video>
		</div>
	</div>
</template>

<script>
import axios from "axios";
import VueAxios from "vue-axios";
export default {
	name: "HelloWorld",
	data() {
		return {
			barStatus: false,
			barSymbolA: false,
			barSymbolB: false,
			lists: [],
			subject: 0,
			book: 0,
			bookName: "必修1",
			subjectName: "高中数学",
			subjectNameb: "高中数学",
			chapter: 0,
			section: 0,
			all: true,
			allall: true,
			sectionName: "全部知识点",
			sectionNameb: "全部知识点",
			video: false,
			id: 0,
		};
	},
	mounted() {
		axios.get("https://raw.githubusercontent.com/ybygjylj/datas/master/data.json").then((res) => {
			this.lists = res.data;
		});
	},
	methods: {
		barSymbol(el) {
			if (el == "A") {
				if (this.barSymbolB) {
					this.barSymbolB = !this.barSymbolB;
				} else {
					this.barStatus = !this.barStatus;
				}
				this.barSymbolA = !this.barSymbolA;
			} else {
				if (this.barSymbolA) {
					this.barSymbolA = !this.barSymbolA;
				} else {
					this.barStatus = !this.barStatus;
				}
				this.barSymbolB = !this.barSymbolB;
			}
			this.subjectName = this.subjectNameb;
		},
		randomNum() {
			return parseInt(Math.random() * (50 - 5 + 1) + 5, 10);
		},
	},
	computed: {
		fine() {
			if (this.sectionName.length > 8) {
				str = this.sectionName.substr(0, 7) + "...";
				return str;
			} else {
				return this.sectionName;
			}
		},
	},
};
</script>

<style>
@charset 'utf-8';
html {
	color: #000;
	background: #fff;
	font-family: "Microsoft YaHei", sans-serif, Arial;
}
body,
div,
dl,
dt,
dd,
ul,
ol,
li,
h1,
h2,
h3,
h4,
h5,
h6,
pre,
code,
form,
fieldset,
legend,
input,
button,
textarea,
p,
blockquote,
th,
td,
strong {
	padding: 0;
	margin: 0;
	font-family: "Microsoft YaHei", sans-serif, Arial;
}
table {
	border-collapse: collapse;
	border-spacing: 0;
}
img {
	border: 0;
}
a {
	text-decoration: none;
	color: #333;
	outline: none;
}
a:hover {
	text-decoration: none;
}
var,
em,
strong {
	font-style: normal;
}
em,
strong,
th,
var {
	font-style: inherit;
	font-weight: inherit;
}
li {
	list-style: none;
}
caption,
th {
	text-align: left;
}
h1,
h2,
h3,
h4,
h5,
h6 {
	font-size: 100%;
	font-weight: normal;
}
input,
button,
textarea,
select,
optgroup,
option {
	font-family: inherit;
	font-size: inherit;
	font-style: inherit;
	font-weight: inherit;
}
input,
button,
textarea,
select {
	*font-size: 100%;
}
.clearfix {
	*zoom: 1;
}
.clearfix:after {
	content: "\200B";
	clear: both;
	display: block;
	height: 0px;
}
@font-face {
	font-family: title;
	src: url("../assets/fonts/HYQiHei-75W.ttf");
}
@font-face {
	font-family: bar;
	src: url("../assets/fonts/HYQiHeiY1-55W-Book.ttf");
}
#warp {
	width: 100vw;
	height: 100vh;
}
#title {
	z-index: 3;
	width: 100%;
	height: 6.2739463602vh;
	line-height: 6.2739463602vh;
	text-align: center;
	font-family: title;
	font-size: 2.3946360153vh;
	background-color: #fff;
}
hr {
	margin: 0;
	height: 0.0478927203vh;
	border: none;
	background-color: #e3e3e3;
}
.bar {
	width: 50%;
	height: 6.8965517241vh;
	line-height: 6.8965517241vh;
	display: inline-block;
	text-align: center;
	font-family: bar;
	font-size: 2.0114942529vh;
	vertical-align: middle;
	background-color: #fff;
}
.bar:after {
	content: "▼";
	font-size: 1.3vh;
	display: inline-block;
	transition: transform 0.3s;
	margin-left: 4px;
}
#menu {
	z-index: 2;
	width: 100%;
	height: 51.5804597701vh;
	background: #fff;
	position: fixed;
	transition: top 0.4s ease-out;
	top: -38.3141762452vh;
	font-size: 0;
}
.mainbar {
	height: 100%;
	display: inline-block;
	overflow: scroll;
}
.mainbar:nth-of-type(1) {
	width: 40vw;
}
.mainbar:nth-of-type(2) {
	width: 60vw;
	background: #fafafa;
}
.barStatus {
	top: 13.2662835249vh !important;
}
.barSymbolA {
	transform: rotate(0deg);
	color: #ceaa76;
}
.barSymbolA:after {
	transform: rotate(180deg);
	color: #ceaa76;
}
.barSymbolB {
	transform: rotate(0deg);
	color: #ceaa76;
}
.barSymbolB:after {
	transform: rotate(180deg);
	color: #ceaa76;
}
#mask {
	z-index: 1;
	width: 100vw;
	height: 100vh;
	background: rgba(0, 0, 0, 0.4);
	position: fixed;
	top: 0;
	left: 0;
}
#fixed {
	position: fixed;
	top: 0;
	width: 100%;
	height: 13.2662835249vh;
	background: #fff;
	z-index: 3;
	font-size: 0;
}
.fade-enter-active {
	transition: all 0.5s ease;
}
.fade-leave-active {
	transition: all 0.5s ease;
}
.fade-enter,
.fade-leave-to {
	opacity: 0 !important;
}
.tab-li {
	padding: 2.04vh;
	padding-left: 3vw;
	font-family: bar;
	font-size: 2.0114942529vh;
}
.active {
	background: #fafafa;
}
#content {
	width: 100%;
	height: auto;
}
.lesson {
	width: 100%;
	height: 12.4vh;
	font-size: 4.444444vw;
}
.icon {
	width: auto;
	height: 8.3796296296vh;
	margin-top: 1.8666666667vh;
	margin-bottom: 2.0833333333vh;
	margin-left: 3vw;
	margin-right: -5vw;
}
.select {
	color: #ceaa76;
}
.info {
	font-size: 3.2407407407vw;
	color: #909090;
}
.lesson-title {
	margin-top: -5px;
}
.lesson-cell {
	padding: 3.35vh;
	overflow: hidden;
	display: inline-block;
	width: 70vw;
	white-space: nowrap;
	text-overflow: ellipsis;
}
ul {
	width: 100%;
}
li {
	text-align: left;
}
#close {
	width: 4vw;
	height: 4vw;
	margin: 1vw 3vw 0vw 0vw;
	position: fixed;
	right: 0;
	z-index: 10000;
	background: #fff;
	opacity: 0.5;
	border-radius: 2vw;
	color: #000;
	text-align: center;
	font-size: 4vw;
	line-height: 4vw;
}

@media screen and (orientation: portrait) {
	video {
		width: 100%;
	}
	#player {
		z-index: 100;
		width: 100%;
		height: auto;
		position: fixed;
		top: 50%;
		margin-top: -25%;
	}
}

@media screen and (orientation: landscape) {
	video {
		height: 100%;
		display: inline-block;
	}
	#player {
		z-index: 100;
		height: 100vh;
		width: 100vw;
		position: absolute;
		top: 0;
		text-align: center;
		margin: 0 auto;
		background: #000;
	}
}
</style>
