tools
=====



main_restore: create_domain(restoreing not migrate):
1. 打开指定的snapshot文件
2. domcreate_bootloader_done：
-libxl__build_pre
-xc_domain_restore:
a.从snapshot文件中读取p2m表大小
b.调用__HYPERVISOR_xen_version，获取vmm的线性地址起点、页表类型，用来？？?
c.调用__HYPERVISOR_memory_op获取该domain的最大机器页表, 用来计算该domain的M2P表大小
d.调用XEN_DOMCTL_get_address_size，获取该domain的内存位宽
e.调用XEN_DOMCTL_getdomaininfo,获取该domain的状态、内存、cpu等信息
f.将snapshot中的保存的内存映像读取到内存
g.将snapshot中的vm的generation id、EPT的identity map参数 、io ring pfn页框、TSS段、vconsole的页框、 vapic的port、viridian flag、mmio地址
  ， 调用__HYPERVISOR_hvm_op, 写入domain中
h.从snapshot文件中读出设备模型的内存映射的start_addr、size、name， 更新到xenstore的/local/domain/0/device-model/domid/physmap目录
i.将设备模型的内存映像写入到/var/lib/xen/qemu-resume.domid
j.调用XEN_DOMCTL_sethvmcontext, 将snapshot文件中的vcpu上下文写入domain
k.调用__HYPERVISOR_memory_op获取该domin最大的页框， 其后一个页框，通过__HYPERVISOR_grant_table_op将其设置为grant table。
  mmap后将console与xenstore的io ring页框添加为条目。 后续其它domin或者xenstore可以访问。
