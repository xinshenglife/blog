## glad、glew、glfw、Freeglut区别
另外在这里科普一下glad、glew、glfw、Freeglut的区别：   
glew（The OpenGL Extension Wrangler Library）是对底层OpenGL接口的封装，可以让你的代码跨平台。  
glad与glew作用相同，可以看作它的升级版。  
Freeglut（OpenGL Utility Toolkit）主要用于创建OpenGL上下文、接收一些鼠标键盘事件等等。  
glfw（Graphics Library Framework）是Freeglut升级版，作用基本一样。  
通常来说glad和glfw配合使用，比如我上面发的那个网站就是。glew和Freeglut配合使用，比如OpenGL红宝书上面的例子。
https://www.zhihu.com/question/264132001/answer/729626917


## 接口、显卡驱动和 dlsym（dynamic linking system） OpenGL 是接口与规范（spec）
glad、glew、glfw、Freeglut都是dynamic linking system 动态链接库  Loading library

## 窗口化和 OpenGL 上下文（Context）是什么
https://www.zhihu.com/question/344133077/answer/2735715316  
首先理解没有窗口化时期的 GUI早期的 OS 一般是单一的 console 输入输出对于 linux 下是开机 init 和 login 。这里主要要理解的 console、terminal（shell 就不用说了，shell 是一个处理文本命令转为 exec 或者 syscall 的程序，非GUI 的UI）。terminal 可以概括为是一套环境，包含文本 IO，或者说他是一个程序，负责 IO 与 shell 互动。  

显卡的 framebuffer 和 OS 的显示缓冲区：实际，早期的系统其终端显示的字体是由显卡来实现的。这种模式在显卡上叫做文本模式。相对的是图形模式，图形模式的 framebuffer 就是一个分辨率（e.g. 4096*2160）的 2d-array，而文本模式，会根据字符大小宽度计算出最大行数和列数。所以说 DOS 游戏其实就是工作在像素模式，显存是像素。  

这也是为什么 dos 进游戏可能会黑屏一会儿，因为从文本模式转像素模式。而 window 旧版本进游戏是 window 窗口管理器直接把全屏缓冲区送 OpenGL 程序或者 Direct X 程序，所以换缓冲区可能会黑屏一小会，而窗口化不会但是窗口化还要走一层窗口管理器再渲染，性能变差。现在 win10 应该有一些改进，具体我不清楚了。

### GDI 是什么
 GDI 是微软的 graphics device interface。一般来说，GPU能做的事情 CPU 也能做。对于 2D 的东西来说，其实CPU也能胜任（漂亮的 GUI 会有很多浮点运算，不过如果不要太多炫酷效果，实际整数也够用了，想各自画图算法 Breshenham 都是整数算法），GPU优势是处理 3D （浮点数，硬件执行算法）很厉害。GDI 在 Windows 7 后支持 GPU 硬件加速。

### Windows terminal
但是如果走了窗口化，就不能依赖显卡的文本模式了，所以实际 Windows 现在的 cmd、windows terminal 实际是一个显卡文本模式的模拟器。查看最新的 UWP 应用 windows terminal  的源码目录（他是 CPP 写的，说是 GPU 绘制，好看快速）里面，renderer 有两个渲染器，一个是 GPU 的一个是 gdi based 。

### 如何实现一个窗口管理器
*注：这个知乎有一些很好的问题和回答，

比如x系统相关的。比较抽象的方案是通过提供一整套组件库等上层抽象（WPF、win32 GUI 程序），比较底层的方案是提供一系列 frame buffer 的管理器。应用请求生成一个 window 的时候，就给他一个 framebuffer（OpenGL、Direct X 应用程序的方案）。对于窗口切换和 resize 等东西，都用事件驱动来做。每次通过计算激活窗口，进行 Z-buffer 遮挡测试，临时缓冲区等方案。还有 diff 等进行合并，以及添加阴影特效等。他很复杂是因为比如你开一个窗口一直需要实时更新的（比如一个计时器一直更新），就算他不是 active，也要实时渲染做 composition 的。每次鼠标移动窗口的时候，要重新做 z-buffer depth 测试。因为 OpenGL 设计上是 3D 的，这里以 2D 图形库 SDL 来举例，OS 给到图形库是一个 framebuffer。以及 SDL 直接操作 OS 提供的 framebuffer 的例子。SDL 中，窗口缓冲区的上下文结构体为 SDL_Surface（由于 SDL 还管理事件和媒体，因此 SDL_Window 可以认为是图形上下文 Surface 加上一些事件处理的东西），主力一面有个 void* pixels，这个东西就是 Windows 系统给到图形库 SDL 的一个特定 window 的 framebuffer 经过 sdl 抽象的一个缓冲区内存:
```
typedef struct SDL_Surface {
      Uint32 flags; 
      SDL_PixelFormat *format;
      int w, h;
      Uint16 pitch;
      void *pixels; /** 看这里！**/
      SDL_Rect clip_rect;
      int refcount;
} SDL_Surface;
```
这里给一个 Windows 下的 SDL 示例程序直接操作 OS 提供的 framebuffer：
```
#include <SDL2/SDL.h>

constexpr int SCREEN_WIDTH = 640;
constexpr int SCREEN_HEIGHT = 480;

SDL_Window* InitSDLWindow(const char* title);
void Wait2SecsThenExit();

SDL_Window* window;

void SetPixel(SDL_Surface *surface, int x, int y, Uint32 pixel);

//SDL demo main
int main(int argc, char* argv[]) {
    window = InitSDLWindow("SDL framebuffer demo");
    auto surface = SDL_GetWindowSurface(window);
    // 在缓冲区画一条线：
    for(int i = 0; i<SCREEN_WIDTH; i++){
      int j1 = 240, j2 = 241, j3 = 242;
      SetPixel(surface, i, j1, 255);
      SetPixel(surface, i, j2, 255);
      SetPixel(surface, i, j3, 255);
    }
    SDL_UpdateWindowSurface(window);
    Wait2SecsThenExit();
    return 0;
}

SDL_Window* InitSDLWindow(const char* title) {
    SDL_Window *window = NULL;
    SDL_Init(SDL_INIT_VIDEO);
    window = SDL_CreateWindow(title,
        SDL_WINDOWPOS_UNDEFINED,SDL_WINDOWPOS_UNDEFINED,
        SCREEN_WIDTH,SCREEN_HEIGHT,
        SDL_WINDOW_SHOWN 
    );
    return window;
}

void SetPixel(SDL_Surface *surface, int x, int y, Uint32 pixel){
    Uint32 * const target_pixel = (Uint32 *) ((Uint8 *) surface->pixels
                                             + y * surface->pitch
                                             + x * surface->format->BytesPerPixel);
    *target_pixel = pixel;
}

void Wait2SecsThenExit(){
    SDL_Delay(2 * 1000);
    SDL_DestroyWindow(window);
}
```
这里就是在 SetPixel 函数中直接操作一个像素数组。示例结果：
### OpenGL 的 context
这么一来，就能明白了，OpenGL 无法提供窗口管理的功能，这种东西一般都要 OS 提供。 Linux 下通过 x window 系统，window 下是 wgl。所以读者可以在上面的源码中看到一个是调用了 glx 的东西，一个是调用的 wgl 的东西，其实这些就是 OS 针对 OpenGL 做的一些窗口 handle 的抽象接口。窗口在 OpenGL context 的存在形式是一个 framebuffer 的描述信息。而用 context 本身是一个状态机模式，整个 OpenGL 就是一个巨大对象，操作不通过对象指针进行，而是全局的 context 指针，所以 Open GL 是通过 setContext 来切换所有函数的操作对象的。OpenGL 的函数也没有返回值，这个是经典的 C style 了，因为返回一个对象很复杂，所以一般用参数同时负责 in out。
### GLFW、GLU
glut 是 utility，他提供了上面说的创建窗口和上下文的事情，防止要自己做一堆脏活。还顺便把键盘鼠标这些和 windows /Linux 的窗口系统的事件负责的东西完成了。glut 还提供了画立体图形、茶壶等的函数，不用自己写 glBegin 和传顶点什么的。最后一次更新是很多年前。
glfw：一个比较新的还在维护的库。It provides a simple API for creating windows, contexts and surfaces, receiving input and events. 他不止支持 OpenGL 还支持 Vulkan （不过 Vulkan 名义上是 OpenGL 的正统不向下兼容的后继）。
结果：一般采用 glad 获取一堆 glxxx 函数的函数指针。用 glfw 管理操作系统的窗口管理器给到的 framebuffer，然后OpenGL 在上面画画。
