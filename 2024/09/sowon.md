#rust

主要是用Rust clone [sowon - Starting Soon Timer for Tsoding Streams](https://github.com/tsoding/sowon) 用来学习的项目。

[Sowon Rust](https://github.com/Ysoding/sowon-rust)

![](https://raw.githubusercontent.com/Ysoding/sowon-rust/main/res.gif)

看C版的代码，没搞过SDL2，看不懂的API，就看C文档，看Rust中sdl2 binding的文档 。

API设计形式可能一样，有需要包装类，用动态指针更好的管理内存。

[SDL2 Rust Doc](https://docs.rs/sdl2/latest/sdl2/index.html)  
[SDL2 Doc](https://wiki.libsdl.org/SDL2/CategoryAPI)

主要实现思虑是，使用包含数字的图片加载成sdl中的texture，然后每次根据窗口的大小计算需要画这些数字的位置。

```rust
fn initial_pen(
    window: &Window,
    pen_x: &mut i32,
    pen_y: &mut i32,
    user_scale: f32,
    fit_scale: &mut f32,
) {
    let (w, h) = window.size();

    let text_aspect_ratio = TEXT_WIDTH as f64 / TEXT_HEIGHT as f64;
    let window_aspect_radio = w as f64 / h as f64;
    *fit_scale = if text_aspect_ratio > window_aspect_radio {
        w as f32 / TEXT_WIDTH as f32
    } else {
        h as f32 / TEXT_HEIGHT as f32
    };

    let effective_digit_width = (CHAR_WIDTH as f32 * user_scale * *fit_scale).floor() as i32;
    let effective_digit_height = (CHAR_HEIGHT as f32 * user_scale * *fit_scale).floor() as i32;
    *pen_x = w as i32 / 2 - effective_digit_width * CHARS_COUNT as i32 / 2;
    *pen_y = h as i32 / 2 - effective_digit_height / 2;
}
```

怎么实现数字抖动的？其实就是在取texture图像的时候，每次根据抖动频率来进行特定的偏移，然后取模保整处于一个循环中。

渲染，可以看到src_rect会根据wiggle_index来确定texture中的要取的高度位置。

```rust
fn render_digit_at(
    renderer: &mut WindowCanvas,
    texture: &Texture,
    digit_index: usize,
    wiggle_index: usize,
    pen_x: &mut i32,
    pen_y: &mut i32,
    user_scale: f32,
    fit_scale: f32,
) {
    let effective_digit_width = (CHAR_WIDTH as f32 * user_scale * fit_scale).floor() as i32;
    let effective_digit_height = (CHAR_HEIGHT as f32 * user_scale * fit_scale).floor() as i32;
    let src_rect = Rect::new(
        (digit_index * CHAR_WIDTH as usize) as i32,
        (wiggle_index * CHAR_HEIGHT as usize) as i32,
        SPRITE_CHAR_WIDTH,
        SPRITE_CHAR_HEIGHT,
    );

    let dst_rect = Rect::new(
        *pen_x,
        *pen_y,
        effective_digit_width as u32,
        effective_digit_height as u32,
    );

    renderer.copy(texture, src_rect, dst_rect).unwrap();
    *pen_x += effective_digit_width;
}



```

抖动频率控制

```rust
        if wiggle_cooldown <= 0.0 {
            wiggle_index += 1;
            wiggle_cooldown = WIGGLE_DURATION;
        }
        wiggle_cooldown -= fps_dt.dt;
```

还有个比较有意思的是帧率管理，在fps为60时，代表一秒中要迭代渲染60次画面。

通过 `frame_end()` 确保每帧不会超出设定的 `fps_cap`的每帧时隔时间，通过帧延迟来控制渲染速度，防止帧率过高。

这样就确保无论实际帧率如何，游戏或动画中的移动、物理效果等都能按照相同的速率运行。这解决了帧率不稳定可能导致的动画速度变化问题。

然后我的们控制的时间是秒为单位的，所以每次渲染加等于dt就行。

```rust
struct FpsDeltaTime {
    pub frame_delay: u32, // Frame delay in milliseconds
    pub dt: f32,          // Delta time in seconds
    pub last_time: u64,
    timer_subsystem: TimerSubsystem,
}

impl FpsDeltaTime {
    pub fn new(fps_cap: u32, timer_subsystem: TimerSubsystem) -> Self {
        Self {
            last_time: timer_subsystem.performance_counter(),
            timer_subsystem,
            frame_delay: 1000 / fps_cap,
            dt: 0.0,
        }
    }

    pub fn frame_start(&mut self) {
        let now = self.timer_subsystem.performance_counter();
        let elapsed = now - self.last_time;
        self.dt = elapsed as f32 / self.timer_subsystem.performance_frequency() as f32;
        self.last_time = now;
    }

    pub fn frame_end(&self) {
        let now = self.timer_subsystem.performance_counter();
        let elapsed = now - self.last_time;
        let cap_frame_end = (((elapsed as f32) * 1000.0)
            / (self.timer_subsystem.performance_frequency() as f32))
            as u32;

        if cap_frame_end < self.frame_delay {
            self.timer_subsystem.delay(self.frame_delay - cap_frame_end);
        }
    }
}

```
