title: 
date: 
categories: doc
---

  [首页](../home/index.html) >[文档](documentation.html) > **医护人员入门指南**	

## 1.8.2 FHIR Overview - Clinicians

FHIR (Fast Health Interoperability Resources)旨在实现医疗健康信息的交换。其中包括了临床数据和健康相关的行政管理数据、公共卫生和临床研究数据。
既包括了人类和兽医，又适合大多数应用场景——住院、慢病管理、社区护理、健康联盟等。

FHIR 是一个以软件开发人员为对象的标准规范。真正使用 FHIR 来构建各种信息系统的是软件开发人员。FHIR 标准规范并没有想去定义最佳的临床实践，或为系统界面、工作流程提供建议。
此类指南是很有帮助的，但是不在 FHIR 的范围之内。

由于 FHIR 是紧紧围绕着开发实现的，该标准规范的很多方面都是为了解决医疗信息系统之间数据交换的技术问题。这部分内容介绍了FHIR 中能够提供哪些内容，跳过了一些技术的细节，
试图强调 FHIR 中医护人员可能会有所兴趣的内容。但如果你想了解更多技术细节，请自行了解。

### 1.8.2.1  资源 Resources

从临床角度来看，FHIR 标准最核心的部分是理解资源的概念。把资源看作是表示所要采集和共享的不同类型医疗信息、管理信息的“form”(这里的form既可以是信息系统或纸质的各种表单，
    也可以是为各种不同类型信息所定义的通用结构)。 FHIR 标准定义了一个每种类型
医疗信息的一个通用结构-比如过敏、处方、转诊申请等等。

FHIR 数据，也就是存储这些资源实例的各种库。资源实例描述了患者信息(人口学信息、疾病症状、手术操作等)、管理类信息(医护人员、组织机构、地理位置)。一些资源是用来支持信息交换的技术类基础架构组件——描述系统能做些什么，定义允许值集等等。
FHIR 存储库可以是 EHR系统、药房系统、HIS 系统等。诸如决策支持系统等其他系统，即使并没有存在任何信息，也可以暴露一些 FHIR 接口。

### 1.8.2.2  Building useful Clinical Systems out of Molecules

每个资源都包含了很少量的高度专业的数据。单个资源并不能表达什么内容，但很多很小的资源聚集起来就构成了一份有用的临床记录。信息系统将用户的行为对应到相应的资源中去。

资源之间的关联构成了信息的网络，能够表示一份健康记录。比如，处方资源实例可能会引用下达该处方的医护人员，处方所属的患者和处方中所包含的药物。每个资源都是单独来维护的。
但是，FHIR 接口使得能够获取所有这些关联，并将碎片化的信息组装成一个用户的完整视图。

### 1.8.2.3  Extensibility and Profiling

FHIR 中的 form 都是通用型的。必须能够在不同的国家、不同的医生不同的场景(人医、兽医、公共卫生、研究等)下可以使用。FHIR 认识到在医疗领域一刀切的方法是行不通的，
必须提供一种能力——添加额外的扩展或强化约束条件，能够根据不同实现中的需求来调整form。比如要支持对受限药品的追踪，需要在“处方”中添加扩展元素，同时也要约束所使用的药品编码。
因此，即使其中包含了任何扩展，或者说接收的系统用不到其中的一些扩展，任何系统也要能够使用完整的form。

 For example, a "prescription" form might have extension elements added to support tracking of restricted medications while also constraining what codes can be used to communicate types of drugs to a particular national standard. However, the forms are designed in such a way that these changes can be made without changing how systems pass forms around. Thus any system can consume completed forms even if they have "extra" elements added - and even if those particular extra elements aren't used by the receiving system.

为了避免大家都在使用的最基本的form过于复杂，FHIR中定了这么一条规则：大多数情况下，一个资源只会包含哪些大多数实现中会使用的数据项。并不是说此类数据项必须总是存在。
比如，现实中大多数系统能够记录患者的死亡日期，即使在大多数病历中该字段值为空。另一方面，很少的系统能够记录发色，所以base form中不存在发色这个数据项，需要该数据项的系统需要定义一个扩展来记录此项。

为了使得资源的数目不会太多，其中一些资源的范围是很宽泛的。[Observation](observation.html) 可以用作生命体征、检验结果、心理学鉴定结果等很多东西。要限制它的范围，比
如说该怎么表示血压呢? FHIR 中可以通过对 base form 的profile来实现。首先应该达成共识，应该采集哪些临床信息，细化程度如何，后续FHIR会发布一些用于辅助医生
直接构建 profile的工具，但现在还处于早期阶段。有兴趣的可以在David Hay的博客上看看相关文章[clinfhir](http://fhirblog.com/2015/07/26/using-clinfhir-to-create-a-profile/)
上次在厦门CHIMA的研讨会上他曾经示范过。

### 1.8.2.4  Narrative

信息系统间使用FHIR 来共享数据的时候要能够支持语义互操作性-决策支持、规则的触发、趋势分析等。然而并非所有系统都是一样的，都能够识别这样的数据。在数据交换的过程中，即使系统
只能采集少量的结构化数据也是很有价值的。鉴于此， FHIR 资源不仅仅支持结构化的数据，也支持非结构的人可读的部分，即使没有结构化的数据，医生也能获取信息。

大多数资源实例中应该是存在 Narrative 的部分的，极少数情况下可以忽略这部分内容。一些情况下，可以从结构化数据中生成 Narrative的部分。比如，患者信息的narrative部分如下所示：

```html
** Peter James Chalmers (OFFICIAL), Jim**  
 **身份标识**: MRN = 12345 (USUAL)  
 **联系方式**: ph: (03) 5555 6473(WORK)  
 **性别**: MALE  
 **出生日期**: Dec 25, 1974  
 **死亡标志**: false  
 **居住地址-家庭地址**: 534 Erewhon St PleasantVille Vic 3999 (HOME)

```

在一些情况下，narrative部分可能是由医生直接生成的。比如出院申请书、病理报告等。 其中一部分narrative数据可能会转化成结构化数据。

### 1.8.2.5  Interfaces

除了定义数据交换时用到的“form”，fhir中还定义了系统交互信息时会使用的各种接口。FHIR 中支持四大类主要的数据交换模式：REST、文档方式、消息方式和服务。

#### 1.8.2.5.1  REST

REST是其中一种最简单的模式。接着前面“form”的比喻，可以把 restful 服务器看作是放满文件柜的一所大房子。在这个房子里，每个文件柜用来存放某种类型的form。文件柜包含了不同的文件夹，每个文件夹有个唯一编号，每个文件夹表示一个唯一的实际存在的事物，
比如一个患者、一次就诊、一种药物等。每个文件夹中可以包含多个文件，每个文件表示其中一个版本。每次出现病历内容的更新，就在文件夹最顶层增加一份文件。要想了解整个变更的历史情
况，只需要翻一下文件夹即可。

想象工作人员站在门口。你可以递给工作人员一个申请来处理文件夹中的信息。工作人员和处理请求就构成了整个RESTFUL API。有了这样的API，你可以完成如下：

*   _search_: 查询文件夹中满足一系列查询条件的文件
*   _read_: 拿到某个文件柜中某个文件夹中的一份文件
*   _create_: 在某个文件柜中增加一个文件夹
*   _update_: 在某个文件夹中增加一个新的文件
*   _delete_: 从文件柜中拿掉一个文件夹 (或者贴个“请勿打开”的条子)
*   _history_: 在某个文件夹中查看所有的文件 (或者是某个文件夹、某个文件柜乃至整个房间的所有文件)
*   _transaction_: 批量提交给服务器很多文件夹来处理

EHR 和其他系统可能会使用更加复杂的界面，但其背后都是类似与文件管理人员类似的请求操作。

#### 1.8.2.5.2  Documents

文档是医疗领域数据共享很常见的方式。无论是否需要告知信息的使用者如何使用，文档都是很有作用的，在未来也要能够可靠的获取之前"冷冻"起来的信息。
文档包括了诸如出院摘要和检验报告等。

在FHIR 中有个特殊的资源叫[Composition](composition.html)，可以将其看做文档的封面。其中确定了文档的标题、作者、日期以及患者、内容。一份 FHIR 文档可以看做是
装订在一起的一堆sheet，包含了一个封面。可以单独存储或传输，一次性表达一组信息。

#### 1.8.2.5.3  Messaging

大多数医疗信息的交换是通过消息模式来实现的。在消息模式中，是通过一个明确的“做什么事”的请求来从一个系统向另一个系统发送一组信息的。
一个消息可能要求执行某个检验医嘱、合并2个患者记录或是告知系统患者已经转床。消息与文档收集资源的方式很类似。然而，在消息中，封面页是[MessageHeader](messageheader.html) 
不像文档那样是订书机装订起来的，消息中的资源更像是回形针别起来的。不期望消息的接收系统会按照数据原来的样子进行存储。

#### 1.8.2.5.4  Services

服务可以看做是轻量级的消息。与其说是完整的封面，而只是在资源的上面贴一个小纸片。有时候，不是发送完整的文件，而是将相应的片段切分开来，发送一个个片段。服务的响应
是与订书机订起来的纸本类似的资源实例的bundle。服务可能被用在决策支持中，比如“病人Y的处方药X是否有问题？”，“患有A,B,C疾病的患者最佳的治疗方案是什么？”

### 1.8.2.6  Approaching the specification

基于FHIR 的系统功能是由它所支持的资源来定义的。从临床的角度来看，以下内容定义了一份临床记录：

*   定义的资源类型
*   数据内容，使用何种术语的业务规则
*   资源之间如何关联
*   如何检索信息

资源定义的章节中可以找到上述四类信息。可以在 [Clinical](clinical.html) and [Administrative](administration.html) 章节中可以找到感兴趣的资源。
如何解读资源内容中的解释可以在 [这里查看](formats.html).逻辑表和UML 可能是最容易理解的。同时，不要忘记查看示例。知道如何使用元素来表达真实数据比看定义来的更有帮助。同时，
查看profile了解在特殊的应用场景中如何对资源进行约束。

由于我们不断地在对 FHIR 标准规范进行重构，欢迎医生和其他专业人员的反馈。在每个资源的页面上有一个链接，指向负责该资源内容的工作组的首页。


&copy;  HL7.org 2011+. FHIR DSTU (v0.5.0-6321) generated on Sun, Aug 16, 2015 16:59+0000\.   

  链接：[试行版是什么](http://hl7.org/implement/standards/fhir/dstu.html) |[版本更新情况](http://hl7.org/implement/standards/fhir/history.html) | [许可协议](http://hl7.org/implement/standards/fhir/license.html) |[提交变更建议](http://gforge.hl7.org/gf/project/fhir/tracker/?action=TrackerItemAdd&tracker_id=677)

