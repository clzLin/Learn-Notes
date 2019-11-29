# Altium_Designer09 制作原理图模板及调用 #

1. 在AD安装路径下，找到安装路径官方的模板文件(.SchDot) D:\install\AD09\Templates
2. 复制一份，按照自己需求修改后保存。
3. 把自己改的模板文件最好也放到 D:\install\AD09\Templates
4. 若需要将自己的模板作为默认的模板，设置如下：
   Tools->Shematic Preference->Default->Template选择自己的模板
   这样之后新建的Sch就会以自己的模板来，若想将A4切换为A3时，不用再Document Option中修改standard styles,选择Design->Template->Set Template File Name,然后选择自己创建的模板 所以为了满足不同纸张的需求，需要根据纸张大小各做一个模板。