# Fire Emblem: Fortune's Weave — DLSS Quality / DLAA patches

Exefs patches for **Fire Emblem: Fortune's Weave** (`04007EF01F8AE000`). By default the game's DLSS works in Performance mode (it renders at half the output size and upscales 2×). These patches make the game render more pixels before upscaling:

| Patch | Render resolution | Docked output 2560×1440 | Handheld output 1920×1080 | Cost |
|---|---|---|---|---|
| (stock) | 1/2 of output (Performance) | 1280×720 → 2560×1440 | 960×540 → 1920×1080 | — |
| **DLSS Quality** | 2/3 of output | 1706×960 → 2560×1440 | 1280×720 → 1920×1080 | moderate |
| **DLAA** | equal to output | 2560×1440 → 2560×1440 | 1920×1080 → 1920×1080 | high |

You'll see a sharper, more stable image: less shimmer on foliage and tiles, and better text and edges. The normal DLSS bump.

Obviously, no game code here. 

The game natively runs the Switch 2 version of NVIDIA’s DLSS, driven through the console’s graphics API (NVN2). So far this variant, deemed 'tiny DLSS' by Digital Foundry, is best known from games like FastFusion. One routine sets the network up, and a second one runs it each frame. Picking apart the game for this mod validates Digital Foundry's findings: 
Handheld upscales 960x540 --> 1920x1080, Docked upsacles 1280x720 --> 2560x1440.  The 'network' is a series of GPU compute passes. Its maths uses the console’s tensor-core matrix instructions (HMMA). Its inputs are low-resolution images, depth, motion vectors and sub-pixel jitter. Eglantine recognises the game’s DLSS work on the GPU and swaps the console network for NVIDIA DLSS on your PC (or FSR 3.1 on cards without DLSS), feeding it those same inputs. 
However, the emulator stays fixed at a 1:2 ratio. 
SpecialK cannot be used to fix this because the resolution does not come from a request to DLSS. This mod edits the game's resolution tables. The DLSS Quality patch raises the render resolution to two-thirds of the output (1280×720 in handheld, 1706×960 docked).  Eglantine sees the new ratio and switches DLSS to the matching mode automatically, so Quality appears as mode 3 in logs. 

Screens:

https://ibb.co/5x2FjXYL

https://ibb.co/vvk1Sbp8

https://ibb.co/v4hxcb5Z

https://ibb.co/WNdM7Mns

https://ibb.co/DPNhgxsT

## Requirements

- **Game build:** these patches apply to main build ID `DB93B0125F4850601D61C3EDDE012F8EB2ECFE77` only. On any other version the emulator silently skips them. To check, look for `Applying IPSwitch patch from mod "...DLSS Quality"` in the log (see Troubleshooting).
- **Emulator:** Eglantine (the standard Windows build `eglantine-windows-x86_64.zip`, **not** the `-pgo` build, which has no NVIDIA DLSS support). Eden-based builds that load `.pchtxt` exefs mods should also apply the patch.
- **NVIDIA DLSS (recommended):** an RTX GPU, with "Use NVIDIA DLSS" enabled and Streamline's `sl.interposer.dll` installed (use Eglantine's DLSS panel). Without NVIDIA DLSS, Eglantine upscales with FSR 3.1 instead, and the patch still helps by feeding FSR more pixels.
- **Lichii:** at the time of writing, Lichii doesn't detect this game's DLSS pass on every version of the game, so it runs the game's DLSS network.
## Install

1. In Eglantine, right-click the game → **Open Mod Data Location**. This opens `…\eden\load\04007EF01F8AE000\` (or `…\user\load\04007EF01F8AE000\` for a portable install).
2. Copy **one** of the two folders into it:
   - `Fire Emblem Fortune's Weave DLSS Quality` (recommended), or
   - `Fire Emblem Fortune's Weave DLAA`
3. Right-click the game → **Properties → Add-ons** and confirm it's ticked. **Don't enable both.** They patch the same instructions, and only one can win.
4. Boot the game.

## Pick your settings by monitor

Your final image is the game's output multiplied by Eglantine's **Resolution scale**:
- **Docked mode:** the game outputs 2560×1440.
- **Handheld mode:** the game outputs 1920×1080.

Choose the combination whose output matches your monitor exactly, so nothing is rescaled afterwards.

| Your monitor | Mode | Resolution scale | Final output | DLSS renders at (Quality / DLAA) | Notes |
|---|---|---|---|---|---|
| 1920×1080 | Handheld | 1× | 1920×1080 | 1280×720 / 1920×1080 | Native match. DLAA is affordable here. |
| 2560×1440 | Docked | 1× | 2560×1440 | 1706×960 / 2560×1440 | Native match. Best default for most RTX cards. |
| 3440×1440 (ultrawide) | Docked | 1× | 2560×1440 | 1706×960 / 2560×1440 | The game is 16:9, so expect pillarbox bars. |
| 3840×2160 (4K) | Docked | 1.5× | 3840×2160 | 2559×1440 / 3840×2160 | Native match. Quality recommended. |
| 3840×2160 (4K) | Handheld | 2× | 3840×2160 | 2560×1440 / 3840×2160 | Same output. Handheld mode may load some lower-detail assets. |
| 4K, supersampled | Docked | 2× | 5120×2880 | 3412×1920 / 5120×2880 | Downsampled to 4K. Very heavy; 60-80% usage on a 4090 |

- **Not sure which to pick?** Use **DLSS Quality** at the native-match row for your monitor.
- **Want maximum sharpness at 1080p or 1440p with GPU headroom?** Use **DLAA** instead. I recommend that you pair this with Eglantine's built-in Frame Generation and Special K's HDR. You do not need to uncap the framerate with ctrl+u for Frame Generation to take effect.

## Recommended companion settings (Eglantine)

- Graphics → **Use NVIDIA DLSS**: on.
- **DLSS model preset:** Eglantine forces preset K by default. Set `EDEN_NX2_DLSS_PRESET` before launch to change it.
- Resolution scale: from the table above.
- Optional: a 60 fps patch and Eglantine's frame generation (2× multiplier) stack fine with this patch.

## Troubleshooting

Logs are in `…\eden\log\eden_log.txt` (or `…\user\log\` for a portable install).

- **Is the patch applied?** Look for `Applying IPSwitch patch from mod "Fire Emblem Fortune's Weave DLSS Quality"`.
- **Is NVIDIA DLSS running?** Look for `NVIDIA DLSS (Streamline): <input> -> <output>, mode 3, preset 11`.
  - `mode 3` means Quality, and `mode 6` means DLAA.
  - With the stock game you'd see `mode 1` (Performance).
- **No DLSS line at all?** You may have the `-pgo` Eglantine build, NVIDIA DLSS may be off, or `sl.interposer.dll` may be missing. Fix whichever applies.
- **Patch line missing?** Your game version differs from the build ID above.
- **Brief flicker on light sources right after loading?** It clears once DLSS has built up a few frames of history, for example after entering and leaving a battle.

## Credits / version

- v1.0 (2026-11-08): DLSS Quality and DLAA for build `DB93B0125F4850601D61C3EDDE012F8EB2ECFE77`.
- Tested on Eglantine `nx2-7adcecc6d2` and an RTX 4090, docked and handheld.
