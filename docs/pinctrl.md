
rk3328.dtsi文件内为提供CLK32OUT的pinctrl定义。

wifi模块需要的32K Clock可以由PMU提供，也可以用CPU提供。

CPU的32K Clock输出引脚是V14，GPIO1_D4/CLK32KOUT_M1，位于原理图符号U800E。

dtsi的pinctrl block中需增加该引脚的pinctrl定义：

```
1856     rtc {
1857       rtc_32k: rtc-32k {
1858         rockchip,pins =
1859           <1 RK_PD4 1 &pcfg_pull_none>;
1860       };
1861     };
```

pinctrl documents:

Documentation/devicetree/bindings/pinctrl/pinctrl-bindings.txt
Documentation/devicetree/bindings/pinctrl/pinctrl-bindings.txt