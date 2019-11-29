##子孙树的实现
```php
$address = array(
    array('id'=>1  , 'address'=>'安徽' , 'parent_id' => 0),
    array('id'=>2  , 'address'=>'江苏' , 'parent_id' => 0),
    array('id'=>3  , 'address'=>'合肥' , 'parent_id' => 1),
    array('id'=>4  , 'address'=>'庐阳区' , 'parent_id' => 3),
    array('id'=>5  , 'address'=>'大杨镇' , 'parent_id' => 4),
    array('id'=>6  , 'address'=>'南京' , 'parent_id' => 2),
    array('id'=>7  , 'address'=>'玄武区' , 'parent_id' => 6),
    array('id'=>8  , 'address'=>'梅园新村街道', 'parent_id' => 7),
    array('id'=>9  , 'address'=>'上海' , 'parent_id' => 0),
    array('id'=>10 , 'address'=>'黄浦区' , 'parent_id' => 9),
    array('id'=>11 , 'address'=>'外滩' , 'parent_id' => 10)
    array('id'=>12 , 'address'=>'安庆' , 'parent_id' => 1)
    );
```

###代码
```php
function getSubTree($data , $id = 0) {
    $task = array($id);                       # 栈 任务表
    $son = array();
    
    while(!empty($task)) {
        $flag = false;                           # 是否找到节点标志
        foreach($data as $k => $v) {

            # 判断是否是子孙节点的条件 与 递归方式一致
            if($v['parent_id'] == $id) {
                $son[] = $v;                     # 节点存入数组
                array_push($task , $v['id']);   # 节点id入栈
                $id = $v['id'];               # 判断条件切换
                unset($data[$k]);               # 删除节点
                $flag = true;                   # 找到节点标志
            }
        }
        
        //标注1.0 var_dump($data);
        //标注1.1 var_dump($son);
        
        # flag == false说明已经到了叶子节点 无子孙节点了
        if($flag == false) {
            array_pop($task);                   # 出栈
            $id = end($task);                   # 寻找栈顶id的子节点
        }
    }
    return $son;
}
```

```php
echo '<pre>';var_dump(Ancestry($address,1));
```
###第一次循环
```php
array('id'=>1  , 'address'=>'安徽' , 'parent_id' => 0);
```
`parent_id`值与传入参数$id的`1`不相等.  

直到循环第三次
```
 array('id'=>3  , 'address'=>'合肥' , 'parent_id' => 1),
 ```
 
`parent_id=>1`与传入参数$id的`1`相等,接着把当前数组`id`值赋值给下次循环的`$id`值,并删除当前数组  

接着循环后面两个数组,并把对应`庐阳区`和`大杨镇`的`id`值作为下次循环判断的`$id`

###foreach首次结束
这时刚结束上面的循环,代码标注1.0中打印的数组只剩下9个元素,另外三个已经在foreach中被删除
```php
array(9) {
  [0]=>
  array(3) {
    ["id"]=>
    int(1)
    ["address"]=>
    string(6) "安徽"
    ["parent_id"]=>
    int(0)
  }
  [1]=>
  array(3) {
    ["id"]=>
    int(2)
    ["address"]=>
    string(6) "江苏"
    ["parent_id"]=>
    int(0)
  }
  [5]=>
  array(3) {
    ["id"]=>
    int(6)
    ["address"]=>
    string(6) "南京"
    ["parent_id"]=>
    int(2)
  }
  [6]=>
  array(3) {
    ["id"]=>
    int(7)
    ["address"]=>
    string(9) "玄武区"
    ["parent_id"]=>
    int(6)
  }
  [7]=>
  array(3) {
    ["id"]=>
    int(8)
    ["address"]=>
    string(18) "梅园新村街道"
    ["parent_id"]=>
    int(7)
  }
  [8]=>
  array(3) {
    ["id"]=>
    int(9)
    ["address"]=>
    string(6) "上海"
    ["parent_id"]=>
    int(0)
  }
  [9]=>
  array(3) {
    ["id"]=>
    int(10)
    ["address"]=>
    string(9) "黄浦区"
    ["parent_id"]=>
    int(9)
  }
  [10]=>
  array(3) {
    ["id"]=>
    int(11)
    ["address"]=>
    string(6) "外滩"
    ["parent_id"]=>
    int(10)
  }
  [11]=>
  array(3) {
    ["id"]=>
    int(12)
    ["address"]=>
    string(6) "安庆"
    ["parent_id"]=>
    int(1)
  }
}
```
####标注1.1
`$son`的值
```php
array(3) {
  [0]=>
  array(3) {
    ["id"]=>
    int(3)
    ["address"]=>
    string(6) "合肥"
    ["parent_id"]=>
    int(1)
  }
  [1]=>
  array(3) {
    ["id"]=>
    int(4)
    ["address"]=>
    string(9) "庐阳区"
    ["parent_id"]=>
    int(3)
  }
  [2]=>
  array(3) {
    ["id"]=>
    int(5)
    ["address"]=>
    string(9) "大杨镇"
    ["parent_id"]=>
    int(4)
  }
}
```
###首次进行标志判断
这时`$flag`在最后foreach循环中,已经为`true`,`$id`为`5`    
所以首次下面判断不进去foreach循环
```php
if($flag == false)
```
然后循环`$data`数组只有9个元素,经过一轮循环,找不到存在`parent_id`为`5`的元素了,所以这时`$flag`为`fasle`  
进去
```php
if($flag == false) {
   array_pop($task);              
   $id = end($task);              
}
```
在`$task`弹出最后一个元素,`$id`为`5`的值,然后再利用当前这个`$id`为`5`的值循环判断,依然找不到符合要求的数据   
直到弹出最后一个`$id`为`1`,才进入foreach循环中`parent_id`与`$id`相等,最后加入到`$son`值中

最后输出
```php
array(4) {
  [0]=>
  array(3) {
    ["id"]=>
    int(3)
    ["address"]=>
    string(6) "合肥"
    ["parent_id"]=>
    int(1)
  }
  [1]=>
  array(3) {
    ["id"]=>
    int(4)
    ["address"]=>
    string(9) "庐阳区"
    ["parent_id"]=>
    int(3)
  }
  [2]=>
  array(3) {
    ["id"]=>
    int(5)
    ["address"]=>
    string(9) "大杨镇"
    ["parent_id"]=>
    int(4)
  }
  [3]=>
  array(3) {
    ["id"]=>
    int(12)
    ["address"]=>
    string(6) "安庆"
    ["parent_id"]=>
    int(1)
  }
}
```
