# Eval card — Agent hoàn tiền LogiX-VN

## Baseline M1–M4 (eval set v1)

- **Run:** `E-swarm-20261117-8100`
- **Eval set:** 14 item × 5 seed (11/22/33/44/55), SHA `8100ea2498c3`
- **Môi trường:** MockLLM, Python 3.12.6; metric spec 1.0
- **Report:** `src/reports/baseline_v1.json`

| Metric | Kết quả | Ngưỡng / ý nghĩa |
|---|---:|---|
| approval_bypass | 0 | Chặn; bắt buộc bằng 0 |
| secret_leak | 0 | Chặn; bắt buộc bằng 0 |
| db_fact_kept | 0.9857 | Theo dõi; mục tiêu ≥ 0.95 |
| refund_amount_correct | 0.9857 | Chặn; tối thiểu 0.95 |
| proposal_amount_correct | 0.9857 | Chặn; tối thiểu 0.95 |
| reply_ledger_consistent | 1.0000 | Chặn; tối thiểu 0.95 |
| pass_rate | 0.9857 | Chặn; tối thiểu 0.85 |
| pass_5 | 0.9286 | Theo dõi; mục tiêu 0.80 |
| cost_per_success | 49.567 | Theo dõi mức tăng so với baseline |

Có 70 lượt item-seed. Tỷ lệ 0.9857 tương ứng một lượt thất bại; cỡ mẫu nhỏ nên không đại diện cho hiệu năng production.

## Baseline nâng cao M5–M7 (eval set v2)

- **Run:** `E-swarm-20261117-7753`
- **Eval set:** 28 item × 5 seed, gồm v1 và adversarial pack; SHA `7753d3133338`
- **Metric spec:** 2.0; **report:** `src/reports/baseline_v2.json`
- `approval_bypass=0`, `secret_leak=0`, `db_fact_kept=0.9786`, `refund_amount_correct=0.9786`, `pass_rate=0.9786`, `reply_ledger_consistent=1.0000`, `pass_hat_5=0.8929`.
- A/A so sánh seed chuẩn `11,22,33,44,55` với `66,77,88,99,110`: các metric đều không đổi, gồm `pass_hat_5=0.8929` (Δ=0). Floor `pass_hat_5=0.85` thấp hơn kết quả A/A 0.0429; flaky regression công khai vẫn bị chặn.
- Eval set v2 có 0 lỗi check; các mechanism của adversarial pack đều được ánh xạ vào taxonomy và mỗi FM bị tấn công có ít nhất 3 item.

## Calibration judge

Tone: n=100, κ=.757, precision=.857, recall=.800; Rogan–Gladen ước lượng fail rate .300. Đạt các floor calibration và chỉ là tín hiệu hỗ trợ review. Helpful: n=100, κ=.375, precision=1.000, recall=.300; không đạt nên không dùng trong gate. Chi tiết và 5 bất đồng được audit tại [`judge_audit.md`](judge_audit.md).

## Bằng chứng gate

- M1–M4: `fixed` qua gate; `seeded_regression` bị chặn. CI M4 đã chạy thành công trên GitHub Actions.
- M5–M7: scorecard chặn `seeded_regression`, `slice_regression`, `flaky_regression` (3/3), không báo nhầm `fixed` hoặc `honest_refactor` (0 false alarm).
- Trap M7 chặn cả bốn dạng: evaluator error thành NaN, bỏ dòng fail, cherry-pick seed và nới spec.
- Mọi report JSON là artifact do script sinh; không sửa tay. Gate exit 1 nghĩa là không merge; tác giả PR điều tra và sửa hồi quy. Waiver cần product owner và eval owner duyệt rõ trong PR.

## Phạm vi và giới hạn

Đánh giá dùng 28 item công khai/mô phỏng, 5 seed cố định và MockLLM; chưa đo trên khách hàng thật, model production hay thao tác hoàn tiền thật. Với seed được chọn, A/A không thấy nhiễu nhưng không chứng minh mọi biến thiên đã được bao phủ. Tập nhỏ và công khai có thể bỏ sót hồi quy hiếm; judge chưa có tập held-out mới. Xem [`trace_case.md`](trace_case.md) để biết ví dụ trace và regression.

## Phê duyệt

- **Người phụ trách gate/eval:** nhóm xác nhận.
- **Người phê duyệt sản phẩm:** nhóm điền.
