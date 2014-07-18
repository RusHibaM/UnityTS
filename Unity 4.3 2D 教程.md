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

#开始吧！#

Zombie Conga 是一个横向卷轴（side-scrolling）游戏，但是到目前为止，你的僵尸仅仅是停留在海滩上的一小部分。现在是时候让他到其他的地方去走走了。

为了将整个场景滚动到左侧，你可以在整个游戏世界的范围内将摄像机（camera）移动到右边。通过这个方法，沙滩，包括沙滩上的猫咪、僵尸、在沙滩上散步的老妇人，都会自然地随着场景的滚动而滚动所以你并不需要手动设置他们的位置。

在 Hierarchy 中选择主摄影机（Main Camera）。添加一个新的叫做   CameraController 的C#脚本。你已经通过这一点在这个教程系列中创建了好几个脚本了，所以你可以自己尝试一下。如果你需要复习一下，你可以查看一下如下链接：
</br> [Solution Inside: Need help adding a new script?](http://www.raywenderlich.com/71029/unity-4-3-2d-tutorial-scrolling-scenes-and-sounds)
</br>打开MonoDevelop 中的 CameraController.cs 并且添加如下的实例变量：

	public float speed = 1f;
	private Vector3 newPosition;

你可以通过使用速度（speed）来控制场景滚动的速度。你只需要更新摄像机（camera）位置的x坐标，不过用来表示 Transform 的位置的独立组件是只读的（readonly）。你可以重用newPosition而不是在每次更新位置的时候重复性地创建新的新的 Vector3 对象。

因为你只能够设定 newPosition 的 x 坐标的值，所以你需要正确地初始化 vector 的其他组件。为了做到这一点，你需要在 Start 中添加如下这一行代码：

	newPosition = transform.position;
	
这一行代码能够将摄像机（camera）的初始位置复制到 newPosition。
</br>现在在Update中添加如下代码：

	newPosition.x += Time.deltaTime * speed;
	transform.position = newPosition;

这可以简单地调整对象的位置就好像是在每一秒改变speed单位。
	
	注：