On 27.11.2024 I had the pleasure of presenting my lecture **Tool your way to inner peace in Unity development** at the [Game Industry Conference](https://gic.gd/) in Poznan.
It was a great experience, just look at the photo :smiley:

In this repo I'm sharing the [slides](https://github.com/patrykf-dev/tools-unity-gic-2024/blob/main/slides.pdf) and all the code samples I used during my talk.
If you have any questions, feel free to ask them by opening an [issue](https://github.com/patrykf-dev/tools-unity-gic-2024/issues).

![Photo from my lecture](https://github.com/patrykf-dev/tools-unity-gic-2024/blob/main/gic-pic.jpg?raw=true)

## Description
I explored a general approach to tool development.
Emphasis was placed on best practices, including good UX design principles.
I showcased real-life examples, including tools from my solo projects and custom solutions built for a commercial project, [StarHeroes](https://starheroes.io/).

## Code samples
Custom editor buttons based on [Unity toolbar extender](https://github.com/marijnz/unity-toolbar-extender):
```cs
[InitializeOnLoad]
public class YourEditorButtons
{
    static YourEditorButtons()
    {
        ToolbarExtender.RightToolbarGUI.Add(OnRightToolbarGUI);
    }

    private static void OnRightToolbarGUI()
    {
        GUILayout.FlexibleSpace();

        if (GUILayout.Button(new GUIContent("Setup scenes", "tooltip")))
        {
            ScenesPresetTool.LoadFullPreset();
            EditorSceneManager.OpenScene("Path/To/Scene.unity");
        }

        if (GUILayout.Button(new GUIContent("Reimport files", "tooltip")))
        {
            if (EditorWarningPopup.UserAccepted())
            {
                YourReimporter.ReimportFragileFiles();
            }
        }

        if (GUILayout.Button(new GUIContent("Tools windows", "tooltip")))
        {
            EditorToolsWindow.Init();
        }
    }
}
```

Custom editor window with seperate tabs:
```cs
public class EditorToolsWindow : OdinEditorWindow
{
    [SerializeField, HideLabel, TabGroup("WEAPONS")]
    private WeaponsToolTab _weaponsToolTab = new();

    [SerializeField, HideLabel, TabGroup("OTHER")]
    private OtherToolsTab _otherTab = new();

    public static void Init()
    {
        GetWindow<EditorToolsWindow>().Show(true);
    }
}

[Serializable]
public class WeaponsToolTab
{
    [Button(ButtonSizes.Large)]
    public void PrintWeaponStats() { }
}

[Serializable]
public class OtherToolsTab
{
    [Button(ButtonSizes.Large)]
    public void DoStuff() { }
}
```

Tool for changing aspect ratio of the game window in the editor:
```cs
public class GameWindowAspectRatioTool
{
    private const int OFFSET = 7;
    private const int USER_RATIOS_COUNT = 5;
    private static int _currentIndex = OFFSET;

    [MenuItem("Tools/Switch aspect ratio %["))]
    public static void SwitchAspectRatio()
    {
        var assembly = typeof(UnityEditor.Editor).Assembly;
        var gameView = assembly.GetType("UnityEditor.GameView");
        var instance = EditorWindow.GetWindow(gameView);
        var method = gameView.GetMethod("SizeSelectionCallback");
        method.Invoke(instance, new object[] { _currentIndex });

        if (++_currentIndex >= OFFSET + USER_RATIOS_COUNT)
        {
            _currentIndex = OFFSET;
        }
    }
}
```

Tool for reimporting assets without restarting the editor:
```cs
public void Reimport(string[] assetPaths)
{
    var importOptions = ImportAssetOptions.ForceUpdate | 
        ImportAssetOptions.ImportRecursive | 
        ImportAssetOptions.ForceSynchronousImport;

    AssetDatabase.StartAssetEditing();

    foreach (var path in assetPaths)
    {
        AssetDatabase.ImportAsset(path, importOptions);
    }

    AssetDatabase.StopAssetEditing();
    
    AssetDatabase.Refresh(importOptions);
}
```

Tool for loading and unloading packages:
```cs
public class PackagesTab
{
    private List<string> ADDITIONAL_PACKAGES = new()
    {
        "com.unity.2d.sprite@1.0.0",
        "com.unity.probuilder@5.0.4",
        "com.unity.sharp-zip-lib@1.3.2-preview",
        "com.unity.formats.fbx@4.1.2"
    };

    [Button(ButtonSizes.Large)]
    public void InstallAdditionalPackages()
    {
        foreach (var package in ADDITIONAL_PACKAGES)
        {
            UnityEditor.PackageManager.Client.Add(package);
        }
    }

    [Button(ButtonSizes.Large)]
    public void UninstallAdditionalPackages()
    {
        foreach (var package in ADDITIONAL_PACKAGES)
        {
            UnityEditor.PackageManager.Client.Remove(package);
        }
    }
}
```

Inspector extensions for better UX in a custom component:
```cs
public class OctreeGraphComponent : MonoBehaviour
{
    [FoldoutGroup("Gizmos")]
    public bool OctreeBounds;

    [FoldoutGroup("Gizmos")]
    private bool DrawBakeResult;

    [FoldoutGroup("Gizmos"), EnableIf(nameof(DrawBakeResult))]
    private int CubeSize;

    [FoldoutGroup("Gizmos"), EnableIf(nameof(DrawBakeResult))]
    [EnumToggleButtons]
    private GizmoDisplayMode DisplayMode;

    [FoldoutGroup("Gizmos"), EnableIf(nameof(DrawBakeResult))]
    private bool DrawJustOneCube = false;

    [FoldoutGroup("Gizmos"), EnableIf(nameof(DrawJustOneCube))]
    [InlineButton(nameof(NextCube), SdfIconType.ArrowRight, "")]
    [InlineButton(nameof(PreviousCube), SdfIconType.ArrowLeft, "")]
    private int CubeIndex;

    private void NextCube() => CubeIndex++;

    private void PreviousCube() => CubeIndex--;

    private void OnDrawGizmosSelected() => DrawGizmos();
}
```
