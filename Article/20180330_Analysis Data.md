title: Analysis Data

date: 2018年 3月30日 星期五 09时55分45秒 CST

tags: 

---

# 简介
一般做应用时，往往我们需要去一些数据，当我们从某些网站上面爬到数据后，那么我们需要写代码对数据进行清洗，变成我们的应用数据，这过程有时会是恶心的，不过我们却不能不做，否则你就自己Ctrl+C，Ctrl+V生吧。。。。。


我从某Wiki上爬到数据，由于该网站数据奇葩，理论上数据应该是数组结构的，但它却是元素平铺，由于篇幅，我只放部分数据：

# 数据

```json
1.风(The Windy)
现在: 好像无意间将采取跟自己所想的正相反的态度，看来不诚意不行哦。
障碍: 会出现爱情的妨碍者，要象樱似的，光明正大地将其拨掉。
未来: 将有新风味来，看来你将会舍去现在喜欢的人，而另有所爱。
对策: 不要太紧张，只要你大胆地看着对方眼睛说话，就会意外地镇静。
幸运项目: 大一点的书，明朗的绿色服饰，团扇。
2.影(The Shadow)
现在: 是有点令人担忧的关系。即使隐藏，也总有一日暴露给大家。
障碍: 看来象有一堵高 ，不过别滞气，总有找到解决方法的。
对策: 暂时没有信心时，要看着自己的影子，连说三次"魔鬼走开"，就会产生新的精神。
幸运项目: 建筑物的背阳地，带洞黑色的上衣，摄像机。
3.幻(The Illusion)
现在: 看不到对方的真实面甩，或许只以能看到你所希望的"幻"。
障碍: 是否只能浮现理想或空想，整日迷迷糊糊。
未来: 如果不紧紧盯住真实面目，对双方都没有好处。
对策: 在心神不定，无所适从时，只要在对方的照片背面写上"^^"便可。
幸运项目: 万花筒、展望台、玻璃球。
4.花(The Flower)
现在: 一切进展顺利，正处热恋之中。
障碍: 两人间毫无障碍，肯定有花在为您祝福着。
未来: 这对情侣，一定会像樱之家一样，建成温暖的家庭。
对策: 一定不能生气，只要总以笑脸相持的话，肯定不错。
幸运项目: 跳舞、学校的活动，粉红色的小物品。
```

# 需求

1. 将data.txt这份数据转化为Divination相应的模型数据
2. 风为中文名字
3. The Windy为英文名字
4. 再者现在、障碍、未来、对策、幸运项目为可有可无
5. 目标数据


```
[
  {
    "name" : "风",
    "now" : "好像无意间将采取跟自己所想的正相反的态度，看来不诚意不行哦。",
    "luckyThing" : "大一点的书，明朗的绿色服饰，团扇。",
    "eName" : "The Windy",
    "obstacle" : "会出现爱情的妨碍者，要象樱似的，光明正大地将其拨掉。",
    "countermeasure" : "不要太紧张，只要你大胆地看着对方眼睛说话，就会意外地镇静。",
    "future" : "将有新风味来，看来你将会舍去现在喜欢的人，而另有所爱。"
  },
  {
    "name" : "影",
    "obstacle" : "看来象有一堵高 ，不过别滞气，总有找到解决方法的。",
    "countermeasure" : "暂时没有信心时，要看着自己的影子，连说三次\"魔鬼走开\"，就会产生新的精神。",
    "now" : "是有点令人担忧的关系。即使隐藏，也总有一日暴露给大家。",
    "luckyThing" : "建筑物的背阳地，带洞黑色的上衣，摄像机。",
    "eName" : "The Shadow"
  },
  {
    "name" : "幻",
    "now" : "看不到对方的真实面甩，或许只以能看到你所希望的\"幻\"。",
    "luckyThing" : "万花筒、展望台、玻璃球。",
    "eName" : "The Illusion",
    "obstacle" : "是否只能浮现理想或空想，整日迷迷糊糊。",
    "countermeasure" : "在心神不定，无所适从时，只要在对方的照片背面写上\"^^\"便可。",
    "future" : "如果不紧紧盯住真实面目，对双方都没有好处。"
  },
  {
    "name" : "花",
    "now" : "一切进展顺利，正处热恋之中。",
    "luckyThing" : "跳舞、学校的活动，粉红色的小物品。",
    "eName" : "The Flower",
    "obstacle" : "两人间毫无障碍，肯定有花在为您祝福着。",
    "countermeasure" : "一定不能生气，只要总以笑脸相持的话，肯定不错。",
    "future" : "这对情侣，一定会像樱之家一样，建成温暖的家庭。"
  }
]
```

# 过程

看到这份数据的开始，我是懵B的，虽然数据看着很整齐，感觉无从下手，只能心理暗骂着，他大爷的，怎么不提供一份JSON，既然没有，那么我们就自己造一份吧。

首先数据无从下手，那么我们就把他变得有从下手吧，使用**replacingOccurrences**来清理一下数据吧 ！

```
 //原数据
 1.风(The Windy)
 现在: 好像无意间将采取跟自己所想的正相反的态度，看来不诚意不行哦。
 障碍: 会出现爱情的妨碍者，要象樱似的，光明正大地将其拨掉。
 未来: 将有新风味来，看来你将会舍去现在喜欢的人，而另有所爱。
 对策: 不要太紧张，只要你大胆地看着对方眼睛说话，就会意外地镇静。
 幸运项目: 大一点的书，明朗的绿色服饰，团扇。
 
 //清理过后的数据
 1.风:The Windy
 现在:好像无意间将采取跟自己所想的正相反的态度，看来不诚意不行哦。
 障碍:会出现爱情的妨碍者，要象樱似的，光明正大地将其拨掉。
 未来:将有新风味来，看来你将会舍去现在喜欢的人，而另有所爱。
 对策:不要太紧张，只要你大胆地看着对方眼睛说话，就会意外地镇静。
 幸运项目:大一点的书，明朗的绿色服饰，团扇。                           
```

首先，可以借助于**txtStr.components(separatedBy: ".")**，对数据进行分组其实是数据，为了表现得直观点，我将转成后数据展示得下面这样子：

```
1

风:The Windy
现在:好像无意间将采取跟自己所想的正相反的态度，看来不诚意不行哦。
障碍:会出现爱情的妨碍者，要象樱似的，光明正大地将其拨掉。
未来:将有新风味来，看来你将会舍去现在喜欢的人，而另有所爱。
对策:不要太紧张，只要你大胆地看着对方眼睛说话，就会意外地镇静。
幸运项目: 大一点的书，明朗的绿色服饰，团扇。
2

影:The Shadow
现在:是有点令人担忧的关系。即使隐藏，也总有一日暴露给大家。
障碍:看来象有一堵高 ，不过别滞气，总有找到解决方法的。
对策:暂时没有信心时，要看着自己的影子，连说三次"魔鬼走开"，就会产生新的精神。
幸运项目:建筑物的背阳地，带洞黑色的上衣，摄像机。
3

```

从数据我们能发现，该数组的第一项是没用的，可以切割掉，再者每个元素除了数据最后一项外，其他每项的最后一行都是没用的，那么我们把它处理掉。


```Swift
//处理后每项数据就变成
[
　　"风:The Windy",
　　"现在:好像无意间将采取跟自己所想的正相反的态度，看来不诚意不行哦。",
　　"障碍:会出现爱情的妨碍者，要象樱似的，光明正大地将其拨掉。",
　　"未来:将有新风味来，看来你将会舍去现在喜欢的人，而另有所爱。",
　　"对策:不要太紧张，只要你大胆地看着对方眼睛说话，就会意外地镇静。",
　　"幸运项目:大一点的书，明朗的绿色服饰，团扇。"
]

var mArray = txtStr.components(separatedBy: ".")
mArray.removeFirst()
for itemSourceStr in mArray {
    var itemArray:[String] = itemSourceStr.components(separatedBy: "\n")
        if let last = itemArray.last {
            if !last.contains(":"){
                itemArray.removeLast()
            }
        }
}
```

最后，我们只需再对用：进行分割，就能简单地获取到所有数据了,最终数据。

```
[
  {
    "name" : "风",
    "now" : "好像无意间将采取跟自己所想的正相反的态度，看来不诚意不行哦。",
    "luckyThing" : "大一点的书，明朗的绿色服饰，团扇。",
    "eName" : "The Windy",
    "obstacle" : "会出现爱情的妨碍者，要象樱似的，光明正大地将其拨掉。",
    "countermeasure" : "不要太紧张，只要你大胆地看着对方眼睛说话，就会意外地镇静。",
    "future" : "将有新风味来，看来你将会舍去现在喜欢的人，而另有所爱。"
  },
  {
    "name" : "影",
    "obstacle" : "看来象有一堵高 ，不过别滞气，总有找到解决方法的。",
    "countermeasure" : "暂时没有信心时，要看着自己的影子，连说三次\"魔鬼走开\"，就会产生新的精神。",
    "now" : "是有点令人担忧的关系。即使隐藏，也总有一日暴露给大家。",
    "luckyThing" : "建筑物的背阳地，带洞黑色的上衣，摄像机。",
    "eName" : "The Shadow"
  }
]
```

以下为完整源码：


```
func analysisData() -> [Divination]{
    guard let resourceDataPath = Bundle.main.path(forResource: "data", ofType: "txt") else {
        return [Divination]()
    }
    
    var txtStr: NSString = try! NSString(contentsOfFile: resourceDataPath, encoding: String.Encoding.utf8.rawValue)
    
    /***
     由于"（）" ，"："，由于这些数据都是我们不需要的，那么我们需要去除他们，清理完后的数据
     
     1.风:The Windy
     现在:好像无意间将采取跟自己所想的正相反的态度，看来不诚意不行哦。
     障碍:会出现爱情的妨碍者，要象樱似的，光明正大地将其拨掉。
     未来:将有新风味来，看来你将会舍去现在喜欢的人，而另有所爱。
     对策:不要太紧张，只要你大胆地看着对方眼睛说话，就会意外地镇静。
     幸运项目:大一点的书，明朗的绿色服饰，团扇。
     
     ***/
    
    txtStr = txtStr.replacingOccurrences(of: ": ", with: ":") as NSString
    txtStr = txtStr.replacingOccurrences(of: "(", with: ":") as NSString
    txtStr = txtStr.replacingOccurrences(of: ")", with: "") as NSString
    
    /***
     2.先通过.进行分割，那么可以分割成为一个含有41个元素的数据，其中首项为"1",其他的如：""
     风(The Windy)
     现在: 好像无意间将采取跟自己所想的正相反的态度，看来不诚意不行哦。
     障碍: 会出现爱情的妨碍者，要象樱似的，光明正大地将其拨掉。
     未来: 将有新风味来，看来你将会舍去现在喜欢的人，而另有所爱。
     对策: 不要太紧张，只要你大胆地看着对方眼睛说话，就会意外地镇静。
     幸运项目: 大一点的书，明朗的绿色服饰，团扇。
     2
     ***/
    var divinations = [Divination]()
    var mArray = txtStr.components(separatedBy: ".")
    
    //3.由于首项为1，为无效数据，切除掉
    mArray.removeFirst()
    
    //4.继续切掉如2，3，4等无效数据
    for itemSourceStr in mArray {
        //5.通过换行，将每项进行分割，除了数组最后一项，
        var itemArray:[String] = itemSourceStr.components(separatedBy: "\n")
        
        if let last = itemArray.last {
            //6. 每个数组的最后一项都会存在如2，3等无效数组，去除掉
            if !last.contains(":"){
                itemArray.removeLast()
            }
        }
    
        print(itemArray)
        let divination: Divination = Divination()
        for (index,item) in itemArray.enumerated() {
           
            //7.最后按：分割，开始取值
            let lineArray = item.components(separatedBy: ":")
            if let key = lineArray.first,let value = lineArray.last {
                if index == 0 {
                    divination.name = key
                    divination.eName = value
                } else if key == "现在"{
                    divination.now =  value
                } else if key == "障碍"{
                    divination.obstacle =  value
                } else if key == "未来"{
                    divination.future =  value
                } else if key == "对策"{
                    divination.countermeasure =  value
                }else if key == "幸运项目"{
                    divination.luckyThing =  value
                }
            }
        }
        
        divinations.append(divination)
        
    }
    
    return divinations
    
}
```


