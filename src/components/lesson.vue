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
				:src="'http://v.leleketang.com/dat/hs/' + subjectCode[subject] + '/k/video/' + id + '.mp4'"
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
			subjectCode: ["ma", "ph", "ch"],
			lists: [
				[
					{
						title: "必修1",
						children: [
							{
								title: "集合与常用逻辑用语",
								children: [
									{
										title: "集合的概念",
										children: [
											{ title: "集合的概念", id: 21052 },
											{ title: "集合的性质及表示", id: 21053 },
											{ title: "集合的描述法", id: 21054 },
										],
									},
									{
										title: "集合间的基本关系",
										children: [
											{ title: "包含与子集", id: 21060 },
											{ title: "子集的个数公式", id: 21066 },
											{ title: "判断集合之间的关系", id: 22024 },
										],
									},
									{
										title: "集合的基本运算",
										children: [
											{ title: "交集概念", id: 240316 },
											{ title: "并集概念", id: 240317 },
											{ title: "补集概念", id: 240318 },
											{ title: "集合的混合运算", id: 21080 },
										],
									},
									{
										title: "充分条件与必要条件",
										children: [{ title: "充分条件与必要条件", id: 23554 }],
									},
									{
										title: "全称量词与存在量词",
										children: [{ title: "全称量词与存在量词", id: 23548 }],
									},
								],
							},
							{
								title: "一元二次函数、方程和不等式",
								children: [
									{
										title: "等式性质与不等式性质",
										children: [
											{ title: "不等式比较大小", id: 23162 },
											{ title: "不等式的性质", id: 23163 },
										],
									},
									{
										title: "基本不等式",
										children: [
											{ title: "均值不等式", id: 23178 },
											{ title: "凑项利用均值求最值", id: 23196 },
										],
									},
									{
										title: "二次函数与一元二次方程、不等式",
										children: [
											{ title: "解一元二次不等式", id: 23180 },
											{ title: "不等式的实际应用", id: 23182 },
										],
									},
								],
							},
							{
								title: "函数的概念与性质",
								children: [
									{
										title: "函数的概念及其表示",
										children: [
											{ title: "具体函数的定义域", id: 21090 },
											{ title: "区间", id: 21089 },
											{ title: "判断是否为同一函数", id: 21092 },
											{ title: "函数是什么", id: 240328 },
											{ title: "求函数值", id: 21093 },
											{ title: "二次函数的值域", id: 21096 },
										],
									},
									{
										title: "函数的基本性质",
										children: [
											{ title: "求函数值", id: 21093 },
											{ title: "二次函数的值域", id: 21096 },
											{ title: "单调性的概念", id: 21117 },
											{ title: "定义法证明函数单调性", id: 21118 },
											{ title: "奇偶性的概念", id: 21129 },
											{ title: "一次、反比例函数的单调性", id: 21119 },
											{ title: "二次函数的单调性", id: 21120 },
											{ title: "奇偶性的运算性质", id: 21130 },
										],
									},
									{
										title: "幂函数",
										children: [
											{ title: "幂函数的定义", id: 21190 },
											{ title: "幂指数对单调性的影响", id: 21194 },
											{ title: "根据图象上的点求解析式", id: 21191 },
											{ title: "常见幂函数的图象", id: 21192 },
											{ title: "幂指数对定义域的影响", id: 21193 },
											{ title: "幂指数对奇偶性的影响", id: 21196 },
											{ title: "一般幂函数图象的画法", id: 21200 },
										],
									},
									{
										title: "函数的应用（一）",
										children: [{ title: "函数的应用（一）", id: 240347 }],
									},
								],
							},
							{
								title: "指数函数与对数函数",
								children: [
									{
										title: "指数",
										children: [
											{ title: "根式", id: 21141 },
											{ title: "指数的扩充", id: 21142 },
											{ title: "指数运算律", id: 21143 },
										],
									},
									{
										title: "指数函数",
										children: [
											{ title: "指数函数的概念", id: 21145 },
											{ title: "用单调性比较数的大小", id: 21154 },
											{ title: "指数函数图象的定点问题", id: 21146 },
											{ title: "指数函数的图象识别", id: 21147 },
											{ title: "根据底数判断单调性", id: 21148 },
										],
									},
									{
										title: "对数",
										children: [
											{ title: "对数的定义", id: 21162 },
											{ title: "对数运算律（上）", id: 21164 },
											{ title: "对数运算律（下）", id: 21165 },
											{ title: "底数和真数的范围", id: 21163 },
											{ title: "对数式之间的表示", id: 21166 },
										],
									},
									{
										title: "对数函数",
										children: [
											{ title: "对数函数的概念", id: 21168 },
											{ title: "对数函数的图象性质", id: 240362 },
											{ title: "对数函数图象的定点问题", id: 21170 },
											{ title: "对数函数的图象和单调性", id: 21171 },
											{ title: "对数函数图象关系的识别", id: 21172 },
											{ title: "用单调性比较对数的大小", id: 240366 },
										],
									},
									{
										title: "函数的应用（二）",
										children: [{ title: "函数的应用（二）", id: 240367 }],
									},
								],
							},
							{
								title: "三角函数",
								children: [
									{
										title: "任意角和弧度制",
										children: [
											{ title: "终边相同的角", id: 21355 },
											{ title: "象限角的判断", id: 21356 },
											{ title: "弧度与角度的相互换算", id: 21357 },
											{ title: "角的正负", id: 21354 },
											{ title: "弧度制的应用", id: 21358 },
										],
									},
									{
										title: "三角函数的概念",
										children: [
											{ title: "三角函数的定义", id: 21359 },
											{ title: "由角求三角函数符号", id: 21360 },
											{ title: "由三角函数符号求角", id: 21361 },
											{ title: "同角三角函数的求值", id: 21366 },
											{ title: "同角三角函数式的化简", id: 21367 },
											{ title: "三角函数基本关系转化求值", id: 21370 },
										],
									},
									{
										title: "诱导公式",
										children: [
											{ title: "诱导公式", id: 21372 },
											{ title: "诱导公式的应用", id: 240380 },
										],
									},
									{
										title: "三角函数的图象与性质",
										children: [
											{ title: "五点法作图", id: 21378 },
											{ title: "正切函数的周期性与奇偶性", id: 21406 },
											{ title: "正切函数的对称性与单调性", id: 21407 },
											{ title: "正弦函数的图象", id: 21375 },
											{ title: "正弦的单调性与奇偶性", id: 21376 },
											{ title: "正弦的对称性与周期性", id: 21377 },
											{ title: "正弦函数图象伸缩变换", id: 21379 },
											{ title: "正弦函数的图象平移", id: 21380 },
											{ title: "正弦型函数的周期性", id: 21385 },
											{ title: "正弦型函数的对称性", id: 21384 },
											{ title: "正弦型函数的奇偶性", id: 21383 },
											{ title: "正弦型函数的单调性", id: 21382 },
											{ title: "余弦函数的周期性与奇偶性", id: 21393 },
											{ title: "余弦函数的对称性与单调性", id: 21394 },
											{ title: "余弦函数的图象伸缩与平移", id: 21395 },
											{ title: "正切函数的图象变换", id: 21408 },
										],
									},
									{
										title: "三角恒等变换",
										children: [
											{ title: "两角和与差的余弦", id: 22456 },
											{ title: "两角和与差的正弦", id: 22463 },
											{ title: "辅助角公式", id: 22464 },
											{ title: "两角和与差的正切", id: 22454 },
											{ title: "倍角公式", id: 23101 },
											{ title: "半角公式", id: 22795 },
											{ title: "三角函数的积化和差与和差化积", id: 22862 },
										],
									},
									{
										title: "函数y=Asin（ωx+φ）",
										children: [
											{ title: "正弦型函数图象变换综合", id: 21381 },
											{ title: "正弦型函数的周期性", id: 21385 },
											{ title: "正弦型函数的对称性", id: 21384 },
											{ title: "正弦型函数的奇偶性", id: 21383 },
											{ title: "正弦型函数的单调性", id: 21382 },
											{ title: "正弦型函数在R上的值域", id: 21387 },
											{ title: "正弦型函数在区间上的值域", id: 21388 },
										],
									},
									{
										title: "三角函数的应用",
										children: [{ title: "三角函数的应用", id: 240411 }],
									},
								],
							},
						],
					},
					{
						title: "必修2",
						children: [
							{
								title: "平面向量及其应用",
								children: [
									{
										title: "平面向量的概念",
										children: [{ title: "向量概念的判断", id: 22273 }],
									},
									{
										title: "平面向量的运算",
										children: [
											{ title: "向量的加法", id: 22901 },
											{ title: "向量的减法", id: 22902 },
											{ title: "数乘向量", id: 22250 },
											{ title: "不依赖几何图形的向量运算", id: 22276 },
										],
									},
									{
										title: "平面向量的基本定理及坐标表示",
										children: [
											{ title: "平面向量基本定理", id: 22279 },
											{ title: "三点共线的向量表示", id: 22267 },
											{ title: "向量的坐标线性运算", id: 22270 },
											{ title: "平行向量", id: 22266 },
											{ title: "数量积的坐标运算", id: 22309 },
											{ title: "向量夹角的坐标运算", id: 22282 },
											{ title: "平面向量的基底", id: 22290 },
											{ title: "向量的数量积", id: 22249 },
											{ title: "数量积的运算律", id: 22274 },
											{ title: "利用数量积判断垂直关系", id: 22322 },
											{ title: "向量模的直接计算", id: 22331 },
											{ title: "向量垂直的坐标表示", id: 22272 },
										],
									},
									{
										title: "平面向量的应用",
										children: [{ title: "平面向量的应用", id: 240429 }],
									},
									{
										title: "解三角形",
										children: [
											{ title: "正弦定理", id: 22918 },
											{ title: "正弦定理的应用", id: 22919 },
											{ title: "余弦定理", id: 22939 },
											{ title: "余弦定理的应用", id: 22920 },
											{ title: "判断三角形解的个数", id: 22935 },
											{ title: "三角形的面积", id: 22937 },
											{ title: "综合判定三角形形状", id: 21521 },
										],
									},
								],
							},
							{
								title: "复数",
								children: [
									{
										title: "复数的概念",
										children: [{ title: "复数的概念", id: 22990 }],
									},
									{
										title: "复数的四则运算",
										children: [
											{ title: "复数的加法与减法", id: 22995 },
											{ title: "复数的乘法", id: 22996 },
											{ title: "共轭复数", id: 22997 },
											{ title: "复数的除法", id: 22998 },
										],
									},
									{
										title: "复数的三角表示",
										children: [{ title: "复数的三角表示", id: 240442 }],
									},
								],
							},
							{
								title: "立体几何初步",
								children: [
									{
										title: "基本立体图形",
										children: [
											{ title: "构成空间几何体的基本元素", id: 21542 },
											{ title: "正方体的展开图复原问题", id: 22162 },
											{ title: "棱柱中的截面问题", id: 22022 },
										],
									},
									{
										title: "立体图形的直观图",
										children: [{ title: "斜二测画法", id: 22069 }],
									},
									{
										title: "简单几何体的表面积与体积",
										children: [
											{ title: "圆柱、圆锥和圆台的表面积", id: 22283 },
											{ title: "球的体积", id: 22093 },
											{ title: "棱柱、棱锥、棱台和球的表面积", id: 22155 },
											{ title: "柱体的体积", id: 22049 },
											{ title: "锥体的体积", id: 22045 },
											{ title: "台体的体积", id: 22053 },
										],
									},
									{
										title: "空间点、直线、平面的位置关系",
										children: [
											{ title: "平面的基本性质与推论", id: 22101 },
											{ title: "三个平面的交线关系", id: 22293 },
										],
									},
									{
										title: "空间直线、平面的平行",
										children: [
											{ title: "空间中的平行关系", id: 22104 },
											{ title: "线线平行", id: 22035 },
										],
									},
									{
										title: "空间直线、平面的垂直",
										children: [{ title: "空间中的垂直关系", id: 22105 }],
									},
								],
							},
							{
								title: "统计",
								children: [
									{
										title: "随机抽样",
										children: [
											{ title: "简单随机抽样", id: 23110 },
											{ title: "分层抽样", id: 23114 },
											{ title: "系统抽样", id: 23112 },
										],
									},
									{
										title: "用样本估计总体",
										children: [
											{ title: "用样本的数字特征估计总体的数字特征", id: 23131 },
											{ title: "频率分布表", id: 23113 },
											{ title: "频率分布直方图", id: 23117 },
											{ title: "茎叶图与数字特征", id: 23130 },
										],
									},
								],
							},
							{
								title: "概率",
								children: [
									{
										title: "随机事件与概率",
										children: [
											{ title: "事件与基本事件空间", id: 23160 },
											{ title: "必然现象与随机现象", id: 23121 },
											{ title: "古典概型", id: 23076 },
											{ title: "几何概型", id: 23127 },
										],
									},
									{
										title: "事件的相互独立性",
										children: [{ title: "概率的加法公式", id: 23123 }],
									},
									{
										title: "频率与概率",
										children: [{ title: "频率与概率", id: 23122 }],
									},
								],
							},
						],
					},
					{
						title: "选择性必修1",
						children: [
							{
								title: "空间向量与立体几何",
								children: [
									{
										title: "空间向量及其运算",
										children: [{ title: "空间向量的线性运算", id: 22304 }],
									},
									{
										title: "空间向量基本定理",
										children: [
											{ title: "空间向量分解定理", id: 22352 },
											{ title: "共线与共面向量基本定理", id: 22351 },
										],
									},
									{
										title: "空间向量及其运算的坐标表示",
										children: [
											{ title: "空间向量的坐标混合运算", id: 22355 },
											{ title: "向量模的计算", id: 22252 },
											{ title: "空间向量平行、垂直和共面的条件", id: 22364 },
											{ title: "向量夹角的计算", id: 22362 },
										],
									},
									{
										title: "空间向量的应用",
										children: [
											{ title: "向量法求两条直线的夹角", id: 22114 },
											{ title: "平面的法向量", id: 22363 },
											{ title: "直线与平面的夹角", id: 22109 },
											{ title: "二面角及其度量", id: 22128 },
											{ title: "点线距离", id: 22064 },
											{ title: "点面距离与线面距离", id: 22357 },
										],
									},
								],
							},
							{
								title: "直线与圆的方程",
								children: [
									{
										title: "直线的倾斜角与斜率",
										children: [{ title: "直线的斜率和倾斜角", id: 22135 }],
									},
									{
										title: "直线方程",
										children: [
											{ title: "直线方程的五种形式", id: 22136 },
											{ title: "确定直线的位置", id: 22137 },
											{ title: "利用直线方程确定倾斜角和斜率", id: 22139 },
											{ title: "直线的平行关系", id: 22145 },
											{ title: "直线的垂直关系", id: 22146 },
										],
									},
									{
										title: "直线的交点坐标与距离公式",
										children: [{ title: "距离公式与中点公式", id: 22147 }],
									},
									{
										title: "圆的方程",
										children: [
											{ title: "圆的标准方程", id: 22369 },
											{ title: "圆的一般方程", id: 22370 },
										],
									},
									{
										title: "直线与圆、圆与圆的位...",
										children: [
											{ title: "直线和圆的位置关系", id: 22374 },
											{ title: "圆中的弦问题", id: 22371 },
											{ title: "圆和圆的位置关系", id: 22373 },
											{ title: "圆的切线问题", id: 22389 },
										],
									},
								],
							},
							{
								title: "圆锥曲线的方程",
								children: [
									{
										title: "椭圆",
										children: [
											{ title: "椭圆的标准方程", id: 22913 },
											{ title: "椭圆的几何性质", id: 23009 },
											{ title: "椭圆的离心率", id: 22916 },
											{ title: "根据条件确定椭圆方程", id: 23021 },
										],
									},
									{
										title: "双曲线",
										children: [
											{ title: "双曲线的标准方程", id: 22927 },
											{ title: "直线与双曲线交点问题", id: 22948 },
											{ title: "双曲线的几何性质", id: 22914 },
											{ title: "双曲线的渐近线", id: 22938 },
											{ title: "双曲线的离心率", id: 22915 },
											{ title: "根据条件求双曲线的标准方程", id: 23032 },
										],
									},
									{
										title: "抛物线",
										children: [
											{ title: "抛物线的标准方程", id: 22912 },
											{ title: "抛物线定义的应用", id: 22954 },
											{ title: "直线与圆锥曲线联立", id: 22957 },
										],
									},
								],
							},
						],
					},
					{
						title: "选择性必修2",
						children: [
							{
								title: "数列",
								children: [
									{
										title: "数列的概念",
										children: [
											{ title: "数列的概念", id: 21531 },
											{ title: "数列的通项公式", id: 21532 },
											{ title: "数列的递推公式", id: 21539 },
										],
									},
									{
										title: "等比数列",
										children: [
											{ title: "等比数列的概念", id: 21583 },
											{ title: "等比数列前n项和公式", id: 21599 },
											{ title: "等比数列通项与公比的求法", id: 21585 },
											{ title: "等比中项", id: 21591 },
											{ title: "等比数列项中角标和的秘密", id: 21593 },
											{ title: "应用等比数列求和公式求和", id: 21600 },
											{ title: "等比数列S_n的代数特征", id: 21603 },
											{ title: "和的等比性质", id: 21606 },
										],
									},
									{
										title: "等差数列",
										children: [
											{ title: "等差数列的概念", id: 21545 },
											{ title: "等差中项", id: 21551 },
											{ title: "等差数列的前n项和公式", id: 21561 },
											{ title: "S_n与a_n之间的关系", id: 21566 },
											{ title: "大招流", id: 21568 },
											{ title: "利用通项公式求等差数列中的项", id: 21547 },
											{ title: "等差数列中的中项性质", id: 21552 },
											{ title: "等差数列项中角标和的秘密", id: 21553 },
											{ title: "前n项和公式的基本用法", id: 21562 },
											{ title: "等差数列S_n的的代数特征", id: 240530 },
										],
									},
									{
										title: "数学归纳法",
										children: [
											{ title: "数学归纳法", id: 22343 },
											{ title: "数学归纳法的注意事项", id: 25442 },
										],
									},
								],
							},
							{
								title: "一元函数的导数及其应用",
								children: [
									{
										title: "导数的概念及其意义",
										children: [
											{ title: "瞬时速度与导数", id: 22415 },
											{ title: "导数的几何意义", id: 22832 },
											{ title: "平均变化率", id: 22414 },
										],
									},
									{
										title: "导数的运算",
										children: [
											{ title: "导函数的直接计算", id: 22426 },
											{ title: "复合函数的导数", id: 22424 },
											{ title: "基本初等函数的导数", id: 22413 },
											{ title: "函数切线问题", id: 22417 },
										],
									},
									{
										title: "导数在研究函数中的应用",
										children: [
											{ title: "利用导数判断函数的单调性", id: 22421 },
											{ title: "函数的极值", id: 22427 },
											{ title: "图象法分析函数零点", id: 22430 },
											{ title: "函数图象与导数", id: 22435 },
											{ title: "三次函数相关问题", id: 22871 },
											{ title: "导数的实际应用", id: 22803 },
										],
									},
								],
							},
						],
					},
					{
						title: "选择性必修3",
						children: [
							{
								title: "计数原理",
								children: [
									{
										title: "分类加法计数原理与分步乘法计数原理",
										children: [{ title: "基本计数原理", id: 23056 }],
									},
									{
										title: "排列与组合",
										children: [
											{ title: "排列", id: 23063 },
											{ title: "利用排列数公式解方程", id: 23079 },
											{ title: "组合", id: 23085 },
											{ title: "组合数公式解方程", id: 24444 },
											{ title: "分堆问题", id: 23094 },
											{ title: "捆绑法", id: 23072 },
											{ title: "插空法", id: 23067 },
											{ title: "组合数的化简计算与证明", id: 23089 },
											{ title: "乘法原理与组合综合", id: 23090 },
											{ title: "加法原理与组合综合", id: 23080 },
										],
									},
									{
										title: "二项式定理",
										children: [
											{ title: "二项式定理", id: 23109 },
											{ title: "求二项展开式中的特定项", id: 23102 },
											{ title: "二项式系数与系数和", id: 23105 },
											{ title: "二项式中的最大项和最小项", id: 23106 },
											{ title: "赋值法求和", id: 23103 },
										],
									},
								],
							},
							{
								title: "随机变量及其分布",
								children: [
									{
										title: "条件概率与全概率公式",
										children: [{ title: "随机事件发生的概率", id: 23091 }],
									},
									{
										title: "离散型随机变量及其分布",
										children: [{ title: "离散型随机变量的分布列", id: 23128 }],
									},
									{
										title: "离散型随机变量的数字特征",
										children: [
											{ title: "数学期望及性质", id: 23158 },
											{ title: "离散型随机变量的方差", id: 23137 },
										],
									},
									{
										title: "二项分布与超几何分布",
										children: [
											{ title: "二项分布", id: 23155 },
											{ title: "超几何分布", id: 23165 },
										],
									},
									{
										title: "正态分布",
										children: [{ title: "正态分布", id: 23166 }],
									},
								],
							},
							{
								title: "成对数据的统计分析",
								children: [
									{
										title: "成对数据的统计相关性",
										children: [{ title: "成对数据的统计相关性", id: 240569 }],
									},
									{
										title: "一元线性回归模型及其应用",
										children: [{ title: "一元线性回归模型及其应用", id: 240570 }],
									},
									{
										title: "列联表与独立检验",
										children: [{ title: "独立性检验", id: 23168 }],
									},
								],
							},
						],
					},
				],
				[
					{
						title: "必修1",
						children: [
							{
								title: "运动的描述",
								children: [
									{
										title: "质点 参考系",
										children: [
											{ title: "运动与质点", id: 22827 },
											{ title: "参考系", id: 22824 },
											{ title: "坐标系", id: 22469 },
										],
									},
									{
										title: "时间 位移",
										children: [
											{ title: "时间", id: 22860 },
											{ title: "位移", id: 22851 },
											{ title: "矢量和标量", id: 22863 },
											{ title: "一维及二维平面求解路程、位移", id: 26954 },
											{ title: "时间的计量", id: 26995 },
											{ title: "三角形法则与多边形法则", id: 26959 },
											{ title: "三维空间求解路程、位移", id: 26957 },
										],
									},
									{
										title: "位置变化快慢的描述—...",
										children: [
											{ title: "速度", id: 22864 },
											{ title: "平均速度和瞬时速度", id: 22866 },
											{ title: "速度和速率的区别", id: 23558 },
											{ title: "从直线运动的x-t图象看速度", id: 22474 },
										],
									},
									{
										title: "速度变化快慢的描述—...",
										children: [
											{ title: "加速度", id: 22476 },
											{ title: "加速度、速度的变化量、速度的关系", id: 22477 },
											{ title: "从速度时间图象看加速度", id: 26968 },
											{ title: "三种运动图象的比较", id: 26969 },
										],
									},
								],
							},
							{
								title: "匀变速直线运动的研究",
								children: [
									{
										title: "实验：探究小车速度随...",
										children: [
											{ title: "打点计时器", id: 23560 },
											{ title: "打点计时器使用步骤与纸带处理", id: 22475 },
											{ title: "利用打点计时器研究物体速度的变化", id: 26973 },
											{ title: "利用纸带画出v-t图象", id: 26974 },
											{ title: "利用纸带求物体加速度的两种方法", id: 26980 },
											{ title: "频闪照相和数据处理", id: 26966 },
											{ title: "光电计时系统", id: 26967 },
											{ title: "超声波测速", id: 27457 },
										],
									},
									{
										title: "匀变速直线运动的速度...",
										children: [
											{ title: "各种直线运动的v-t图象", id: 26984 },
											{ title: "速度与时间的关系", id: 26444 },
											{ title: "v-t图象的面积含义", id: 26985 },
											{ title: "v-t图象面积的进一步讨论", id: 26986 },
											{ title: "中点时刻速度与平均速度", id: 26987 },
											{ title: "利用贴纸条法画出v-t图象", id: 26988 },
										],
									},
									{
										title: "匀变速直线运动的位移...",
										children: [
											{ title: "位移与时间关系", id: 27417 },
											{ title: "匀变速直线运动连续相等时间位移特点", id: 27418 },
											{
												title: "利用打点计时器研究匀变速直线运动物体的加速度",
												id: 27419,
											},
										],
									},
									{
										title: "自由落体运动",
										children: [
											{ title: "自由落体运动", id: 26455 },
											{ title: "应用自由落体运动规律解题", id: 26456 },
											{ title: "自由落体运动规律的灵活运用", id: 26998 },
											{ title: "测重力加速度", id: 26457 },
											{ title: "自由落体运动规律的实际应用", id: 26997 },
											{ title: "自由落体运动的综合问题", id: 27290 },
											{ title: "竖直上抛运动", id: 26458 },
											{ title: "应用竖直上抛运动规律解题", id: 27001 },
										],
									},
								],
							},
							{
								title: "相互作用—力",
								children: [
									{
										title: "重力与弹力",
										children: [
											{ title: "重力", id: 27305 },
											{ title: "重力的方向", id: 27306 },
											{ title: "重心", id: 27307 },
											{ title: "复杂物体的重心", id: 27308 },
											{ title: "形变与弹性形变", id: 27329 },
											{ title: "几种常见的弹力", id: 27330 },
											{ title: "弹力的方向", id: 27331 },
											{ title: "弹力有无的判断", id: 27332 },
											{ title: "弹力大小与胡克定律", id: 27333 },
											{ title: "胡克定律的理解与应用", id: 27352 },
											{ title: "弹簧测力计的使用及读数", id: 27353 },
											{ title: "弹簧的串联", id: 27354 },
											{ title: "弹簧的并联", id: 27355 },
										],
									},
									{
										title: "摩擦力",
										children: [
											{ title: "摩擦力产生的条件", id: 27344 },
											{ title: "静摩擦力", id: 27345 },
											{ title: "滑动摩擦力", id: 27346 },
											{ title: "摩擦因数的测量", id: 27347 },
											{ title: "摩擦力方向的判断方法", id: 27348 },
											{ title: "传送带摩擦力方向的判断方法", id: 27349 },
											{ title: "摩擦力大小的计算", id: 27350 },
											{ title: "摩擦力的深入理解", id: 27351 },
										],
									},
									{
										title: "牛顿第三定律",
										children: [
											{ title: "牛顿第三定律", id: 26476 },
											{ title: "作用力、反作用力与平衡力的比较", id: 27398 },
											{ title: "牛顿第三定律应用实例", id: 27399 },
											{ title: "粗糙面上拔河问题", id: 27400 },
											{ title: "光滑面上拔河问题", id: 27401 },
											{ title: "用牛顿第三定律进行受力分析", id: 27402 },
										],
									},
									{
										title: "力的合成和分解",
										children: [
											{ title: "合力与分力的概念", id: 27366 },
											{ title: "探究共点力的合成法则的实验操作", id: 27367 },
											{ title: "探究共点力的合成法则的实验误差分析", id: 27368 },
											{ title: "力的平行四边形定则", id: 27370 },
											{ title: "合力与分力的大小和方向关系", id: 27371 },
											{ title: "在一定条件下进行力的分解", id: 27317 },
											{ title: "按照力的作用效果进行力的分解", id: 27318 },
											{ title: "力的正交分解", id: 27319 },
											{ title: "矢量运算法则", id: 27316 },
										],
									},
									{
										title: "共点力的平衡",
										children: [
											{ title: "正交分解法处理平衡问题", id: 27320 },
											{ title: "摩擦力相关的平衡问题（1）", id: 27321 },
											{ title: "摩擦力相关的平衡问题（2）", id: 27322 },
											{ title: "力的反向法处理三力平衡问题", id: 27323 },
											{ title: "三角形法则处理三力平衡问题", id: 27324 },
											{ title: "三角形法则处理力的变化问题", id: 27325 },
											{ title: "整体法处理系统平衡问题", id: 27326 },
											{ title: "整体+隔离的方法研究系统平衡问题", id: 27327 },
										],
									},
								],
							},
							{
								title: "运动和力的关系",
								children: [
									{
										title: "牛顿第一定律",
										children: [
											{ title: "牛顿第一定律", id: 26475 },
											{ title: "惯性与质量", id: 26866 },
										],
									},
									{
										title: "实验：探究加速度与力...",
										children: [
											{ title: "实验：探究加速度与力、质量的关系", id: 27414 },
											{
												title: "实验：探究加速度与力、质量的关系的数据分析",
												id: 27415,
											},
											{
												title: "实验：探究加速度与力、质量的关系的误差分析",
												id: 27416,
											},
										],
									},
									{
										title: "牛顿第二定律",
										children: [
											{ title: "牛顿第二定律", id: 26477 },
											{ title: "牛顿第二定律中F代表合外力", id: 27334 },
											{ title: "牛顿第二定律的矢量性与瞬时性", id: 27335 },
											{ title: "牛顿第二定律的同体性与独立性", id: 27336 },
											{ title: "牛顿第二定律——突变问题", id: 27337 },
										],
									},
									{
										title: "力学单位制",
										children: [
											{ title: "力学单位制", id: 27359 },
											{ title: "力学单位制之量纲分析法", id: 27360 },
										],
									},
									{
										title: "牛顿运动定律的应用",
										children: [
											{ title: "已知力求运动的问题", id: 27433 },
											{ title: "已知运动求力的问题", id: 27434 },
											{ title: "水平传送带问题", id: 27435 },
											{ title: "斜面传送带问题", id: 27436 },
											{ title: "加速度相同的连接体问题", id: 27437 },
											{ title: "带滑轮的连接体问题", id: 27450 },
											{ title: "系统的牛顿第二定律", id: 27451 },
											{ title: "板块模型", id: 27452 },
											{ title: "动力学中的图象问题", id: 27453 },
											{ title: "摩擦力的临界问题", id: 27454 },
											{ title: "与绳子有关的临界问题", id: 27455 },
											{ title: "物体间分离的临界问题", id: 27456 },
										],
									},
									{
										title: "超重和失重",
										children: [
											{ title: "超重和失重的原理", id: 27448 },
											{ title: "超重和失重的应用", id: 27449 },
										],
									},
								],
							},
						],
					},
					{
						title: "必修2",
						children: [
							{
								title: "抛体运动",
								children: [
									{
										title: "曲线运动",
										children: [
											{ title: "曲线运动", id: 27474 },
											{ title: "物体做曲线运动的条件", id: 27475 },
											{ title: "曲线运动中力对速度的改变", id: 27476 },
										],
									},
									{
										title: "运动的合成与分解",
										children: [
											{ title: "运动的合成与分解", id: 27477 },
											{ title: "两个直线运动的合运动性质的判断", id: 27478 },
											{ title: "小船如何渡河时间最短？", id: 27479 },
											{ title: "小船如何渡河距离最短？", id: 27480 },
											{ title: "绳子的“关联”速度问题", id: 27515 },
											{ title: "杆以及相互接触物体的“关联”速度问题", id: 27516 },
											{ title: "变换参考系相关的运动合成与分解", id: 27518 },
										],
									},
									{
										title: "实验：探究平抛运动的...",
										children: [
											{ title: "研究平抛运动的实验装置和方法", id: 27444 },
											{ title: "求平抛运动的初速度", id: 27445 },
											{ title: "求平抛运动初速度的其他方法", id: 27446 },
										],
									},
									{
										title: "抛体运动的规律",
										children: [
											{ title: "平抛运动特点", id: 27314 },
											{ title: "平抛运动的位移分析", id: 27315 },
											{ title: "平抛运动的速度分析", id: 27339 },
											{ title: "频闪照相下的平抛运动分析", id: 27340 },
											{ title: "平抛运动的速度矢量图", id: 27341 },
											{ title: "平抛运动中位移与水平夹角的应用", id: 27342 },
											{ title: "平抛运动中速度与水平夹角的应用", id: 27343 },
											{ title: "斜抛运动", id: 27357 },
											{ title: "抛铅球的最佳角度", id: 27358 },
											{
												title: "将斜抛运动到最高点看作平抛运动的逆过程",
												id: 27376,
											},
										],
									},
								],
							},
							{
								title: "圆周运动",
								children: [
									{
										title: "圆周运动",
										children: [
											{ title: "圆周运动的线速度", id: 27361 },
											{ title: "圆周运动的角速度、周期和频率", id: 27362 },
											{ title: "三种传动装置", id: 27364 },
											{ title: "描述圆周运动物理量之间的关系", id: 27363 },
											{ title: "圆周运动与直线运动的综合问题", id: 27365 },
										],
									},
									{
										title: "向心力",
										children: [
											{ title: "向心力", id: 27411 },
											{ title: "向心力的验证", id: 27412 },
											{ title: "变速圆周运动的受力", id: 27483 },
											{ title: "一般的曲线运动", id: 27485 },
											{ title: "水平面的圆周运动", id: 27413 },
											{ title: "圆锥摆的周期", id: 27481 },
											{ title: "临界态匀速圆周运动问题", id: 27482 },
											{ title: "相互连接物体的临界态问题", id: 27484 },
										],
									},
									{
										title: "向心加速度",
										children: [
											{ title: "向心加速度的推导", id: 27409 },
											{ title: "向心加速度的理解与应用", id: 27410 },
										],
									},
									{
										title: "生活中的圆周运动",
										children: [
											{ title: "汽车拐弯问题", id: 27521 },
											{ title: "火车、飞机拐弯问题", id: 27522 },
											{ title: "拱形桥和下凹桥问题", id: 27523 },
											{ title: "向心运动与离心运动", id: 27527 },
											{ title: "航天器中的失重现象", id: 27524 },
											{
												title: "绳子连接小球在竖直面内做变速圆周运动",
												id: 27525,
											},
											{ title: "硬杆连接小球在竖直面内做匀速圆周运动", id: 27526 },
										],
									},
								],
							},
							{
								title: "万有引力与宇宙航行",
								children: [
									{
										title: "行星的运动",
										children: [
											{ title: "天文学历史简介", id: 27528 },
											{ title: "开普勒三定律", id: 27529 },
											{ title: "开普勒三定律的应用", id: 27530 },
										],
									},
									{
										title: "万有引力定律",
										children: [
											{ title: "月—地检验", id: 27532 },
											{ title: "万有引力定律", id: 27533 },
											{ title: "卡文迪许的扭秤实验", id: 27534 },
											{ title: "重力与万有引力的关系", id: 27536 },
											{ title: "“黄金代换”公式", id: 27535 },
											{ title: "万有引力定律的推论", id: 27537 },
											{ title: "万有引力定律的成就", id: 27633 },
										],
									},
									{
										title: "万有引力理论的成就",
										children: [
											{ title: "求星球质量的几种方法", id: 27428 },
											{ title: "求星球密度的几种方法", id: 27429 },
											{ title: "行星追及和最大观测角问题", id: 27425 },
											{ title: "行星椭圆轨道运动的时间问题", id: 27426 },
											{ title: "卫星的可视性问题", id: 27427 },
											{ title: "利用一块手表测量星球密度", id: 27430 },
										],
									},
									{
										title: "宇宙航行",
										children: [
											{ title: "人造卫星的轨道", id: 27564 },
											{ title: "同步卫星", id: 27565 },
											{ title: "牛顿大炮", id: 27486 },
											{ title: "第一宇宙速度", id: 27491 },
											{ title: "第二、第三宇宙速度", id: 27492 },
											{ title: "卫星的失重", id: 27566 },
											{ title: "几类卫星的比较", id: 27493 },
											{ title: "卫星变轨问题", id: 27494 },
											{ title: "人造卫星的多次变轨问题", id: 27495 },
											{ title: "双星", id: 27496 },
											{ title: "黑洞与暗物质", id: 27538 },
										],
									},
									{
										title: "相对论时空观与牛顿力...",
										children: [
											{ title: "低速", id: 27539 },
											{ title: "宏观、弱引力", id: 27540 },
										],
									},
								],
							},
							{
								title: "机械能守恒定律",
								children: [
									{
										title: "功与功率",
										children: [
											{ title: "功", id: 27568 },
											{ title: "正功与负功", id: 27569 },
											{ title: "功率", id: 26527 },
											{ title: "机车恒功率启动", id: 27577 },
											{ title: "机车匀加速启动", id: 27578 },
											{ title: "恒力做功", id: 27570 },
											{ title: "弹力做功问题", id: 27571 },
											{ title: "静摩擦力做功问题", id: 27572 },
											{ title: "滑动摩擦力做功问题", id: 27573 },
											{ title: "变力做功问题", id: 27574 },
											{ title: "作用力与反作用力做功问题", id: 27575 },
											{ title: "恒力做功的功率", id: 27576 },
										],
									},
									{
										title: "重力势能",
										children: [
											{ title: "重力做功与重力势能", id: 27579 },
											{ title: "重力做功与重力势能的关系", id: 27580 },
											{ title: "与重心变化有关的重力做功", id: 27581 },
											{ title: "弹性势能的概念和表达式", id: 27783 },
											{ title: "弹性势能的计算", id: 27784 },
										],
									},
									{
										title: "动能和动能定理",
										children: [
											{ title: "动能的概念", id: 27786 },
											{ title: "动能定理的内容和用法", id: 27787 },
											{ title: "动能定理解决多阶段运动问题", id: 27788 },
											{ title: "动能定理解决变力做功问题", id: 27789 },
											{ title: "动能定理解决水平变速圆周运动问题", id: 27790 },
											{
												title: "动能定理解决竖直面内变速圆周运动问题",
												id: 27791,
											},
											{ title: "动能定理解决抛体问题", id: 27792 },
										],
									},
									{
										title: "机械能守恒定律",
										children: [
											{ title: "机械能变化与力做功的关系", id: 27582 },
											{ title: "机械能守恒的条件和应用", id: 27583 },
											{ title: "机械能守恒时的能量转化", id: 27584 },
											{ title: "做功与能量变化关系的总结", id: 27585 },
											{ title: "天体的机械能", id: 27586 },
										],
									},
									{
										title: "实验：验证机械能守恒...",
										children: [
											{ title: "验证机械能守恒的实验装置", id: 27594 },
											{ title: "验证机械能守恒的数据处理", id: 27595 },
										],
									},
								],
							},
						],
					},
					{
						title: "必修3",
						children: [
							{
								title: "静电场及其应用",
								children: [
									{
										title: "电荷",
										children: [
											{ title: "电荷", id: 27795 },
											{ title: "使物体带电的三种方式", id: 27796 },
											{ title: "电荷守恒定律元电荷", id: 27797 },
										],
									},
									{
										title: "库仑定律",
										children: [
											{ title: "库仑定律", id: 27798 },
											{ title: "库仑扭秤", id: 27799 },
											{ title: "库仑定律的再讨论", id: 27800 },
											{ title: "两球接触后分开库仑力的计算", id: 27801 },
										],
									},
									{
										title: "电场 电场强度",
										children: [
											{ title: "电场强度的定义", id: 27803 },
											{ title: "点电荷电场强度的叠加", id: 30221 },
											{ title: "常见的电场分布", id: 27805 },
											{ title: "匀强电场的产生和性质", id: 27806 },
										],
									},
									{
										title: "静电的防止与利用",
										children: [
											{ title: "静电平衡", id: 27827 },
											{ title: "导体上的电荷和电势分布", id: 27828 },
											{ title: "尖端放电", id: 27829 },
											{ title: "静电屏蔽", id: 28087 },
										],
									},
								],
							},
							{
								title: "静电场中的能量",
								children: [
									{
										title: "电势能和电势",
										children: [
											{ title: "电势能", id: 27810 },
											{ title: "电势", id: 27811 },
										],
									},
									{
										title: "电势差",
										children: [
											{ title: "电势差", id: 27812 },
											{ title: "等势面和等势线", id: 27813 },
											{ title: "常见电场的电势分布", id: 27814 },
										],
									},
									{
										title: "电势差与电场强度的关...",
										children: [
											{ title: "用等分法计算匀强电场中的电场与电势", id: 28098 },
											{ title: "实验：用描迹法绘制等势线", id: 240043 },
										],
									},
									{
										title: "电容器的电容",
										children: [
											{ title: "电容器", id: 28094 },
											{ title: "电容器的电容", id: 28095 },
											{ title: "电容器在Q不变时的探究", id: 28142 },
											{ title: "电容器在U不变时的探究", id: 28143 },
										],
									},
									{
										title: "带电粒子在电场中的运...",
										children: [
											{ title: "力的角度分析带电粒子的运动", id: 27816 },
											{ title: "能量角度分析带电粒子的运动", id: 27818 },
											{ title: "带电粒子在电容器中的偏转", id: 27823 },
											{ title: "示波器原理", id: 27825 },
											{ title: "密立根油滴实验", id: 27817 },
										],
									},
								],
							},
							{
								title: "电路及其应用",
								children: [
									{
										title: "电源和电流",
										children: [
											{ title: "电源", id: 27832 },
											{ title: "电流的定义式", id: 27834 },
											{ title: "电流的微观表达式", id: 27835 },
											{ title: "导线中的电场", id: 240054 },
										],
									},
									{
										title: "导体的电阻",
										children: [
											{ title: "电阻率", id: 27857 },
											{ title: "常见半导体", id: 240058 },
											{ title: "超导材料", id: 240059 },
										],
									},
									{
										title: "实验：导体电阻率的测...",
										children: [
											{ title: "金属电阻率的测量方法", id: 27858 },
											{ title: "螺旋测微器的结构和使用", id: 27860 },
											{ title: "伏安法测电阻的两种方式", id: 27854 },
											{ title: "滑动变阻器的两种接法", id: 27855 },
											{ title: "游标卡尺的结构和使用", id: 27859 },
											{ title: "限流法与分压法的再讨论", id: 27856 },
										],
									},
									{
										title: "串联电路和并联电路",
										children: [
											{ title: "串联电路的性质", id: 27843 },
											{ title: "并联电路的性质", id: 27844 },
											{ title: "电压表的改装", id: 27847 },
											{ title: "电流表的改装", id: 27848 },
										],
									},
									{
										title: "实验：练习使用多用电...",
										children: [
											{ title: "欧姆表原理", id: 27864 },
											{ title: "欧姆表的使用", id: 27865 },
											{ title: "多用电表", id: 27866 },
										],
									},
								],
							},
							{
								title: "电能 能量守恒定律",
								children: [
									{
										title: "电路中的能量转化",
										children: [
											{ title: "电功率", id: 27850 },
											{ title: "焦耳定律", id: 27851 },
										],
									},
									{
										title: "闭合电路的欧姆定律",
										children: [
											{ title: "闭合电路欧姆定律", id: 28147 },
											{ title: "闭合电路中的路端电压", id: 28148 },
											{ title: "闭合电路中的功率和效率", id: 28288 },
											{ title: "闭合电路中的U－I图象问题", id: 28289 },
											{ title: "直流电路动态分析", id: 28290 },
											{ title: "含电容的直流电路分析", id: 28291 },
										],
									},
									{
										title: "实验：电池电动势和内...",
										children: [
											{ title: "测电源的电动势和内阻的实验方法", id: 28293 },
											{ title: "测电源的电动势和内阻的其它方法", id: 28295 },
											{ title: "测电源的电动势和内阻的误差分析", id: 28294 },
										],
									},
									{
										title: "能源与可持续发展",
										children: [
											{ title: "能量守恒定律", id: 27596 },
											{ title: "能量的耗散", id: 27597 },
										],
									},
								],
							},
							{
								title: "电磁感应与电磁波初步",
								children: [
									{
										title: "磁场 磁感线",
										children: [
											{ title: "磁现象和电流的磁效应", id: 27873 },
											{ title: "磁感线", id: 26612 },
											{ title: "安培分子电流假说", id: 27877 },
											{ title: "三种常见的电流磁场", id: 27878 },
											{ title: "安培定则", id: 27876 },
											{ title: "地磁场", id: 27880 },
										],
									},
									{
										title: "磁感应强度 磁通量",
										children: [
											{ title: "磁感应强度的定义", id: 27874 },
											{ title: "磁通量", id: 27881 },
											{ title: "磁通量的正负", id: 27882 },
										],
									},
									{
										title: "电磁感应现象及应用",
										children: [
											{ title: "划时代的发现", id: 27915 },
											{ title: "切割磁感线与感应电流的产生", id: 27916 },
											{ title: "磁通量变化与感应电流的产生", id: 27917 },
											{ title: "切割磁感线和磁通量变化", id: 27918 },
										],
									},
									{
										title: "电磁波的发现及应用",
										children: [
											{ title: "麦克斯韦电磁场理论", id: 28077 },
											{ title: "振荡电路", id: 28079 },
											{ title: "有效发射电磁波的条件", id: 28082 },
											{ title: "电磁波谱的介绍", id: 28100 },
											{ title: "无线电波", id: 28101 },
										],
									},
									{
										title: "能量量子化",
										children: [{ title: "能量子", id: 28105 }],
									},
								],
							},
						],
					},
					{
						title: "选择性必修1",
						children: [
							{
								title: "动量守恒定律",
								children: [
									{
										title: "动量",
										children: [
											{ title: "动量", id: 29545 },
											{ title: "冲量", id: 29546 },
										],
									},
									{
										title: "动量定理",
										children: [
											{ title: "动量定理的推导和内容", id: 29547 },
											{ title: "利用动量定理求平均冲力", id: 29548 },
											{ title: "动量定理与动能定理的比较", id: 29549 },
											{ title: "利用动量定理求力的冲量或动量变化量", id: 29550 },
											{ title: "利用动量定理求解多过程问题", id: 29551 },
										],
									},
									{
										title: "动量守恒定律",
										children: [
											{ title: "动量守恒定律的内容和条件", id: 29554 },
											{ title: "动量守恒定律的计算", id: 29555 },
											{ title: "分方向上的动量守恒定律", id: 29556 },
										],
									},
									{
										title: "实验：验证动量守恒定...",
										children: [{ title: "实验：验证动量守恒定律", id: 240115 }],
									},
									{
										title: "弹性碰撞和非弹性碰撞",
										children: [
											{ title: "碰撞的分类", id: 29560 },
											{ title: "弹性碰撞", id: 29561 },
											{ title: "速度互换的应用", id: 29562 },
											{ title: "完全非弹性碰撞", id: 30209 },
										],
									},
									{
										title: "反冲现象 火箭",
										children: [
											{ title: "反冲运动", id: 30212 },
											{ title: "火箭", id: 30214 },
											{ title: "人船问题", id: 30213 },
										],
									},
								],
							},
							{
								title: "机械振动",
								children: [
									{
										title: "简谐运动",
										children: [
											{ title: "什么是简谐运动", id: 28036 },
											{ title: "简谐运动的规律", id: 28276 },
											{ title: "简谐运动的图像", id: 28277 },
										],
									},
									{
										title: "简谐运动的描述",
										children: [{ title: "描述简谐运动的物理量", id: 28038 }],
									},
									{
										title: "简谐运动的回复力和能...",
										children: [
											{ title: "简谐运动的回复力", id: 28040 },
											{ title: "简谐运动的能量", id: 28041 },
											{ title: "用参考圆分析简谐运动", id: 28161 },
											{ title: "简谐运动的表达式", id: 28278 },
										],
									},
									{
										title: "单摆",
										children: [
											{ title: "单摆", id: 28043 },
											{ title: "单摆的周期", id: 28044 },
											{ title: "单摆的周期与摆长的关系", id: 28045 },
											{ title: "单摆的周期与重力加速度的关系", id: 28046 },
										],
									},
									{
										title: "实验：用单摆测量重力...",
										children: [{ title: "实验：用单摆测量重力加速度", id: 240135 }],
									},
									{
										title: "受迫振动 共振",
										children: [
											{ title: "受迫振动", id: 28048 },
											{ title: "共振", id: 28049 },
										],
									},
								],
							},
							{
								title: "机械波",
								children: [
									{
										title: "波的形成",
										children: [
											{ title: "波的形成和传播条件", id: 28050 },
											{ title: "横波和纵波", id: 28051 },
										],
									},
									{
										title: "波的描述",
										children: [
											{ title: "波的图像", id: 28053 },
											{ title: "波长", id: 28055 },
											{ title: "频率和波速", id: 28056 },
											{ title: "振动图像与波动图像", id: 28057 },
										],
									},
									{
										title: "波的反射、折射和衍射",
										children: [
											{ title: "惠更斯原理", id: 28061 },
											{ title: "波的反射", id: 28062 },
											{ title: "波的折射", id: 28063 },
											{ title: "波的衍射", id: 28064 },
										],
									},
									{
										title: "波的干涉",
										children: [
											{ title: "波的叠加原理", id: 28066 },
											{ title: "波的干涉", id: 28069 },
											{ title: "叠加波振动方向的判断", id: 28068 },
											{ title: "相干波源", id: 28070 },
										],
									},
									{
										title: "多普勒效应",
										children: [
											{ title: "多普勒效应", id: 28074 },
											{ title: "多普勒效应频率变化计算", id: 28075 },
										],
									},
								],
							},
							{
								title: "光",
								children: [
									{
										title: "光的折射",
										children: [
											{ title: "光的折射", id: 28324 },
											{ title: "折射定律的应用", id: 28325 },
											{ title: "折射定律的联系实际问题", id: 28326 },
										],
									},
									{
										title: "全反射",
										children: [
											{ title: "全反射", id: 28349 },
											{ title: "全反射棱镜", id: 28350 },
											{ title: "光导纤维", id: 28351 },
											{ title: "海市蜃楼", id: 28352 },
											{ title: "平行玻璃砖", id: 28353 },
											{ title: "光在介质中传播的时间", id: 28354 },
										],
									},
									{
										title: "光的干涉",
										children: [
											{ title: "相干光源", id: 28330 },
											{ title: "干涉条纹的加强与减弱", id: 28331 },
											{ title: "干涉条纹的间距", id: 28332 },
										],
									},
									{
										title: "实验：用双缝干涉测量...",
										children: [
											{ title: "实验：测定光波的波长", id: 28333 },
											{ title: "测定光波波长的数据处理", id: 28334 },
										],
									},
									{
										title: "光的衍射",
										children: [
											{ title: "光的衍射", id: 28342 },
											{ title: "狭缝衍射", id: 28343 },
											{ title: "其它衍射", id: 28344 },
										],
									},
									{
										title: "光的偏振 激光",
										children: [
											{ title: "光的偏振", id: 28345 },
											{ title: "偏振镜", id: 28346 },
											{ title: "激光的产生与特点", id: 28357 },
											{ title: "激光的应用", id: 28358 },
										],
									},
								],
							},
						],
					},
					{
						title: "选择性必修2",
						children: [
							{
								title: "安培力与洛伦兹力",
								children: [
									{
										title: "磁场对通电导线的作用...",
										children: [
											{ title: "电荷", id: 27795 },
											{ title: "使物体带电的三种方式", id: 27796 },
											{ title: "电荷守恒定律元电荷", id: 27797 },
											{ title: "库仑定律", id: 27798 },
										],
									},
									{
										title: "磁场对运动电荷的作用...",
										children: [
											{ title: "库仑扭秤", id: 27799 },
											{ title: "库仑定律的再讨论", id: 27800 },
											{ title: "两球接触后分开库仑力的计算", id: 27801 },
											{ title: "电场强度的定义", id: 27803 },
										],
									},
									{
										title: "带电粒子在匀强磁场中...",
										children: [
											{ title: "点电荷电场强度的叠加", id: 30221 },
											{ title: "常见的电场分布", id: 27805 },
										],
									},
									{
										title: "质谱仪与回旋加速器",
										children: [
											{ title: "匀强电场的产生和性质", id: 27806 },
											{ title: "静电平衡", id: 27827 },
										],
									},
								],
							},
							{
								title: "电磁感应",
								children: [
									{
										title: "楞次定律",
										children: [
											{ title: "导体上的电荷和电势分布", id: 27828 },
											{ title: "尖端放电", id: 27829 },
											{ title: "静电屏蔽", id: 28087 },
											{ title: "电势能", id: 27810 },
											{ title: "电势", id: 27811 },
										],
									},
									{
										title: "法拉第电磁感应定律",
										children: [
											{ title: "电势差", id: 27812 },
											{ title: "等势面和等势线", id: 27813 },
											{ title: "常见电场的电势分布", id: 27814 },
											{ title: "用等分法计算匀强电场中的电场与电势", id: 28098 },
											{ title: "实验：用描迹法绘制等势线", id: 240043 },
										],
									},
									{
										title: "涡流、电磁阻尼和电磁...",
										children: [
											{ title: "电容器", id: 28094 },
											{ title: "电容器的电容", id: 28095 },
											{ title: "电容器在Q不变时的探究", id: 28142 },
											{ title: "电容器在U不变时的探究", id: 28143 },
										],
									},
									{
										title: "互感和自感",
										children: [
											{ title: "力的角度分析带电粒子的运动", id: 27816 },
											{ title: "能量角度分析带电粒子的运动", id: 27818 },
											{ title: "带电粒子在电容器中的偏转", id: 27823 },
											{ title: "示波器原理", id: 27825 },
											{ title: "密立根油滴实验", id: 27817 },
										],
									},
								],
							},
							{
								title: "交变电流",
								children: [
									{
										title: "交变电流",
										children: [
											{ title: "电源", id: 27832 },
											{ title: "电流的定义式", id: 27834 },
											{ title: "电流的微观表达式", id: 27835 },
										],
									},
									{
										title: "交变电流的描述",
										children: [
											{ title: "导线中的电场", id: 240054 },
											{ title: "电阻率", id: 27857 },
											{ title: "常见半导体", id: 240058 },
											{ title: "超导材料", id: 240059 },
										],
									},
									{
										title: "变压器",
										children: [
											{ title: "金属电阻率的测量方法", id: 27858 },
											{ title: "螺旋测微器的结构和使用", id: 27860 },
											{ title: "伏安法测电阻的两种方式", id: 27854 },
											{ title: "滑动变阻器的两种接法", id: 27855 },
										],
									},
									{
										title: "电能的输送",
										children: [
											{ title: "游标卡尺的结构和使用", id: 27859 },
											{ title: "限流法与分压法的再讨论", id: 27856 },
											{ title: "串联电路的性质", id: 27843 },
											{ title: "并联电路的性质", id: 27844 },
										],
									},
								],
							},
							{
								title: "电磁振荡与电磁波",
								children: [
									{
										title: "电磁振荡",
										children: [
											{ title: "电压表的改装", id: 27847 },
											{ title: "电流表的改装", id: 27848 },
											{ title: "欧姆表原理", id: 27864 },
										],
									},
									{
										title: "电磁场与电磁波",
										children: [{ title: "欧姆表的使用", id: 27865 }],
									},
									{
										title: "无线电波的发射和接收",
										children: [
											{ title: "多用电表", id: 27866 },
											{ title: "电功率", id: 27850 },
											{ title: "焦耳定律", id: 27851 },
										],
									},
									{
										title: "电磁波谱",
										children: [
											{ title: "闭合电路欧姆定律", id: 28147 },
											{ title: "闭合电路中的路端电压", id: 28148 },
											{ title: "闭合电路中的功率和效率", id: 28288 },
										],
									},
								],
							},
							{
								title: "传感器",
								children: [
									{
										title: "认识传感器",
										children: [{ title: "闭合电路中的U－I图象问题", id: 28289 }],
									},
									{
										title: "常见传感器的工作原理...",
										children: [
											{ title: "直流电路动态分析", id: 28290 },
											{ title: "含电容的直流电路分析", id: 28291 },
											{ title: "测电源的电动势和内阻的实验方法", id: 28293 },
											{ title: "测电源的电动势和内阻的其它方法", id: 28295 },
										],
									},
									{
										title: "利用传感器制作简单的...",
										children: [{ title: "测电源的电动势和内阻的误差分析", id: 28294 }],
									},
								],
							},
						],
					},
					{
						title: "选择性必修3",
						children: [
							{
								title: "分子动理论",
								children: [
									{
										title: "分子动理论的基本内容",
										children: [
											{ title: "能量守恒定律", id: 27596 },
											{ title: "能量的耗散", id: 27597 },
											{ title: "磁现象和电流的磁效应", id: 27873 },
										],
									},
									{
										title: "实验：用油膜法估测油...",
										children: [{ title: "磁感线", id: 26612 }],
									},
									{
										title: "分子运动速率分布规律",
										children: [{ title: "安培分子电流假说", id: 27877 }],
									},
									{
										title: "分子动能和分子势能",
										children: [
											{ title: "三种常见的电流磁场", id: 27878 },
											{ title: "安培定则", id: 27876 },
										],
									},
								],
							},
							{
								title: "气体、固体和液体",
								children: [
									{
										title: "温度和温标",
										children: [
											{ title: "地磁场", id: 27880 },
											{ title: "磁感应强度的定义", id: 27874 },
											{ title: "磁通量", id: 27881 },
										],
									},
									{
										title: "气体的等温变化",
										children: [
											{ title: "磁通量的正负", id: 27882 },
											{ title: "划时代的发现", id: 27915 },
											{ title: "切割磁感线与感应电流的产生", id: 27916 },
										],
									},
									{
										title: "气体的等压变化和等容...",
										children: [
											{ title: "磁通量变化与感应电流的产生", id: 27917 },
											{ title: "切割磁感线和磁通量变化", id: 27918 },
											{ title: "麦克斯韦电磁场理论", id: 28077 },
											{ title: "振荡电路", id: 28079 },
										],
									},
									{
										title: "固体",
										children: [
											{ title: "有效发射电磁波的条件", id: 28082 },
											{ title: "电磁波谱的介绍", id: 28100 },
										],
									},
									{
										title: "液体",
										children: [
											{ title: "无线电波", id: 28101 },
											{ title: "能量子", id: 28105 },
											{ title: "曲线运动", id: 27474 },
											{ title: "物体做曲线运动的条件", id: 27475 },
											{ title: "曲线运动中力对速度的改变", id: 27476 },
										],
									},
								],
							},
							{
								title: "热力学定律",
								children: [
									{
										title: "功、热和内能的改变",
										children: [
											{ title: "焦耳实验", id: 28022 },
											{ title: "热量和热功当量", id: 28023 },
										],
									},
									{
										title: "热力学第一定律",
										children: [
											{ title: "热力学第一定律", id: 28024 },
											{ title: "用热力学第一定律解释生活中的现象", id: 28025 },
											{ title: "气体做功与热传递的微观解释", id: 28026 },
										],
									},
									{
										title: "能量守恒定律",
										children: [{ title: "能量守恒和永动机", id: 28027 }],
									},
									{
										title: "热力学第二定律",
										children: [
											{ title: "热力学第二定律的克劳修斯表述", id: 28028 },
											{ title: "热力学第二定律的开尔文表述", id: 28030 },
											{ title: "卡诺定理", id: 28029 },
											{ title: "宏观态和微观态", id: 28031 },
											{ title: "气体扩散的微观解释", id: 28032 },
											{ title: "熵", id: 28033 },
										],
									},
								],
							},
							{
								title: "原子结构和波粒二象性",
								children: [
									{
										title: "普朗克黑体辐射理论",
										children: [
											{ title: "黑体", id: 28103 },
											{ title: "黑体辐射", id: 28104 },
										],
									},
									{
										title: "光电效应",
										children: [
											{ title: "光电效应", id: 28106 },
											{ title: "光电效应的产生条件", id: 28107 },
											{ title: "最大初动能", id: 28108 },
											{ title: "饱和光电流", id: 28109 },
										],
									},
									{
										title: "原子的核式结构模型",
										children: [
											{ title: "汤姆孙模型", id: 28123 },
											{ title: "α粒子散射实验", id: 28124 },
											{ title: "原子核式结构模型", id: 28125 },
											{ title: "原子的尺度", id: 28126 },
										],
									},
									{
										title: "氢原子光谱和玻尔的原...",
										children: [
											{ title: "光谱的分类", id: 28174 },
											{ title: "氢原子光谱的实验规律", id: 28175 },
											{ title: "经典理论的困难", id: 28176 },
											{ title: "玻尔的假设", id: 28127 },
											{ title: "玻尔理论对氢光谱的解释", id: 28128 },
											{ title: "能级跃迁与电离", id: 28130 },
											{ title: "原子激发的两种方式与能量变化", id: 28131 },
										],
									},
									{
										title: "粒子的波动性和量子力...",
										children: [
											{ title: "粒子的波动性", id: 28118 },
											{ title: "光的波粒二象性", id: 28117 },
											{ title: "概率波", id: 28119 },
											{ title: "不确定关系", id: 28120 },
										],
									},
								],
							},
							{
								title: "原子核",
								children: [
									{
										title: "原子核的组成",
										children: [
											{ title: "天然放射性现象", id: 28362 },
											{ title: "三种射线", id: 28363 },
											{ title: "原子核的组成", id: 28364 },
										],
									},
									{
										title: "放射性元素的衰变",
										children: [
											{ title: "放射性元素的衰变", id: 28365 },
											{ title: "半衰期", id: 28367 },
											{ title: "衰变中的动量守恒", id: 28366 },
										],
									},
									{
										title: "核力与结合能",
										children: [
											{ title: "原子核中质子与中子的比例", id: 28177 },
											{ title: "结合能", id: 28178 },
											{ title: "质量亏损", id: 28179 },
										],
									},
									{
										title: "核裂变与核聚变",
										children: [
											{ title: "核裂变", id: 28180 },
											{ title: "链式反应", id: 28181 },
											{ title: "核反应堆", id: 28182 },
											{ title: "核聚变", id: 28183 },
											{ title: "质能方程的应用", id: 28184 },
										],
									},
									{
										title: "“基本”粒子",
										children: [
											{ title: "基本粒子", id: 28185 },
											{ title: "夸克模型", id: 28186 },
										],
									},
								],
							},
						],
					},
				],
				[
					{
						title: "必修1",
						children: [
							{
								title: "物质及其变化",
								children: [
									{
										title: "物质的分类",
										children: [
											{ title: "物质的分类", id: 25954 },
											{ title: "几种分散系", id: 25958 },
										],
									},
									{
										title: "物质的转化",
										children: [{ title: "物质的转化", id: 240574 }],
									},
									{
										title: "电解质的电离",
										children: [
											{ title: "电解质和电离", id: 25959 },
											{ title: "强电解质和弱电解质", id: 25960 },
										],
									},
									{
										title: "离子反应",
										children: [
											{ title: "离子反应", id: 25961 },
											{ title: "离子方程式的概念和书写", id: 25963 },
											{ title: "离子反应的发生条件", id: 25964 },
										],
									},
									{
										title: "氧化还原反应",
										children: [
											{ title: "氧化还原反应", id: 25968 },
											{ title: "氧化还原反应中电子转移的表示方法", id: 25973 },
											{ title: "氧化还原反应的相关概念", id: 25969 },
											{ title: "氧化还原反应方程式配平的特殊方法", id: 25976 },
											{ title: "氧化还原反应方程式配平的一般方法", id: 25975 },
											{ title: "氧化还原反应的应用", id: 25978 },
										],
									},
									{
										title: "氧化剂和还原剂",
										children: [
											{ title: "氧化剂和还原剂的判断", id: 25972 },
											{ title: "氧化性与还原性的强弱判断", id: 25977 },
											{ title: "归中反应和歧化反应", id: 25974 },
										],
									},
								],
							},
							{
								title: "海水中的重要元素——钠和氯",
								children: [
									{
										title: "活泼的金属单质——钠",
										children: [
											{ title: "钠", id: 25980 },
											{ title: "钠和水的反应", id: 25982 },
										],
									},
									{
										title: "钠的几种化合物",
										children: [
											{ title: "碳酸钠和碳酸氢钠的鉴别", id: 25990 },
											{ title: "过氧化钠与水的反应", id: 25985 },
											{ title: "金属及其化合物复习1—钠及其化合物", id: 29013 },
											{ title: "钠与铝的氧化物", id: 25981 },
											{ title: "碳酸钠和碳酸氢钠的区别和联系", id: 25989 },
											{
												title: "过氧化钠的漂白性及其与二氧化碳的反应",
												id: 25986,
											},
											{ title: "氧化钠和过氧化钠的比较", id: 25987 },
										],
									},
									{
										title: "氯气",
										children: [
											{ title: "氯水性质的探究", id: 26017 },
											{ title: "氯气与金属和非金属单质的反应", id: 26012 },
											{ title: "氯气在生活中的应用", id: 26433 },
										],
									},
									{
										title: "氯气与碱反应及氯离子...",
										children: [{ title: "氯气的制备", id: 26018 }],
									},
									{
										title: "物质的量 摩尔质量",
										children: [{ title: "物质的量的概念", id: 240602 }],
									},
									{
										title: "气体摩尔体积",
										children: [
											{ title: "气体摩尔体积的概念", id: 25945 },
											{
												title: "气体摩尔体积的应用1——用于化学方程式的计算",
												id: 28194,
											},
											{
												title: "气体摩尔体积的应用2——与物质的量、质量、微粒数的换算",
												id: 28195,
											},
										],
									},
									{
										title: "一定物质的量浓度的溶...",
										children: [
											{ title: "物质的量浓度在计算中的应用", id: 28201 },
											{ title: "一定物质的量浓度的溶液配制", id: 25951 },
											{ title: "一定物质的量浓度溶液配制的误差分析", id: 28199 },
											{ title: "物质的量浓度的概念", id: 25946 },
										],
									},
								],
							},
							{
								title: "铁 金属材料",
								children: [
									{
										title: "铁及其化合物",
										children: [
											{ title: "铁及其常见化合物", id: 26001 },
											{ title: "氢氧化亚铁的制备", id: 26002 },
											{ title: "金属及其化合物复习3—铁及其化合物", id: 29015 },
											{ title: "铁离子和亚铁离子的鉴别及铁三角", id: 26003 },
											{ title: "铁和水蒸气的反应", id: 28203 },
										],
									},
									{
										title: "常见的合金及应用",
										children: [{ title: "合金", id: 240615 }],
									},
									{
										title: "物质的量在化学方程式...",
										children: [
											{
												title: "物质的量和摩尔质量的应用2——用于化学方程式的计算",
												id: 28191,
											},
										],
									},
								],
							},
							{
								title: "物质结构 元素周期律",
								children: [
									{
										title: "原子结构",
										children: [
											{ title: "相对原子质量和核外电子排布规则", id: 26958 },
											{ title: "原子组成与原子符号", id: 26955 },
										],
									},
									{
										title: "元素周期表",
										children: [
											{ title: "原子结构与元素周期表的关系", id: 26960 },
											{ title: "元素周期表的结构", id: 26871 },
										],
									},
									{
										title: "核素",
										children: [{ title: "元素、核素与同位素", id: 26956 }],
									},
									{
										title: "原子结构与元素的性质",
										children: [{ title: "原子结构的综合应用", id: 27030 }],
									},
									{
										title: "元素性质的周期性变化...",
										children: [
											{ title: "元素周期律", id: 26058 },
											{ title: "同周期元素金属性和非金属性的递变", id: 26961 },
											{ title: "同周期同主族元素性质递变规律总结", id: 26963 },
											{
												title: "元素的金属性或非金属性强弱的判断依据",
												id: 26970,
											},
											{ title: "同主族元素金属性和非金属性的递变", id: 26962 },
										],
									},
									{
										title: "元素周期表和元素周期...",
										children: [
											{ title: "元素周期表的应用", id: 26971 },
											{ title: "元素周期律的综合应用1——短周期元素", id: 27026 },
											{ title: "元素周期律的综合应用2——陌生元素", id: 27027 },
											{ title: "10电子微粒和18电子微粒", id: 27025 },
											{ title: "微粒半径大小比较", id: 26972 },
										],
									},
									{
										title: "离子键",
										children: [
											{ title: "离子键", id: 26063 },
											{ title: "电子式的概念", id: 26067 },
											{ title: "复杂的电子式", id: 26068 },
										],
									},
									{
										title: "共价键",
										children: [
											{ title: "用电子式表示物质的形成", id: 27498 },
											{ title: "氢键", id: 27514 },
											{ title: "分子间作用力", id: 26418 },
											{ title: "离子化合物和共价化合物的比较", id: 26065 },
											{ title: "共价键", id: 26064 },
											{ title: "共价化合物的电子式和结构式", id: 26069 },
											{ title: "化学键和范德华力与物质形成的关系", id: 26857 },
											{ title: "离子键和共价键的概念辨析", id: 26417 },
											{ title: "化学键的类型和物质的电子式", id: 26419 },
										],
									},
								],
							},
						],
					},
					{
						title: "必修2",
						children: [
							{
								title: "化工生产中的重要非金属元素",
								children: [
									{
										title: "硫和二氧化硫",
										children: [
											{ title: "硫与硫化氢", id: 26021 },
											{ title: "二氧化硫的性质和制取", id: 26022 },
											{ title: "二氧化硫的漂白性及其应用", id: 26024 },
										],
									},
									{
										title: "硫酸和硫酸根离子的检...",
										children: [
											{ title: "浓硫酸的综合实验题", id: 29023 },
											{ title: "稀硫酸的酸性和浓硫酸的吸水性", id: 26038 },
											{ title: "浓硫酸的脱水性和强氧化性", id: 26039 },
										],
									},
									{
										title: "不同价态含硫物质的转...",
										children: [{ title: "二氧化硫的综合实验题", id: 29020 }],
									},
									{
										title: "氮气与氮氧化物",
										children: [
											{ title: "氮气与氮的固定", id: 240652 },
											{ title: "一氧化氮和二氧化氮", id: 26029 },
											{
												title: "一氧化氮或二氧化氮与氧气混合溶于水的问题",
												id: 28209,
											},
											{ title: "氮氧化物的实验室制法", id: 28207 },
											{ title: "一氧化氮和二氧化氮混合溶于水的问题", id: 28208 },
										],
									},
									{
										title: "氨和铵盐",
										children: [
											{ title: "氨气的还原性", id: 26034 },
											{ title: "氨气的制备和铵根离子的检验", id: 26036 },
											{ title: "氨气和氨水", id: 26032 },
											{ title: "氨气的碱性及铵盐的热不稳定性", id: 26033 },
										],
									},
									{
										title: "硝酸 酸雨及防治",
										children: [
											{ title: "酸雨的成因和防治", id: 26026 },
											{ title: "硝酸的强氧化性", id: 26042 },
											{ title: "硝酸的性质和工业制法", id: 26041 },
										],
									},
									{
										title: "无机非金属材料",
										children: [
											{ title: "硅酸和硅酸盐", id: 26009 },
											{ title: "硅和二氧化硅", id: 26007 },
										],
									},
								],
							},
							{
								title: "化学反应与能量",
								children: [
									{
										title: "化学反应与热能",
										children: [
											{ title: "化学能与热能的相互转化", id: 26070 },
											{ title: "化学键与反应中的能量关系", id: 26072 },
											{ title: "从化学键推导化学反应的热效应", id: 26071 },
											{ title: "与化学反应热效应相关的探究性实验题", id: 26420 },
										],
									},
									{
										title: "化学反应与电能",
										children: [{ title: "发展中的化学电源", id: 26079 }],
									},
									{
										title: "原电池原理应用 化...",
										children: [
											{
												title: "根据氧化还原反应的原理书写原电池的电极反应",
												id: 27432,
											},
											{ title: "原电池的工作原理", id: 26075 },
											{ title: "原电池的形成条件", id: 26077 },
											{ title: "如何设计原电池", id: 27374 },
											{ title: "根据原电池的原理分析金属活动性", id: 26080 },
										],
									},
									{
										title: "化学反应速率",
										children: [
											{ title: "化学反应速率", id: 26081 },
											{ title: "化学反应速率的影响因素", id: 26084 },
											{ title: "化学反应速率的比较", id: 26089 },
											{
												title: "多个条件比较反应速率的问题及转化率的计算",
												id: 27447,
											},
										],
									},
									{
										title: "化学反应的限度 化学...",
										children: [
											{ title: "化学平衡状态的判断方法", id: 26858 },
											{ title: "化学平衡状态的判断", id: 26087 },
											{ title: "有关反应限度的计算方法", id: 26088 },
											{ title: "化学反应限度", id: 26085 },
										],
									},
								],
							},
							{
								title: "有机化合物",
								children: [
									{
										title: "碳原子的成键特点 烷...",
										children: [{ title: "有机化合物中碳原子的成键特点", id: 240684 }],
									},
									{
										title: "烷烃的性质",
										children: [
											{ title: "同系物", id: 26094 },
											{ title: "同分异构体", id: 26095 },
											{ title: "烷烃的结构特点与性质", id: 26093 },
											{ title: "甲烷的化学性质", id: 26092 },
											{ title: "甲烷的存在与结构", id: 26090 },
											{ title: "烷烃的命名", id: 26096 },
											{ title: "综合计算", id: 26854 },
											{ title: "确定有机物分子式的一般方法", id: 26097 },
										],
									},
									{
										title: "乙烯",
										children: [
											{ title: "乙烯的来源和结构", id: 26098 },
											{ title: "乙烯的氧化反应", id: 26099 },
											{ title: "乙烯的加成反应", id: 27372 },
											{ title: "乙烯的综合应用", id: 27499 },
										],
									},
									{
										title: "烃 有机高分子材料",
										children: [
											{ title: "乙烯的来源和结构", id: 26098 },
											{ title: "乙烯的氧化反应", id: 26099 },
											{ title: "乙烯的加成反应", id: 27372 },
											{ title: "乙烯的综合应用", id: 27499 },
											{ title: "苯的综合应用", id: 26108 },
											{ title: "苯的结构和物理性质", id: 26103 },
											{ title: "苯的化学性质", id: 26104 },
											{ title: "有机高分子材料", id: 240704 },
										],
									},
									{
										title: "乙醇",
										children: [
											{ title: "乙醇的结构和物理性质", id: 26110 },
											{ title: "乙醇的置换反应", id: 26111 },
											{ title: "乙醇的氧化反应", id: 27375 },
											{ title: "乙醇的综合应用", id: 27500 },
										],
									},
									{
										title: "乙酸",
										children: [
											{ title: "乙酸的结构和性质", id: 26113 },
											{ title: "乙酸乙酯的制备", id: 26116 },
											{ title: "乙酸的综合应用", id: 26117 },
										],
									},
									{
										title: "糖类 蛋白质",
										children: [
											{ title: "糖类和单糖", id: 27020 },
											{ title: "双糖和多糖", id: 27021 },
											{ title: "蛋白质", id: 27024 },
											{ title: "氨基酸", id: 27023 },
										],
									},
									{
										title: "油脂 奶油",
										children: [{ title: "油脂", id: 27022 }],
									},
								],
							},
							{
								title: "化学与可持续发展",
								children: [
									{
										title: "金属矿物、海水资源的...",
										children: [
											{ title: "金属冶炼的一般方法", id: 26975 },
											{ title: "金属冶炼的特殊方法", id: 26976 },
											{ title: "海水中水资源的利用", id: 26977 },
											{ title: "海水中金属元素的提取", id: 26978 },
											{ title: "海水中非金属元素的提取", id: 26981 },
										],
									},
									{
										title: "煤、石油和天然气的综...",
										children: [
											{ title: "煤和天然气的综合利用", id: 26982 },
											{ title: "石油的综合利用", id: 26983 },
										],
									},
									{
										title: "化肥、农药的合理施用...",
										children: [{ title: "化学品的合理使用", id: 240724 }],
									},
									{
										title: "安全使用食品添加剂",
										children: [{ title: "安全使用食品添加剂", id: 240725 }],
									},
									{
										title: "环境保护与绿色化学",
										children: [{ title: "环境保护与绿色化学", id: 26135 }],
									},
								],
							},
						],
					},
					{
						title: "选择性必修1",
						children: [
							{
								title: "化学反应的热效应",
								children: [
									{
										title: "反应热",
										children: [
											{ title: "反应热与化学键的关系", id: 26171 },
											{ title: "中和热概念", id: 26177 },
											{ title: "中和热的测定", id: 26178 },
											{ title: "热化学方程式的概念和书写步骤", id: 26174 },
											{ title: "热化学方程式的正误判断", id: 26175 },
											{ title: "根据所给信息书写热化学方程式", id: 26176 },
											{ title: "反应热和物质能量的关系", id: 26172 },
											{ title: "焓变反应热习题课1——根据键能求反应热", id: 28210 },
											{ title: "焓变反应热习题课2——根据反应热求键能", id: 28211 },
											{ title: "中和热的实验测定注意事项", id: 28212 },
											{ title: "燃烧热", id: 26179 },
											{ title: "反应热、中和热、燃烧热对比", id: 28213 },
										],
									},
									{
										title: "反应热的计算",
										children: [
											{ title: "盖斯定律内容与证明", id: 26181 },
											{ title: "盖斯定律的应用1——计算反应热", id: 26182 },
											{ title: "盖斯定律的应用2——计算键能", id: 26183 },
											{ title: "盖斯定律的应用3——判断反应热的大小", id: 26184 },
											{
												title: "根据反应物的性质或盖斯定律比较△H的大小",
												id: 28214,
											},
											{
												title: "根据反应进行的程度或反应规律比较△H的大小",
												id: 28215,
											},
										],
									},
								],
							},
							{
								title: "化学反应速率与化学平衡",
								children: [
									{
										title: "化学反应速率",
										children: [
											{ title: "化学反应速率的概念和计算方法", id: 26186 },
											{ title: "化学反应速率的比较方法", id: 28217 },
											{ title: "化学反应速率的测量方法", id: 26187 },
											{ title: "影响化学反应速率的因素1—浓度和温度", id: 26191 },
											{ title: "影响化学反应速率的因素3—催化剂", id: 26194 },
											{ title: "化学反应速率的微观解释", id: 26190 },
											{ title: "总结外界条件对反应速率的影响及本质", id: 26195 },
											{ title: "影响化学反应速率的因素2—压强", id: 26192 },
											{ title: "控制变量在速率影响因素中的应用", id: 26196 },
										],
									},
									{
										title: "化学平衡",
										children: [
											{ title: "可逆反应与不可逆反应", id: 26197 },
											{ title: "化学平衡状态的概念和特征", id: 26198 },
											{ title: "化学平衡状态判断的标志", id: 26199 },
											{ title: "化学平衡常数的概念和计算方法", id: 26207 },
											{ title: "计算化学平衡常数的注意事项", id: 28219 },
											{ title: "平衡常数的应用1——计算平衡状态的组成", id: 28221 },
											{ title: "平衡常数的应用2——判断反应进行方向", id: 28220 },
											{ title: "影响化学平衡的条件3—温度", id: 26203 },
											{ title: "勒夏特列原理", id: 26205 },
											{ title: "影响化学平衡的条件1—浓度", id: 26201 },
											{ title: "影响化学平衡的条件2—压强（1）", id: 26202 },
											{ title: "影响化学平衡的条件2—压强（2）", id: 28222 },
										],
									},
									{
										title: "化学反应的方向 化学...",
										children: [
											{ title: "化学反应进行的方向", id: 26217 },
											{
												title: "勒夏特列原理在工业生产的应用—合成氨条件的选择",
												id: 28224,
											},
										],
									},
								],
							},
							{
								title: "水溶液中的离子反应与平衡",
								children: [
									{
										title: "电离平衡",
										children: [
											{ title: "强弱电解质对比", id: 26218 },
											{ title: "电离方程式的书写", id: 26224 },
											{ title: "弱电解质的电离平衡及其影响因素", id: 26220 },
											{ title: "弱电解质电离平衡的实例分析", id: 26221 },
											{ title: "电离常数", id: 26225 },
											{ title: "一元强酸与一元弱酸的比较", id: 28240 },
											{ title: "电离常数的计算", id: 28241 },
										],
									},
									{
										title: "水的电离和溶液的pH",
										children: [
											{ title: "水的电离与水的离子积", id: 26227 },
											{ title: "水的电离平衡移动", id: 28244 },
											{ title: "溶液的酸碱性", id: 26228 },
											{ title: "pH", id: 26229 },
											{ title: "酸碱中和滴定", id: 26233 },
											{ title: "酸碱中和滴定操作步骤", id: 26234 },
											{ title: "酸碱中和滴定的滴定曲线", id: 28254 },
											{ title: "中和滴定的误差分析1——洗涤和取液过程", id: 28255 },
											{ title: "中和滴定的误差分析2——滴定和读数过程", id: 28256 },
											{ title: "氧化还原滴定", id: 28257 },
											{ title: "由溶液pH推测水的电离情况", id: 28245 },
											{ title: "由水的电离情况推测溶液pH", id: 28246 },
											{ title: "酸溶液稀释后的pH变化", id: 26230 },
											{ title: "碱溶液稀释后的pH变化", id: 28249 },
											{ title: "酸溶液的pH和浓度之间的换算", id: 28247 },
											{ title: "碱溶液的pH和浓度之间的换算", id: 28248 },
											{
												title: "强酸与强酸或强碱与强碱混合后的pH计算",
												id: 28250,
											},
											{ title: "强酸与强碱混合后的pH计算", id: 28251 },
											{
												title: "已知强酸和强碱的pH之和，判断等体积混合后溶液的pH",
												id: 28252,
											},
											{ title: "pH的计算的例题分析", id: 28253 },
										],
									},
									{
										title: "盐类的水解",
										children: [
											{ title: "盐的水解实质", id: 26236 },
											{ title: "盐的水解规律", id: 26237 },
											{ title: "盐的水解特点", id: 28258 },
											{ title: "多元弱酸盐或弱碱盐的水解", id: 28259 },
											{ title: "影响盐类水解的主要因素", id: 26238 },
											{ title: "盐类水解反应的应用1——制备物质", id: 28269 },
											{
												title: "盐类水解反应的应用2——分析离子共存和除杂问题",
												id: 28270,
											},
											{
												title: "盐类水解反应的应用3——在生活和生产中的应用",
												id: 28271,
											},
											{ title: "双水解（互促水解）", id: 26239 },
											{ title: "水解平衡移动的应用", id: 28261 },
										],
									},
									{
										title: "沉淀溶解平衡",
										children: [
											{ title: "溶解平衡移动的应用1——沉淀的溶解", id: 28272 },
											{ title: "溶解平衡移动的应用2——沉淀的转化", id: 28273 },
											{
												title: "溶解平衡移动的应用3——沉淀的转化后除去",
												id: 28274,
											},
											{ title: "自然界和生活中的沉淀溶解平衡", id: 26257 },
											{ title: "沉淀溶解平衡的概念和特征", id: 26251 },
											{ title: "溶度积常数和溶解平衡移动", id: 26253 },
										],
									},
								],
							},
							{
								title: "化学反应与电能",
								children: [
									{
										title: "原电池",
										children: [
											{ title: "双液电池工作原理", id: 26260 },
											{ title: "原电池原理的应用", id: 26261 },
											{ title: "原电池的工作原理", id: 26075 },
											{ title: "一次电池——锌锰电池", id: 28283 },
											{ title: "二次电池——铅蓄电池", id: 28284 },
											{ title: "二次电池——锂离子电池", id: 26267 },
											{ title: "化学电源—氢氧燃料电池", id: 26268 },
											{ title: "化学电源—甲烷燃料电池", id: 28285 },
											{ title: "化学电源—甲醇燃料电池", id: 28286 },
											{ title: "化学电源—熔融盐电池", id: 28287 },
										],
									},
									{
										title: "电解池",
										children: [
											{ title: "电解原理", id: 26269 },
											{ title: "电解反应类型1——水或溶质参与反应", id: 28296 },
											{
												title: "电解反应类型2——离子或电极材料参与反应",
												id: 28297,
											},
											{ title: "电解反应实例分析1——只有水参与反应", id: 28298 },
											{
												title: "电解反应实例分析2——只有电解质参与反应",
												id: 28299,
											},
											{
												title: "电解反应实例分析3——电解质和水都参与反应",
												id: 28300,
											},
											{ title: "电解原理的应用1——氯碱工业", id: 26275 },
											{ title: "电解原理的应用2——电镀和电冶金", id: 26278 },
											{ title: "电解原理的应用3——精炼铜", id: 26277 },
											{ title: "电解电解质溶液的分析方法与规律", id: 28303 },
											{ title: "氯碱工业原理的应用", id: 28302 },
										],
									},
									{
										title: "金属的腐蚀与防护",
										children: [
											{ title: "金属的电化学腐蚀", id: 26281 },
											{ title: "析氢腐蚀和吸氧腐蚀的比较", id: 26282 },
											{ title: "金属的电化学防护", id: 26284 },
										],
									},
								],
							},
						],
					},
					{
						title: "选择性必修2",
						children: [
							{
								title: "原子结构与性质",
								children: [
									{
										title: "原子结构",
										children: [
											{ title: "能层与能级", id: 240835 },
											{ title: "基态与激发态原子光谱", id: 240836 },
											{ title: "构造原理与电子排布式", id: 240837 },
											{ title: "电子云与原子轨道", id: 240838 },
											{ title: "泡利原理、洪特规则、能量最低原理", id: 240839 },
										],
									},
									{
										title: "原子结构与元素的性质",
										children: [
											{ title: "原子结构与元素周期表", id: 240840 },
											{ title: "元素周期律", id: 26058 },
										],
									},
								],
							},
							{
								title: "分子结构与性质",
								children: [
									{
										title: "共价键",
										children: [
											{ title: "共价键", id: 26064 },
											{ title: "键参数——键能、键长与键角", id: 240843 },
										],
									},
									{
										title: "分子的空间结构",
										children: [
											{ title: "分子结构的测定", id: 240844 },
											{ title: "多样的分子空间结构", id: 240845 },
											{ title: "价层电子对互斥模型", id: 240846 },
											{ title: "杂化轨道理论简介", id: 240847 },
										],
									},
									{
										title: "分子结构与物质的性质",
										children: [
											{ title: "共价键的极性", id: 240848 },
											{ title: "分子间的作用力", id: 240849 },
											{ title: "分子的手性", id: 240850 },
										],
									},
								],
							},
							{
								title: "晶体结构与性质",
								children: [
									{
										title: "物质的聚集状态与晶体...",
										children: [
											{ title: "物质的聚集状态", id: 240851 },
											{ title: "晶体与非晶体", id: 240852 },
											{ title: "晶胞", id: 240853 },
											{ title: "晶体结构的测定", id: 240854 },
										],
									},
									{
										title: "分子晶体与共价晶体",
										children: [
											{ title: "分子晶体", id: 240855 },
											{ title: "共价晶体", id: 240856 },
										],
									},
									{
										title: "金属晶体与离子晶体",
										children: [
											{ title: "金属键与金属晶体", id: 240857 },
											{ title: "离子晶体", id: 240858 },
											{ title: "过渡晶体与混合型晶体", id: 240859 },
										],
									},
									{
										title: "配合物与超分子",
										children: [
											{ title: "配合物", id: 240860 },
											{ title: "超分子", id: 240861 },
										],
									},
								],
							},
						],
					},
					{
						title: "选择性必修3",
						children: [
							{
								title: "有机化合物的结构特点与研究方法",
								children: [
									{
										title: "有机化合物的结构特点",
										children: [
											{ title: "烃及其分类", id: 26287 },
											{ title: "烃的衍生物及其分类", id: 26288 },
											{ title: "有机化合物中的共价键", id: 240864 },
											{ title: "同分异构体", id: 26095 },
											{ title: "烃复习2-同分异构体的书写方法1", id: 27380 },
											{ title: "烃复习2-同分异构体的书写方法2", id: 27381 },
										],
									},
									{
										title: "研究有机化合物的一般...",
										children: [
											{ title: "有机物的分离和提纯1-蒸馏", id: 26305 },
											{ title: "有机物的分离和提纯2-重结晶", id: 26306 },
											{ title: "有机物的分离和提纯3-萃取与分液", id: 26307 },
											{ title: "有机物最简式（实验式）的确定", id: 26308 },
											{ title: "相对分子质量的确定—质谱法", id: 26310 },
											{ title: "分子结构的鉴定—红外光谱", id: 26311 },
											{ title: "分子结构的鉴定—核磁共振氢谱", id: 26312 },
											{ title: "研究有机物的一般步骤和方法", id: 26432 },
										],
									},
								],
							},
							{
								title: "烃",
								children: [
									{
										title: "烷烃",
										children: [
											{ title: "脂肪烃1-烷烃", id: 26314 },
											{ title: "烷烃的命名", id: 26096 },
											{ title: "烷烃的结构特点与性质", id: 26093 },
										],
									},
									{
										title: "烯烃 炔烃",
										children: [
											{ title: "脂肪烃2-烯烃的组成和同分异构", id: 26315 },
											{ title: "脂肪烃4-烯烃的加聚反应", id: 26317 },
											{ title: "乙炔的结构和性质", id: 26319 },
											{ title: "乙炔的实验室制法", id: 26320 },
											{ title: "炔烃的结构与性质", id: 26325 },
											{ title: "烯烃炔烃的命名", id: 240879 },
											{ title: "脂肪烃3-烯烃的性质", id: 26316 },
											{ title: "乙烷、乙烯、乙炔的对比", id: 26326 },
										],
									},
									{
										title: "芳香烃",
										children: [
											{ title: "苯的取代反应", id: 26328 },
											{ title: "苯的同系物的组成、结构和同分异构", id: 26329 },
											{ title: "苯的结构和物理性质", id: 26103 },
											{ title: "苯的同系物的命名", id: 26304 },
											{ title: "苯的氧化反应和加成反应", id: 27562 },
											{ title: "苯的同系物的性质", id: 26332 },
										],
									},
								],
							},
							{
								title: "烃的衍生物",
								children: [
									{
										title: "卤代烃",
										children: [
											{ title: "溴乙烷的结构和取代反应", id: 26336 },
											{ title: "溴乙烷的消去反应", id: 26337 },
											{ title: "检验溴乙烷消去反应中的溴离子", id: 26338 },
											{ title: "卤代烃", id: 26342 },
											{ title: "卤代烃的综合应用", id: 26343 },
										],
									},
									{
										title: "醇 酚",
										children: [
											{ title: "醇的结构特征和命名", id: 26346 },
											{ title: "重要醇的物理性质及简单应用", id: 26347 },
											{ title: "醇类的化学性质1——置换反应和取代反应", id: 26349 },
											{ title: "醇类的化学性质2——燃烧和催化氧化", id: 26350 },
											{ title: "醇类的化学性质3——与强氧化剂的反应", id: 26351 },
											{ title: "醇类的化学性质4——消去反应", id: 27389 },
											{ title: "醇类的化学性质5——结构与性质的关系", id: 27390 },
											{ title: "苯酚1——苯酚及其同系物", id: 26352 },
											{ title: "苯酚2——物理性质和酸性", id: 26353 },
											{ title: "苯酚3——苯酚的弱酸性", id: 26354 },
											{ title: "苯酚4——取代反应和显色反应", id: 26355 },
											{ title: "苯酚5——加成反应和缩聚反应", id: 26356 },
											{ title: "醇类的同分异构体", id: 26348 },
											{ title: "分离苯和苯酚", id: 27392 },
											{ title: "处理含有苯酚的废水", id: 27393 },
										],
									},
									{
										title: "醛 酮",
										children: [
											{ title: "醛的结构特征和同分异构体", id: 26357 },
											{ title: "乙醛的性质1——加成反应和氧化反应", id: 26358 },
											{ title: "乙醛的性质2——银镜反应", id: 26359 },
											{ title: "乙醛的性质3——与新制氢氧化铜反应", id: 26360 },
											{ title: "甲醛的性质", id: 26364 },
											{ title: "醛类的化学性质", id: 26365 },
										],
									},
									{
										title: "羧酸 羧酸衍生物",
										children: [
											{ title: "羧酸的结构特征", id: 26367 },
											{ title: "常见羧酸的物理性质和羧酸的通性", id: 26368 },
											{ title: "羧酸的酯化反应", id: 26369 },
											{ title: "验证乙酸、碳酸和苯酚的酸性强弱", id: 26370 },
											{ title: "各类酯化反应", id: 26375 },
											{ title: "酯的结构特征和性质", id: 26372 },
											{ title: "酯类的异构", id: 26374 },
											{ title: "各类酯的水解反应", id: 26376 },
										],
									},
									{
										title: "有机合成",
										children: [
											{ title: "有机合成的过程1——官能团的转化", id: 26386 },
											{ title: "有机合成的方法1——正向合成分析法", id: 27431 },
											{ title: "有机合成的方法2——逆向合成分析法", id: 27396 },
											{ title: "有机合成的过程2——卤代烃的应用", id: 26387 },
										],
									},
								],
							},
							{
								title: "生物大分子",
								children: [
									{
										title: "糖类",
										children: [
											{ title: "糖类定义和分类", id: 26395 },
											{ title: "葡萄糖和果糖", id: 26396 },
											{ title: "蔗糖和麦芽糖", id: 26397 },
											{ title: "淀粉和纤维素", id: 26398 },
										],
									},
									{
										title: "蛋白质",
										children: [
											{ title: "氨基酸的结构和性质", id: 26399 },
											{ title: "蛋白质的组成和结构", id: 26400 },
											{
												title: "蛋白质的性质1——两性、盐析、颜色反应等",
												id: 28136,
											},
											{ title: "蛋白质的性质2——变性", id: 28137 },
										],
									},
									{
										title: "核酸",
										children: [{ title: "酶与核酸", id: 26401 }],
									},
								],
							},
							{
								title: "合成高分子",
								children: [
									{
										title: "合成高分子的基本方法",
										children: [
											{ title: "加成聚合反应", id: 26403 },
											{ title: "缩合聚合反应", id: 26404 },
										],
									},
									{
										title: "高分子材料",
										children: [
											{ title: "塑料1—聚乙烯", id: 26405 },
											{ title: "塑料2—酚醛树脂", id: 26406 },
											{ title: "合成纤维和合成橡胶", id: 26407 },
											{ title: "功能高分子材料", id: 26408 },
										],
									},
								],
							},
						],
					},
				],
			],
		};
	},
	//mounted() {
	//	axios.get("https://raw.githubusercontent.com/ybygjylj/datas/master/data.json").then((res) => {
	//		this.lists = res.data;
	//	});
	//},
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
	margin-top: 13.2662835249vh;
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
