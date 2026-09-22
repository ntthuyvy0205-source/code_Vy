# Sequence Diagram -- Duyệt phiếu chào hàng

``` mermaid
sequenceDiagram
    autonumber
    actor QL as Chủ cửa hàng/Quản lý
    participant HT as Hệ thống
    participant PCH as Phiếu chào hàng
    participant XM as Xe mới
    participant XC as Xe cũ
    participant NVBH as Nhân viên bán hàng

    QL->>HT: Chọn chức năng "Duyệt phiếu chào hàng"
    HT->>PCH: Lấy danh sách phiếu đang chờ duyệt
    PCH-->>HT: Danh sách phiếu
    HT-->>QL: Hiển thị danh sách phiếu đang chờ duyệt

    QL->>HT: Chọn một phiếu cần duyệt
    HT->>PCH: Lấy thông tin phiếu
    PCH-->>HT: Giá đề xuất, trạng thái, thông tin khách hàng
    HT->>XM: Lấy thông tin xe mới
    XM-->>HT: Thông tin xe, giá vốn, chi phí hóa đơn

    opt Giao dịch đổi xe
        HT->>XC: Lấy thông tin xe cũ
        XC-->>HT: Thông tin xe cũ, giá đổi, giá trị tham khảo
    end

    HT-->>QL: Hiển thị chi tiết phiếu
    QL->>HT: Yêu cầu xem giá vốn/chi phí hóa đơn
    HT->>XM: Lấy giá vốn/chi phí hóa đơn
    XM-->>HT: Thông tin giá vốn
    HT-->>QL: Hiển thị thông tin giá vốn
    QL->>QL: Kiểm tra giá đề xuất, giá vốn và điều khoản

    alt Chấp nhận
        QL->>HT: Chọn "Chấp nhận"
        HT-->>QL: Yêu cầu xác nhận duyệt
        QL->>HT: Xác nhận
        HT->>PCH: Cập nhật trạng thái "Đã duyệt"
        HT->>PCH: Lưu người duyệt, thời điểm, ghi chú
        PCH-->>HT: Cập nhật thành công
        HT-->>NVBH: Thông báo phiếu đã được duyệt
        HT-->>QL: Hiển thị trạng thái "Đã duyệt"

    else Yêu cầu điều chỉnh
        QL->>HT: Chọn "Yêu cầu điều chỉnh"
        QL->>HT: Nhập nội dung/giá mong muốn
        HT->>PCH: Cập nhật "Yêu cầu điều chỉnh"
        PCH-->>HT: Cập nhật thành công
        HT-->>NVBH: Gửi thông tin để thương lượng lại

        alt Khách đồng ý điều chỉnh
            NVBH->>HT: Lập phiếu chào hàng mới
            HT->>PCH: Phiếu cũ = "Đã thay thế/Hủy"
            HT->>PCH: Tạo phiếu mới
            PCH-->>HT: Tạo thành công
            HT-->>QL: Phiếu mới quay lại quy trình duyệt
        else Khách không đồng ý
            NVBH->>HT: Cập nhật "Khách không đồng ý"
            HT->>PCH: Lưu kết quả vào lịch sử
            PCH-->>HT: Lưu thành công
        end

    else Từ chối
        QL->>HT: Chọn "Từ chối"
        HT-->>QL: Yêu cầu nhập lý do
        QL->>HT: Nhập lý do và xác nhận
        HT->>PCH: Cập nhật trạng thái "Từ chối"
        HT->>PCH: Lưu lý do, người duyệt, thời điểm
        PCH-->>HT: Cập nhật thành công
        HT-->>NVBH: Thông báo phiếu bị từ chối
        HT-->>QL: Hiển thị trạng thái "Từ chối"
    end
```

## Đối tượng tham gia

  -----------------------------------------------------------------------
  Đối tượng                           Vai trò
  ----------------------------------- -----------------------------------
  **Chủ cửa hàng/Quản lý**            Actor chính kiểm tra và duyệt phiếu
                                      chào hàng.

  **Hệ thống**                        Tiếp nhận thao tác, hiển thị dữ
                                      liệu và điều phối xử lý.

  **Phiếu chào hàng (PCH)**           Lưu giá đề xuất, trạng thái, nội
                                      dung điều chỉnh, lý do từ chối,
                                      người duyệt và thời điểm duyệt.

  **Xe mới**                          Cung cấp thông tin xe, giá vốn và
                                      chi phí hóa đơn.

  **Xe cũ**                           Cung cấp thông tin xe cũ, giá đổi
                                      và giá trị tham khảo khi có giao
                                      dịch đổi xe.

  **Nhân viên bán hàng (NVBH)**       Nhận kết quả duyệt/yêu cầu điều
                                      chỉnh và thực hiện thương lượng
                                      lại.
  -----------------------------------------------------------------------
