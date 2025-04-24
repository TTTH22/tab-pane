<!DOCTYPE html>
<html lang="vi" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Quản lý đơn hàng</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.1/font/bootstrap-icons.css">
    <style>
        .nav-pills .nav-link.active {
            background-color: #0d6efd;
        }

        .order-card {
            transition: transform 0.3s;
            margin-bottom: 1rem;
        }

        .order-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 20px rgba(0, 0, 0, 0.1);
        }

        .status-badge {
            font-size: 0.8rem;
            padding: 0.4rem 0.6rem;
        }

        .order-date {
            font-size: 0.85rem;
            color: #6c757d;
        }

        .empty-state {
            text-align: center;
            padding: 3rem 1rem;
            color: #6c757d;
        }

        .empty-state i {
            font-size: 3rem;
            margin-bottom: 1rem;
        }
    </style>
</head>
<body>
<div class="container py-5">
    <h2 class="mb-4 fw-bold">
        <i class="bi bi-box-seam me-2"></i>Đơn hàng của tôi
    </h2>

    <!-- Tab Navigation -->
    <ul class="nav nav-pills mb-4">
        <li class="nav-item">
            <a class="nav-link active" data-bs-toggle="pill" href="#waiting">
                <i class="bi bi-hourglass-split me-1"></i>Chờ xác nhận
                <span class="badge bg-primary rounded-pill ms-1" th:text="${listChoXacNhan.size()}"></span>
            </a>
        </li>
        <li class="nav-item">
            <a class="nav-link" data-bs-toggle="pill" href="#shipping">
                <i class="bi bi-truck me-1"></i>Đang giao
                <span class="badge bg-primary rounded-pill ms-1" th:text="${listDangGiao.size()}"></span>
            </a>
        </li>
        <li class="nav-item">
            <a class="nav-link" data-bs-toggle="pill" href="#delivered">
                <i class="bi bi-check-circle me-1"></i>Đã giao
                <span class="badge bg-primary rounded-pill ms-1" th:text="${listDaGiao.size()}"></span>
            </a>
        </li>
        <li class="nav-item">
            <a class="nav-link" data-bs-toggle="pill" href="#waiting-cancel">
                <i class="bi bi-clock-history me-1"></i>Chờ xác nhận hoàn hàng
                <span class="badge bg-primary rounded-pill ms-1" th:text="${listChoHoan.size()}"></span>
            </a>
        </li>
        <li class="nav-item">
            <a class="nav-link" data-bs-toggle="pill" href="#cancel">
                <i class="bi bi-clock-history me-1"></i>Đã hoàn hàng
                <span class="badge bg-primary rounded-pill ms-1" th:text="${listDaHoan.size()}"></span>
            </a>
        </li>
        <li class="nav-item">
            <a class="nav-link" data-bs-toggle="pill" href="#canceled">
                <i class="bi bi-x-circle me-1"></i>Đã hủy
                <span class="badge bg-primary rounded-pill ms-1" th:text="${listDaHuy.size()}"></span>
            </a>
        </li>
    </ul>

    <!-- Tab Content -->
    <div class="tab-content">
        <!-- Chờ xác nhận -->
        <div class="tab-pane fade show active" id="waiting">
            <div class="card order-card" th:each="hd : ${listChoXacNhan}">
                <div class="card-header d-flex justify-content-between align-items-center">
                    <span class="fw-bold" th:text="'Đơn hàng ' + ${hd.ma}"></span>
                    <span class="badge bg-warning status-badge">Chờ xác nhận</span>
                </div>
                <div class="card-body" th:each="hdct : ${hd.listHoaDonChiTiet}">
                    <div class="row mb-3">
                        <div class="col-md-2 col-4">
                            <img th:src="${hdct.idSanPhamChiTiet.idSanPham.anhSanPham}" style="width: 80px"
                                 alt="Sản phẩm" class="img-thumbnail">
                        </div>
                        <div class="col-md-10 col-8">
                            <h6 class="mb-1" th:text="${hdct.idSanPhamChiTiet.idSanPham.ten}"></h6>
                            <p class="text-muted mb-1" th:text="'Phân loại: ' + ${hdct.idSanPhamChiTiet.idMauSac.ten}
                             + ', ' + ${hdct.idSanPhamChiTiet.idKhoiLuong.ten} +'g'"></p>
                            <p class="mb-0" th:text="'x' + ${hdct.soLuong}"></p><span
                                class="float-end text-danger fw-bold"
                                th:text="${#numbers.formatCurrency(hdct.donGia)}"></span>
                        </div>
                    </div>
                    <hr>
                </div>
                <div class="card-footer">
                    <div class="d-flex justify-content-between align-items-center">
                        <span class="order-date"><i class="bi bi-calendar3 me-1"></i><i
                                th:text="${hd.ngayTao}"></i></span>
                        <div>
                            <span class="me-3 fw-bold"
                                  th:text="'Tổng tiền: ' + ${#numbers.formatCurrency(hd.tongTien)}"></span>
                            <button class="btn btn-sm btn-danger" th:onclick="'showHuyDonModal(' + ${hd.id} + ')'">Hủy
                                đơn
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- Modal xác nhận hủy đơn -->
        <div class="modal fade" id="huyDonModal" tabindex="-1" aria-hidden="true">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title">Xác nhận hủy đơn hàng</h5>
                        <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
                    </div>
                    <div class="modal-body">
                        <input type="hidden" id="huyDonId">
                        <div class="mb-3">
                            <label for="lyDoHuy" class="form-label">Lý do hủy</label>
                            <select class="form-select" id="lyDoHuy">
                                <option value="Không còn nhu cầu" selected>Không còn nhu cầu</option>
                                <option value="Tôi đặt nhầm">Tôi đặt nhầm</option>
                                <option value="Tôi muốn thay đổi mặt hàng">Tôi muốn thay đổi mặt hàng</option>
                                <option value="Khác">Khác</option>
                            </select>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button class="btn btn-secondary" data-bs-dismiss="modal">Đóng</button>
                        <button class="btn btn-danger" onclick="xacNhanHuy()">Xác nhận hủy</button>
                    </div>
                </div>
            </div>
        </div>

        <!-- Đang giao -->
        <div class="tab-pane fade" id="shipping">
            <div class="card order-card" th:each="hd : ${listDangGiao}">
                <div class="card-header d-flex justify-content-between align-items-center">
                    <span class="fw-bold" th:text="'Đơn hàng ' + ${hd.ma}"></span>
                    <span class="badge bg-info status-badge">Đang giao</span>
                </div>
                <div class="card-body" th:each="hdct : ${hd.listHoaDonChiTiet}">
                    <div class="row mb-3">
                        <div class="col-md-2 col-4">
                            <img th:src="${hdct.idSanPhamChiTiet.idSanPham.anhSanPham}" style="width: 80px"
                                 alt="Sản phẩm" class="img-thumbnail">
                        </div>
                        <div class="col-md-10 col-8">
                            <h6 class="mb-1" th:text="${hdct.idSanPhamChiTiet.idSanPham.ten}"></h6>
                            <p class="text-muted mb-1" th:text="'Phân loại: ' + ${hdct.idSanPhamChiTiet.idMauSac.ten}
                             + ', ' + ${hdct.idSanPhamChiTiet.idKhoiLuong.ten} +'g'"></p>
                            <p class="mb-0" th:text="'x' + ${hdct.soLuong}"></p><span
                                class="float-end text-danger fw-bold"
                                th:text="${#numbers.formatCurrency(hdct.donGia)}"></span>
                        </div>
                    </div>
                    <hr>
                </div>
                <div class="card-footer">
                    <div class="d-flex justify-content-between align-items-center">
                        <span class="order-date"><i class="bi bi-calendar3 me-1"></i><i
                                th:text="${hd.ngayTao}"></i></span>
                        <div>
                            <span class="me-3 fw-bold"
                                  th:text="'Tổng tiền: ' + ${#numbers.formatCurrency(hd.tongTien)}"></span>
                            <button class="btn btn-sm btn-primary">Theo dõi</button>
                            <button class="btn btn-sm btn-primary" th:onclick="'showDaNhanDonModal(' + ${hd.id} + ')'">
                                Đã nhận
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- Đã giao -->
        <div class="tab-pane fade" id="delivered">
            <div class="card order-card" th:each="hd : ${listDaGiao}">
                <div class="card-header d-flex justify-content-between align-items-center">
                    <span class="fw-bold" th:text="'Đơn hàng ' + ${hd.ma}"></span>
                    <span class="badge bg-success status-badge">Đã giao</span>
                </div>
                <div class="card-body" th:each="hdct : ${hd.listHoaDonChiTiet}">
                    <div class="row mb-3">
                        <div class="col-md-2 col-4">
                            <img th:src="${hdct.idSanPhamChiTiet.idSanPham.anhSanPham}" style="width: 80px"
                                 alt="Sản phẩm" class="img-thumbnail">
                        </div>
                        <div class="col-md-10 col-8">
                            <h6 class="mb-1" th:text="${hdct.idSanPhamChiTiet.idSanPham.ten}"></h6>
                            <p class="text-muted mb-1" th:text="'Phân loại: ' + ${hdct.idSanPhamChiTiet.idMauSac.ten}
                             + ', ' + ${hdct.idSanPhamChiTiet.idKhoiLuong.ten} +'g'"></p>
                            <p class="mb-0" th:text="'x' + ${hdct.soLuong}"></p><span
                                class="float-end text-danger fw-bold"
                                th:text="${#numbers.formatCurrency(hdct.donGia)}"></span>
                        </div>
                    </div>
                    <hr>
                </div>
                <div class="card-footer">
                    <div class="d-flex justify-content-between align-items-center">
                        <span class="order-date"><i class="bi bi-calendar3 me-1"></i><i
                                th:text="${hd.ngayTao + ' - ' + hd.ngayNhan}"></i></span>
                        <div>
                            <span class="me-3 fw-bold"
                                  th:text="'Tổng tiền: ' + ${#numbers.formatCurrency(hd.tongTien)}"></span>
                            <button class="btn btn-sm btn-outline-primary" th:onclick="'muaLai(' + ${hd.id} + ')'">Mua
                                lại
                            </button>
                            <button class="btn btn-sm btn-outline-secondary"
                                    th:disabled="${hd.danhGia}"
                                    th:onclick="'moDanhGia(' + ${hd.id} + ')'">
                                Đánh giá
                            </button>
                            <button class="btn btn-sm btn-outline-danger"
                                    th:attr="data-id=${hd.id}, data-ngaynhan=${hd.ngayNhan}"
                                    onclick="hoanHang(this.getAttribute('data-id'), this.getAttribute('data-ngaynhan'))">
                                Hoàn hàng
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div id="modalHoanHang"
             style="display: none; position: fixed; z-index: 9999; top: 50%; left: 50%; transform: translate(-50%, -50%); background: white; padding: 20px; border-radius: 10px; box-shadow: 0 0 10px rgba(0,0,0,0.3); width: 400px;">
            <h5 class="mb-3">Yêu cầu hoàn hàng</h5>
            <input type="hidden" id="hoanHang_hoaDonId">

            <div class="mb-3">
                <label for="hoanHang_lyDo" class="form-label">Lý do:</label>
                <select id="hoanHang_lyDo" class="form-select">
                    <option value="Sai sản phẩm">Sai sản phẩm</option>
                    <option value="Hỏng, lỗi sản phẩm">Hỏng, lỗi sản phẩm</option>
                    <option value="Không hài lòng">Không hài lòng</option>
                    <option value="Khác">Khác</option>
                </select>
            </div>

            <div class="mb-3">
                <label for="hoanHang_moTa" class="form-label">Mô tả thêm:</label>
                <textarea id="hoanHang_moTa" class="form-control" rows="3"></textarea>
            </div>

            <div class="text-end">
                <button class="btn btn-secondary me-2" onclick="dongModal()">Đóng</button>
                <button class="btn btn-primary" onclick="guiYeuCauHoanHang()">Gửi yêu cầu</button>
            </div>
        </div>


        <!-- Chờ xác nhận hoàn hàng -->
        <div class="tab-pane fade" id="waiting-cancel">
            <div class="card order-card" th:each="hd : ${listChoHoan}">
                <div class="card-header d-flex justify-content-between align-items-center">
                    <span class="fw-bold" th:text="'Đơn hàng ' + ${hd.ma}"></span>
                    <span class="badge bg-secondary status-badge">Chờ xác nhận hoàn hàng</span>
                </div>
                <div class="card-body" th:each="hdct : ${hd.listHoaDonChiTiet}">
                    <div class="row mb-3">
                        <div class="col-md-2 col-4">
                            <img th:src="${hdct.idSanPhamChiTiet.idSanPham.anhSanPham}" style="width: 80px"
                                 alt="Sản phẩm" class="img-thumbnail">
                        </div>
                        <div class="col-md-10 col-8">
                            <h6 class="mb-1" th:text="${hdct.idSanPhamChiTiet.idSanPham.ten}"></h6>
                            <p class="text-muted mb-1" th:text="'Phân loại: ' + ${hdct.idSanPhamChiTiet.idMauSac.ten}
                             + ', ' + ${hdct.idSanPhamChiTiet.idKhoiLuong.ten} +'g'"></p>
                            <p class="mb-0" th:text="'x' + ${hdct.soLuong}"></p><span
                                class="float-end text-danger fw-bold"
                                th:text="${#numbers.formatCurrency(hdct.donGia)}"></span>
                        </div>
                    </div>
                    <hr>
                </div>
                <div class="alert alert-secondary mt-3 mb-0">
                    <div class="d-flex">
                        <div class="me-2">
                            <i class="bi bi-info-circle"></i>
                        </div>
                        <div>
                            <h6 class="mb-1">Thông tin hoàn đơn</h6>
                            <p class="mb-1"><strong>Lý do: </strong> <i th:text="${hd.lyDoHoanHang}"></i></p>
                            <p class="mb-1"><strong>Ghi chú: </strong> <i th:text="${hd.ghiChuHoanHang}"></i></p>
                        </div>
                    </div>
                </div>
                <div class="card-footer">
                    <div class="d-flex justify-content-between align-items-center">
                        <span class="order-date"><i class="bi bi-calendar3 me-1"></i><i
                                th:text="${hd.ngayHoanHang}"></i></span>
                        <div>
                            <span class="me-3 fw-bold"
                                  th:text="'Tổng tiền: ' + ${#numbers.formatCurrency(hd.tongTien)}"></span>
                            <button class="btn btn-sm btn-outline-secondary">Liên hệ CSKH</button>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- Đã Hoàn -->
        <div class="tab-pane fade" id="cancel">
            <div class="card order-card" th:each="hd : ${listDaHoan}">
                <div class="card-header d-flex justify-content-between align-items-center">
                    <span class="fw-bold" th:text="'Đơn hàng ' + ${hd.ma}"></span>
                    <span class="badge bg-secondary status-badge">Đã hoàn hàng</span>
                </div>
                <div class="card-body" th:each="hdct : ${hd.listHoaDonChiTiet}">
                    <div class="row mb-3">
                        <div class="col-md-2 col-4">
                            <img th:src="${hdct.idSanPhamChiTiet.idSanPham.anhSanPham}" style="width: 80px"
                                 alt="Sản phẩm" class="img-thumbnail">
                        </div>
                        <div class="col-md-10 col-8">
                            <h6 class="mb-1" th:text="${hdct.idSanPhamChiTiet.idSanPham.ten}"></h6>
                            <p class="text-muted mb-1" th:text="'Phân loại: ' + ${hdct.idSanPhamChiTiet.idMauSac.ten}
                             + ', ' + ${hdct.idSanPhamChiTiet.idKhoiLuong.ten} +'g'"></p>
                            <p class="mb-0" th:text="'x' + ${hdct.soLuong}"></p><span
                                class="float-end text-danger fw-bold"
                                th:text="${#numbers.formatCurrency(hdct.donGia)}"></span>
                        </div>
                    </div>
                    <hr>
                </div>
                <div class="alert alert-secondary mt-3 mb-0">
                    <div class="d-flex">
                        <div class="me-2">
                            <i class="bi bi-info-circle"></i>
                        </div>
                        <div>
                            <h6 class="mb-1">Thông tin hoàn đơn</h6>
                            <p class="mb-1"><strong>Lý do: </strong> <i th:text="${hd.lyDoHoanHang}"></i></p>
                            <p class="mb-1"><strong>Ghi chú: </strong> <i th:text="${hd.ghiChuHoanHang}"></i></p>
                        </div>
                    </div>
                </div>
                <div class="card-footer">
                    <div class="d-flex justify-content-between align-items-center">
                        <span class="order-date"><i class="bi bi-calendar3 me-1"></i><i
                                th:text="${hd.ngayHoanHang}"></i></span>
                        <div>
                            <span class="me-3 fw-bold"
                                  th:text="'Tổng tiền: ' + ${#numbers.formatCurrency(hd.tongTien)}"></span>
                            <button class="btn btn-sm btn-outline-secondary">Liên hệ CSKH</button>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- Đã hủy -->
        <div class="tab-pane fade" id="canceled">
            <div class="card order-card" th:each="hd : ${listDaHuy}">
                <div class="card-header d-flex justify-content-between align-items-center">
                    <span class="fw-bold" th:text="'Đơn hàng ' + ${hd.ma}"></span>
                    <span class="badge bg-secondary status-badge">Đã hủy</span>
                </div>
                <div class="card-body" th:each="hdct : ${hd.listHoaDonChiTiet}">
                    <div class="row mb-3">
                        <div class="col-md-2 col-4">
                            <img th:src="${hdct.idSanPhamChiTiet.idSanPham.anhSanPham}" style="width: 80px"
                                 alt="Sản phẩm" class="img-thumbnail">
                        </div>
                        <div class="col-md-10 col-8">
                            <h6 class="mb-1" th:text="${hdct.idSanPhamChiTiet.idSanPham.ten}"></h6>
                            <p class="text-muted mb-1" th:text="'Phân loại: ' + ${hdct.idSanPhamChiTiet.idMauSac.ten}
                             + ', ' + ${hdct.idSanPhamChiTiet.idKhoiLuong.ten} +'g'"></p>
                            <p class="mb-0" th:text="'x' + ${hdct.soLuong}"></p><span
                                class="float-end text-danger fw-bold"
                                th:text="${#numbers.formatCurrency(hdct.donGia)}"></span>
                        </div>
                    </div>
                    <hr>
                </div>
                <div class="alert alert-secondary mt-3 mb-0">
                    <div class="d-flex">
                        <div class="me-2">
                            <i class="bi bi-info-circle"></i>
                        </div>
                        <div>
                            <h6 class="mb-1">Thông tin hoàn đơn</h6>
                            <p class="mb-1"><strong>Lý do: </strong> <i th:text="${hd.lyDoHuy}"></i></p>
                        </div>
                    </div>
                </div>
                <div class="card-footer">
                    <div class="d-flex justify-content-between align-items-center">
                        <span class="order-date"><i class="bi bi-calendar3 me-1"></i><i
                                th:text="${hd.ngayHuyHang}"></i></span>
                        <div>
                            <span class="me-3 fw-bold"
                                  th:text="'Tổng tiền: ' + ${#numbers.formatCurrency(hd.tongTien)}"></span>
                            <button class="btn btn-sm btn-outline-primary" th:onclick="'muaLai(' + ${hd.id} + ')'">
                                Mua
                                lại
                            </button>
                            <button class="btn btn-sm btn-outline-secondary">Liên hệ CSKH</button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/sweetalert2@11"></script>
<script src="/js/DonHang/DonHangChoXacNhan.js"></script>
<script src="/js/DonHang/DonHangDangGiao.js"></script>
<script src="/js/DonHang/DonHangDaGiao.js"></script>
</body>
</html>
