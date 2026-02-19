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

## Sponsor

**Bacon But Pro** là đơn vị đã giúp **Mèo Bypass** phát triển và tạo ra các loại bypass mới.
  
- **Cộng đồng Discord Bacon But Pro:** [Tham gia Discord server](https://discord.com/invite/baconbutpro)      
     
<p align="center">
  <img src="https://raw.githubusercontent.com/longndev/Meo-Bypass-Leaked-Resources/refs/heads/main/assets/bacon-but-pro.png" alt="Bacon But Pro" width="550"/>
</p>

---

## Các phần:
  
- `🔴`: [Xlink](#xlink)  
- `🔴`: [Linktot](#linktot)  
- `🔴`: [Linkngon (.com)](#linkngoncom)
- `🔴`: [Linkngon (.net)](#linkngonnet)  
- `🔴`: [TrafficHay](#traffichay)
- `🔴`: [TrafficUser](#trafficuser)
- `🔴`: [Funlink](#funlink)
- `🔴`: [Toplinks](#toplinks)
- `🔴`: [Yeulink](#old-yeulink) (**OLD**)
- `🔴`: [Trafficseotop](#trafficseotopnet)
  
---

## Xlink
> Thật ra thì đây là cái bypass mình không thích nhất, chủ yếu vì nó không ổn định. Sau khi **Xlink** thêm vào một đống loại nhiệm vụ kiểu "vào trang, tìm đoạn text được chỉ định rồi nhập lại" trong khi tool chỉ xử lý được loại nhiệm vụ ấn nút thông thường, mình quyết định loại hẳn cái này ra. Thôi thì mình giải thích qua cách nó hoạt động để các bạn tham khảo nhé.

- **Xlink** cấp cho mình một cái ID của trang nhiệm vụ ngay từ request đầu tiên, ID này được nhúng sẵn trong script của trang HTML. Vì vậy mình dùng package regex ([re](https://docs.python.org/3/library/re.html)) của Python để kéo nó ra.

1. Gửi request `GET` đến trang nhiệm vụ để lấy HTML:

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

2. Dùng regex để lấy ID nhiệm vụ từ HTML:

```py
match = re.search(r'https://xlink\.co/site-[uv]-[v35]\.js\?id=([a-zA-Z0-9]+)', html)
if match:
    task_id = match.group(1)
    print(f' Mã nhiệm vụ: {task_id}')
    return task_id
```

- Có ID rồi thì mình gửi tiếp các request để lấy mã và kích hoạt đồng hồ đếm ngược.

1. Gửi request `POST` kèm thông tin để bắt đầu đếm ngược:

```py
    headers = {
        'accept': '*/*',
        'accept-language': 'en-GB,en;q=0.7',
        'content-type': 'application/x-www-form-urlencoded',
        'origin': url, # URL trang nhiệm vụ — nhiều bên rút gọn link kiểm tra xem request có xuất phát đúng từ trang của họ không
        'priority': 'u=1, i',
        'referer': url, # tương tự, vẫn là URL trang nhiệm vụ
        'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/14.0.3 Safari/605.1.15',
    }
    data = {
        'code': code,
        'token': '',
    }

    try:
        response = requests.post('https://xlink.co/step', headers=headers, data=data)
```

2. Trong JSON trả về sẽ có biến `token`, mình lấy nó ra để dùng cho request tiếp theo:

```py
    json = response.json()
    token = json.get('token')
```

3. Gửi tiếp request `POST` đến endpoint countdown để lấy thời gian chờ:

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
        'token': token, # token lấy ở bước trên
    }

    try:
        response = requests.post('https://xlink.co/countdown', headers=headers, data=data)
```

4. JSON của request này trả về biến `timer` — đây là số giây mình phải chờ.

---

## Linktot
> Linktot có 3 loại nhiệm vụ chính: `normal`, `backlink`, và `changecolor`. Mỗi loại xử lý khác nhau, từ endpoint cho đến cách mã hóa.

### Loại Normal & Backlink

Hai loại này hoạt động gần như giống nhau, chỉ khác endpoint gọi đến:

1. **Khởi tạo request**: Tạo một ID ngẫu nhiên để tracking session

```py
request_id = str(random.randint(100000, 999999))

headers = {
    'origin': task_url,
    'referer': task_url,
    'rid': request_id,
    'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 15_5) AppleWebKit/605.1.15',
}
```

2. **Ping server**: Gửi `OPTIONS` request để báo hiệu bắt đầu nhiệm vụ
   - **Normal**: `https://linktot.net/ping.php`
   - **Backlink**: `https://linktot.net/ping_backlink.php`

```py
response = requests.options(endpoint_url, headers=headers)
```

3. **Chờ xử lý**: Server cần 80 giây để xác nhận request hợp lệ

```py
time.sleep(80)
```

4. **Lấy mã đã mã hóa**: Gửi `POST` request kèm thông tin task

```py
headers = {
    'accept': '*/*',
    'content-type': 'application/json',
    'origin': task_url,
    'referer': task_url,
    'rid': request_id,
}

payload = {
    'href': task_url,
    'hostname': task_url,
}

response = requests.post('https://linktot.net/get-code.php', headers=headers, json=payload)
```

5. **Giải mã XOR**: Mã trả về được mã hóa bằng XOR với key cố định

```py
ENCRYPTION_KEY = "2ThDrStTr"

# Giải base64 lớp đầu tiên
decoded_bytes = base64.b64decode(encoded_string).decode('utf-8')

# Giải mã XOR với key
decrypted = ""
for index, char in enumerate(decoded_bytes):
    key_char = ENCRYPTION_KEY[index % len(ENCRYPTION_KEY)]
    decrypted += chr(ord(char) ^ ord(key_char))
```

### Loại Changecolor

Loại này dùng mã hóa AES-GCM, phức tạp hơn XOR một chút: (loại này mình k chắc chắn đây là cách lấy mã đúng lắm vì mình ch test bao h, mình nhìn script r nghĩ v th)

1. **Tạo payload**: Lấy timestamp hiện tại cộng thêm 300 giây

```py
timestamp = int(time.time()) + 300
payload = str(timestamp)
domain = get_domain(task_url)
```

2. **Mã hóa AES-GCM**: Gồm 3 thành phần chính:
   - **Key (32 bytes)**: Khóa mã hóa AES-256
   - **IV (12 bytes)**: Initialization Vector cho GCM mode
   - **Tag (16 bytes)**: Authentication tag để xác minh tính toàn vẹn

```py
def encrypt_aes_gcm(domain, payload):
    from cryptography.hazmat.primitives.ciphers.aead import AESGCM
    encryption_key = os.urandom(32)
    initialization_vector = os.urandom(12)
    cipher = AESGCM(encryption_key)
    encrypted_data = cipher.encrypt(initialization_vector, payload.encode(), None)
    ciphertext = encrypted_data[:-16]
    auth_tag = encrypted_data[-16:]
    encode_b64 = lambda x: base64.b64encode(x).decode().rstrip("=")
    key_encoded = encode_b64(encryption_key)
    ciphertext_encoded = encode_b64(ciphertext)
    iv_encoded = encode_b64(initialization_vector)
    tag_encoded = encode_b64(auth_tag)
    domain_encoded = encode_b64(domain.encode())
    combined = f"{key_encoded}:{domain_encoded}:{ciphertext_encoded}:{iv_encoded}:{tag_encoded}"
    return base64.b64encode(combined.encode()).decode().rstrip("=")
```

3. **Chờ 30 giây**: Server cần thời gian để xử lý (mình nghĩ v)

```py
time.sleep(30)
```

Mã tạo ra ở đây chính là mã cần nhập vào trang nhiệm vụ.

---

## Traffichay
> Cơ chế xác thực khá đơn giản, dựa trên query parameters được server cấp.

1. **Lấy authentication parameters**: Gọi endpoint đặc biệt để server trả về các tham số xác thực

```py
headers = {
    'accept': '*/*',
    'origin': task_url,
    'referer': task_url,
}

response = requests.get(
    'https://traffichay.com/que?q=status,azauth,q,t,z&filter=connection',
    headers=headers
)
```

Response trả về JSON chứa:
- `azauth`: Token xác thực do server tạo ra
- `q`: idk
- `t`: Timestamp token
- `z`: Tham số xác minh phụ (idk)

2. **Build request với auth params**: Kết hợp các tham số xác thực với session ID ngẫu nhiên

```py
auth_data = response.json()
session_id = str(random.randint(100000, 999999))

params = {
    'azauth': auth_data.get('azauth'),
    'q': auth_data.get('q'),
    't': auth_data.get('t'),
    'opa': session_id,
    'z': session_id,
}
```

3. **Request mã từ publisher endpoint**: Gửi `GET` request với đầy đủ auth params

```py
response = requests.get(
    'https://traffichay.com/publisher', 
    params=params, 
    headers=headers
)
```

4. **Parse mã từ response**: Mã có thể nằm ở nhiều key khác nhau tùy lúc

```py
json_data = response.json()
code = json_data.get('password') or json_data.get('pass') or json_data.get('code')
```

---

## TrafficUser
> Về cơ bản là clone hoàn toàn của Traffichay, chỉ khác domain.

Workflow giống hệt Traffichay, chỉ đổi domain:
- `https://traffichay.com` → `https://my.trafficuser.net`

---

## Funlink
> Cái này thật sự khó, nó update liên tục và đến phiên bản mới nhất thì bọn mình bỏ cuộc không bypass nổi nữa.

### Phase 1: Lấy Authentication Token

1. **Request trang HTML**: Lấy trang với cookie phù hợp

```py
cookies = {'i18n_redirected': 'vi'}
response = requests.get(funlink_url, cookies=cookies)
html_content = response.text
```

2. **Parse dữ liệu Nuxt.js**: Lấy JSON từ script tag `__NUXT_DATA__`

```py
pattern = r'id="__NUXT_DATA__"[^>]*>(.*?)</script>'
match = re.search(pattern, html_content, re.DOTALL)
nuxt_data_json = match.group(1)
```

3. **Tìm authentication token**: Duyệt qua JSON array để lấy token

```py
parsed_data = json.loads(nuxt_data_json)

auth_token = None
for item in parsed_data:
    # Token là string dài hơn 25 ký tự và chỉ chứa chữ + số
    if isinstance(item, str) and len(item) > 25 and item.isalnum():
        auth_token = item
        break
```

### Phase 2: Lấy thông tin Keyword

4. **Lấy alias từ URL**: Tách phần alias ra khỏi link Funlink

```py
url_pattern = r"funlink\.io/([A-Za-z0-9]+)"
match = re.search(url_pattern, funlink_url)
url_alias = match.group(1)
```

5. **Gọi API lấy keyword**: Lấy thông tin keyword và URL trang nv, do h Funlink chỉ cho ảnh nên mình khuyên các b có 2 cách lấy url nv:

- **OCR** (tỉ lệ cực kì thấp do Funlink bôi màu vào url trong các ảnh)
- **Lập bảng url ảnh và url trang web**: Sẽ lấy url ảnh nhiệm vụ so sánh với bảng đã tổng hợp xem có khớp với url nv nào k (cách này tốn tgian cực kì và độ hiệu quả k cao nhưng là cách tốt nhất)

```py
api_url = f"https://public.funlink.io/api/url/get?id={url_alias}"
response = requests.get(api_url)

keyword_data = response.json()["data_keyword"]
destination_url = keyword_data.get("url_destination")
```

### Phase 3: Tạo Custom Headers

6. **Tạo client fingerprint**: Encode thông tin thiết bị sang base64

```py
def generate_client_data(href: str, hostname: str) -> str:
    client_info = {
        "screen": "5000 x 5000",
        "browser_name": "Chromium",
        "browser_version": "141",
        "os_name": "Windows",
        "os_version": "10.0",
        "href": href,
        "user_agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36",
        "hostname": hostname
    }
    
    json_string = json.dumps(client_info, ensure_ascii=False)
    encoded = base64.b64encode(json_string.encode()).decode()
    return encoded
```

7. **Tạo request ID**: UUID để tracking

```py
request_id = str(uuid.uuid4())
```

8. **Build headers**: Ghép tất cả lại

```py
headers = {
    "User-Agent": "Mozilla/5.0 ...",
    "Accept": "*/*",
    "Origin": destination_url,
    "Referer": destination_url,
    "Content-Key": generate_client_data(destination_url, destination_url),
    "Rid": request_id
}
```

### Phase 4: Lấy mã xác nhận

9. **`OPTIONS` request**:

```py
client.options("https://public.funlink.io/api/code/ch", headers=headers)
```

10. **Chờ bắt buộc**: Phải đợi đúng 50 giây (sau nhiều lần test thì thấy đây là tgian chờ ngắn nhất mà Funlink vẫn cho qua)

```py
time.sleep(50)
```

11. **`POST` request để lấy mã**: Gửi thông tin thiết bị

```py
device_payload = {
    'screen': '5460 × 1080',
    'browser_name': 'Not_A Brand',
    'browser_version': '99',
    'os_name': 'Windows',
    'os_version': '10.0',
    'href': destination_url,
    'user_agent': headers["User-Agent"],
    'hostname': destination_url,
    'code': '4',
}

response = client.post(
    "https://public.funlink.io/api/code/code", 
    headers=headers, 
    json=device_payload
)

verification_code = response.json()["code"]
```

### Phase 5: Lấy URL đích (Tùy chọn)

12. **Tạo NCL hash**: Fingerprint info thiết bị

```py
def generate_ncl_hash():
    is_mobile = platform.system() in ['Android', 'iOS']
    
    capabilities = [
        "1" if random.random() < 0.2 else "0",  # ctrl_pressed
        "1" if is_mobile and random.random() < 0.3 else "0",  # virtual_keyboard
        "1" if random.random() < 0.15 else "0",  # long_press
        "1" if random.random() < 0.1 else "0",  # context_menu
        "1" if random.random() < 0.7 else "0",  # mouse_wheel
        "1" if random.random() < 0.95 else "0",  # has_speakers
        "1" if random.random() < 0.85 else "0",  # has_microphone
        "1" if random.random() < 0.6 else "0",  # has_webcam
        "1" if random.random() < 0.5 else "0",  # battery_status
    ]
    
    return "".join(capabilities)
```

13. **Gửi dữ liệu tracking**: Submit mã xác nhận kèm metadata

```py
tracking_payload = {
    "browser_name": "Chrome",
    "browser_version": "137.0.0.0",
    "os_name": "Linux",
    "os_version": "-",
    "keyword_answer": verification_code,
    "link_shorten_id": url_alias,
    "keyword": keyword_data["keyword_text"],
    "ip": "",
    "user_agent": headers["User-Agent"],
    "device_name": "desktop",
    "token": auth_token,
    "keyword_id": keyword_data["id"]
}

tracking_headers = {
    **headers,
    "Ncl": generate_ncl_hash(),
}

response = client.post(
    "https://public.funlink.io/api/url/tracking-url",
    headers=tracking_headers,
    json=tracking_payload
)

final_destination = response.json()["data_link"]["url"]
```

---

## Funlink Xanh (dr.funlink.io)
> Biến thể của Funlink với domain riêng và một vài điều chỉnh nhỏ trong headers.

**Khác biệt chính**:
- Domain: `dr.funlink.io` thay vì `funlink.io`
- API base: `https://public-dr.funlink.io/api/...`
- Header NCL cố định (idk): `'ncl': '100011111'` (không random như bản gốc)
- Không cần cookie `i18n_redirected`

Ngoài ra workflow giống hệt Funlink tím, chỉ thay endpoints và headers tương ứng là được.

---

## Toplinks
> Cũng tương tự Funlink nhưng có thêm cơ chế keyword renewal và dùng endpoints riêng.

### Điểm khác so với Funlink:

1. **Cơ chế keyword renewal**: Phải làm mới key trước khi lấy mã (thật ra đây chỉ đơn giản là mình k thích dùng lấy url nv như cái Funlink kia do k ổn định, đổi nhiệm vụ sẽ cho ra kết quả url nv ổn định hơn)

```py
# Lấy keyword data ban đầu
response = requests.get(f"https://pub.toplinks.io/cfm/get?id={alias}")
keyword_data = response.json()["data_keyword"]
ignore_id = keyword_data.get("id")

# Renew keyword để lấy URL đích mới nhất
renew_response = requests.get(
    f'https://pub.toplinks.io/dest/get-new-key?ignoreId={ignore_id}&id={alias}'
)
destination_url = renew_response.json()['data_keyword']['url_destination']
```

2. **Endpoints lấy mã khác**:

```py
# Ping endpoint (GET thay vì OPTIONS)
client.get("https://pub.toplinks.io/dest/p", headers=headers)

time.sleep(50)

# Endpoint lấy mã dùng GET thay vì POST, thêm header x-client-data
headers_with_client_data = {
    **headers,
    "x-client-data": generate_client_data(destination_url, destination_url)
}

response = client.get(
    "https://pub.toplinks.io/dest/o", 
    headers=headers_with_client_data
)

verification_code = response.json()["code"]
```

3. **Tracking endpoint khác**:

```py
client.post(
    "https://pub.toplinks.io/cfm/check",
    headers=headers,
    json=tracking_payload
)
```

---

## Old Yeulink
> **Yeulink** đã thay đổi cơ chế lấy mã nên phần này đã outdate hoàn toàn.

1. **Lấy task ID từ HTML**: Parse script tag để tìm ID nhiệm vụ

```py
response = requests.get(task_url, headers=headers)
html_content = response.text

# Regex tìm script URL chứa task ID
pattern = r'https://yeulink\.com/site-[\w-]+\.js\?id=([\w-]+)'
match = re.search(pattern, html_content)
task_id = match.group(1)
```

2. **Bước 1 - Khởi tạo**: Gửi task ID để mở session

```py
step1_payload = {
    'code': task_id,
    'token': ''
}

response = requests.post(
    'https://yeulink.com/step',
    headers=headers,
    data=step1_payload
)

session_token = response.json().get('token')
```

3. **Bước 2 - Countdown**: Lấy thời gian chờ từ server

```py
step2_payload = {
    'code': task_id,
    'token': session_token
}

response = requests.post(
    'https://yeulink.com/countdown',
    headers=headers,
    data=step2_payload
)

wait_time = response.json().get('timer')
time.sleep(int(wait_time))
```

4. **Bước 3 - Continue**: Lấy mã sau khi đếm ngược xong

```py
response = requests.post(
    'https://yeulink.com/continue',
    headers=headers,
    data=step2_payload  # Dùng lại payload từ bước 2
)

verification_code = response.json().get('code')
```

---

## Linkngon.com
## Trafficseotop.net
## Linkngon.net
> Ba cái này về cơ bản là như nhau, authentication flow y hệt Traffichay.

**Workflow chung cho cả 3**:

1. **Lấy authentication parameters**: `GET` qua endpoint

```py
# Mapping domain
domains = {
    'linkngon_com': 'https://linkngon.com',
    'linkngon_net': 'https://linkngon.net',
    'trafficseotop': 'https://trafficseotop.net',
}

headers = {
    'accept': '*/*',
    'origin': task_url,
    'referer': task_url,
}

response = requests.get(
    f'{domain}/que?q=status,azauth,q,t,z&filter=connection',
    headers=headers
)

auth_params = response.json()
```

2. **Build publisher request**: Ghép auth params với session ID ngẫu nhiên

```py
session_id = str(random.randint(100000, 999999))

publisher_params = {
    'azauth': auth_params.get('azauth'),
    'q': auth_params.get('q'),
    't': auth_params.get('t'),
    'opa': session_id,
    'z': session_id,
}
```

3. **Lấy mã xác nhận**: Request publisher endpoint

```py
response = requests.get(
    f'{domain}/publisher',
    params=publisher_params,
    headers=headers
)

json_data = response.json()
verification_code = (
    json_data.get('password') or 
    json_data.get('pass') or 
    json_data.get('code')
)
```

---

## Repository Activity
  
<p align="center">
  <img src="https://repobeats.axiom.co/api/embed/3e765992107484864746304850bf1f072eb2f54f.svg" alt="Mèo Bypass Activity"/>
</p>
  
---
