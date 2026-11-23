# 第3次練習題目-隨堂-QZ3
>
>學號：113111121
><br />
>姓名：邱怡瑄
><br />
>作業撰寫時間：15m
><br />
>最後撰寫文件日期：2026/11/23
>



1.

Ans:
(1)首先設定兔子的移動方式跟速度
    using UnityEngine;

    using UnityEngine.InputSystem;

    public class Rabbit : MonoBehaviour
    {
        public float speed = 5f;

        // Start 留空即可，因為我們需要每一幀都更新位置
        void Start()
        {
            
        }

        // Update 每一幀都會執行一次
        void Update()
        {
            // 1. 如果目前沒有鍵盤，就不執行
            if (Keyboard.current == null)
            {
                return;
            }

            // 2. 每一幀都重新取得兔子目前的位置
            float currentX = transform.position.x;
            float currentY = transform.position.y;
            float currentZ = transform.position.z;

            // 3. 檢測玩家輸入：W 向前
            if (Keyboard.current.wKey.isPressed)
            {
                // 向前移動 Z 軸正方向
                currentZ = currentZ + speed * Time.deltaTime;
            }
            // 檢測玩家輸入：S 向後
            if (Keyboard.current.sKey.isPressed)
            {
                // 向後移動 Z 軸負方向
                currentZ = currentZ - speed * Time.deltaTime;
            }
            // 檢測玩家輸入：A 向左
            if (Keyboard.current.aKey.isPressed)
            {
                // 向左移動 X 軸負方向
                currentX = currentX - speed * Time.deltaTime;
            }
            // 檢測玩家輸入：D 向右
            if (Keyboard.current.dKey.isPressed)
            {
                // 向右移動 X 軸正方向
                currentX = currentX + speed * Time.deltaTime;
            }

            // 4. 應用新位置
            transform.position = new Vector3(currentX, currentY, currentZ);
        }
    }

(2)接著設定敵人的移動方式，讓敵人能夠自己找到兔子
    using UnityEngine;

public class EnemyMove : MonoBehaviour
{
    public float speed = 3f;         // 敵人移動速度
    private Transform targetRabbit;  // 目標兔子的位置

    void Start()
    {
        // 自動尋找場上 Tag 為 "Player" 的兔子
        GameObject rabbitObj = GameObject.FindGameObjectWithTag("Player");
        if (rabbitObj != null)
        {
            targetRabbit = rabbitObj.transform;
        }
        else
        {
            Debug.LogWarning;
        }
    }

    void Update()
    {
        // 若找不到兔子則不執行移動
        if (targetRabbit == null)
        {
            return;
        }

        // 1. 取得敵人目前的位置
        float currentX = transform.position.x;
        float currentY = transform.position.y;
        float currentZ = transform.position.z;

        // 2. Z 軸移動（加上 0.1f 緩衝距離，避免重疊時原地抖動）
        if (targetRabbit.position.z - currentZ > 0.1f)
        {
            currentZ = currentZ + speed * Time.deltaTime;
        }
        else if (currentZ - targetRabbit.position.z > 0.1f)
        {
            currentZ = currentZ - speed * Time.deltaTime;
        }

        // 3. X 軸移動（加上 0.1f 緩衝距離，避免重疊時原地抖動）
        if (targetRabbit.position.x - currentX > 0.1f)
        {
            currentX = currentX + speed * Time.deltaTime;
        }
        else if (currentX - targetRabbit.position.x > 0.1f)
        {
            currentX = currentX - speed * Time.deltaTime;
        }

        // 4. 更新敵人位置
        transform.position = new Vector3(currentX, currentY, currentZ);
    }
}

(3)最後設定兔子的血量、撞到敵人會扣一點血、吃到補包會加一點血
using UnityEngine;

public class RabbitPlayer : MonoBehaviour
{
    [Header("血量設定")]
    public int maxHealth = 5;     // 最大血量上限
    public int currentHealth;     // 目前血量

    void Start()
    {
        // 遊戲開始時，將目前血量設為滿血
        currentHealth = maxHealth;
        Debug.Log("遊戲開始，兔子目前血量：" + currentHealth);
    }

    // 【修正重點】改為 3D 碰撞觸發：OnTriggerEnter(Collider other)
    private void OnTriggerEnter(Collider other)
    {
        // 1. 遇到敵人：扣 1 血
        if (other.CompareTag("Enemy"))
        {
            ChangeHealth(-1);
        }
        // 2. 遇到補包：加 1 血
        else if (other.CompareTag("HealthPack"))
        {
            // 只有未滿血時才吃補包（避免浪費）
            if (currentHealth < maxHealth)
            {
                ChangeHealth(1);
                Destroy(other.gameObject); // 補包被吃掉後從場景中移除
            }
        }
    }

    // 統一處理血量加減的函式
    public void ChangeHealth(int amount)
    {
        currentHealth = Mathf.Clamp(currentHealth + amount, 0, maxHealth);
        
        Debug.Log("血量變動：" + amount + "，兔子目前剩餘血量：" + currentHealth);

        if (currentHealth <= 0)
        {
            Die();
        }
    }

    private void Die()
    {
        Debug.Log("兔子沒血了，遊戲結束！");
    }
}



## 其他
    經過今天的課程學習之後，讓我可以把今天學到的，扣血、加血的設定放到我們的遊戲之中，讓自己做的遊戲增加更多可玩性。