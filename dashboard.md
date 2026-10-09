# OPERATING DASHBOARD — Chatbot AI chốt đơn & CSKH cho shop online SMB

**Loại mô hình:** B2B (chủ shop vừa trả tiền vừa dùng sản phẩm; Haravan chỉ là kênh phân phối, chia 20%) · **Cập nhật:** 09/10/2026 · Ngô Lê Thuỷ Tiên – 2A202602614
**NORTH STAR:** Time-to-first-value (TTFV) — hiện tại ⚪ chưa đo (0 shop) — mục tiêu trung vị ≤7 ngày trên 5 shop pilot trước 08/12/2026

### Đèn báo sớm (Leading — nhìn hằng ngày/tuần)

Định nghĩa "xong": bot trả lời hết hoặc chốt đơn VÀ trong 24 giờ người mua không hỏi lại, không đòi gặp người, chủ shop không tiếp quản. Token do tôi gánh 100% (giá tính theo job).

| Đèn | Hiện | 🟢 / 🟡 / 🔴 | Nguồn | Báo trước cho |
|---|---|---|---|---|
| TTFV (ngày từ cài app đến hội thoại thật đầu tiên xong) | ⚪ chưa đo | ≤7 / 8–30 / >30 ngày | [TB] | POC→paid, churn |
| Containment rate | 82% (ước tính) ⚪ | ≥80% / 69,1–80% / <69,1% | [MH] | GM, churn |
| Cost/Job (AI) | $0,1047 (mô hình) 🟢 | ≤$0,1125 / ≤$0,1575 / >$0,1575 | [MH] | GM |

### Đèn vận hành (Operating — nhìn hằng tuần/tháng)

| Đèn | Hiện | 🟢 / 🟡 / 🔴 | Nguồn | Báo trước cho |
|---|---|---|---|---|
| Usage depth (job xong/shop/tháng) | ⚪ chưa đo (mô hình giả định 400) | ≥400 / 267–400 / <267 | [MH] | CAC payback, churn |
| POC → paid | ⚪ chưa có pilot | ≥50% / 35–50% / <35% | [BM] ICONIQ GTM 2026, kiểm tra 09/10/2026 | CAC payback |

### Đèn kết quả (Lagging — nhìn hằng quý)

| Đèn | Hiện | 🟢 / 🟡 / 🔴 | Nguồn |
|---|---|---|---|
| Gross Margin sau phí kênh | 56,7% (mô hình, chưa gồm overhead) 🟢 | ≥55% / 45–55% / <45% | [BM] ICONIQ 07/2026, kiểm tra 09/10/2026 + [MH] |
| CAC payback | ⚪ chưa đo (CAC tối đa ≈$1.225) | ≤12 / 12–18 / >18 tháng | [BM]+[MH] |
| Churn shop hằng tháng | ⚪ chưa có shop trả tiền | <5,6% / 5,6–8,3% / >8,3% | [MH] |

### 5 luật quyết định (⏹ = luật dừng)

1. ⏹ NẾU TTFV >30 ngày TRÊN 3 shop gần nhất (chưa có hội thoại thật sau 30 ngày = >30) THÌ Tiên trong 48 giờ dừng nhận shop mới, cắt onboarding về 1 kênh + 1 luồng đến khi 3 shop liên tiếp <7 ngày KHÔNG THÌ không đưa thêm shop vào phễu, không viết thêm tính năng.
2. ⏹ NẾU containment <69,1% TRÊN 200 hội thoại gần nhất của một shop THÌ Tiên tắt ngay trong ngày tự chốt đơn ở shop đó, chuyển sang bot soạn nháp, chủ shop bấm gửi KHÔNG THÌ không hạ ngưỡng chuyển sang người để số đẹp hơn.
3. NẾU Cost/Job của shop >$0,1575 TRONG 2 tuần VÀ ≥90 job/tuần THÌ Tiên trong 48 giờ bắt đầu chuyển luồng tốn token nhất sang model rẻ hơn hoặc cắt ngữ cảnh KHÔNG THÌ không tăng giá $0,45 đại trà.
4. NẾU usage depth của shop <267 job/tháng SAU 60 ngày go-live THÌ Tiên gọi chủ shop trong 1 tuần, sửa 1 luồng chưa chạy trước khi bàn gia hạn KHÔNG THÌ không mời shop đó mở thêm kênh hay ngành.
5. ⏹ NẾU POC→paid <35% (≤1/5 shop) TRÊN 5 shop pilot đầu THÌ Tiên dừng nhận pilot mới ngay hôm đó, phỏng vấn lại cả 5 shop trong 2 tuần KHÔNG THÌ không giảm giá $0,45.

### Cổng gác 90 ngày

| Ngày | Metric (1) | Ngưỡng qua cổng | Bằng chứng | Nếu trượt |
|---|---|---|---|---|
| 30 (08/11/2026) — cổng học | Containment đo bằng eval 200 hội thoại | ≥69,1% | File `eval_results` có 200 dòng chấm đúng/sai (hạn 22/10) | FIX một lần nếu 63–69,1% và biết lỗi ở đâu; PIVOT value metric (Outcome → Hybrid) nếu <63% |
| 60 (08/12/2026) | TTFV trung vị của 5 shop pilot | ≤7 ngày (tức ≥3/5 shop xong trong 7 ngày) | Log `app_installed` và hội thoại thật đầu tiên theo shop_id + Pilot Report nháp (hạn 30/11) | FIX một lần (cắt onboarding về 1 kênh, 1 luồng); PIVOT sang PLG nếu Haravan chưa trả lời |
| 90 (07/01/2027) | POC → paid trên 5 shop pilot | ≥50% (≥3/5 shop trả tiền) | Ghi nhận thanh toán của từng shop + Pilot Report có số | PIVOT sang Hybrid (phí nền + $/job); KILL nếu đã FIX một lần mà vẫn trượt |

**KILL CRITERIA:** Dừng sản phẩm nếu đến 07/01/2027 chỉ còn ≤1/5 shop pilot trả tiền (đúng ngưỡng đỏ POC→paid <35%) mà trước đó đã dùng FIX một lần ở ngày 30 hoặc 60, HOẶC containment đo thật vẫn <63% sau khi đã FIX một lần. Còn 2/5 shop trả tiền là vùng vàng: FIX hoặc PIVOT, chưa KILL.

**CHƯA ĐO ĐƯỢC:**
- **Containment 82% và Cost/Job $0,1047** là số mô hình, chưa có eval hay log thật — cần chạy eval 200 hội thoại, có số 22/10/2026 (Cost/Job thật có sau pilot, ~30/11/2026).
- **TTFV, usage depth, POC→paid, churn** — chưa có shop nào; cần event `app_installed` + hội thoại thật đầu tiên + log job theo shop_id, có số đầu tiên ~08/12/2026.
- **CAC thật qua Haravan** — chưa nói chuyện với Haravan; $1.657 là ngân sách, không phải số đo; mức chia 20% chưa được ký và không có chuẩn công bố. Có số khi Haravan trả lời hoặc sau pilot.
- **Chi phí triển khai ÷ ACV, sales cycle, pipeline coverage, NRR, tập trung doanh thu** — chưa theo dõi; cần bảng ghi giờ onboarding và timestamp từng shop, có số đầu tiên sau pilot (~30/11/2026); NRR chỉ có nghĩa sau kỳ gia hạn đầu (≥ quý 2/2027).
- **GM 56,7% chưa gồm overhead** (mô hình để S8 = 0), thực tế sẽ thấp hơn.
