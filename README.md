<p align="center">
  <img src="https://github.com/longndev/Meo-Bypass/blob/main/meobypass.png?raw=true" alt="Mèo Bypass Logo" width="700"/>
  <h1 align="center">Mèo Bypass (Leaked Resources)</h1>
  <p align="center">A fast and reliable way to bypass Vietnamese shortlinks.</p>
</p>

---

## About

**Mèo Bypass** là một công cụ dùng để bypass link rút gọn **Việt Nam**.
  
- **Cộng đồng Discord:** [Tham gia Discord server](https://discord.gg/r8xQ7K59Mt) để nhận thông báo về các bạn cập nhật mới và nhận hỗ trợ  
- **Cách sử dụng:** https://www.youtube.com/watch?v=3PZa8D3CoE0
  
---

> [!IMPORTANT]  
> Đây là các loại bypass đã ngừng hoạt động, quá hạn hoặc không sử dụng được tiếp, chỉ dùng để tham khảo (những loại bypass có trong repo này đều đã bị loại bỏ khỏi hệ thống **Mèo Bypass chính thức**).

---

## Các phần:
  
- `🔴`: [Xlink](#xlink)  
- `🔴`: [Linktot](#linktot)  
- `🔴`: [Linkngon (.com)](#linkngoncom)
- `🔴`: [Linkngon (.net)](#old-linkngonnet) (**OLD**) 
- `🔴`: [TrafficHay](#traffichay)
- `🔴`: [TrafficUser](#trafficuser)
- `🔴`: [Funlink](#funlink)
- `🔴`: [Toplinks](#toplinks)
- `🔴`: [Yeulink](#old-yeulink) (**OLD**)
- `🔴`: [Trafficseotop](#trafficseotop)
  
---

## Xlink
> Về cơ bản đây là cái bypass mà mình đánh giá thấp nhất về độ ổn định, sau khi **Xlink** thêm quá nhiều nhiệm vụ theo kiểu vào trang sau đó lấy một cái đoạn text được chỉ định trong trang và nhập còn tool thì lại chi làm được loại nhiệm vụ ấn nút thì mình đã quyết định bỏ cái bypass này ra khỏi tool do không còn đc sử dụng nhiều cx như sự thiếu ổn định của nó. Vậy nên mình sẽ giải thích cách nó hoạt động nhé:
  
- **Xlink** sẽ cho mình một cái id của trang nhiệm vụ mỗi khi vào trang qua một req của xlink đc gửi đầu tiên và gắn trong script của html trang nvu vậy nên mình sẽ sử dụng package regex ([re](https://docs.python.org/3/library/re.html)) của Python để match ra id nhiệm vụ

1. Gửi req `GET` đến trang nvu để lấy html của trang:

```py
def xlink_fetch(url):
    headers = {
        'accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7',
        'accept-language': 'en-US,en;q=0.9',
        'priority': 'u=0, i',
        'referer': 'https://www.google.com/',
        'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/139.0.0.0 Safari/537.36',
    }
    response = requests.get(url, headers=headers)
    html = response.text
```

2. Dùng regex match id nhiệm vụ ra khỏi html trang:

```py
match = re.search(r'https://xlink\.co/site-[uv]-[v35]\.js\?id=([a-zA-Z0-9]+)', html)
if match:
    task_id = match.group(1)
    print(f' Mã nhiệm vụ: {task_id}')
    return task_id
```

- Sau khi đã có id nvu của **Xlink**, chúng ta sẽ gửi các req để lấy mã và đếm ngược tgian

1. Gửi req `POST` kèm ttin để bắt đầu quá trình đếm ngược

```py
    headers = {
        'accept': '*/*',
        'accept-language': 'en-GB,en;q=0.7',
        'content-type': 'application/x-www-form-urlencoded',
        'origin': url, # cái url ở đây sẽ là url trang nhiệm vụ, bọn mình phải để url nvu vào trong headers do nhiều bên rút gọn link vì anti bypass nên ktra xem có đg gửi req từ đúng trang nvu của họ k
        'priority': 'u=1, i',
        'referer': url, # tương tự vẫn là url trang nhiệm vụ
        'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/14.0.3 Safari/605.1.15',
    }
    data = {
        'code': code,
        'token': '',
    }

    try:
        response = requests.post('https://xlink.co/step', headers=headers, data=data)
```

2. Sau req đầu này, sẽ có một biến tên là `token` trong json của response, mình sẽ lấy nó ra vì sẽ cần dùng trong req tiếp theo

```py
    json = response.json()
    token = json.get('token')
```

3. Theo đúng như các req đc thực hiện, cminh đến quá trình countdown (nhận tgian đếm ngược), gửi req `POST`

```py
    headers = {
        'accept': '*/*',
        'accept-language': 'en-GB,en;q=0.7',
        'content-type': 'application/x-www-form-urlencoded',
        'origin': url,
        'priority': 'u=1, i',
        'referer': url,
        'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/14.0.3 Safari/605.1.15',
    }
    data = {
        'code': code,
        'token': token, # token đc lấy ở trên
    }

    try:
        response = requests.post('https://xlink.co/countdown', headers=headers, data=data)
```

4. Trong json của req này sẽ có một biến tên là `timer`, đây sẽ là tgian mà cta ph chờ

---
  
## Repository Activity
  
<p align="center">
  <img src="https://repobeats.axiom.co/api/embed/3e765992107484864746304850bf1f072eb2f54f.svg" alt="Mèo Bypass Activity"/>
</p>
  
---
