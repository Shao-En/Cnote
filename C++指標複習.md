**第一集 指標跟*之間的關係**

指標是用來儲存記憶體位址的變數，用來配置 存值 取值 釋放
宣告
```c++=
//*代表宣告指標 (int* = 宣告一個int的指標)
type* name;
type* name = address;
//相同型別的指標可以一行內宣告 但要記得都要加上 * 
type* nameA, *nameB;
```
指標的宣告與存取
![](https://hackmd.io/_uploads/rJuyMw0Y3.png)

```c++=
int a;
a = 8;
cout << a << endl; // 8
int * p;
p = &a // 把a這個變數的記憶體位址指派給p p只能等於記憶體位址 若p = 10會是錯誤的
cout << a << endl;
//p 有沒有 * 的差別, 指標沒有*的=右邊一定是一個記憶體位址, 之後透過*字號管理這個記憶體位址存放的值
//透過指標所存取的記憶體位置管理值
*p = 10; //當指標前面加了*字號代表要存取的不是指標 是指標管理的記憶體空間
cout<< *p << endl; // 10
cout<< a << endl;  // 10 被*p改變了 因為*p是用來管理p儲存的記憶體位址

```

```c++=
//容易搞混的點
//這樣是合法的 但會誤會的點是 
//*q 實際上應該是 int * q; 代表宣告了一個int的指標名稱是 p
//int *q = &a;
int * q = &a;
*q = 20;
cout << a << endl;    // 20 因為 q儲存了a的記憶體位址並且用*q管理了該記憶體位址儲存的值
cout << *p << endl;   // 20 因為p儲存的記憶體位址的值被*q改變了
```

第二集 
**指派時甚麼東西能指派給指標**

記憶體位址有三種寫法
1.& 取得一個 "已存在的變數" 的記憶體位址
2.new 配置指定型別的記憶體空間，並回傳該空間的記憶體位址
3.另一個指標 //變成有兩個指標指向同一個記憶體位址
```c++=

int * r;
r = new int; //另外配一個可以放int的記憶體空間
*r = 100;
cout << r << endl;    //    0x123
cout << *r << endl;   //    100    

int * s = r; //r是一個已經存在的指標 變成有兩個指標指向同一個記憶體位址
*s = 200;
cout << *r << endl; // 200

```

delete
用 new 配置的記憶體可以用delete釋放
再用delete之前是要確定這個new的空間不要了 而不是這個指標不要了 
```c++=
delete r;
cout<< r << endl;    //0x1011a08 還是儲存了剛才的記憶體的位址 因為delete不是清空了指標儲存的值 
cout<< *r << endl;   //16879656  是釋放掉了儲存這個值的空間
//其他指向同一個位址的指標結果一樣會被改變
cout<< s << endl;    //0x1011a08
cout<< *s << endl;   //16879656

```

const 用來宣告常數 理解被設為不可變動的對象是指標 還是 被指標所管理的記憶體
1.const type * n; //不可變動的對象是指標所管理的記憶體內的值 *n
2.type * const n; //不可變動的對象是指標 n
  Ex:
      int * const m = new int; //寫在 m 之前代表是 m 被 read-only

```c++=
int * const m = new int;
*m = 100;    //合法
*m = 200;    //合法
cout<< *m << endl;    //200
delete m;    //合法
//m = new int;    //不合法 因為不能再重新指派m

const int * n = new int;    //這種方式沒有意義 因為沒辦法去修改new出來的空間
*n = 100; //不合法 因為 * n 是 read-only 
```
const int * n的正確使用方法
```c++=
int * const m = new int;
*m = 100;    //合法
*m = 200;    //合法
----------------------------------------------------------
const int * n = m;    //現在 m 和 n 都可以管理這個記憶體空間
//*n = 100    //不合法 因為 n 不能管理該記憶體內的值 
cout << *n << endl;    //200 但可以用來得到這個記憶體位址裡面的值

//但 n 是可以隨時指派新的指標內的記憶體位址給他
n = s; // n 是可以變動的
cout << *n << endl;    //300

```

第三集
指標參數 
以指標為參數的Function，Function的參數要放甚麼 要回傳甚麼

1.以指標為參數
    pass by point
指標參數型別和指標返回值型別
三種指派為只給指標的方式都能用來當函數的參數
//注意指標指派new int的意思

參數的傳遞分為兩種
1.內容的複製
2.記憶體位址的複製
大量資料時是複製 還是 直接改記憶體位址

C++中分成
1.passByValue
2.passByAddress又分成passByPoint和passByReference
```c++=
int a = 10;

showValue(&a) //放記憶體位址 10

int * b = new int;
*b = 20;
showValue(b); //因為指標也代表了一個記憶體位址 放&b是錯的 因為b已經給了位址 

int * p;
p = &a   //只能指派位址
p = b;

int value = 1;
passByValue(value);
cout<< value << endl;    // 1 

passByPoint(&value);    //因為int * pValue是指標只能放記憶體位址
cout< value << end;;    // 101
---------------------------
void showValue(int * p){
    cout << *p << endl;
}

void passByValue(int value);
void passByValue(int value){
    value+ = 100;        //把value的值複製給 passByvalue的參數 並非同一個值 是複製貼上的意思 執行結束後記憶體                            釋放放了
}

void passByPoint(int * pValue);
void passByPoint(int * pValue){    //甚麼東西可以當參數 一個記憶體位址 因為int * pValue是指標只能放記憶體位址
    *pValue+ = 100;                //所以複製了記憶體位址0x123過來 所以改掉了值 
                                   //因為地址給人了
}

```
第四集
返回型別為指標時開發的設計者要注意甚麼 呼叫敘述又要注意甚麼

return型別為指標
1.呼叫敘述是一個記憶體位址
2.不應return區域變數的記憶體位址
3.可以宣告為const
```c++=
int * getAddressA();
int * i = getAddressA();//這東西可以指派給指標代表這個呼叫敘述是一個記憶體位址
cout << * i << endl;    //100 


int * j = getAddressB();
*j = 200;
cout << *j << endl;    //200 代表getAddressB給的記憶體位址是可用的
----------------------------------------
int * getAddressA(){
    int * p = new int;
    * p = 100;
    return p; //放 p 因為要回傳的是記憶體位址 是記憶體位址被複製被貼到 指標i中
}

int * getAddressB(){
    return new int;    //合法 少用
    }
```
2.不應return區域變數的記憶體位址
```c++=
int * k = getAddressC()
cout << *k << endl;	//不影響執行的錯誤! 警告 回傳了"區域變數" p 的位址
-----------------------------------------------
int * getAddressC()
cout << *k << endl;	//不影響執行的錯誤! 警告 回傳了"區域變數" p 的位址{
    int p = 8;
    return &p;    // 區域變數只有在函數執行時期才存在 函數結束就放掉了 所以沒得存取
}

```

```c++=
int * m = new int;
*m = 1;
getAddressD(m);
cout << *m << end;    // 101
---------------------------
int * getAddressD(int * p) //接收一個記憶體位址當參數
    *p += 100;
    //return p;     //此處沒必要return 因為已經改變了位址的值
}
```
參數用(const int * p)是在告訴使用者放心呼叫 因為函式不會改到這個指標的值
```c++=
int * m = new int;
*m = 1;
int * n = getAddressD(m);
cout << *n << end;    //101
---------------------------
int * getAddressD(const int * p) //在這裡用const是因為 當參數的那個指標的值不會被改掉
                                 //讓看到文件的人知道現在當參數的指標不會被改掉
    int * q = new int;
    *q = *p;
    *q += 100;
    return q;     //這種方式是利用 p 來 創造一個新的副本 q 
}
```
如果是希望回傳的記憶體位址裡面的值不要被改變呢
```c++
int * m = new int;
*m = 1;
const int * n = getAddressD(m);    //接收的指標也要是const
*n = 100    //不合法
cout << *n << end;    //101
----------------------------
前面這邊的const int *是說return type
const int * getAddressD(const int * p) //告訴你函式return的記憶體位址不能被修改
                                       //
    int * q = new int;
    *q = *p;
    *q += 100;
    return q;     //這種方式是利用 p 來 創造一個新的副本 q 

```

第五集
動態陣列跟陣列參數
記憶體配置跟釋放 不是new出來的就不能delete
動態陣列
1.用new配置記憶體空間
2.可以delete
動態陣列的範例
```c++=
int classCount;
cout<<"請輸入班級數量" =>";
cin >> classCount;
double * ave = new double[classCount]; //宣告一個動態陣列
for(int j = 0; j < classCount; j++){
    int count;
    cout<< "請輸入學生人數";
    cin >> count;
    
    int * score = new int[count];
    for(int i = 0; i < count; i++){
        cout << "請輸入學生成績";
        cin >> score[i];
    }
    double total = 0;
    for(int i = 0; i < count; i++){
        total += score[i];
    }
    avg[j] = total / count;
    delete score;     //重點是釋放
}
```
陣列參數 函式開發者要接收一個陣列當參數時要用陣列宣告好還是指標宣告好
1.型別可以用 * 取代 [] 但 不建議

```c++=
int classCount;
cout<<"請輸入班級數量" =>";
cin >> classCount;
double * ave = new double[classCount]; //宣告一個動態陣列
for(int j = 0; j < classCount; j++){
    int count;
    cout<< "請輸入學生人數";
    cin >> count;
    
    int * score = new int[count];
    for(int i = 0; i < count; i++){
        cout << "請輸入學生成績";
        cin >> score[i];
    }

    avg[j] = getAvg(score, count);
    delete score;     //重點是釋放
}

-------------------------------------------------
//把計算平均的地方抽出來寫
//為甚麼宣告的score是指標動態陣列卻可以直接用 int socre[] 
//因為陣列名稱 或是 指標也好都能用指派記憶體位址
//double getAvg(int * score, int length) 也可以 但不建議 要讓大家知道這邊是用陣列
double getAvg(int score[], int length){
    double total = 0;
    for(int i = 0; i < length; i++){
        total += score[i];
    }
    return total / length;
};

```

第六集
指標的指標和void的指標和指標陣列
指標的指標
1.宣告type ** name
2.存取值 **name
```c++=
int ** a;    //指標的指標
a = new int*;    //可以指派的是 一個指標的記憶體位址
*a = new int;    //可以放int值的記憶體位址
**a = 10;        //**a才是在存取值
cout << a << endl;          // 0x6e1a48
cout << *a << endl;         // 0x6e1a58
cout << **a << endl;        //10

int * b = new int;
*b = 20;
a = &b;     //指標的指標真實用法是用來儲存指標的記憶體位址
cout << **a << endl;    // 20

void * v; //可以指派任意型別的指標給他 但要取值很麻煩 要轉型別
v = b;
cout << b << endl; //0x123
cout << v << endl; //0x123
//cout << *v << endl; //不合法 因為不知道型別是甚麼
cout << *((int*)v) << endl;    //20
```
指標的陣列
1.宣告
2.存取

```c++=
int * array[10];
for(int i = 0; i < 10; i++){
    array[i] = new int;        //先每一格指標做初始化的動作
    *array[i] = i + 1;
}
for(int i = 0; i < 10; i++){
    cout << *array[i] << endl; // 0~10
}
```

動態的指標陣列 要用到指標的指標 (**a)
```c++=
int ** pArray            //表示 pArray是指標的指標 那這東西該放甚麼
pArray = new int*[10]     //new一個指標的陣列給這一個指標的指標
for(int i = 0; i < 10; i++){
    pArray[i] = new int;
    *pArray[i] = i + 1;
}
for(int i = 0; i < 10; i++){
    cout << *pArray[i] << endl; // 0~10
}
```
