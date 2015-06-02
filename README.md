tools
=====



todo：paging_domctl；hvm_memory_op


xc_domain_save:

1. 调用__HYPERVISOR_xen_version，获取vmm的线性地址起点、页表类型，用来
2. 调用__HYPERVISOR_memory_op获取该domain的最大机器页表, 用来计算该domain的M2P表大小
3. 调用XEN_DOMCTL_get_address_size，获取该domain的内存位宽 
4. 调用XEN_DOMCTL_getdomaininfo,获取该domain的状态、内存、cpu等信息
5. 调用__HYPERVISOR_memory_op,获取该domain在vmm中的p2m表大小
6. libxl__domain_suspend_callback:
-调用__HYPERVISOR_hvm_op获取cpu0同步中断方式、cpu的s-state状态
-如果该domain的cpu0在sleep，通过suspend evtchn唤醒并通知domain进入suspend，通过写
 save到/local/domain/0/device-model/domid/command，使设备模型保存现场，准备挂起
-调用__HYPERVISOR_sched_op,使domain进入suspend模式
7. 调用XEN_DOMCTL_gethvmcontext, 获取该domain使用的内存大小，并分配相应内存空间，顺便缓存
8. 调用__HYPERVISOR_memory_op获取domain的p2m表条目（mpt virt开始），map可访问
9. 调用XEN_DOMCTL_gettscinfo, 获取tsc，写入save文件
10.将p2m表中额内存条目，按page写入save文件
11.调用XEN_DOMCTL_getvcpuinfo获得vcpu信息， 写入save文件
12.调用__HYPERVISOR_hvm_op, 获取vm的generation id、EPT的identity map参数 、io ring pfn页框、TSS段、vconsole的页框、
   vapic的port、viridian flag、mmio地址，写入save文件
13.将设备模型的使用的内存addr、len等描述写入save文件
14.调用XEN_DOMCTL_gethvmcontext， 将vcpu信息写入save文件
15. fflush
