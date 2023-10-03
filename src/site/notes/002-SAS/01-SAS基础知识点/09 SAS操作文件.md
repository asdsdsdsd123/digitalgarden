---
{"dg-publish":true,"dg-home":null,"aliases":null,"tags":["002-SAS/01-SAS基础知识点"],"permalink":"/002-SAS/01-SAS基础知识点/09 SAS操作文件/","dgPassFrontmatter":true}
---


```sas
/*指定blk所有文件，copy到user目录下*/
rc=system("copy &_rootpath.\blk\*.rtf &_rootpath.\user\*.rtf"); 

/*copy到user目录下后，1.rtf重命名为2.rtf*/
rc=rename("&_rootpath.\user\1.rtf", "&_rootpath.\user\2.rtf", "file");
```

