
2.IE专用的条件注释
<!--其他浏览器 -->
<link rel="stylesheet" type="text/css" href="css.css" />
<!--[if IE 7]>
<!-- 适合于IE7 -->
<link rel="stylesheet" type="text/css" href="ie7.css" />
<![endif]-->
<!--[if lte IE 6]>
<!-- 适合于IE6及一下 -->
<link rel="stylesheet" type="text/css" href="ie.css" />
<![endif]-->


              IE6 IE7 IE8 FF 
*              √   √   ×   × 
!important     ×   √   ×   √ 
_              √   ×   ×   × 
\9             ×   ×   √   × 
*html          √   ×   ×   × 
*+html         ×   √   ×   × 


.warp{ 
Height:100px; /*IE6、IE7、IE8、FF识别*/ 
Height:110px\9; /*IE8识别*/ 
*height:120px!important; /*IE7 识别*/ 
*height:130px; /*IE6、IE7识别，但上一段代码中!important的级别比*号的级别高，所以此段代码只有IE6中才有效*/ 
}  