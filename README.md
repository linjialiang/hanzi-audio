# hanzi-audio

500 个常用汉字标准发音音频文件，通过 GitHub + jsdelivr CDN 为 H5 和微信小程序提供音频资源。

## 文件结构

```
hanzi-audio/
├── mp3/            # 500 个汉字发音 mp3 文件
│   ├── 一_yī.mp3
│   ├── 二_èr.mp3
│   └── ...
├── index.json      # 汉字→拼音→文件路径 索引
└── README.md
```

## 文件命名规则

每个 mp3 文件命名格式：`{汉字}_{拼音}.mp3`

例如：`中_zhōng.mp3`、`大_dà.mp3`

## CDN 使用

### jsdelivr CDN（海外用户）

```
https://cdn.jsdelivr.net/gh/{用户名}/hanzi-audio@main/mp3/{汉字}_{拼音}.mp3
```

示例：
```
https://cdn.jsdelivr.net/gh/linjialiang/hanzi-audio@main/mp3/中_zhōng.mp3
```

索引文件：
```
https://cdn.jsdelivr.net/gh/{用户名}/hanzi-audio@main/index.json
```

### raw.githubusercontent（备用）

```
https://raw.githubusercontent.com/{用户名}/hanzi-audio/main/mp3/{汉字}_{拼音}.mp3
```

## H5 使用示例

```html
<script>
// 1. 加载索引
const BASE = 'https://cdn.jsdelivr.net/gh/{用户名}/hanzi-audio@main';
const res = await fetch(`${BASE}/index.json`);
const index = await res.json();

// 2. 按汉字播放发音
function playAudio(char) {
  const entry = index[char];
  if (!entry) return;
  const audio = new Audio(`${BASE}/${entry.file}`);
  audio.play();
}

playAudio('中');
</script>
```

## 微信小程序使用示例

```javascript
// 1. 在小程序管理后台 → 开发管理 → 服务器域名 → downloadFile合法域名
//    添加: https://cdn.jsdelivr.net

// 2. 代码中使用
const BASE = 'https://cdn.jsdelivr.net/gh/{用户名}/hanzi-audio@main';

// 加载索引
wx.request({
  url: `${BASE}/index.json`,
  success(res) {
    const index = res.data;
    // 按汉字播放发音
    function playAudio(char) {
      const entry = index[char];
      if (!entry) return;
      const audio = wx.createInnerAudioContext();
      audio.src = `${BASE}/${entry.file}`;
      audio.play();
    }
    playAudio('中');
  }
});
```

## 国内用户注意事项

jsdelivr CDN 在部分地区可能存在访问不稳定的情况，如遇此问题可考虑：

1. 将 mp3 文件部署到国内云存储（如腾讯云 COS、阿里云 OSS）
2. 使用自有服务器托管
3. 在小程序中使用腾讯云 COS 作为音频源

索引文件 `index.json` 中的 `file` 字段为相对路径，方便替换为任意 CDN 基础地址。
