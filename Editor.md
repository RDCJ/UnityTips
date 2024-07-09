# Unity Editor相关

## CreateAssetMenu
CreateAssetMenu() 是一个自定义属性(Attribute)，用于在编辑器中创建一个自定义 ScriptableObject 的菜单项。这个菜单项可以让开发者在 Unity 编辑器中快速创建新的 ScriptableObject 实例

```
[CreateAssetMenu(fileName= "xxx",menuName = "xxx/xxx", order=0)]
public class SampleScriptableObject : ScriptableObject
{
    // something
}
```
### 参数
* fileName: 这是创建的新ScriptableObject实例的默认文件名。Unity将根据这个文件名在项目中创建对应的文件。通常使用 "NewAsset" 或类名作为默认文件名，并且Unity将自动添加一个数字后缀，以确保文件名的唯一性。
* menuName: 这是在Unity编辑器中创建菜单项的路径。在此重载中，可以自定义菜单项的路径。例如："Assets/Create/Custom Assets"。如果使用无参重载，菜单项路径将默认为"Assets/Create"。
* order: 这是一个可选参数，用于指定菜单项的顺序。如果有多个菜单项，可以使用order参数来设置它们的显示顺序。较小的order值将优先显示。

## MenuItem
MenuItem可用在顶部菜单栏添加按钮，执行相关的函数
```
[MenuItem("xxx/xxx", true/false, 0)]
private static void SampleFunction()
{
    // Do something
}
```
### 参数
1. 路径: string 类型。用 '/' 来分割路径 <br><br>
2. (可选) 是否是验证函数：bool 类型，默认为false <br>
    控制跟它同路径的方法是否可点击。
    ```
    [MenuItem("Learn/Log2",false,1001)]
    public static void Log2()
    {
        Debug.Log("2");
    }


    [MenuItem("Learn/Log2",true,1001)]
    public static bool ValidateLog2()
    {
        return Selection.activeTransform != null;
    }

    ```
    这里第2个方法是对第1个方法的验证, 如果有选中一个transform，那么返回true；否则返回false。它的返回值，决定了跟它同路径的第一个方法是否可点击。<br><br>
3. (可选) 函数优先级：影响在面板上的出现顺序，默认为1000
  
## EditorWindow

### 创建自定义窗口
```
// 继承EditorWindow
public class SampleEditorWindow : EditorWindow
{
    public static void Open()
    {
        // GetWindow打开窗口
        SampleEditorWindow window = GetWindow<SampleEditorWindow>("window name");
    }

    // 顶部菜单添加按钮
    [MenuItem("xxx/xxx")]
    private static void OpenWindow()
    {
        SampleEditorWindow.Open();
    }
    
    // 窗口绘制逻辑
    private void OnGUI()
    {

    }
}
```

## Editor
### 自定义面板
```
// 继承Editor
// 添加属性CustomEditor
[CustomEditor(typeof(SampleClass))]
public class SampleClassEditor : Editor
{
    // 绘制Inspector
    public override void OnInspectorGUI()
    {
        // 调用父类方法可用按照默认方式绘制
        // base.OnInspectorGUI();
    }
}
```
### Editor类成员
* public UnityEngine.Object target<br>
  代表当前控制的组件，可以类型转换为对应的类
  ```
  SampleClass sampleClass = (SampleClass)target;
  ```
* public SerializedObject serializedObject<br>
  代表当前控制的组件

### 常用函数
* 查找属性
  ```
  // public SerializedProperty FindProperty(string propertyPath)

  SerializedProperty property = serializedObject.FindProperty(property_name);
  ```
* 在inpector中显示/编辑组件的属性
    ```
    // public static bool PropertyField(SerializedProperty property, params GUILayoutOption[] options)

    EditorGUILayout.PropertyField(property);
    ```
* 保存编辑结果
    ```
    public override void OnInspectorGUI()
    {
        EditorGUI.BeginChangeCheck();

        // 显示属性控件
        
        if (EditorGUI.EndChangeCheck())
        {
            EditorUtility.SetDirty(target);
            serializedObject.ApplyModifiedProperties();
        }
    }
    ```
    EditorGUI.BeginChangeCheck与EditorGUI.EndChangeCheck结合使用，创建一个代码块，检查该代码块中包含的控件的 GUI 状态是否发生了更改。

## GUILayout
### 水平布局
```
GUILayout.BeginHorizontal();
// ...
GUILayout.EndHorizontal();
```
### 垂直布局
```
GUILayout.BeginVertical();
// ...
GUILayout.EndVertical();
```
### 空行
```
// 参数表示空行所占像素
GUILayout.Space(10);
```
### 显示文字/标题
```
GUILayout.Label(text)
```
### Button 按钮
```
if (GUILayout.Button(text))
{
    // Do something when click
}
```
### Toggle 选项框
```
// 需要声明一个bool变量记录/更新状态
toggle_value = GUILayout.Toggle(toggle_value, title_txt);
```
### 单选选项组
```
// 需要声明一个int变量记录/更新状态
index = GUILayout.SelectionGrid(index, new string[] { "A", "B", "C", "D"}, selection_count);
```
### Scroll View 滚动列表

```
// 需要声明一个Vector2变量记录/更新滚动位置
_scroll = GUILayout.BeginScrollView(_scroll);

// 中间显示的空间会放入Scroll View中
if (GUILayout.Button(text))
{
    // Do something when click
}

GUILayout.EndScrollView();
```
