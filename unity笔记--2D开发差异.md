## unity笔记--2D开发差异

此笔记记录**2D开发**时，与3D开发的项目的区别

### 项目结构差异

见unity笔记--项目管理篇

### 摄像机设置差异

#### 视野（Field of View）和正交投影（Orthographic Projection）

在2D游戏中，通常使用**正交投影（Orthographic Projection）**模式。这种模式与3D相机的透视投影不同，它不会因距离而缩放物体。

可以通过调整Camera的**Size**属性来控制相机能看到的世界范围。这个Size值决定了相机的垂直视野高度（以世界单位为单位）。例如，Size设为5时，表示相机会看到5个单位高度（屏幕从上到下共10个单位，因为上下各有一半）

#### 摄像机的移动和跟随玩家

对于大多数2D游戏，摄像机会跟随玩家角色进行移动。通常可以通过编写脚本来让摄像机的位置始终与玩家位置同步。例如：

```csharp
public Transform player;  // 玩家角色的Transform
public Vector3 offset;     // 可调整的偏移量

void LateUpdate()
{
    // 保持摄像机跟随玩家
    transform.position = new Vector3(player.position.x + offset.x, player.position.y + offset.y, transform.position.z);
}

```

在这个例子中，`LateUpdate()` 确保摄像机的移动发生在所有角色和物体的移动之后，这样可以避免抖动问题。

#### 摄像机的边界限制

在某些情况下，你可能不希望相机超出游戏的边界。这可以通过在摄像机的移动脚本中加入边界条件来实现：

```cs
public Vector2 minBounds;
public Vector2 maxBounds;

void LateUpdate()
{
    float clampedX = Mathf.Clamp(player.position.x + offset.x, minBounds.x, maxBounds.x);
    float clampedY = Mathf.Clamp(player.position.y + offset.y, minBounds.y, maxBounds.y);
    transform.position = new Vector3(clampedX, clampedY, transform.position.z);
}

```

使用 `Mathf.Clamp()` 可以将摄像机的移动范围限制在定义好的最小和最大边界之间。

#### 摄像机的抖动效果

如果想要在某些情况下，例如爆炸或碰撞时，让相机轻微抖动，可以实现**Camera Shake**效果。这通常用于增加游戏的动态感。简单的实现方式是通过随机扰动相机的位置：

```cs
public class CameraController : MonoBehaviour
{
    public Transform player;  // 玩家角色的Transform
    public Vector3 offset;     // 偏移量

    public Vector2 minBounds;  // 最小边界
    public Vector2 maxBounds;  // 最大边界

    // 抖动参数
    public IEnumerator Shake(float duration, float magnitude)
    {
        Vector3 originalPos = transform.localPosition;
        float elapsed = 0.0f;

        while (elapsed < duration)
        {
            float x = Random.Range(-1f, 1f) * magnitude;
            float y = Random.Range(-1f, 1f) * magnitude;

            // 临时计算相机的新位置
            Vector3 newPos = new Vector3(originalPos.x + x, originalPos.y + y, originalPos.z);

            // 将抖动位置限制在边界范围内
            float clampedX = Mathf.Clamp(newPos.x, minBounds.x, maxBounds.x);
            float clampedY = Mathf.Clamp(newPos.y, minBounds.y, maxBounds.y);

            transform.localPosition = new Vector3(clampedX, clampedY, originalPos.z);

            elapsed += Time.deltaTime;
            yield return null;
        }

        // 抖动结束后恢复到原始位置
        transform.localPosition = originalPos;
    }

    void LateUpdate()
    {
        // 摄像机跟随玩家，并确保在边界范围内
        float clampedX = Mathf.Clamp(player.position.x + offset.x, minBounds.x, maxBounds.x);
        float clampedY = Mathf.Clamp(player.position.y + offset.y, minBounds.y, maxBounds.y);
        transform.position = new Vector3(clampedX, clampedY, transform.position.z);
    }
}

```

**Shake 函数**：在每次应用抖动偏移之前，计算抖动后的相机位置，并使用 `Mathf.Clamp()` 函数确保位置在指定的 `minBounds` 和 `maxBounds` 之间。这就防止了相机因抖动而超出边界。

**跟随玩家**：同时，`LateUpdate()` 中的逻辑确保了相机在抖动时仍然跟随玩家，但限制在边界范围内。

> 抖动需要结合边界限制来进行

#### 多相机

在一些复杂的2D游戏中，可能需要使用多个相机。例如，一个主相机用于游戏场景，而另一个相机用于显示UI。可以通过设置**Camera Depth**来决定多个相机的渲染顺序，并且通过调整**Culling Mask**来决定每个相机显示哪些图层的物体。

#### 镜头的缩放

有时你需要根据游戏场景或玩家的需求动态调整相机的缩放。对于2D游戏，应通过调整相机的**Size**属性来实现缩放效果：

```cs
Camera.main.orthographicSize = Mathf.Lerp(Camera.main.orthographicSize, targetSize, Time.deltaTime * zoomSpeed);
```

这种方法可以平滑地缩放相机的视野

#### 相机的背景颜色

如果你想调整游戏背景，可以通过相机的**Background**属性设置一个颜色，或者通过在游戏场景中添加一个背景图层来实现背景效果