# Fortune Lyric - 随机洛天依歌曲歌词

> 总有一些不可思议的歌词<br>轻而易举地唱迸<br>人们历经风吹雨打的心
> 
> 　　　　-- _[幽天歌](https://space.bilibili.com/592784581), 「[中V宝藏歌曲挖掘站](https://vcmusic.org)」成员_

此项目提供了一个 [洛天依](https://zh.wikipedia.org/zh-cn/%E6%B4%9B%E5%A4%A9%E4%BE%9D) 的经典曲目歌词选段的数据库，并以此实现了一个获得随机歌词的程序。

灵感来源：[一言](https://hitokoto.cn/)、[Fortune](https://en.wikipedia.org/wiki/Fortune_(Unix))。

歌词保存在 [database.txt](database.txt) 中。若您需要提交歌词，请提交 PR 或者联系 i@lty.vc。提交歌词时，请遵守 #[数据库格式与约定](#数据库格式与约定)。

## 使用方法

Fortune Lyric 支持多种使用方式：
 - **在 Bash 中使用**：直接输出歌词、作为 SSH Banner (MOTD) 或通过管道输出给其他程序。
 - **通过 API 调用**：适用于个人网站等网页场景。
 - **下载数据库**：在自己的程序中嵌入数据库，自行实现随机歌词。

无论您使用何种方式，都应遵守本项目的版权规定。请参见 [版权](#版权) 一节。

您可以通过下面的链接获取最新的发布版本，如果 GitHub 下载过慢，请使用我们的 API 下载。

| GitHub Release                   | API                       | Description           |
| -------------------------------- | ------------------------- | --------------------- |
| [fortune-lyric.sh]               | [bash]                    | 单行歌词 Shell 脚本     |
| [fortune-lyric-multiline.sh]     | [bash-multiline]          | 多行歌词 Shell 脚本     |
| [fortune-lyric.json]             | -                         | JSON 格式              |
| [fortune-lyric.min.json]         | [json]                    | JSON 格式（最小化）     |
| [fortune-lyric.txt]              | [text]                    | 单行文本格式           |
| -                                | GET https://lty.vc/lyric  | API 调用接口           |

[fortune-lyric.sh]:           https://github.com/luotianyi-dev/fortune-lyric/releases/latest/download/fortune-lyric.sh
[fortune-lyric-multiline.sh]: https://github.com/luotianyi-dev/fortune-lyric/releases/latest/download/fortune-lyric-multiline.sh
[fortune-lyric.json]:         https://github.com/luotianyi-dev/fortune-lyric/releases/latest/download/fortune-lyric.json
[fortune-lyric.min.json]:     https://github.com/luotianyi-dev/fortune-lyric/releases/latest/download/fortune-lyric.min.json
[fortune-lyric.txt]:          https://github.com/luotianyi-dev/fortune-lyric/releases/latest/download/fortune-lyric.txt
[database.txt]:               https://github.com/luotianyi-dev/fortune-lyric/releases/latest/download/database.txt
[bash]:                       https://lty.vc/lyric/bash
[bash-multiline]:             https://lty.vc/lyric/bash-multiline
[json]:                       https://lty.vc/lyric/json
[text]:                       https://lty.vc/lyric/text
[database]:                   https://lty.vc/lyric/database

### 在 Bash 中使用

**单行歌词版：**
```bash
curl -L https://lty.vc/lyric/bash -o /usr/local/bin/fortune-lyric && chmod +x /usr/local/bin/fortune-lyric
```
显示效果如：
```
$ fortune-lyric
思念的含义在无尽生命中淡去 帷幕落下喝彩响起 片刻后都沉寂
```

**多行歌词版：**
```bash
curl -L https://lty.vc/lyric/bash-multiline -o /usr/local/bin/fortune-lyric && chmod +x /usr/local/bin/fortune-lyric
```
显示效果如：
```
$ fortune-lyric
思念的含义在无尽生命中淡去
帷幕落下喝彩响起 片刻后都沉寂

                -- COP 《为了你唱下去》, 2016
```

### 配置为 SSH Banner
**Ubuntu / Debian**
将上面的命令替换为：
```bash
curl -L <安装地址> -o /etc/update-motd.d/25-fortune-lyric && chmod +x /etc/update-motd.d/25-fortune-lyric
```

**Arch Linux**
```bash
pacman -S run-parts bash curl
mkdir -p /etc/update-motd.d
echo 'session    optional    pam_exec.so    stdout /usr/bin/run-parts /etc/update-motd.d' >> /etc/pam.d/system-login
curl -L <安装地址> -o /etc/update-motd.d/25-fortune-lyric && chmod +x /etc/update-motd.d/25-fortune-lyric
```

### 通过网页调用
直接打开 https://lty.vc/lyric 即可获得随机歌词。

```
$ curl -L lty.vc/lyric

思念的含义在无尽生命中淡去
帷幕落下喝彩响起 片刻后都沉寂
```

详细的 API 文档请参见 [API 文档](https://docs.lty.vc/)。

## 数据库格式与约定
数据收录要求为：
 1. 必须是 **洛天依** 演唱的原创歌曲，语言不限。
 2. 推荐向我们提交那些有代表性、引起大家共鸣的歌词。

### 数据库的结构
数据库 (database) 由「块 (quoteblock)」组成，每一块包含若干个「行 (line)」。

每一块包含以下内容：
 - 所引用的歌词内容，称为「歌词行 (`lyricLine`)」
 - 歌词的作者、歌曲名称、歌曲年份，在一行内书写，称为「元数据行 (`metadataLine`)」

块与块之间用 30 个 dash (`-`) 分隔。行与行之间用 Unix-Style 换行符 (`<LF>`) 分隔。

### 块的格式
```
歌词的第一行
歌词的第二行(可选)
歌词的第三行(可选)
歌词的第四行(可选)
        -- 歌词作者 《歌曲名称》, 歌曲年份
```

对于块的格式，另外有以下约定：
 1. 任意行，不管是元数据行，还是歌词行，行前、行后的空格或不可见字符均会被忽略。
 2. **歌词行**中可以包含表示停顿或语义分割的空格。一般来说，是否加空格，按照原作者提供的歌词文本为准。歌词中添加的空格，始终是英文空格（`U+0020`），不要添加中文全角空格（`U+3000`）。
 3. 元数据行缩进 8 个空格，即第 9 个字符（数组下标为 8）为 `-`。这是为了统一风格、使文本美观易读而制定的规范。程序处理时，应当忽略元数据头、尾的空格。元数据行末尾不应有任何空格或不可显示字符。
 4. **元数据行**的歌曲作者、歌曲名称、歌曲年份前，均应添加英文空格（`U+0020`）。将所有不可显示字符表示出来的元数据行应是如下所示：
    ```
    <SPACE*8>--<SPACE><歌词作者><SPACE>《<歌曲名称>》,<SPACE><歌曲年份><LF>
    ```
 5. **歌词作者**为**歌曲的作词人，而不是歌曲的作曲、编曲、策划、调教或 UP 主**。请注意区分，并从 Staff 表中查找核对。歌词作者可以有多个，并始终在程序中应以数组形式存储。有多个作词人的，用英文斜杠 `/` 分开。
 6. **歌曲名称**前、后为中文书名号。
 7. **歌曲年份**为歌曲的发布年份，为 4 位阿拉伯数字，以公元计算，时区始终为发布时的北京时间所在时区。歌曲发布于网络平台的，为平台显示的首版投稿发布时间；发布于专辑的，为专辑发布时间；发布于预售专辑的，为专辑开始出荷的时间。

### 长度限制
在长度限制的说明中，遵循以下定义：
 1. 字符长度：字符串「按照 GB18030 编码的字节数」与「显示长度」中较大者；即 `strlen = max(bytelen, displen)`
 2. 显示长度：字符串在终端中显示时的长度，按照中日韩越 (CJKV) 字符宽度为 2、英文字母及西欧字符宽度为 1 计算；即 `displen = str.forEach(char => char.isCJKV ? 2 : 1).sum()`
 3. 字节长度：字符串在 GB18030 编码下的字节；即 `bytelen = str.encode('gb18030').length`

基于以上定义，字符长度遵循以下限制：
 1. 任意一个块中，歌词不得超过 `4` 行。（不含元数据行）
 2. 任意一行（不含元数据行）的字符长度不得超过 `30`。
 3. 将所有行（不含元数据行）用英文空格（`U+0020`）连接后，字符长度不超过 `80`。

### 数据库格式的 ABNF 描述
```abnf
database = quoteblock *(quoteblockBreak quoteblock)
quoteblock = 1*4lyricLine metadataLine
quoteblockBreak = 30"-"
lyricLine = 1*30VCHAR LF
metadataLine = 8SP 2"-" SP author SP "《" songName "》," SP year LF
author = *VCHAR
songName = *VCHAR
year = 4DIGIT
```

注：`VCHAR` 为可见字符，在本文定义中不仅包括 ASCII 可见字符，也包括 Unicode 可见字符。

## 版权

**请注意：此项目不是开源软件。** 此项目的分发、使用、修改、衍生等相关规定均比开源项目更为严格。

 1. 代码部分。代码部分指的是除了本项目的 `database.txt` 以外，所有在本项目 Git 仓库中的代码。此项目的代码部分由 ChatGPT 所编写。目前 AI 编写的代码版权所属在法律界尚不明确，用户应自行承担相关风险。此项目的贡献者放弃未来一切可能的对此项目代码部分的版权主张。
 2. 数据库部分。数据库指的是本项目中 `database.txt` 的部分。此项目的数据库部分为歌词，其著作权属于原著作权人。您在使用过程中，应遵守中文虚拟歌手社区的相关规定，包括但不限于[《Vsinger同人创作规约》](https://vsinger.com/aboutus#%E7%89%88%E6%9D%83%E8%AF%B4%E6%98%8E)。此外，您还需要遵守歌曲原作者的二次创作规定。您对此项目的任何分发、使用、修改、衍生等行为，均应确保符合歌词原作者的相关规定或获得授权。此项目的贡献者基于对相关数据的收集、整理、组织的贡献，要求您在使用此项目数据库部分及其产物时，遵守更加严格的限制。
 3. API 调用。若你使用 API 调用方式使用本项目，即代表您同意 lty.vc 的服务政策和隐私条款。lty.vc 有权收集您调用 API 的服务器信息、软件信息和网络信息，若通过前端 CROS 方式调用，lty.vc 亦会收集您的用户的设备信息、浏览器信息和网络信息。您应将 lty.vc 的隐私政策及所收集的信息告知您的用户。以商业为目的的网站、App、软件，包括但不限于要求用户付费以解锁内容、通过网站销售实体或虚拟产品的网站，不得使用本项目的 API 服务。lty.vc 有权对调用 API 的网站、App、软件进行评估，若相关网站、App、软件含有违反法律法规规定的内容，或含有对虚拟歌手及虚拟歌手创作者攻击、侮辱、抹黑内容的，lty.vc 有权终止其服务。通过 API 调用的，应在网站底部或友情链接页面，标明使用了由「传输控制协议TCP」开发的 lty.vc API 服务，并标明「传输控制协议TCP」的哔哩哔哩账号地址 https://space.bilibili.com/1425000748 和 lty.vc 的 API 地址 https://docs.lty.vc/ 。
 4. 产物分发。产物指的是本项目的编译结果，即将本项目的数据库部分输入本项目的代码部分，经过代码部分处理得到的文件。使用 GitHub Release 及 lty.vc 分发的产物，为官方产物。用户使用此项目的 Git 仓库，运行代码部分得到的文件，为非官方产物。由于产物内包括数据库部分的内容，故分发、使用、修改产物或将产物嵌入您的产品，亦需遵守中文虚拟歌手社区的相关规定及歌曲原作者的二次创作规定，或获得相关授权。在分发产物时，应向用户提供此项目的许可证，即本文档「版权」一节 1 至 10 条的全部内容。
 5. 非商业使用。您不得以商业目的使用此项目。您不得将此项目的数据库部分或产物嵌入任何商用产品或服务。以企业、事业单位、非盈利组织、社会团体及任何法人性质的组织为主体开办的服务或生产的产品，不得嵌入此项目的数据库部分或产物。您不得在雇主、学校、研究所等您所在单位的计算机上使用此项目的产物，除非您的所在单位为以中文虚拟歌手创作为主要业务。
 6. 与您的产品隔离。若您在您的项目、产品或服务中使用、分发或嵌入此项目的数据库部分或产物，无论您的项目、产品或服务是否开发源代码、是否为商业性质，您都应采取措施将此项目的数据库部分或产物与您的项目、产品或服务隔离。对未形成有效隔离的项目，您的项目、产品或服务的版权声明及主张，对使用、分发或嵌入此项目数据库或产物的部分无效。您不得对本项目及其产物进行再许可。
 7. 修改与衍生。您可以随意修改与衍生此项目的代码部分。您可以修改此项目的数据库及其产物，但不得向任何第三人分发修改后的内容及其产物，除非您以相同的许可证、以相对便利可获取的方式、不设门槛与条件地公开您修改后的内容，并按指定的方式向此项目的维护者发送通知。指定的通知发送方式为 GitHub Issue 或向 support@lty.vc 发送电子邮件。您应在通知中包含您公开修改后内容的地址。您的修改仅限于向数据库中加入虚拟歌手为原唱演唱的中文歌曲，不得加入其他类型的歌曲，亦不得删除数据库中的条目。
 8. 免责声明。此项目的贡献者不对此项目及其产物的安全性、适用性、适销性提供任何明示或暗示的担保与保证。无论用户以何种方式使用此项目及其产物，作者均不对其用户使用此项目及其产物说造成的损害、责任赔偿、人身伤害或任何其他后果负责。
 9. bang15便士条款。无论因何种情况，此项目的贡献者对任何人拥有法定的求偿或追索的权利，其金额止于 9 英镑 15 便士。
