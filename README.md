### Android使用sdl库显示图片
根据https://wiki.libsdl.org/Android介绍的方法，使用sdl sdk展示图片
#### 思路总体
- 编写sdl的c文件
- 通过androidbuild.sh编译编写的sdl文件
- 运行

#### 环境
SDL：v2-2.0.10
SDL_image：v2.0.1
mac os：v10.13.4
android studio：v3.4.1
ndk：v17c

#### 步骤
- 编写如下sdl文件：
```
#include "SDL.h"
#include "SDL_image.h"

/* XPM */
static char * icon_xpm[] = {
  "32 23 3 1",
  "     c #FFFFFF",
  ".    c #000000",
  "+    c #FFFF00",
  "                                ",
  "            ........            ",
  "          ..++++++++..          ",
  "         .++++++++++++.         ",
  "        .++++++++++++++.        ",
  "       .++++++++++++++++.       ",
  "      .++++++++++++++++++.      ",
  "      .+++....++++....+++.      ",
  "     .++++.. .++++.. .++++.     ",
  "     .++++....++++....++++.     ",
  "     .++++++++++++++++++++.     ",
  "     .++++++++++++++++++++.     ",
  "     .+++++++++..+++++++++.     ",
  "     .+++++++++..+++++++++.     ",
  "     .++++++++++++++++++++.     ",
  "      .++++++++++++++++++.      ",
  "      .++...++++++++...++.      ",
  "       .++............++.       ",
  "        .++..........++.        ",
  "         .+++......+++.         ",
  "          ..++++++++..          ",
  "            ........            ",
  "                                "};

int main(int argc, char *argv[])
{
  SDL_Window *window;
  SDL_Renderer *renderer;
  SDL_Surface *surface;
  SDL_Texture *texture;
  int done;
  SDL_Event event;

  if (SDL_CreateWindowAndRenderer(0, 0, 0, &window, &renderer) < 0) {
    SDL_LogError(SDL_LOG_CATEGORY_APPLICATION,
        "SDL_CreateWindowAndRenderer() failed: %s", SDL_GetError());
    return(2);
  }

  surface = IMG_ReadXPMFromArray(icon_xpm);
  texture = SDL_CreateTextureFromSurface(renderer, surface);
  if (!texture) {
    SDL_LogError(SDL_LOG_CATEGORY_APPLICATION,
        "Couldn't load texture: %s", SDL_GetError());
    return(2);
  }
  SDL_SetWindowSize(window, 800, 480);

  done = 0;
  while (!done) {
    while (SDL_PollEvent(&event)) {
      if (event.type == SDL_QUIT)
        done = 1;
    }
    SDL_RenderCopy(renderer, texture, NULL, NULL);
    SDL_RenderPresent(renderer);
    SDL_Delay(100);
  }
  SDL_DestroyTexture(texture);

  SDL_Quit();
  return(0);
}
```
- 编译
```
cd {SDL2 sdk}/build-scripts
./androidbuild.sh {app包名} {sdl文件}
```
{SDL2 sdk}等中括号需要替换为自己的具体内容
执行以上内容之后会在{SDL2 sdk}/build/下生成{app包名}的app 工程

- 添加SDL2_image内容
```
wget https://www.libsdl.org/projects/SDL_image/release/SDL2_image-2.0.1.tar.gz   //下载SDL2_image库
tar xf SDL2_image-2.0.1.tar.gz                                                   //解压
ln -s {SDL2_image} L2 sdk} {SDL2 sdk}/build/{app包名}/app/jni/                               //使用ln链接(或者直接拷贝)
sed -i -e 's/^LOCAL_SHARED_LIBRARIES.*/& SDL2_image/' {SDL2 sdk}/build/{app包名}/app/jni/src/Android.mk  //添加SDL2_image库
```

#include SDL_image的路径根据需要修改。

- 运行

#### 小结
查看代码，发现生成的activity继承自SDLActivity，本例中所有代码逻辑都是在SDLActivity中实现的，以后有时间研究一下。