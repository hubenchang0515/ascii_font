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

Read the array as `ascii_font[character][y][x]` to get pixel gray scale value.

> You can also look on the gray scale value as alpha channel value.

Example:  

```
$ ls

$ ascii_font /usr/share/fonts/truetype/freefont/FreeMono.ttf 32
$ ls
ascii_font.h  preview.ppm
```

Header usage example by SDL2:  

```c
#include <SDL2/SDL.h>

#define ASCII_FONT_IMPLEMENTATION
#include "ascii_font.h"

void draw_char(SDL_Renderer* renderer, int x, int y, char ch, Uint8 r, Uint8 g, Uint8 b)
{
    for (int dy = 0; dy < ASCII_FONT_HEIGHT; dy++)
    {
        for (int dx = 0; dx < ASCII_FONT_WIDTH; dx++)
        {
            SDL_SetRenderDrawColor(renderer, r, g, b, ascii_font[(size_t)ch][dy][dx]);
            SDL_RenderDrawPoint(renderer, x + dx, y + dy);
        }
    }
}

void draw_text(SDL_Renderer* renderer, int x, int y, const char* text, Uint8 r, Uint8 g, Uint8 b)
{    
    for (size_t i = 0; text[i] != 0; i++)
    {
        draw_char(renderer, x + i * ASCII_FONT_WIDTH, y, text[i], r, g, b);
    }
}

int main(int argc, char* argv[])
{
    (void)argc;
    (void)argv;

    SDL_Init(SDL_INIT_VIDEO | SDL_INIT_EVENTS);

    SDL_Window* window = SDL_CreateWindow("ascii_font demo", 
                                            SDL_WINDOWPOS_UNDEFINED, 
                                            SDL_WINDOWPOS_UNDEFINED, 
                                            26 * ASCII_FONT_WIDTH, 
                                            7 * ASCII_FONT_HEIGHT, 
                                            SDL_WINDOW_SHOWN);

    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, SDL_RENDERER_SOFTWARE);
    SDL_SetRenderDrawBlendMode(renderer, SDL_BLENDMODE_BLEND);

    while (1)
    {
        SDL_SetRenderDrawColor(renderer, 0, 0, 0, 255);
        SDL_RenderClear(renderer);

        SDL_Event ev;
        while(SDL_PollEvent(&ev) > 0)
        {
            if (ev.type == SDL_QUIT)
                goto EXIT;
        }

        draw_text(renderer, 0, 0, "ascii_font example", 255, 255, 255);
        draw_text(renderer, 0, ASCII_FONT_HEIGHT, "ABCDEFGHIJKLMNOPQRSTUVWXUZ", 255, 0, 0);
        draw_text(renderer, 0, 2 * ASCII_FONT_HEIGHT, "abcdefghijklmnopqrstuvwxyz", 0, 255, 0);
        draw_text(renderer, 0, 3 * ASCII_FONT_HEIGHT, "ABCDEFGHIJKLMNOPQRSTUVWXUZ", 0, 0, 255);
        draw_text(renderer, 0, 4 * ASCII_FONT_HEIGHT, "abcdefghijklmnopqrstuvwxyz", 255, 255, 0);
        draw_text(renderer, 0, 5 * ASCII_FONT_HEIGHT, "ABCDEFGHIJKLMNOPQRSTUVWXUZ", 0, 255, 255);
        draw_text(renderer, 0, 6 * ASCII_FONT_HEIGHT, "abcdefghijklmnopqrstuvwxyz", 255, 0, 255);

        SDL_RenderPresent(renderer);
    }

EXIT:
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    
    return 0;
}
```

![header-usage-example](https://raw.githubusercontent.com/hubenchang0515/resource/master/ascii_font/example.png)