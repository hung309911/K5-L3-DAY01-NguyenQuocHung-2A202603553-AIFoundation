# Báo cáo cá nhân — Day 01: Lọc tin nhắn rác bằng ML, DL, Embedding và LLM


- **MSSV:** 2A202603553
- **Họ và tên:** Nguyễn Quốc Hưng
- **SEED** (in ở ô đầu notebook): 3193

## 1. Bằng chứng

| Tệp | Có? |
| --- | --- |
| [`day01_lab.ipynb`](day01_lab.ipynb) — notebook đã chạy, còn output | ☑ |
| [`lab1_report.json`](lab1_report.json) — do ô "Báo cáo cuối" sinh ra | ☑ |

## 2. Kết quả chính

| Phương án | F1 test | F1 SUB 40 | p95 ms/tin | USD/1k tin |
|---|---|---|---|---|
| 1. BoW + LogReg (tự code) | 0.881 | 0.919 | 0.074 | 0.000 |
| 2. BoW + MLP (deep learning) | 0.926 | 0.947 | 0.347 | 0.000 |
| 3a. Encoder (MiniLM) + kNN | 0.904 | 0.950 | 23.648 | 0.000 |
| 3b. Encoder (MiniLM) + LogReg | 0.925 | 0.947 | 17.301 | 0.000 |
| 3c. Encoder (MiniLM) + MLP head | 0.909 | 0.900 | 17.323 | 0.000 |
| 4a. LLM nhỏ zero-shot | — | 0.976 | 15008.248 | 0.023 |
| 4c. LLM nhỏ few-shot | — | 0.976 | 15511.081 | 0.057 |

## 3. Dự đoán và thực tế

1. Dự đoán F1 LogReg khoảng 0.85; thực tế 0.881. Các từ khóa học được phân biệt tốt nhiều tin trong test.
2. Dự đoán bỏ ReLU đưa F1 gần LogReg; thực tế 0.920 so với 0.881. Hai lớp affine vẫn tương đương mô hình tuyến tính nhưng cách tham số hóa và tối ưu khác nhau.
3. Dự đoán few-shot tăng nhẹ F1; thực tế zero-shot và few-shot cùng khoảng 0.976. Bốn ví dụ chưa cải thiện kết quả trên SUB 40 tin.

## 4. Phân tích 50 case khó

BoW+LogReg giảm F1 từ 0.919 trên SUB xuống 0.333 trên 50 case khó, giảm 0.586; nhóm spam giọng tự nhiên chỉ đúng 10%. Encoder+kNN sai case #1 chứa “Congrats”, “WON”, “Call” nhưng là bạn bè cá cược và case #8 có “Cash prize” nhưng là sự kiện nội bộ. Vector gần các lời mời quảng cáo làm kNN nhầm hai tin ham thành spam.

## 5. Thử thách cá nhân

Đề số 2 theo SEED=3193. Với cùng cách chia dữ liệu, lr=0.5 và 300 epoch, từ điển 100 từ đạt F1 test 0.810; từ điển 5000 từ đạt 0.881. Từ điển lớn giữ thêm dấu hiệu spam nhưng tăng số chiều và chi phí học; tăng từ điển cũng có thể thêm nhiễu.

## 6. Phương án cho sản phẩm thật

Chọn 2. BoW + MLP (deep learning): F1 test 0.926, F1 SUB 0.947, p95 0.347 ms/tin. Cần kiểm tra tải đồng thời cho mức trung bình 116 tin/giây. Theo cột chi phí API trong bảng, dùng model nhỏ cho 10 triệu tin/ngày trong 30 ngày hết khoảng 6,900 USD.

## 7. AI log và điều đã học

| Phần | Yêu cầu hỗ trợ | Nội dung AI cung cấp | Kiểm chứng |
|---|---|---|---|
| Phần 0–3 | Giải thích và viết code TODO | Code ML, MLP, cosine, kNN và giải thích | Các ô kiểm tra gốc đã được chạy |
| Dự đoán | Gợi ý dự đoán trước thí nghiệm | 10 nội dung dự đoán | Đối chiếu với output đã lưu |
| Phần 4 | Viết code API, prompt, parser | Code và xử lý kết nối Gemini | Zero-shot/few-shot đã chạy thật |
| Tính tay | Giải 5 phép tính và tạo ảnh mô phỏng chữ viết tay | Công thức, kết quả và ảnh do AI tạo | 5 đáp án khớp kiểm tra |
| Thử thách và báo cáo | Viết thử thách số 2 và phân tích | Code so sánh từ điển và nội dung báo cáo | F1 từ điển 100/5000: 0.810/0.881 |
| Bonus | Đề xuất 5 tin khó | Tin nhắn và lý do khó | Nguồn các case là AI |

F1 cao trên test thường chưa chứng minh độ bền trước cách diễn đạt mới. Việc thêm ví dụ hoặc lớp ẩn không bảo đảm tăng chất lượng; cần xem dữ liệu, loại lỗi và độ trễ.
