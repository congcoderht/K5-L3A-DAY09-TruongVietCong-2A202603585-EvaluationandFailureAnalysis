# Judge audit — calibration M2

Replay calibration trên mỗi CSV có 100 dòng; lớp dương là `FAIL`. Lệnh chuẩn cho audit tone:
`python -m lab9.judge --csv data/eval/judge_tone_public.csv --audit 5`.

| Tiêu chí | n | κ | Precision | Recall | Fail rate quan sát → Rogan–Gladen | Floors κ≥.6, P≥.8, R≥.7 | Quyết định |
|---|---:|---:|---:|---:|---:|---|---|
| tone_ok | 100 | .757 | .857 | .800 | .280 → .300 | đạt | Chỉ dùng làm tín hiệu phụ về tone |
| helpful | 100 | .375 | 1.000 | .300 | .090 → .300 | không đạt κ và recall | Loại khỏi gate |

Ma trận nhầm lẫn tone (TP, FP, FN, TN) = (24, 4, 6, 66). Helpful = (9, 0, 21, 70). Judge helpful bỏ sót các câu trả lời sai tiền dù lời văn lịch sự; precision 1.0 không bù được recall .3.

## Audit thủ công: 5 bất đồng tone đầu tiên

| Case | Human / judge | Nhận định | Ghi chú |
|---|---|---|---|
| H-008 | FAIL / PASS | Human đúng | “em”, “nha”, “check” quá suồng sã. |
| H-017 | FAIL / PASS | Human đúng | “bạn ơi” không phù hợp với khách lớn tuổi. |
| H-029 | FAIL / PASS | Human đúng | “không cần nhắn thêm” mang giọng ra lệnh. |
| H-042 | PASS / FAIL | Human đúng | Câu ngắn nhưng đủ ý và lịch sự; không bắt buộc phải xin lỗi. |
| H-051 | PASS / FAIL | Human đúng | “refund” được CS LogiX chấp nhận; cách dùng từ không làm câu mất lịch sự. |

Helpful bị loại sau calibration; các FN minh họa gồm HL-015 (reply 55.000đ, ledger 40.000đ), HL-016 (55.000đ so với 35.000đ), HL-020 (90.000đ so với 120.000đ), HL-028 và HL-035 (50.000đ so với ledger 35.000đ). Judge đánh PASS cả năm vì bị lời xin lỗi/giọng lịch sự đánh lạc hướng.

## Kết luận

- Chỉ dùng judge tone cho review hỗ trợ, không để nó quyết định tiền, approval hoặc release.
- Không dùng judge helpful trong gate vì κ và recall thấp hơn floor.
- Rogan–Gladen hiệu chỉnh fail rate ra .30; đây vẫn là ước lượng từ replay công khai, không phải đo độc lập ngoài mẫu.
- Hạn chế: cần tập held-out có người gán nhãn mới trước khi khái quát kết quả sang model hoặc khách hàng thật.
