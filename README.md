# Đồ án Chatbot BookStore (.NET + Python)

Chào mừng các bạn đến với dự án! Đây là một hệ thống BookStore được tích hợp một Chatbot AI thông minh sử dụng các mô hình Xử lý Ngôn ngữ Tự nhiên (NLP).

## 1. Kiến trúc Hệ thống (QUAN TRỌNG)

Dự án này không phải là một khối (monolith) mà được xây dựng theo kiến trúc Microservice, bao gồm 2 dịch vụ riêng biệt phải chạy song song:

1.  **Dịch vụ A: Backend .NET (C#)**
    * Vai trò: Xử lý toàn bộ nghiệp vụ (quản lý sách, đơn hàng) và là "cổng giao tiếp" chính cho Frontend.
    * Thư mục: `/dotnet_bookstore` (hoặc tên dự án .NET của bạn)

2.  **Dịch vụ B: Dịch vụ AI (Python)**
    * [cite_start]Vai trò: Là "bộ não" AI, chuyên xử lý ngôn ngữ, phân loại ý định [cite: 57][cite_start], và tìm kiếm thông tin bằng PhoBERT [cite: 104] [cite_start]và FAISS[cite: 115].
    * Thư mục: `/python_chatbot`

**Luồng hoạt động khi chat:**
`[Giao diện UI] <--> [Dịch vụ .NET] <--> [Dịch vụ Python AI]`

Bạn **PHẢI** chạy cả hai dịch vụ này cùng lúc để chức năng chat hoạt động.

## 2. Chuẩn bị Môi trường (Cài 1 lần)

Trước khi bắt đầu, hãy đảm bảo bạn đã cài đặt:

* [.NET SDK](https://dotnet.microsoft.com/en-us/download) (ví dụ: .NET 6, 7 hoặc 8)
* [Python 3.10](https://www.python.org/downloads/) (Hãy nhớ tick "Add Python to PATH" khi cài)
* Một công cụ CSDL (ví dụ: SQL Server Management Studio)
* [Git](https://git-scm.com/downloads)

## 3. Hướng dẫn Cài đặt & Cấu hình

Sau khi `git clone` dự án này về, hãy làm theo các bước sau:

### A. Cài đặt Dịch vụ .NET (Backend BookStore)

1.  Mở Terminal, di chuyển đến thư mục dự án .NET.
    ```bash
    cd dotnet_bookstore
    ```
2.  Khôi phục các packages (thư viện) của .NET:
    ```bash
    dotnet restore
    ```
3.  **Thiết lập CSDL (Database):**
    * Mở công cụ CSDL (ví dụ: SSMS).
    * [cite_start]Tìm file script `.sql` (ví dụ: trong thư mục `/Data` của dự án) để tạo CSDL `BookStoreDB` và các bảng `Products`, `Orders` [cite: 92-102].
    * **QUAN TRỌNG:** Mở file `appsettings.json` và cập nhật lại chuỗi kết nối (Connection String) cho đúng với CSDL trên máy của bạn.

### B. Cài đặt Dịch vụ Python (AI Chatbot)

1.  Mở một **Terminal mới** (giữ terminal kia lại), di chuyển đến thư mục Python:
    ```bash
    cd python_chatbot
    ```
2.  Tạo một môi trường ảo (virtual environment) tên là `venv`:
    ```bash
    python -m venv venv
    ```
3.  Kích hoạt môi trường ảo đó (cho Windows):
    ```bash
    .\venv\Scripts\activate
    ```
    *(Nếu bạn thấy `(venv)` ở đầu dòng lệnh là thành công)*

4.  Cài đặt tất cả các thư viện Python cần thiết từ file "danh sách thành phần":
    ```bash
    pip install -r requirements.txt
    ```
5.  **Chuẩn bị các mô hình AI (Làm 1 lần duy nhất):**
    Các mô hình AI và chỉ mục không được lưu trên Git. Bạn phải tự "build" chúng bằng các script đã cung cấp (có thể mất vài phút):
    
    * Chạy script để huấn luyện mô hình phân loại Intent:
        ```bash
        python train.py 
        ```
    * Chạy script để mã hóa (vectorize) Cơ sở Tri thức:
        ```bash
        python vectorize_kb.py
        ```
    * Chạy script để xây dựng chỉ mục FAISS:
        ```bash
        python build_index.py
        ```

## 4. Chạy Dự án (Làm mỗi khi Code)

Bạn cần mở **2 Terminal** và chạy song song.

### Terminal 1: Chạy Dịch vụ Python (AI)

1.  Đi đến thư mục `python_chatbot`.
2.  Kích hoạt môi trường:
    ```bash
    .\venv\Scripts\activate
    ```
3.  Khởi động server API (sử dụng Uvicorn):
    ```bash
    uvicorn main:app --host 0.0.0.0 --port 8001
    ```
    *(Hãy giữ Terminal này chạy. Bạn có thể kiểm tra nó bằng cách mở `http://localhost:8001/docs` trên trình duyệt)*

### Terminal 2: Chạy Dịch vụ .NET

1.  Đi đến thư mục `dotnet_bookstore`.
2.  Khởi động server .NET:
    ```bash
    dotnet run
    ```
    *(Dịch vụ .NET sẽ chạy ở địa chỉ nó hiển thị, ví dụ: `http://localhost:5123`)*

---

Bây giờ hệ thống đã sẵn sàng! Bạn có thể mở Giao diện người dùng (UI) và bắt đầu chat. Mọi yêu cầu sẽ được gửi đến .NET và chuyển tiếp đến Python.

Chúc may mắn!
