# 第A-06課 修改Animator

#### Animator管理多個動畫片段, 可以設定在何種條件執行何種片斷.


### 結果
![GitHub Logo](/screen/img06.png)


### 檔案放置位置
```
<Unity專案>
   |  
   |___<Assets>
         |___ Main.unity  (主場景)  
         |
         |___ <sprites>
         |        |___ Player.png  (主角圖片)   
         |        
         |___ <animators>
         |        |___ Player.controller  (Animator, 動畫控制器)     
         |        |___ down.anim          (向下走的動畫片段)                 
         |        |___ up.anim            (向上走的動畫片段)  
         |        |___ left.anim          (向左走的動畫片段)  
         |        |___ right.anim         (向右走的動畫片段)           
         |        
         |___ <scripts>
                  |___ Player.cs   (主角的程式)                     
```


### 1. 開啟動畫視窗

![GitHub Logo](/screen/img06-01.png)

```
(1) 在[Hierarchy]中點選[Player]主角.
(2) 在[Window]工具中, 選[Animation]動畫視窗.
```

### 2. 建立動畫片斷

![GitHub Logo](/screen/img06-02.png)

```
(1) 在[Animation]動畫視窗, 點選展開所有動畫片斷的[小圖示].
(2) 點選[Create New Clip...]功能.
```


### 3. 設定動畫片斷檔名

![GitHub Logo](/screen/img06-03.png)

```
(1) 假設程式名為[up].
(2) 點擊[存檔].
```


### 4. 將圖片加入時間軸中

![GitHub Logo](/screen/img06-04.png)

```
將[sprites]中的[Player_12]~[Player_15]圖片, 加入[影格0], [影格10], [影格20], [影格30]及[影格39]中.
```


### 5. 增加向左及向右動畫片斷

![GitHub Logo](/screen/img06-05.png)

```
重覆步驟3及4, 完成[向左]及[向右]動畫片斷.
```


### 6. 打開動畫控制器

![GitHub Logo](/screen/img06-06.png)

```
雙點擊[Player]動畫控制器.
```


### 7. 在動畫控制器中加入參數

![GitHub Logo](/screen/img06-07.png)

```
(1) 點擊[Parameters].
(2) 點擊[+].
(3) 選擇布林型態[Bool].
```


### 8. 設定參數名稱

![GitHub Logo](/screen/img06-08.png)

```
參數名稱輸入[down].
```


### 9. 增加其他參數

![GitHub Logo](/screen/img06-09.png)

```
重覆步驟7及8, 再增加[up], [left]及[right]參數.
```


### 10. 設定動畫片斷轉換

![GitHub Logo](/screen/img06-10.png)

```
(1) 在[Any State]按滑鼠右鍵.
(2) 點擊[Make Transaction].
```


### 11. 連結轉換動畫片斷

![GitHub Logo](/screen/img06-11.png)

```
點擊[down], 系統將產生有方向的線條連結.
```


### 12. 連結轉換其他的動畫片斷

![GitHub Logo](/screen/img06-12.png)

```
重覆步驟10及11, 再連結[up], [left]及[right]動畫片斷.
```


### 13. 設定轉換條件及播放方式

![GitHub Logo](/screen/img06-13.png)

```
(1) 點擊連結down的[有方向的線條], 此時會變成[藍色].
(2) 在[Inspector]中, 將[TransitionDuration]改成[0].
(3) 取消[Can Transition To Self].
(4) 在[Conditions]按[+], 選擇參數[down]值為[true].
```


### 14. 設定其他的轉換條件及播放方式

![GitHub Logo](/screen/img06-14.png)

```
重覆步驟13, 設定[up], [left]及[right]的轉換條件及播放方式.
```


### 15. 加入程式

![GitHub Logo](/screen/img06-15.png)

```
雙點擊[scripts]的[Player], 打開程式畫面.
```

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Player : MonoBehaviour {
    private float moving_speed = 1.3F;
    private float animation_speed = 1.2F;
    private Vector3 target_position;
    private Animator animator;
    private string direction;

    private int cnt=0;

    //---------------------------------------
    // Use this for initialization
    //---------------------------------------
    void Start () {
        //取得主角的動畫器, 設定動畫播放速度
        animator = GetComponent<Animator> ();
        animator.speed = animation_speed;

        //設定主角初始座標
        transform.position = new Vector3 (5, 5, 0);

        //設定主角初始方向
        direction = "down";
        ClearParams ();
        animator.SetBool ("down", true);

        //設定目標位置為主角現在位置的下方方格
        target_position = transform.position;
        target_position += Vector3.down;
    }
    //---------------------------------------


    //---------------------------------------
    // Update is called once per frame
    //---------------------------------------
    void Update () {				
        //在[播放一個frame的時間]=[Time.deltaTime]的條件下,    
        //系統會在大約1000微秒的時間, 將主角從transform.position, 移動到target_position.        
        //[每次移動距離]將被等分為[總移動距離]*Time.deltaTime/1000.
        //如果在第3個參數乘以moving_speed, 則[每次移動距離]將被等分為[總移動距離]*Time.deltaTime*moving_speed/1000.
        //因此, 移動速度變成moving_speed倍.
        transform.position = Vector3.MoveTowards(transform.position, target_position, Time.deltaTime * moving_speed);

        //為避免小數誤差, 進行最後一個frame的移動之後, 系統會將自動修正[主角座標]=[目的地座標].
        if (transform.position == target_position) {
            //除錯的輸出, 在Console視窗顯示
            Debug.Log (cnt);
            Debug.Log ("時間:"+Time.time*1000);
            Debug.Log ("y座標:"+transform.position.y);
            cnt++;

            //如果到達某些點, 讓主角換方向
            if (transform.position.x == 5 && transform.position.y == -5) {
                direction = "left";
            }else if(transform.position.x == -5 && transform.position.y == -5) {
                direction = "up";
            }else if(transform.position.x == -5 && transform.position.y == 5) {
                direction = "right";
            }else if(transform.position.x == 5 && transform.position.y == 5) {
                direction = "down";
            }			

            //設定接下來的動畫及下一個目的地
            ClearParams ();

            if (direction == "up") {
                animator.SetBool ("up", true);
                target_position += Vector3.up;
            }else if (direction == "down") {
                animator.SetBool ("down", true);
                target_position += Vector3.down;
            }else if (direction == "left") {
                animator.SetBool ("left", true);
                target_position += Vector3.left;
            }else if (direction == "right") {
                animator.SetBool ("right", true);
                target_position += Vector3.right;
            }
        }
    }
    //--------------------------------------- 


    //---------------------------------------
    // Clear parameters of Animator
    //---------------------------------------
    void ClearParams () {	
        animator.SetBool ("up", false);
        animator.SetBool ("down", false);
        animator.SetBool ("left", false);
        animator.SetBool ("right", false);
    }
    //---------------------------------------
}
```


### 16. 完成! 

![GitHub Logo](/screen/img06-16.png)

