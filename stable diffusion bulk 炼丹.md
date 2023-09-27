有插件提供了类似功能 https://github.com/ArtVentureX/sd-webui-agent-scheduler

cons
- 不能直接用adtailer
- 还是需要手动一个一个添加

### 引入 playwright
stable diffusion虽然提供了完善的api供调用，但是实际用户操作的界面还是更贴合使用习惯。
通过playwright或者selenium等工具，利用ipython特性，启动jupyter等工具启动，可以实时看到操作到了哪一步，并且有error发生时，可以当时停止进行修复。
完美兼容adtailer等

```python
代码待拷贝
```
bulk炼丹整体不复杂，可以很好的去评价比如
- 用户脸模型
- base模型
- sampler
- step


既然都是炼丹了，那就别让计算机闲着，每一个参数按照排列组合进行炼丹，最后形成成熟的预设，就是可用的方案了