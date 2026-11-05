# Mouse Macro Recorder

A small Windows program that records your mouse clicks and key presses, lets you edit the pauses between them, and plays them back as often as you like, for anyone who repeats the same clicks every day.

## What it does
- Records left mouse clicks (screen position) and every key you press and release.
- Stores the pause before each step, so playback keeps your timing.
- Plays the macro back faster or slower, and repeats it as many times as you set.
- Lets you change or add pauses, and delete steps, in a list.
- Saves macros to JSON files and loads them again.
- Works with global hotkeys (F9, F10, Esc), even when the window is behind other programs.

## What you need
- Windows 10 or 11 (it is made and tested for Windows).
- Python 3.8 or newer, with tkinter (included in the python.org installer for Windows).
- The packages `pyautogui` and `pynput` (in `requirements.txt`).

## Install
1. Download this project (green **Code** button, then **Download ZIP**) and unzip it.
2. Open a Command Prompt in the project folder.
3. Run:
   ```
   pip install -r requirements.txt
   ```

## How to use
1. Start the program:
   ```
   python mouse_macro_recorder.py
   ```
2. Press **F9** (or click **Start Recording (F9)**). The status line says "Recording...".
3. Do the clicks and typing you want to repeat.
4. Press **F9** again (or click **Stop Recording (F9)**). The steps appear in the list. If you stopped with the button, that last click on the button is removed by itself.
5. Press **F10** (or click **Play Macro (F10)**) to play it back.
6. Press **Esc** at any time to stop playback or recording.
7. Click **Save Macro** to keep it in a `.json` file, and **Load Macro** to open it again later.

To edit the macro:
- Select a step, type a number in **New delay for selected (seconds):** and click **Update Delay**.
- Select one or more steps, type a number in **Add delay to selected (+seconds):** (it can be negative, such as `-0.05`) and click **Add to Selected**.
- Select steps and click **Delete Selected**, or click **Clear All** to start over.

## Settings and options
| Setting | What it does |
|---|---|
| Playback speed (1.0 = normal) | `1.0` plays at the recorded speed, `2.0` twice as fast, `0.5` half as fast. The default is **1.3** (a little faster than recorded). |
| Loop count | How many times the macro runs in a row. The default is 1. |

Hotkeys (they work in any program while the recorder is open):

| Key | What it does |
|---|---|
| F9 | Start or stop recording |
| F10 | Play the macro |
| Esc | Stop recording, or stop playback |

F9, F10 and Esc are never recorded.

### File format
A saved macro looks like this (version 3):

```json
{
  "version": 3,
  "events": [
    { "type": "mouse_click", "delay_before": 0.0, "x": 523, "y": 742 },
    { "type": "key_down", "delay_before": 0.84, "key": "a" },
    { "type": "key_up", "delay_before": 0.09, "key": "a" },
    { "type": "key_down", "delay_before": 0.5, "key": "Key.enter" },
    { "type": "key_up", "delay_before": 0.07, "key": "Key.enter" }
  ]
}
```

`delay_before` is the pause in seconds before the step. Special keys are saved as `Key.<name>` (for example `Key.ctrl`) and other keys as their character, or as `KeyCode.vk.<number>`. Older files with only `x`, `y` and `delay_before` load as mouse clicks.

## Safety notes
- **It records every key you press while recording, including passwords.** The keys are saved as plain text in the JSON file. Do not type passwords or other private text while recording, and do not share macro files that contain them.
- **Playback clicks and types for real.** It can change or delete things in the program under the mouse. Try a new macro with **Loop count** 1 first.
- **Emergency stop:** press **Esc**, or move the mouse quickly into a corner of the screen. The corner stop is the pyautogui safety feature; the recorder then shows "Playback stopped".
- Positions are absolute screen positions. If a window moves, or you change screens or display scaling, the clicks land in the wrong place. Record again in that case.
- Nothing is sent anywhere. Macros are only saved where you choose.

## Troubleshooting
- **The clicks land in the wrong place.** Keep the windows where they were when you recorded, and use the same screen and display scaling. Otherwise record again.
- **"Playback stopped by the safety stop"**: the mouse reached a screen corner during playback. Move the mouse away from the corners and play again.
- **Hotkeys do nothing in some programs.** Programs that run as administrator ignore hotkeys from normal programs. Start the recorder as administrator too.
- **"No module named pynput" or "pyautogui"**: run `pip install -r requirements.txt`.
- **A key stays pressed after you stop playback.** Press and release that key once on the keyboard.

## How it works
The window is built with tkinter. Recording uses global mouse and keyboard hooks from `pynput`, and saves each step with the time since the previous step. Playback runs on a background thread so the window stays responsive: it waits each pause (divided by the speed) in short steps so Esc stops it quickly, then clicks with `pyautogui` and presses keys with the `pynput` keyboard controller. The process is DPI-aware, so positions match on screens with 125% or 150% scaling.

## License

MIT. See [LICENSE](LICENSE).
