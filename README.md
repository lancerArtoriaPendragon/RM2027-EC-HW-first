# 电控组第二次培训作业

- **陈金泽**
- ***20263006501***

## 实现思路

在 CubeMX 中配置了三个 GPIO 输出（PA0、PA1、PA2），分别驱动三个 LED；LED 阳极接 GPIO，阴极接 GND。
代码中利用 if-else 计数循环实现非阻塞延时，利用 HAL_GetTick() 设定两个变量 now 与 lastclick，当两变量相差 500ms 时执行一次切换，以此实现流水灯效果。

## 非阻塞延时

常用的 Delay 函数让 CPU 原地等待来实现闪灯，该代码利用 if-else 循环让 CPU 持续运作，并增加了一个三灯全灭的状态，让流水效果更清晰。
首先设定全局变量 lastclick 与计数器 step；每次循环读取 now 并赋予当前时间，当时间差达到 500ms 时进入 if 分支，先令三个 GPIO 输出关闭，再根据 step 点亮对应的灯，最后更新 step 实现循环。

## 核心代码

```c
uint32_t lastclick = 0;
uint32_t step = 0;

while (1)
{
    uint32_t now = HAL_GetTick();

    if (now - lastclick >= 500)
    {
        lastclick = now;

        // 先全部熄灭
        HAL_GPIO_WritePin(LED0_GPIO_Port, LED0_Pin, GPIO_PIN_RESET);
        HAL_GPIO_WritePin(LED1_GPIO_Port, LED1_Pin, GPIO_PIN_RESET);
        HAL_GPIO_WritePin(LED2_GPIO_Port, LED2_Pin, GPIO_PIN_RESET);

        // 再按 step 点亮对应的灯
        if (step == 0)
        {
            HAL_GPIO_WritePin(LED0_GPIO_Port, LED0_Pin, GPIO_PIN_SET);
        }
        else if (step == 1)
        {
            HAL_GPIO_WritePin(LED1_GPIO_Port, LED1_Pin, GPIO_PIN_SET);
        }
        else if (step == 2)
        {
            HAL_GPIO_WritePin(LED2_GPIO_Port, LED2_Pin, GPIO_PIN_SET);
        }

        step++;
        step = step % 4;
    }
}
```

## 运行效果

三个 LED 依次点亮，每个状态保持 500ms，循环执行。演示视频见仓库根目录。
