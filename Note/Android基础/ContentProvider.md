ContentProvider一般为存储和获取数据提供统一的接口，可以在不同的应用程序之间共享数据。
之所以使用ContentProvider，主要有以下几个理由：
1，ContentProvider提供了对底层数据存储方式的抽象。比如下图中，底层使用了SQLite数据库，在用了ContentProvider封装后，即使你把数据库换成MongoDB，也不会对上层数据使用层代码产生影响。
2，Android框架中的一些类需要ContentProvider类型数据。如果你想让你的数据可以使用在如SyncAdapter, Loader, CursorAdapter等类上，那么你就需要为你的数据做一层ContentProvider封装。
3，第三个原因也是最主要的原因，是ContentProvider为应用间的数据交互提供了一个安全的环境。它准许你把自己的应用数据根据需求开放给其他应用进行增、删、改、查，而不用担心直接开放数据库权限而带来的安全问题。
我们知道了ContentProvider是对数据层的封装后，那么大家可能会问我们要如何对ContentProvider进行增，删，改，查的操作呢？下面我们来介绍一个新的类ContentResolver，我们可以通过它，来对不同的ContentProvider进行操作。
ContentResolver
有些人可能会疑惑，为什么我们不直接访问Provider，而是又在上面加了一层ContentResolver来进行对其的操作，这样岂不是更复杂了吗？其实不然，大家要知道一台手机中可不是只有一个Provider内容，它可能安装了很多含有Provider的应用，比如联系人应用，日历应用，字典应用等等。有如此多的Provider，如果你开发一款应用要使用其中多个，如果让你去了解每个ContentProvider的不同实现，岂不是要头都大了。所以Android为我们提供了ContentResolver来统一管理与不同ContentProvider间的操作。

在Context.java的源码中有一段
/** Return a ContentResolver instance for your application's package. */
 public abstract ContentResolver getContentResolver();
所以我们可以通过在所有继承Context的类中通过调用getContentResolver()来获得ContentResolver。
可能又有童鞋会问，那ContentResolver是如何来区别不同的ContentProvider的呢？这就涉及到URI（Uniform Resource Identifier）问题，对URI是什么还不明白的童鞋请自行Google。
ContentProvider中的URI
ContentProvider中的URI有固定格式，如下图：
Authority：授权信息，用以区别不同的ContentProvider；
Path：表名，用以区分ContentProvider中不同的数据表；
Id：Id号，用以区别表中的不同数据；
从上面代码我们可以看到，我们创建了一个
content://me.pengtao.contentprovidertest/test的uri，并且开了一个静态方法，用以在有新数据产生时根据id生成新的uri。下面介绍下如何把此uri映射到数据库表中。
