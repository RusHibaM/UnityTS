#Unity 4.3 2D 教程： 滚动，场景和音效

###Christopher LaPollo on July 14, 2014
###RusHibaM 译

欢迎回到我们的 Unity 4.3 2D 系列教程。

的确，Unity 4.5在不久前已经组合框发布了。但是本系列主要涉及的是Unity的2D特效，而这些特效正是在Unity 4.3版本中才被首次引入的。在4.5版本中，我们修复了一些bug并稍微修改了一些GUI元素。所以，如果你正在使用一个更新版本的引擎你应该时刻留意这一点，并且你应该注意你自己的编辑器与这个网页中的截图之间的细微差别。

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
	
以上的代码会按如下方式初始化你添加的变量：

1. 寻找到场景的主摄影机（camera）对象（Zombie Conga的为一个摄影机）并且通过设置将 cameraTransform 指向这个摄影机（camera）的 Transform

2. 将对象的内建渲染器属性（object’s built-in renderer property）传递到 SpriteRenderer 以获取精灵（sprite）的属性，这样就能从精灵的属性中得到精灵的 bounds。bounds 对象有一个尺寸属性，它的 x 元件（x component）掌握着对象的宽度（width），并储存在 spriteWidth 中。

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

注：你可以在场景的生命周期内将这个脚本添加到任何已经存在的 GameObject 上，比如僵尸（zombie）或者主摄影机（Main Camera）。但是使用专用的对象让你可以赋予它一个描述性的名字，这样的方式使得你能够更容易地在 Hierarchy 中找到你需要设置的对象。

在 Unity 菜单中选择 GameObject\Create Empty 以创建新的空的游戏对象。我们将本例中创建的对象命名为 Kitten Factory。

创建一个叫做 KittyCreator 的 C# 脚本并附属在刚才创建的 Kitten Factory 下。接下来就没有创建新脚本的提示了，你应该能够自己应付了。（但是如果你实在自己应付不了，就去查看这个系列教程之前的部分吧）

打开 MonoDevelop 中的 KittyCreator.cs 并将其中的内容替换为如下代码：

	using UnityEngine;
 
	public class KittyCreator: MonoBehaviour 
	{
	//1
	public float minSpawnTime = 0.75f; 
	public float maxSpawnTime = 2f; 
 
	//2    
	void Start () 
	{
    Invoke("SpawnCat",minSpawnTime);
	}
 
	//3
	void SpawnCat()
	{
    Debug.Log("TODO: Birth a cat at " + Time.timeSinceLevelLoad);
    Invoke("SpawnCat", Random.Range(minSpawnTime, maxSpawnTime));
	}
	}
	
事实上这些代码并不能产生任何猫咪，他简单地将这项工作留给了 groundwork。以下是这段代码做了什么：

1. minSpawnTime 和 maxSpawnTime 决定猫咪产生的频率。在一只猫咪产生之后，KittyCreator 会在等待至少 minSpawnTime 秒和至多 maxSpawnTime 秒后产生下一只猫咪。你将他们定义为公用的所以你之后可以在编辑器中按照你的喜好修改猫咪产生的速度。

2. Unity 的 Invoke 方法可以让你在指定的延迟后调用另一个方法。 Start 会调用 Invoke 方法，命令它等待 minSpawnTime 秒并在以后调用 SpawnCat。这会在场景开始播放后加入一段没有猫咪产生的时期。

3. 现在 SpawnCat 会简单地记录一条消息使得你可以在它运行并使用 Invoke 来规划另一个对于 SpawnCat 的调用的时候知道它正在进行这些操作。猫喵产生的间隔时间是一个随机产生的介于 minSpawnTime 与 maxSpawnTime 的值，这样猫咪产生的时间间隔就看起来不是可预见的了。

保存文件（File\Save）并转回Unity.</br>
运行这个场景，你将可以看到在 Console 中出现的日志，如下图所示：

![Alt text](http://cdn2.raywenderlich.com/wp-content/uploads/2015/04/cat_spawn_msg.png) 

这样你的 Kitten Factory 就可以按照规划工作了，你需要让它产生（原文使用spit out）一些猫咪。为了达到这一目的，你需要使用Unity 最强大的特性之一：Prefabs。

##Prefabs
Prefabs 现在在你的 Project 中，而不是在你场景的 Hierarchy 中。你可以将 Prefabs 用作在你的场景中创建对象的模板。

但是，这些实例并不仅仅是原始 Prefabs 的副本。取而代之的是，Prefab 定义了一个对象的默认值，接下来你可以自由地修改你的场景中的特定实例，而不会影响其他同样通过这个 Prefab 创建的对象。

在 Zombie Conga 中，你希望创建一个猫咪 Prefab 并且让 Kitten Factory 在场景中的不同位置创建该 Prefab 的实例。但是在你的场景中不是已经有了一个猫咪对象了吗？并且你不是已经通过你自己设置好的所有的动画，物理效果和脚本配置好了吗？可以确定的是，如果制作一个 Prefab 的时候你必须重做这些步骤，这一定很恼人吧。幸运的是，你的确不必。

为了将它变为一个 Prefab，你只需要简单地将 cat 从 Hierarchy 中拖动到 Project 浏览器中。你可以在 Project 浏览器中看到一个新建的 cat Prefab 对象，但是你同样需要注意的是，单词 cat 在 Hierarchy 中变成了蓝色，就像下图所示：

![Alt text](http://cdn5.raywenderlich.com/wp-content/uploads/2015/04/create_cat_prefab.gif) 

在制作你自己的游戏的时候，在 Hierarchy 名字显示为蓝色的对象是 Prefabs 的实例。当你选择其中之一时，你会在监视器（Inspector）中看到如下的按钮：

![Alt text](http://cdn2.raywenderlich.com/wp-content/uploads/2015/04/prefab_inspector_buttons.png)

这个按钮在编辑 Prefabs 的实例时非常有用。它们让你可以完成如下工作：

1. 选择：这个按钮在 Project 浏览器中选择被用来创建实例的 Prefab 对象。

2. 还原：这个按钮会将任何你对实例做的本地修改恢复到 Prefab 中的默认值。

3. 申请：这个按钮会获取所有你在该实例上所做的本地修改并将这些值设置到原来的 Prefab 上，将这些值设置为所有通过这个 Prefab 创建的实例的默认值。任何已经存在的由该 Prefab 得到的，还没有通过进行本地重写来设置这些值的实例会自动地把它们的默认值修改为最新设置的默认值。

重要提示：点击 申请 按钮会影响所有共享这个对象的 Prefab 的 GameObject，不仅仅是目前这个场景中相关的 GameObject会受影响，整个project里面所有场景中相关的 GameObject 都会受到影响。

现在你需要停止 Kitten Factory 对于你的 Console 的文字污染，你现在该让它用猫咪污染你的沙滩了！

返回 MonoDevelop 中的 KittyCreator.cs 并在 KittyCreator 中添加如下变量：

	public GameObject catPrefab;
	
你将你的 cat Prefab 分配给 Unity 编辑器中的 catPrefab，然后 KittyCreator 会在创建新的猫咪的时候将其用作模板。但是在你这么做以前，将 SpawnCat 的中 Debug.Log 行（Debug.Log line in SpawnCat）替换为如下代码：

	// 1
	Camera camera = Camera.main;
	Vector3 cameraPos = camera.transform.position;
	float xMax = camera.aspect * camera.orthographicSize;
	float xRange = camera.aspect * camera.orthographicSize * 1.75f;
	float yMax = camera.orthographicSize - 0.5f;
 
	// 2
	Vector3 catPos = new Vector3(cameraPos.x + Random.Range(xMax - xRange, xMax),
              Random.Range(-yMax, yMax),
              catPrefab.transform.position.z);
 
	// 3
	Instantiate(catPrefab, catPos, Quaternion.identity);

以上的代码选择了一个随机的在摄影机（camera）可视范围内的位置并将新的猫咪放置在该位置。</br>具体地：

1. 摄影机（camera）当前的位置和尺寸决定了场景的可视区域。你通过这些信息来计算出你希望放置一只新猫咪的区域的 x 和 y 值的界限。这个计算不会将猫咪放置在太靠近屏幕顶部或底部的位置亦或是很远的屏幕的左边界。如下的图片可以帮助你对其中包含的数学概念由一个视觉上的认识。

2. 你创建一个新的包括固定的 catPrefab 的 z 位置（因此所有的猫咪会出现在相同的 z 层深度），随机的 x 值和 y 值的位置。这些随机值是在下图所示的区域内选取，这个所示区域比世纪场景的可见区域稍微小一些。

3. 你调用 Instantiate 来创建 catPrefab 的实例并放置到场景中的一个由 catPos 决定的位置。你传递 Quaternion.identity 作为新对象的 rotation 因为你根本不希望新对象被旋转。相反，猫咪的旋转会由你在这个系列教程的第二部分完成的 spawn 动画来设置。

注：这会让所有刚生成的猫咪的脸朝向同一个方向。如果你希望猫咪的脸朝向不同的方向，你可以向 Instantiate 传递一个围绕 z 轴的随机的转动而不是使用单位矩阵。但是值得注意的是，事实上，这在你读完这篇教程的后面的部分并进行一些修改之前并不会管用，

![Alt text](http://cdn4.raywenderlich.com/wp-content/uploads/2015/04/cat_spawn_area.png)

保存文件（File\Save）并转回Unity。

你的场景中不再需要猫咪了因为你的工厂（factory）会在运行的时候创建它们。右键点击 Hierarchy 中的 cat 并选择弹出的菜单中的 Delete进行删除操作，如下图所示：

![Alt text](http://cdn3.raywenderlich.com/wp-content/uploads/2015/04/delete_cat.png)

选择 Hierarchy 中的 Kitten Factory。在监视器（Inspector）中，点击在Kitty Creator (Script) 元件（component）的 Cat Prefab 区域的小巧的 circle/target 图标，如下红色箭头所示：

![Alt text](http://cdn1.raywenderlich.com/wp-content/uploads/2015/04/cat_prefab_field.png)

再出现的 Select GameObject 对话框中，选择 Assets 选项卡内的 cat， 如下图所示：

![Alt text](http://cdn3.raywenderlich.com/wp-content/uploads/2015/04/cat_prefab_selection.png)

Kitten Factory 现在在监视器（Inspector）中看来是这个样子的：

![Alt text](http://cdn1.raywenderlich.com/wp-content/uploads/2015/04/kitten_factory_inspector.png)

如果你的 Kitten Factory 的 Transform 值和这里展示的有所不同，也请不要担心。Kitten Factory 的存在仅仅是为了保持 Kitty Creator 脚本 元件（Component）。它并不具备可视化的元件（component）所以它的 Transform 值是没有什么意义的。

再次运行这个场景并看看任何你想看的地方，这片沙滩看上去已经可以咳出可爱的毛球了。（译者注：应该是说沙滩上已经由猫咪了，我的理解是猫咪会咳出毛球，所以作者此处用可以咳出毛球指代有猫咪，原文为 the very beach itself appears to be coughing up adorable fur balls.）。

![Alt text](http://cdn1.raywenderlich.com/wp-content/uploads/2015/04/cats_spawning.png)

但是仍然存在一个问题。当你玩这个游戏的时候，留意到数量庞大的猫咪在 Hierarchy 中被缓慢的建立，如下图所示：

![Alt text](http://cdn2.raywenderlich.com/wp-content/uploads/2015/04/crazy_cat_clones-170x500.png)

这样不行！如果你的游戏持续足够长的时间，这个排序逻辑会让游戏因为意外停止而崩溃。当你的猫咪试图关闭（go off）你的屏幕的时候，你应该将它们从屏幕中除去。

打开 MonoDevelop 中的 CatController.cs 并将如下方法添加到 CatController 中：

	void OnBecameInvisible() {
	Destroy( gameObject ); 
	}
	
这只是简单地调用 Destroy 来释放（destroy） gameObject。所有的 MonoBehaviour 脚本，比如 CatController，会访问指向保持（hold）脚本的 GameObject 的 gameObject。虽然这种方法并没有表现出来，在调用完 Destroy 后再执行一个方法中的其他代码才是安全的因为其实 Unity 并不会马上释放（destroy）这个对象。

注：你应该已经注意到了 CatController 中的另一个方法 GrantCatTheSweetReleaseOfDeath。这个方法为了和你现在使用 Destroy 方法相同的理由而使用 DestroyObject。这说明什么？
</br>老实说，我也不清楚这两者间有什么不同。Unity的文档中包含了 Destroy 却没有包含 DestroyObject，但是它们看起来具有相同的作用。我或许只是输入了这两者中的其中之一，而编译器并没有报错，所以我就再没有考虑任何与这个问题相关的东西了。
</br>如果你知道它们的不同或是知道为什么它们中的一个要优于另外一个，请在评论中提及，万分感谢！（截止发布，很遗憾我没有在原帖的评论或是其他地方找到这个问题的答案。）

保存文件(File\Save)并转回Unity.

再次运行这个场景。就像我们在本系列的第三部分提到的那样，只有到一个对象在所有摄影机（camera）视野之外的时候，OnBecameInvisible 才会被调用，所以请保证在测试本例的时候场景试图是不可见的。

现在无论你玩多久，Hierarchy 中猫咪的数量永远不会超过某一个数量了。更具体地说，Hierarchy 会包含与场景中可以看到的猫咪数量相同的对象，如下图所示：

![Alt text](http://cdn2.raywenderlich.com/wp-content/uploads/2015/04/cat_spawns_under_control.png)

注：在 Unity 的运行环境中创建或是释放（destroy）对象需要付出昂贵的代价。如果你正在制作一个仅仅比 Zombie Conga 复杂一点点的游戏，或许尽可能地重用那些对象是很有价值的。

比如说，与其在一只猫咪离开屏幕时释放（destroy）掉它，不如再下次你需要生成一只猫咪时重用这个对象。你在处理敌人的时候已经使用了类似的方法，但是在处理猫咪的时候你需要同时保持整整一个列表的可重用对象并记住在重新使用它们来生成猫咪的时候重新将动画状态设置到初始状态。

这种技术被称为 对象池（object pooling）并且你可以在 Unity 的[培训课程](http://unity3d.com/learn/tutorials/modules/beginner/live-training-archive/object-pooling)中找到一些相关的内容.

OK,你现在已经得到了一片满是猫咪的沙滩，并且沙滩上还有一个到处闲逛寻找陪伴的僵尸。我觉得你应该已经知道我们做了什么以及接下来该做什么了。

##Conga时刻！

如果你从这个系列教程的第一部分开始就是忠实的读者，你或许已经开始怀疑为什么这个游戏被叫做Zombie Conga。

![Alt text](http://cdn2.raywenderlich.com/wp-content/uploads/2015/04/wheres_the_conga2.png)

是时候了。

当这个僵尸撞到一个猫咪，你将把这个猫咪加入到 conga 线中（译者注：就是猫咪会一直跟着僵尸运动）。但是，你会想要当僵尸撞到敌人时能有不同的效果。为了区别撞击到的是猫咪还是敌人，你需要为它们分配不同的标签（tags）。

###用标签（tags）来分辨对象

Unity 允许你向任何 GameObject 分配一个字符串，这个字符串被称作标签（tag）。新创建的项目包含了一些默认的标签（tag），就像主摄影机（Main Camera）和玩家（Player），但是你可以自由地添加任何你希望添加的标签（tag）。

在 Zombie Conga 中，你可以通过使用唯一一个标签来避免一些问题，因为在这个游戏中僵尸可以撞击的对象只有两种。比如说，你可以给猫咪添加一个标签（tag）并假设如果僵尸撞到一个没有这个标签的对象，就认为僵尸撞到的一定是敌人。但是，当你想要修改你的游戏的时候，这个快捷的方式反而会变成一个产生 bug 的好方法。

为了让你的代码更容被读懂且更容易维护，你需要创建两个标签（tag）：猫咪和敌人。

从 Unity 菜单中选择 Edit\Project Settings\Tags and Layers。监视器（Inspector）现在显示的是 Tags & Layers 编辑器。如果还没有打开，可以通过点击名字左侧的三角形来展开标签（tag）列表，如下图所示：

![Alt text](http://cdn1.raywenderlich.com/wp-content/uploads/2015/04/empty_tags_list.png)

在标记为 Element 0 的输入框中输入 cat。当你输入的时候，Unity会自动添加一个具有新的具有标记的输入框，新的标记为 Element 1.你的监视器（Inspector）看起来就像下面这个样子：

![Alt text](http://cdn5.raywenderlich.com/wp-content/uploads/2015/04/cat_tag.png)

注：除了你定义的标签（tag），Unity 会保持至少一个额外的标记了的输入框，所以无论什么时候，只要你在最后一个可输入的输入框输入了信息，它最会添加一个新的输入框。就算你改变输入框的 Size 的值来匹配你实际有的标签的数目，Unity 也会自动将这个数值改回为刚好比你现有的标签的数目大一个的值。
</br>译者注：原文使用的 field 来表述可以进行输入的区域，所以我在这里译为“输入框”。

在 Project 浏览器中选择 cat 并将监视器（Inspector）中的标记为 Tag 的组合框中选择 cat，如下图所示：

![Alt text](http://cdn1.raywenderlich.com/wp-content/uploads/2015/04/setting_cat_tag.gif)

当你在添加 cat 的标签（tag）的时候，你应该已经也添加了 enemy 的标签（tag）。但是我希望给你看一种不同的创建标签的方法。

很多时候当你决定要标记某一个对象的时候，只需要检查监视器（Inspector）中标签（tag）的组合框并确定你希望使用的标签（tag）并不存在。你可以在标签（Tags）的组合框中直接打开 Tags and Layers 编辑器而不用再去浏览 Unity 的编辑器菜单。

在 Hierarchy 中选择 enemy。在监视器（Inspector）中从标签（tag）的组合框选择添加标签（ Add Tag…），如下图所示：

![Alt text](http://cdn4.raywenderlich.com/wp-content/uploads/2015/04/add_tag_menu.png)

监视器（Inspector）中再一次显示 Tags & Layers 编辑器。在标签（Tags）部分中，在标记为 Element 1 的输入框中输入 enemy。监视器（Inspector）现在看起来如下图所示：

![Alt text](http://cdn5.raywenderlich.com/wp-content/uploads/2015/04/tags_in_list.png)

新标签（tag）的创建已经完成了，现在选择 Hierarchy 中 enemy并将它的标签（Tag）设置为上一段中输入的 enemy。如下图所示：

![Alt text](http://cdn3.raywenderlich.com/wp-content/uploads/2015/04/enemy_tag_set.png)

嗯，现在你的对象已经被打上标签了，你可以在你的脚本中轻松辨识它们了。该怎么样呢，打开 MonoDevelop 中的 ZombieController.cs 并用如下代码将 OnTriggerEnter2D 中的内容替换掉。

	if(other.CompareTag("cat")) 
	{
		Debug.Log ("Oops. Stepped on a cat.");
	}
	else if (other.CompareTag("enemy")) 
	{
		Debug.Log ("Pardon me, ma'am.");
	}


你可以通过调用 CompareTag 来检查一个特定的 GameObject 是否已经被打上了标签（tag）。只有 GameObject 能够有标签（tag），但是在元件（Component）上调用这个方法－－就像你正在做的这样－－检查这个元件（Component）的 GameObject 的标签（tag）

保存文件（File\Save）并转回Unity.

运行场景，无论僵尸撞到的是猫咪还是敌人，你都应该在 Console 中看到一些适当的消息。如下图所示：

![Alt text](http://cdn4.raywenderlich.com/wp-content/uploads/2015/04/collision_msgs.png)

现在你知道你的碰撞测试已经被正确设置了。是时候让它们为你做点什么了！

##从脚本触发动画

还记得你在本系列的第二、三部分中制作的动画吗？猫咪在快乐地上下摆动，就像这样

![Alt text](http://cdn1.raywenderlich.com/wp-content/uploads/2015/02/cat_anim_wiggle.gif)

当你的僵尸撞到一只猫咪，你会希望这只猫咪变成一只僵尸猫咪。下面这张图片向你展示了如何在动画窗口（Animator window）里，通过将猫咪的 InConga 参数设置为 true 来实现这一想法。

![Alt text](http://cdn2.raywenderlich.com/wp-content/uploads/2015/02/cat_conga_working.gif)
 
现在你会希望用内部代码来做一些相同的事情。为了这么做，回到 MonoDevelop 中的 CatController.cs 并向 CatController 中添加如下方法：

	public void JoinConga() {
	collider2D.enabled = false;
	GetComponent<Animator>().SetBool( "InConga", true );
	}

第一行代码禁用了猫咪的碰撞事件。在僵尸撞到一只猫咪的时候，这可以避免 Unity 发送超过一个碰撞事件。（之后为了处理这个问题，你将用一个另外的办法来处理僵尸与敌人（enemy）的碰撞）

第二行代码在猫咪的动画元件（Animator Component）将 InConga 设置为 true。通过这个做法，你触发了一个状态转换，将 CatWiggle 动画剪辑（Animation Clip）转换到 CatZombify 动画剪辑（Animation Clip）。你将通过本系列第三部分提到的动画窗口（Animator window）来设置这个转换。

顺便提一句，你必须注意到你将 JoinConga 声明为公开（public）。这样你就可以在其他脚本中调用它，这也正是你正在做的。

保存 CatController.cs (File\Save) 并转回依然是在 MonoDevelop 中 ZombieController.cs。

在 ZombieController 里面的 OnTriggerEnter2D 中找到如下这行代码：

	Debug.Log ("Oops. Stepped on a cat.");
	
然后将这行代码更换为：

	other.GetComponent<CatController>().JoinConga();
	
现在无论什么时候，只要僵尸撞到了一只猫咪，就会在猫咪的 CatController 组件（component）上调用 JoinConga。

保存文件（File\Save）并转回 Unity.

播放场景，可以看到当僵尸穿过一只猫咪的时候，猫咪会变成绿色并开始在原地跳动。到目前为止，看起来还行。

![Alt text](http://cdn1.raywenderlich.com/wp-content/uploads/2015/04/zombification_1.gif)

没有人希望看到一大堆猫咪零散地分布在沙滩上。你所希望的是它们加入到你僵尸坚持不懈的舞蹈当中，你需要教会这些猫咪怎么样区跟随它们的僵尸老大。

###Conga 运动

你需要一个 List 来追踪哪些猫咪已经加入到 conga 线了（译者注：就是指哪些猫咪已经在跟随着僵尸运动了）。

回到 MonoDevelop 中的 ZombieController.cs。

首先，将如下这条这额外的 using 语句添加到文件的开头位置：

	using System.Collections.Generic;

这条 using 语句和 Objective-C 中的 #import 语句是相似的。它使得脚本可以访问特定的命名空间和这些命名空间中的类型（types）。在本例中，你需要访问 Generic 命名空间来声明一个具有特定数据类型的 List。

将如下私有变量（private variable）添加到 ZombieController 中：

	private List<Transform> congaLine = new List<Transform>();
	
congaLine 会为 conga 线中的猫咪储存 Transform 对象。你正在储存 Transform 而不是 GameObject 因为你大部分时间需要应付的是猫咪的位置。而且不管怎么说，如果你实在需要访问其他的部分，你可以通过其 Transform 获取该 GameObject 的任何部分。

每一次僵尸碰到猫咪，你会把这个猫咪的 Transform 附加到 congaLine。这意味着 congaLine 中第一个 Transform 代表者刚好在僵尸后面那个猫咪，第二个 Transform 代表着第一只猫咪后面的那只猫咪，依此类推。

为了将猫咪添加到 conga 线中，在 ZombieController 的 OnTriggerEnter2D 中添加如下这行代码，只需要放置在调用 JoinConga 的那一行的后面：

	congaLine.Add( other.transform );

这一行代码将猫咪的 Transform 添加到了 congaLine 中。

如果你打算现在运行这个场景，你不会看到任何的变化。因为你只是在维护一个满是猫咪的 list，却没有写任何代码让猫咪从加入 conga 线的初始位置起一直跟着僵尸移动。所以当僵尸领头的 conga 线移动的时候，这看起来一点也不欢乐。

为了解决这个问题，打开 MonoDevelop 中的 CatController.cs。

移动猫咪的代码和你在本系列的第一部分中写的移动僵尸的代码是相似的。开始吧，将如下的实例变量加入到 CatController：

	private Transform followTarget;
	private float moveSpeed; 
	private float turnSpeed; 
	private bool isZombie;
	
你将会使用 moveSpeed 和 turnSpeed 来控制猫咪运动的频率，就像你在控制僵尸运动时所做的那样。你只希望猫咪在变成僵尸猫咪以后才开始运动，所以你会一直检测 isZombie 变量的值来确定猫咪是否已经变成了僵尸猫咪。最后 followTarget 会保持一个指向这个猫咪队列中前一个目标（可能是一只猫咪，也可能是那个僵尸）的引用（reference）。你会使用这个来计算它移动到的位置。

以上的变量都是私有的（private），所以你会在想我该怎么设置它们。为了让 conga 线运动看起来是自然的，你会以僵尸的运动和转速度（turn speeds）为基础设置猫咪的动作。因此，在整个僵尸运动的过程中，你得让僵尸把运动的信息传递给队列中的每一只猫咪。

在 CatController.cs 中将 JoinConga 的 implementation更换为如下代码：

	//1
	public void JoinConga( Transform followTarget, float moveSpeed, float turnSpeed ) {
 
	//2
	this.followTarget = followTarget; 
	this.moveSpeed = moveSpeed;
	this.turnSpeed = turnSpeed;
 
	//3
	isZombie = true;
 
	//4
	collider2D.enabled = false;
	GetComponent<Animator>().SetBool( "InConga", true );
	}
	
以下是对于新版本 JoinConga 的分析与解释：

1. 这个方法现在需要一个目标 Transform，一个运动的速度和一个转速度（turn speeds）。接着你会改变 ZombieController 以通过适当的值调用 JoinConga。

2. 这几行会储存传递到方法中的变量。注意通过使用 this. 来区别同名的猫咪的变量和该方法的参数

3. 这会将猫咪标记为僵尸猫咪。接下来你会看到这一步是多么的重要

4. 这最后的两行和之前版本的 JoinConga 是完全相同的。

现在向 CatController 中加入 Update 的 implementation 如下：

	void Update () {
	//1
	if(isZombie)
	{
    	//2
    	Vector3 currentPosition = transform.position;            
    	Vector3 moveDirection = followTarget.position - currentPosition;
 
    	//3
    	float targetAngle = 
      Mathf.Atan2(moveDirection.y, moveDirection.x) * Mathf.Rad2Deg;
    	transform.rotation = Quaternion.Slerp( transform.rotation, Quaternion.Euler(0, 0, targetAngle), turnSpeed * Time.deltaTime );
 
    	//4
    	float distanceToTarget = moveDirection.magnitude;
    	if (distanceToTarget > 0)
    	{
      		//5
      		if ( distanceToTarget > moveSpeed )
        		distanceToTarget = moveSpeed;
 
      		//6
      		moveDirection.Normalize();
      		Vector3 target = moveDirection * distanceToTarget + currentPosition;
      		transform.position = Vector3.Lerp(currentPosition, target, moveSpeed * Time.deltaTime);
    	}
	}
	}
	
这看起来有一点复杂，但是实际上大部分和你所写的移动僵尸的代码是一样的。我们来看看这些代码做了些什么：

1. 你不希望猫咪在加入到 conga 线以前开始运动，所以只要猫咪在屏幕中处于激活状态（译者注：即可操作状态），Unity 会在每一帧调用一次 Update。这项检测机制会保证猫咪不会在不该活动的时候活动。

2. 如果这个猫咪已经在 conga 线上了，这个方法会获取猫咪当前的位置并计算从它目前的位置到 followTarget（译者注：就是这只猫咪一直跟随着的那个目标，也就是队列中的上一只猫咪或者那个僵尸） 的位置的向量。

3. 这些代码让猫咪始终朝向它运动的方向。这和你在本系列第一部分中写在 ZombieController.cs 中的代码是一样的。

4. 接下来会检测 moveDirection 的 magnitude－－也就是运动向量的长度（length），这里没有任何和数学相关的东西－－并且检测目前这只猫咪是不是已经到达了目标位置。

5. 这项检测确保了猫咪不会以超出每秒 moveSpeed 的速度运动。

6. 最后，猫咪按照一个基于 Time.deltaTime 的适当距离运动。这基本上和你在本系列第一部分中写在 ZombieController.cs 中的代码一样。

你现在已经完成了 CatController.cs，所以保存文件吧（File\Save）.

因为你更改了 JoinConga 方法的签名（signature），你需要修改 ZombieController 中调用这个方法的那行代码。转回到 MonoDevelop 中的 ZombieController.cs。

在 OnTriggerEnter2D 中，将调用 JoinConga 的代码替换为如下代码：

	Transform followTarget = congaLine.Count == 0 ? transform : congaLine[congaLine.Count-1];
	other.GetComponent<CatController>().JoinConga( followTarget, moveSpeed, turnSpeed );
	
看上去特别麻烦的第一行代码会算出哪一个对象是 conga 线中处于这只猫咪以前的对象。如果这条 conga 线是空的，就将僵尸的 Transform 分配给 followTarget。否则，就会使用 congaLine 中储存的最后一项。

接下来这行代码调用了 JoinConga，并且这一次将正在跟随的目标，僵尸的动作和转速度传递给它。

保存文件(File\Save)并转回Unity.

运行场景你会发现你的 conga 线已经在那儿了。看上去是井然有序的，但是实际上却不是这样的哦。

当你在运行这个场景的时候，你或许已经发现了如下的问题：

1. 当任何 conga 线上的猫咪跑到屏幕外面以后，它会被从场景中清除然后 Unity 会在 console 中打印出如下的异常（exception）：

![Alt text](http://cdn2.raywenderlich.com/wp-content/uploads/2015/04/exception_destroying_cats_in_conga.png)
 
2. 这条 conga 线的运动太过完美了。它流畅得像一条蛇，但是你在 CatConga 中定义的动画希望得到的是快乐跳舞中的僵尸猫咪，而不是滑动的“蛇猫”。你知道快乐跳舞中的僵尸猫咪是什么样子，难道不是吗？

3. 猫咪总会直接指向右边，不管它们到底是在忘哪个方向移动。僵尸猫咪看起来是一样的，但看起来像是彻头彻尾的幽灵。

这些问题碰巧被按照从易到难的顺序排序了。既然想要解决第一个问题看起来很简单，就让我们从这个问题开始吧。

回到 MonoDevelop 里面的 CatController.cs。

你已经加入了 isZombie 来追踪猫咪是否变成了僵尸猫咪。在 OnBecameInvisible 的开头加入如下这一行以避免对于已经变成僵尸的猫咪的删除：

	if ( !isZombie )

保存文件(File\Save)并转回Unity.

再一次运行这个场景，现在 conga 线上的猫咪可以自由且安全地离开屏幕，然后又跳着舞回到视野当中了

![Alt text](http://cdn5.raywenderlich.com/wp-content/uploads/2015/04/better_conga_line.gif)


###修正 conga 动画








  

	
	










	
