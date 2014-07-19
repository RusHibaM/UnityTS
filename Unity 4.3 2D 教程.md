#Unity 4.3 2D 教程： 滚动，场景和音效

###Christopher LaPollo on July 14, 2014
###RusHibaM 译

欢迎回到我们的 Unity 4.3 2D 系列教程。

的确，Unity 4.5在不久前已经发布了。但是本系列主要涉及的是Unity的2D特效，而这些特效正是在Unity 4.3版本中才被首次引入的。在4.5版本中，我们修复了一些bug并稍微修改了一些GUI元素。所以，如果你正在使用一个更新版本的引擎你应该时刻留意这一点，并且你应该注意你自己的编辑器与这个网页中的截图之间的细微差别。

通过这个系列教程的第一、二、三部分，你已经大致学会了使用Unity 2D工具所需要的相关知识，包括如何引入和初始化你的精灵。

在这个系列教程的第四部分，我们介绍了Unity 2D的物理引擎并且你还学会了一个处理不同屏幕尺寸和不同屏幕比例的方法。

在这个系列教程最后的部分，也就是第五部分，你将能够让你的猫咪在conga线上跳舞，并且让你的玩家能够赢或者输掉游戏。你甚至可以仅仅为了好玩而设置一些音乐或是声音特效。

上一个教程是这个系列教程中最长的一篇，但是看起来我们一次性发布整篇超长教程会比让读者花上额外的一天来等待教程的另外一半更加明智。

这篇教程还包括了一些第四部分遗漏的内容。如果你还没有那篇教程中使用到的项目，你可以点击[这里](http://cdn4.raywenderlich.com/wp-content/uploads/2015/04/ZombieConga-Part5-Start.zip)下载

解压文件（如果你需要下载这个文件）并且通过双击如下的文件打开你的场景：
</br> ZombieConga/Assets/Scenes/CongaScene.unity

你已经拿到了Zombie Conga项目的一部分内容，所以现在你需要做的就是许多充满抱负的游戏开发者最头疼的事情：完成游戏！

##开始吧！##

Zombie Conga 是一个横向卷轴（side-scrolling）游戏，但是到目前为止，你的僵尸仅仅是停留在海滩上的一小部分。现在是时候让他到其他的地方去走走了。

为了将整个场景滚动到左侧，你可以在整个游戏世界的范围内将摄影机（camera）移动到右边。通过这个方法，沙滩，包括沙滩上的猫咪、僵尸、在沙滩上散步的老妇人，都会自然地随着场景的滚动而滚动所以你并不需要手动设置他们的位置。

在 Hierarchy 中选择主摄影机（Main Camera）。添加一个新的叫做 CameraController 的C#脚本。你已经通过这一点在这个教程系列中创建了好几个脚本了，所以你可以自己尝试一下。如果你需要复习一下，你可以查看一下如下链接：
</br> [Solution Inside: Need help adding a new script?](http://www.raywenderlich.com/71029/unity-4-3-2d-tutorial-scrolling-scenes-and-sounds)
</br>打开 MonoDevelop 中的 CameraController.cs 并且添加如下的实例变量：

	public float speed = 1f;
	private Vector3 newPosition;

你可以通过使用速度（speed）来控制场景滚动的速度。你只需要更新摄影机（camera）位置的x坐标，不过用来表示 Transform 的位置的独立组件是只读的（readonly）。你可以重用newPosition而不是在每次更新位置的时候重复性地创建新的新的 Vector3 对象。

因为你只能够设定 newPosition 的 x 坐标的值，所以你需要正确地初始化 vector 的其他组件。为了做到这一点，你需要在 Start 中添加如下这一行代码：

	newPosition = transform.position;
	
这一行代码能够将摄影机（camera）的初始位置复制到 newPosition。
</br>现在在Update中添加如下代码：

	newPosition.x += Time.deltaTime * speed;
	transform.position = newPosition;

这样就可以轻松地调整对象的位置，就好像是在每一秒移动 speed 个单位长度一样。 
	
注：执着于修改错误的人可能不会喜欢如上第一行代码依赖“newPosition会准确地反应摄影机（camera）的位置”这一假设的方式。如果你是这样执着的人，你也可以用以下代码替换上个代码块中的第一行：</br>
	
	newPosition.x = transform.position.x + Time.deltaTime * speed;
	
保存这个文件（File\Save）并且交换回Unity。</br>
播放你的场景并且你场景中的元素开始移动。僵尸和敌人似乎运行状况良好，但是很快他们就跑到沙滩外面了！</br>

![Alt text](http://cdn5.raywenderlich.com/wp-content/uploads/2015/04/broken_scrolling_no_repeats.gif)  

你需要像你处理敌人那样处理你的背景。就是说，当设置的敌人走到屏幕的边界外面以后，你会改变他的位置让他能偶从屏幕的另外一端重新进入。

添加一个新的叫做 BackgroundRepeater 的C#脚本到背景（background）中。你已经通过这一点在这个教程系列中创建了好几个脚本了，所以你可以自己尝试一下。如果你需要复习一下，请回顾以下以前的教程并找到相应的方法。
</br>打开 MonoDevelop 中的 BackgroundRepeater.cs 并且添加如下的实例变量：

	private Transform cameraTransform;
	private float spriteWidth;
	
你可以在cameraTransform中的摄影机（camera）的Transform中储存一个引用（reference）。这并不是必须的，但是介于你需要在每次更新（Update）的时候使用它，你只需要找到它一次并持续使用它而不是一次有一次地去找寻找相同的元件（component）。

你同样一次有一次地获取精灵的宽度，你会将其缓存在 spriteWidth 因为你知道你不会在运行的过程中改变背景的精灵。

在 Start 添加如下代码来初始化这些变量：

	//1
	cameraTransform = Camera.main.transform;
	//2
	SpriteRenderer spriteRenderer = renderer as SpriteRenderer;
	spriteWidth = spriteRenderer.sprite.bounds.size.x;
	
以上的代码会按如下方式初始化你添加的变量：</br>
1.寻找到场景的主摄影机（camera）对象（Zombie Conga的为一个摄影机）并且通过设置将 cameraTransform 指向这个摄影机（camera）的 Transform

2.将对象的内建渲染器属性（object’s built-in renderer property）传递到 SpriteRenderer 以获取精灵（sprite）的属性，这样就能从精灵的属性中得到精灵的 bounds。bounds 对象有一个尺寸属性，它的 x 元件（x component）掌握着对象的宽度（width），并储存在 spriteWidth 中。

为了判定背景精灵已经超出屏幕，你应该实行（implement）OnBecameInvisible，就像你在处理敌人的时候那样。但是既然你已经学过这一部分知识了，所以这一次你可以直接检测对象的位置了。

在 Zombie Conga 中，摄影机（camera）的位置一直是在屏幕的正中。同样，当你用这个系列教程的第一部分中的方法来引用背景精灵，你将精灵的正中设置为原点。

你会通过“如果至少有一个精灵在横向上完全离开摄影机（camera）的范围，就认为背景已经滚动出了屏幕”这一假设来判定背景是否已经滚动出了屏幕，而不是通过计算屏幕左边界的 x 位置来判定背景是否已经滚动出了屏幕。如下的图片展现了当刚好一个精灵在横向上被放置到摄影机以外的位置时，这个背景精灵就刚好越出屏幕。

![Alt text](http://cdn4.raywenderlich.com/wp-content/uploads/2015/04/bg_offscreen_detection.png) 

屏幕的左边界在不同的设备上是不同的，但是只要屏幕的宽度不比背景精灵的宽度大，这个方法就是适用的。

将如下代码添加到 Update 中：

	if( (transform.position.x + spriteWidth) < cameraTransform.position.x) 
	{
	Vector3 newPos = transform.position;
	newPos.x += 2.0f * spriteWidth;
	transform.position = newPos;
	}

这个 if 语句检测了是否这个对象已经完全超出屏幕范围，就像我们早些时候提到的那样。如果是，计算出一个偏离当前位置的偏移量为精灵的宽度的两倍的新位置。

为什么是两倍宽度呢？当检测到背景已经超出屏幕时，只是将精灵移动 spriteWidth 的距离会将这个精灵突然地移动到一个摄影机可视范围内位置，如下图所示：

![Alt text](http://cdn5.raywenderlich.com/wp-content/uploads/2015/04/bg_adjusted_1x.png) 

保存文件（File\Save）并交换回Unity

播放场景，你可以看到背景离开屏幕然后最终又回到视野中，如下图加速序列（sped-up sequence）所示（下图经过加速）：

![Alt text](http://cdn4.raywenderlich.com/wp-content/uploads/2015/04/broken_scrolling_gaps.gif) 

这很管用，但是或许你不希望那些蓝色的间隔一次有一次地出现。为了解决这个问题，你可以简单地添加另一个背景精灵来填充那段空白。

在 Hierarchy 中的 background 上点击鼠标右键并在出现的弹出窗口菜单中选择复制（Duplicate）。选择复制的背景（如果它并没有被选中）并将它的 Transform‘s 的位置的 x 值设置为20.48，如下图所示：

![Alt text](http://cdn5.raywenderlich.com/wp-content/uploads/2015/04/duplicate_bg_position.png) 

记得在这个系列教程的第一部分中有提到，背景精灵的宽度是2048像素并且你用单位长度为100的ratio来引用这个背景精灵。也就是说将一个背景精灵的 x 位置设置为20.48将会把这个背景精灵放置在其他对象的右边，其他对象的 x 位置为0.

你现在在你的场景视图（Scene view）得到了一个大幅伸展的海滩，如下图所示：

![Alt text](http://cdn5.raywenderlich.com/wp-content/uploads/2015/04/long_beach_in_scene.png) 

再一次播放这个场景，现在你的僵尸将会一直在沙滩上漫步，如下图加速序列（sped-up sequence）所示。不要让这幅低质量gif中的瑕疵愚弄了你，要知道在真正的游戏中，背景是无缝滚动的。

![Alt text](http://cdn2.raywenderlich.com/wp-content/uploads/2015/04/good_scrolling_bg.gif) 

在播放这个场景的时候，一个很突出的问题是这个沙滩上完全没有猫咪的踪影。我不知道你们的习惯是怎样的，反正我每次去砂染的时候，都会带上我的小猫咪。

##产生猫咪

你会需要新的猫咪不停地出现在沙滩上直到玩家获胜或是输掉游戏。为了处理这个问题，你需要创建一个全新的脚本并添加一个空的GameObject。











	
