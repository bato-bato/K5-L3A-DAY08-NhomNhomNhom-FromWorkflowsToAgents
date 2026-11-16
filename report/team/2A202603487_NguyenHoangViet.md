# Báo cáo cá nhân — 03487_NguyenHoangViet

## 1. Thông tin thành viên

- Họ tên: Nguyễn Hoàng Việt
- MSSV: 2A202603487
- Email: hoangviet1824qsqn@gmail.com
- GitHub: HViettt

## 2. Phần việc phụ trách

- **Kiến trúc đảm nhiệm:** Shape B — Single Agent (`src/lab8/shapes/single.py`).
- **Nội dung kỹ thuật:**
  - Thiết kế và triển khai vòng lặp ReAct có chặn trên (`run_react` với `max_steps=8`).
  - Kiểm soát nghiêm ngặt ranh giới quyền hạn: Worker chạy với `worker=True`, chỉ được gọi các công cụ đọc trong allowlist (`lookup_order`, `lookup_refund_history`, `compute_compensation`). Chặn tuyệt đối model gọi công cụ ghi (`issue_refund`).
  - Kháng Prompt Injection từ ticket: Model không nghe theo số ngày trễ giả lập hay chỉ thị bỏ qua policy từ khách hàng, mà chỉ căn cứ trên dữ liệu đơn hàng (`orders`) thật từ SQLite.
  - Phối hợp với Host Durability: Tính toán và truyền quote đầy đủ vào `durability.issue_refund` để host tự xử lý trần bồi thường theo đơn (Order Cap) và trừ phần đã hoàn trước đó (chuẩn T13, T14).
  - Tương thích xử lý sự cố (Crash / Idempotency): Truyền `crash_at` trực tiếp vào host và để `InjectedCrash` lan truyền mà không nuốt lỗi, đảm bảo khôi phục an toàn (chuẩn T11, T12).
  - Ghi nhật ký chuẩn (Trace Contract v1): Đảm bảo sinh trace file JSON hợp lệ cho mọi nhánh trả về, không để rò rỉ canary secret hay PII.
  - Xây dựng bộ kiểm thử riêng `src/tests/test_single_agent_tv2.py`.

## 3. Đóng góp cụ thể

- **Commit chính:**
  - Hash: `76f2ec1` (`feat(single): implement bounded ReAct single agent and safety tests`)
- **Code:**
  - File `src/lab8/shapes/single.py`: Thay thế hoàn toàn stub `not_implemented` bằng Single Agent ReAct hoàn chỉnh.
- **Bộ test kỹ thuật đã thêm:**
  - File `src/tests/test_single_agent_tv2.py`:
    1. `test_single_agent_worker_cannot_call_write_tools`: Xác thực hệ thống chặn đứng mọi hành vi gọi write tools của model.
    2. `test_single_agent_prompt_injection_resistant`: Kiểm tra khả năng miễn nhiễm trước prompt injection gian lận ngày trễ trong ticket (claim C-002).
    3. `test_single_agent_trace_is_clean_and_valid`: Kiểm tra sự tuân thủ Trace Contract v1 và đảm bảo không lộ PII/Secret.
    4. `test_single_agent_order_already_refunded_cap`: Kiểm tra quy tắc trần đơn hàng đã hoàn đủ từ trước (claim C-013).

## 4. Bằng chứng

- **Commit Git:** `76f2ec1` trên branch `main`.
- **Kết quả kiểm thử Single Agent:**
  - Đạt 62/62 test student cases cho Shape B trong ma trận `test_public_matrix.py` (bao gồm 60 ca đơn lẻ và 2 ca retry T11, crash T12).
  - Đạt 4/4 test kỹ thuật riêng trong `src/tests/test_single_agent_tv2.py`.
  - Cổng Host `python -m pytest -q` đạt 58/58 passed (100% xanh).
  - Smoke test `python -m lab8.smoke` đạt `lab8.smoke pass`.

## 5. Công cụ AI đã dùng

- **Công cụ:** Antigravity AI Assistant.
- **Mục đích sử dụng:**
  - Hỗ trợ phân tích luồng ReAct theo schema quy định của bài lab.
  - Sinh khung code ban đầu cho các test case kiểm tra biên an toàn (safety boundaries, prompt injection rejection).
- **Quy trình tự kiểm tra:**
  - Tự rà soát từng dòng code đảm bảo tuân thủ strict allowlist và không bypass cổng duyệt tiền.
  - Chạy toàn bộ pytest suite trên môi trường cục bộ để xác minh 100% test pass.

## 6. Tự đánh giá

- Hoàn thành đầy đủ và vượt tiến độ các yêu cầu của Checkpoint CP1, CP2, CP3, CP4 dành cho vai trò Single Agent (Shape B).
- Đảm bảo trọn vẹn 3 điều kiện must-pass: Không bypass approval, không lộ secret/PII, bảo đảm an toàn khi crash (idempotency).
- Đóng góp đầy đủ mã nguồn, bộ test độc lập và tài liệu cá nhân theo đúng rubric C8.
