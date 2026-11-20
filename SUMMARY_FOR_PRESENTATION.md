# Tổng hợp kết quả huấn luyện và đánh giá Uncertainty-RIFA-Net (Cheng)

## Phạm vi thực nghiệm
- Dataset Cheng/Figshare v8: 3.064 MRI T1-CE 2D, 233 bệnh nhân, 3 lớp (meningioma, glioma, pituitary).
- Patient-disjoint outer 5-fold CV. Mỗi fold: 5 mô hình RIFA khởi tạo độc lập, 200 epochs/model. Tổng 25 mô hình/5.000 epoch.
- Monte Carlo dropout: 500 forward passes cho mỗi member khi đánh giá, ensemble trung bình xác suất.
- Phiên bản PyTorch điều chỉnh so với upstream; không phải phép tái lập chính xác 98,37% của tác giả.

## Kết quả pooled out-of-fold (toàn bộ 3.064 lát / 233 bệnh nhân)
| Chỉ số | Giá trị |
|---|---:|
| Image-level accuracy | 88,8708% |
| Image-level balanced accuracy | 87,7887% |
| Image-level macro-F1 | 87,6323% |
| Patient-level accuracy | 88,8412% |
| Patient-level macro-F1 | 89,2332% |
| ROC-AUC macro one-vs-rest | 0,9659 |
| Matthews correlation coefficient | 0,8260 |
| Expected calibration error (10 bins) | 0,0248 |
| Negative log likelihood | 0,3466 |
| Multiclass Brier score | 0,1701 |

## Kết quả từng fold
| Fold | Image accuracy | Image macro-F1 | Patient macro-F1 |
|---|---:|---:|---:|
| 1 | 90,96% | 90,21% | 89,47% |
| 2 | 84,98% | 82,53% | 84,65% |
| 3 | 90,73% | 90,04% | 92,37% |
| 4 | 91,72% | 90,15% | 91,99% |
| 5 | 86,78% | 85,58% | 87,27% |

## Per-class pooled metrics
| Lớp | Precision | Recall | F1 |
|---|---:|---:|---:|
| Meningioma | 78,34% | 78,67% | 78,51% |
| Glioma | 92,01% | 90,39% | 91,19% |
| Pituitary | 92,12% | 94,30% | 93,20% |

## Nhận định từ log
- Trung bình qua 25 member, accuracy ở epoch cuối trên Train là **99,17%**, trên Validation là **88,39%**: chênh lệch khoảng **10,78 điểm phần trăm**. Điều này gợi ý nguy cơ overfitting; cần đọc đường loss và kiểm tra thêm qua external validation.
- Epoch được chọn theo **minimum validation loss** có trung bình **69,32**, trung vị **55**, cho thấy checkpoint tốt nhất nhiều khi xuất hiện trước epoch 200.
- Tổng thời gian epoch tính từ cột `epoch_duration_s`: khoảng **25,0 giờ** (tổng thời lượng ghi nhận các epoch, không phải thời gian end-to-end cả MC evaluation).
- Tổng **341/3.064** lát MRI được dự đoán sai trên OOF. F1 của meningioma thấp hơn hai lớp còn lại, đáng ưu tiên khi phân tích lỗi.
- Không có lớp MRI bình thường; không suy rộng thành bài toán phát hiện tumor vs normal.
- Các ví dụ ảnh đúng/sai chỉ hiển thị **mask tham chiếu** từ dataset, không phải dự đoán segmentation.
- Các đường risk–coverage là đánh giá hậu nghiệm theo confidence, **không phải kết quả RL-1**.

## File sử dụng
- `RIFA_FULL_VISUAL_REPORT_OFFLINE.html`: xem toàn bộ 12 biểu đồ, bảng metrics và 18 MRI predictions bằng Chrome/Edge không cần Python hay kết nối internet.
- `figures/`: 12 hình PNG riêng biệt.
- `raw_logs/fold_*_model_*_history.csv`: đầy đủ 25 log 200 epoch.
- `all_5000_train_epochs.csv`: tất cả 5.000 bản ghi epoch trong một bảng.
- `model_run_summary.csv`: best validation epoch, loss, accuracy và test score cho từng member.
- `FULL_RESULTS.json`: kết quả gốc của pipeline.
- `all_oof_predictions.csv`: xác suất, predicted/actual class của 3.064 lát MRI.
- `prediction_gallery_samples.csv`: 18 ảnh đã chọn làm ví dụ (9 đúng/9 sai).
- `patient_level_predictions.csv`: dự đoán gộp ở cấp bệnh nhân.
- `REPORT_MANIFEST.json`: giao thức, nguồn dữ liệu và vị trí checkpoint đã tham chiếu.

Checkpoint `best.pt` của 25 member giữ nguyên trong `fold_*/model_*/`, không gói vào ZIP để tránh gói rất lớn.

### Cách xem
Mở `RIFA_FULL_VISUAL_REPORT_OFFLINE.html` bằng trình duyệt. Có thể chia sẻ trực tiếp file này; để giữ toàn bộ log + CSV + hình nguồn, chia sẻ ZIP `RIFA_POST_TRAIN_REPORT_FULL.zip`.
