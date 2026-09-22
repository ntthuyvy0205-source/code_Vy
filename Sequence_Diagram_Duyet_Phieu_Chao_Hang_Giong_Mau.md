# Sequence Diagram -- Duyệt phiếu chào hàng

``` mermaid
sequenceDiagram
    autonumber off

    actor QL as Chủ cửa hàng / Quản lý
    participant GD as GD_DuyetPCH
    participant CTR as Ctr_DuyetPCH
    participant PCH as PhieuChaoHang
    participant KH as KhachHang
    participant XM as XeMoi
    participant XC as XeCu
    actor NVBH as NhanVienBanHang

    Note over QL,NVBH: 1. Xem danh sách phiếu chờ duyệt
    QL->>GD: 1.1 Chọn chức năng duyệt phiếu
    activate GD
    GD->>CTR: 1.2 yeuCauDanhSachPCHChoDuyet()
    activate CTR
    CTR->>PCH: 1.3 layDanhSachChoDuyet()
    activate PCH
    PCH-->>CTR: 1.4 Danh sách phiếu
    deactivate PCH
    CTR-->>GD: 1.5 Hiển thị danh sách phiếu
    deactivate CTR
    GD-->>QL: Hiển thị danh sách phiếu chờ duyệt
    deactivate GD

    rect rgb(250,250,250)
        Note over QL,NVBH: 2. Xem chi tiết phiếu
        QL->>GD: 2.1 Chọn phiếu cần duyệt
        activate GD
        GD->>CTR: 2.2 layChiTietPCH(maPCH)
        activate CTR
        CTR->>PCH: 2.3 layThongTinPCH(maPCH)
        activate PCH
        PCH-->>CTR: 2.4 Thông tin PCH
        deactivate PCH

        CTR->>KH: 2.5 layThongTinKH(maKH)
        activate KH
        KH-->>CTR: 2.6 Thông tin KH
        deactivate KH

        CTR->>XM: 2.7 layThongTinXeMoi(maXe)
        activate XM
        XM-->>CTR: 2.8 Thông tin xe mới
        deactivate XM

        opt Giao dịch đổi xe
            CTR->>XC: 2.9 layThongTinXeCu(maXeCu)
            activate XC
            XC-->>CTR: 2.10 Thông tin xe cũ, giá định giá
            deactivate XC
        end

        CTR-->>GD: 2.11 Hiển thị chi tiết phiếu
        deactivate CTR
        GD-->>QL: Hiển thị chi tiết phiếu
        deactivate GD
    end

    rect rgb(250,250,250)
        Note over QL,NVBH: 3. Xem giá vốn / chi phí hóa đơn
        QL->>GD: 3.1 Yêu cầu xem giá vốn
        activate GD
        GD->>CTR: 3.2 layGiaVonChiPhi(maXe)
        activate CTR
        CTR->>XM: 3.3 layGiaVonChiPhi(maXe)
        activate XM
        XM-->>CTR: 3.4 Giá vốn, chi phí hóa đơn
        deactivate XM
        CTR-->>GD: 3.5 Hiển thị giá vốn/chi phí
        deactivate CTR
        GD-->>QL: Hiển thị giá vốn/chi phí
        deactivate GD
    end

    rect rgb(250,250,250)
        Note over QL,NVBH: 4. Chọn hướng xử lý
        QL->>GD: 4.1 Chọn hướng xử lý<br/>(Chấp nhận / Yêu cầu điều chỉnh / Từ chối)
        activate GD
        GD->>CTR: 4.2 xuLyPCH(maPCH, luaChon)
        activate CTR

        alt Chấp nhận duyệt
            QL->>GD: 4.3 Xác nhận duyệt
            GD->>CTR: Xác nhận
            CTR->>PCH: 4.4 capNhatTrangThai("Đã duyệt", nguoiDuyet, thoiDiem)
            activate PCH
            PCH-->>CTR: 4.5 Kết quả cập nhật
            deactivate PCH
            CTR-->>NVBH: 4.6 Thông báo phiếu đã duyệt

        else Yêu cầu điều chỉnh
            QL->>GD: 4.7 Nhập nội dung/giá mong muốn
            GD->>CTR: Nội dung điều chỉnh
            CTR->>PCH: 4.8 capNhatTrangThai("Yêu cầu điều chỉnh", noiDung)
            activate PCH
            PCH-->>CTR: 4.9 Kết quả cập nhật
            deactivate PCH
            CTR-->>NVBH: 4.10 Gửi yêu cầu điều chỉnh

            alt Khách đồng ý điều chỉnh
                NVBH->>GD: 4.11 Lập phiếu mới theo điều chỉnh
                GD->>CTR: Yêu cầu tạo phiếu mới
                CTR->>PCH: 4.12 taoPhieuChaoHangMoi()
                activate PCH
                PCH-->>CTR: Kết quả tạo phiếu
                deactivate PCH
                CTR-->>NVBH: 4.13 Thông báo lập phiếu mới
            else Khách không đồng ý điều chỉnh
                NVBH->>GD: 4.14 Cập nhật khách không đồng ý
                GD->>CTR: Kết quả thương lượng
                CTR->>PCH: 4.15 luuLichSuKhachKhongDongY()
                activate PCH
                PCH-->>CTR: Kết quả lưu
                deactivate PCH
                CTR-->>NVBH: 4.16 Thông báo kết thúc
            end

        else Từ chối
            QL->>GD: 4.17 Nhập lý do từ chối
            GD->>CTR: Lý do từ chối
            CTR->>PCH: 4.18 capNhatTrangThai("Từ chối", lyDo)
            activate PCH
            PCH-->>CTR: 4.19 Kết quả cập nhật
            deactivate PCH
            CTR-->>NVBH: 4.20 Thông báo phiếu bị từ chối
        end

        deactivate CTR
        deactivate GD
    end
```

## Các đối tượng tham gia

  -----------------------------------------------------------------------
  Đối tượng               Loại                    Vai trò
  ----------------------- ----------------------- -----------------------
  Chủ cửa hàng / Quản lý  Actor                   Xem và quyết định duyệt
                                                  phiếu chào hàng

  GD_DuyetPCH             Boundary                Giao diện duyệt phiếu
                                                  chào hàng

  Ctr_DuyetPCH            Control                 Điều phối nghiệp vụ
                                                  duyệt phiếu

  PhieuChaoHang           Entity                  Lưu và xử lý dữ liệu
                                                  phiếu chào hàng

  KhachHang               Entity                  Cung cấp thông tin
                                                  khách hàng

  XeMoi                   Entity                  Cung cấp thông tin xe
                                                  mới và giá vốn/chi phí

  XeCu                    Entity                  Cung cấp thông tin xe
                                                  cũ khi có giao dịch đổi
                                                  xe

  NhanVienBanHang         Actor                   Nhận kết quả duyệt và
                                                  thực hiện điều
                                                  chỉnh/thương lượng
  -----------------------------------------------------------------------
