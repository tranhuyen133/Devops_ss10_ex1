# Báo cáo kỹ thuật: Container vs Virtual Machine

## Bối cảnh
Nhiều doanh nghiệp chuyển từ VPS (dựa trên máy ảo) sang Docker để
tiết kiệm tài nguyên, triển khai nhanh và dễ mở rộng. Báo cáo này
phân tích sự khác biệt kiến trúc giữa hai mô hình ảo hóa.

## 1. Bảng so sánh VM vs Container

| Tiêu chí            | Virtual Machine            | Container                     |
|---------------------|----------------------------|-------------------------------|
| Dung lượng Image    | Lớn (hàng GB, chứa cả OS)   | Nhỏ (vài MB–vài trăm MB)      |
| Thời gian khởi động | Chậm (vài phút, phải boot OS) | Nhanh (vài giây)           |
| Hao phí OS          | Cao (mỗi VM 1 OS riêng)     | Thấp (chia sẻ kernel của host)|
| Mức độ cô lập       | Rất cao (ảo hóa phần cứng)  | Trung bình (cô lập ở mức OS)  |
| Khả năng mở rộng    | Hạn chế, nặng nề            | Rất linh hoạt, scale nhanh    |

**Giải thích ngắn:** VM ảo hóa ở tầng phần cứng qua Hypervisor, mỗi VM
chạy một hệ điều hành đầy đủ nên nặng. Container ảo hóa ở tầng hệ điều
hành, dùng chung kernel của host nên nhẹ và khởi động gần như tức thì.

## 2. Namespaces và Control Groups (cgroups)

### Namespaces — cô lập (isolation)
Namespaces là cơ chế của Linux kernel giúp mỗi container "nhìn thấy" một
hệ thống riêng, dù thực chất dùng chung kernel:
- **PID namespace:** cô lập tiến trình — container chỉ thấy process của nó.
- **Network namespace:** mỗi container có network stack riêng (IP, port, route).
- **Mount namespace:** cô lập hệ thống file (mount points) riêng cho container.

### Control Groups (cgroups) — giới hạn tài nguyên
Cgroups kiểm soát và giới hạn tài nguyên mỗi container được dùng:
- **CPU:** giới hạn số core / tỷ lệ CPU.
- **RAM:** đặt mức bộ nhớ tối đa, tránh một container chiếm hết tài nguyên host.

Namespaces lo phần "cô lập", cgroups lo phần "giới hạn tài nguyên" — hai
cơ chế này là nền tảng để Docker hoạt động.

## 3. Thông số từ `docker info`

| Thông số       | Giá trị trên máy        |
|----------------|-------------------------|
| Server Version | <điền giá trị thật>     |
| Cgroup Driver  | <điền giá trị thật>     |
| Containers     | <điền số thật>          |
| Images         | <điền số thật>          |