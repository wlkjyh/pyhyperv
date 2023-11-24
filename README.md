我对OS-win操作Hyper-v虚拟机进行了总结，以下是调用方法

QQ：3139505131


## 安装
```angular2html
pip3 install os_win
```



## 导包调用
```py
# 虚拟机
from os_win.utils.compute.vmutils import VMUtils as os_win_VMUtils

# 网路
from os_win.utils.network.networkutils import NetworkUtils as os_win_NetworkUtils

# 虚拟磁盘
from os_win.utils.storage.virtdisk.vhdutils import VHDUtils as os_win_VHDUtils
```

## 虚拟机状态代码示意表
| 状态代码 | 状态   |
| --- |------|
| 1 | 其他   |
| 2 | 正在运行 |
| 3 | 已经关闭 |
| 4 | 正在关闭 |
| 10 | 正在重启 |
| 32768 | 暂停   |
| 32769 | 挂起   |

## 磁盘类型代码示意表

| 磁盘类型代码 | 磁盘类型   |
| --- |------|
| 2 | 固定   |
| 3 | 动态 |
| 4 | 差异 |



## 封装的方法
其中vm_name是虚拟机的名称，可以在hyper-v管理器中看到

### 判断是否有管理Hyper-v的权限
如果有则返回None，否则抛出异常
```python
print(os_win_VMUtils().check_admin_permissions())
```
有返回True，否则返回False

### 创建一个虚拟机
```python
print(os_win_VMUtils().create_vm(
    vm_name='1234',
    vnuma_enabled=False,
    vm_gen=1,
    instance_path='C:\\instance\\123',
    notes='描述'
))
```
vm_gen是虚拟机代数，1是第一代，2是第二代，每一代有不同的功能。


### 判断虚拟机是否存在
```python
print(os_win_VMUtils().vm_exists(
    vm_name='123'
))
```

### 为虚拟机添加一个网卡，并设置名称
```python
print(os_win_VMUtils().create_nic(
    vm_name='123',
    nic_name='123'
))
```

### 获取虚拟机所有网卡
```python
print(os_win_VMUtils().get_vm_nic_names(
    vm_name='123'
))
```
返回一个列表，如下内容
```python
['123']
```

### 为虚拟机删除一个网卡，使用名称
```python
print(os_win_VMUtils().destroy_nic(
    vm_name='123',
    nic_name='123'
))
```


### 删除一个虚拟机
```python
print(os_win_VMUtils().destroy_vm(
    vm_name='123'
))
```

### 获取虚拟机的ID
使用freerdp这些连接虚拟机需要用到这个id
```python
print(os_win_VMUtils().get_vm_id(
    vm_name='123'
))
```

### 设置虚拟机内存和CPU
```python
print(os_win_VMUtils().update_vm(
    vm_name='123',
    # 内存大小
    memory_mb=1024,
    # cpu个数
    vcpus_num=2,
    limit_cpu_features=False,
    # 和上面保持一样
    memory_per_numa_node=1024,
    vcpus_per_numa_node=2,
    dynamic_mem_ratio=False,
    

))
```

### 设置虚拟机状态

操作状态数：

| req_state | 操作   |
| --- |------|
| 2 | 启动   |
| 3 | 关闭 |
| 11 | 重启 |
| 32768 | 暂停   |
| 32769 | 挂起   |

```python
print(os_win_VMUtils().set_vm_state(
    vm_name='123',
    req_state=2,
))
```

### 软关机虚拟机
```python
print(os_win_VMUtils().soft_shutdown_vm(
    vm_name='123'
))
```

### 创建一个SCSI控制器
```python
print(os_win_VMUtils().create_scsi_controller(
    vm_name='123'
))
```

### 获取SCSI控制器
```python

print(os_win_VMUtils().get_vm_scsi_controller(
    vm_name='123'
))

```

### 将虚拟磁盘vhdx连接到scsi控制器
```python

print(os_win_VMUtils().attach_scsi_drive(
    vm_name='123',
    path=r'C:\123.vhdx',
))
```

### 将vhdx从虚拟机中移除
```python
print(os_win_VMUtils().detach_vm_disk(
    vm_name='test',
    disk_path=r'C:\123.vhdx',
    is_physical=False
))

```


### 设置安全启动
```python
print(os_win_VMUtils().enable_secure_boot(
    vm_name='123',
    msft_ca_required=False,
))
```

### 获取虚拟机是否支持RemoteFX
```python
print(os_win_VMUtils().vm_gen_supports_remotefx(
    # 第一代是1 第二代是2
    vm_gen=2,
))
```

### 获取虚拟机当前信息
```python
print(os_win_VMUtils().get_vm_summary_info(
    vm_name='123'
))
```
返回一个字典，如下内容
```python
{'NumberOfProcessors': 1, 'EnabledState': 2, 'MemoryUsage': 1024, 'UpTime': 13633}
```
分别为CPU个数，虚拟机状态（参考表），内存使用量，运行时间（毫秒）

### 开启RemoteFX
这玩意好像在新版上已经不支持了，在老版本Windows上应该是支持的，报错
```
警告: 我们不再支持 RemoteFX 3D
显示适配器。如果你仍在使用此适配器，可能会容易受安全风险影响。了解更多信息(https://go.microsoft.com/fwlink/?linkid=2131
976)
```
```python
print(os_win_VMUtils().enable_remotefx_video_adapter(
    vm_name='123',
    monitor_count=1,
    max_resolution='1920x1200',
))
```

### 关闭RemoteFX
```python
print(os_win_VMUtils().disable_remotefx_video_adapter(
    vm_name='123'
))
```

### 获取所有虚拟机
```python
print(os_win_VMUtils().list_instances())
```
返回一个列表，内容为虚拟机名称
```python
['123']
```

### 获取所有虚拟机并包含描述
```python

print(os_win_VMUtils().list_instance_notes())
```
返回一个字典，每一个项是一个元组
```python
[('123', []), ('1234', ['d'])]
```

### 获取所有已经开机的虚拟机
```python
print(os_win_VMUtils().get_active_instances())
```
返回一个列表，内容为虚拟机名称
```python
['123']
```

### 获取虚拟机内存信息
```python
print(os_win_VMUtils().get_vm_memory_info(
    vm_name='123'
))
```

返回一个字典
```python
{'DynamicMemoryEnabled': True, 'Reservation': 512, 'Limit': 1048576, 'Weight': 5000, 'MaxMemoryBlocksPerNumaNode': 15388}
```
DynamicMemoryEnabled是否开启动态内存

Reservation内存预留

Limit内存上限

Weight内存权重

MaxMemoryBlocksPerNumaNode最大内存块数


### 获取虚拟机的根目录
```python
print(os_win_VMUtils().get_vm_config_root_dir(
    vm_name='123'
))
```

### 获取虚拟机是第几代
```python
print(os_win_VMUtils().get_vm_generation(
    vm_name='123'
))
```

### callback虚拟机状态

```callback```回调函数包含vm_name和state方法，vm_name是虚拟机名称，state是虚拟机状态，参考表


```python
_listener = os_win_VMUtils().get_vm_power_state_change_listener(
            timeframe=15,
            event_timeout=3,
            filtered_states=[2, 3, 32768, 32769],
            get_handler=True
)

def callback(vm_name, state):
    print(vm_name, state)

print(_listener(callback))
```

### 创建虚拟机快照
```python
print(os_win_VMUtils().take_vm_snapshot(
    vm_name='123',
    snapshot_name='snapshot1'
))
```

### 恢复虚拟机快照
```python
print(os_win_VMUtils().get_vm_snapshots(
    vm_name='123',
    snapshot_name='snapshot1'
).revert_to_snapshot())
```

### 恢复快照
我在os_win里面没有找到恢复快照的方法，所以我用了powershell的命令来恢复快照

```python
import os
vm_name = '123'
snapshot_name = 'snapshot1'
command = "Restore-VMSnapshot -VMName {} -Name {} -Confirm:$false".format(vm_name, snapshot_name)
result = os.popen(' '.join(['powershell.exe','-command',command])).read()
print(result)
```
如果返回空字符串则表示成功，否则失败

### 删除快照
os_win中提供的那个删除快照是直接删除所有快照，我这里提供了删除指定快照的方法

```python
import os
vm_name = '123'
snapshot_name = 'snapshot1'
command = "Remove-VMSnapshot -VMName {} -Name {} -Confirm:$false".format(vm_name, snapshot_name)
result = os.popen(' '.join(['powershell.exe','-command',command])).read()
print(result)
```
如果返回空字符串则表示成功，否则失败

### 获取虚拟磁盘类型
```python
result = os_win_VHDUtils().get_vhd_type(
    vhd_path='C:\\123.vhdx'
)
print(result)
```

### 获取虚拟磁盘格式
```python
result = os_win_VHDUtils().get_vhd_format(
    vhd_path='C:\\123.vhdx'
)
print(result)
```

## 获取虚拟磁盘大小
```python
result = os_win_VHDUtils().get_vhd_size(
    vhd_path='C:\\123.vhdx'
)
print(result)
```
返回一个字典，内容如下
```python
{'VirtualSize': 136365211648, 'PhysicalSize': 4194304, 'BlockSize': 33554432, 'SectorSize': 512}

```
分别为虚拟大小，物理大小，块大小，扇区大小

### 获取虚拟磁盘的父磁盘
```python
result = os_win_VHDUtils().get_vhd_parent_path(
    vhd_path='C:\\123.vhdx'
)
print(result)
```
如果自己是父磁盘则返回None，否则返回父磁盘路径

### 获取虚拟磁盘详情信息
```python
result = os_win_VHDUtils().get_vhd_info(
    vhd_path='C:\\123.vhdx'
)
print(result)
```

## 用父盘创建一个差异盘
```python
result = os_win_VHDUtils().create_differencing_vhd(
    path='C:\\new.vhdx',
    parent_path='C:\\123.vhdx',
)
print(result)
```

### 创建一个空白的固定大小的虚拟磁盘
os-win中提供那个不知道为什么要报错，我这里提供了一个powershell的方法
```python
import os
path = 'C:\\new.vhdx'
size = 10
command = "New-VHD -Path {} -SizeBytes {}GB -BlockSizeBytes 128MB -LogicalSectorSize 4KB".format(path, size)
result = os.popen(' '.join(['powershell.exe','-command',command])).read()
if os.path.exists(path):
    print('success')
```

### 修改虚拟磁盘大小
os-win中提供那个不知道为什么要报错，我这里提供了一个powershell的方法
```python
import os
path = 'C:\\new.vhdx'
size = 11
command = "Resize-VHD -Path {} -SizeBytes {}GB".format(path, size)
result = os.popen(' '.join(['powershell.exe','-command',command])).read()
print(result)
```
如果返回空字符串则表示成功，否则失败

## 连接虚拟交换机到虚拟机
他这玩意有点逆天，vswitch_name是虚拟交换机名称，switch_port_name是虚拟机网卡名称，就是你创建nic那个名称
```python   
os_win_NetworkUtils().connect_vnic_to_vswitch(
    vswitch_name="Default Switch",
    switch_port_name="123",
)
```

### 获取所有网络接口名称
这玩意就是你创建nic那个名称
```python
print(
    os_win_NetworkUtils().get_vnic_ids()
)
```

### 获取网络接口的MAC地址
switch_port_name是你创建nic那个名称
```python
print(
    os_win_NetworkUtils().get_vnic_mac_address(
        switch_port_name='123'
    )
)
```

### 设置网络端口QOS
port_id是你创建nic那个名称
```python
print(
    os_win_NetworkUtils().set_port_qos_rule(
        port_id='123',
        qos_rule={
            'min_kbps' : 10240,
            'max_kbps' : 10240,
            'max_burst_kbps' : 10240,
            'max_burst_size_kb' : 10240,

        }
    )
)

# max_kbps是最大带宽，单位kbps
# max_burst_kbps是最大突发带宽，单位kbps
# max_burst_size_kb是最大突发大小，单位kb
```

### 清除QOS规则
```python
print(
    os_win_NetworkUtils().remove_port_qos_rule(
        port_id='123',
    )
)
```

