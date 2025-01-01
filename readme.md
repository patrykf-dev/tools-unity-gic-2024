On 27.11.2024 I had the pleasure of presenting my lecture _Tool your way to inner peace in Unity development_ at the [Game Industry Conference](https://gic.gd/) in Poznan.
It was a great experience!
In this repo I'm sharing the [slides](https://github.com/patrykf-dev/tools-unity-gic-2024/blob/main/slides.pdf) and all the code samples I used during my talk.
If you have any questions, feel free to ask them as issues in this repository.

![Photo from my lecture](https://github.com/patrykf-dev/tools-unity-gic-2024/blob/main/gic-pic.jpg?raw=true)

## Description
I explored a general approach to tool development.
Emphasis was placed on best practices, including good UX design principles.
I showcased real-life examples, including tools from my solo projects and custom solutions built for a commercial project, [StarHeroes](https://starheroes.io/).

## Code samples

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
