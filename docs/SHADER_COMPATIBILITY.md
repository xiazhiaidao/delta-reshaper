# Shader compatibility / 光影兼容

Delta Reshaper 1.2.1 uses the shaderpack's native rendering path when Oculus/Iris reports that a pack is active. The mod does not copy the shaderpack framebuffer for its custom reflections. Without a shaderpack, its existing custom metal/iridescence rendering remains enabled.

LabPBR maps provide independent blade and grip materials. This is standard material support, not a guarantee for every shaderpack or loader version. Shaderpack configuration, reflection implementation, exposure, bloom and temporal filtering affect the final result. The no-shader custom thin-film effect is not reproduced identically by every shaderpack.

## Tested setup

Minecraft 1.20.1; Forge 47.4.13; Oculus 1.8.0; TaCZ 1.1.8-hotfix2 and LR Tactical 0.4.3.

| Shaderpack | Settings | Verified behavior |
|---|---|---|
| Complementary Reimagined r5.9.3 | `RP_MODE=3`, `POM=false` | Native LabPBR maps, inspect emission, basic fallback, pack off/on |
| Photon v1.3b | `NORMAL_MAPPING=true`, `SPECULAR_MAPPING=true`, `POM=false` | Native maps, inspect emission without an opaque black overlay, basic fallback, pack off/on |
| Sildur's Vibrant v1.51 Extreme-VL | Default | Complete base appearance, inspect emission, basic fallback, pack off/on; this version does not decode the mod's LabPBR metal/smoothness channels |
| Derivative 25.0.1 | Not supported on this setup | Oculus rejects render target index 31 (only 16 targets supported). Reproduced with the Reshaper add-on removed |

## Settings

**F8 → 光影材质 / Shader material** switches between LabPBR and the basic path. Basic mode uses the ordinary model material and disables extra PBR submission and emission layers. It does not alter animations or combat. In a shaderpack, reflections are controlled by the pack; the no-shader reflection slider is disabled while a pack is active.

Installing the JAR does not automatically edit your shaderpack options. Choose LabPBR or enable normal/specular texture mapping in your pack. Global material settings can affect other blocks as well. No shaderpack ZIPs are modified or redistributed.

不开光影时保留原有自定义虹光、刀刃反射和检视线光。开光影时，材质的实际效果由光影决定；不支持 LabPBR 的光影通常只能显示基础贴图。出现显示异常时先按 F8 切到基础材质，再确认光影设置。

## Validation and limits

- Native tests cover three supported packs, real 2048px material-map loading in Complementary/Photon, inspect emission, live off/on switching, actual F8 button persistence, and a separate launch without Oculus.
- Modern and legacy Iris names are checked through the public API with internal-method fallbacks. Eight isolated API tests passed; real old Oculus/Iris binaries have not been tested. OptiFine and other loaders are not verified.
- Shaderpack state is queried before capture/drawing. An unknown or failing loader probe disables scene sampling instead of trusting stale state.
- F3+T no longer dereferences a missing Lua animation context. In the isolated reload test, LR Tactical's context did not automatically recover even after re-equipping; the test driver used the framework's `tryInit` to verify that the dynamic emission texture could be recreated. Automatic post-reload animation recovery remains unresolved. Re-enter the game if animations stop after a resource reload.
- Native submission exceptions can fall back to base textures. This does not repair shader compilation failures, other mods' crashes or graphics-driver failures.
- Bloom depends on the shaderpack. Inspect emission does not cast a dynamic light into the world.

## What changed in 1.2.1

- Optional modern/legacy Iris/Oculus detection, using the public API where available.
- Native blade/body material maps and a source-timed cyan inspect pulse for shaderpack rendering.
- Correct transparent pixels outside the pulse mask, fixing Photon's opaque black knife overlay.
- F8 basic-material fallback, queued settings saves and clear reflection-control behavior.
- Null-context protection and dynamic texture reset during resource reloads.

Models, geometry, animations, input rules and audio remain identical to the native-tested build. Release packaging only updates the version and preserves the existing public license/credits.

![Native comparison](../preview/three-shader-native.jpg)
