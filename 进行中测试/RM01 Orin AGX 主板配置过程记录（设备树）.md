# RM01 Orin AGX 主板配置

## 安装完成SDK Manager之后

创建设备树

``` bash
# 定位官方dts文件Linux_for_Tegra/source/hardware/nvidia/t23x/nv-public
cd ~/nvidia/nvidia_sdk/RMinte/Linux_for_Tegra/source/hardware/nvidia/t23x/nv-public

# 复制需要的dts和dtsi到Linux_for_Tegra/source/kernel-devicetree
cp ./tegra234-p3737-0000+p3701-0000.dts ~/nvidia/nvidia_sdk/RMinte/Linux_for_Tegra/source/kernel-devicetree/
cp ./tegra234.dtsi ~/nvidia/nvidia_sdk/RMinte/Linux_for_Tegra/source/kernel-devicetree/
cp ./tegra234-p3701.dtsi ~/nvidia/nvidia_sdk/RMinte/Linux_for_Tegra/source/kernel-devicetree/
cp ./tegra234-p3701-0000.dtsi ~/nvidia/nvidia_sdk/RMinte/Linux_for_Tegra/source/kernel-devicetree/
cp ./tegra234-p3737-0000.dtsi ~/nvidia/nvidia_sdk/RMinte/Linux_for_Tegra/source/kernel-devicetree/

# 复制需要的dts和dtsi到Linux_for_Tegra/source/kernel-devicetree
cp ./tegra234-p3737-0000+p3701-0000.dts ~/nvidia/nvidia_sdk/JetPack_6.2_Linux_JETSON_AGX_ORIN_TARGETS/Linux_for_Tegra/source/kernel-devicetree/
cp ./tegra234.dtsi ~/nvidia/nvidia_sdk/JetPack_6.2_Linux_JETSON_AGX_ORIN_TARGETS/Linux_for_Tegra/source/kernel-devicetree/
cp ./tegra234-p3701.dtsi ~/nvidia/nvidia_sdk/JetPack_6.2_Linux_JETSON_AGX_ORIN_TARGETS/Linux_for_Tegra/source/kernel-devicetree/
cp ./tegra234-p3701-0000.dtsi ~/nvidia/nvidia_sdk/JetPack_6.2_Linux_JETSON_AGX_ORIN_TARGETS/Linux_for_Tegra/source/kernel-devicetree/
cp ./tegra234-p3737-0000.dtsi ~/nvidia/nvidia_sdk/JetPack_6.2_Linux_JETSON_AGX_ORIN_TARGETS/Linux_for_Tegra/source/kernel-devicetree/
```

其他可以用于设备树配置的文件也在该文件夹下：

``` bash
.
├── Makefile
├── include
│   ├── kernel
│   │   ├── dt-bindings
│   │   │   ├── clock
│   │   │   │   └── tegra234-clock.h
│   │   │   ├── gpio
│   │   │   │   ├── gpio.h
│   │   │   │   └── tegra234-gpio.h
│   │   │   ├── input
│   │   │   │   ├── gpio-keys.h
│   │   │   │   └── linux-event-codes.h -> ../../uapi/linux/input-event-codes.h
│   │   │   ├── interrupt-controller
│   │   │   │   ├── arm-gic.h
│   │   │   │   └── irq.h
│   │   │   ├── mailbox
│   │   │   │   └── tegra186-hsp.h
│   │   │   ├── memory
│   │   │   │   └── tegra234-mc.h
│   │   │   ├── pinctrl
│   │   │   │   ├── pinctrl-tegra-io-pad.h
│   │   │   │   └── pinctrl-tegra.h
│   │   │   ├── power
│   │   │   │   └── tegra234-powergate.h
│   │   │   ├── reset
│   │   │   │   └── tegra234-reset.h
│   │   │   ├── sound
│   │   │   │   └── rt5640.h
│   │   │   └── thermal
│   │   │       ├── tegra234-bpmp-thermal.h
│   │   │       └── thermal.h
│   │   └── uapi
│   │       └── linux
│   │           └── input-event-codes.h
│   ├── nvidia-oot
│   │   └── dt-bindings
│   │       ├── interrupt
│   │       │   └── tegra234-irq.h
│   │       ├── p2u
│   │       │   └── tegra234-p2u.h
│   │       └── sound
│   │           └── tegra-asoc-dais.h
│   └── platforms
│       └── dt-bindings
│           ├── tegra234-p3737-0000+p3701-0000.h
│           ├── tegra234-p3740-0002-p3701-0008.h
│           └── tegra234-p3767-0000-common.h
├── nv-platform
│   ├── Makefile
│   ├── tegra234-camera-p3785.dtsi
│   ├── tegra234-dcb-p3737-0000-p3701-0000.dtsi
│   ├── tegra234-p3701-0000-prod-overlay.dtsi
│   ├── tegra234-p3701-0000.dtsi
│   ├── tegra234-p3701-0005.dtsi
│   ├── tegra234-p3701-0008.dtsi
│   ├── tegra234-p3737-0000+p3701-0000-nv.dts
│   ├── tegra234-p3737-0000+p3701-0004-nv.dts
│   ├── tegra234-p3737-0000+p3701-0005-nv.dts
│   ├── tegra234-p3737-0000+p3701-0008-nv.dts
│   ├── tegra234-p3737-0000+p3701-xxxx-nv-common.dtsi
│   ├── tegra234-p3737-0000.dtsi
│   ├── tegra234-p3740-0002+p3701-0008-nv-common.dtsi
│   ├── tegra234-p3740-0002+p3701-0008-nv-safety.dts
│   ├── tegra234-p3740-0002+p3701-0008-nv.dts
│   ├── tegra234-p3740-0002+p3701-0008-safety.dtsi
│   ├── tegra234-p3740-0002.dtsi
│   ├── tegra234-p3767-0000.dtsi
│   ├── tegra234-p3768-0000+p3767-0000-nv-px1.dts
│   ├── tegra234-p3768-0000+p3767-0000-nv-super.dts
│   ├── tegra234-p3768-0000+p3767-0000-nv-taylor-high.dts
│   ├── tegra234-p3768-0000+p3767-0000-nv-taylor-low.dts
│   ├── tegra234-p3768-0000+p3767-0000-nv.dts
│   ├── tegra234-p3768-0000+p3767-0001-nv-super.dts
│   ├── tegra234-p3768-0000+p3767-0001-nv.dts
│   ├── tegra234-p3768-0000+p3767-0003-nv-super.dts
│   ├── tegra234-p3768-0000+p3767-0003-nv.dts
│   ├── tegra234-p3768-0000+p3767-0004-nv-super.dts
│   ├── tegra234-p3768-0000+p3767-0004-nv.dts
│   ├── tegra234-p3768-0000+p3767-0005-nv-super.dts
│   ├── tegra234-p3768-0000+p3767-0005-nv.dts
│   ├── tegra234-p3768-0000+p3767-xxxx-nv-common.dtsi
│   └── tegra234-p3768-0000.dtsi
├── nv-soc
│   ├── tegra234-base-overlay.dtsi
│   ├── tegra234-overlay.dtsi
│   ├── tegra234-soc-audio-dai-links.dtsi
│   ├── tegra234-soc-camera.dtsi
│   ├── tegra234-soc-display-overlay.dtsi
│   ├── tegra234-soc-overlay.dtsi
│   ├── tegra234-soc-prod-overlay.dtsi
│   ├── tegra234-soc-safetyservice-fsicom.dtsi
│   ├── tegra234-soc-thermal-shutdown.dtsi
│   ├── tegra234-soc-thermal-slowdown-cluster.dtsi
│   ├── tegra234-soc-thermal-slowdown-corepair.dtsi
│   ├── tegra234-soc-thermal-trip-event.dtsi
│   └── tegra234-soc-thermal.dtsi
├── optee-dts
│   └── tegra234-optee.dts
├── overlay
│   ├── Makefile
│   ├── tegra-optee.dts
│   ├── tegra234-audio-overlay.dts
│   ├── tegra234-camera-ar0234-a00.dtsi
│   ├── tegra234-camera-e3331-a00.dtsi
│   ├── tegra234-camera-e3333-a00.dtsi
│   ├── tegra234-camera-imx185-a00.dtsi
│   ├── tegra234-camera-imx274-dual.dtsi
│   ├── tegra234-camera-imx390-a00.dtsi
│   ├── tegra234-camera-p3762-a00.dtsi
│   ├── tegra234-camera-p3783-a00.dtsi
│   ├── tegra234-camera-rbpcv2-imx219.dtsi
│   ├── tegra234-carveouts.dts
│   ├── tegra234-dcb-p3767-0000-hdmi.dts
│   ├── tegra234-p3737-0000+p3701-0000-as-p3701-0004.dts
│   ├── tegra234-p3737-0000+p3701-0000-as-p3767-0000.dts
│   ├── tegra234-p3737-0000+p3701-0000-as-p3767-0001.dts
│   ├── tegra234-p3737-0000+p3701-0000-as-p3767-0003.dts
│   ├── tegra234-p3737-0000+p3701-0000-as-p3767-0004.dts
│   ├── tegra234-p3737-0000+p3701-0000-audio-adafruit-sph0645lm4h.dts
│   ├── tegra234-p3737-0000+p3701-0000-audio-adafruit-uda1334a.dts
│   ├── tegra234-p3737-0000+p3701-0000-audio-fe-pi.dts
│   ├── tegra234-p3737-0000+p3701-0000-audio-respeaker-4-mic-array.dts
│   ├── tegra234-p3737-0000+p3701-0000-audio-respeaker-4-mic-lin-array.dts
│   ├── tegra234-p3737-0000+p3701-0000-csi.dts
│   ├── tegra234-p3737-0000+p3701-0000-dynamic.dts
│   ├── tegra234-p3737-0000+p3701-0000-hdr40.dts
│   ├── tegra234-p3737-0000+p3701-0000-m2ke.dts
│   ├── tegra234-p3737-0000-camera-imx185-a00.dtsi
│   ├── tegra234-p3737-0000-camera-imx274-dual.dtsi
│   ├── tegra234-p3737-camera-dual-hawk-ar0234-e3653-overlay.dts
│   ├── tegra234-p3737-camera-dual-imx274-overlay.dts
│   ├── tegra234-p3737-camera-e3331-overlay.dts
│   ├── tegra234-p3737-camera-e3333-overlay.dts
│   ├── tegra234-p3737-camera-eCAM130A-overlay.dts
│   ├── tegra234-p3737-camera-imx185-overlay.dts
│   ├── tegra234-p3737-camera-imx390-addr-0x21-overlay.dts
│   ├── tegra234-p3737-camera-imx390-overlay.dts
│   ├── tegra234-p3737-camera-modules.dtsi
│   ├── tegra234-p3737-camera-p3762-a00-1Hawk-overlay.dts
│   ├── tegra234-p3737-camera-p3762-a00-2Hawk-overlay.dts
│   ├── tegra234-p3737-camera-p3762-a00-3Hawk-3Owl-overlay.dts
│   ├── tegra234-p3737-camera-p3762-a00-4Hawk-overlay.dts
│   ├── tegra234-p3737-camera-p3762-a00-4Owl-overlay.dts
│   ├── tegra234-p3737-camera-p3762-a00-overlay.dts
│   ├── tegra234-p3737-camera-p3762-a00.dtsi
│   ├── tegra234-p3740-0002+p3701-0008-hdr20.dts
│   ├── tegra234-p3740-0002+p3701-0008-m2kb.dts
│   ├── tegra234-p3740-0002+p3701-0008-m2ke.dts
│   ├── tegra234-p3740-0002-p3701-0008-csi.dts
│   ├── tegra234-p3740-camera-p3783-a00-overlay.dts
│   ├── tegra234-p3767-0000+p3509-a02-audio-adafruit-sph0645lm4h.dts
│   ├── tegra234-p3767-0000+p3509-a02-audio-adafruit-uda1334a.dts
│   ├── tegra234-p3767-0000+p3509-a02-audio-fe-pi.dts
│   ├── tegra234-p3767-0000+p3509-a02-audio-respeaker-4-mic-array.dts
│   ├── tegra234-p3767-0000+p3509-a02-audio-respeaker-4-mic-lin-array.dts
│   ├── tegra234-p3767-0000+p3509-a02-csi.dts
│   ├── tegra234-p3767-0000+p3509-a02-hdr40.dts
│   ├── tegra234-p3767-0000+p3509-a02-m2ke.dts
│   ├── tegra234-p3767-0000+p3768-0000-csi.dts
│   ├── tegra234-p3767-0000-common-hdr40.dtsi
│   ├── tegra234-p3767-camera-p3768-imx219-A.dts
│   ├── tegra234-p3767-camera-p3768-imx219-C.dts
│   ├── tegra234-p3767-camera-p3768-imx219-dual.dts
│   ├── tegra234-p3767-camera-p3768-imx219-imx477.dts
│   ├── tegra234-p3767-camera-p3768-imx477-A.dts
│   ├── tegra234-p3767-camera-p3768-imx477-C.dts
│   ├── tegra234-p3767-camera-p3768-imx477-dual-4lane.dts
│   ├── tegra234-p3767-camera-p3768-imx477-dual.dts
│   ├── tegra234-p3767-camera-p3768-imx477-imx219.dts
│   ├── tegra234-p3767-sku-handling.dtsi
│   ├── tegra234-p3768-0000+p3767-0000-dynamic.dts
│   └── tegra234-soc-overlay-mods.dtsi
├── staging
│   ├── Makefile
│   ├── tegra234-p3737-0000+p3701-0004.dts
│   ├── tegra234-p3737-0000+p3701-0005.dts
│   ├── tegra234-p3737-0000+p3701-0008.dts
│   ├── tegra234-p3768-0000+p3767-0001.dts
│   ├── tegra234-p3768-0000+p3767-0003.dts
│   └── tegra234-p3768-0000+p3767-0004.dts
├── tegra234-p3701-0000.dtsi
├── tegra234-p3701-0008.dtsi
├── tegra234-p3701.dtsi
├── tegra234-p3737-0000+p3701-0000.dts
├── tegra234-p3737-0000.dtsi
├── tegra234-p3740-0002+p3701-0008.dts
├── tegra234-p3740-0002.dtsi
├── tegra234-p3767.dtsi
├── tegra234-p3768-0000+p3767-0000.dts
├── tegra234-p3768-0000+p3767-0005.dts
├── tegra234-p3768-0000.dtsi
└── tegra234.dtsi

28 directories, 164 files
```

修改名称：

``` bash
# 进入Linux_for_Tegra/source/kernel-devicetree/目录
cd ~/nvidia/nvidia_sdk/RMinte/Linux_for_Tegra/source/kernel-devicetree/

# 进入Linux_for_Tegra/source/kernel-devicetree/目录
cd ~/nvidia/nvidia_sdk/JetPack_6.2_Linux_JETSON_AGX_ORIN_TARGETS/Linux_for_Tegra/source/kernel-devicetree/

# 修改dts文件名
mv tegra234-p3737-0000+p3701-0000.dts tegra234-rm01-v1.dts
```

将dts中不存在的设备disabled或删除文件如下：

tegra234-rm01-v1.dts

``` dts
// SPDX-License-Identifier: GPL-2.0
/dts-v1/;

#include <dt-bindings/input/linux-event-codes.h>
#include <dt-bindings/input/gpio-keys.h>

#include "tegra234-p3701-0000.dtsi"
#include "tegra234-p3737-0000.dtsi"

/ {
    model = "RMinte RM-01";
    compatible = "nvidia,p3701-0000", "nvidia,tegra234";

    aliases {
        serial0 = &tcu;
        serial1 = &uarta;
    };

    chosen {
        bootargs = "console=ttyTCU0,115200n8";
        stdout-path = "serial0:115200n8";
    };

    bus@0 {

		pmc@c360000 {
			#address-cells = <0>; /* 改为 0，因为没有子节点需要地址 */
			interrupt-controller; /* 明确声明为中断控制器 */
		};

        serial@3100000 {
            compatible = "nvidia,tegra194-hsuart";
            reset-names = "serial";
            status = "okay";
        };

        serial@31d0000 {
            current-speed = <115200>;
            status = "okay";
        };

        gpio@2200000 {
            #address-cells = <1>;
            #size-cells = <0>;
        };
        aconnect@2900000 {
            interrupt-controller@2a40000 {
                #address-cells = <1>;
                #size-cells = <0>;
            };
        };
        gpio@c2f0000 {
            #address-cells = <1>;
            #size-cells = <0>;
        };
        interrupt-controller@f400000 {
            #address-cells = <1>;
            #size-cells = <0>;
        };

        pwm@32a0000 {
            assigned-clocks = <&bpmp TEGRA234_CLK_PWM3>;
            assigned-clock-parents = <&bpmp TEGRA234_CLK_PLLP_OUT0>;
            status = "okay";
        };

        hda@3510000 {
            nvidia,model = "NVIDIA Jetson AGX Orin HDA";
            status = "disabled";
        };

        padctl@3520000 {
            status = "okay";
            pads {
                usb3 {
                    lanes {
                        usb3-0 { status = "okay"; };
                        usb3-1 { status = "okay"; };
                    };
                };
            };
            ports {
                usb3-0 { nvidia,usb2-companion = <0>; status = "okay"; };
                usb3-1 { nvidia,usb2-companion = <1>; status = "okay"; };
            };
        };

        usb@3610000 {
            status = "okay";
            phys = <&{/bus@0/padctl@3520000/pads/usb3/lanes/usb3-0}>,
                   <&{/bus@0/padctl@3520000/pads/usb3/lanes/usb3-1}>;
            phy-names = "usb3-0", "usb3-1";
        };

        pcie@141a0000 { /* NVMe */
            status = "okay";
            vddio-pex-ctl-supply = <&vdd_1v8_ls>;
            vpcie3v3-supply = <&vdd_3v3_pcie>;
            vpcie12v-supply = <&vdd_12v_pcie>;
            nvidia,pex-wake-gpio = <&gpio TEGRA234_MAIN_GPIO(AG, 1) 0>;
            phys = <&p2u_nvhs_0>, <&p2u_nvhs_1>, <&p2u_nvhs_2>,
                   <&p2u_nvhs_3>, <&p2u_nvhs_4>, <&p2u_nvhs_5>,
                   <&p2u_nvhs_6>, <&p2u_nvhs_7>;
            phy-names = "p2u-0", "p2u-1", "p2u-2", "p2u-3", "p2u-4",
                        "p2u-5", "p2u-6", "p2u-7";
        };

        ethernet@6800000 { /* RGMII */
            status = "okay";
            phy-mode = "rgmii";
            nvidia,phy-reset-gpio = <&gpio TEGRA234_MAIN_GPIO(AF, 2) 0>;
        };

		/delete-node/ pcie-ep@140e0000;
		/delete-node/ pcie-ep@141a0000;
		/delete-node/ pcie-ep@141c0000;
		/delete-node/ pcie-ep@141e0000;
    };

    gpio-keys {
        compatible = "gpio-keys";
        status = "okay";

        key-force-recovery {
            label = "Force Recovery";
            gpios = <&gpio TEGRA234_MAIN_GPIO(G, 0) GPIO_ACTIVE_LOW>;
            linux,input-type = <EV_KEY>;
            linux,code = <BTN_1>;
        };

        key-power {
            label = "Power";
            gpios = <&gpio_aon TEGRA234_AON_GPIO(EE, 4) GPIO_ACTIVE_LOW>;
            linux,input-type = <EV_KEY>;
            linux,code = <KEY_POWER>;
            wakeup-event-action = <EV_ACT_ASSERTED>;
            wakeup-source;
        };

        key-suspend {
            label = "Suspend";
            gpios = <&gpio TEGRA234_MAIN_GPIO(G, 2) GPIO_ACTIVE_LOW>;
            linux,input-type = <EV_KEY>;
            linux,code = <KEY_SLEEP>;
        };
    };

    pwm-fan {
        cooling-levels = <66 215 255>;
    };

    serial {
        status = "okay";
    };

    sound {
        status = "disabled";
    };

    thermal-zones {
        tj-thermal {
            cooling-maps {
                map-active-0 {
                    cooling-device = <&fan 0 1>;
                    trip = <&tj_trip_active0>;
                };

                map-active-1 {
                    cooling-device = <&fan 1 2>;
                    trip = <&tj_trip_active1>;
                };
            };
        };
    };
};
```

tegra234-p3737-0000.dtsi

``` dtsi
// SPDX-License-Identifier: GPL-2.0

#include <dt-bindings/sound/rt5640.h>

/ {
	compatible = "nvidia,p3737-0000";

	bus@0 {

		i2c@3160000 {
			status = "disabled";

			eeprom@56 {
				compatible = "atmel,24c02";
				reg = <0x56>;

				label = "system";
				vcc-supply = <&vdd_1v8_sys>;
				address-width = <8>;
				pagesize = <8>;
				size = <256>;
				read-only;
			};
		};

		i2c@31e0000 {
			status = "disabled";
		};

		pwm@3280000 {
			status = "okay";
		};

		pwm@32c0000 {
			status = "okay";
		};

		pwm@32f0000 {
			status = "okay";
		};
	};

	fan: pwm-fan {
		compatible = "pwm-fan";
		pwms = <&pwm3 0 45334>;
		#cooling-cells = <2>;
	};

	vdd_1v8_sys: regulator-vdd-1v8-sys {
		compatible = "regulator-fixed";
		regulator-name = "VDD_1V8_SYS";
		regulator-min-microvolt = <1800000>;
		regulator-max-microvolt = <1800000>;
		regulator-always-on;
	};
};

```

tegra234-p3701.dtsi

``` dtsi
// SPDX-License-Identifier: GPL-2.0

/ {
	compatible = "nvidia,p3701", "nvidia,tegra234";

	aliases {
		mmc0 = "/bus@0/mmc@3460000";
		mmc1 = "/bus@0/mmc@3400000";
	};
	
};
```

tegra234-p3701-0000.dtsi

``` dtsi
// SPDX-License-Identifier: GPL-2.0

#include "tegra234.dtsi"
#include "tegra234-p3701.dtsi"

/ {
	model = "NVIDIA Jetson AGX Orin";
	compatible = "nvidia,p3701-0000", "nvidia,tegra234";

	bus@0 {
		i2c@3160000 {
			status = "okay";

			eeprom@50 {
				compatible = "atmel,24c02";
				reg = <0x50>;

				label = "module";
				vcc-supply = <&vdd_1v8_hs>;
				address-width = <8>;
				pagesize = <8>;
				size = <256>;
				read-only;
			};
		};

		spi@3270000 {
			status = "okay";

			flash@0 {
				compatible = "jedec,spi-nor";
				reg = <0>;
				spi-max-frequency = <102000000>;
				spi-tx-bus-width = <4>;
				spi-rx-bus-width = <4>;
			};
		};

		mmc@3400000 {
			status = "okay";
			bus-width = <4>;
			cd-gpios = <&gpio TEGRA234_MAIN_GPIO(G, 7) GPIO_ACTIVE_LOW>;
			disable-wp;
		};

		mmc@3460000 {
			status = "okay";
			bus-width = <8>;
			non-removable;
		};

		padctl@3520000 {
			vclamp-usb-supply = <&vdd_1v8_ao>;
			avdd-usb-supply = <&vdd_3v3_ao>;

			ports {
				usb2-0 {
					vbus-supply = <&vdd_5v0_sys>;
				};

				usb2-1 {
					vbus-supply = <&vdd_5v0_sys>;
				};

				usb2-2 {
					vbus-supply = <&vdd_5v0_sys>;
				};

				usb2-3 {
					vbus-supply = <&vdd_5v0_sys>;
				};
			};
		};

		rtc@c2a0000 {
			status = "okay";
		};

		pmc@c360000 {
			nvidia,invert-interrupt;
		};
	};

	vdd_5v0_sys: regulator-vdd-5v0-sys {
		compatible = "regulator-fixed";
		regulator-name = "VIN_SYS_5V0";
		regulator-min-microvolt = <5000000>;
		regulator-max-microvolt = <5000000>;
		regulator-always-on;
		regulator-boot-on;
	};

	vdd_1v8_ls: regulator-vdd-1v8-ls {
		compatible = "regulator-fixed";
		regulator-name = "VDD_1V8_LS";
		regulator-min-microvolt = <1800000>;
		regulator-max-microvolt = <1800000>;
		regulator-always-on;
	};

	vdd_1v8_hs: regulator-vdd-1v8-hs {
		compatible = "regulator-fixed";
		regulator-name = "VDD_1V8_HS";
		regulator-min-microvolt = <1800000>;
		regulator-max-microvolt = <1800000>;
		regulator-always-on;
	};

	vdd_1v8_ao: regulator-vdd-1v8-ao {
		compatible = "regulator-fixed";
		regulator-name = "VDD_1V8_AO";
		regulator-min-microvolt = <1800000>;
		regulator-max-microvolt = <1800000>;
		regulator-always-on;
	};

	vdd_3v3_ao: regulator-vdd-3v3-ao {
		compatible = "regulator-fixed";
		regulator-name = "VDD_3V3_AO";
		regulator-min-microvolt = <3300000>;
		regulator-max-microvolt = <3300000>;
		regulator-always-on;
	};

	vdd_3v3_pcie: regulator-vdd-3v3-pcie {
		compatible = "regulator-fixed";
		regulator-name = "VDD_3V3_PCIE";
		regulator-min-microvolt = <3300000>;
		regulator-max-microvolt = <3300000>;
		gpio = <&gpio TEGRA234_MAIN_GPIO(H, 4) GPIO_ACTIVE_HIGH>;
		regulator-boot-on;
		enable-active-high;
	};

	vdd_12v_pcie: regulator-vdd-12v-pcie {
		compatible = "regulator-fixed";
		regulator-name = "VDD_12V_PCIE";
		regulator-min-microvolt = <12000000>;
		regulator-max-microvolt = <12000000>;
		gpio = <&gpio TEGRA234_MAIN_GPIO(A, 1) GPIO_ACTIVE_LOW>;
		regulator-boot-on;
	};

	thermal-zones {
		tj-thermal {
			polling-delay = <1000>;
			polling-delay-passive = <1000>;
			status = "okay";

			trips {
				tj_trip_active0: active-0 {
					temperature = <75000>;
					hysteresis = <4000>;
					type = "active";
				};

				tj_trip_active1: active-1 {
					temperature = <95000>;
					hysteresis = <4000>;
					type = "active";
				};
			};
		};
	};
};

```

尝试编译，部分节点强制覆盖了一些属性。

``` bash
# 预处理主 DTS 文件
cd Linux_for_Tegra
cpp -nostdinc \
    -I bootloader \
    -I source/kernel-devicetree \
    -I source/hardware/nvidia/t23x/nv-public/include/kernel \
    -undef -x assembler-with-cpp \
    source/kernel-devicetree/rm01-v1.dts > preprocessed.dts

# 编译预处理文件
dtc -I dts -O dtb -o ./bootloader/rm01-v1.dtb preprocessed.dts
```

文件位置确认

以下是你当前涉及的文件及其预期位置，请核对：

| 文件名                             | 位置                                                         | 状态               |
| ---------------------------------- | ------------------------------------------------------------ | ------------------ |
| tegra234-rm01-v1.dts               | Linux_for_Tegra/source/kernel-devicetree/                    | 已创建，需精简     |
| tegra234-rm01-pinmux-v1.dtsi       | Linux_for_Tegra/bootloader/                                  | 已生成，编译通过   |
| tegra234-rm01-padcfg-v1.dtsi       | Linux_for_Tegra/bootloader/                                  | 已生成，编译通过   |
| tegra234-rm01-gpio-default-v1.dtsi | Linux_for_Tegra/bootloader/                                  | 已修复，需确认编译 |
| tegra234-rm01-v1.conf              | Linux_for_Tegra/bootloader/                                  | 需确认是否存在     |
| tegra234-rm01-v1.dtb               | Linux_for_Tegra/bootloader/                                  | 待生成             |
| tegra234-p3701-0000.dtsi           | ~/nvidia/nvidia_sdk/RMinte/Linux_for_Tegra/source/hardware/nvidia/t23x/nv-public | 默认存在，无需修改 |
| tegra234.dtsi                      | ~/nvidia/nvidia_sdk/RMinte/Linux_for_Tegra/source/hardware/nvidia/t23x/nv-public | 默认存在，无需修改 |
| tegra234-gpio.h                    | Linux_for_Tegra/bootloader/                                  | 默认存在，无需修改 |



``` bash
cp bootloader/tegra234-rm01-v1.dtb /home/tom/nvidia/nvidia_sdk/RMinte/Linux_for_Tegra/kernel/dtb/tegra234-rm01-v1.dtb
# 环境迁移
cd JetPack_6.2_Linux_JETSON_AGX_ORIN_TARGETS/Linux_for_Tegra/
cp bootloader/tegra234-rm01-v1.dtb kernel/dtb/

cp ./RMinte/Linux_for_Tegra/source/hardware/nvidia/t23x/nv-public/tegra234-p3737-0000.dtsi ~/nvidia/nvidia_sdk/JetPack_6.2_Linux_JETSON_AGX_ORIN_TARGETS/Linux_for_Tegra/source/kernel-devicetree/
cp ./RMinte/Linux_for_Tegra/source/hardware/nvidia/t23x/nv-public/tegra234-p3701-0000.dtsi ~/nvidia/nvidia_sdk/Je
tPack_6.2_Linux_JETSON_AGX_ORIN_TARGETS/Linux_for_Tegra/source/kernel-devicetree/
cp ./RMinte/Linux_for_Tegra/source/hardware/nvidia/t23x/nv-public/tegra234-p3701.dtsi ~/nvidia/nvidia_sdk/JetPack_6.2_Linux_JETSON_AGX_ORIN_TARGETS/Linux_for_Tegra/source/kernel-devicetree/
cp ./RMinte/Linux_for_Tegra/source/hardware/nvidia/t23x/nv-public/tegra234.dtsi ~/nvidia/nvidia_sdk/JetPack_6.2_Linux_JETSON_AGX_ORIN_TARGETS/Linux_for_Tegra/source/kernel-devicetree/
cp ./RMinte/Linux_for_Tegra/source/hardware/nvidia/t23x/nv-public/tegra234-p3737-0000+p3701-0000.dts ~/nvidia/nvidia_sdk/JetPack_6.2_Linux_JETSON_AGX_ORIN_TARGETS/Linux_for_Tegra/source/kernel-devicetree/

cp ./RMinte/Linux_for_Tegra/bootloader/tegra234-rm01-v1-pinmux.dtsi ./JetPack_6.2_Linux_JETSON_AGX_ORIN_TARGETS/Linux_for_Tegra/bootloader/
cp ./RMinte/Linux_for_Tegra/bootloader/tegra234-rm01-v1-padcfg.dtsi ./JetPack_6.2_Linux_JETSON_AGX_ORIN_TARGETS/L
inux_for_Tegra/bootloader/
cp ./RMinte/Linux_for_Tegra/bootloader/tegra234-rm01-v1-gpio-default.dtsi ./JetPack_6.2_Linux_JETSON_AGX_ORIN_TARGETS/Linux_for_Tegra/bootloader/
cp ./RMinte/Linux_for_Tegra/source/kernel-devicetree/tegra234-rm01-v1.dts ./JetPack_6.2_Linux_JETSON_AGX_ORIN_TAR
GETS/Linux_for_Tegra/source/kernel-devicetree
cp ./RMinte/Linux_for_Tegra/bootloader/tegra234-rm01-v1.dtb ./JetPack_6.2_Linux_JETSON_AGX_ORIN_TARGETS/Linux_for_Tegra/bootloader/
cp ./RMinte/Linux_for_Tegra/jetson-agx-orin-devkit.conf ./JetPack_6.2_Linux_JETSON_AGX_ORIN_TARGETS/Linux_for_Tegra/
```





## 基本流程

1. 创建rm01-v1.conf文件
2. 使用官方模板生成pinmux等三个文件
3. 根据实际情况生成设备树
4. 自定义编辑其他需要烧录的分区文件

