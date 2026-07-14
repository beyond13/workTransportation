# workTransportation

如果你是在 Figma 里看到 W3，它通常表示的是：

Font Weight（字重）= 300

对应关系如下：

Figma / 字重	CSS font-weight	效果
W1	100	Thin（极细）
W2	200	Extra Light
W3	300	Light（细体）
W4	400	Regular（标准）
W5	500	Medium
W6	600	SemiBold
W7	700	Bold（粗体）
W8	800	ExtraBold
W9	900	Black（最粗）

结合你前几天问的

你之前问过 W6 30，其实就是：

* W6 → 字重 600（SemiBold）
* 30 → 字号 30px

同理：

* W3 14 → font-weight: 300；font-size: 14px
* W4 16 → font-weight: 400；font-size: 16px
* W5 14 → font-weight: 500；font-size: 14px
* W7 20 → font-weight: 700；font-size: 20px

⸻

不过有一点要注意：

有些设计系统（尤其是日本公司的设计规范）会写成 W3、W4、W5、W6；而有些 Figma 文件则直接显示 Regular、Medium、Bold。两种表示方式本质上是一样的，都是在描述 字重（font-weight）。