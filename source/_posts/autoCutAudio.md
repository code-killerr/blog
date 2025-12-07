---
title: 自动切分音频(wav文件分离有效声音)
cover: /images/jiabaili1.png
tags: 
	- Python
	- Audio
	- Wav
---
音频太长，音频太多，想把里面人声截取出来调整顺序或者作为素材都可以用这个脚本来实现，脚本的制作主要是因为有场景需要分割出音频中的人声。

背景噪音越小，幅度越平稳切割效果越好。当然有底噪也不怕，脚本自带识别逻辑，甚至还可以手动调参来进行调整。

## 1.导入auto_cut_audio
通过`git`下载或者`clone`到本地使用`setup.py`安装即可。

[https://github.com/code-killerr/Auto_Cut_Audio](https://github.com/code-killerr/Auto_Cut_Audio)

下载完成后，在命令行中进行安装。
```shell
python setup.py install
```

## 2.导入auto_cut_audio
安装完成后可以直接导入`auto_cut_audio`,`auto_cut_audio`可以直接使用预设的函数轻松调用，或者直接使用`HandleAudio`调用类来解锁更多玩法。

## 3.准备完成，开始搞事！
### (1) 自动音频切割
<b>静音段是指在两段声音之间存在的相对平静且音量较低的空隙。</b>

切割将根据音频的静音段进行切割，在说话的间隙进行切割，如果静音段过长将自动切出静音时段，也可以设定是否保留,去除或者合并等。


切割完成的音频可以保留在本地。

```python
import auto_cut_audio
# 保留切割音频
auto_cut_audio.auto_cut_audio(r'audio path',saveFolder=r'save folder')
```
也可以不保留切割的音频。

```python
import auto_cut_audio
# 保存切割音频信息
cut_time = auto_cut_audio.auto_cut_audio(r'audio path')
print(cut_time)
```

也可以使用HandleAudio类来进行更多操作。

```python
import auto_cut_audio
audio = auto_cut_audio.HandleAudio(r'audio_path')
audio.autoSplitAudio()
```

其中还有多个参数可以进行定制，解锁更多玩法
```python
audioPath: str
# 音频文件路径
saveFolder: str
# 切割文件存储路径
emptySecond: float
# 音频静音时长超过emptySecond*2后切割音频/音频前后最少留白，设定为99999,可进行对切割出的音频时长通过changeSecond设定
emptySecond2: float
# 超过设定时长后,音频静音时长超过emptySecond2*2后切割音频/超过设定时长后音频前后最少留白
changeSecond: float
# 设定静音段分贝,如果为None将自动匹配底噪分贝(需背景音仅为底噪)
limitDB: float
# 设定静音段分贝,如果为None将自动匹配底噪分贝(需背景音仅为底噪)
minSilentTime: float
# 切割出空白音频最小时间，设定时长为99999将不切分空白音频
```

### (2) 手动音频切割

如果有了音频的时间，不想自动切割怎么办，这里也提供了切割的函数。
```python
import auto_cut_audio
cut_time = [[0,12],[12,23],[23,30]]
auto_cut_audio.cut_audio('audio path', 'save folder', cut_time)
```

### (3)指定自动切割时间
想要自动切割音频，但是又不想让音频太长，也不想让音频太短，我们也可以指定切割的大概时间，保证切割大于指定时间又不会大的很离谱。
为了防止声音被切开，切割不会精确到指定时间，一般来说会大于指定时间。

```python
import auto_cut_audio
auto_cut_audio.auto_cut_audio_with_time(audio_path='audio path', limit_time='audio duration time')
```

### (4)获取音频信息
不想切音频，只想要音频信息，这里也可以获取音频的基本信息。
```python
import auto_cut_audio
cut_time = auto_cut_audio.get_audio_info('audio path')
# auto_cut_audio.get_audio_info('audio path', saveFolder='save folder') # when you want save the audio
print(cut_time)
```

### (5)获取音频静音时间

静音时间至只存在底噪的时间段，没有人声或者其它声音的时间。

这里我们可以提取出声音前后静音段时间，进行切割等操作。

```python
import auto_cut_audio
cut_time = auto_cut_audio.auto_cut_audio_delete_empty_audio('audio path')
# auto_cut_audio.auto_cut_audio_delete_empty_audio('audio path', saveFolder='save folder') # when you want save the audio
print(cut_time)
```

### (6)音频静音段操作

在进行音频切割的时候可以对音频的静音段进行合并或者去除

合并指将音频静音段合并至切割出来的音频中。

去除是指将静音段从音频中去掉，只留有信息的音频。

<b>进行静音段合并切割</b>

```python
import auto_cut_audio
cut_time = auto_cut_audio.auto_cut_audio_without_empty_audio('audio path')
# auto_cut_audio.auto_cut_audio_delete_empty_audio('audio path', saveFolder='save folder') # when you want save the audio
print(cut_time)
```

<b>进行静音段去除切割</b>

```python
import auto_cut_audio
cut_time = auto_cut_audio.auto_cut_audio_delete_empty_audio('audio path')
# auto_cut_audio.auto_cut_audio_delete_empty_audio('audio path', saveFolder='save folder') # when you want save the audio
print(cut_time)
```

代码核心为音频底噪的判断，代码中有两种判断方式，根据振幅判断以及根据音量大小进行判断，由于太菜没有使用傅里叶进行判断😭，等啥时候学会傅里叶了就可以更新了🙃。

