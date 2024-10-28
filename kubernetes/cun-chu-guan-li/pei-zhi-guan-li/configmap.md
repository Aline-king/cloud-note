# configMap

## 基本属性

```
kubectl explain cm
```

> 子选项
>
> binaryData：使用二进制传递数据       &#x20;
>
> data：传递数据 ，他会在pod的容器内部生成一个单独的数据文件

### 数据的表现样式

{% hint style="warning" %}
<mark style="color:red;">**注意：**</mark>

cm资源配置文件修改完毕后，会自动更新到所有应用该CM的pod里面
{% endhint %}

普通数据：&#x20;

属性名称: 属性值&#x20;

文件数据： 文件名称: | # | 是"多行键值"的标识符  文件内容

```
普通数据：
    属性名称: 属性值
文件数据：
    文件名称: |  # | 是"多行键值"的标识符
      文件内容
```
