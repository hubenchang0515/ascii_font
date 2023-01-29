# ascii_font
Generate ASCII font as header of C - 生成 C 语言的 ASCII 字符字体头文件

## Depends - 依赖
* libfreetype

## Usage - 使用
```
Usage: ascii_font <font-file> <font-size>
       ascii_font FreeMono.ttf 16
```

Generated file list:  
* `ascii_font.h` - the header file
* `preview.ppm` - a preview image

Example:  

```
$ ls

$ ascii_font /usr/share/fonts/truetype/ubuntu/UbuntuMono-R.ttf 64
$ ls
ascii_font.h  preview.ppm
```