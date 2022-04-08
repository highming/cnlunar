# cnlunar
<h1>项目优点</h1>
1、不使用寿星通式[Y*D+C]-L，而使用香港天文台数据（阴阳合历，节气准农历日期才能准）

2、无数据库，依赖库少，运行快速，提供内容丰富

3、主要内容来自于《钦定协纪辨方书》，每一个神煞宜忌都有依据，遵循宜忌等第表，包含民用、御用事宜，且支持港式（通书配图）八字月柱算法-默认，通书原文文字农历月份算法，具体看demo.py

4、不盈利，开源免费，长期有人维护

5、民俗社会科学项目，不搞封建迷信，宜忌意义在于民间是将红白事合理分开，避免今日您宴请宾客，邻居办白事情况出现，引起邻里纠纷社会分裂。

特别鸣谢@DarkmoonRabbit(https://github.com/DarkmoonRabbit)

使用本开源项目的相关产品：

翻黄历 https://www.fanhuangli.com/

万年历-最新专业桌面老黄‪历 https://apps.apple.com/cn/app/id1555822960

```
$ pip3 install cnlunar
```
<h1>快速上手</h1>

```
import datetime
import cnlunar

# a = cnlunar.Lunar() # 为空为当前时间

a = cnlunar.Lunar(datetime.datetime(2019, 2, 4, 22, 30))
dic = {
    '日期': a.date,
    '农历数字': (a.lunarYear, a.lunarMonth, a.lunarDay, '闰' if a.isLunarLeapMonth else ''),
    '农历': '%s %s[%s]年 %s%s' % (a.lunarYearCn, a.year8Char, a.chineseYearZodiac, a.lunarMonthCn, a.lunarDayCn),
    '星期': a.weekDayCn,
    # 未增加除夕
    '今日节日': (a.get_legalHolidays(), a.get_otherHolidays(), a.get_otherLunarHolidays()),
    '八字': ' '.join([a.year8Char, a.month8Char, a.day8Char, a.twohour8Char]),
    '今日节气': a.todaySolarTerms,
    '下一节气': (a.nextSolarTerm, a.nextSolarTermDate, a.nextSolarTermYear),
    '今年节气表': a.thisYearSolarTermsDic,
    '季节': a.lunarSeason,
    '今日时辰': a.twohour8CharList,
    '时辰凶吉': a.get_twohourLuckyList(),
    '生肖冲煞': a.chineseZodiacClash,
    '星座': a.starZodiac,
    '星次': a.todayEastZodiac,
    '彭祖百忌': a.get_pengTaboo(),
    '彭祖百忌精简': a.get_pengTaboo(long=4, delimit='<br>'),
    '十二神': a.get_today12DayOfficer(),
    '廿八宿': a.get_the28Stars(),
    '今日三合': a.zodiacMark3List,
    '今日六合': a.zodiacMark6,
    '今日五行': a.get_today5Elements(),
    '纳音': a.get_nayin(),
    '九宫飞星': a.get_the9FlyStar(),
    '吉神方位': a.get_luckyGodsDirection(),
    '今日胎神': a.get_fetalGod(),
    '神煞宜忌': a.angelDemon,
    '今日吉神': a.goodGodName,
    '今日凶煞': a.badGodName,
    '宜': a.goodThing,
    '忌': a.badThing,
    '时辰经络': a.meridians
}
```

<h1>一、序言</h1>
    历法是基于人们生产活动需要，依据太阳、月亮等天象而制订计算时间的方法，根据月亮的周期变化所订立的历法称为阴历，根据太阳在不同季节的位置的周期变化所订立的历法称为阳历。
因为月亮的周期变化较短，观测方式较为简单，所以阴历在世界各地普遍比阳历更早出现，比如我国的上古六历：黄帝历、夏历、殷历、周历、鲁历、颛顼历，古希腊的希腊历等。大约公元前104年至84年，以农耕文化为主的中国西汉，汉武帝刘彻责成邓平、唐都、落下闳等人编写了《太初历》，中国农历经历了第一次重大改革，引入二十四节气正式成为阴阳合历，历法在中国也成为了一门较为独立的科学技术。与之同一时期，公元前46年，罗马统帅凯撒大帝（Gaius Julius Caesar）在希腊数学家兼天文学家索西琴尼的帮助下制订《儒略历》（Julian calendar）取代旧罗马历法，《儒略历》以回归年为基本单位，是一部纯粹的阳历，并在1582年发展成格里历延续至今，即目前在使用的公历。
南北朝时期数学家祖冲之在历法中引入了岁差，将朔望月长度定为29.5309日，将木星公转周期为11.858年（今测为11.862年），对农历进行了第二次改革。清朝汤若望（德国）推出《时宪历》，废除了圭表测影法，把全年分成二十四份，正式采用以太阳在黄道上位置为标准的定气，中国农历最后一次大改革结束并沿用至今。
由于计算机在我国起步较晚，阴阳合历计算方式更为复杂，需要掌握历法、数学、天文学等多学科知识，许多人对中国农历阴阳合历复杂算法不甚了解，在很长一段时间里，多数研发工程师在计算中国农历时都是用“通用寿星公式”[Y*D+C]-L进行节气计算。“通用寿星公式”属于经验公式，实际上是通过寻找一个拟合值C，来不断趋近实际的农历二十四节气，在计算结果不正确时，又套用了“某某年需要加1日，某某年需要减1日”等“特殊情况”校正值，存在农历算法不准情况，且“通用寿星公式”只输出二十四节气内容有限，无法满足用户对农历更为丰富的文化需求。
为符合Python解释型语言的本质，与跨平台脚本、快速开发应用的特性[2]，为传达精确计算及Python“胶水”语言的特性，让更多人方便使用，我们将设计一个依赖库少，农历算法清晰，二十四节气精准、功能丰富、基于Python的无数据库农历算法项目，并将其开源到Pypi（https://pypi.org/）上。

<h1>二、需求分析</h1>

<h2>（一）二十四节气寿星公式存在问题</h2>
二十四节气寿星公式：[Y×D+C]-L。属于经验公式，其中Y取年数的后2位数，D取经验值0.2422，L取Y/4，公历2000年的小寒、大寒、立春、雨水按照20世纪的C值来算，具体C值如图2-1所示：

图2-1 寿星公式查表值及特殊情况表

根据以上情况，经过分析及实际使用，我们发现寿星公式看似公式简单，但实际判断逻辑复杂，在1901-2000年、2000年前四个节气、2000-2100年存在不同的算法值，200年内存在了22个不同年份的矫正值，在使用过程中极容易出错。经过行业调查发现，许多项目都使用了寿星公式进行二十四节气计算，在输出错误的节气日期后，转而影响到了农历阳历部分包括八字月柱表达、每日宜忌等数据的错误。为此，我们希望使用Python设计并实现一个精准清晰、功能多样化的农历综合算法程序，弥补行业内关于农历历法算法及宜忌算法的空白。
<h2>（二）阴阳合历核心数据二十四节气数据计算方式</h2>

方案一，使用几何数学采用15%黄经夹角，计算日地椭圆轨道。在校验过数据与公式差异后，发现地球椭圆形轨道一年内公转速度差异超过7%，地球围绕著太阳作椭圆形公转，其自转轴并非垂直于公转面，而是倾斜约23度。从地球上观察，太阳相对遥远恒星的轨迹被称为“黄道”。概念上，节气是近乎均匀分布于“黄道”上24个位置的时刻。一个循环历时一年，更精确地说是一个“回归年”。[3]我们尝试使用平均值、开普勒第二定律计算行星轨道，计算值和香港天文台数据已经十分贴近了，但依然有差异。

图2-2 椭圆形的地球公转轨道及二十四节气位置[3]

差异主要来源于一下几个方面：（1）地球、月亮、太阳三个天体呈现无法完美预测的三体运动，甚至还包括木星引力影响（虽然引力抵消微弱）；（2）太阳本身也在运动，地球和太阳的质量也不是永恒不变的，这就导致地球的恒星年相对稳定，但回归年浮动振荡。
方案二，直接使用香港紫金山天文台《公历农历对照表》。香港天文台提供的天文授时服务，整理1901年至2100年的观测和预测数据得出《公历农历对照表》。历法是严谨的天文科学，脱离了天文观测的历法将失去其科学性，横跨两个世纪的数据足够覆盖多数场景，在科学严谨性与实用性之间找到最佳平衡。
为此，我们选择方案二作为定气数据。

<h2>（三）设计一个即科学且富有文化传承的历法程序</h2>

中国传统历法中除“授时”等天文科学部分外，还有大量的“择日”相关社会科学内容。传统文化中设计了很多“神”，“神”的设计并不全是为了封建迷信，比如故人称木星为“岁”，但根据长久的天象观察，发现木星的公转周期并不是完整的12年（今测为11.862年），于是古人们为了节约历法的计算量，便虚拟出一颗与木星轨道相反，周期为十二年的暗星，称“太岁”，即岁神。
中国传统社会有着丰富的社会活动，不同的社会活动赋予的文化含义不同，部分甚至相互对立，比如喜事与丧事。传统中国历法中设计了很多值班的“神”，不同“神”掌管不同的事务，合理地将这些事务归类，就避免邻里之间喜事丧事在同一天举行，极大地减少社会矛盾发生。然而，因社会活动复杂繁多，历法也设计出了极为复杂的算法，有的根据月份，有的根据日期，有的根据季节。
在提供严谨科学的农历服务的同时，我们希望对中国农历的“文化部分”在代码中进行一定程度的传承。除提供历法科学部分外，根据《四库全书 - 钦定协纪辨方书》的内容，我们额外增加黄历传统文化内容转换等，考虑到使用者的便利性，又另外增加西方历法部分功能。
综上，我们将功能解构为：
<h3>1．历法部分：</h3>
公历农历转换（数字表达）、二十四节气日期、季节等；
<h3>2．传统部分：</h3>
农历八字表达、每日宜忌、十二神、二十八星宿、纳音、七十二物候、时辰、星次、彭祖百忌、中医时辰经络、农历节日等；
<h3>3．补充部分：</h3>
星座、公历节日、常见西方节日等。

<h2>（四）设计一个容易使用的历法程序</h2>

2017年7月20日，国务院发布《新一代人工智能发展规划》，人工智能正式纳入国家发展战略，并且已经有数个省份将Python纳入到高考体系中。这说明，在未来很长一段时间内，会有越来越多学龄儿童使用到Python编程语言。为此我们应当设计一个能够轻易安装使用，没有额外附加环境条件的历法程序，我们希望它不包含SQL等数据库、不依赖第三方库只依赖Python基础库、可以使用“pip install”命令直接安装，包含一个用例让用户便捷使用。
综上，我们得出如下设计方案：
（1）将农历春节、闰月、二十四节气等数据使用十六进制存储、向量压缩保存在代码中；
（2）仅用到Python基础库；
（3）提供项目用例函数，且有默认值；
（4）将项目发布到Pypi开源平台；
（5）取一个简单容易记住的项目名称。


<h1>三、系统设计</h1>
<h2>（一）农历项目命名</h2>

中国农历英文直译为“Chinese lunar calendar”，在Pypi上遍历不同名称组合及占用情况后，我们选择Python官方推荐的“驼峰命名法”，将我们的程序命名为：“cnLunar”。
<h2>（二）目录结构设计</h2>

我们搭建了一个简易的框架结构来实现设计需求，如图3-1所示:
config.py作为配置扩展使用，用于实现无数据库存放农历、二十四节气、星座等配置信息；
demo.py作为项目用例，在研发过程中用于调试，程序设计完成后用作帮助用户快速上手的代码模板；
holidays.py用于存放公历、农历节日数据；
lunar.py作为核心模块，用于支撑农历历法部分、传统部分、补充部分数据计算输出，并且保留大量《钦定协纪辨方书》原文作为注释，在输出出现差异时矫正结果；
solar24.py用于将200年内的二十四节气数据解压缩，为核心模块提供函数；
tools.py用于存放基础的数据类型转换、数据清洗函数。


图3-1 项目结构图
<h2>（三）核心模块结构及算法设计</h2>

为减少开发者学习成本，我们将核心算法设计成一个类，对农历的所有数据转换只访问Lunar一个对象，对历法部分、常用传统部分数据在类初始化时生成，非常用传统部分数据、补充部分数据按需计算生成。这样，待项目发布后，用户只需要执行引入cnLunar，并调用Lunar对象初始化，就能在这个对象中获取到所有数据。
<h3>1．每日神煞脚本结构设计</h3>

为满足传承中国历法的“传统部分”中复杂的历法算法和逻辑关系的需求，我们将根据《钦定协纪辨方书》原文，将“神煞”看做一个对象，设计一种与古文文字描述结构相匹配的脚本，以便支持快速将古文历法算法，快速翻译成Python语言的简易脚本结构。如图3-2。

图3-2 《钦定协纪辨方书》影印版节选[4]

在阅读《钦定协纪辨方书》原文大量章节后，我们总结出以下规律：
（1）对象是有固定名称的；
（2）一个对象是有值班时间的，值班时间和阳历、阴历年月日都有关系，多数对象习惯按月与日的关系排班；
（3）书中的文字一般从正月开始排班，由于农历正月和农历正月八字表达方式“月柱”（农历八字表达方式的第三、四两字，以农历阳历二十四节气分割）有细微差异，农历正月并不完全等于八字正月；
（4）一个对象是没有绝对好坏的，会在宜从事部分事情的同时，不宜从事部分事情；
（5）不同的值班对象是有等级的，存在输出宜忌需要删减调整；
（6）不同时代的宜忌翻译用词不太一样，需要矫正。
根据对古文的抽象分析，我们设计如下脚本结构：
```
[(变量1：神煞名称，变量2：筛选条件，变量3：包含条件，变量4：命中后宜事集合，变量5：命中后忌事集合),……]
```
筛选规则尽量使用字符串或者有序列表合集，使用index指针取值，满足上述条件（3），支持传入指针值来源选择农历数字还是农历八字表达数字。执行规则为：判断变量2是否在变量3内。是，则变量1加入到今天的值神清单，变量4加入今日宜事清单，变量5加入今日忌事清单。否，下一步。
比如《钦定协纪辨方书》原文中：“《广圣历》曰：‘岁破者，太岁所冲之辰也。其地不可兴造、移徙，嫁娶、远行，犯者主损财物及害家长，惟战伐向之吉。’”与“《广圣历》曰:‘岁德者，甲德在甲,乙德在庚,丙德在丙,丁德在壬,戊德在戊,己德在甲，庚德在庚,辛德在丙,壬德在壬,癸德在戊。’”[4]这两句可以转化为以下伪代码脚本：
```
[('岁破',{今日地支编号} == ({今年地支编号} + 6) % 12,[True],[],['兴造','移徙','嫁娶','远行']),
('岁德','甲庚丙壬戊甲庚丙壬戊'[{今年天干编号}],{今日八字日柱},[],[])]
```
<h3>2．农历春节、闰月、二十四节气数据压缩</h3>

经过对香港天文台《公历与农历对照表》统计数据分析，如果对农历春节、闰月、二十四节气数据进行存储，可以节约计算量，为符合不引入数据库的设计需求，我们将设计二进制数据结构，存储到一个二进制文件中（调试时可以十六进制数的方式先存放于config.py配置结构）中，在需要使用时，使用位移运算符可高效地提取数据。

图3-3香港天文台 - 公历与农历对照表 - 1901年[5]

<h4>（1）春节数据</h4>
春节数据需要存储春节公历月和日数据，根据对1901-2100年春节日期变化数据统计，变化范围分别是1-3和1-31，对应二进制2位和5位，我们将月份数据放置于日期数据左侧，设计如下数据结构，1901年春节2月19日可存储为：
```
月份    日期
010    10011
```
转化为两位十六进制为：0x53 。
读取数据时，使用按位与运算符0x53 & 0x1f，获取春节日期为19日，使用位移运算符、按位与运算符(0x53 >> 5) & 0x3可得月份2月。
<h4>（2）闰月及农历月份数据</h4>

运用同样的方法，我们可以将1901年农历全年月份大小月数据（小，大，小，小，大，小，大，小，大，大，大，大，小），以大月30天为1，小月29天为0，即可逆序表达为二进制011101010010。闰月月份变化范围为1-12，闰月和农历其他月份一样，也是有大小月区分，所以以1901年为例，全年数据可储存为：
```
闰月月份	闰月大小月		全年农历大小月（腊月.冬月.十月...正月）
0000		    0			    011101010010
```
转化为十六进制为：0x752 。
<h4>（3）二十四节气数据</h4>

二十四节气日期一共有四十八个数据，由于二十四节气是农历中阳历部分，划分后每个公历月份都会平均分到两个节气，我们就可以只保存日期数据，用数据在列表中的指针顺序整除以2再加1，即可求得月份。比如：1901年全年二十四节气只要保存为列表为：
```
[6,21, 4,19, 5,21, 6,22, 6,22, 8,23, 8,24, 8,24, 9,24, 8,23, 8,22]
```
继承农历月份数据将小月大月天数[29，30]，转换成[0,1]二进制存储的数据压缩方式，经过对1901-2100年二十四节气数据分析，我们可求得一条最小公差向量：
```
[4,19, 3,18, 4,19, 4,19, 4,20, 4,20, 6,22, 6,22, 6,22, 7,22, 6,21, 6,21]
```
将全年二十四节气数据-最小公差向量可得：
```
[2,2,  1,1,  1,2,  2,3,  2,2,  2,1,  2,2,  2,2,  2,2,  2,2  2,1]
```
可按月份逆序储存为二进制：
```
（12月.11月.10月...1月）
0110  1010  1010  1010  1010  0110  1010  1010  1001  1010  0101 1010
```
转化为十六进制为：0x6aaaa6aa9a5a 。


<h1>四、系统实现</h1>
<h2>（一）开发工具选择</h2>
我们选择PyCharm CE作为开发工具。PyCharm是最受欢迎的Python开发IDE（Integrated Development Environment，集成开发环境）之一，官方提供Windows、macOS、Linux不同系统版本及芯片支持，提供Professional专业版和Community社区版，其中社区版开源且免费。
在IDE的帮助下，可以更方便地帮助我们在系统开发过程中提高效率、调试错误、保持规范化等。
<h2>（二）面向对象开发</h2>

考虑到农历在不同场景下的不同应用，我们选择将核心模块作为一个强大的对象进行开发，方便后续开发者根据实际场景需要，选取自己所需的数据，或在进行二次开发时，直接继承该对象。
我们将对象命名为Lunar，选用Python基础库datetime作为公历数据输入对象，提供一个额外参数godType，让使用者可以选择以农历月份，还是八字月柱确定宜忌，并提供默认值。具体结构如下：
```
from datetime import datetime
class Lunar:
def __init__(self, date=datetime.now(), godType='8char'):
    	self.godType = godType
    	self.date = date
```
我们希望用户在使用时，只需要引入我们的项目和Python基础库datetime，就可以根据需要，通过对象的方法和实例变量即可获取相应的数据：
```
import datetime, cnlunar
a = cnlunar.Lunar(datetime.datetime(2022, 4, 7, 10, 30)
print(a.lunarYear, a.lunarMonth, a.lunarDay)
```
<h2>（三）命名规则</h2>
由于项目中含有大量的中国农历历法传统文化名称，而Python编码不像易语言一样支持中文作为变量名称，在“驼峰命名法”的基础上，我们要需要为项目定义一套便于理解的变量名称命名规则。
有英文翻译的使用英文或英文缩写，一些特定名称暂时无法翻译的，使用拼音，比如纳音允许使用拼音nayin；
对于名称包含数字的直接使用阿拉伯数字，如果数字位于开头则使用the，作为前缀，比如：Lunar().the28Stars代表二十八星宿；
对于包含关联关系的，使用相同的前缀，比如：Lunar().goodGodName代表今日吉神，而Lunar().goodThing代表今日宜事；
对有时间或前后关系的，使用today、next等作为前缀，比如：Lunar().todaySolarTerm代表查询今日节气，Lunar().nextSolarTerm代表下一个节气名称；
对于输出数据与其他格式有显著不同，则在实例或方法后面添加输出类型后缀，比如：Lunar().thisYearSolarTermsDic代表获取今年全年二十四节气日期的字典。
此外，在项目中我们对比较明确历法算法的使用get作为前缀，对于传统部分、补充部分中，一些可能涉及封建迷信部分，比如阴阳五行；或数据尚不完全，比如西洋节日等，我们暂时使用get_作为前缀，以期后续开发迭代中逐步完善。
<h2>（四）项目仓库选择及开源模式选择</h2>
选择全球最大的开源平台Github.com作为代码仓库，使用Git对项目进行代码管理、项目分支管理，为后续系统测试提供灰测、众测用户获取渠道。
选择GPL3.0协议作为开源协议，其核心是第三方若使用本项目进行二次开发，则不可闭源使用，新增的代码依然继承使用GPL3.0协议，即强制二次开发后依然需要开源。
该项目选用Python作为主要开发语言，不同用户可以使用PHP、Java、C#.net等语言混合使用，但只要不变更项目内代码，则不用开源自己项目部分。选择GPL3.0协议可以在开源的同时，最大限度约束二次开发者帮助并共同优化原始项目。
<h2>（五）无数据库农历算法实现</h2>
<h3>1．配置模块实现</h3>
在开发过程中，我们将一些常量从项目中逐步移出到配置文件config.py，并添加适当的注释描述。这样有助于项目代码的“解耦”，方便在开发过程中调试，对后续其他开发者在对项目二次开发时也较为友好。部分配置代码如下：
```
MONTH_DAY_BIT = 12
LEAPMONTH_NUM_BIT = 13
# 1901-2100年二十节气最小数序列 向量压缩法
ENC_VECTOR_LIST = [4,19,3,18,4,19,4,19,4,20,4,20,6,22,6,22,6,22,7,22,6,21,6,21]
# 1901-2100年二十节气数据 每个元素的存储格式如下：
# https://www.hko.gov.hk/sc/gts/time/conversion.htm
# 香港天文台公布二十四节气按年存储16进制，1个16进制为4个2进制
SOLAR_TERMS_DATA_LIST = [
 0x6aaaa6aa9a5a,0xaaaaaabaaa6a,0xaaabbabbafaa,0x5aa665a65aab,0x6aaaa6aa9a5a, # 1901 ~ 1905
0xaaaaaaaaaa6a,0xaaabbabbafaa,0x5aa665a65aab,0x6aaaa6aa9a5a,0xaaaaaaaaaa6a,
    ……
]
```
在其他目录结构中，使用from cnlunar.config import *，来引入常量配置，比如在对农历闰月数据获取中，我们通过这样的代码组合实现读取压缩数据：
```
from cnlunar.config import *
tmp = lunarMonthData[self.lunarYear - START_YEAR]
leapMonth = (tmp >> LEAPMONTH_NUM_BIT) & 0xf
```
<h3>2．核心模块实现</h3>
在开始开发核心模块历法部分功能后，我们使用之前设计的脚本结构，对《钦定协纪辨方书》卷九至卷十一关于每日宜忌部分的古文进行了大规模的逻辑算法翻译，最终使用超过700行代码、4万个字符的结构化脚本，还原古书中复杂的逻辑关系，完成了Lunar().get_AngelDemon()的开发，实现对《钦定协纪辨方书》中描述的农历每日宜忌规则进行了逻辑翻译。

图4-1使用脚本结构翻译《钦定协纪辨方书》卷九至卷十一内容



<h1>附录</h1>

根据《四库全书 - 协纪辨方书》添加了部分的吉神凶煞、八字，修正了部分八字月柱算法，关于值神方面的算法没有采用农历月算，而是根据农历八字月柱为依据，不同算法之间有细微区别。主要参考了老黄历、聚宝堂日历等，待持续优化
铺注条例：
凡铺注《万年历》、《通书》，先依用事次第察其所宜忌之日，于某日下注宜某事，某日下注忌某事，次按宜忌，较量其凶吉之轻重，以定去取。
凡宜宣政事，布政事之日，只注宜宣政事。
凡宜营建宫室、修宫室之日，只注宜营建宫室。

凡吉足胜凶，从宜不从忌者，如遇德犹忌之事，则仍注忌。

凡吉凶相抵，不注宜亦不注忌者，如遇德犹忌之事，则仍注忌。

凡德合、赦愿、月恩、四相、时德等日，不注忌进人口、安床、经络、酝酿、开市、立券、交易、纳财、开仓库、出货财。如遇德犹忌，及从忌不从宜之日，则仍注忌。
凡天狗寅日忌祭祀，不注宜求福、祈嗣。

凡卯日忌穿井，不注宜开渠。壬日忌开渠，不注宜穿井。
凡巳日忌出行，不注宜出师、遣使。
凡酉日忌宴会，亦不注宜庆赐、赏贺。
凡丁日忌剃头，亦不注宜整容。

凡吉凶相抵，不注忌祈福，亦不注忌求嗣。

凡忌诏命公卿、招贤，不注宜施恩、封拜、举正直、袭爵受封。
凡忌施恩、封拜、举正直、袭爵受封，亦不注宜诏命公卿、招贤。
凡宜宣政事之日遇往亡则改宣为布。
凡月厌忌行幸、上官，不注宜颁诏、施恩封拜、诏命公卿、招贤、举正直。遇宜宣政事之日，则改宣为布。

凡吉凶相抵，不注忌结婚姻，亦不注忌冠带、纳采问名、嫁娶、进人口，如遇德犹忌之日则仍注忌。
凡吉凶相抵，不注忌嫁娶，亦不注忌冠带、结婚姻、纳采问名、进人口、搬移、安床，如遇德犹忌之日，则仍注忌。遇不将而不注忌嫁娶者，亦仍注忌。遇亥日、厌对、八专、四忌、四穷而仍注忌嫁娶者，只注所忌之事，其不忌者仍不注忌。
凡吉凶相抵，不注忌搬移，亦不注忌安床。不注忌安床，亦不注忌搬移。如遇德犹忌之日，则仍注忌。
凡吉凶相抵，不注忌解除，亦不注忌整容、剃头、整手足甲。如遇德犹忌之日，则仍注忌。
凡吉凶相抵，不注忌修造动土、竖柱上梁，亦不注忌修宫室、缮城郭、筑提防、修仓库、鼓铸、苫盖、修置产室、开渠穿井、安碓硙、补垣塞穴、修饰垣墙、平治道涂、破屋坏垣。如遇德犹忌之日，则仍注忌。
凡吉凶相抵，不注忌开市，亦不注忌立券、交易、纳财。不注忌纳财，亦不注忌开市、立券、交易。不注忌立券、交易，亦不注忌开市、纳财。
凡吉凶相抵，不注忌开市、立券、交易，亦不注忌开仓库、出货财。如遇专忌之日，则仍注忌。
凡吉凶相抵，不注忌牧养，亦不注忌纳畜。不注忌纳畜，亦不注忌牧养。
凡吉凶相抵，有宜安葬不注忌启攒，有宜启攒不注忌安葬。

凡土府、土符、地囊，只注忌补垣，亦不注宜塞穴。
凡开日，不注宜破土、安葬、启攒，亦不注忌。遇忌则注。
凡四忌、四穷只忌安葬。如遇鸣吠、鸣吠对亦不注宜破土、启攒。
凡天吏、大时不以死败论者，遇四废、岁薄、逐阵仍以死败论。
凡岁薄、逐阵日所宜事，照月厌所忌删去，所忌依然依从本日。

凡二月甲戌、四月丙申、六月甲午、七月戊申、八月庚辰、九月辛卯、十月甲子、十二月甲子、德合与赦（天赦）、愿（天愿）所会之辰，诸事不忌。

5、星次
星纪、玄枵、娵訾、降娄、大梁、实沈、鹑首、鹑火、鹑尾、寿星、大火、析木