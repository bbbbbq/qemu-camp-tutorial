# 基于 CXLMemSim 和 QEMU 的 GPU 可观测系统构建

!!! tip "项目简介"

    CXLMemSim 提供了一套基于 QEMU 的 CXL Type‑2 加速器建模与 hetGPU 集成方案，用于跨平台 GPGPU 仿真。Guest 端 CUDA 应用无需改动，通过 libcuda shim 将 CUDA Driver API 翻译为 CXL 命令，并借助内核驱动配置 CXL.cache/CXL.mem 与一致性状态；Host 端在 QEMU 设备模型中模拟 Type‑2 加速器，由 hetGPU 作为后端执行命令处理与设备内存仿真，实现端到端的“像真 GPU”运行体验。

    ![](../../../image/qemu-cxl-type2-gpu.png)

!!! note "项目方向"

    待更新

!!! question "考核标准"

    1. 技术报告（必须）：包含项目成果、代码链接
    2. 其他条件，待更新

## 相关学习资料

1. [OCEAN IPDPS 2026 tutorial draft - PPTX](./OCEAN_IPDPS_2026_tutorial_draft.pptx)
2. [Emulating CXL with QEMU - YouTube](https://www.youtube.com/watch?v=SWwv6XfdXic)
3. [CXL Device Emulation: Leveraging QEMU - PDF](https://www.snia.org/sites/default/files/SDC/Austin/SNIA-RSDC24-Manzanares-CXL-Device-Emulation-Leveraging-QEMU.pdf)
4. [hetGPU paper - arXiv](https://arxiv.org/abs/2506.15993)
5. [hetGPU paper - PDF](https://asplos.dev/pdf/hetgpu.pdf)
6. [GPGPU-Sim 官网](https://gpgpu-sim.org/)
7. [CXL Specification 4.0 evaluation copy - PDF](https://computeexpresslink.org/wp-content/uploads/2026/02/CXL-Specification_rev4p0_ver1p0_2026February26_clean_evalcopy_v2.pdf)
8. [CXL Specification 2.0 - PDF](https://computeexpresslink.org/wp-content/uploads/2024/02/CXL-2.0-Specification.pdf)
9. [QEMU 官方 CXL 文档](https://www.qemu.org/docs/master/system/devices/cxl.html)
10. [Tenstorrent ttsim - GitHub](https://github.com/tenstorrent/ttsim)
11. [OCEAN - GitHub](https://github.com/fam-emu/OCEAN)
12. [OCEAN Concepts & Architecture 文档](https://ocean.readthedocs.io/en/latest/architecture.html)
13. [CXLMemSim: A pure software simulated CXL.mem for performance characterization - arXiv](https://arxiv.org/abs/2303.06153)
14. [CXLMemSim - GitHub](https://github.com/SlugLab/CXLMemSim)
