# 💾 HƯỚNG DẪN LƯU LỊch SỬ CHAT

## 🎯 Tổng Quan

Chatbot giờ đây có **2 cách** để lưu lịch sử tin nhắn:

### ✅ Cách 1: **LocalStorage (Browser)**
- ✅ Không cần backend chạy
- ✅ Lưu ngay trên browser của user
- ✅ Không cần đăng nhập
- ❌ Dữ liệu chỉ lưu trên máy tính đó (nếu xóa cache thì mất)
- ❌ Không thể truy cập từ thiết bị khác

### ✅ Cách 2: **Database (Backend Python - Optional)**
- ✅ Dữ liệu lưu trên server
- ✅ Có thể truy cập từ nhiều thiết bị (nếu cần)
- ✅ An toàn hơn
- ❌ Cần backend chạy
- ❌ Sử dụng thêm SQLite

---

## 📱 CÁCH 1: LocalStorage - ĐƠN GIẢN (ĐÃ CÓ SẴN)

### ✨ Tính Năng:

#### 1. **Giao diện bên trái** - Danh sách cuộc trò chuyện cũ
- Hiển thị tất cả cuộc trò chuyện được lưu
- Click để xem lại
- Tự động đổi tiêu đề từ tin nhắn đầu tiên

#### 2. **Nút "➕ Mới"** - Tạo cuộc trò chuyện mới
- Xóa tin nhắn cũ
- Bắt đầu cuộc trò chuyện mới

#### 3. **Nút "🗑️ Xóa"** - Xóa cuộc trò chuyện hiện tại
- Hỏi xác nhận trước khi xóa
- Xóa khỏi danh sách lịch sử

### 🚀 Cách Dùng:

1. **Lần đầu vào web:**
   - Bạn sẽ thấy sidebar bên trái rỗng
   - Nút "🗑️ Xóa" bị disable (không thể click)

2. **Nhắn tin bình thường:**
   - Hệ thống tự động tạo cuộc trò chuyện mới
   - Dữ liệu được lưu vào browser

3. **Đóng/mở lại tab:**
   - Bạn sẽ thấy lại tất cả cuộc trò chuyện cũ
   - Click để mở lại

4. **Xóa:**
   - Click "🗑️ Xóa"
   - Chọn "OK" để xác nhận

### 💾 Dữ Liệu Được Lưu:

Trong browser (localStorage):
```
{
  "chat_1623948000000_abc123": {
    "id": "chat_1623948000000_abc123",
    "title": "Bạn có khoa ngành nào?...",
    "timestamp": "18/6/2024 09:00:00",
    "messages": [
      {
        "sender": "user",
        "text": "Bạn có khoa ngành nào?",
        "timestamp": "2024-06-18T09:00:00"
      },
      {
        "sender": "bot",
        "text": "Swinburne có các khoa ngành như...",
        "timestamp": "2024-06-18T09:00:05"
      }
    ]
  }
}
```

### 🔍 Kiểm Tra LocalStorage:

1. Mở browser → Nhấn **F12** (hoặc Chuột phải → Inspect)
2. Đi tới tab **Application** (hoặc **Storage**)
3. Click **LocalStorage** → Click domain của bạn
4. Tìm key: `swinburne_chats`
5. Bạn sẽ thấy toàn bộ dữ liệu được lưu

---

## 🔧 CÁCH 2: Database Backend (OPTIONAL - Nâng Cao)

### ⚙️ Khởi Động:

1. **Cài đặt (nếu chưa):**
   ```powershell
   cd backend
   pip install -r requirements.txt
   ```

2. **Chạy backend:**
   ```powershell
   python app.py
   ```

3. **Mở frontend:**
   ```
   frontend/index.html
   ```

### 📊 Database Structure:

**File:** `chatbot_data.db` (tạo tự động)

**Bảng 1: chat_sessions**
```
┌────────────┬───────────┬─────────────────┐
│ id (PK)    │ title     │ created_at      │
├────────────┼───────────┼─────────────────┤
│ abc123     │ Khoa...   │ 2024-06-18 ...  │
│ def456     │ Học phí...│ 2024-06-18 ...  │
└────────────┴───────────┴─────────────────┘
```

**Bảng 2: messages**
```
┌────┬────────────┬────────┬─────────────────────────┐
│ id │ session_id │ sender │ content                 │
├────┼────────────┼────────┼─────────────────────────┤
│ 1  │ abc123     │ user   │ Bạn có khoa ngành nào? │
│ 2  │ abc123     │ bot    │ Swinburne có ...        │
└────┴────────────┴────────┴─────────────────────────┘
```

### 🔗 API Endpoints (Backend):

#### 1. **POST /api/save-chat** - Lưu tin nhắn
```javascript
// Request
fetch('http://localhost:5000/api/save-chat', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
        chat_id: 'abc123',
        message: 'Nội dung tin nhắn',
        sender: 'user'  // hoặc 'bot'
    })
});

// Response
{
    "success": true
}
```

#### 2. **GET /api/chats** - Lấy danh sách tất cả chat
```javascript
fetch('http://localhost:5000/api/chats')
    .then(r => r.json())
    .then(data => console.log(data));

// Response
{
    "success": true,
    "chats": [
        {
            "id": "abc123",
            "title": "Bạn có khoa ngành nào?...",
            "created_at": "2024-06-18 09:00:00",
            "updated_at": "2024-06-18 09:05:00"
        }
    ]
}
```

#### 3. **GET /api/chat/{id}** - Lấy chi tiết 1 chat
```javascript
fetch('http://localhost:5000/api/chat/abc123')
    .then(r => r.json())
    .then(data => console.log(data));

// Response
{
    "success": true,
    "chat": {
        "id": "abc123",
        "title": "Bạn có khoa ngành nào?...",
        "created_at": "2024-06-18 09:00:00",
        "messages": [
            {
                "sender": "user",
                "content": "Bạn có khoa ngành nào?",
                "timestamp": "2024-06-18 09:00:00"
            },
            {
                "sender": "bot",
                "content": "Swinburne có...",
                "timestamp": "2024-06-18 09:00:05"
            }
        ]
    }
}
```

#### 4. **POST /api/chat-new** - Tạo session mới
```javascript
fetch('http://localhost:5000/api/chat-new', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
        title: 'Cuộc trò chuyện mới'
    })
});

// Response
{
    "success": true,
    "chat_id": "abc123"
}
```

---

## 📋 CODE PYTHON QUAN TRỌNG

### 1. Lưu tin nhắn vào database:
```python
def save_message_to_db(chat_id, sender, content):
    """Lưu tin nhắn vào database"""
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    cursor.execute('''
        INSERT INTO messages (session_id, sender, content)
        VALUES (?, ?, ?)
    ''', (chat_id, sender, content))
    
    conn.commit()
    conn.close()
```

### 2. Lấy tất cả chat:
```python
def get_all_chat_sessions():
    """Lấy danh sách tất cả cuộc trò chuyện"""
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    cursor.execute('''
        SELECT id, title, created_at, updated_at 
        FROM chat_sessions 
        ORDER BY updated_at DESC
    ''')
    
    sessions = cursor.fetchall()
    conn.close()
    return sessions
```

### 3. Tạo session mới:
```python
def create_chat_session(title):
    """Tạo cuộc trò chuyện mới"""
    chat_id = str(uuid4())  # ID duy nhất
    conn = sqlite3.connect(DB_FILE)
    cursor = conn.cursor()
    
    cursor.execute('''
        INSERT INTO chat_sessions (id, title)
        VALUES (?, ?)
    ''', (chat_id, title))
    
    conn.commit()
    conn.close()
    return chat_id
```

---

## 🧠 KIẾN THỨC PYTHON CẦN BIẾT

### 1. SQLite (Database):
```python
import sqlite3

# Kết nối
conn = sqlite3.connect('data.db')
cursor = conn.cursor()

# Tạo bảng
cursor.execute('''
    CREATE TABLE users (
        id INTEGER PRIMARY KEY,
        name TEXT
    )
''')

# Thêm dữ liệu
cursor.execute('INSERT INTO users (name) VALUES (?)', ('Tuan',))

# Lấy dữ liệu
cursor.execute('SELECT * FROM users')
rows = cursor.fetchall()

# Lưu
conn.commit()
conn.close()
```

### 2. UUID (ID duy nhất):
```python
from uuid import uuid4

id1 = uuid4()  # Tạo ID ngẫu nhiên duy nhất
print(id1)     # Output: 550e8400-e29b-41d4-a716-446655440000
```

### 3. Foreign Key (Mối quan hệ):
```python
# Bảng messages có session_id refer tới chat_sessions
cursor.execute('''
    CREATE TABLE messages (
        id INTEGER PRIMARY KEY,
        session_id TEXT NOT NULL,
        content TEXT,
        FOREIGN KEY (session_id) REFERENCES chat_sessions(id)
    )
''')
```

---

## ⚡ FLOW HOÀN CHỈNH

```
Browser (Frontend)
    ↓
    ├─ 1. User nhắn: "Bạn có khoa ngành nào?"
    ├─ 2. Lưu vào LocalStorage (tự động)
    ├─ 3. Gửi /api/chat
    ↓
Backend (Python)
    ├─ 4. Gọi Gemini API
    ├─ 5. Trả lời
    ├─ 6. Lưu vào Database (nếu có)
    ↓
Browser (Frontend)
    ├─ 7. Hiển thị câu trả lời
    ├─ 8. Lưu vào LocalStorage (tự động)
    └─ 9. Cập nhật danh sách lịch sử
```

---

## ✅ CHECKLIST

- [ ] LocalStorage đang hoạt động (kiểm tra F12)
- [ ] Danh sách lịch sử hiển thị
- [ ] Click vào lịch sử được tải lại
- [ ] Nút "New" tạo cuộc trò chuyện mới
- [ ] Nút "Delete" xóa cuộc trò chuyện
- [ ] Nếu có backend: dữ liệu cũng được lưu vào DB

---

## 🔐 CẢnh Báo Bảo Mật

⚠️ **LocalStorage chỉ lưu trên browser của user**
- Nếu user xóa cache → dữ liệu mất
- Nếu user đổi browser → không có dữ liệu
- **Giải pháp:** Dùng backend database (Cách 2)

⚠️ **Database không có password**
- Ai có truy cập file `chatbot_data.db` thì có thể đọc được dữ liệu
- **Giải pháp:** Deploy lên server thật, bảo vệ bằng authentication

---

## 🆘 TROUBLESHOOTING

### LocalStorage không hoạt động
1. Kiểm tra F12 → Application → LocalStorage
2. Nếu không có key `swinburne_chats`, thử nhắn tin mới
3. Kiểm tra console có lỗi gì không

### Database không tạo được
1. Kiểm tra quyền folder `backend/`
2. Chạy `python app.py` xem có lỗi gì
3. File `chatbot_data.db` phải được tạo tự động

### Data bị mất sau khi restart
- **LocalStorage:** Dữ liệu vẫn lưu (trừ khi xóa cache)
- **Database:** Dữ liệu vẫn lưu trong file `chatbot_data.db`

**Chúc bạn thành công! 🚀**
