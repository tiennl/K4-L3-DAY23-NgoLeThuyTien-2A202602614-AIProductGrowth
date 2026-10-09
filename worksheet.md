# Worksheet — Chatbot AI chốt đơn & CSKH cho shop online SMB

Họ tên: Ngô Lê Thuỷ Tiên · MSSV: 2A202602614 · Ngày làm: 09/10/2026

Nguồn số liệu: `day22_monetization_model.xlsx` và `day22_one_pager_template.docx` (Lab Day 22). Số nào là **ước tính từ mô hình, chưa đo thật** đều ghi rõ. Hiện chưa có shop nào trả tiền.

## Trạm 1 — Loại mô hình

**Câu chốt loại:** Chúng tôi là **B2B** vì tiền đến từ chủ shop online SMB (trả $0,45 mỗi hội thoại xong), người dùng sản phẩm chính là chủ shop (họ cài app, cấu hình, duyệt ca chuyển người và trả tiền), còn khách mua của shop chỉ nhắn với bot dưới tên shop: chúng tôi không có thương hiệu trước mặt họ và không sở hữu quan hệ với họ. Haravan App Store chỉ là kênh phân phối (marketplace, chia 20% doanh thu mỗi job), không phải bên trả tiền.

**Ba câu hỏi (thực tế hôm nay):**
1. Ai trả tiền? Doanh nghiệp: chủ shop SMB.
2. Ai dùng? Chính người trả tiền. Khách mua của shop chỉ là người nhận câu trả lời.
3. Chạm end-user qua đâu? Bot chạy trong Messenger/Zalo OA của shop dưới tên shop, nên không có thương hiệu của chúng tôi trước mặt người mua. Haravan chỉ phân phối, nên không tạo ra B2B2C.

**Bảng đèn §3.2 B2B** (✅ đo được hôm nay · 🔧 đo được trong 2 tuần · ❌ chưa biết cách). Khách = một shop.

| Đèn | ✅ / 🔧 / ❌ | Số nằm ở đâu / cần gì để đo |
|---|---|---|
| Time-to-first-value (TTFV) | 🔧 | Cần event `app_installed` và `first_real_resolved_conversation` gắn shop_id. Chưa có shop nào, đo từ 5 shop pilot. |
| Pipeline coverage | 🔧 | Cần một bảng theo dõi shop (từ 15 cuộc phỏng vấn ở Day 22). Chưa có win rate thật để tính coverage cần thiết. |
| % deal chết ở khâu security/procurement | 🔧 | Cần ghi lý do mỗi shop từ chối. Risk Checklist (hallucinate, dữ liệu, startup chết thì data ở đâu) hạn 29/10/2026. |
| POC → paid | 🔧 | Cần 5 shop pilot và log shop nào chuyển sang trả tiền. Có số sau 30/11/2026. |
| Sales cycle (tuần) | 🔧 | Cần timestamp từ lúc shop đủ điều kiện đến lúc đồng ý trả tiền. Chưa có shop nào. |
| Usage depth trong tài khoản | 🔧 | Log số job xong theo shop_id. Mô hình giả định 400 job/shop/tháng (`4_Channel_Fit!B5`), chưa đo thật. |
| Chi phí triển khai ÷ ACV | 🔧 | ACV = $180 × 12 = $2.160 (✅ từ mô hình). Cần ghi giờ onboarding mỗi shop và đơn giá giờ công. |
| Tập trung doanh thu | ❌ | 0 khách trả tiền. Chỉ có nghĩa khi ≥10 shop trả tiền. |
| NRR | ❌ | 0 khách trả tiền, chưa có kỳ gia hạn. |
| Gross Margin | ✅ | `2_Pricing!B21` = 76,7% trước phí kênh; sau phí Haravan 20% là 56,7% (mô hình, chưa gồm overhead). Xem Phụ lục [MH] 2. |
| CAC payback | 🔧 | Ngân sách CAC $1.657 (`4_Channel_Fit!B9`) là số mô hình. CAC thực cần chi phí pilot và onboarding thật. |

## Trạm 2 — Thẻ đèn

**North Star:** Time-to-first-value (TTFV) — hiện tại chưa đo (0 shop) — mục tiêu trung vị ≤7 ngày trên 5 shop pilot trước 08/12/2026.

**Định nghĩa dùng chung — hội thoại "xong":** hội thoại của người mua thật mà bot trả lời hết câu hỏi (hoặc chốt đơn) và trong 24 giờ sau đó người mua **không** nhắn lại để hỏi lại cùng vấn đề, **không** đòi gặp người, và chủ shop **không** tiếp quản. Bot gửi câu trả lời cuối nhưng người mua quay lại hỏi lại thì **chưa** tính là xong. Mọi đèn dưới đây dùng đúng định nghĩa này.

| # | Tầng (L/O/G) | Đèn | Định nghĩa (đếm gì · **không** đếm gì) | Công thức | Nhịp · ai lấy số | Báo trước cho |
|---|---|---|---|---|---|---|
| 1 | L | **TTFV** ⭐ | Số ngày từ ngày cài app đến hội thoại **của người mua thật** đầu tiên bot xử lý xong và không bị chuyển người. **Không** tính hội thoại test của chủ shop, **không** tính ngày kết nối kênh chưa xong; shop chưa có hội thoại thật nào tính là chưa đạt. | Ngày(hội thoại thật xong đầu tiên) − Ngày(cài app), lấy trung vị các shop | Mỗi shop · Tiên | POC→paid (đèn 5) và churn shop (đèn 8) |
| 2 | L | **Containment rate** | Đếm hội thoại bot tự xử lý xong và được chấm "đúng" khi eval. **Không** đếm hội thoại bot trả lời sai; **không** đếm hội thoại chuyển người. | Hội thoại bot xử lý đúng ÷ tổng hội thoại, trên 200 hội thoại gần nhất của mỗi shop | Hằng tuần · Tiên | Gross Margin (đèn 7), churn shop (đèn 8) |
| 3 | L | **Cost/Job (AI)** — đèn chi phí AI | Chi phí AI (token vào/ra, retry, chi phí kênh) chia cho số hội thoại xong. **Không** chia cho tổng hội thoại, vì job thất bại vẫn tốn token. Xem **theo từng shop**, lấy shop xấu nhất. | Tổng chi phí AI trong kỳ ÷ số job xong | Hằng tuần · Tiên (từ log token) | Gross Margin (đèn 7) |
| 4 | O | **Usage depth (job xong/shop/tháng)** | Số hội thoại bot xử lý xong của một shop trong 30 ngày. **Không** tính hội thoại test, **không** tính hội thoại chuyển người. | Đếm job xong theo shop_id, trung vị các shop trả tiền | Hằng tuần · Tiên | Doanh thu/shop → CAC payback (đèn 6), churn (đèn 8) |
| 5 | O | **POC → paid** | % shop pilot chuyển thành trả tiền ngay sau pilot. **Không** tính shop trả tiền mà không qua pilot, **không** tính shop dùng thử miễn phí kéo dài. | Số shop pilot trả tiền ÷ số shop pilot | Hằng tháng · Tiên | Doanh thu và CAC payback (đèn 6) |
| 6 | G | **CAC payback (tháng)** | Số tháng để lãi gộp một shop (sau phí kênh) bù CAC đã tiêu cho shop đó. CAC gồm công onboarding và chi phí chạy pilot; **không** gồm lương của tôi (đang bằng 0, ghi ở mục Chưa đo được). | CAC thực ÷ (ARPU × GM sau phí kênh) | Hằng quý · Tiên | Không báo trước đèn nào (đèn kết quả). Dùng để quyết định có tăng chi tiêu pilot/onboarding hay không |
| 7 | G | **Gross Margin sau phí kênh** | (Doanh thu − phí Haravan 20% − COGS) ÷ doanh thu mỗi job. **Không** gồm overhead nhân sự (mô hình để S8 = 0). | (0,45 − 0,09 − Cost/Job) ÷ 0,45 | Hằng tháng · Tiên | Không báo trước đèn nào (đèn kết quả). Dùng để quyết định có đàm phán lại phí 20% với Haravan hay không |
| 8 | G | **Churn shop hằng tháng** | Số shop trả tiền ngừng dùng hoặc gỡ app trong tháng ÷ số shop trả tiền đầu tháng. **Không** tính shop pilot miễn phí. | Shop rời ÷ shop đầu tháng | Hằng tháng · Tiên | Không báo trước đèn nào (đèn kết quả). Dùng để kiểm tra lại giả định "shop ở lại ≥12 tháng" của mô hình Day 22 |

Cơ cấu: 3 Leading · 2 Operating · 3 Lagging (37,5% Lagging, thấp hơn mức 50%).

**Đèn chi phí AI là đèn số 3 (Cost/Job).** Nó bắt chi phí AI tăng ngay trong tuần, trước khi GM tháng (đèn 7) kịp đổi. Giá tính theo job ($0,45) nên **tôi gánh 100% hoá đơn token**, không pass-through cho shop; shop dùng nhiều thì chi phí của tôi tăng theo mà doanh thu chỉ tăng khi job xong. Đèn này đo theo từng shop và lấy shop xấu nhất, vì với 5 shop pilot chưa đủ mẫu để tính p95; khi có ≥20 shop sẽ đổi sang p95.

## Trạm 3 — Ngưỡng

Ngày làm bài: 09/10/2026. Ngày kiểm tra các benchmark [BM]: **09/10/2026**, đã mở lại nguồn gốc:
- ICONIQ *State of AI 2026* (iconiq.com/growth/reports/state-of-ai-2026): GM 45% (2025) → 53% (2026P) → 59% (2027P). Đã xác nhận.
- ICONIQ *State of Go-to-Market 2026* (iconiq.com/growth/reports/state-of-go-to-market-2026): POC/free-trial → paid ~50% (2026), ~36% (2025). Đã xác nhận; trang không ghi ngày xuất bản.
- CAC payback SMB <12 · mid-market <18 · enterprise <24 tháng: **chỉ xác nhận qua nguồn thứ cấp** (nhiều bài tổng hợp gán cho Bessemer, các bài khác cho David Skok *SaaS Metrics 2.0*), chưa mở được bản gốc. Dùng như quy ước ngành, không như số khảo sát.

| # | Đèn | 🟢 | 🟡 | 🔴 | Nguồn | Lý do (1 câu) · ngày kiểm tra nếu [BM] |
|---|---|---|---|---|---|---|
| 1 | TTFV | ≤7 ngày | 8–30 ngày | >30 ngày | [TB] | Chưa có baseline vì chưa có shop; mốc 30/60 ngày của HANDBOOK §3.2 là cho khách doanh nghiệp, còn shop cài app từ Haravan không cần ký hợp đồng nên tôi siết xanh còn 7 ngày và đỏ còn 30 ngày; đo 5 pilot (có số ~08/12/2026) rồi chỉnh. |
| 2 | Containment rate | ≥80% | 69,1–80% | <69,1% | [MH] | Dưới 69,1% là dưới breakeven containment của mô hình Day 22; 80% là KPI pilot trong 90-Day Plan. Phép tính ở Phụ lục [MH] 1. |
| 3 | Cost/Job (AI) | ≤$0,1125 | $0,1125–0,1575 | >$0,1575 | [MH] | Suy ngược từ GM sau phí kênh 55% / 45%. Phép tính ở Phụ lục [MH] 2. |
| 4 | Usage depth | ≥400 job/tháng | 267–400 | <267 | [MH] | 400 job/tháng là mức ARPU $180 của mô hình; dưới 267 job thì payback vượt 18 tháng. Phép tính ở Phụ lục [MH] 3. |
| 5 | POC → paid | ≥50% (≥3/5 shop) | 35–50% (2/5) | <35% (≤1/5) | [BM] | ICONIQ State of Go-to-Market 2026: POC→paid ~50% (2026), ~36% (2025), kiểm tra 09/10/2026; số này là của AI doanh nghiệp, SMB có thể khác nên chỉ là điểm bắt đầu. Với 5 shop, 3/5 = 60%, 2/5 = 40%, nên các ngưỡng khớp số nguyên. |
| 6 | CAC payback | ≤12 tháng | 12–18 tháng | >18 tháng | [BM] + [MH] | SMB cần <12 tháng (quy ước Bessemer/Skok, kiểm tra 09/10/2026 qua nguồn thứ cấp, chưa mở bản gốc) và Day 22 đặt payback tối đa 12 tháng; 18 tháng là mốc mid-market, chỉ chấp nhận trong lúc đang FIX một lần. Phép tính ở Phụ lục [MH] 3. |
| 7 | Gross Margin sau phí kênh | ≥55% | 45–55% | <45% | [BM] + [MH] | Mốc 45% là GM AI-native năm 2025 và 53% là 2026P (ICONIQ State of AI 2026, 07/2026, kiểm tra 09/10/2026); mô hình hiện cho 56,7% nên chỉ hơn mốc xanh 1,7 điểm. Phép tính ở Phụ lục [MH] 2. |
| 8 | Churn shop hằng tháng | <5,6% | 5,6–8,3% | >8,3% | [MH] | Kỳ vọng sống của shop = 1 ÷ churn phải ≥ payback 12 tháng, xanh khi có dư 1,5×. Phép tính ở Phụ lục [MH] 4. |

### Phụ lục [MH] — phép tính

**[MH] 1 — Containment rate**

```
Đầu vào (Day 22): Giá $0,45/job · Cost/Job $0,1047 (2_Pricing!B5)
                  Breakeven containment = 69,1% (2_Pricing!B33)
                  GM tụt <50% khi containment <~63% (2_Pricing!B39:C44)
                  KPI pilot tháng 2–3: containment ≥80% (5_90Day_Plan)
Phép tính: Sàn tuyệt đối = 63%, vì dưới mức này mô hình mất biên.
           Đèn đỏ đặt sớm hơn sàn, ở mức breakeven 69,1% (để còn thời gian phản ứng).
           Đèn xanh = KPI pilot 80%.
Kết quả → 🟢 ≥80% · 🟡 69,1–80% · 🔴 <69,1% (Luật 2 kích hoạt tại đúng mức này; 63% là sàn mà dưới đó GM <50%, dùng ở cổng ngày 30 và kill criteria)
Hiện tại: 82% là ước tính, chưa có eval → ⚪ chưa đo.
```

**[MH] 2 — Cost/Job (AI) và Gross Margin sau phí kênh**

```
Đầu vào: Giá $0,45/job · Phí kênh Haravan 20% = $0,09/job (tính như COGS cho chặt)
         Doanh thu giữ lại = 0,45 − 0,09 = $0,36/job · Cost/Job $0,1047 (2_Pricing!B5)
         Mục tiêu GM: 55% (xanh), 45% (đỏ), theo HANDBOOK §3.2 (AI-native 45% → 53%)
Phép tính: GM = (0,36 − Cost) ÷ 0,45
           GM = 55% → Cost = 0,36 − 0,2475 = $0,1125
           GM = 45% → Cost = 0,36 − 0,2025 = $0,1575
           GM hiện tại = (0,36 − 0,1047) ÷ 0,45 = 0,2553 ÷ 0,45 = 56,7%
           (GM trước phí kênh = (0,45 − 0,1047) ÷ 0,45 = 76,7%, khớp 2_Pricing!B21)
Kết quả → Cost/Job 🟢 ≤$0,1125 · 🟡 $0,1125–0,1575 · 🔴 >$0,1575
          GM       🟢 ≥55% · 🟡 45–55% · 🔴 <45%
Hiện tại: Cost/Job $0,1047 (mô hình) → 🟢, nhưng dư địa chỉ 0,1125 ÷ 0,1047 = 1,07×, tức chỉ được tăng ~7%.
          GM 56,7% (chưa gồm overhead) → 🟢 sát mốc.
```

**[MH] 3 — Usage depth và CAC payback**

```
Đầu vào: ARPU $180/tháng = 400 job × $0,45 (4_Channel_Fit!B5)
         GM sau phí kênh 56,7% (Phụ lục 2) · Payback tối đa 12 tháng SMB (4_Channel_Fit!B8)
Phép tính: Lãi gộp/shop/tháng = 180 × 56,7% = $102,1
           CAC tối đa để payback 12 tháng = 102,1 × 12 ≈ $1.225
           (Day 22 cho $1.657 vì dùng GM 76,7% trước phí kênh.)
           Payback 18 tháng → lãi gộp/tháng cần = 1.225 ÷ 18 = $68,1
           → ARPU cần = 68,1 ÷ 56,7% = $120 → job/tháng = 120 ÷ 0,45 = 267
Kết quả → Usage depth 🟢 ≥400 · 🟡 267–400 · 🔴 <267 job/tháng
          CAC phải ≤$1.225 để payback ≤12 tháng.
```

**[MH] 4 — Churn shop**

```
Đầu vào: Payback tối đa 12 tháng · GM sau phí kênh 56,7%
Phép tính: Kỳ vọng sống = 1 ÷ churn tháng. Cần ≥ 12 tháng → churn ≤ 1 ÷ 12 = 8,3% (ngưỡng đỏ).
           Xanh khi sống kỳ vọng ≥ 1,5 × 12 = 18 tháng → churn ≤ 1 ÷ 18 = 5,6%.
           (KPI churn <5% của Day 22 chặt hơn mốc xanh này một chút.)
Kết quả → 🟢 <5,6% · 🟡 5,6–8,3% · 🔴 >8,3%
```

## Trạm 4 — 5 luật quyết định

⏹ = luật dừng (3 luật: 1, 2, 5).

1. ⏹ **NẾU** TTFV >30 ngày **TRÊN** 3 shop cài gần nhất (shop sau 30 ngày kể từ lúc cài vẫn chưa có hội thoại thật nào xong thì tính là TTFV >30 ngày) **THÌ** Tiên bắt đầu trong 48 giờ: dừng nhận shop mới, cắt onboarding về 1 kênh (Messenger hoặc Zalo OA, không cả hai) và 1 luồng (hỏi tồn kho), cho đến khi 3 shop liên tiếp có TTFV dưới 7 ngày **KHÔNG THÌ** không đưa thêm shop vào phễu và không viết thêm tính năng mới, vì thêm shop vào một cửa cài đặt đang tắc chỉ làm tắc nặng hơn.

2. ⏹ **NẾU** Containment rate <69,1% (đèn đỏ, dưới breakeven của mô hình) **TRÊN** 200 hội thoại gần nhất của một shop **THÌ** Tiên tắt ngay trong ngày chế độ tự chốt đơn ở shop đó, chuyển sang chế độ bot chỉ soạn nháp và chủ shop bấm gửi, cho đến khi eval lại ≥69,1% **KHÔNG THÌ** không hạ ngưỡng chuyển sang người thật để containment nhìn đẹp hơn (bài học Klarna: ép chi phí làm hỏng chất lượng).

3. **NẾU** Cost/Job của một shop >$0,1575 **TRONG** 2 tuần liên tiếp **VÀ** shop đó có ≥90 job mỗi tuần (≈400 job/tháng ÷ 4,3, mức ARPU của mô hình, để tuần ít job không làm đèn nhiễu) **THÌ** Tiên bắt đầu trong 48 giờ, hoàn thành trong sprint tới: chuyển luồng tốn token nhất sang model rẻ hơn hoặc cắt độ dài ngữ cảnh, rồi đo lại Cost/Job của shop đó sau 1 tuần **KHÔNG THÌ** không tăng giá $0,45 đại trà để bù chi phí, vì giá đã neo vào lương 1 người trực $450/tháng.

4. **NẾU** Usage depth của một shop <267 job/tháng **SAU** 60 ngày kể từ ngày go-live **THÌ** Tiên gọi chủ shop trong 1 tuần, tìm một luồng bot chưa chạy (ví dụ phí ship) và sửa nó trước khi nói chuyện gia hạn **KHÔNG THÌ** không mời shop đó mở thêm kênh Zalo hay thêm ngành, vì mở rộng vào một tài khoản chưa dùng hết là cách nhanh nhất mất luôn khách.

5. ⏹ **NẾU** POC→paid <35% (≤1/5 shop trả tiền) **TRÊN** 5 shop pilot đầu tiên **THÌ** Tiên dừng nhận pilot mới ngay hôm đó, và trong 2 tuần phỏng vấn lại cả 5 shop để ghi lý do không trả tiền **KHÔNG THÌ** không giảm giá $0,45 để kéo shop trả tiền: shop bỏ vì chưa thấy giá trị, không phải vì giá.
