# 📚 HƯỚNG DẪN SỬ DỤNG BACKEND PYTHON

## 🎯 Tổng Quan

Dự án này gồm:
- **Frontend**: HTML/JavaScript (Giao diện chatbot)
- **Backend**: Python Flask (Xử lý logic, gọi Gemini API)

Backend xử lý:
✅ Nhận tin nhắn từ frontend
✅ Gọi Gemini API  
✅ Tự động chuyển sang API khác khi gặp lỗi token
✅ Trả kết quả về frontend

---

## 🛠️ BƯỚC 1: CÀI ĐẶT PYTHON

### Windows:
1. Tải Python tại: https://www.python.org/downloads/
2. Chọn **Python 3.11** hoặc mới hơn
3. ⚠️ **RẤT QUAN TRỌNG**: Tick vào "Add Python to PATH"
4. Nhấn "Install Now"
5. Chờ cài đặt hoàn thành

### Kiểm tra cài đặt:
Mở PowerShell hoặc Command Prompt, gõ:
```
python --version
```
Bạn sẽ thấy: `Python 3.x.x`

---

## 📁 BƯỚC 2: CẤU TRÚC THƯ MỤC

```
FINAL_TEC004/
├── backend/
│   ├── app.py                    ← File Python chính
│   ├── requirements.txt          ← Danh sách thư viện
│   └── .env (tùy chọn)
│
└── frontend/
    └── index.html                ← Giao diện chatbot
```

---

## ⚙️ BƯỚC 3: CÀI ĐẶT THƯ VIỆN

Mở PowerShell, di chuyển đến thư mục `backend`:

```powershell
cd C:\Users\USER\Desktop\FINAL_TEC004\backend
```

Cài đặt các thư viện từ requirements.txt:

```powershell
pip install -r requirements.txt
```

⏳ Chờ cài đặt (2-3 phút)

### Thư viện sẽ được cài:
- **Flask**: Web framework để tạo server
- **flask-cors**: Cho phép gọi từ các domain khác
- **requests**: Để gọi HTTP requests

---

## 🔑 BƯỚC 4: THÊM API KEYS

Mở file `backend\app.py` bằng text editor (Notepad, VS Code, ...)

Tìm phần này (khoảng dòng 30):

```python
API_KEYS = [
    'AQ.Ab8RN6Ie4PwG3_ot3CNABv2OBf8GD9-CBFomC5ohAwd8JHJbYQ',  # API Key 1
    'ĐIỀN_API_KEY_DỰ_PHÒNG_2_VÀO_ĐÂY',                        # API Key 2
    'ĐIỀN_API_KEY_DỰ_PHÒNG_3_VÀO_ĐÂY'                         # API Key 3
]
```

### Lấy API Key:
1. Vào: https://aistudio.google.com/app/apikey
2. Nhấn "Create API key"
3. Copy API key
4. Dán vào file `app.py` (thay thế chữ "ĐIỀN...")

💡 **Cần tối thiểu 1 API key, tối đa 3 key dự phòng**

---

## 🚀 BƯỚC 5: CHẠY BACKEND

Trong PowerShell (ở thư mục backend):

```powershell
python app.py
```

Bạn sẽ thấy:
```
============================================================
🚀 CHATBOT BACKEND - PYTHON FLASK
============================================================
⏱️  API Key được sử dụng: 1/3
🌐 URL: http://localhost:5000
📡 Endpoint chính: http://localhost:5000/api/chat
============================================================
 * Running on http://0.0.0.0:5000
 * Press CTRL+C to quit
```

✅ **Backend đang chạy!**

---

## 🌐 BƯỚC 6: MỞ FRONTEND

Bây giờ backend đang chạy, hãy mở frontend:

1. Mở file: `frontend\index.html` bằng trình duyệt
2. Hoặc double-click file `index.html`

Bạn sẽ thấy:
- ✅ Giao diện chatbot
- ✅ Indicator API (API #1)
- ✅ Có thể nhắn tin bình thường

---

## 💬 KIỂM TRA HOẠT ĐỘNG

### Test 1: Gửi tin nhắn bình thường
Frontend:
```
Bạn: Swinburne có khoa ngành nào?
```

Kiểm tra PowerShell (backend):
```
📨 Nhận tin nhắn: Swinburne có khoa ngành nào?
📡 Gọi API: https://generativelanguage.googleapis.com/v1beta...
✅ Nhận được câu trả lời từ Gemini
```

### Test 2: Kiểm tra failover
Nếu API #1 gặp vấn đề, bạn sẽ thấy:
```
⚠️  Lỗi token ở API #1
🔄 Chuyển sang API #2
```

---

## 🔧 HIỂU VỀ CODE PYTHON

### Các hàm quan trọng:

#### 1. `call_gemini_api(user_message)`
- **Tác dụng**: Gọi Gemini API để xử lý tin nhắn
- **Input**: Tin nhắn từ user
- **Output**: Câu trả lời

```python
def call_gemini_api(user_message):
    # 1. Lấy URL API hiện tại
    # 2. Chuẩn bị request body
    # 3. Gửi POST request
    # 4. Phân tích response
    # 5. Trả về kết quả
```

#### 2. `is_token_error(status_code, error_data)`
- **Tác dụng**: Kiểm tra lỗi có liên quan đến token không
- **Input**: HTTP status code + dữ liệu lỗi
- **Output**: True/False

```python
def is_token_error(status_code, error_data):
    # Kiểm tra các status code: 401, 403, 429
    # Kiểm tra keywords: QUOTA, RATE, TOKEN
    # Trả về True/False
```

#### 3. `switch_to_next_api()`
- **Tác dụng**: Chuyển sang API tiếp theo
- **Output**: True (có API khác) / False (hết API)

```python
def switch_to_next_api():
    # Tăng current_api_index
    # Nếu còn API khác, return True
    # Nếu hết, return False
```

#### 4. `process_message_with_failover(user_message)`
- **Tác dụng**: Xử lý tin nhắn với khả năng failover
- **Input**: Tin nhắn từ user
- **Output**: Kết quả (thành công hoặc lỗi)

```python
def process_message_with_failover(user_message):
    # Lặp: Thử gọi API
    # Nếu thành công: return kết quả
    # Nếu lỗi token: chuyển API tiếp theo
    # Nếu lỗi khác: return lỗi ngay
```

---

## 📡 API ENDPOINTS

Backend cung cấp 3 endpoint:

### 1. POST `/api/chat` - Gửi tin nhắn
**Request:**
```json
{
    "message": "Bạn có khoa ngành nào?"
}
```

**Response (thành công):**
```json
{
    "success": true,
    "reply": "Swinburne có các khoa ngành như...",
    "current_api": 1
}
```

**Response (thất bại):**
```json
{
    "success": false,
    "error": "Lỗi API: 429",
    "current_api": 2
}
```

### 2. GET `/api/status` - Kiểm tra trạng thái
**URL:** `http://localhost:5000/api/status`

**Response:**
```json
{
    "status": "running",
    "current_api_index": 1,
    "total_apis": 3,
    "timestamp": "2024-06-18T09:00:00"
}
```

### 3. POST `/api/reset` - Reset API
**URL:** `http://localhost:5000/api/reset`

**Response:**
```json
{
    "success": true,
    "message": "Đã reset về API đầu tiên",
    "current_api_index": 1
}
```

---

## 🐛 DEBUG & TROUBLESHOOTING

### Lỗi: "ModuleNotFoundError: No module named 'flask'"
**Giải pháp:**
```powershell
pip install flask flask-cors requests
```

### Lỗi: "Port 5000 đã được sử dụng"
**Giải pháp:** Thay port trong `app.py` (dòng cuối):
```python
app.run(host='0.0.0.0', port=5001, debug=True)  # Thay 5000 thành 5001
```

### Frontend không kết nối được backend
1. Kiểm tra backend đã chạy chưa (xem PowerShell)
2. Kiểm tra URL trong `index.html` (dòng 155):
```javascript
const BACKEND_URL = 'http://localhost:5000';
```

### API không trả lời
1. Kiểm tra API Key đã điền đúng chưa
2. Kiểm tra console của browser (F12 > Console)
3. Kiểm tra PowerShell xem có lỗi gì

---

## 📋 KIẾN THỨC PYTHON CẦN BIẾT

### 1. Functions (Hàm)
```python
def hello_world():
    print("Hello")

hello_world()  # Gọi hàm
```

### 2. Dictionaries (Từ điển)
```python
data = {
    'name': 'Swinburne',
    'year': 2024
}

print(data['name'])  # Output: Swinburne
```

### 3. Lists (Danh sách)
```python
api_keys = ['key1', 'key2', 'key3']
print(api_keys[0])  # Output: key1

for key in api_keys:
    print(key)  # In từng key
```

### 4. Try-Except (Xử lý lỗi)
```python
try:
    result = 10 / 0  # Lỗi!
except:
    print("Có lỗi xảy ra")
```

### 5. Global Variables (Biến toàn cục)
```python
count = 0

def increase():
    global count
    count += 1

increase()
print(count)  # Output: 1
```

---

## 🎓 CÁC COMMENT TRONG CODE

Code Python có rất nhiều comment giúp bạn hiểu:

```python
# Đây là comment 1 dòng

"""
Đây là comment
nhiều dòng
"""

def get_current_api_key():
    """Lấy API key đang sử dụng"""  # Docstring
    return API_KEYS[current_api_index]
```

---

## ⏹️ DỪNG SERVER

Ở PowerShell, nhấn: **CTRL + C**

Bạn sẽ thấy:
```
^CKeyboardInterrupt
...
```

---

## ✅ CHECKLIST HOÀN THÀNH

- [ ] Cài Python
- [ ] Cài thư viện (pip install -r requirements.txt)
- [ ] Thêm API Keys vào app.py
- [ ] Chạy backend (python app.py)
- [ ] Mở frontend (index.html)
- [ ] Test gửi tin nhắn
- [ ] Kiểm tra console (F12)
- [ ] Kiểm tra PowerShell xem backend chạy ok

---

## 📞 CẦN GIÚP?

Kiểm tra:
1. PowerShell có lỗi gì không?
2. Browser console (F12) có lỗi không?
3. URL backend đúng không?
4. API Key có hợp lệ không?

Xem file `app.py` dòng 1-50 để hiểu cấu trúc cơ bản.

**Chúc bạn thành công! 🚀**
