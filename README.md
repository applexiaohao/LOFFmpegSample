*最近有同学来询问`FFmpeg`框架的使用方法*
*以下过程中如有问题，请简信我.*

###使用环境

- **Mac OS Yosemite 10.10.5**

###开发工具

- **Xcode 7.0**

- **Terminal**

###需要的文件链接
- [gas-preprocessor](https://github.com/applexiaohao/gas-preprocessor.git)
- [yasm](https://github.com/yasm/yasm)
- [FFmpeg-iOS-build-script](https://github.com/applexiaohao/FFmpeg-iOS-build-script)
- [ffmpeg-2.8](http://www.ffmpeg.org/releases/?C=M;O=D)
- [kxmovie](https://github.com/applexiaohao/kxmovie)

###编译适用于iOS平台的FFmpeg静态库

- 打开终端`Terminal`进入下载后的`gas-preprocessor`文件夹

	- 将文件夹内的`gas-preprocessor.pl`文件拷贝到`/usr/sbin/`目录下
	
	- 修改`/usr/sbin/gas-preprocessor.pl`的文件权限为可执行权限
	
		```
		chmod 777 /usr/sbin/gas-preprocessor.pl
		```

- 执行`FFmpeg-iOS-build-script-master`文件夹内的`build-ffmpeg.sh`
	
	- 编译所有的版本`arm64`、`armv7`、`x86_64`的静态库
	
	```
	./build-ffmpeg.sh
	```
	
	- 编译支持`arm64`架构的静态库
	
	```
	./build-ffmpeg.sh arm64
	```
	
	- 编译适用于`armv7`和`x86_64(64-bit simulator)`的静态库
	
	```
	./build-ffmpeg.sh armv7 x86_64
	```
	
	- 编译合并的版本
	
	```
	./build-ffmpeg.sh lipo
	```

###编译静态库遇到的问题
- `yasm`没有安装的情况

	- 解决方案`1`
	
		- 进入下载后的`yasm`文件夹，通过编译安装命令`yasm`
			
			```
			./configure && make -j 4 && sudo make install
			```
	
	- 解决方案`2`

		- 使用Homebrew包管理器，进行安装
		
			```
			brew install yasm
			```	
		
	- 测试是否安装成功
	
		```
		yasm --verision
		```
- `c test failed`的情况

	- `xcode`环境安装过多，使用`xcode-select`选择默认的工具路径`/Applications/Xcode-beta.app`
		
		```
		sudo xcode-select -s /Applications/Xcode-beta.app
		```
		
###使用编译完成的`FFmpeg`静态库

- **编译成功后，即可将FFmpeg-iOS文件夹(包含include和lib)引入到Xcode内**

- **加入依赖库libz.lib**

- **加入依赖库libbz2.lib**

- **加入依赖库libiconv.lib**

- **如有编译错误，链接文件不存在**

	```
	修改Build Setting 
	Header Search Paths = $(SRCROOT)/LOFFmpegSample/FFmpeg-iOS/include
	```

###使用框架`kxmovie`播放视频
```
    NSString *path = @"";
    NSMutableDictionary *parameters = [NSMutableDictionary dictionary];
    
    // increase buffering for .wmv, it solves problem with delaying audio frames
    if ([path.pathExtension isEqualToString:@"wmv"])
        parameters[KxMovieParameterMinBufferedDuration] = @(5.0);
    
    // disable deinterlacing for iPhone, because it's complex operation can cause stuttering
    if (UI_USER_INTERFACE_IDIOM() == UIUserInterfaceIdiomPhone)
        parameters[KxMovieParameterDisableDeinterlacing] = @(YES);
    
    KxMovieViewController *vc = [KxMovieViewController movieViewControllerWithContentPath:path
                                                                               parameters:parameters];
    [self presentViewController:vc animated:YES completion:nil];
```
