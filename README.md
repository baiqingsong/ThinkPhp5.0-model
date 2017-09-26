# think php5.0 模型的使用

* [数据的添加](#数据的添加)
* [数据的删除](#数据的删除)
* [数据的更新](#数据的更新)
* [数据的查询](#数据的查询)
* [数据的聚合](#数据的聚合)
* [模型器获取](#模型器获取)
* [模型器修改](#模型器修改)
* [自动完成](#自动完成)
* [时间戳](#时间戳)



## 数据的添加

插入数据有三种方法，分别是类的create方法，对象的save方法和插入多条的saveAll方法
```
        //create方法，传递实例，返回实例，可以获取相应字段
//        $res = User::create(
//            [
//                "username" => "dawn12",
//                "password" => md5("dawn12")
//            ]
//        );
        //插入的时候可能存在数据库中没有的字段，需要添加第二个参数为true
//        $res = User::create(
//            [
//                "username" => "dawn13",
//                "password" => md5("dawn13"),
//                "demo" => "nihao"
//            ]
//        , true);
        //如果限制字段插入值，在第二个参数中添加要插入的字段
//        $res = User::create(
//            [
//                "username" => "dawn14",
//                "password" => md5("dawn14"),
//                "demo" => "nihao"
//            ]
//            , ["username"]);
        //save方法，需要创建对象,此方法返回int类型，插入成功几条数据
//        $userModel = new User();
//        $userModel->username = "dawn21";
//        $userModel->password = md5("dawn21");
//        $res = $userModel->save();
        //save方法中可以直接添加实例的数组，这个较上个方法简单一些
//        $userModel = new User();
//        $res = $userModel->save([
//            "username" => "dawn17",
//            "password" => md5("dawn17")
//        ]);
        //同理，如果插入了数据库中没有的字段，需要调用方法allowFiled，参数和上面的第二个参数一致，true或者是需要插入的字段
//        $userModel = new User();
//        $res = $userModel->allowField(true)->save([
//            "username" => "dawn18",
//            "password" => md5("dawn18"),
//            "demo" => "nihao"
//        ]);
        //saveAll方法添加多条数据，返回添加数据的实例，saveAll方法中可以添加二位数组
//        $userModel = new User();
//        $res = $userModel->saveAll([
//            [
//                "username" => "dawn19"
//            ],
//            [
//                "username" => "dawn20"
//            ]
//        ]);
//        dump($res);
```

## 数据的删除

删除数据的方法有类的destroy方法，对象的delete方法，类的where后的delete方法
```
        //destroy删除数据
//        $res = User::destroy(7);
        //参数可以是闭包函数，传入条件
//        $res = User::destroy(function($query){
//            $query -> where("id", "lt", 10);
//        });
        //对象的delete方法
//        $userModel = User::get(10);
//        $res = $userModel -> delete();
        //可以先调用类的where方法然后执行delete方法删除数据
//        $res = User::where("id", "lt", 12) -> delete();
        //条件横成立代表删除数据库中所有数据
//        $res = User::where("1==1")->delete();
//        dump($res);
```

## 数据的更新

更新数据有四种方法，类的update方法，类的where然后update方法，对象的save方法，saveAll方法。  
这个推荐的方法是类的where然后update方法和对象的save方法，因为返回值可以判断是否更新
```
        //update方法，参数是个数组，返回值是修改后的实例，需要注意的是没有修改（调用第二次）也返回这个实例，所以没办法判断是否修改
//        $res = User::update([
//            "id" => 10,
//            "password" => "10"
//        ]);
        //update的第二个参数可以传更新条件，如果更改字段中有id不需要传
//        $res = User::update([
//            "password" => "11"
//        ],["id" => 11]);
        //第二个参数可以传个闭包函数
//        $res = User::update([
//            "password" => "<10"
//        ], function($query){
//            $query -> where("id", "lt", 10);
//        });
        //利用where传更新条件，然后调用update方法，这个返回的是更新数据的个数，可以区分是否更新过,建议使用这个方法
//        $res = User::where("id", "lt", 10)->update([
//            "password" => "<<10"
//        ]);
        //可以先获取实例对象，然后更改属性，然后保存
//        $userModel = User::get(10);
//        $userModel->password = "save 10";
//        $res = $userModel->save();
        //同理save可以添加第二个参数，更新的条件,可以是数组，也可以是闭包函数
//        $userModel = new User();
//        $res = $userModel -> save([
//            "password" => "id save 10"
//        ], ["id" => 10]);
        //多个更新，可以用saveAll方法，但是需要注意的是返回的是实例的数组，所以不建议用
//        $userModel = new User();
//        $res = $userModel->saveAll([
//            [
//                "id" => 10,
//                "password" => "save all 10"
//            ],
//            [
//                "id" => 11,
//                "password" => "save all 11"
//            ]
//        ]);
//        dump($res);
```
## 数据的查询

查询数据的四种方法，分别是get，find，select，all
```
        //1.get方法，获取一条信息，传递id值
//        $res = User::get(7);
        //get方法中可以传闭包，里面添加查询语句，闭包中也可以查询指定字段
//        $res = User::get(function($query){
//            $query->where("username", "eq", "dawn");
//        });
        //2.可以使用where函数，链式结构，可以指定查询字段
//        $res = User::where("id", 10)
//            ->field("id, username")
//            ->find();
        //3.select和find的区别是find查询一个数据，select查询多个数据
//        $res = User::where("id", "lt", 10)
//            ->select();
        //4.all返回的是数据的集合，需要遍历显示
//        $res = User::all("7,8,9");
//        $res = User::all([7,8,9,10]);
//        $res = User::all(function($query){
//            $query->where("id", "lt", 10);
//        });
        //直接查询某个字段对应的值
//        $res = User::where("id", 10)->value("username");
//        dump($res);
//        dump($res->toArray());
//        foreach($res as $val){
//            dump($val->toArray());
//        }
```


## 数据的聚合

聚合操作包括查询总个数（count）,最大值（max），最小值（min），平均值（avg），总和（sum）
```
//        $res = User::count();
        //可以通过where添加查询条件
//        $res = User::where("id", "gt", 20)->count();
        //可以通过max方法查询最大值
//        $res = User::max("id");
        //可以通过where添加查询条件
//        $res = User::where("id", "lt", 20) -> max("id");
        //计算总和用sum方法，同样可以使用where方法作为判断
//        $res = User::sum("id");
        //通过avg来获取平均值,也可以通过where来作为筛选
//        $res = User::avg("id");
        //通过min来获取最小值
//        $res = User::min("id");
//        dump($res);
```

## 模型器获取

可能有的字段获取的值和显示的值不相同，例如性别，数据库中保存
的是1和2，但是显示的是男和女，所以要对获取的值进行设置  
model中设置
```
public function getSexAttr($val){
    //获取sex字段的方法，名称固定
    switch ($val){
        case 1:
            return "男";
        case 2:
            return "女";
        default:
            return "未知";
    }
}
```
控制器中
```
//由于对sex进行获取更改返回，所以返回结果为修改后的结果
$res = User::get(15);
dump($res->toArray());
//获取数据库的原始数据
dump($res->getData());
```

## 模型器修改
在模型中对数据进行修改
```
public function setPasswordAttr($val){
    //此方法名称必须和字段password一致
    return md5($val);
}
```
方法中可以传递第二个参数$data,可以对传过来的实例进行获取

## 自动完成

自动完成
```
//此参数为自动完成字段
protected $auto = [
  'time'
];
public function setTimeAttr(){
    return time();
}
```

新增的自动完成
```
//此参数只有在新增的时候有效
protected $insert = [
    'time_insert'
];
public function setTimeInsertAttr(){
    return time();
}
```

更新的自动完成
```
//此参数只有在更新的时候有效
protected $update = [
    'time_update'
];
public function setTimeUpdateAttr(){
    return time();
}
```

## 时间戳

需要在模型中开启时间戳
```
//开启时间戳
protected $autoWriteTimestamp = true;
```
数据库的表对应的字段是create_time和update_time  
如果需要改变数据库中的字段则
```
//设置创建时间的字段
protected $createTime = "create_at";
//关闭更新时间的字段
protected $updateTime = false;
```
