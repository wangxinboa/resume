# tween简析

&emsp;[tween](https://github.com/tweenjs/tween.js): 进行js动画操作的一个小工具， 主要是与three.js搭配，这里只是对一些主要的运行原理进行解析，有很多细节方面是直接略过的。

## tween动画的核心原理

&emsp;先简单说一下tween动画的核心原理，本质上就是数据的操作。比如说，在 0s 到 1s 内，我想要一个高度为 0px 物体向下移动 100px，那么 0s 高度为 0px ，1s 高度为 -100px 。只是，如果只设置这两个时间的高度的话，那么 0s 到 0.99...s 的高度就一直会是 0px ，1s 突然变成 -100px ，会显得很突兀，那么就在 0s ～ 1s 之间不断插入对应的位置，即 ( 当前经过的时间 - 0 ) / 1s * -100px 。所以说，动画的本质依旧是离散的，只是每个数据之间的时间间隔在 requestAnimationFrame 函数下大约为 17ms （1s / 60） ，对人眼进行视觉欺骗认为是连贯的动画。

## 使用

&emsp;[演示案例，直接在浏览器打开的html文件](https://officialbusiness.github.io/react_project/resume/tween/tween_demo.html)

&emsp;首先创建一个始终执行的动画函数，在其中调用函数TWEEN.update

```
function animate(time) {
	TWEEN.update()
	requestAnimationFrame(animate)
}
```

&emsp;然后创建实例对象 new TWEEN.Tween，传入操作的对象，链式语法调用相关的函数执行

```
function init() {
	position = {x: 0, y: 0, rotation: 0}
	target = document.getElementById('target')
	tween = new TWEEN.Tween(position)
		.to({x: 200, y: 200, rotation: 360}, 3000)
		.easing(TWEEN.Easing.Linear.None)
		.onUpdate(update)
	tween.start()
}
function update() {
	target.style.left = position.x + 'px'
	target.style.top = position.y + 'px'
	target.style.webkitTransform = 'rotate(' + Math.floor(position.rotation) + 'deg)'
	target.style.MozTransform = 'rotate(' + Math.floor(position.rotation) + 'deg)'
}
```

&emsp;其中,涉及到tween相关的代码有

* TWEEN.Tween: 构造函数，传入一个对象作为动画数值变化的数据，对象一开始的数值会作为起点深拷贝保存下来
* to: 传入两个参数，第一个是表示数据终点的对象，属性格式与表示一开始初始化传入的，表示起点的对象数据一致；第二个参数表示动画进行的时间，单位为毫秒
* easing: 传入TWEEN内置的信息，表示动画的运动速度。比如，是一直匀速、先快后慢、先慢后快还是其他等
* onUpdate: 传入一个函数，每次动画内部执行更新对象数据(就是最初传入的)的时候会同步执行传入的函数
* start: 表示动画开始运动的函数

&emsp;简单的来说,在TWEEN.update执行的前提下，创建一个 new TWEEN.Tween 实例对象，通过操作初始化传入的对象数据实现动画

## 源码简单分析

&emsp;[源码](https://github.com/tweenjs/tween.js/blob/master/dist/tween.esm.js)

&emsp;tween的组成主要有Easing、now、Group、Tween这四个变量

### Easing

&emsp;行数: 4-186

&emsp;对象，表示动画运动的速度曲线，为[tween](https://github.com/officialBusiness/react_project/blob/main/resume/tween/tween%E7%AE%80%E6%9E%90.md#tween)服务，[相关链接](http://tweenjs.github.io/tween.js/examples/03_graphs.html)

### now,now$1

&emsp;行数: 188-218

&emsp;函数，表示获取当前时间的函数。一开始没有赋值，根据不同的环境赋值不同的函数，获取当前时间。为[tween](https://github.com/officialBusiness/react_project/blob/main/resume/tween/tween%E7%AE%80%E6%9E%90.md#tween)服务

### Group

&emsp;行数: 226-273

&emsp;匿名函数自执行，返回内部的Group构造函数。Group代码的理解与Tween有关，可以将它视作一个容器用于存储tween

&emsp;初始化的两个对象就是用于存储tween的

```
function Group() {
	this._tweens = {};
	this._tweensAddedDuringUpdate = {};
}
```

&emsp;在add方法中添加tween，tween的id作为对象的属性

```
Group.prototype.add = function (tween) {
	this._tweens[tween.getId()] = tween;
	this._tweensAddedDuringUpdate[tween.getId()] = tween;
};
```

&emsp;update函数其实就是在遍历执行tween的动画函数

```
Group.prototype.update = function (time, preserve) {
	if (time === void 0) { time = now$1(); }
	if (preserve === void 0) { preserve = false; }
	var tweenIds = Object.keys(this._tweens);
	if (tweenIds.length === 0) {
		return false;
	}
	while (tweenIds.length > 0) {
		this._tweensAddedDuringUpdate = {};
		//*********遍历存储在_tweens中的tween实例对象
		for (var i = 0; i < tweenIds.length; i++) {
			var tween = this._tweens[tweenIds[i]];
			var autoStart = !preserve;
			//*********执行tween.update函数,进行动画更新
			if (tween && tween.update(time, autoStart) === false && !preserve) {
					delete this._tweens[tweenIds[i]];
			}
		}
		tweenIds = Object.keys(this._tweensAddedDuringUpdate);
	}
	return true;
};
```

&emsp;再看369、776行和785行的代码，可以知道，一开始在外界执行的 TWEEN.update 就是不断执行一个Group实例对象（ mainGroup ）的 update 方法

```
//369行的代码
var mainGroup = new Group();
//776行的代码
var TWEEN = mainGroup;
//785行的代码
var update = TWEEN.update.bind(TWEEN);
```

### Tween

&emsp;行数: 379-757

* 匿名函数自执行，返回内部的Tween构造函数。Tween一开始初始化实例传入两个参数，第一个是_object，之前就说过，是表示动画数据的一个对象；第二个是group实例，表示实在那个group中，默认是mainGroup。（381行到383行）

```
function Tween(_object, _group) {
	if (_group === void 0) { _group = mainGroup; }
	this._object = _object;
	this._group = _group;
}
```

* 方法to: 传入终点属性properties和动画执行时间duration，保存在实例中，properties格式需要和最初保存的_object一致（414行到424行）

```
Tween.prototype.to = function (properties, duration) {
	this._valuesEnd = Object.create(properties);
	if (duration !== undefined) {
		this._duration = duration;
	}
	return this;
};
```

* 方法duration: 表示动画延迟执行的时间，单位同样是毫秒（425行到428行）

```
Tween.prototype.duration = function (d) {
	this._duration = d;
	return this;
};
```

* 方法repeat: 传入times参数保存在实例对象中，表示动画重新执行的次数（570到574）

```
Tween.prototype.repeat = function (times) {
	this._initialRepeat = times;
	this._repeat = times;
	return this;
};
```

* 方法yoyo: 传入参数true或者false，保存在实例对象中，表示重复执行的动画是不是像悠悠球一样是 起点—终点—起点，还是 起点-终点、起点—终点（579行到577行）

```
Tween.prototype.yoyo = function (yoyo) {
	this._yoyo = yoyo;
	return this;
};
```

* 方法easing: 传入[Easing](https://github.com/officialBusiness/react_project/blob/main/resume/tween/tween%E7%AE%80%E6%9E%90.md#easing)对象中的属性方法，表示动画的运动速度。比如，是一直匀速、先快后慢、先慢后快还是其他等（583行到586）

```
Tween.prototype.easing = function (easingFunction) {
	this._easingFunction = easingFunction;
	return this;
};
```

* 方法onStart、onUpdate、onRepeat、onComplete、onStop: 都是传入一个函数，分别在动画开始时、动画进行时、动画重复执行的开始、动画结束时、动画停止时执行传入的函数（599行到618行）

```
Tween.prototype.onStart = function (callback) {
	this._onStartCallback = callback;
	return this;
};
Tween.prototype.onUpdate = function (callback) {
	this._onUpdateCallback = callback;
	return this;
};
Tween.prototype.onRepeat = function (callback) {
	this._onRepeatCallback = callback;
	return this;
};
Tween.prototype.onComplete = function (callback) {
	this._onCompleteCallback = callback;
	return this;
};
Tween.prototype.onStop = function (callback) {
	this._onStopCallback = callback;
	return this;
};
```

* 方法start: 让动画开始执行的函数（429行到453行）

```
Tween.prototype.start = function (time) {
	//*********如果执行过start函数了，就直接返回
	if (this._isPlaying) {
		return this;
	}
	//*********将tween存储到group中
	this._group && this._group.add(this);
	this._repeat = this._initialRepeat;
	if (this._reversed) {
		this._reversed = false;
		for (var property in this._valuesStartRepeat) {
			this._swapEndStartRepeatValues(property);
			this._valuesStart[property] = this._valuesStartRepeat[property];
		}
	}
	//*********标记动画已经开始了
	this._isPlaying = true;
	this._isPaused = false;
	this._onStartCallbackFired = false;
	this._isChainStopped = false;
	//*********通过now$1()获取当前时间，标记动画开始的时间
	this._startTime = time !== undefined ? (typeof time === 'string' ? now$1() + parseFloat(time) : time) : now$1();
	//*********加上延迟的时间
	this._startTime += this._delayTime;
	//*********深拷贝this._object到this._valuesStart
	this._setupProperties(this._object, this._valuesStart, this._valuesEnd, this._valuesStartRepeat);
	return this;
};
```

* this._isPlaying: 是否执行过start正在进行的动画，如果正在执行就不在执行了
* 将tween存储到[group，一般默认是mainGroup](https://github.com/officialBusiness/react_project/blob/main/resume/tween/tween%E7%AE%80%E6%9E%90.md#group)中，遍历执行tween.update函数，执行对象的数值修改，进行动画
* 标记动画已经开始
* 一般执行start开始动画是不传入time时间的，是通过now$1()获取当前时间，标记动画开始的时间(一般之外的情况不影响动画的执行和对tween主干的理解，所以略过。~~我也没怎么去仔细研究~~)
* 执行_setupProperties函数，主要是深拷贝作为动画操作的对象数据this._object，作为动画起点this._valuesStart保存起来
* 方法_setupProperties: 简单的来说，就是之前说过的深拷贝，当然还是有其他的作用的，不过略过，不影响主体的理解（454行到510行）
* 方法update: 用于更新动画数据 this._object（624行到702行）

```
//*********由于代码稍微有点多，便于阅读，略去一些可以略去的代码
Tween.prototype.update = function (time) {
  if (time === void 0) { time = now$1(); }
  if (this._isPaused)
      return true;
  var elapsed;
  var endTime = this._startTime + this._duration;
  if (!this._isPlaying) {
    	//如果时间超出了，就停止动画
      if (time > endTime)
          return false;
  }
	//如果时间还没有超出，就停止动画
  if (time < this._startTime) {
      return true;
  }
  //计算已经经过的时间占总的执行之间的比值
  elapsed = (time - this._startTime) / this._duration;
  elapsed = this._duration === 0 || elapsed > 1 ? 1 : elapsed;
  // 根据不同运行方式，获得当前时间动画数据变化的比值
  var value = this._easingFunction(elapsed);
  // 更新动画对象数据
  this._updateProperties(this._object, this._valuesStart, this._valuesEnd, value)
  //系数为1，表示动画已经执行完毕
  if (elapsed === 1) {
		this._isPlaying = false;
		return false;
  }
  return true;
};
```

* 如果没有传入时间time的话，获取当前时间
* elapsed表示 从开始的时间点到目前的时间 占 从开始的时间点到终点的时间点 的比值，最小值为0，最大值为1
* 根据不同运行方式，即[easing](https://github.com/officialBusiness/react_project/blob/main/resume/tween/tween%E7%AE%80%E6%9E%90.md#easing)，获得当前时间动画数据变化的比值
* 更新动画对象数据，如果elapsed系数为1，表示动画已经执行完毕
* 方法_updateProperties: 简单的来说，就是更新作为动画操作的数据对象this._object，让数据是指定的时间点的数据。举个案例如下:

		假设深拷贝的作为起点数据this._valuesStart如下:

```
{
	a: 1,
	{
		b: 2
	}
}
```
		在to函数中，传入的作为终点的数据如下

```
{
	a: 2,
	{
		b: 4
	}
}
```
		如果执行的时间为2s，速度为匀速，那么经过了1s的时候，为作为动画操作的对象数据this._object应当如下:

```
{
	a: 1.5,
	{
		b: 3
	}
}
```

## 总结

&emsp;只是对tween的主干进行了简单的解析。简单的来说，就是group存储执行一个个tween，tween再执行动画，用于变化动画数据对象，从 深拷贝的起点数据对象 随着时间变化到 终点数据对象。当然，还有很多的细节，比如暂停、继续；多个组动画的衔接等等，不过都是基于以上原理进行组装

&emsp;tween的缺陷的话就是不适合特别复杂的场景动画，因为它是以一个个tween实例为单位进行动画操作的，如果在不同的tween中，初始化的动画数据对象有重复的话，那就很容易造成动画的混乱了。比如说tweenA初始化的动画数据对象中包含了对象A和对象B，tweenB初始化的动画数据对象中包含了对象B，两个同时执行的的话，可能就会出问题了
