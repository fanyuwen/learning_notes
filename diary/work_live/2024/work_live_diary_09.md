# 2024 34岁 09-04 05:35 周三

已经好几天没记录了,恍恍惚惚已经是9月份了,中间没有经历什么让人印象深刻的事情,就是重复重复再重复,这几周的周末倒是变化了,因为之前买的多功能电磁炉,倒是自己买了很多肉,自己烤着吃,但可能因为自己在超市里买的那些食材自己都不熟,尤其是以为买的鱼之类的,到自己烤的时候才发现跟自己想的不一样,但是在那个火锅专业店里买了不少肉吃着也还行,也可能是自己烤肉的水平太差了,不知道这个肉是不是熟了,总之还有待加强吧,其它的就是正常地上班了,前天刚经历了一个简单的试用期转正的面聊,倒是栋哥还是挺走流程的,聊的也更多是之前工作上遇到的问题,然后就是被安排进一个新的项目里,项目倒是挺多的,目前还在熟悉吧,上周把一直纠结的《python面向对象编程》(第四版)买了,当当网上恢复原价了,看着京东上的价格更低,就狠下心买了,
看了几页前面还是很常规的介绍了一些面向对象的基础信息,后面没继续看了,因为考虑到工作和兼职的事情,确实要开始准备兼职的事情了,因为拖的实在是太久了,但是因为自己这个生活作息的不可控,身体调节的紊乱,导致现在每次下班回家都会特别的累,加上又爱看直播和小视频(这个现在在有意识地控制自己),所以总是事后懊恼自己为什么不花时间在学习上,而且也因为我自己的心态和从小到现在的思想观念也会影响我跟陌生人的交流,虽然现在看着都是借口,想着应该开始改变了,却始终迈不出那一步,倒是现在上班的时候跟舟菱聊的挺多的,她会聊她的孩子,以前上班的事情,过去上学的事情,还有*汤伟宏*就比较孩子气一点,倒是没什么心机,知道的还挺多,就是会时不时地埋怨,不过在吃这块还是挺有主意的,上周赋能平台有一个修改标签的功能确实给我干的快吐血了,
一个功能涉及的表还挺多的,一直到后面有一个表的`tkmybatis`的接口实现没有`xml`,估计是*王文振*懒的实现了,后面也是基于以前对`tkmybatis`的底层了解,准备自己写一个`provider`,按照自己的使用习惯来实现一个不是很复杂的自动方法sql实现,但也经过这次发现`tkmybatis`的实现确实不算是最优的,它加上`mybatis`本身的扫描注册,然后在`MapperFactoryBean`的实现里去`MapperHelper`实现自己扩展功能的处理,但是在处理的时候只会对父接口的实现进行处理,不会对当前接口进行处理,所以就会很尴尬,因为我动态的参数是可变的不是固定的,如果是只取父接口的实现的话就无法获取动态的参数,后来想到接口方法的重写,然后在子接口重写的方法上加上动态参数,想的是挺好的,结果实际跑的时候发现`mybatis`原生扫描的时候它指挥判断是不是标识了自己定义的注解,
没有标识注解的方法都会被忽略,所以就出现很奇怪的现象,子接口重写的方法上又要把之前的`mybatis`的注解重新标识一遍,就跟父接口一样,然后子接口的方法上再加上动态参数标识的注解,所以就觉得实现上怪怪地,昨天也是发现其实`tkmybatis`的实现里那个`MapperHelper`的实现是可以自定义的,然后注入到`mapper`的扫描器里,但是我还没试,因为新项目还需要准备时序图,加上最近被《红楼梦》吸引到了,会经常搜索红楼梦的信息,尤其是87版电视剧相关的信息,了解了很多以前对于电视剧里的人物和现实中的人物不知道的事情,也挺有感触的,主要是能把里面主要的人物关系给理清了,现在会经常听那部电视剧的主题曲,也是感慨万千,还有就是因为《黑神话:悟空》的爆火,也会关注《西游记》的各种细节,也是发现了很多自己不知道的,四大名著不愧为四大名著啊,
最近还迷恋上了看星际和玩星际,几乎是整个晚上都在玩,忘我了属于是,和一个专家难度对战2个疯狂难度,战术基本一样,rush加速机场出解放者,只是需要解决对面农名集体出来解救的问题,而且还有好几次面对人族速坦克、神族速不朽的情况也没想出好的办法,一般情况下如果队友要被灭了,是要去解救队友的,让队友存活也是保证自己发育的前提,但是也有队友也会rush,然后被对面另一家直接打爆的情况,那种情况我还是把我所有的空军主力派过去,以为能解救呢,结果全送了,导致最后我自己也被干了,所以电脑的策略是随机的,还是有不少战术是没法应对的,还需要锻炼,最后还是要学习,加油,努力

### 09-04 周三 21:01

这几天属实是被压力压到了,新安排的任务连业务都没熟悉就已经说延期了,然后代码的写法还是让我摸不着头脑的,昨晚就跟韬哥聊过的,然后今天下班前开了个会,了解了下进度情况,栋哥专门跑过来讲解了一下机台的操作情况,好在是讲的详细,算是理解了,把料、批次的关系给搞清楚了,但是后面跟栋哥聊现在的代码实现方案时栋哥给的理由说实在的有点牵强,总之我后面也没忍住去辩论了一下,但也没什么意思,还是得实现功能,但是后续还得涉及新的字段的存取方案和实时获取读取图片位置信息,确实还挺懵逼的,剩下的就是明天去考虑了,刚才又玩了几把星际争霸2,主要是看直播看的手痒痒,后面就收着了,得开始学习,看着手机上当当云阅读里一直收藏的《scala编程》(第5版)的试读,也觉得要学的东西确实是太多咯,现在掌握的信息也差不多了,就看明天的实际处理了,
感觉应该是要加班了,加油吧!

# 2024 34岁 09-06 00:33 周五
这周的周五了,时间过的真的好快啊,想想这周从周一开始准备那个新项目什么极耳翻折,然后就是看代码梳理逻辑,结果就说已经延期了,要昨天周四加班解决,结果周四早上我尽然睡过了,我都没意识到我第二个闹钟是怎么忽略过去的,反正就是睡不着,又不想醒,然后迷迷糊糊地感觉怎么闹钟还没想,起来一看已经8点36了,已经意识到迟到是必然的,也是不紧不慢地起床洗漱,然后出发去地铁,一路上虽然也注意着时间,但是也没那么在意,倒是*肖俊伟*在企业微信里问了我几个问题,我虽然在玩支付宝里的游戏,但是也即时回复了,顺带说明了其它问题,到公司已经快10点了,遇到*姜悦*还被她稍微吓到一丢丢,说只能请假不能补卡,我觉得应该是可以的吧,等今天上班再看吧,接下来就是跟俊伟沟通问题,解决问题然后理解逻辑,总之昨天这一天我就预感到要加班,结果一天下来调试也好,
后面的逻辑适配也罢,总之问题不少,主要是代码的实现格式实在是让人不知道流程该怎么流转,而且方法本身逻辑就很重,调用嵌套层级也多,最主要的是共享静态属性这个方式,真的是让人望代码兴叹啊,终于搞到8、9点,卡在了redis传图片字节序列的问题上,最后也是沟通逻辑改了代码才勉强能够通过,实在是太难了,好在栋哥在,不然应该是下不了班了,确实是太难了,万幸的是还能赶上末班地铁,在地铁上除了玩赛车,还在当当云阅读坚持看了《scala编程》(第5版)电子书试读,到家已经23点30了,差不多洗洗脚,就准备、准备整理技术文章和生活笔记,然后睡吧,但还是打开了该死的直播,看着直播星际,刚才又手痒玩了2把,已经快2点了,赶紧整理完睡觉吧,加油吧!一些关于自己性格和思想方面的感悟和体会就之后再去说吧!

# 2024 34岁 09-07 01:40 周六
熬到了周六的凌晨了,刚才看了会儿直播,有dota2主播看cs2比赛的,还有如火如荼地TI比赛,后面又看了会儿星际主播直播吃鸡的,打了2盘星际之后愈发觉得惭愧,就准备回来整理技术笔记的,结果看着业主群里的聊天记录,发现这边的业务对物业真的很不满意,问题很多,之前垃圾房的问题就吵了很久,我其实不是太关注这个,但是看见群里一直在吵,总觉得是问题很多,也把《scala编程》(第五版)试读版看完了,也不能说没收获吧,看完之后倒是挺想把原书买下来的,昨天一天本以为问题会没那么多,但是还是让我很捉急,只是对代码的了解更近了一步,前面推送`redis`消息的逻辑还是存在问题,又是调了半天,然后找韬哥帮忙,一直到下午接近下班的时候,没想到还是出问题了,虽然后来才知道是我推送的问题(只推了json,没有推图),但是接下来栋哥硬是要循环推多张图,
然后在推的过程中也是问题不断,什么相机、什么照片、什么阶段(stage),真的是给我整蒙了,最主要的还是代码层面,这种写法真的不出问题才怪啊,当然我也意识到了我的问题,总是在抱怨,让我想起了以前在雅座的时候,也会在罗老师旁边抱怨,我倒是看到栋哥跟罗老师相似的点,那就是专注于解决问题,停止抱怨,这是我要学习和纠正的地方,还有一个问题就是我要改变自己思考事务的逻辑,这个从小到大已经根深蒂固的思维确实让我固步自封,很难与人交流,甚至是跟家人,跟女生,我意识到我的问题了,我得放开,保持好心情,开开心心地生活和学习,今天我妈还找我了,我也不知道该如何面对她,得明天回她了,我就是觉得现在的我真的是羞于面对,人生、事业、爱情,我该何去何从,唯独加油!加油!再加油!

### 09-07 周六 13:55
起来没多久,从凌晨2点多睡到12点多,迷迷糊糊,感觉是没睡够,但是也不想睡了,起来上个厕所,望着外面的太阳有点大,本来微微想出去的心情瞬间没了,然后就躺在沙发上看了会儿电视,现在电视就是我B站视频的另一个观看的地方,现在主要推的就是喜剧、黑神话和海贼王相关的,看了会儿就想着回来得干点实事,把昨天没记录的技术笔记整理一下,然后得把目前为止整理的技术笔记也得整理一下,说实话`python`相关的确实有点多了,分类可以更细一点,循环听着`linkin park`的`Papercut`,整理好了,就开整吧,但是发现好像外面没太阳了,好像可以出去溜街了

# 2024 34岁 09-08 20:21
好吧,这个记录本来是凌晨的记录,不过也算了,毕竟并没有真实记录,倒是现在突然意识到要开始记录了,前天我妈在微信上问我关于国庆节来无锡的事情,我没好意思回她,刚才给我打了不少电话和微信视频,我都没敢回,说实话,我是诚惶诚恐,想告诉她实情,但是又怕这怕那的,我从小就这样了,我想改,我真的想改,而且还一度沉溺性中无法自拔,我根本无法控制自己,我就感觉玩游戏都属于是转移注意力的权宜之计,我想着要让自己变好,但是苦于没有方法,或者说没有勇气,或者说不敢面对,这原来是我一直缺失的,我想知道如何弥补,我想知道如何像我羡慕的那些人一样成为一个顶天立地的人,堂堂正正、光明正大地面对一切,不用撒谎和欺骗,我想直击我的灵魂深处,我想知道我最真实的自己,我想知道如何能拜托别人的眼光,我只是想坦坦荡荡、光明磊落地活在这个世界上,
说实话我觉得我大概率是得了抑郁症或者自闭症了,只是羞于面对,只是强忍强撑着,以前是因为不需要抛头露面,现在是到了我这个年纪需要去打交道,尤其是结婚、生活、还有以后的社交,都是我要去面对的,这次我妈找我,是因为我跟她说了我国庆节会回来,但是没想到我依然没有准备好,我依然无法去面对真实的自己和这个世界,包括这个世界里的每一个人,我想面对这一切,这是心理疾病,我知道,我要客服它,战胜它,我要给自己暗示,给自己鼓劲,给自己能量,我要成为我想成为的那种人,我要克服诱惑,我要战胜恐惧,我要成为自己,后面的努力和奋斗才会有意义,我不能被消极的思绪打乱和影响,不能因为自己的缺点和劣势而感到方徨和不安,要相信自己,像正常人和普通人那样去努力、去奋斗,所以,虽然我上面能打出这些字,但是并不代表我就能做到,说实话,是现在的我做不到,
但是我必须给自己保证,之后的我需要做到,点了个兰州拉面的外卖(土豆牛肉盖浇面),我要有担当和责任,要有积极阳光的心,要用于突破,用于超越,这些有点冠冕堂皇的话确实是我想做到的,我真的想成为那样的人,但是首先我得战胜我内心的恐惧,勇敢面对,你能做到吗?相信自己吗?