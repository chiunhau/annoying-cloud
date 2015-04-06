# annoying-cloud

## 1.取得視訊裝置來源：getUserMedia

使用navigator.getUserMedia()這個web API，讓瀏覽器跳出取得webcam source權限的東東，取得權限後把webcam stream流向<video> element。

具體做法就是這個

```javascript
navigator.getUserMedia (
	{ 
	  video: true,
	  audio: false
	},
	function(localMediaStream){
	  //success callback，即取得webcam stream之後要做的事
	  var video = document.querySelector('video');
	  video.src = window.URL.createObjectURL(localMediaStream);
	  
	  //耶～製造一個自己呼叫自己的遞迴函式，就可以一直取得最新的stream frame囉
	  ...
	}, 
	function(e){
	  //error callback
		console.log(e);
	}
)
```

## 2.然後把接出來的stream丟到<canvas>上顯示

```javascript
function frameHandler(event) {
  //把canvas做水平鏡射，這樣才會跟使用者同邊
  var c = canvas.getContext('2d');
	c.save();
	c.scale(-1,1);
	c.drawImage(video,-width,0,width,height);
	c.restore();
	
	//把此一frame和前一個frame送到detectMotion()裡，做比對運算偵測移動
	...
	//最後呼叫自己
	frameHandler;
}
```

## 3.detectMotion()：定義偵測點，每個點做兩個frame的像素rgb比對
  如果此偵測點的delta r + delta g + delta b 相加超過100，就判定有移動。
  所以每次detectMotion()都會得出一個有哪些偵測點被觸發的map～～～
