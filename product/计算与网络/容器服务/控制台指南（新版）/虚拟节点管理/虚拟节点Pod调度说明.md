## 计费方式

调度到虚拟节点上的 Pod 支持预付费、后付费（按量计费、竞价）的两种计费模式，计费详情请参见 [弹性容器计费概述](https://cloud.tencent.com/document/product/457/39807)、[弹性容器产品定价](https://cloud.tencent.com/document/product/457/39806)、[弹性容器购买限制](https://cloud.tencent.com/document/product/457/39821)。


## Kubernetes 版本

仅支持 1.16 及以上版本集群。

## 默认配额

默认每个集群仅可将**100个 Pod** 调度到虚拟节点上。若您需要超过以上配额的资源，可填写提升配额申请，由腾讯云对您的实际需求进行评估，评估通过之后将为您提升配额。

### 申请提升配额操作指引

1. 请 [在线咨询](https://cloud.tencent.com/online-service?from=doc_457)，选择【人工支持】或者【其他问题】>【立即创建】，进入创建工单信息填写页面。
2. 在问题描述中填写“期望提升集群虚拟节点 Pod 配额”，注明目标地区及目标配额，并按照页面提示填写您可用的手机号等信息。
3. 填写完成后，单击【在线咨询】即可。

## Pod 配置说明

### Pod 规格配置

Pod 的规格配置是容器运行时可用资源和使用服务计费的依据，请务必了解虚拟节点 Pod 的资源规格配置和指定方法，详情请参见 [资源规格](https://cloud.tencent.com/document/product/457/39808) 和 [指定资源规格](https://cloud.tencent.com/document/product/457/44174)。

### Pod 临时存储

每个调度到虚拟节点上的 Pod 创建时会分配不超过20GiB的临时镜像存储。

>!
>- 临时镜像存储将于 Pod 生命周期结束时删除，请勿用于存储重要数据。
>- 由于需存储镜像，实际可用空间小于20GiB。
>- 重要数据、超大文件等推荐挂载 Volume 持久化存储。

### Pod 网络

调度到虚拟节点上的 Pod 采用的是与云服务器、云数据库等云产品平级的 VPC 网络，每个 Pod 都会占用一个 VPC 子网 IP。

Pod 与 Pod、Pod 与其他同 VPC 云产品间可直接通过 VPC 网络通信，没有性能损耗。

### Pod 隔离性

调度到虚拟节点上的 Pod 拥有与云服务器完全一致的安全隔离性。Pod 在腾讯云底层物理服务器上调度创建，创建时会通过虚拟化技术保证 Pod 间的资源隔离。

### 其他 Pod 特殊配置

调度到虚拟节点上的 Pod 可以通过在 yaml 中定义 `template annotation` 的方式，实现为 Pod 绑定安全组、分配资源、分配EIP等能力。配置方法见下表：

>!
>- 如果不指定安全组，则 Pod 会默认绑定节点池指定的安全组。请确保安全组的网络策略不影响该 Pod 正常工作，例如，Pod 启用 80 端口提供服务，请放通入方向 80 端口的访问。
>- 如需通过 annotation 指定的方式分配 CPU 资源，则必须同时填写 `cpu` 和 `mem` 2个 annotation，且数值必须符合 [资源规格](https://cloud.tencent.com/document/product/457/39808) 中的 CPU 规格。另外，可以通过 `cpu-type` 指定分配 intel 或 amd CPU，其中 amd 具备更高的性价比，详情请参考 [产品定价](https://cloud.tencent.com/document/product/457/39806)。 
- 如需通过 annotation 指定的方式分配 GPU 资源，则必须同时填写 `cpu`、`mem`、`gpu-type` 及 `gpu-count` 4个 annotation，且数值必须符合 [资源规格](https://cloud.tencent.com/document/product/457/39808) 中的 GPU 规格。


<table>
<thead>
<tr>
<th width="20%">Annotation Key</th>
<th width="40%">Annotation Value 及描述</th>
<th width="40%">是否必填</th>
</tr>
</thead>
<tbody>
<tr>
<td>eks.tke.cloud.tencent.com/security-group-id</td>
<td>工作负载默认绑定的安全组，请填写 <a href="https://console.cloud.tencent.com/cvm/securitygroup" target="_blank">安全组 ID</a>：
	<li>可填写多个，以<code>,</code>分割。例如 <code>sg-id1,sg-id2</code>。</li>
	<li>网络策略按安全组顺序生效。</li>
</td>
<td> 否。如不填写，则默认绑定节点池指定的安全组。<br>如填写，请确保同地域已存在该安全组 ID。</td></tr>
<tr>
<td>eks.tke.cloud.tencent.com/cpu</td>
<td>Pod 所需的 CPU 核数，请参考 <a href="https://cloud.tencent.com/document/product/457/39808" target="_blank">资源规格</a> 填写。默认单位为核，无需再次注明。</td>
<td>否。如填写，请确保为支持的规格，且需完整填写 <code>cpu</code> 和 <code>mem</code> 两个参数。</td>
</tr>
<tr>
<td>eks.tke.cloud.tencent.com/mem</td>
<td>Pod 所需的内存数量，请参考 <a href="https://cloud.tencent.com/document/product/457/39808" target="_blank">资源规格</a> 填写，需注明单位。例如，512Mi、0.5Gi、1Gi。</td>
<td>否。如填写，请确保为支持的规格，且需完整填写 <code>cpu</code> 和 <code>mem</code> 两个参数。</td>
</tr>
<tr>
<td>eks.tke.cloud.tencent.com/cpu-type</td>
<td>Pod 所需的 CPU 资源型号，目前支持型号如下：
<li>intel</li>
<li>amd</li>
<li>具体型号，如 S4、S3</li>
各型号支持的具体配置请参考 <a href="https://console.cloud.tencent.com/cvm/securitygroup" target="_blank">资源规格</a>。</td>
<td>否。如果不填写则默认不强制指定 CPU 类型，会根据 <a href="https://cloud.tencent.com/document/product/457/44174" target="_blank">指定资源规格方法</a> 尽量匹配最合适的规格，若匹配到的规格 Intel 和 amd 均支持，则优先选择 Intel。</td>
</tr>
<tr>
<td>eks.tke.cloud.tencent.com/gpu-type</td>
<td>Pod 所需的 GPU 资源型号，目前支持型号如下：
<ul  class="params">
<li>V100</li>
<li>1/4*T4</li>
<li>1/2*T4</li>
<li>T4</li>
<li>支持优先级顺序写法，如 “T4,V100” 表示优先创建 T4 资源 Pod，如果所选地域可用区 T4 资源不足，则会创建 V100 资源 Pod。</li>
</ul>
各型号支持的具体配置请参考 <a href="https://cloud.tencent.com/document/product/457/39808">资源规格</a>。</td>
<td>如需 GPU，则此项为必填项。填写时，请确保为支持的 GPU 型号，否则会报错。</td>
</tr>
<tr>
<td>eks.tke.cloud.tencent.com/eip-attributes</td>
<td>表明该 Workload 的 Pod 需要关联 EIP，值为 "" 时表明采用 EIP 默认配置创建。"" 内可填写 EIP 云 API 参数 json，实现自定义配置。例如 annotation 的值为 '{"InternetMaxBandwidthOut":2}' 即为使用2M的带宽。</td>
<td>否 </td>
</tr>
<tr>
<td>eks.tke.cloud.tencent.com/eip-claim-delete-policy</td>
<td> Pod 删除后，EIP 是否自动回收，“Never” 不回收，默认回收。</td>
<td>否 </td>
</tr>
<tr>
<td>eks.tke.cloud.tencent.com/eip-injection</td>
<td>值为 "true" 时，表明会在 Pod 内暴露 EIP 的 IP 信息。在 Pod 内使用 ip addr 命令可以查看到 EIP 的地址。</td>
<td>否 </td>
</tr>
</tbody></table>


示例请参考 [Annotation 说明](https://cloud.tencent.com/document/product/457/44173)。

## Pod 限制说明

### Workload 限制

DaemonSet 类型工作负载的 Pod 不会调度到虚拟节点上。

### Service 限制

采用 [GlobalRouter 网络模式](https://cloud.tencent.com/document/product/457/50354) 的集群 service 如果开启了 externaltrafficpolicy = local，流量不会转发到调度到虚拟节点上的 Pod。

### Volume 限制

挂载 hostPath 类型数据卷的 Pod 不会调度到虚拟节点上。


### 其他限制

- 没有任何服务器节点的空集群暂时无法正常使用虚拟节点功能。
- 开启了 [固定 IP ](https://cloud.tencent.com/document/product/457/34994)的 Pod 暂不支持调度到虚拟节点上。
- 指定了 hostPort 的 Pod 不会调度到虚拟节点上。
- 指定了 hostIP 配置的 Pod 默认会把 Pod IP 作为 hostIP。
- 如果开启了反亲和性特性，同工作负载 Pod 仅会在虚拟节点上创建一个。
- 如果容器日志存储在指定的节点文件中，也是通过节点文件进行的日志采集，则无法采集虚拟节点上的 pod 日志。


## 如何调度 Pod 至虚拟节点

### 自动扩容
若集群配置了虚拟节点，当业务高峰且已有节点资源不足时，会自动调度 Pod 至虚拟节点，无需购买服务器；业务恢复平稳，自动释放在虚拟节点中的 pod 资源，也无需再进行退还机器操作。

如果集群同时开启了 [Cluster Autoscaler](https://cloud.tencent.com/document/product/457/32190#AutomaticAddAndRemove) 和虚拟节点，则会尽量优先将 Pod 调度到虚拟节点上，而非触发集群节点扩容。如果受上述调度限制影响，Pod 无法调度到虚拟节点上，则会依然正常触发集群节点扩容。而服务器节点资源充足时，会优先缩容虚拟节点上的 Pod。


### 手动调度

虚拟节点支持手动将 Pod 调度至虚拟节点，详情请见 [手动调度 Pod 至虚拟节点](https://cloud.tencent.com/document/product/457/54754#.E5.A6.82.E4.BD.95.E6.89.8B.E5.8A.A8.E8.B0.83.E5.BA.A6-pod-.E5.88.B0.E8.99.9A.E6.8B.9F.E8.8A.82.E7.82.B9.EF.BC.9F.3Ca-id.3D.22pod1.22.3E.3C.2Fa.3E)。


## 虚拟节点 annotation 说明
您可通过在 yaml 中定义 annotation 的方式，实现虚拟节点的自定义 DNS 等能力，具体如下：

<table>
<thead>
<tr>
<th width="20%">Annotation Key</th>
<th width="40%">Annotation Value 及描述</th>
<th width="40%">是否必填</th>
</tr>
</thead>
<tbody>
<tr>
<td>eks.tke.cloud.tencent.com/resolv-conf</td>
<td> 容器解析域名时查询 DNS 服务器的 IP 地址列表。例如 <code>nameserver 8.8.8.8</code>。
<br> 可通过 <code>kubectl edit node eklet-subnet-xxxx</code> 添加该 annotation。
<br> 修改后调度到该虚拟节点的 Pod 默认全部采用该 DNS 配置。</td>
<td>否。</td>
</tr>
</tr>
</tbody></table>

#### 示例
以下为虚拟节点自定义 DNS 配置的示例：

```
apiVersion: v1
kind: Node
metadata:
    annotations:
      eks.tke.cloud.tencent.com/resolv-conf：|
	   	nameserver 4.4.4.4
        nameserver 8.8.8.8
    
	
```





## 常见问题
[如何禁止 Pod 调度到某个虚拟节点？](https://cloud.tencent.com/document/product/457/54754#.E5.A6.82.E4.BD.95.E7.A6.81.E6.AD.A2-pod-.E8.B0.83.E5.BA.A6.E5.88.B0.E6.9F.90.E4.B8.AA.E8.99.9A.E6.8B.9F.E8.8A.82.E7.82.B9.EF.BC.9F)

[如何禁止 TKE 普通集群在资源不足时自动调度到虚拟节点？
](https://cloud.tencent.com/document/product/457/54754#.E5.A6.82.E4.BD.95.E7.A6.81.E6.AD.A2-tke-.E6.99.AE.E9.80.9A.E9.9B.86.E7.BE.A4.E5.9C.A8.E8.B5.84.E6.BA.90.E4.B8.8D.E8.B6.B3.E6.97.B6.E8.87.AA.E5.8A.A8.E8.B0.83.E5.BA.A6.E5.88.B0.E8.99.9A.E6.8B.9F.E8.8A.82.E7.82.B9.EF.BC.9F)


[如何手动调度 Pod 至虚拟节点](https://cloud.tencent.com/document/product/457/54754#.E5.A6.82.E4.BD.95.E6.89.8B.E5.8A.A8.E8.B0.83.E5.BA.A6-pod-.E5.88.B0.E8.99.9A.E6.8B.9F.E8.8A.82.E7.82.B9.EF.BC.9F.3Ca-id.3D.22pod1.22.3E.3C.2Fa.3E)

[如何强制调度 Pod 到虚拟节点，无论虚拟节点是否支持该 Pod？
](https://cloud.tencent.com/document/product/457/54754#.E5.A6.82.E4.BD.95.E5.BC.BA.E5.88.B6.E8.B0.83.E5.BA.A6-pod-.E5.88.B0.E8.99.9A.E6.8B.9F.E8.8A.82.E7.82.B9.EF.BC.8C.E6.97.A0.E8.AE.BA.E8.99.9A.E6.8B.9F.E8.8A.82.E7.82.B9.E6.98.AF.E5.90.A6.E6.94.AF.E6.8C.81.E8.AF.A5-pod.EF.BC.9F)

