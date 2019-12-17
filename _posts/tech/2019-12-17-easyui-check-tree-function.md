​---
layout: post
title: EasyUi--Check的方法
category: 技术
tags: [Jquery]
keywords: Jquery,Check
description:
---

> EasyUi官网上只给出了有Check的方法，但是具体怎么调用，还需要研究一番。

# 不多说，直接上代码：

```
function InitDepartmentTree(formObj, authorDepartmentObj) {
    authorDepartmentObj.tree({
        url: "/InitDepartByTree",
        checkbox: true,
        lines: true,
        cascadeCheck: false,
        onLoadSuccess: function(node, data) {}
    });
    var contentGuid = $("input[name='contentsGuid']", formObj).val();
    $.post("/GetAuthorizeListByType", { FKGuid: contentGuid}, function (json) {
        if (json) {
            $.each(json,
                function (index, value) {
                    var node = authorDepartmentObj.tree('find', value.DepartmentGuid);//根据GUID寻找对应的节点
                    authorDepartmentObj.tree('check', node.target);//调用Check方法
                });
        }
    }, 'json');
}

​```