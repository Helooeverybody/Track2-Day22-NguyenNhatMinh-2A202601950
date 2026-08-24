# Evidence — Day 22: LangSmith + Prompt Versioning

## Nộp bài

- **LangSmith project URL**: https://smith.langchain.com/o/d1e7551d-47f6-4ea8-b7f2-ddc15f6b23dc/projects/p/b22ef663-aeb1-4029-b9e1-017bd3134eb2

## Danh sách tệp

| Tệp | Nội dung |
|---|---|
| `01_langsmith_traces.png` | Ảnh chụp trang Runs của project `day22-lab`, hiển thị các trace thật |
| `01_run_console.txt` | Log console chạy `01_langsmith_rag_pipeline.py` — 50 câu hỏi/đáp thật |
| `02_prompt_hub.png` | Ảnh chụp Prompt Hub với 2 phiên bản prompt |
| `02_ab_routing_log.txt` | Log console A/B routing 50 câu, có nhãn v1/v2 và URL push lên Hub |
| `03_ragas_scores.txt` | Bảng so sánh điểm RAGAS V1 vs V2 |
| `03_ragas_report.json` | Bản sao `data/ragas_report.json` |
| `04_pii_demo_log.txt` | Log demo PII detector, 6 test case |
| `04_json_demo_log.txt` | Log demo JSON formatter, 5 test case |

## Kết quả RAGAS — V1 vs V2

| Metric | V1 (concise) | V2 (structured expert) | Chênh lệch |
|---|---:|---:|---|
| faithfulness | 0.9610 | 0.9403 | V1 nhỉnh hơn ~0.02 |
| answer_relevancy | 0.9136 | 0.9021 | V1 nhỉnh hơn ~0.01 |
| context_recall | 1.0000 | 1.0000 | Bằng nhau |
| context_precision | 0.9450 | 0.9417 | V1 nhỉnh hơn ~0.003 |

Cả 4 chỉ số đều dùng đủ 50/50 mẫu hợp lệ cho mỗi phiên bản. **Cả 2 phiên bản đều đạt faithfulness ≥ 0.9** (vượt xa ngưỡng yêu cầu 0.8).

## Phân tích V1 so với V2

V1 (system prompt yêu cầu trả lời ngắn gọn 2-4 câu, không có cấu trúc) đạt điểm nhỉnh hơn V2 ở cả 3/4 chỉ số, dù chênh lệch khá nhỏ (0.01–0.02). Lý do khả dĩ nhất: V2 yêu cầu mô hình viết câu kết luận về "mức độ context bao phủ câu hỏi" (ví dụ: "The context provides a comprehensive overview..."). Câu này không trực tiếp trích dẫn từ context, khiến bước kiểm tra faithfulness (tách các claim rồi đối chiếu với context) có xác suất đánh giá một claim là "không được context hỗ trợ" cao hơn một chút so với câu trả lời ngắn gọn thuần trích dẫn của V1. Ngược lại, cấu trúc rõ ràng của V2 không giúp cải thiện context_recall/context_precision vì cả hai phiên bản dùng chung một retriever — sự khác biệt ở 2 chỉ số này chỉ đến từ nhiễu ngẫu nhiên của LLM giám khảo.

Kết luận: với knowledge base và bộ câu hỏi này, phong cách trả lời ngắn gọn (V1) an toàn hơn một chút về mặt faithfulness so với phong cách có thêm nhận định tổng hợp (V2), nhưng khác biệt không đáng kể — cả hai đều vượt xa ngưỡng chấp nhận.
