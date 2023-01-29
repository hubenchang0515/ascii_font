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

Header usage example by SDL2:  

```c
#include <SDL2/SDL.h>

#define ASCII_FONT_IMPLEMENTATION
#include "ascii_font.h"

void draw_text(SDL_Renderer* renderer, const char* text, int x, int y, SDL_Color color)
{
    SDL_Texture* texture = SDL_CreateTexture(renderer, 
                                                SDL_PIXELFORMAT_RGBA32, 
                                                SDL_TEXTUREACCESS_TARGET, 
                                                ASCII_FONT_WIDTH, 
                                                ASCII_FONT_HEIGHT);
    SDL_SetTextureBlendMode(texture, SDL_BLENDMODE_BLEND);

    uint8_t points[4 * ASCII_FONT_WIDTH * ASCII_FONT_HEIGHT];
    
    for (size_t i = 0; text[i] != 0; i++)
    {
        for (size_t y = 0; y < ASCII_FONT_HEIGHT; y++)
        {
            for (size_t x = 0; x < ASCII_FONT_WIDTH; x++)
            {
                points[4 * (y*ASCII_FONT_WIDTH + x)] = color.r;
                points[4 * (y*ASCII_FONT_WIDTH + x) + 1] = color.g;
                points[4 * (y*ASCII_FONT_WIDTH + x) + 2] = color.b;
                points[4 * (y*ASCII_FONT_WIDTH + x) + 3] = ascii_font[text[i]][y][x];
            }
        }
        SDL_UpdateTexture(texture, NULL, points, ASCII_FONT_WIDTH * 4);
        SDL_Rect rect;
        rect.x = x + i * ASCII_FONT_WIDTH;
        rect.y = y;
        rect.w = ASCII_FONT_WIDTH;
        rect.h = ASCII_FONT_HEIGHT;
        SDL_RenderCopy(renderer, texture, NULL, &rect);
    }
    
}

int main(int argc, char* argv[])
{

    SDL_Init(SDL_INIT_VIDEO | SDL_INIT_EVENTS);

    SDL_Window* window = SDL_CreateWindow("freetype2 demo", 
                                            SDL_WINDOWPOS_UNDEFINED, 
                                            SDL_WINDOWPOS_UNDEFINED, 
                                            640, 
                                            480, 
                                            SDL_WINDOW_SHOWN);

    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, SDL_RENDERER_SOFTWARE);

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

        draw_text(renderer, "Hello", 0, 0, (SDL_Color){0, 255, 0});
        SDL_RenderPresent(renderer);
    }

EXIT:
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    
    return 0;
}
```

![header example](https://raw.githubusercontent.com/hubenchang0515/resource/master/ascii_font/example.png)