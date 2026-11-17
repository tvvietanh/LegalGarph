# HierarGraph — Bàn giao dữ liệu đồ thị pháp luật lao động Việt Nam

**Mục đích:** Bộ dữ liệu rút gọn để thành viên trong nhóm **nạp, quan sát và kiểm thử cấu trúc ba tầng** (Rule, Ontology, Fact)

## 1. Cấu trúc bộ bàn giao

```text
HierarGraph/
├── rule_graph/                  # M1 — Quy định pháp luật
│   ├── nodes.jsonl
│   ├── edges.jsonl
│   ├── manifest.json
│   └── quality_report.json
├── ontology_graph/              # M2 — Khái niệm pháp lý
│   ├── nodes.jsonl
│   ├── edges.jsonl
│   ├── manifest.json
│   ├── quality_report.json
│   └── evidence.jsonl
├── fact_graph/                  # M3 — Hồ sơ vụ việc
│   ├── nodes.jsonl
│   ├── edges.jsonl
│   ├── manifest.json
│   └── quality_report.json
└── ReadmeHiergraph.md           # File đang đọc
```

**Tổng cộng:** 13 file dữ liệu JSON/JSONL + 1 file Markdown (14 file). Giữ **nguyên tên file**, đặt vào đúng thư mục; không tự sửa ID hoặc nội dung bản ghi.

## 2. Nội dung và nhiệm vụ từng tầng

### M1 — Rule Graph (tầng quy định pháp luật)

**Nhiệm vụ:** Tổ chức các văn bản pháp luật lao động thành đồ thị có cấu trúc để hệ thống có thể xác định văn bản, Điều, Khoản, Điểm, định danh và phiên bản của quy định.

- **Loại node đã xuất:** `Document` (39), `Provision` (3.934), `ProvisionIdentity` (2.697), `ProvisionVersion` (3.934). **ARTICLE/CLAUSE/POINT là cấp của `Provision`, không phải ba node type độc lập.**
- **Loại edge đã xuất:** `CONTAINS` (3.934), `HAS_IDENTITY` (3.934), `HAS_VERSION` (3.934).
- **Quy mô:** **10.604 nodes / 11.802 edges**.
- **Giới hạn:** Chưa công bố quan hệ sửa đổi/bãi bỏ/viện dẫn pháp lý được xác minh; chưa xác minh khả năng áp dụng điều khoản theo thời điểm. Việc tồn tại node phiên bản không có nghĩa hiệu lực pháp lý đã được xác thực.

| File | Nhiệm vụ |
|---|---|
| `nodes.jsonl` | Các đối tượng văn bản, quy định, định danh và phiên bản; giữ thuộc tính và tham chiếu nguồn. |
| `edges.jsonl` | Các cạnh cấu trúc nối văn bản với quy định, quy định với định danh/phiên bản. |
| `manifest.json` | Thông tin build, số lượng node/edge, schema, hash artifact và dấu vết đầu vào. |
| `quality_report.json` | Báo cáo kiểm tra cấu trúc, bản ghi được xuất/bị loại, giới hạn, dữ liệu cần rà soát. |

### M2 — Ontology Graph (tầng khái niệm pháp lý)

**Nhiệm vụ:** Chuẩn hóa một số **khái niệm pháp luật lao động** và liên hệ khái niệm đó với định nghĩa trong điều luật. Ví dụ: “hợp đồng lao động xác định thời hạn” thuộc nhóm “hợp đồng lao động”.

- **Loại node đã xuất:** `LegalConcept` (10 khái niệm được duyệt), `LegalIssue` (17 chủ đề/từ vựng nguồn; **chưa phải khái niệm định nghĩa đầy đủ**).
- **Loại edge đã xuất:** `IS_A` (2 cạnh phân loại trong Ontology) và `REGULATED_BY` (10 cạnh **từ M2 sang các Provision thuộc M1**, dùng để chỉ nguồn định nghĩa).
- **Quy mô:** **27 nodes / 12 edges**, trong đó **2 cạnh nội tầng và 10 cạnh liên tầng**.
- **Giới hạn:** Chỉ là ontology được tuyển chọn ở phạm vi nhỏ, không bao quát toàn bộ luật lao động. Liên kết `REGULATED_BY` ở đây biểu thị **nguồn định nghĩa pháp lý**, **không chứng minh quy định đang có hiệu lực hoặc áp dụng cho một vụ việc cụ thể**. Không có cạnh `RELATED_TO` được xuất bản trong bộ này.

| File | Nhiệm vụ |
|---|---|
| `nodes.jsonl` | Các khái niệm/chủ đề pháp lý và thuộc tính liên quan. |
| `edges.jsonl` | Quan hệ `IS_A` giữa khái niệm và `REGULATED_BY` trỏ sang node quy định ở M1. |
| `manifest.json` | Thông tin build, catalog/approval, hash artifact và danh sách tham chiếu node bên ngoài (Rule). |
| `quality_report.json` | Thống kê độ bao phủ, phê duyệt ngữ nghĩa, kiểm tra quan hệ và giới hạn. |
| **`evidence.jsonl`** | **Bằng chứng nguồn** cho các khái niệm/cạnh đã duyệt: trích đoạn nguyên văn, node quy định, vị trí văn bản, hash và provenance. Dùng khi kiểm thử tính có căn cứ của Ontology. |

### M3 — Fact Graph (tầng hồ sơ vụ việc)

**Nhiệm vụ:** Quản lý các **định danh hồ sơ vụ việc** và tham chiếu đến tài liệu nguồn làm tiền đề để nghiên cứu hoặc mở rộng trích xuất sự kiện sau này.

- **Loại node đã xuất:** `Case` (24).
- **Số cạnh:** **0**; `edges.jsonl` **rỗng, đây là trạng thái có chủ đích, không phải lỗi đóng gói**.
- **Dữ liệu đầu vào:** 36 hồ sơ, trong đó 24 hồ sơ đủ điều kiện xuất thành node và 12 hồ sơ bị loại theo chính sách nguồn.
- **Giới hạn:** **Chưa có** `Actor`, `Event`, `FactAssertion`, kết quả xét xử được xác minh hoặc liên kết ngữ nghĩa Case → Rule/Ontology. Không được coi phần mô tả vụ việc chưa duyệt là sự thật đã được tòa án xác nhận.

| File | Nhiệm vụ |
|---|---|
| `nodes.jsonl` | Các định danh Case và thông tin tham chiếu nguồn đã được phép xuất. |
| `edges.jsonl` | File cạnh chuẩn để chương trình nạp graph; **hiện rỗng (0 byte)**. |
| `manifest.json` | Thông tin build, hash artifact, số lượng hồ sơ, tham chiếu document của Rule Graph. |
| `quality_report.json` | Thể hiện phạm vi Fact Graph: 24 Case, 0 fact edges, các hồ sơ bị loại và giới hạn xác minh. |

## 3. Quy ước dữ liệu và cách đọc

- `.jsonl` = **JSON Lines**: mỗi dòng không rỗng là **một đối tượng JSON**, không phải một mảng JSON bao ngoài. Đọc **UTF-8** theo từng dòng.
- `nodes.jsonl`: trường cốt lõi gồm `id`, `type`, `layer`, `properties`.
- `edges.jsonl`: trường cốt lõi gồm `id`, `source_id`, `target_id`, `type`, `layer`, `properties`.
- ID có namespace như `rule:...`, `ontology:...`, `fact:...`. **Không thay đổi ID, không loại trùng theo nhãn tên.**
- Khi kiểm tra các cạnh M2 `REGULATED_BY`, **phải nạp node M1 cùng lúc**. Nếu chỉ nạp M2, một số `target_id` hợp lệ sẽ có vẻ như không tồn tại vì đích nằm ở tầng Rule.
- M3 không có cạnh, vì vậy không nên kết luận file bị lỗi hoặc tự tạo cạnh giả.

**Ví dụ kiểm tra nhanh bằng Python 3 (đặt terminal tại thư mục `HierarGraph/`):**

```python
import json
from pathlib import Path

root = Path('.')
all_nodes = {}
all_edges = []
for layer in ('rule_graph', 'ontology_graph', 'fact_graph'):
    folder = root / layer
    nodes = [json.loads(line) for line in (folder / 'nodes.jsonl').read_text(encoding='utf-8').splitlines() if line.strip()]
    edges = [json.loads(line) for line in (folder / 'edges.jsonl').read_text(encoding='utf-8').splitlines() if line.strip()]
    print(f'{layer}: {len(nodes)} nodes, {len(edges)} edges')
    for node in nodes:
        assert node['id'] not in all_nodes, f"Duplicate node: {node['id']}"
        all_nodes[node['id']] = node
    all_edges.extend(edges)

missing = [(e['id'], e['source_id'], e['target_id'])
           for e in all_edges
           if e['source_id'] not in all_nodes or e['target_id'] not in all_nodes]
print('Missing edge endpoints:', len(missing))
assert not missing, missing[:5]
```

**Kết quả dự kiến:** `rule_graph: 10604 nodes, 11802 edges`; `ontology_graph: 27 nodes, 12 edges`; `fact_graph: 24 nodes, 0 edges`; `Missing edge endpoints: 0` khi nạp đủ ba tầng và không sửa các file.

## 4. Kiểm thử nên thực hiện

1. **Đọc tệp:** Parse JSONL không lỗi; tổng số node/edge khớp bảng quy mô ở trên.
2. **Tính toàn vẹn:** ID node không trùng; mọi `source_id`/`target_id` của cạnh tìm thấy trong **tập node của cả ba tầng**.
3. **Kiểm tra M1:** Có các loại node/edge như mô tả và phân cấp Điều–Khoản–Điểm không bị đảo lộn.
4. **Kiểm tra M2:** Có 2 `IS_A`, 10 `REGULATED_BY`; đối chiếu một vài khái niệm với trích dẫn trong `evidence.jsonl`.
5. **Kiểm tra M3:** Có đúng 24 `Case`, file cạnh rỗng; không suy diễn tình tiết hoặc phán quyết chưa được duyệt.
6. **Kiểm tra nguồn:** Đọc `quality_report.json` để nắm dữ liệu loại trừ và trạng thái chưa xác minh trước khi dùng kết quả trả lời câu hỏi pháp luật.

## 5. Giới hạn của gói rút gọn

- Gói này phục vụ **nạp và kiểm thử cấu trúc ba tầng, kiểm thử ngữ nghĩa M2 ở mức có bằng chứng**; **không đủ** để tái dựng toàn bộ pipeline M0–M5 hay xác minh toàn bộ 219 file dữ liệu đầu vào.
- Các `manifest.json` **giữ nguyên từ bản gốc** nên có thể liệt kê thêm `candidates.jsonl`, `rejects.jsonl`, `review_queue.jsonl`... không nằm trong gói. Đó **không phải lỗi thiếu file** nếu mục tiêu chỉ là kiểm thử graph rút gọn; nhưng **không thể xác nhận toàn bộ artifact hash từ gói này**.
- **Không có đầy đủ thông tin kiểm tra URL hay hiệu lực thời gian** trong bộ M1–M3 rút gọn. Nếu làm hệ thống RAG cần nguồn URL đã đối chiếu, bằng chứng và truy vấn liên tầng, nên lấy thêm từ bản tích hợp `outputs/final/` trong repository gốc, đặc biệt `url_audit.jsonl`, `provenance.jsonl`, `traversal_examples.json` và `docs/HANDOFF.md`.
- Việc có các node/cạnh được xuất bản **không đồng nghĩa** mọi quy định hiện còn hiệu lực hoặc luôn áp dụng cho mọi trường hợp.

## 6. Nguồn và phiên bản


**Tóm tắt:** `nodes.jsonl` + `edges.jsonl` là **đồ thị**; `manifest.json` là **phiếu nhận diện bản build**; `quality_report.json` là **phiếu kiểm tra chất lượng**; riêng `ontology_graph/evidence.jsonl` là **bằng chứng liên kết khái niệm với văn bản luật**.
