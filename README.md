# Color-Attenuation-Prior
使用Python實作[A Fast Single Image Haze Removal Algorithm Using Color Attenuation Prior](https://ieeexplore.ieee.org/document/7128396)此篇論文的影像去霧演算法

步驟
---
1. 計算輸入影像 $I$ 的depth map $\rightarrow$ $D(x)$

    >1.將輸入影像 $I$ 從 $RGB$ 色彩空間轉換至 $HSV$ 色彩空間
    $$I_{HSV} = \mathcal{T}(I_{RGB})$$
    >2.對 $I_{S}$ 、 $I_{V}$ 進行 $normalize$
    >
    >$$I^{'}_{S}(x) = \frac{I_S(x)}{255}$$
    >
    >$$I^{'}_{V}(x) = \frac{I_V(x)}{255}$$
    >
    >3.計算depth map $\rightarrow$ $D(x)$
    >
    >$$D(x) = 0.121779 + 0.959710 \times I^{'}_{V}(x) - 0.780245 \times I^{'}_S(x) + N(0,\sigma^{2})$$
    >* $\sigma$ 為 $0.041337$
    
   $\color {red} {問題:不確定計算出來的depth \ map中有小於零的值的話是否要設為零，在paper中只看到d(x)範圍介於[0,+\infty )}$
    
2. 使用最小值濾波器(minimum filter)對depth map進行濾波
    >$$D^{'}(x) = \min_{y \in \Omega(x)} (D(x))$$
    >* 最小值濾波器(minimum filter)大小設定為 $15$ $\times$ $15$
    


3. 使用[引導濾波器(guide filter)](https://ieeexplore.ieee.org/document/6319316)精煉、提煉(refine) $D^{'}(x)$ 得到新的 $D^{'}_{guide}(x)$
    
    >$$D^{'}_{guide}(x) = guided \ filter \lbrace	D^{'}(x) \rbrace$$
    >* $\sigma$ 設定為 $10^{-3}$ ( $\sigma$ 為[引導濾波器(guide filter)](https://ieeexplore.ieee.org/document/6319316)使用參數)
    >* $r$ 設定為 $20$ ( $r$ 為[引導濾波器(guide filter)](https://ieeexplore.ieee.org/document/6319316)使用參數).

   $\color {red} {問題:作為guided的影像必須為0  \textasciitilde  255的資料型態，不能是0 \textasciitilde 1的資料型態，不知道是甚麼原因}$

4. 使用新得到的 $D^{'}_{guide}(x)$ 計算transmission map $\rightarrow$ $t(x)$
    
    >$$t(x) = e^{-\beta\times D^{'}_{guide}(x)}$$
    >* $\beta$ 設定為 $1.0$
    


5. 使用 $D^{'}_{guide}(x)$ 計算Atomspheric Light
    >* 對 $D^{'}_{guide}(x)$ 找前 $0.1\%$ 強度值的位置，再對應找輸入影像 $I$ 三個Channel相對位置的強度值，並計算平均
    >* $A$ 會為 $[$ $mean_B$ , $mean_G$ , $mean_R$ $]$


6. 計算去霧影像(haze-free image)
    
    >$$\mathbf{J}(x) = \frac{\mathbf{I}(x)-\mathbf{A}}{min(max(t(x),0.1),0.9)}+\mathbf{A}$$
    >* $\mathbf{J}$ 、 $\mathbf{I}$ 、 $\mathbf{A}$ 各代表三個Channel


參考網址
---
[A Fast Single Image Haze Removal Algorithm Using Color Attenuation Prior](https://ieeexplore.ieee.org/document/7128396)

[引導濾波器(guide filter)](https://ieeexplore.ieee.org/document/6319316)

[论文阅读：A Fast Single Image Haze Removal Algorithm Using Color Attenuation Prior](https://blog.csdn.net/space_walk/article/details/107833984)
