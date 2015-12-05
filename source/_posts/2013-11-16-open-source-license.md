---
layout: default
title: 开源协议整理
---

使用开源软件的几个方式：  
1. 如果软件可以独立运行，则以可执行程序的方式使用  
2. 如果软件是library，则以引用链接的方式使用  
3. 修改软件，自己使用  
4. 修改软件后发布  

<!--more-->

<table class="dataTable">
    <thead>
        <tr>
            <th>协议</th>
            <th>链接</th>
            <th>修改</th>
            <th>分发</th>
            <th>商业闭源</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Public Domain</td>
            <td>允许</td>
            <td>允许</td>
            <td>允许</td>
            <td>公共领域软件，基本没有任何使用上的限制。比如<a href="http://sqlite.org/copyright.html" target="_blank">SQLite</a></td>
        </tr>
        <tr>
            <td><a href="http://opensource.org/licenses/gpl-license" target="_blank">GPL</a></td>
            <td>只能被GPL软件链接使用（传染性）</td>
            <td>GPL保护</td>
            <td>以GPL分发</td>
            <td>不允许。ASP漏洞参见下面的AGPL</td>
        </tr>
        <tr>
            <td><a href="http://opensource.org/licenses/lgpl-license" target="_blank">LGPL</a>（GPL的缩减版）</td>
            <td>允许被其他协议的软件链接</td>
            <td>LGPL保护</td>
            <td>以LGPL分发</td>
            <td>建议使用动态链接。因为静态链接是否属于软件衍生品有争议。这个协议专门提供给程序库使用，避免因为使用程序库而使得整个程序变成GPL的。例如<a href="http://log4cpp.sourceforge.net/#license" target="_blank">Log4Cpp</a>。</td>
        </tr>
        <tr>
            <td><a href="http://opensource.org/licenses/AGPL-3.0" target="_blank">AGPL</a>（GPL的补充）</td>
            <td>AGPL</td>
            <td>AGPL</td>
            <td>AGPL</td>
            <td>不允许。为了弥补GPL的漏洞而出现。以Google为代表的网络提供者使用GPL软件，但是不分发，仅在服务端使用对外提供服务，造成事实上的商业使用而不开源。AGPL规定即使在这种情况下也需要提供代码下载。使用AGPL的其中一个例子是<a href="http://www.mongodb.org/about/licensing" target="_blank">MongoDB</a>。</td>
        </tr>
        <tr>
            <td><a href="http://opensource.org/licenses/BSD-2-Clause" target="_blank">Simplified BSD</a></td>
            <td>允许</td>
            <td>允许</td>
            <td>允许</td>
            <td>允许。但是要附上原软件的License</td>
        </tr>
        <tr>
            <td><a href="http://opensource.org/licenses/MIT" target="blank">MIT</a></td>
            <td>允许</td>
            <td>允许</td>
            <td>允许</td>
            <td>允许。但是要附上原软件的License</td>
        </tr>
        <tr>
            <td><a href="http://opensource.org/licenses/Apache-2.0" target="_blank">Apache</a></td>
            <td>允许</td>
            <td>允许。每个修改的文件都要带Apache License</td>
            <td>允许。每个修改的文件都要带Apache License</td>
            <td>允许。但是要附上原软件的License</td>
        </tr>
    </tbody>
</table>


