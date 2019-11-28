#### docx 格式简介	

***



`docx` 是微软为 Word 软件开发的文件格式，其背后是一个国际标准——[Office Open XML](http://officeopenxml.com/)（简称 OOXML）。`docx` 其实是打包文件，把扩展名从 `docx` 改为 `zip` 就能使用解压软件打开，看到其中的内容。解压后的文件结构如下所示：

```
.
├── [Content_Types].xml
├── _rels
├── docProps
│   ├── app.xml
│   └── core.xml
└── word
    ├── _rels
    │   ├── document.xml.rels
    │   └── footnotes.xml.rels
    ├── document.xml
    ├── fontTable.xml
    ├── footnotes.xml
    ├── media
    ├── numbering.xml
    ├── settings.xml
    ├── styles.xml
    ├── theme
    │   └── theme1.xml
    └── webSettings.xml
	
```

##### 26套判题json

```javascript
1.
[
    {
        "score":"3",
        "knowledge":"CHECK_FILE_IS_EXIST",
        "param":{
            "fileName":"Word.docx"
        },
        "baseFile":"Word.docx"
    }
]

2.
//页大小,页边距度量为缇,1厘米=567缇,xml存储值为缇,厘米转化为缇进行四舍五入
[
  {
  	"score":"3",
    "knowledge":"CHECK_PAGE",
    "param":{
    	"PAGE_SIZE":"11907:16840"
    },
    "baseFile":"Word.docx"
  },
  {
  	"score":"3",
    "knowledge":"CHECK_PAGE",
    "param":{
       "PAGE_MARGIN_TOP":1418,
       "PAGE_MARGIN_BOTTOM":1134,
       "PAGE_MARGIN_LEFT":1418,
      "PAGE_MARGIN_RIGHT":1418
    },
    "baseFile":"Word.docx"
  },
   {
  	"score":"3",
    "knowledge":"CHECK_PAGE",
    "param":{
       "PAGE_MARGIN_HEADER":567,
       "PAGE_MARGIN_FOOTER":567
    },
    "baseFile":"Word.docx"
  }
]

3.
[
	{
    	"score":"3",
      	"knowledge":"CHECK_PICTURE_IS_EXIST",
      	"param":{
          "FILE_NAME":"Logo.gif"
		},
      	"location":{"lo":"HEADER"},
      	"baseFile":"Word.docx"
    },
    {
    	"score":"3",
      	"knowledge":"CHECK_HEADER_CONTENT",
        "param":{
        	"CONTENT":"北京向阳中路"
        },
        "baseFile":"Word.docx"
    },
	{
    	"score":"3",
      	"knowledge":"CHECK_BORDERS",
      	"param":{
        	"BOTTOM_COLOR":"FF0000",
          	"BOTTOM_SIZE":"18",
          	"BOTTOM_STYLE":"thinThickSmallGap"
        },
      	"location":{"lo":"HEADER"},
        "baseFile":"Word.docx"
    },
  	{
    	"score":"3",
      	"knowledge":"CHECK_FOOTER_CONTENT",
        "param":{
        	"CONTENT":"北京市海淀区中关村北大街"
        },
      	"baseFile":"Word.docx"
    }
     
]

4
[
    {
        "score":"3",
        "knowledge":"CHECK_TABLE",
        "param":{
            "TABLE_WIDTH_TYPE":"pct"
        },
      	"location":{
      		"lo":"姓名："
    	},
       "baseFile":"Word.docx"
    } 
]
5
[
    {
        "score":"3",
        "knowledge":"CHECK_TABLE_CELL",
        "param":{
            "BORDER_TOP_STYLE":"dotDash",
            "BORDER_BOTTOM_STYLE":"dashDotStroked",
            "BORDER_LEFT_STYLE":"nil",
            "BORDER_RIGHT_STYLE":"nil",
            "VERTICAL_ALINGMENT":"CENTER",
            "ALINGMENT":"CENTER"
        },
        "location":{
            "lp":"0",
            "ls":"0",
            "lo":"家长会通知回执"
        },
       "baseFile":"Word.docx"
    }
]
6
/**
 *lo="TABLE"，当lo为表格时,
 *lp="科目"，lp为该表格区别于其他表格的文本，用于定位表格
 *------------------------*
 *lo="PARAGRAPH",当lo为段落时
 *lp="尊敬的",lp为改段落区别于其他段落的文本,用于定义段落
 *------------------------*
 *当knowledge为CHECK_MAILL_MERGE时(邮寄合并)
 *param(参数)MERGE_FIELD(代码域)可以有多个,
 *用MERGE_FIELD_*(*可以为序号)
 *MERGE_FIELD值不加引号"
 */
[
  {
    "score":"3.0",
    "knowledge":"CHECK_MAIL_MERGE",
    "param":{
      "MERGE_FIELD":"MERGEFIELD姓名"
    },
   "location":{
      "lp":"尊敬的",
      "lo":"PARAGRAPH"
    },
    "baseFile":"Word.docx"
  },
  {
  	"score":"3.0",
    "knowledge":"CHECK_MAIL_MERGE",
    "param":{
      "MERGE_FIELD_1":"MERGEFIELD语文\\#0.00",
      "MERGE_FIELD_2":"MERGEFIELD物理\\#0.00",
      "MERGE_FIELD_3":"MERGEFIELD总分\\#0.00"
    },
    "location":{
      "lo":"TABLE",
      "lp":"科目"
    },
    "baseFile":"Word.docx"
  }
]
7
/**
 *当没有给出具体参数是,只要设置了跟原来不一样的样式即可
 *在值中加“-”表示非
 */
[
    {
        "score":"1.0",
        "knowledge":"CHECK_FONT",
        "param":{
            "FONT_SIZE":"--1",
            "FONT_COLOR":"-FF0000",
            "FONT_FAMILY":"-null"
        },
        "location":{
            "lo":"RUN",
            "lp":"家长会通知",
            "ls":"会"
        },
        "baseFile":"Word.docx"
    },
    {
        "score":"1.0",
        "knowledge":"CHECK_FONT",
        "param":{
            "FONT_SIZE":"--1",
            "FONT_COLOR":"-FF0000",
            "FONT_FAMILY":"-null"
        },
        "location":{
            "lo":"RUN",
            "lp":"期中考试成绩报告单",
            "ls":"绩"
        },
        "baseFile":"Word.docx"
    },
    {
        "score":"0.5",
        "knowledge":"CHECK_PARAGRAPH",
        "param":{
            "ALIGNMENT":"CENTER"
        },
        "location":{
            "lo":"PARAGRAPH",
            "lp":"家长会通知"
        },
        "baseFile":"Word.docx"
    },
    {
        "score":"0.5",
        "knowledge":"CHECK_PARAGRAPH",
        "param":{
            "ALIGNMENT":"CENTER"
        },
        "location":{
            "lo":"PARAGRAPH",
            "lp":"期中考试成绩报告单"
        },
        "baseFile":"Word.docx"
    },
    {
        "score":"0.5",
        "knowledge":"CHECK_PARAGRAPH",
        "param":{
            "ALIGNMENT":"-LEFT",
            "SPACING_BETWEEN":"--1",
            "SPACING_AFTER":"--1"
        },
        "location":{
            "lo":"PARAGRAPH",
            "lp":"时光荏苒"
        },
        "baseFile":"Word.docx"
    },
    {
        "score":"0.5",
        "knowledge":"CHECK_PARAGRAPH",
        "param":{
            "ALIGNMENT":"RIGHT"
        },
        "location":{
            "lo":"PARAGRAPH",
            "lp":"身体健康，万事如意！"
        },
        "baseFile":"Word.docx"
    }
]


8
[
  {
    "score":"3.0",
    "knowledge":"CHECK_MAIL_MERGE",
    "param":{
      "MERGE_CONTENT_1":"宋子丹",
      "MERGE_CONTENT_1":"郑菁华",
      "MERGE_CONTENT_1":"宋子文",
      "MERGE_CONTENT_1":"刘小红",
      "MERGE_CONTENT_1":"李北冥"
      
    },
    "baseFile":"正式家长会通知.docx"
  },
  {
    "score":"3.0",
    "knowledge":"CHECK_PAGE",
    "param":{
      "PAGE_COUNT":"15"
    },
    "baseFile":"正式家长会通知.docx"
  }
]
```

