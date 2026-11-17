# Chi tiết dữ liệu Vietnamese Labor Law

Ngày lập tài liệu: **29/10/2026**. Tài liệu mô tả các tệp thực tế trong `Data/` tại thời điểm kiểm kê. Các số liệu về kiểm chứng là trạng thái lưu trong dữ liệu, không phải kết quả truy cập lại website trong lần lập tài liệu này.

Mục đích của bộ dữ liệu là lưu nguồn văn bản pháp luật, bản án và án lệ liên quan đến nghiên cứu pháp luật lao động Việt Nam; kèm văn bản đã trích xuất, cấu trúc điều khoản, metadata, thông tin nguồn và các kết quả nghiên cứu kế thừa. Phạm vi có cả tài liệu bổ trợ, tài liệu lịch sử và hồ sơ đang chờ duyệt. Vì vậy, không nên coi mọi tệp đều là pháp luật lao động hiện hành hoặc dữ liệu đã được chấp thuận sử dụng.

Đi nhanh: [Cách bắt đầu](#bat-dau) · [Quy mô](#quy-mo) · [Nguồn gốc](#raw) · [Trích xuất](#processed) · [Bản án/án lệ](#cases) · [Metadata](#metadata) · [Truy vết](#provenance) · [Thời gian](#temporal) · [Nghiên cứu bổ sung](#extensions) · [URL](#urls) · [Cách nối dữ liệu](#joins) · [Hạn chế](#limitations) · [Danh sách hồ sơ và tệp](#inventory).

<a id="bat-dau"></a>

## 1. Đọc bộ dữ liệu từ đâu?

| Nhu cầu | Tệp/thư mục nên mở | Cách hiểu |
|---|---|---|
| Xem danh sách hồ sơ, số hiệu, tên, loại và trạng thái | [04_metadata/documents.jsonl](Data/04_metadata/documents.jsonl) | Danh mục trung tâm, 107 hồ sơ |
| Đọc toàn văn đã trích xuất | [02_processed_data/documents.jsonl](Data/02_processed_data/documents.jsonl) | Trường `text`; đủ 107 hồ sơ, kể cả một số hồ sơ chưa có tệp gốc tại chỗ |
| Mở PDF/DOCX nguồn | [01_raw_data](Data/01_raw_data/) | Tra `local_file_path` từ metadata; không suy tên văn bản từ tên tệp băm |
| Đọc từng điều, khoản, điểm | [02_processed_data/provisions.jsonl](Data/02_processed_data/provisions.jsonl) | Cấu trúc phân cấp, liên kết về hồ sơ bằng `document_id` |
| Xem bản án, án lệ và toàn văn vụ việc | [03_case_data/cases.jsonl](Data/03_case_data/cases.jsonl) | 36 hồ sơ tư pháp; các phần phân tích vụ việc chưa được duyệt toàn bộ |
| Xem tất cả liên kết theo văn bản | [08_Url/url.csv](Data/08_Url/url.csv) | Bảng dễ mở bằng Excel; có cả URL hiện tại, URL cũ và URL chưa xác minh |
| Tra bằng chứng kiểm tra URL | [08_Url/url_verification.jsonl](Data/08_Url/url_verification.jsonl) | Nhật ký nhiều lần kiểm tra, không phải một dòng cho mỗi hồ sơ |
| Truy một đoạn trích về trang nguồn | [05_provenance](Data/05_provenance/) | Vị trí ký tự, trang, ảnh xem trước và kiểm tra chất lượng trang |
| Xem phiên bản và thay đổi pháp lý dự kiến | [06_temporal](Data/06_temporal/) | Dữ liệu thời gian chưa đủ cơ sở để khẳng định hiệu lực theo từng điều khoản |
| Xem dữ liệu bổ sung và kết quả nghiên cứu trước đây | [07_research_extensions](Data/07_research_extensions/) | Phải phân biệt dữ liệu kế thừa với dữ liệu đã được duyệt hiện tại |

Đường dẫn trong tài liệu Markdown này tính từ thư mục gốc `artifacts/`. Ngược lại, nhiều trường `local_file_path` trong JSON tính từ `Data/`, ví dụ `01_raw_data/...` phải mở thành `Data/01_raw_data/...` khi đứng ở thư mục gốc.

<a id="quy-mo"></a>

## 2. Quy mô và cách đếm

| Chỉ tiêu | Số lượng | Ý nghĩa |
|---|---:|---|
| Hồ sơ văn bản | 107 | Theo `document_id` trong metadata |
| Nhóm `LEGAL_DOCUMENT` | 57 | Nhãn nhóm nguồn, không phải xác nhận toàn bộ đang có hiệu lực |
| Nhóm `CONSOLIDATED` | 4 | Văn bản hợp nhất; không cộng như bốn đạo luật mới độc lập |
| Nhóm `JUDICIAL` | 36 | 33 hồ sơ bản án/quyết định, gồm một vụ án nguồn của án lệ; 3 án lệ chính thức |
| Nhóm `SUPPLEMENTARY` | 10 | Tài liệu bổ trợ |
| Hồ sơ có tệp nguồn tại chỗ | 84 | `local_source_status = AVAILABLE` |
| Hồ sơ chưa có tệp nguồn tại chỗ | 23 | `SOURCE_MISSING`; bản trích xuất vẫn được giữ |
| Tệp vật lý trong `01_raw_data` | 85 | Có một bản sao nguồn bổ sung; số tệp không bằng số hồ sơ |
| Hồ sơ có trạng thái URL `VERIFIED` | 95 | Theo trạng thái hiện có trong metadata |
| Hồ sơ có trạng thái URL `REVIEW_REQUIRED` | 12 | Chưa đủ bằng chứng URL/định danh/nội dung |
| Hồ sơ `graph_status = ACTIVE` | 39 | Chỉ được sử dụng theo phạm vi `graph_import_policy` của từng hồ sơ |
| Hồ sơ `graph_status = QUARANTINE` | 68 | Giữ nguyên dữ liệu, chưa đưa vào tập sử dụng chính thức |
| Văn bản trích xuất | 107 | `02_processed_data/documents.jsonl` |
| Đoạn/phân vùng văn bản | 512 | `segments.jsonl` |
| Tiêu đề phân cấp | 562 | `hierarchy_headings.jsonl` |
| Điều/khoản/điểm đã lưu trong tập cấu trúc | 18.871 | `provisions.jsonl`; không đồng nghĩa tất cả được phép đưa vào đồ thị |
| Ứng viên điều khoản cách ly | 896 | `quarantined_provisions.jsonl`, đọc riêng |
| Bản ghi nguồn gốc điều khoản | 18.871 | `provision_spans.jsonl` |
| Bản ghi kiểm tra trang | 3.116 | Không phải số trang của toàn bộ 107 hồ sơ hiện tại |
| Định danh điều khoản qua phiên bản | 15.651 | `provision_identities.jsonl` |
| Phiên bản điều khoản | 18.871 | Đều chưa được xác minh hiệu lực pháp lý theo từng phiên bản |
| Ứng viên thay đổi pháp lý | 25 | Cần thẩm định; không phải 25 thay đổi đã được công nhận |
| Dòng trong bảng URL | 367 | 365 cặp hồ sơ–URL không rỗng và 2 dòng URL trống |
| Chuỗi URL không rỗng khác nhau | 300 | Một URL có thể xuất hiện ở nhiều hồ sơ |

Toàn bộ `Data/` hiện có **421 tệp**, tổng **374.977.620 byte**, khoảng **357,61 MiB**. Có 84 PDF, 5 DOCX, 270 PNG, 40 JSONL, 19 JSON, 2 CSV và 1 HTML. Bốn DOCX trong `converted_docx/` là bản chuyển đổi; chúng không làm tăng số hồ sơ nguồn gốc.

<a id="raw"></a>

## 3. `01_raw_data/` — tệp nguồn

Thư mục lưu bản PDF hoặc DOCX để người đọc mở và đối chiếu trực tiếp. Đây là lớp cần giữ nếu muốn kiểm tra lại văn bản trích xuất với nguồn. Tên nhiều tệp là mã SHA-256, phục vụ nhận diện nội dung tệp thay vì tên dễ đọc.

| Thư mục con | Nội dung | Số tệp |
|---|---|---:|
| `court_judgments/` | PDF bản án/quyết định của tòa án | 25 PDF |
| `legal_documents/` | PDF văn bản pháp luật, văn bản hợp nhất và tài liệu nguồn được xếp cùng nhóm tệp này | 57 PDF |
| `legal_precedents/` | Tệp nguồn án lệ | 2 PDF + 1 DOCX |

Không phân loại pháp lý chỉ bằng vị trí thư mục: cần đọc `source_group`, `document_type`, `record_classification`, `graph_role` và nội dung hồ sơ. Một tệp PDF có thể liên quan tới nhiều biểu diễn dữ liệu; một hồ sơ cũng có thể có biến thể nguồn. `files_manifest.jsonl` chứa liên kết này, gồm cả `document_ids` dạng danh sách và `alternative_local_paths` khi có.

Phụ lục hồ sơ ở cuối tài liệu liệt kê `document_id`, số hiệu, tên và đường dẫn nguồn tương ứng để đọc được ý nghĩa của những tên tệp băm. Danh sách tệp vật lý đầy đủ nằm ở phụ lục kiểm kê.

<a id="processed"></a>

## 4. `02_processed_data/` — toàn văn và cấu trúc trích xuất

### `documents.jsonl` — toàn văn của 107 hồ sơ

Mỗi dòng là một đối tượng JSON chứa văn bản trong `text`, định danh `document_id`/`file_id`, phương pháp trích xuất, kích thước văn bản và thông tin nguồn. Các trường `needs_ocr`, `extraction_error`, `page_provenance`, `non_body_annotations` và `provenance_status` hỗ trợ đánh giá cách tạo bản trích xuất.

Có bản trích xuất không có nghĩa đã có PDF nguồn cục bộ: 23 hồ sơ hiện vẫn `SOURCE_MISSING`. Không tự tạo đường dẫn PDF cho chúng. `text` là nội dung nguồn đã lưu; các trường phân loại và kiểm chứng bổ sung không làm thay đổi nội dung này.

### `segments.jsonl` — 512 phân vùng

Chia văn bản thành vùng có `segment_id`, `segment_type`, `heading`, `text`, vị trí dòng và vị trí ký tự trong văn bản. Dùng để xác định một điều khoản thuộc phần chính, phụ lục hoặc vùng trích xuất nào. Không coi mỗi segment là một văn bản pháp luật độc lập.

### `hierarchy_headings.jsonl` — 562 tiêu đề cấu trúc

Lưu nhãn phân cấp như chương/mục, số và tên tiêu đề, cùng `document_id`, `segment_id` và tọa độ. Giúp tái dựng bố cục phía trên điều khoản; khác với bản ghi Điều/Khoản/Điểm trong `provisions.jsonl`.

### `provisions.jsonl` — 18.871 điều/khoản/điểm

Các trường quan trọng:

| Nhóm trường | Trường tiêu biểu | Cách dùng |
|---|---|---|
| Khóa | `provision_id`, `document_id`, `segment_id` | Nhận diện và nối dữ liệu |
| Cấp pháp lý | `level`, `number`, `article_number`, `clause_number`, `point_number` | Phân biệt `ARTICLE`, `CLAUSE`, `POINT` |
| Nội dung | `heading`, `text` | Đọc phần trích xuất |
| Cây | `parent_id`, `order`, `chapter`, `section`, `canonical_path` | Ghép cha–con và sắp xếp trong phạm vi phân cấp; `order` không phải số thứ tự toàn cục |
| Vị trí nguồn | `coordinate_space`, `segment_char_start/end`, `document_char_start/end`, `page_start/end` | Đối chiếu vùng ký tự/trang nguồn |
| Trạng thái nguồn | `page_status`, `source_unit_type`, `provenance_status` | Phân biệt trang PDF và đơn vị nguồn khác |
| Phiên bản | `provision_identity_id`, `provision_version_id`, `source_document_version_id` | Nối với `06_temporal/` |
| Thời gian | `valid_from/to`, `temporal_status`, `temporal_evidence`, `provision_temporal_verified` | Chỉ sử dụng như dữ liệu chưa duyệt nếu chưa có bằng chứng pháp lý |

Các khoảng ký tự phải được đọc theo `coordinate_space` của bản ghi; không đánh đồng tọa độ trong segment với tọa độ trong toàn văn. Khi dùng `start/end` để cắt chuỗi, cần kiểm tra quy ước của bản ghi với chuỗi nguồn; các tọa độ ký tự kiểu lát cắt dùng điểm cuối không bao gồm ký tự tại `end`. Văn bản cha có thể bao trùm văn bản con, nên cộng độ dài các cấp sẽ đếm lặp.

### `quarantined_provisions.jsonl` — 896 ứng viên bị cách ly

Giữ các bản ghi cấu trúc chưa được chấp thuận, chẳng hạn do đường dẫn cấu trúc mơ hồ hoặc văn bản quá ngắn. Không nối thêm tập này vào `provisions.jsonl` chỉ để tăng số lượng. Cần đọc lý do trong dữ liệu/hồ sơ kiểm tra liên quan và đối chiếu nguồn trước khi phục hồi.

### `converted_docx/` — 4 tệp DOCX chuyển đổi

Là sản phẩm chuyển đổi phục vụ đọc/trích xuất các nguồn tương ứng. Không thay thế vai trò PDF/DOCX nguồn trong `01_raw_data/`. Tên từng tệp được ghi đầy đủ trong phụ lục kiểm kê.

<a id="cases"></a>

## 5. `03_case_data/` — bản án và án lệ

`cases.jsonl` có **36 hồ sơ**, bao gồm **33 bản án/quyết định và 3 án lệ chính thức**. Một trong 33 hồ sơ là vụ án nguồn của án lệ (`PRECEDENT_SOURCE_CASE`), không phải thêm một án lệ chính thức. Ba án lệ chính thức là 20/2018/AL, 69/2023/AL và 70/2023/AL.

| Nhóm trường | Ý nghĩa |
|---|---|
| `case_id`, `document_id` | Khóa vụ việc và khóa nối sang danh mục văn bản |
| `case_number`, `case_type`, `court`, `decision_date` | Số hiệu, loại, tòa và ngày; cần đối chiếu chứng cứ ở chính phần đầu văn bản khi dùng chính thức |
| `title`, `record_classification`, `official_document_id` | Tên và định danh nguồn |
| `full_text`, `search_text` | Toàn văn và nội dung hỗ trợ tìm kiếm |
| `dispute`, `facts`, `reasoning`, `decision`, `features` | Các phần trích xuất/diễn giải vụ việc; không mặc nhiên là dữ liệu đã được chuyên gia duyệt |
| `citations_law` | Thông tin viện dẫn được lưu; cần kiểm tra đích viện dẫn |
| `case_sections_review_status` | Mức duyệt các phần nội dung vụ án |
| Trường URL, nguồn, graph | Truy vết và giới hạn phạm vi sử dụng |

Hiện **24** hồ sơ có `case_sections_review_status = LEGACY_UNREVIEWED`; **12** hồ sơ có `UNRESOLVED_FULL_TEXT_PRESERVED`. Vì vậy có thể đọc và tìm kiếm toàn văn, nhưng không tự động chuyển các trường facts/reasoning/decision thành sự kiện pháp lý đã xác thực. Hồ sơ có URL đúng vẫn có thể bị cách ly vì thiếu nguồn, sai phạm vi hoặc chưa duyệt nội dung.

<a id="metadata"></a>

## 6. `04_metadata/` — danh mục và định danh

### `documents.jsonl` — danh mục trung tâm

107 dòng, mỗi dòng ứng với một `document_id`. Đây là điểm bắt đầu thuận tiện nhất để tìm số hiệu/tên và quyết định bản ghi nào được phép sử dụng.

| Nhóm | Trường tiêu biểu |
|---|---|
| Định danh | `document_id`, `file_id`, `document_number`, `instrument_number`, `case_number`, `instrument_id`, `version_id` |
| Mô tả | `title`, `document_type`, `source_group`, `issuer`, `language`, `record_classification` |
| Nguồn tại chỗ | `local_file_path`, `local_source_status`, `sha256`, `local_sha256` |
| Nguồn trực tuyến | `view_url`, `official_url`, `download_url`, `source_url`, `canonical_url`, `source_references` |
| Truy vết | `old_url`, `url_history`, `legacy_path_references`, `legacy_provenance`, `provenance` |
| Kiểm chứng | `verification_status`, `source_verification_status`, `metadata_verified`, `metadata_evidence`, `last_url_verification` |
| Hiệu lực/phiên bản | `promulgated_date`, `effective_from/to`, `status`, `legal_status`, `temporal_verified`, `consolidation_as_of` |
| Phạm vi sử dụng | `graph_status`, `graph_eligible`, `graph_role`, `graph_quarantine_reasons`, `graph_import_policy`, `final_scope_review`, `final_date_review` |

Một số trường không xuất hiện ở mọi dòng. Giá trị `status`/ngày hiệu lực được kế thừa không tự trở thành kết quả thẩm định mới. Cần đọc bằng chứng và ngày xác minh, không suy hiệu lực từ năm ban hành hoặc từ HTTP 200.

### `files_manifest.jsonl` — 191 bản ghi quản lý tệp

Gồm **107 mốc nguồn hồ sơ** (`DOCUMENT_SOURCE_ANCHOR`) và **84 bản ghi tệp nguồn vật lý chính**. Bản ghi mốc có thể chỉ ra nguồn đang thiếu; vì vậy 191 không phải số tệp gốc và cũng không phải số văn bản.

Các khóa `file_id`, `document_id` hoặc `document_ids`, `path`, `relative_path`, `local_file_path`, `sha256`, `size_bytes`, `extension`, `source_status`, `expected_original_sha256`, `record_kind` và đường dẫn thay thế giúp tìm tệp thực. `expected_original_sha256` là giá trị kỳ vọng của nguồn, không được nhầm với mọi checksum của bản chuyển đổi.

### `source_catalog.jsonl` — 107 hồ sơ nguồn

Lưu nguồn, URL ưu tiên, URL cũ, bằng chứng kiểm chứng, biến thể nguồn và alias của từng văn bản. Tại thời điểm kiểm kê, tệp này có nội dung byte giống `08_Url/source_catalog.jsonl`; hai bản là bản sao danh mục, không phải 214 văn bản khác nhau. Nếu cập nhật sau này, phải kiểm tra tính đồng bộ của cả hai.

<a id="provenance"></a>

## 7. `05_provenance/` — truy vết đoạn trích về nguồn

| Thành phần | Quy mô | Nội dung và cách đọc |
|---|---:|---|
| `provision_spans.jsonl` | 18.871 dòng | `provision_id` → `document_id`/`segment_id`, tọa độ ký tự, trang, loại đơn vị nguồn và trạng thái truy vết |
| `page_audit.jsonl` | 3.116 dòng | Kiểm tra lớp chữ/OCR theo trang: phương pháp, số ký tự, mức đồng thuận, mã nguyên nhân và đường dẫn ảnh |
| `footnotes.jsonl` | 106 dòng | Chú thích/ghi chú ngoài thân bài, marker, text, bounding box, trang, nguồn băm và trạng thái phát hiện/duyệt |
| `page_previews/` | 96 PNG | Ảnh xem trước trang; không phải toàn bộ các trang nguồn |

`page_audit` hiện có **2.927 PASS**, **15 REVIEWED**, **174 REVIEW_REQUIRED**. Đây là trạng thái chất lượng trang, khác với kiểm chứng URL và khác với xác minh hiệu lực. Trường `page` phải hiểu trong tài liệu nguồn tương ứng; không dùng số trang như khóa duy nhất.

Đường dẫn `relative_path`/`preview_path` kế thừa có thể dùng bố cục cũ. Khi đường dẫn không tồn tại, tìm lại bằng `file_id`, SHA-256, số trang và danh sách tệp thực tế; không kết luận rằng văn bản nguồn bị mất chỉ vì một chuỗi đường dẫn lịch sử không mở được.

<a id="temporal"></a>

## 8. `06_temporal/` — định danh và phiên bản theo thời gian

| Tệp | Số dòng | Ý nghĩa |
|---|---:|---|
| `provision_identities.jsonl` | 15.651 | Định danh khái niệm điều khoản theo `instrument_id` và `canonical_path` |
| `provision_versions.jsonl` | 18.871 | Một biểu diễn phiên bản, nối bằng `provision_identity_id` và `source_document_version_id` |
| `legal_changes.jsonl` | 25 | Ứng viên sửa đổi/thay thế và bằng chứng trích xuất, gồm văn bản/điều khoản nguồn và đích dự kiến |

Toàn bộ phiên bản hiện có `temporal_status = INHERITED_DOCUMENT` và `provision_temporal_verified = false`. Cả 25 ứng viên thay đổi có `temporal_status = UNKNOWN`; 11 đích có `resolution_status = RESOLVED`, 14 đích còn `UNRESOLVED`. Việc giải được đích viện dẫn hoặc có `evidence_status = RESOLVED` không đồng nghĩa đã xác nhận quan hệ sửa đổi và ngày hiệu lực về mặt pháp lý.

Các trường `operation`, `effective_from`, `evidence_text`, `evidence_span`, `confidence` và `method` phục vụ thẩm định. Chưa nên dùng thư mục này để trả lời chắc chắn “điều khoản nào có hiệu lực vào ngày X”.

<a id="extensions"></a>

## 9. `07_research_extensions/` — dữ liệu nghiên cứu bổ sung và kế thừa

Đây là nơi giữ nội dung hữu ích từ các lần xử lý trước. Không phải mọi dòng ở đây đã được đối chiếu với danh mục 107 hồ sơ hiện tại. Số liệu 95 văn bản/24 vụ việc xuất hiện trong một số tệp là quy mô của phiên bản cũ, không thay thế quy mô hiện tại.

### Các tệp ở ngay cấp thư mục

| Tệp | Số dòng | Nội dung |
|---|---:|---|
| `extension_catalog.jsonl` | 217 | Danh mục tệp mở rộng: đường dẫn, SHA-256, dung lượng, trạng thái và ghi chú |
| `inheritance_locations.jsonl` | 317 | Ánh xạ thành phần nguồn cũ sang vị trí kế thừa cục bộ; dùng để truy nguồn, đường dẫn có thể đã cũ |
| `huggingface_selected_records.jsonl` | 15 | Các dòng nguồn Hugging Face đã được chọn, có markdown, URL, số hiệu/tòa/ngày và metadata nguồn |
| `legal_metadata_observations.jsonl` | 66 | Quan sát metadata thứ cấp cho 59 hồ sơ, gồm `document_id`, dataset/revision, cách ghép và `source_metadata` |

15 dòng được chọn từ `tmquan/anle-toaan-gov-vn` là dữ liệu nguồn phục vụ 12 hồ sơ mới và 3 alias, không phải 15 văn bản mới cộng thêm vào 107. Những quan sát từ `th1nhng0/vietnamese-legal-documents` là nguồn tham khảo thứ cấp; không thay cho kiểm chứng trên nguồn cơ quan ban hành. Bộ dữ liệu hiện không chứa toàn bộ hai kho Hugging Face đó.

### `extraction_review/` — hồ sơ kiểm tra trích xuất

| Thành phần | Nội dung |
|---|---|
| `documents_with_annotations.jsonl` | 95 bản trích xuất kế thừa có chú thích; không phải bản danh mục 107 hồ sơ mới nhất |
| `footnote_evidence.jsonl` | 106 dòng chứng cứ chú thích, phục vụ so sánh với thân bài |
| `page_review_evidence.jsonl` | 174 hồ sơ trang có dấu hiệu cần kiểm tra: native/selected/cleaned text, metrics, khác biệt số, kết luận và trạng thái người duyệt |
| `page_review_tracking.csv` | 174 dòng theo dõi reviewer, thời điểm, quyết định, bằng chứng và đường dẫn bản sửa nếu có |
| `summary.json` | Thống kê đợt rà soát trích xuất, loại phát hiện và dấu vết nguồn |
| `index.html` | Trang xem hỗ trợ rà soát; việc mở đầy đủ phụ thuộc các đường dẫn ảnh còn phù hợp |
| `images/` | 174 PNG minh họa các trang cần rà soát |

Các ảnh này khác tập 96 ảnh ở `05_provenance/page_previews/`; tổng ảnh trong hai nơi là 270. Có ảnh và có bảng theo dõi không có nghĩa cả 174 trang đã được người duyệt phê chuẩn.

### `knowledge/` — kết quả tri thức được kế thừa

| Tệp | Quy mô | Ý nghĩa và giới hạn |
|---|---:|---|
| `issues.jsonl` | 17 dòng | Từ điển chủ đề/vấn đề: `issue_id`, `issue_key`, `label_vi` |
| `issue_edges.jsonl` | 7.859 dòng | Liên hệ nguồn–chủ đề có điểm, từ khóa và bằng chứng; không xem phân loại theo từ khóa là kết luận pháp lý |
| `issue_review_queue.jsonl` | 3.835 dòng | Hàng chờ xem xét liên hệ chủ đề; có thể là tập liên quan với edges, không cộng thành số quan hệ độc lập |
| `citations.jsonl` | 4.371 dòng | Viện dẫn, đoạn bằng chứng, trạng thái giải đích và điều/khoản/điểm đích dự kiến |
| `relation_edges.jsonl` | 2.073 dòng | Ứng viên quan hệ, loại quan hệ, confidence, phương pháp và chứng cứ |
| `accepted_relation_edges.jsonl` | 2.029 dòng | Tập được chấp nhận bởi quy trình cũ; vẫn cần kiểm tra gate hiện tại và đích ID trước khi dùng |
| `relation_review_queue.jsonl` | 44 dòng | Quan hệ còn chờ duyệt của quy trình cũ |
| `diagnostic_checklists.jsonl` | 16.241 dòng | Câu hỏi/checklist sinh từ điều khoản, có source text, fact slots, generator và confidence |
| `diagnostic_review_queue.jsonl` | 130 dòng | Checklist cần xem xét |
| `communities.json` | Đối tượng JSON | Các cộng đồng và tóm tắt/quan hệ cộng đồng của phiên bản nghiên cứu cũ |

Các tên `accepted`, các điểm confidence và báo cáo chạy thành công không thay thế việc xác minh nội dung. Thư mục này không phải bản xuất đồ thị hoàn chỉnh sẵn dùng của 107 hồ sơ hiện tại.

### `legacy_quality_reports/` — 23 tệp báo cáo lịch sử

Các tệp phản ánh pipeline trước đây, trong đó có thử kết nối, AI, cấu trúc, embedding và Neo4j. Không coi các cờ PASS cũ là kiểm định mới cho toàn bộ dữ liệu hiện tại. Hiện thư mục Data không chứa bộ chỉ mục vector/embedding hoặc bản xuất toàn bộ đồ thị chỉ vì báo cáo cũ nhắc tới chúng.

| Tệp | Nội dung |
|---|---|
| `ai_provider_preflight.json` | Kiểm tra kết nối/schema/độ trễ dịch vụ AI |
| `aura_preflight.json` | Kiểm tra kết nối Aura trong lần chạy cũ |
| `build_issues.jsonl` | Danh sách vấn đề dựng dữ liệu; hiện 0 dòng |
| `case_ai_quality.json` | Thống kê xử lý AI từng vụ việc, số phần, đặc trưng và lỗi |
| `checklist_ai_failures.jsonl` | 690 dòng lỗi/thất bại xử lý checklist |
| `checklist_ai_quality.json` | Thống kê sinh checklist, thành công một phần và fallback |
| `dense_request.json` | Tham số yêu cầu xử lý và đường dẫn môi trường cũ |
| `dense_validation.json` | Kết quả kiểm tra embedding của lần chạy cũ |
| `extraction_integrity.json` | Kiểm tra tính toàn vẹn trích xuất của 95 hồ sơ cũ |
| `extraction_issues.jsonl` | Danh sách vấn đề trích xuất; hiện 0 dòng |
| `final_outputs_validation.json` | Tổng kiểm tra các đầu ra của bố cục cũ |
| `kaggle_run.json` | Thời gian, giai đoạn và trạng thái lần chạy Kaggle |
| `neo4j_validation.json` | Kết quả nạp/kiểm tra Neo4j lịch sử |
| `offline_ai_progress.json` | Tiến độ bổ sung dữ liệu bằng AI |
| `offline_ai_requests.jsonl` | 5.504 sự kiện yêu cầu AI, gồm thời gian/lỗi/trạng thái HTTP khi có |
| `offline_build_progress.json` | Tiến độ build/index lịch sử |
| `offline_final_run.json` | Trạng thái cuối lần chạy offline lịch sử |
| `page_extraction_quality.json` | Tổng hợp chất lượng 3.116 trang |
| `parsing_issues.jsonl` | 42 bản ghi vấn đề phân tích cấu trúc |
| `provision_provenance_integrity.json` | Kiểm tra liên kết vị trí nguồn điều khoản lịch sử |
| `structure_coverage.json` | Độ bao phủ cấu trúc theo hồ sơ của lần xử lý cũ |
| `summary.json` | Tổng hợp quy mô/đầu ra của phiên bản cũ |
| `validation_issues.jsonl` | 44 bản ghi vấn đề validation lịch sử |

### `legacy_review_queues/`

Chứa `source_review_queue.jsonl`: **95 dòng** hàng chờ rà soát nguồn của bộ cũ. Có thông tin file, nhà cung cấp, URL, nguồn chính thức, reviewer, trường thiếu và lý do duyệt. Không dùng 95 dòng này làm danh mục hồ sơ hiện tại hoặc để ghi đè trạng thái kiểm chứng mới hơn.

<a id="urls"></a>

## 10. `08_Url/` — toàn bộ liên kết và lịch sử kiểm chứng

### `source_catalog.jsonl`

107 hồ sơ nguồn, hiện trùng nội dung với bản ở `04_metadata/`. `view_url` dành cho đọc trực tiếp, `official_url` giữ nguồn cơ quan nhà nước, `download_url` dành cho tệp tải/mở trực tiếp. Trang chính thức có thể chỉ có metadata và tệp đính kèm; không phải cứ là trang chính thức thì toàn văn đã hiển thị ở HTML.

`original_url`, `old_url`, `url_history`, `alternative_urls`, `source_aliases` và các trường provenance giữ dấu vết. URL cũ không đương nhiên là URL vẫn nên dùng hiện tại.

### `url_verification.jsonl`

**565 dòng** nhật ký kiểm tra. Một hồ sơ/URL có thể có nhiều dòng ở các thời điểm khác nhau. Có HTTP status, URL cuối, redirect, content type, bằng chứng nội dung và các kiểm tra số hiệu/tiêu đề/cơ quan; một số hồ sơ tư pháp có kiểm tra tòa, ngày, loại án. Không phải mọi dòng đều có đủ cùng một bộ trường.

| Mức | Cách hiểu |
|---|---|
| `SEARCH_FOUND` | Tìm thấy một liên kết ứng viên; chưa chứng minh mở được hoặc đúng nội dung |
| `HTTP_OK` | Truy cập thành công về mặt HTTP; vẫn có thể là trang sai, trang lỗi mềm hoặc chỉ có metadata |
| `CONTENT_MATCH` | Có bằng chứng nội dung phù hợp với hồ sơ; cần xem tiêu chí nào đã được xác nhận |
| `VERIFIED` | Trạng thái xác minh được lưu sau kiểm tra URL/nội dung/định danh phù hợp |
| `REVIEW_REQUIRED` | Chưa đủ điều kiện; đọc `error_reason`, evidence và lý do cấp hồ sơ |
| `NOT_CHECKED` / `MISSING_URL` | Chưa kiểm tra hoặc chưa có URL; không coi là xác minh thất bại có nội dung |

Đây là các mức cần phân biệt khi đọc dữ liệu, không có nghĩa tất cả dòng nhật ký đều đi qua đủ các mức. Ngày `checked_at` cho biết thời điểm kiểm tra; tính truy cập của website có thể thay đổi sau đó.

### `url.csv`

**367 dòng, 38 cột, UTF-8 có BOM**. Mỗi dòng gắn một `document_id` với một URL; có 2 dòng URL trống để không làm mất hồ sơ thiếu liên kết. 365 cặp không rỗng dùng chung 300 chuỗi URL khác nhau. Không cộng dòng CSV thành số văn bản.

| Nhóm cột | Cột |
|---|---|
| Văn bản | `document_id`, `file_id`, `document_number`, `title`, `document_type`, `source_group`, `issuer` |
| Trạng thái cấp hồ sơ | `document_verification_status`, `graph_status`, `document_review_reason` |
| URL của dòng | `url`, `url_roles`, `url_type`, `source_official` |
| Vai trò hiện tại | `is_current_view_url`, `is_current_official_url`, `is_current_download_url` |
| URL đang dùng của hồ sơ | `current_view_url`, `current_official_url`, `current_download_url`, `old_url` |
| Kết quả truy cập | `url_verification_status`, `last_checked_at`, `http_status`, `final_url`, `content_type` |
| Nội dung/định danh | `document_number_match`, `title_match`, `issuer_match`, `content_match` |
| Dấu vết phản hồi | `response_sha256`, `response_bytes`, `error_reason` |
| Lịch sử có cấu trúc | `redirect_chain_json`, `source_locations_json`, `verification_history_json` |
| Giá trị gốc trước đối chiếu | `original_document_number`, `original_title` |

Các cột kết thúc bằng `_json` chứa JSON bên trong ô CSV, cần parse thêm nếu xử lý bằng chương trình. `document_verification_status = VERIFIED` không có nghĩa mọi URL lịch sử của hồ sơ đều đã VERIFIED. Một dòng có URL dẫn đến dataset nguồn cũng không phải link toàn văn riêng của văn bản.

<a id="joins"></a>

## 11. Cách nối dữ liệu và lựa chọn tập sử dụng

### Khóa nối

1. Dùng `04_metadata/documents.jsonl` làm danh mục; khóa chính là **`document_id`**.
2. Ghép cùng `document_id` sang toàn văn, danh mục nguồn, cases và URL. Không ghép chỉ bằng `document_number`: số hiệu có thể trùng, có nhiều cách biểu diễn và có bản ghi chưa đủ định danh.
3. Với cấu trúc, nối `document_id` → `segment_id` → `provision_id`; dùng `parent_id` để dựng cây trong đúng văn bản/phân vùng.
4. Với truy vết, nối `provision_id` sang `provision_spans`; dùng `file_id`/SHA-256 và số trang để tìm `page_audit`/tệp nguồn.
5. Với phiên bản, nối `provision_identity_id` và `provision_version_id`; không coi một identity là một đoạn chữ cố định ở mọi phiên bản.
6. `files_manifest` có hai kiểu khóa: dòng mốc dùng `document_id`, dòng tệp có thể dùng `document_ids` dạng danh sách. Phải xử lý cả hai.

### Đọc toàn văn và nguồn gốc

Để tra cứu nguồn: tìm hồ sơ trong metadata → đọc `text` từ processed documents → mở `Data/` cộng `local_file_path` nếu `AVAILABLE` → dùng URL nếu muốn đối chiếu trực tuyến. Với `SOURCE_MISSING`, có thể đọc bản trích xuất đang lưu nhưng phải nói rõ chưa có tệp nguồn cục bộ để đối chiếu.

### Khi xây tập chính thức hoặc HierarGraph

Đọc `graph_status`, `graph_eligible` và **từng cờ trong `graph_import_policy`**. `ACTIVE` vẫn có thể chỉ cho phép node văn bản/nguồn, không cho phép nhập toàn bộ điều khoản hoặc sự kiện vụ án.

| `graph_role` | Phạm vi cần hiểu |
|---|---|
| `LABOR_LEGAL_SOURCE` | Nguồn pháp luật lao động; chỉ dựng cấu trúc nếu policy của hồ sơ cho phép |
| `PROCEDURAL_REFERENCE_METADATA_ONLY` | Nguồn thủ tục/tố tụng tham chiếu, dùng metadata theo policy |
| `SUPPLEMENTARY_REFERENCE_METADATA_ONLY` | Tài liệu bổ trợ, dùng metadata tham chiếu |
| `VOCATIONAL_EDUCATION_REFERENCE_METADATA_ONLY` | Nguồn giáo dục nghề nghiệp tham chiếu |
| `JUDICIAL_SOURCE_AND_IDENTITY_ONLY` | Nguồn và định danh tư pháp; không nhập facts/tòa/ngày chưa có bằng chứng duyệt phù hợp |

Không tự bật các cờ `allow_inherited_effect_status_as_fact`, `allow_unreviewed_temporal_edges` hoặc `allow_unreviewed_case_facts`. Những cờ này hiện thể hiện giới hạn của dữ liệu. Nguồn có URL VERIFIED vẫn có thể `QUARANTINE`; 95 hồ sơ URL đã xác minh và 39 hồ sơ ACTIVE là hai phép đếm khác nhau.

<a id="limitations"></a>

## 12. Hạn chế và dấu vết lịch sử cần biết

- **Nguồn tại chỗ chưa đủ:** 23/107 hồ sơ chưa có tệp gốc; toàn văn trích xuất của chúng vẫn tồn tại.
- **Chất lượng trang:** 174 bản ghi trang còn `REVIEW_REQUIRED`. Việc lập tài liệu này không thực hiện OCR hay duyệt lại các trang đó.
- **Thời gian pháp lý chưa hoàn thiện:** không có phiên bản điều khoản nào được đánh dấu `provision_temporal_verified = true`; không suy hiệu lực từ trường kế thừa.
- **Các phần vụ án chưa duyệt:** 24 hồ sơ kế thừa và 12 hồ sơ giữ toàn văn chưa được duyệt phân đoạn như mô tả tại mục 5.
- **Trùng số hiệu không có nghĩa trùng hồ sơ:** nhóm `01/2021/LĐ-GĐT` và `1/2021/LĐ-GĐT` có định danh/ngày và nội dung khác nhau; cần giữ riêng. Hai hồ sơ `326/2016/UBTVQH14` có biểu diễn nguồn khác nhau; không tự xóa hoặc hợp nhất chỉ bằng số hiệu.
- **Xung đột ngày được bảo lưu:** hồ sơ `doc_7dd079c75c200f67` có ngày metadata 14/03/2025 khác ngày đầu văn bản 24/04/2025; `doc_d53552a67d44c239` có ngày metadata 18/03/2024 khác ngày đầu văn bản 20/06/2024. Phải đọc chứng cứ, không chọn ngày theo suy đoán.
- **Ba nghị định đã có URL VERIFIED:** 10/2024/NĐ-CP, 39/2016/NĐ-CP, 140/2018/NĐ-CP vẫn cần tuân thủ policy/phạm vi hiện có; URL đúng không tự làm hồ sơ ACTIVE. Nghị định 10/2024/NĐ-CP có tiêu đề về khu công nghệ cao, cần đặc biệt đọc phạm vi trước khi dùng cho nghiên cứu lao động.
- **Đường dẫn cũ còn trong metadata:** có chuỗi dùng `Url/`, `09_research_extensions/`, các bố cục tiền nhiệm và đường dẫn Kaggle/Linux. Tên thư mục hiện tại là `08_Url/` và `07_research_extensions/`. Đây là dấu vết được giữ, không phải cam kết mọi đường dẫn lịch sử đều bấm mở được.
- **Báo cáo được dẫn nhưng không có trong cây hiện tại:** một số trường trỏ tới `07_quality_reports/FINAL_DATASET_REVIEW.md` hoặc báo cáo chất lượng khác. Các thư mục báo cáo độc lập đó hiện không nằm trong Data; các tệp còn ở `legacy_quality_reports/` là báo cáo kế thừa riêng.
- **Không có bộ triển khai hoàn chỉnh:** Data hiện lưu nguồn và dữ liệu dẫn xuất, không chứa toàn bộ mã dựng lại pipeline, chỉ mục tìm kiếm, vector embedding hay bản xuất đồ thị đầy đủ. Không thể chỉ dựa vào báo cáo lịch sử để tuyên bố đã sẵn sàng triển khai hệ thống trả lời pháp lý.

Việc lập `Detail.data.md` chỉ đọc và mô tả dữ liệu hiện có; không sửa toàn văn, không cập nhật URL, không thay đổi trạng thái hồ sơ và không xóa tệp nguồn.

<a id="inventory"></a>

## 13. Phụ lục được kiểm kê trực tiếp

Các phần dưới đây được sinh từ các tệp thực tế để cung cấp danh sách đầy đủ hồ sơ, cấu trúc tệp và tên trường, thay vì chỉ mô tả những ví dụ đại diện.

### 13.1. Danh mục đủ 107 hồ sơ

Tên và số hiệu dưới đây lấy nguyên từ metadata; giữ nguyên cả giá trị cần đối chiếu. URL_VERIFIED chỉ là trạng thái URL, không thay thế graph policy.

| document_id | Số hiệu | Tiêu đề | Nhóm | Tệp nguồn / trạng thái | URL / graph |
|---|---|---|---|---|---|
| `doc_d98ede4425ed3133` | 18/VBHN-VPQH | Văn bản hợp nhất Bộ luật Lao động | CONSOLIDATED | [Data/01_raw_data/legal_documents/file_a6a61feef6d93b80.pdf](Data/01_raw_data/legal_documents/file_a6a61feef6d93b80.pdf) | VERIFIED / ACTIVE |
| `doc_1db38b2072ff219c` | 19/VBHN-VPQH | Văn bản hợp nhất Luật Bảo hiểm xã hội | CONSOLIDATED | [Data/01_raw_data/legal_documents/90cf1e79ba6e778fd1bb7702662167372c7f646622edf83efd9851035af642b7.pdf](Data/01_raw_data/legal_documents/90cf1e79ba6e778fd1bb7702662167372c7f646622edf83efd9851035af642b7.pdf) | VERIFIED / ACTIVE |
| `doc_5dc90079ef8a514c` | 21/VBHN-VPQH | Văn bản hợp nhất Bộ luật Tố tụng dân sự | CONSOLIDATED | [Data/01_raw_data/legal_documents/6c2bbf0fba807ac2a3c84320da8bb5c88a4e0b0e264222f75c2fe6d99c1d36b9.pdf](Data/01_raw_data/legal_documents/6c2bbf0fba807ac2a3c84320da8bb5c88a4e0b0e264222f75c2fe6d99c1d36b9.pdf) | VERIFIED / ACTIVE |
| `doc_82c69b5e44cbb2bd` | 90/VBHN-VPQH | Văn bản hợp nhất Luật Công đoàn | CONSOLIDATED | [Data/01_raw_data/legal_documents/95349436112a28676c84322b92954c6b820db75be08cb5dea35b8ce8646765a6.pdf](Data/01_raw_data/legal_documents/95349436112a28676c84322b92954c6b820db75be08cb5dea35b8ce8646765a6.pdf) | VERIFIED / ACTIVE |
| `doc_146ffa9a3336532f` |  | 36-2024-LĐ-PT | JUDICIAL | SOURCE_MISSING | REVIEW_REQUIRED / QUARANTINE |
| `doc_1956bb3730bc8844` |  | 03-2020-LĐ-GĐT | JUDICIAL | SOURCE_MISSING | REVIEW_REQUIRED / QUARANTINE |
| `doc_2701792edc6f226f` |  | 01-2021-LĐ-GĐT | JUDICIAL | SOURCE_MISSING | REVIEW_REQUIRED / QUARANTINE |
| `doc_2cbcfe7e9be31868` |  | 01-20-07-2023 | JUDICIAL | [Data/01_raw_data/court_judgments/5d3f060f19acaea3745a80698732ecd5b561e05ad7667d6fdfc9dcd6601c8612.pdf](Data/01_raw_data/court_judgments/5d3f060f19acaea3745a80698732ecd5b561e05ad7667d6fdfc9dcd6601c8612.pdf) | VERIFIED / ACTIVE |
| `doc_3010c47481ff071b` |  | 14-2025-LĐ-PT | JUDICIAL | [Data/01_raw_data/court_judgments/d3bed9996dc3ee41d5d1205986e99f97e054210b78cce44494db6e68cd3edd25.pdf](Data/01_raw_data/court_judgments/d3bed9996dc3ee41d5d1205986e99f97e054210b78cce44494db6e68cd3edd25.pdf) | VERIFIED / ACTIVE |
| `doc_30679e58371c5aa1` |  | 70-2023-AL | JUDICIAL | [Data/01_raw_data/legal_precedents/3b451fd330f9583d4468767cc1d503898f259084ee4e690f08226e85c93e78d1.pdf](Data/01_raw_data/legal_precedents/3b451fd330f9583d4468767cc1d503898f259084ee4e690f08226e85c93e78d1.pdf) | VERIFIED / ACTIVE |
| `doc_39a196aa116a5878` |  | 30-2025-LĐ-PT | JUDICIAL | [Data/01_raw_data/court_judgments/5b719cd3c895afe9c0974f0af65ad5292ae63ecc8b6d4636e77bb491940b6392.pdf](Data/01_raw_data/court_judgments/5b719cd3c895afe9c0974f0af65ad5292ae63ecc8b6d4636e77bb491940b6392.pdf) | VERIFIED / QUARANTINE |
| `doc_3a11c3840c3ba73c` |  | 01-2022-LĐ-GĐT | JUDICIAL | [Data/01_raw_data/court_judgments/98b2fbc1ac163ce0629132076830c2591f7c134b0698db6935c7ab6312fb6d6f.pdf](Data/01_raw_data/court_judgments/98b2fbc1ac163ce0629132076830c2591f7c134b0698db6935c7ab6312fb6d6f.pdf) | VERIFIED / ACTIVE |
| `doc_48e5e927eba7322c` |  | 20-2018-AL | JUDICIAL | [Data/01_raw_data/legal_precedents/ab9da143e8423cc76363a0b194cbe660b3bd13f10f94269dfb75fe77c3745dd0.docx](Data/01_raw_data/legal_precedents/ab9da143e8423cc76363a0b194cbe660b3bd13f10f94269dfb75fe77c3745dd0.docx) | REVIEW_REQUIRED / QUARANTINE |
| `doc_4fca3aab26c728a0` |  | 54-2025-LD-PT Ky-luat-sa-thai | JUDICIAL | [Data/01_raw_data/court_judgments/9e6f84b22b96c5c3ad72876de67bd6f0fa77878571e8e901f38ca4b3e2b25a74.pdf](Data/01_raw_data/court_judgments/9e6f84b22b96c5c3ad72876de67bd6f0fa77878571e8e901f38ca4b3e2b25a74.pdf) | VERIFIED / ACTIVE |
| `doc_504b41989bd57952` |  | 80-2025-LD-PT Don-phuong-cham-dut-HDLĐ | JUDICIAL | [Data/01_raw_data/court_judgments/08608899dba38a8a020f27f2e342ed8bdf1bc05f2d79840d90585f00c57b7fea.pdf](Data/01_raw_data/court_judgments/08608899dba38a8a020f27f2e342ed8bdf1bc05f2d79840d90585f00c57b7fea.pdf) | VERIFIED / ACTIVE |
| `doc_591caf88b030b39f` |  | 01-2024-LĐ-GĐT | JUDICIAL | SOURCE_MISSING | REVIEW_REQUIRED / QUARANTINE |
| `doc_7dd079c75c200f67` |  | 02-2025-LĐPT | JUDICIAL | SOURCE_MISSING | VERIFIED / QUARANTINE |
| `doc_8020744ce12bdda0` |  | 605-2023-LĐ PT | JUDICIAL | SOURCE_MISSING | VERIFIED / QUARANTINE |
| `doc_85a5d6d71b4c2588` |  | 02-2019-LĐ-GĐT | JUDICIAL | [Data/01_raw_data/court_judgments/b1e32da8ccd53981a5cab144565574e7c98ce4bb9918f0a41e6cfe69ae7798e1.pdf](Data/01_raw_data/court_judgments/b1e32da8ccd53981a5cab144565574e7c98ce4bb9918f0a41e6cfe69ae7798e1.pdf) | VERIFIED / ACTIVE |
| `doc_8a4d133761a6d5d9` |  | 01-10-01-2023 | JUDICIAL | [Data/01_raw_data/court_judgments/65b98fa01981cfc1622d3d3b5b8f0382019ec3ec9c8a0da6fb60dd9d75517aa5.pdf](Data/01_raw_data/court_judgments/65b98fa01981cfc1622d3d3b5b8f0382019ec3ec9c8a0da6fb60dd9d75517aa5.pdf) | VERIFIED / ACTIVE |
| `doc_a04d2ae3f247b632` |  | 69-2023-AL | JUDICIAL | [Data/01_raw_data/legal_precedents/14f91b3f764c0eaa558533fe1ba8848904d2b2daf02981a8753d811ea19e71d6.pdf](Data/01_raw_data/legal_precedents/14f91b3f764c0eaa558533fe1ba8848904d2b2daf02981a8753d811ea19e71d6.pdf) | VERIFIED / ACTIVE |
| `doc_c75d29389761983a` |  | 03-2024-LĐ-PT | JUDICIAL | [Data/01_raw_data/court_judgments/3c5d7964c2cab23a1e2ac90bfe1a872a31c548277eb96e409e98c08f0e7e3b01.pdf](Data/01_raw_data/court_judgments/3c5d7964c2cab23a1e2ac90bfe1a872a31c548277eb96e409e98c08f0e7e3b01.pdf) | VERIFIED / ACTIVE |
| `doc_d53552a67d44c239` |  | 69-20-06-2024 | JUDICIAL | SOURCE_MISSING | REVIEW_REQUIRED / QUARANTINE |
| `doc_e2b4afb84320c14d` |  | 03-2025-LĐ-GĐT | JUDICIAL | [Data/01_raw_data/court_judgments/b9c3596cf9ad672724158518e60b4aca96783d77b7ec68e90994a835ea528bfe.pdf](Data/01_raw_data/court_judgments/b9c3596cf9ad672724158518e60b4aca96783d77b7ec68e90994a835ea528bfe.pdf) | VERIFIED / ACTIVE |
| `doc_e3196e7101577bf4` |  | 01-2025-LĐ-GĐT | JUDICIAL | [Data/01_raw_data/court_judgments/9fb43f3725e912b72e244e9b03bd6836ab053c29ca7445077ae6fc3fa0b00692.pdf](Data/01_raw_data/court_judgments/9fb43f3725e912b72e244e9b03bd6836ab053c29ca7445077ae6fc3fa0b00692.pdf) | VERIFIED / ACTIVE |
| `doc_ea1ffa62ff5bb867` |  | 16-18-12-2025 | JUDICIAL | SOURCE_MISSING | REVIEW_REQUIRED / QUARANTINE |
| `doc_eac0c25d13ce6849` |  | 06-2022-LĐ-GĐT | JUDICIAL | [Data/01_raw_data/court_judgments/f8040f0720da7eb186dfd2f17b1887d31ca5278e8491db7384bb62e6eee90e5b.pdf](Data/01_raw_data/court_judgments/f8040f0720da7eb186dfd2f17b1887d31ca5278e8491db7384bb62e6eee90e5b.pdf) | VERIFIED / ACTIVE |
| `doc_feeac807707ad4cd` |  | 02-2021-LĐ-GĐT | JUDICIAL | [Data/01_raw_data/court_judgments/f371c16b1b94284a440285d2021eb066d59055af9d2510e011d30d8cb3827505.pdf](Data/01_raw_data/court_judgments/f371c16b1b94284a440285d2021eb066d59055af9d2510e011d30d8cb3827505.pdf) | VERIFIED / QUARANTINE |
| `doc_hf_36d00eeb4ea97850` | 1/2021/DS-ST | V/v tranh chấp hợp đồng lao động đòi chi phí đào tạo | JUDICIAL | [Data/01_raw_data/court_judgments/7915ba4206b65572bbd24309de9ad43b151b5f835f5007c2dd6088adfd20d755.pdf](Data/01_raw_data/court_judgments/7915ba4206b65572bbd24309de9ad43b151b5f835f5007c2dd6088adfd20d755.pdf) | VERIFIED / ACTIVE |
| `doc_hf_36732cbc34545cd8` | 1/2021/LĐ-GĐT | Về:“Tranh chấp bồi thường thiệt hại khi thực hiện chế độ bảo hiểm xã hội” | JUDICIAL | [Data/01_raw_data/court_judgments/c34750e61927fc705e6dd111e3191182541ee355b22ce8afda5939ccce7ad3ee.pdf](Data/01_raw_data/court_judgments/c34750e61927fc705e6dd111e3191182541ee355b22ce8afda5939ccce7ad3ee.pdf) | VERIFIED / ACTIVE |
| `doc_hf_1d5c454c4bbad425` | 2/2021/HC-GĐT | V/v khiếu kiện quyết định hành chính | JUDICIAL | [Data/01_raw_data/court_judgments/877203a6751037f004f12ccd58a0041d9c00b47877b990a077a3b530bb87f9e5.pdf](Data/01_raw_data/court_judgments/877203a6751037f004f12ccd58a0041d9c00b47877b990a077a3b530bb87f9e5.pdf) | VERIFIED / ACTIVE |
| `doc_hf_b67c21c82404d45d` | 2/2021/LĐ-ST | V/v: Tranh chấp đơn phương chấm dứt hợp đồng lao động | JUDICIAL | [Data/01_raw_data/court_judgments/4c62b30feb1e7e8a5c51ebda35eaf07dc4d9a968eed4e58881a0c77ec878c1cf.pdf](Data/01_raw_data/court_judgments/4c62b30feb1e7e8a5c51ebda35eaf07dc4d9a968eed4e58881a0c77ec878c1cf.pdf) | VERIFIED / ACTIVE |
| `doc_hf_8c225aeb3ac7b8dc` | 2/2023/LĐ-GĐT | V/v “Đơn phương chấm dứt hợp đồng lao động; vay tài sản; đòi lại tài sản” | JUDICIAL | [Data/01_raw_data/court_judgments/735fbbdf3b8c6409661cf2a3830f506d59bd1ae0582a249082d6c4186465bd21.pdf](Data/01_raw_data/court_judgments/735fbbdf3b8c6409661cf2a3830f506d59bd1ae0582a249082d6c4186465bd21.pdf) | VERIFIED / ACTIVE |
| `doc_hf_360812f6de75ada6` | 273/2021/HC-PT | V/v Khởi kiện quyết định hành chính. | JUDICIAL | [Data/01_raw_data/court_judgments/75420501c339c057812bf1dcf5eccfb2541f938b90ebdbab73dd0e2bc95a6211.pdf](Data/01_raw_data/court_judgments/75420501c339c057812bf1dcf5eccfb2541f938b90ebdbab73dd0e2bc95a6211.pdf) | VERIFIED / ACTIVE |
| `doc_hf_49a5f1ef23dd03a6` | 290/2021/HC-PT | V/v Khiếu kiện quyết định hành chính | JUDICIAL | [Data/01_raw_data/court_judgments/bc8f7803ae60b475780b16b62f0776f99bfbf95dac52d64a0402af3ed44c2073.pdf](Data/01_raw_data/court_judgments/bc8f7803ae60b475780b16b62f0776f99bfbf95dac52d64a0402af3ed44c2073.pdf) | VERIFIED / ACTIVE |
| `doc_hf_9e769e7eed70d931` | 3/2023/LĐ-GĐT | V/v “Tranh ch ấp x ử lý k ỷ luật lao động; chấm dứt hợp đồng lao động” | JUDICIAL | [Data/01_raw_data/court_judgments/bbb4532c6030b76b7acbccb981edf2b5ff51d93e09099408d9b82b5715585af4.pdf](Data/01_raw_data/court_judgments/bbb4532c6030b76b7acbccb981edf2b5ff51d93e09099408d9b82b5715585af4.pdf) | VERIFIED / ACTIVE |
| `doc_hf_cdd0ad14bfc840eb` | 5/2022/LĐ-PT | V/v Đơn phương chấm dứt hợp đồng lao động. | JUDICIAL | [Data/01_raw_data/court_judgments/e5987463ae48c79dbb5a6020d31ed58aee5d5b3cd9377d379a2d78c048a2a531.pdf](Data/01_raw_data/court_judgments/e5987463ae48c79dbb5a6020d31ed58aee5d5b3cd9377d379a2d78c048a2a531.pdf) | VERIFIED / ACTIVE |
| `doc_hf_5d0eaa2b1e774b79` | 53/2022/HC-PT | V/v khiếu kiện quyết định hành chính | JUDICIAL | [Data/01_raw_data/court_judgments/b505c773fd04237ca1eeee9664f9cdd1ee862a3e4d5cb1e38527def057a5bd26.pdf](Data/01_raw_data/court_judgments/b505c773fd04237ca1eeee9664f9cdd1ee862a3e4d5cb1e38527def057a5bd26.pdf) | VERIFIED / ACTIVE |
| `doc_hf_81793f6bcbf5f6bc` | 917/2022/HC-PT | V/v khiếu kiện qu ết ịn n c n | JUDICIAL | [Data/01_raw_data/court_judgments/58c788ed4141c67aabe9ab0711252f972d68c0343cf6fe3a6a68f469fccc8ef2.pdf](Data/01_raw_data/court_judgments/58c788ed4141c67aabe9ab0711252f972d68c0343cf6fe3a6a68f469fccc8ef2.pdf) | VERIFIED / QUARANTINE |
| `doc_hf_75f08303ccde4c61` | 95/2023/HC-PT | V/v “Khiếu kiện quyết định hành chính trong lĩnh vực bảo hiểm xã hội”. | JUDICIAL | [Data/01_raw_data/court_judgments/72d09850af54edb9e3018e3611742e302788a85cfe701f0891ae0c6798ce3637.pdf](Data/01_raw_data/court_judgments/72d09850af54edb9e3018e3611742e302788a85cfe701f0891ae0c6798ce3637.pdf) | VERIFIED / ACTIVE |
| `doc_224d0de9edada97e` | 01/2014/NQ-HĐTP | Hướng dẫn thi hành một số quy định của Luật Trọng tài thương mại | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/f6c86888a5103b55d6de0e28de80f76dd694e3aad890793366f07e034c9cb3cb.pdf](Data/01_raw_data/legal_documents/f6c86888a5103b55d6de0e28de80f76dd694e3aad890793366f07e034c9cb3cb.pdf) | VERIFIED / QUARANTINE |
| `doc_a2d10b27624aa7f0` | 01/2019/NQ-HĐTP | Hướng dẫn áp dụng một số quy định của pháp luật về lãi, lãi suất, phạt vi phạm | LEGAL_DOCUMENT | SOURCE_MISSING | VERIFIED / QUARANTINE |
| `doc_065302d4e48ec7a8` | 06/2020/TT-BLĐTBXH | Ban hành Danh mục công việc có yêu cầu nghiêm ngặt về an toàn, vệ sinh lao động | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/66a2ad19e6b8cdaa8ee6bf3e4cc32765a206d2f007ac8c89af65f4cab51de5fe.pdf](Data/01_raw_data/legal_documents/66a2ad19e6b8cdaa8ee6bf3e4cc32765a206d2f007ac8c89af65f4cab51de5fe.pdf) | VERIFIED / ACTIVE |
| `doc_46de83d52d280724` | 08/2023/TT-BLĐTBXH | Sửa đổi, bổ sung, bãi bỏ một số điều của các Thông tư, Thông tư liên tịch có quy định liên quan đến giấy tờ về cư trú khi thực hiện thủ tục hành chính | LEGAL_DOCUMENT | SOURCE_MISSING | VERIFIED / QUARANTINE |
| `doc_88c028fd122cc91f` | 08/2026/TT-BNV | Quy định chi tiết và hướng dẫn thi hành một số điều của Nghị định số 337/2025/NĐ-CP về hợp đồng lao động điện tử | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/679d32cf5ed9c3fdaae14b6428a054b62fee7af0a51404126b3214a60fe715a7.pdf](Data/01_raw_data/legal_documents/679d32cf5ed9c3fdaae14b6428a054b62fee7af0a51404126b3214a60fe715a7.pdf) | VERIFIED / ACTIVE |
| `doc_6d600abcb6d8afda` | 09/2020/TT-BLĐTBXH | Quy định chi tiết và hướng dẫn thi hành một số điều của Bộ luật Lao động về lao động chưa thành niên | LEGAL_DOCUMENT | SOURCE_MISSING | VERIFIED / QUARANTINE |
| `doc_32bb226053f3fd6c` | 09/2026/TT-BNV | Sửa đổi, bổ sung một số điều của Thông tư số 21/2021/TT-BLĐTBXH về người lao động Việt Nam đi làm việc ở nước ngoài theo hợp đồng | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/a432cb3add48d3461caf3247f2086f0ae43c101e4f8dd8520ccb9cc136358411.pdf](Data/01_raw_data/legal_documents/a432cb3add48d3461caf3247f2086f0ae43c101e4f8dd8520ccb9cc136358411.pdf) | VERIFIED / ACTIVE |
| `doc_d56381af16e3cd69` | 10/2012/QH13 | Bộ luật Lao động | LEGAL_DOCUMENT | SOURCE_MISSING | VERIFIED / QUARANTINE |
| `doc_42cd56772f3e0b93` | 10/2020/TT-BLĐTBXH | Quy định chi tiết và hướng dẫn thi hành một số điều của Bộ luật Lao động về hợp đồng lao động, Hội đồng thương lượng tập thể và nghề, công việc có ảnh hưởng xấu tới chức năng sinh sản, nuôi con | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/b2b7e891bec31090825b93cd4e3d582cb4c779d34aca5e32e5234d9023efc853.pdf](Data/01_raw_data/legal_documents/b2b7e891bec31090825b93cd4e3d582cb4c779d34aca5e32e5234d9023efc853.pdf) | VERIFIED / ACTIVE |
| `doc_d0da32fc356d9ac1` | 10/2024/NĐ-CP | Quy định về khu công nghệ cao | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/9bbb4c8c7ee072b5b79d89e2444ef6a8686922557719b0138881bd39c6fd30fe.pdf](Data/01_raw_data/legal_documents/9bbb4c8c7ee072b5b79d89e2444ef6a8686922557719b0138881bd39c6fd30fe.pdf) | VERIFIED / QUARANTINE |
| `doc_cb2f08d549926d92` | 105/2026/NĐ-CP | Quy định chi tiết và hướng dẫn thi hành một số điều của Luật Công đoàn về tài chính công đoàn | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/3e09eee0f94cf44243c8b06e2cb3e86d68d0646cf041f28950fabedff339e5a1.pdf](Data/01_raw_data/legal_documents/3e09eee0f94cf44243c8b06e2cb3e86d68d0646cf041f28950fabedff339e5a1.pdf) | VERIFIED / QUARANTINE |
| `doc_160c80c72e5b8d1d` | 11/2020/TT-BLĐTBXH | Ban hành danh mục nghề, công việc nặng nhọc, độc hại, nguy hiểm và nghề, công việc đặc biệt nặng nhọc, độc hại, nguy hiểm | LEGAL_DOCUMENT | SOURCE_MISSING | VERIFIED / QUARANTINE |
| `doc_ca13a6074afc8089` | 112/2021/NĐ-CP | Quy định chi tiết một số điều và biện pháp thi hành Luật Người lao động Việt Nam đi làm việc ở nước ngoài theo hợp đồng | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/64f90db071566152494a173c5ddcff719af7b81a3585c82ca7b896f03aaf380b.pdf](Data/01_raw_data/legal_documents/64f90db071566152494a173c5ddcff719af7b81a3585c82ca7b896f03aaf380b.pdf) | VERIFIED / QUARANTINE |
| `doc_2bc424bbc2d91f61` | 113/2025/QH15 | Luật Dân số | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/792cc0e350fd37e05e38fed76341bab9479b239313af3e6e57926d18d6b085af.pdf](Data/01_raw_data/legal_documents/792cc0e350fd37e05e38fed76341bab9479b239313af3e6e57926d18d6b085af.pdf) | VERIFIED / QUARANTINE |
| `doc_0822cf08e3311d68` | 12/2022/NĐ-CP | Quy định xử phạt vi phạm hành chính trong lĩnh vực lao động, bảo hiểm xã hội, người lao động Việt Nam đi làm việc ở nước ngoài theo hợp đồng | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/6773588aae73522a26ddb1d5e5f4551912008e0ac62fcc0117be3e95e79c7ae1.pdf](Data/01_raw_data/legal_documents/6773588aae73522a26ddb1d5e5f4551912008e0ac62fcc0117be3e95e79c7ae1.pdf) | VERIFIED / QUARANTINE |
| `doc_ba532d4bf6c52138` | 124/2025/QH15 | Luật Giáo dục nghề nghiệp | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/4d5837340a71715a8b9c474632a4ea060cb4ddca69b9919b875db652762f1127.pdf](Data/01_raw_data/legal_documents/4d5837340a71715a8b9c474632a4ea060cb4ddca69b9919b875db652762f1127.pdf) | VERIFIED / ACTIVE |
| `doc_561ecdac819eb64a` | 128/2025/NĐ-CP | Quy định về phân quyền, phân cấp trong quản lý nhà nước lĩnh vực nội vụ | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/011851d005cf33fa0f47cf4602e08be98e0c7abd3d068105934752007e5a8f84.pdf](Data/01_raw_data/legal_documents/011851d005cf33fa0f47cf4602e08be98e0c7abd3d068105934752007e5a8f84.pdf) | VERIFIED / QUARANTINE |
| `doc_a7ba0e20b95e0938` | 129/2025/NĐ-CP | Quy định về phân định thẩm quyền của chính quyền địa phương 02 cấp trong lĩnh vực quản lý nhà nước của Bộ Nội vụ | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/72735be7240c01db3c66295ea27161d92e6f87432e48459f6284f94d60c1a113.pdf](Data/01_raw_data/legal_documents/72735be7240c01db3c66295ea27161d92e6f87432e48459f6284f94d60c1a113.pdf) | VERIFIED / QUARANTINE |
| `doc_fe3b7ff048970f11` | 135/2020/NĐ-CP | Quy định về tuổi nghỉ hưu | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/3e14d2e05041c12b6d71739dbb958015e0604eed41a6432e6d8cbbd4276455b0.pdf](Data/01_raw_data/legal_documents/3e14d2e05041c12b6d71739dbb958015e0604eed41a6432e6d8cbbd4276455b0.pdf) | VERIFIED / QUARANTINE |
| `doc_148666f895612c09` | 140/2018/NĐ-CP | Sửa đổi, bổ sung các Nghị định liên quan đến điều kiện đầu tư kinh doanh và thủ tục hành chính thuộc phạm vi quản lý nhà nước của Bộ Lao động - Thương binh và Xã hội | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/f53ea03405c28b95c8782a16bf65bd9aa3c4ce4c4d979e56b568654fbb13586c.pdf](Data/01_raw_data/legal_documents/f53ea03405c28b95c8782a16bf65bd9aa3c4ce4c4d979e56b568654fbb13586c.pdf) | VERIFIED / QUARANTINE |
| `doc_c35ce37b0a91928e` | 145/2020/NĐ-CP | Quy định chi tiết và hướng dẫn thi hành một số điều của Bộ luật Lao động về điều kiện lao động và quan hệ lao động | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/a72a1ff295cc06fbddd335d57257688f5118826968cd8b62d58f50fc1554b230.pdf](Data/01_raw_data/legal_documents/a72a1ff295cc06fbddd335d57257688f5118826968cd8b62d58f50fc1554b230.pdf) | VERIFIED / QUARANTINE |
| `doc_b00669e192090f4d` | 152/2020/NĐ-CP | Quy định về người lao động nước ngoài làm việc tại Việt Nam và tuyển dụng, quản lý người lao động Việt Nam làm việc cho tổ chức, cá nhân nước ngoài tại Việt Nam | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/f7f2c507fee5d05fbc0f0152e4ab309f3a6ebfb82b1bd3a646b75a3661cc26ef.pdf](Data/01_raw_data/legal_documents/f7f2c507fee5d05fbc0f0152e4ab309f3a6ebfb82b1bd3a646b75a3661cc26ef.pdf) | VERIFIED / QUARANTINE |
| `doc_2806de3dbf27e14d` | 158/2025/NĐ-CP | Quy định chi tiết và hướng dẫn thi hành một số điều của Luật Bảo hiểm xã hội về bảo hiểm xã hội bắt buộc | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/51b605010600da706432e19bf80bada5f099f66806e3eba502517febc70d3e95.pdf](Data/01_raw_data/legal_documents/51b605010600da706432e19bf80bada5f099f66806e3eba502517febc70d3e95.pdf) | VERIFIED / QUARANTINE |
| `doc_71164ef9d5c12b3a` | 159/2025/NĐ-CP | Quy định chi tiết và hướng dẫn thi hành một số điều của Luật Bảo hiểm xã hội về bảo hiểm xã hội tự nguyện | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/8ffa3bdce6cdf2856eb0ffd21161dc194d38f8c7ff88114b91e24ea3d7b07b36.pdf](Data/01_raw_data/legal_documents/8ffa3bdce6cdf2856eb0ffd21161dc194d38f8c7ff88114b91e24ea3d7b07b36.pdf) | VERIFIED / QUARANTINE |
| `doc_f08509139d2d0a6e` | 176/2025/NĐ-CP | Quy định chi tiết và hướng dẫn thi hành một số điều của Luật Bảo hiểm xã hội về trợ cấp hưu trí xã hội | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/7942675d74c11a731cc3d57fcbd218e85aad853b7e881fbe0988ad2d2edfeb3f.pdf](Data/01_raw_data/legal_documents/7942675d74c11a731cc3d57fcbd218e85aad853b7e881fbe0988ad2d2edfeb3f.pdf) | VERIFIED / QUARANTINE |
| `doc_7f06c613431a5acb` | 18/2021/TT-BLĐTBXH | Quy định về thời giờ làm việc, thời giờ nghỉ ngơi đối với người lao động làm các công việc sản xuất có tính thời vụ, công việc gia công theo đơn đặt hàng | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/dac0bce70e502e765045a6bd4c95719515b0b802f83a2cf424432758115d740f.pdf](Data/01_raw_data/legal_documents/dac0bce70e502e765045a6bd4c95719515b0b802f83a2cf424432758115d740f.pdf) | VERIFIED / ACTIVE |
| `doc_b07dd68758d0414e` | 21/2021/TT-BLĐTBXH | Quy định chi tiết một số điều của Luật Người lao động Việt Nam đi làm việc ở nước ngoài theo hợp đồng | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/2630af5e30afd39e376e85ca1426ca55f9f2a8a829459a0358b6c232d3251c94.pdf](Data/01_raw_data/legal_documents/2630af5e30afd39e376e85ca1426ca55f9f2a8a829459a0358b6c232d3251c94.pdf) | VERIFIED / QUARANTINE |
| `doc_36b9f21ffaf1d5b5` | 219/2025/NĐ-CP | Quy định về người lao động nước ngoài làm việc tại Việt Nam | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/4757806c7e00ecdee50184e7c563732ca83bb559ea74f2d5578458efb42a184f.pdf](Data/01_raw_data/legal_documents/4757806c7e00ecdee50184e7c563732ca83bb559ea74f2d5578458efb42a184f.pdf) | VERIFIED / QUARANTINE |
| `doc_80d3fb54e832516b` | 24/2022/TT-BLĐTBXH | Quy định việc bồi dưỡng bằng hiện vật đối với người lao động làm việc trong điều kiện có yếu tố nguy hiểm, yếu tố có hại | LEGAL_DOCUMENT | SOURCE_MISSING | VERIFIED / QUARANTINE |
| `doc_659442ab3ede5f4a` | 274/2025/NĐ-CP | Quy định chi tiết một số điều của Luật Bảo hiểm xã hội về chậm đóng, trốn đóng bảo hiểm xã hội bắt buộc, bảo hiểm thất nghiệp; khiếu nại, tố cáo về bảo hiểm xã hội | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/41364e90f8f10f76755ea52fcfd95aba75c53a3e1a1d1ab76f7a945842ec93ac.pdf](Data/01_raw_data/legal_documents/41364e90f8f10f76755ea52fcfd95aba75c53a3e1a1d1ab76f7a945842ec93ac.pdf) | VERIFIED / QUARANTINE |
| `doc_fa0ed18b5101fdd6` | 28/2015/NĐ-CP | Quy định chi tiết thi hành một số điều của Luật Việc làm về bảo hiểm thất nghiệp | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/8b6011860f8ef102d023a73285ad62c2f8ef4ac1edac4ade94c52d5fecc8192c.pdf](Data/01_raw_data/legal_documents/8b6011860f8ef102d023a73285ad62c2f8ef4ac1edac4ade94c52d5fecc8192c.pdf) | VERIFIED / QUARANTINE |
| `doc_d06b5e780447c4ae` | 293/2025/NĐ-CP | Quy định mức lương tối thiểu đối với người lao động làm việc theo hợp đồng lao động | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/80d2943b04403338a18af20c488b4f215a98b023046ba3775f99b6e2cda93169.pdf](Data/01_raw_data/legal_documents/80d2943b04403338a18af20c488b4f215a98b023046ba3775f99b6e2cda93169.pdf) | VERIFIED / QUARANTINE |
| `doc_3af2f4d28b5a10dd` | 326/2016/UBTVQH14 | Quy định về mức thu, miễn, giảm, thu, nộp, quản lý và sử dụng án phí và lệ phí Tòa án | LEGAL_DOCUMENT | SOURCE_MISSING | VERIFIED / QUARANTINE |
| `doc_c9179c6b36474436` | 326/2016/UBTVQH14 | Quy định về mức thu, miễn, giảm, thu, nộp, quản lý và sử dụng án phí và lệ phí Tòa án | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/25b7fa97779eb34fbb8b32cc04d5a980a22d23280ae308a0f485ccfb4aa101f8.pdf](Data/01_raw_data/legal_documents/25b7fa97779eb34fbb8b32cc04d5a980a22d23280ae308a0f485ccfb4aa101f8.pdf) | VERIFIED / QUARANTINE |
| `doc_b749939edf056942` | 337/2025/NĐ-CP | Quy định về hợp đồng lao động điện tử | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/9a04efe6691b871da3cc8ac3fd6984cc9425040a973f27990e6a3081d9d76fec.pdf](Data/01_raw_data/legal_documents/9a04efe6691b871da3cc8ac3fd6984cc9425040a973f27990e6a3081d9d76fec.pdf) | VERIFIED / QUARANTINE |
| `doc_1df49d89022b7631` | 338/2025/NĐ-CP | Quy định chi tiết một số điều của Luật Việc làm về chính sách hỗ trợ việc làm | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/5fa5c87a9803435523b6cc3ee5d5c05bc9f6f1caa75fb8d727afc181ef435093.pdf](Data/01_raw_data/legal_documents/5fa5c87a9803435523b6cc3ee5d5c05bc9f6f1caa75fb8d727afc181ef435093.pdf) | VERIFIED / QUARANTINE |
| `doc_7a130e559bb0e6d0` | 35/2022/NĐ-CP | Quy định về quản lý khu công nghiệp và khu kinh tế | LEGAL_DOCUMENT | SOURCE_MISSING | VERIFIED / QUARANTINE |
| `doc_405efb719d3a82e9` | 372/2025/NĐ-CP | Sửa đổi, bổ sung một số điều của Nghị định số 112/2021/NĐ-CP | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/18128cd9fede4032054d95fc5f783f019460e4e3461d0bc746ac9c27e1b1ae04.pdf](Data/01_raw_data/legal_documents/18128cd9fede4032054d95fc5f783f019460e4e3461d0bc746ac9c27e1b1ae04.pdf) | VERIFIED / QUARANTINE |
| `doc_5fbf008bf9f2adc3` | 374/2025/NĐ-CP | Quy định chi tiết một số điều của Luật Việc làm về bảo hiểm thất nghiệp | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/4d315f8dabefed454759302b45921f4c02e9fc3426682a377306b8982918e3dd.pdf](Data/01_raw_data/legal_documents/4d315f8dabefed454759302b45921f4c02e9fc3426682a377306b8982918e3dd.pdf) | VERIFIED / QUARANTINE |
| `doc_99da5ab0803c65bc` | 38/2013/QH13 | Luật Việc làm | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/7384f7ace45493621d03f3b1529ec4227d5d9d7e7d721e22ca12513717208104.pdf](Data/01_raw_data/legal_documents/7384f7ace45493621d03f3b1529ec4227d5d9d7e7d721e22ca12513717208104.pdf) | VERIFIED / QUARANTINE |
| `doc_bf0a50c5df805780` | 38/2022/NĐ-CP | Quy định mức lương tối thiểu đối với người lao động làm việc theo hợp đồng lao động | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/092677375bf6b34e861c9ee5fd55e8fb8de1a11a9aa8ecc01967b5c1f3684341.pdf](Data/01_raw_data/legal_documents/092677375bf6b34e861c9ee5fd55e8fb8de1a11a9aa8ecc01967b5c1f3684341.pdf) | VERIFIED / QUARANTINE |
| `doc_2acaf84788b4bda7` | 39/2016/NĐ-CP | Quy định chi tiết thi hành một số điều của Luật An toàn, vệ sinh lao động | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/1bf9d48b13e76a3525b6014d9df1692999a5f457ad52136c9f4f773cd607da66.pdf](Data/01_raw_data/legal_documents/1bf9d48b13e76a3525b6014d9df1692999a5f457ad52136c9f4f773cd607da66.pdf) | VERIFIED / QUARANTINE |
| `doc_7df196c7887fe1d0` | 41/2024/QH15 | Luật Bảo hiểm xã hội | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/d46db2fcf0a62e8d788f99612071c881bbba0e00ec1992691f615fd1832ce8e8.pdf](Data/01_raw_data/legal_documents/d46db2fcf0a62e8d788f99612071c881bbba0e00ec1992691f615fd1832ce8e8.pdf) | VERIFIED / ACTIVE |
| `doc_de1deaf033386c6d` | 44/2016/NĐ-CP | Quy định chi tiết một số điều của Luật An toàn, vệ sinh lao động về kiểm định kỹ thuật, huấn luyện và quan trắc môi trường lao động | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/533e22a7d88811de684c9e33ca9129c2253e35e7298725ae1ff2a7319945e7d8.pdf](Data/01_raw_data/legal_documents/533e22a7d88811de684c9e33ca9129c2253e35e7298725ae1ff2a7319945e7d8.pdf) | VERIFIED / QUARANTINE |
| `doc_614eb60e302f7530` | 45/2019/QH14 | Bộ luật Lao động | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/a0c0fcc3b9e040bcdcab70191f0ab95952183be94a6f5bc6424e8d3e85697139.pdf](Data/01_raw_data/legal_documents/a0c0fcc3b9e040bcdcab70191f0ab95952183be94a6f5bc6424e8d3e85697139.pdf) | VERIFIED / QUARANTINE |
| `doc_70a82d034d3458b9` | 50/2024/QH15 | Luật Công đoàn | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/7ddcbdf2d1db00d6d6a85ca01a086f8ed729ad3bba8d24a31913aec84965314e.pdf](Data/01_raw_data/legal_documents/7ddcbdf2d1db00d6d6a85ca01a086f8ed729ad3bba8d24a31913aec84965314e.pdf) | VERIFIED / ACTIVE |
| `doc_436595929326bf14` | 58/2014/QH13 | Luật Bảo hiểm xã hội | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/10dc17ba706c03f93a5b9f0aae492a3e637a09b480cbf01c7e12d6ffc547e64d.pdf](Data/01_raw_data/legal_documents/10dc17ba706c03f93a5b9f0aae492a3e637a09b480cbf01c7e12d6ffc547e64d.pdf) | VERIFIED / QUARANTINE |
| `doc_4dbbe01c66e5d948` | 69/2020/QH14 | Luật Người lao động Việt Nam đi làm việc ở nước ngoài theo hợp đồng | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/da46b079e36457b4eb2c56b8650d66a93d09428e6646353b0685a97351093af6.pdf](Data/01_raw_data/legal_documents/da46b079e36457b4eb2c56b8650d66a93d09428e6646353b0685a97351093af6.pdf) | VERIFIED / QUARANTINE |
| `doc_3c40f2024e74c15c` | 70/2023/NĐ-CP | Sửa đổi, bổ sung một số điều của Nghị định số 152/2020/NĐ-CP về người lao động nước ngoài làm việc tại Việt Nam | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/323f28188f977c0f04cf1fa8937e37fd80659928d0598ce04ccf6a11ba5f9d0e.pdf](Data/01_raw_data/legal_documents/323f28188f977c0f04cf1fa8937e37fd80659928d0598ce04ccf6a11ba5f9d0e.pdf) | VERIFIED / QUARANTINE |
| `doc_3443ed0cc50d91d9` | 71/2025/QH15 | Luật Công nghiệp công nghệ số | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/b80b983f152d364e0e3299d20ef967cb646df5d75d02caff661d1dd88ae8df51.pdf](Data/01_raw_data/legal_documents/b80b983f152d364e0e3299d20ef967cb646df5d75d02caff661d1dd88ae8df51.pdf) | VERIFIED / QUARANTINE |
| `doc_84d0d02b8a84d52d` | 74/2024/NĐ-CP | Quy định mức lương tối thiểu đối với người lao động làm việc theo hợp đồng lao động | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/416134481b357b75ad2fbd1365e631d525007801483deaf1e19fdf63f3b344fb.pdf](Data/01_raw_data/legal_documents/416134481b357b75ad2fbd1365e631d525007801483deaf1e19fdf63f3b344fb.pdf) | VERIFIED / QUARANTINE |
| `doc_7eb6a9b70196c053` | 74/2025/QH15 | Luật Việc làm | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/64bc8c066afea584cd661d040b5e12996e797a9dc7460f1a0c12181d98f7e3c7.pdf](Data/01_raw_data/legal_documents/64bc8c066afea584cd661d040b5e12996e797a9dc7460f1a0c12181d98f7e3c7.pdf) | VERIFIED / QUARANTINE |
| `doc_44f75f41a7ed712f` | 84/2015/QH13 | Luật An toàn, vệ sinh lao động | LEGAL_DOCUMENT | SOURCE_MISSING | VERIFIED / QUARANTINE |
| `doc_5bb4f56057cfa139` | 88/2020/NĐ-CP | Quy định chi tiết và hướng dẫn thi hành một số điều của Luật An toàn, vệ sinh lao động về bảo hiểm tai nạn lao động, bệnh nghề nghiệp bắt buộc | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/19d43e580e3072ec64dff4893cb6d2ee00eb6924a632f400f38d21ec17e562b5.pdf](Data/01_raw_data/legal_documents/19d43e580e3072ec64dff4893cb6d2ee00eb6924a632f400f38d21ec17e562b5.pdf) | VERIFIED / QUARANTINE |
| `doc_7871a0fdba039eca` | 90/2019/NĐ-CP | Quy định mức lương tối thiểu vùng đối với người lao động làm việc theo hợp đồng lao động | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/aa61975fb17282b4b6aa2cf0a1ff28d05cf6901e142c3d366acf4700e2c299df.pdf](Data/01_raw_data/legal_documents/aa61975fb17282b4b6aa2cf0a1ff28d05cf6901e142c3d366acf4700e2c299df.pdf) | VERIFIED / QUARANTINE |
| `doc_4c035479f2272926` | 92/2015/QH13 | Bộ luật Tố tụng dân sự | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/fea333cfe1e69659cca3999e0f7678d9f74beb2bc6c8190e3144450fed7558c2.pdf](Data/01_raw_data/legal_documents/fea333cfe1e69659cca3999e0f7678d9f74beb2bc6c8190e3144450fed7558c2.pdf) | VERIFIED / QUARANTINE |
| `doc_a3dcedc7afa1646e` | 97/2025/QH15 | Luật sửa đổi, bổ sung một số điều của Luật Mặt trận Tổ quốc Việt Nam, Luật Công đoàn, Luật Thanh niên và Luật Thực hiện dân chủ ở cơ sở | LEGAL_DOCUMENT | [Data/01_raw_data/legal_documents/53d8067effa9b6dfb906c3ba201a70339e632082bad3893a1dc8500d6db9df3c.pdf](Data/01_raw_data/legal_documents/53d8067effa9b6dfb906c3ba201a70339e632082bad3893a1dc8500d6db9df3c.pdf) | VERIFIED / QUARANTINE |
| `doc_2a69c6093219f719` |  | Vietnam ratifications NORMLEX | SUPPLEMENTARY | SOURCE_MISSING | REVIEW_REQUIRED / QUARANTINE |
| `doc_2c22d3859448f105` |  | Vietnam Labour Code 2019 EN UNOFFICIAL NATLEX | SUPPLEMENTARY | SOURCE_MISSING | REVIEW_REQUIRED / QUARANTINE |
| `doc_4f0b04303dd2fb0d` |  | BHXHVN 1674-QD-BHXH 2752025 | SUPPLEMENTARY | [Data/01_raw_data/legal_documents/e8c3b0c6f686b22d3e71b6db642e8f8eda6aee820dcbceab8b5948527d1356c1.pdf](Data/01_raw_data/legal_documents/e8c3b0c6f686b22d3e71b6db642e8f8eda6aee820dcbceab8b5948527d1356c1.pdf) | VERIFIED / ACTIVE |
| `doc_5bf27c772a1e62c2` |  | BHXHVN 1455-QD-BHXH 1252025 | SUPPLEMENTARY | [Data/01_raw_data/legal_documents/84c7ac6f1744b21ea8820b29d512bed75fceb53ff45e7b96e9952ad3fc59da95.pdf](Data/01_raw_data/legal_documents/84c7ac6f1744b21ea8820b29d512bed75fceb53ff45e7b96e9952ad3fc59da95.pdf) | VERIFIED / ACTIVE |
| `doc_668bd02d8dd29a11` |  | BHXHVN 422-QD-BHXH 2752026 | SUPPLEMENTARY | [Data/01_raw_data/legal_documents/0d23d35e150f816f5d6fbaa59ce11d3d3bce2093557c372f8ccd99fdfe0d7841.pdf](Data/01_raw_data/legal_documents/0d23d35e150f816f5d6fbaa59ce11d3d3bce2093557c372f8ccd99fdfe0d7841.pdf) | VERIFIED / ACTIVE |
| `doc_755c71a7aa094da0` |  | MOHA 2026 Huong-dan-HDLD-dien-tu | SUPPLEMENTARY | SOURCE_MISSING | VERIFIED / QUARANTINE |
| `doc_c0751635bdf9df85` |  | 2222 QD-BHXH 667488 | SUPPLEMENTARY | SOURCE_MISSING | REVIEW_REQUIRED / QUARANTINE |
| `doc_c52e6736b6516d15` |  | MOHA Nen-tang-hop-dong-lao-dong-dien-tu | SUPPLEMENTARY | SOURCE_MISSING | VERIFIED / QUARANTINE |
| `doc_c880c8739c80c721` |  | 313 QD-BHXH 700242 | SUPPLEMENTARY | SOURCE_MISSING | REVIEW_REQUIRED / QUARANTINE |
| `doc_f09dca861179b57e` |  | BHXHVN 366-QD-BHXH 2942026 | SUPPLEMENTARY | [Data/01_raw_data/legal_documents/c51c8df4896683d5d477cf1c5ea7c369637c13e85bad6d369d032127412a9d3c.pdf](Data/01_raw_data/legal_documents/c51c8df4896683d5d477cf1c5ea7c369637c13e85bad6d369d032127412a9d3c.pdf) | REVIEW_REQUIRED / QUARANTINE |

### 13.2. Quy mô từng thư mục

Số tệp và dung lượng tính cả các thư mục con; không cộng các dòng cha/con với nhau.

| Thư mục | Tệp | Byte | MiB |
|---|---:|---:|---:|
| `Data/` | 421 | 374,977,620 | 357.61 |
| `Data/01_raw_data/` | 85 | 157,738,593 | 150.43 |
| `Data/01_raw_data/court_judgments/` | 25 | 10,242,762 | 9.77 |
| `Data/01_raw_data/legal_documents/` | 57 | 146,596,966 | 139.81 |
| `Data/01_raw_data/legal_precedents/` | 3 | 898,865 | 0.86 |
| `Data/02_processed_data/` | 9 | 64,527,866 | 61.54 |
| `Data/02_processed_data/converted_docx/` | 4 | 3,420,592 | 3.26 |
| `Data/03_case_data/` | 1 | 6,103,452 | 5.82 |
| `Data/04_metadata/` | 3 | 3,215,432 | 3.07 |
| `Data/05_provenance/` | 99 | 28,763,326 | 27.43 |
| `Data/05_provenance/page_previews/` | 96 | 18,650,314 | 17.79 |
| `Data/06_temporal/` | 3 | 10,624,808 | 10.13 |
| `Data/07_research_extensions/` | 218 | 99,456,305 | 94.85 |
| `Data/07_research_extensions/extraction_review/` | 180 | 67,607,887 | 64.48 |
| `Data/07_research_extensions/extraction_review/images/` | 174 | 38,912,860 | 37.11 |
| `Data/07_research_extensions/knowledge/` | 10 | 27,478,468 | 26.21 |
| `Data/07_research_extensions/legacy_quality_reports/` | 23 | 3,456,072 | 3.30 |
| `Data/07_research_extensions/legacy_review_queues/` | 1 | 63,673 | 0.06 |
| `Data/08_Url/` | 3 | 4,547,838 | 4.34 |

### 13.3. Cây toàn bộ 421 tệp

Bao gồm từng PDF, DOCX, PNG và tệp dữ liệu. Con số trong ngoặc là dung lượng byte, không phải số dòng.

```text
Data/
  01_raw_data/
    court_judgments/
      08608899dba38a8a020f27f2e342ed8bdf1bc05f2d79840d90585f00c57b7fea.pdf (548,047 bytes)
      3c5d7964c2cab23a1e2ac90bfe1a872a31c548277eb96e409e98c08f0e7e3b01.pdf (332,723 bytes)
      4c62b30feb1e7e8a5c51ebda35eaf07dc4d9a968eed4e58881a0c77ec878c1cf.pdf (459,690 bytes)
      58c788ed4141c67aabe9ab0711252f972d68c0343cf6fe3a6a68f469fccc8ef2.pdf (490,321 bytes)
      5b719cd3c895afe9c0974f0af65ad5292ae63ecc8b6d4636e77bb491940b6392.pdf (429,860 bytes)
      5d3f060f19acaea3745a80698732ecd5b561e05ad7667d6fdfc9dcd6601c8612.pdf (377,403 bytes)
      65b98fa01981cfc1622d3d3b5b8f0382019ec3ec9c8a0da6fb60dd9d75517aa5.pdf (413,046 bytes)
      72d09850af54edb9e3018e3611742e302788a85cfe701f0891ae0c6798ce3637.pdf (449,181 bytes)
      735fbbdf3b8c6409661cf2a3830f506d59bd1ae0582a249082d6c4186465bd21.pdf (438,267 bytes)
      75420501c339c057812bf1dcf5eccfb2541f938b90ebdbab73dd0e2bc95a6211.pdf (504,735 bytes)
      7915ba4206b65572bbd24309de9ad43b151b5f835f5007c2dd6088adfd20d755.pdf (242,149 bytes)
      877203a6751037f004f12ccd58a0041d9c00b47877b990a077a3b530bb87f9e5.pdf (451,080 bytes)
      98b2fbc1ac163ce0629132076830c2591f7c134b0698db6935c7ab6312fb6d6f.pdf (288,587 bytes)
      9e6f84b22b96c5c3ad72876de67bd6f0fa77878571e8e901f38ca4b3e2b25a74.pdf (605,590 bytes)
      9fb43f3725e912b72e244e9b03bd6836ab053c29ca7445077ae6fc3fa0b00692.pdf (412,994 bytes)
      b1e32da8ccd53981a5cab144565574e7c98ce4bb9918f0a41e6cfe69ae7798e1.pdf (434,980 bytes)
      b505c773fd04237ca1eeee9664f9cdd1ee862a3e4d5cb1e38527def057a5bd26.pdf (572,177 bytes)
      b9c3596cf9ad672724158518e60b4aca96783d77b7ec68e90994a835ea528bfe.pdf (427,787 bytes)
      bbb4532c6030b76b7acbccb981edf2b5ff51d93e09099408d9b82b5715585af4.pdf (425,765 bytes)
      bc8f7803ae60b475780b16b62f0776f99bfbf95dac52d64a0402af3ed44c2073.pdf (266,374 bytes)
      c34750e61927fc705e6dd111e3191182541ee355b22ce8afda5939ccce7ad3ee.pdf (397,310 bytes)
      d3bed9996dc3ee41d5d1205986e99f97e054210b78cce44494db6e68cd3edd25.pdf (348,651 bytes)
      e5987463ae48c79dbb5a6020d31ed58aee5d5b3cd9377d379a2d78c048a2a531.pdf (247,416 bytes)
      f371c16b1b94284a440285d2021eb066d59055af9d2510e011d30d8cb3827505.pdf (403,424 bytes)
      f8040f0720da7eb186dfd2f17b1887d31ca5278e8491db7384bb62e6eee90e5b.pdf (275,205 bytes)
    legal_documents/
      011851d005cf33fa0f47cf4602e08be98e0c7abd3d068105934752007e5a8f84.pdf (2,109,145 bytes)
      092677375bf6b34e861c9ee5fd55e8fb8de1a11a9aa8ecc01967b5c1f3684341.pdf (458,768 bytes)
      0d23d35e150f816f5d6fbaa59ce11d3d3bce2093557c372f8ccd99fdfe0d7841.pdf (6,790,132 bytes)
      10dc17ba706c03f93a5b9f0aae492a3e637a09b480cbf01c7e12d6ffc547e64d.pdf (1,829,177 bytes)
      18128cd9fede4032054d95fc5f783f019460e4e3461d0bc746ac9c27e1b1ae04.pdf (834,270 bytes)
      19d43e580e3072ec64dff4893cb6d2ee00eb6924a632f400f38d21ec17e562b5.pdf (2,653,319 bytes)
      1bf9d48b13e76a3525b6014d9df1692999a5f457ad52136c9f4f773cd607da66.pdf (2,786,004 bytes)
      25b7fa97779eb34fbb8b32cc04d5a980a22d23280ae308a0f485ccfb4aa101f8.pdf (22,904,742 bytes)
      2630af5e30afd39e376e85ca1426ca55f9f2a8a829459a0358b6c232d3251c94.pdf (1,021,490 bytes)
      323f28188f977c0f04cf1fa8937e37fd80659928d0598ce04ccf6a11ba5f9d0e.pdf (824,237 bytes)
      3e09eee0f94cf44243c8b06e2cb3e86d68d0646cf041f28950fabedff339e5a1.pdf (396,128 bytes)
      3e14d2e05041c12b6d71739dbb958015e0604eed41a6432e6d8cbbd4276455b0.pdf (697,036 bytes)
      41364e90f8f10f76755ea52fcfd95aba75c53a3e1a1d1ab76f7a945842ec93ac.pdf (685,537 bytes)
      416134481b357b75ad2fbd1365e631d525007801483deaf1e19fdf63f3b344fb.pdf (396,544 bytes)
      4757806c7e00ecdee50184e7c563732ca83bb559ea74f2d5578458efb42a184f.pdf (1,409,245 bytes)
      4d315f8dabefed454759302b45921f4c02e9fc3426682a377306b8982918e3dd.pdf (2,164,611 bytes)
      4d5837340a71715a8b9c474632a4ea060cb4ddca69b9919b875db652762f1127.pdf (8,083,314 bytes)
      51b605010600da706432e19bf80bada5f099f66806e3eba502517febc70d3e95.pdf (2,276,739 bytes)
      533e22a7d88811de684c9e33ca9129c2253e35e7298725ae1ff2a7319945e7d8.pdf (2,678,400 bytes)
      53d8067effa9b6dfb906c3ba201a70339e632082bad3893a1dc8500d6db9df3c.pdf (445,225 bytes)
      5fa5c87a9803435523b6cc3ee5d5c05bc9f6f1caa75fb8d727afc181ef435093.pdf (1,472,285 bytes)
      64bc8c066afea584cd661d040b5e12996e797a9dc7460f1a0c12181d98f7e3c7.pdf (1,230,315 bytes)
      64f90db071566152494a173c5ddcff719af7b81a3585c82ca7b896f03aaf380b.pdf (2,211,844 bytes)
      66a2ad19e6b8cdaa8ee6bf3e4cc32765a206d2f007ac8c89af65f4cab51de5fe.pdf (764,518 bytes)
      6773588aae73522a26ddb1d5e5f4551912008e0ac62fcc0117be3e95e79c7ae1.pdf (4,766,689 bytes)
      679d32cf5ed9c3fdaae14b6428a054b62fee7af0a51404126b3214a60fe715a7.pdf (560,295 bytes)
      6c2bbf0fba807ac2a3c84320da8bb5c88a4e0b0e264222f75c2fe6d99c1d36b9.pdf (2,671,722 bytes)
      72735be7240c01db3c66295ea27161d92e6f87432e48459f6284f94d60c1a113.pdf (2,106,957 bytes)
      7384f7ace45493621d03f3b1529ec4227d5d9d7e7d721e22ca12513717208104.pdf (1,345,856 bytes)
      792cc0e350fd37e05e38fed76341bab9479b239313af3e6e57926d18d6b085af.pdf (858,166 bytes)
      7942675d74c11a731cc3d57fcbd218e85aad853b7e881fbe0988ad2d2edfeb3f.pdf (381,990 bytes)
      7ddcbdf2d1db00d6d6a85ca01a086f8ed729ad3bba8d24a31913aec84965314e.pdf (260,498 bytes)
      80d2943b04403338a18af20c488b4f215a98b023046ba3775f99b6e2cda93169.pdf (711,910 bytes)
      84c7ac6f1744b21ea8820b29d512bed75fceb53ff45e7b96e9952ad3fc59da95.pdf (3,939,618 bytes)
      8b6011860f8ef102d023a73285ad62c2f8ef4ac1edac4ade94c52d5fecc8192c.pdf (2,156,896 bytes)
      8ffa3bdce6cdf2856eb0ffd21161dc194d38f8c7ff88114b91e24ea3d7b07b36.pdf (760,542 bytes)
      90cf1e79ba6e778fd1bb7702662167372c7f646622edf83efd9851035af642b7.pdf (3,714,646 bytes)
      95349436112a28676c84322b92954c6b820db75be08cb5dea35b8ce8646765a6.pdf (607,971 bytes)
      9a04efe6691b871da3cc8ac3fd6984cc9425040a973f27990e6a3081d9d76fec.pdf (802,003 bytes)
      9bbb4c8c7ee072b5b79d89e2444ef6a8686922557719b0138881bd39c6fd30fe.pdf (2,548,271 bytes)
      9f0ead44c45112ca46882b01036179c23772d1494f7af32024c07f2366a62d27.pdf (2,296,937 bytes)
      a0c0fcc3b9e040bcdcab70191f0ab95952183be94a6f5bc6424e8d3e85697139.pdf (5,232,503 bytes)
      a432cb3add48d3461caf3247f2086f0ae43c101e4f8dd8520ccb9cc136358411.pdf (973,371 bytes)
      a72a1ff295cc06fbddd335d57257688f5118826968cd8b62d58f50fc1554b230.pdf (6,176,759 bytes)
      aa61975fb17282b4b6aa2cf0a1ff28d05cf6901e142c3d366acf4700e2c299df.pdf (480,803 bytes)
      b2b7e891bec31090825b93cd4e3d582cb4c779d34aca5e32e5234d9023efc853.pdf (711,689 bytes)
      b80b983f152d364e0e3299d20ef967cb646df5d75d02caff661d1dd88ae8df51.pdf (1,355,976 bytes)
      c51c8df4896683d5d477cf1c5ea7c369637c13e85bad6d369d032127412a9d3c.pdf (7,023,429 bytes)
      d46db2fcf0a62e8d788f99612071c881bbba0e00ec1992691f615fd1832ce8e8.pdf (1,311,363 bytes)
      da46b079e36457b4eb2c56b8650d66a93d09428e6646353b0685a97351093af6.pdf (3,234,360 bytes)
      dac0bce70e502e765045a6bd4c95719515b0b802f83a2cf424432758115d740f.pdf (1,063,192 bytes)
      e8c3b0c6f686b22d3e71b6db642e8f8eda6aee820dcbceab8b5948527d1356c1.pdf (5,481,528 bytes)
      f53ea03405c28b95c8782a16bf65bd9aa3c4ce4c4d979e56b568654fbb13586c.pdf (3,850,911 bytes)
      f6c86888a5103b55d6de0e28de80f76dd694e3aad890793366f07e034c9cb3cb.pdf (595,792 bytes)
      f7f2c507fee5d05fbc0f0152e4ab309f3a6ebfb82b1bd3a646b75a3661cc26ef.pdf (2,892,930 bytes)
      fea333cfe1e69659cca3999e0f7678d9f74beb2bc6c8190e3144450fed7558c2.pdf (6,301,431 bytes)
      file_a6a61feef6d93b80.pdf (2,296,937 bytes)
    legal_precedents/
      14f91b3f764c0eaa558533fe1ba8848904d2b2daf02981a8753d811ea19e71d6.pdf (463,668 bytes)
      3b451fd330f9583d4468767cc1d503898f259084ee4e690f08226e85c93e78d1.pdf (419,118 bytes)
      ab9da143e8423cc76363a0b194cbe660b3bd13f10f94269dfb75fe77c3745dd0.docx (16,079 bytes)
  02_processed_data/
    converted_docx/
      10_laodong.docx (140,778 bytes)
      20-2018-AL.docx (27,169 bytes)
      2222_QD-BHXH_667488.docx (3,205,932 bytes)
      326-2016-UBTVQH14_text_copy.docx (46,713 bytes)
    documents.jsonl (25,572,939 bytes)
    hierarchy_headings.jsonl (251,105 bytes)
    provisions.jsonl (26,855,854 bytes)
    quarantined_provisions.jsonl (776,824 bytes)
    segments.jsonl (7,650,552 bytes)
  03_case_data/
    cases.jsonl (6,103,452 bytes)
  04_metadata/
    documents.jsonl (1,960,382 bytes)
    files_manifest.jsonl (222,956 bytes)
    source_catalog.jsonl (1,032,094 bytes)
  05_provenance/
    page_previews/
      file_0248baa39c8a23a0_p0001.png (281,641 bytes)
      file_0f228744c9eebe6a_p0001.png (345,270 bytes)
      file_1826728e1b8cc0bc_p0001.png (250,101 bytes)
      file_1f2f266d24d84ad1_p0018.png (139,161 bytes)
      file_1f2f266d24d84ad1_p0020.png (143,428 bytes)
      file_1f2f266d24d84ad1_p0022.png (139,691 bytes)
      file_1f2f266d24d84ad1_p0024.png (154,827 bytes)
      file_1f2f266d24d84ad1_p0026.png (167,910 bytes)
      file_1f2f266d24d84ad1_p0028.png (146,705 bytes)
      file_1f2f266d24d84ad1_p0030.png (143,237 bytes)
      file_1f2f266d24d84ad1_p0032.png (121,402 bytes)
      file_292514e5c977295c_p0001.png (272,857 bytes)
      file_2a0cd45d1cb362ea_p0016.png (147,457 bytes)
      file_2a0cd45d1cb362ea_p0017.png (120,309 bytes)
      file_2a0cd45d1cb362ea_p0018.png (119,314 bytes)
      file_2a0cd45d1cb362ea_p0019.png (130,007 bytes)
      file_2a0cd45d1cb362ea_p0020.png (153,557 bytes)
      file_2a0cd45d1cb362ea_p0028.png (99,367 bytes)
      file_2a0cd45d1cb362ea_p0029.png (81,086 bytes)
      file_2a0cd45d1cb362ea_p0030.png (108,207 bytes)
      file_2a0cd45d1cb362ea_p0031.png (101,360 bytes)
      file_2a0cd45d1cb362ea_p0032.png (73,192 bytes)
      file_35c16b5d43c47f4b_p0001.png (244,774 bytes)
      file_3d70c9693cc380cf_p0001.png (198,806 bytes)
      file_3e49a66f27ab4280_p0001.png (293,861 bytes)
      file_435deed8a2667740_p0001.png (233,511 bytes)
      file_47c02d7626cfe612_p0001.png (282,740 bytes)
      file_4b275c29430b3226_p0001.png (281,404 bytes)
      file_4f8fc163d71198d6_p0001.png (287,801 bytes)
      file_57667e7c531c27d8_p0001.png (240,607 bytes)
      file_57f7984b0b7f1df5_p0001.png (294,753 bytes)
      file_57f7984b0b7f1df5_p0002.png (256,543 bytes)
      file_57f7984b0b7f1df5_p0003.png (286,802 bytes)
      file_57f7984b0b7f1df5_p0004.png (273,384 bytes)
      file_57f7984b0b7f1df5_p0005.png (305,492 bytes)
      file_57f7984b0b7f1df5_p0006.png (262,796 bytes)
      file_57f7984b0b7f1df5_p0007.png (282,782 bytes)
      file_57f7984b0b7f1df5_p0008.png (329,891 bytes)
      file_57f7984b0b7f1df5_p0009.png (328,881 bytes)
      file_57f7984b0b7f1df5_p0010.png (297,667 bytes)
      file_57f7984b0b7f1df5_p0011.png (311,399 bytes)
      file_57f7984b0b7f1df5_p0012.png (327,301 bytes)
      file_5ab5ca0c5c4db876_p0001.png (247,716 bytes)
      file_5c0031c48badae76_p0001.png (224,966 bytes)
      file_75f0b3fd93648884_p0001.png (269,412 bytes)
      file_7812e1c0e91e5b04_p0001.png (260,176 bytes)
      file_7c9330389ad66857_p0001.png (246,177 bytes)
      file_8116aa30cc61641f_p0001.png (250,487 bytes)
      file_85382a6487ec076f_p0115.png (125,346 bytes)
      file_85382a6487ec076f_p0118.png (171,828 bytes)
      file_85382a6487ec076f_p0119.png (162,527 bytes)
      file_85382a6487ec076f_p0120.png (125,751 bytes)
      file_85382a6487ec076f_p0121.png (69,152 bytes)
      file_85382a6487ec076f_p0122.png (44,273 bytes)
      file_85382a6487ec076f_p0128.png (114,957 bytes)
      file_85382a6487ec076f_p0133.png (124,523 bytes)
      file_85382a6487ec076f_p0135.png (137,801 bytes)
      file_85382a6487ec076f_p0136.png (155,373 bytes)
      file_85382a6487ec076f_p0137.png (185,581 bytes)
      file_85382a6487ec076f_p0138.png (117,653 bytes)
      file_8a2bf4f655fd7d1b_p0001.png (283,869 bytes)
      file_8a2bf4f655fd7d1b_p0025.png (187,593 bytes)
      file_8a2bf4f655fd7d1b_p0026.png (329,516 bytes)
      file_9671271fdbbc6c80_p0001.png (228,394 bytes)
      file_a8f894e40a8eb15d_p0001.png (283,083 bytes)
      file_aebf389f9ee23975_p0001.png (292,266 bytes)
      file_af62e9ef6cc30fd7_p0001.png (198,582 bytes)
      file_b013aaa83aea2a39_p0005.png (5,223 bytes)
      file_b2cced5eb37da954_p0001.png (245,402 bytes)
      file_b73aaa3979139e25_p0001.png (239,369 bytes)
      file_b8d64c68a959fff0_p0013.png (106,621 bytes)
      file_b8d64c68a959fff0_p0014.png (120,632 bytes)
      file_b8d64c68a959fff0_p0015.png (105,777 bytes)
      file_b8d64c68a959fff0_p0016.png (131,523 bytes)
      file_b8d64c68a959fff0_p0017.png (151,979 bytes)
      file_b8d64c68a959fff0_p0018.png (108,271 bytes)
      file_b8d64c68a959fff0_p0019.png (122,033 bytes)
      file_b8d64c68a959fff0_p0020.png (119,582 bytes)
      file_b8d64c68a959fff0_p0021.png (155,869 bytes)
      file_b8d64c68a959fff0_p0029.png (154,465 bytes)
      file_b8d64c68a959fff0_p0032.png (97,624 bytes)
      file_b8d64c68a959fff0_p0033.png (115,567 bytes)
      file_bd091e2e0e0c69d6_p0001.png (233,617 bytes)
      file_bfccff56f6cded6d_p0001.png (257,868 bytes)
      file_c3ebb517cbe5e66e_p0010.png (109,445 bytes)
      file_c3ebb517cbe5e66e_p0011.png (78,653 bytes)
      file_c3ebb517cbe5e66e_p0012.png (83,638 bytes)
      file_c3ebb517cbe5e66e_p0014.png (88,660 bytes)
      file_d314e96799881135_p0001.png (199,783 bytes)
      file_e013480ad499fe62_p0001.png (281,926 bytes)
      file_e0ab4475e9f51c1c_p0001.png (241,345 bytes)
      file_e16d4c3191353d85_p0001.png (199,495 bytes)
      file_e29448f3fa3c56a5_p0001.png (239,973 bytes)
      file_ecc65e5488f38890_p0001.png (264,613 bytes)
      file_f5e6d48206fe9666_p0001.png (250,468 bytes)
      file_fd69cf6107ce2a8b_p0001.png (242,610 bytes)
    footnotes.jsonl (115,072 bytes)
    page_audit.jsonl (2,994,935 bytes)
    provision_spans.jsonl (7,003,005 bytes)
  06_temporal/
    legal_changes.jsonl (22,411 bytes)
    provision_identities.jsonl (2,554,758 bytes)
    provision_versions.jsonl (8,047,639 bytes)
  07_research_extensions/
    extraction_review/
      images/
        file_0248baa39c8a23a0_p0001.png (327,369 bytes)
        file_0f228744c9eebe6a_p0001.png (379,096 bytes)
        file_1826728e1b8cc0bc_p0001.png (290,343 bytes)
        file_1f2f266d24d84ad1_p0018.png (165,638 bytes)
        file_1f2f266d24d84ad1_p0020.png (168,886 bytes)
        file_1f2f266d24d84ad1_p0022.png (164,158 bytes)
        file_1f2f266d24d84ad1_p0024.png (183,102 bytes)
        file_1f2f266d24d84ad1_p0026.png (196,538 bytes)
        file_1f2f266d24d84ad1_p0028.png (175,150 bytes)
        file_1f2f266d24d84ad1_p0030.png (167,642 bytes)
        file_1f2f266d24d84ad1_p0032.png (142,984 bytes)
        file_292514e5c977295c_p0001.png (320,559 bytes)
        file_2a0cd45d1cb362ea_p0016.png (169,478 bytes)
        file_2a0cd45d1cb362ea_p0017.png (139,997 bytes)
        file_2a0cd45d1cb362ea_p0018.png (139,606 bytes)
        file_2a0cd45d1cb362ea_p0019.png (148,789 bytes)
        file_2a0cd45d1cb362ea_p0020.png (180,634 bytes)
        file_2a0cd45d1cb362ea_p0028.png (114,059 bytes)
        file_2a0cd45d1cb362ea_p0029.png (92,191 bytes)
        file_2a0cd45d1cb362ea_p0030.png (121,922 bytes)
        file_2a0cd45d1cb362ea_p0031.png (118,923 bytes)
        file_2a0cd45d1cb362ea_p0032.png (87,905 bytes)
        file_35c16b5d43c47f4b_p0001.png (267,989 bytes)
        file_3d70c9693cc380cf_p0001.png (223,288 bytes)
        file_3e49a66f27ab4280_p0001.png (326,399 bytes)
        file_435deed8a2667740_p0001.png (257,583 bytes)
        file_47c02d7626cfe612_p0001.png (309,330 bytes)
        file_4b275c29430b3226_p0001.png (327,872 bytes)
        file_4f8fc163d71198d6_p0001.png (317,502 bytes)
        file_57667e7c531c27d8_p0001.png (267,064 bytes)
        file_57f7984b0b7f1df5_p0001.png (345,880 bytes)
        file_57f7984b0b7f1df5_p0002.png (296,780 bytes)
        file_57f7984b0b7f1df5_p0003.png (335,295 bytes)
        file_57f7984b0b7f1df5_p0004.png (320,166 bytes)
        file_57f7984b0b7f1df5_p0005.png (357,441 bytes)
        file_57f7984b0b7f1df5_p0006.png (306,532 bytes)
        file_57f7984b0b7f1df5_p0007.png (329,013 bytes)
        file_57f7984b0b7f1df5_p0008.png (385,648 bytes)
        file_57f7984b0b7f1df5_p0009.png (383,263 bytes)
        file_57f7984b0b7f1df5_p0010.png (348,590 bytes)
        file_57f7984b0b7f1df5_p0011.png (364,296 bytes)
        file_57f7984b0b7f1df5_p0012.png (381,145 bytes)
        file_57f7984b0b7f1df5_p0013.png (289,441 bytes)
        file_57f7984b0b7f1df5_p0014.png (381,513 bytes)
        file_57f7984b0b7f1df5_p0015.png (365,075 bytes)
        file_57f7984b0b7f1df5_p0016.png (400,678 bytes)
        file_57f7984b0b7f1df5_p0017.png (381,542 bytes)
        file_57f7984b0b7f1df5_p0018.png (362,856 bytes)
        file_57f7984b0b7f1df5_p0019.png (274,624 bytes)
        file_57f7984b0b7f1df5_p0020.png (359,098 bytes)
        file_57f7984b0b7f1df5_p0021.png (354,166 bytes)
        file_57f7984b0b7f1df5_p0022.png (343,640 bytes)
        file_57f7984b0b7f1df5_p0023.png (380,982 bytes)
        file_57f7984b0b7f1df5_p0024.png (370,221 bytes)
        file_57f7984b0b7f1df5_p0025.png (395,319 bytes)
        file_57f7984b0b7f1df5_p0026.png (403,854 bytes)
        file_57f7984b0b7f1df5_p0027.png (382,169 bytes)
        file_57f7984b0b7f1df5_p0028.png (387,058 bytes)
        file_57f7984b0b7f1df5_p0029.png (351,041 bytes)
        file_57f7984b0b7f1df5_p0030.png (402,757 bytes)
        file_57f7984b0b7f1df5_p0031.png (370,548 bytes)
        file_57f7984b0b7f1df5_p0032.png (205,373 bytes)
        file_57f7984b0b7f1df5_p0033.png (154,874 bytes)
        file_57f7984b0b7f1df5_p0035.png (238,150 bytes)
        file_57f7984b0b7f1df5_p0037.png (221,210 bytes)
        file_57f7984b0b7f1df5_p0038.png (109,522 bytes)
        file_57f7984b0b7f1df5_p0039.png (190,907 bytes)
        file_57f7984b0b7f1df5_p0040.png (237,954 bytes)
        file_57f7984b0b7f1df5_p0041.png (220,086 bytes)
        file_57f7984b0b7f1df5_p0043.png (161,191 bytes)
        file_57f7984b0b7f1df5_p0044.png (172,349 bytes)
        file_57f7984b0b7f1df5_p0045.png (77,755 bytes)
        file_57f7984b0b7f1df5_p0047.png (285,454 bytes)
        file_57f7984b0b7f1df5_p0049.png (108,604 bytes)
        file_57f7984b0b7f1df5_p0051.png (250,545 bytes)
        file_57f7984b0b7f1df5_p0052.png (153,407 bytes)
        file_57f7984b0b7f1df5_p0053.png (244,631 bytes)
        file_57f7984b0b7f1df5_p0054.png (106,352 bytes)
        file_57f7984b0b7f1df5_p0055.png (225,738 bytes)
        file_57f7984b0b7f1df5_p0057.png (290,999 bytes)
        file_57f7984b0b7f1df5_p0058.png (150,859 bytes)
        file_57f7984b0b7f1df5_p0059.png (289,169 bytes)
        file_57f7984b0b7f1df5_p0060.png (149,449 bytes)
        file_57f7984b0b7f1df5_p0061.png (253,507 bytes)
        file_57f7984b0b7f1df5_p0062.png (148,462 bytes)
        file_57f7984b0b7f1df5_p0063.png (295,615 bytes)
        file_57f7984b0b7f1df5_p0064.png (149,052 bytes)
        file_57f7984b0b7f1df5_p0065.png (227,349 bytes)
        file_57f7984b0b7f1df5_p0066.png (248,814 bytes)
        file_57f7984b0b7f1df5_p0067.png (242,860 bytes)
        file_57f7984b0b7f1df5_p0068.png (141,833 bytes)
        file_57f7984b0b7f1df5_p0069.png (227,061 bytes)
        file_57f7984b0b7f1df5_p0070.png (241,254 bytes)
        file_57f7984b0b7f1df5_p0071.png (149,841 bytes)
        file_57f7984b0b7f1df5_p0073.png (283,363 bytes)
        file_57f7984b0b7f1df5_p0074.png (149,713 bytes)
        file_5ab5ca0c5c4db876_p0001.png (273,121 bytes)
        file_5c0031c48badae76_p0001.png (262,327 bytes)
        file_75f0b3fd93648884_p0001.png (296,859 bytes)
        file_7812e1c0e91e5b04_p0001.png (288,048 bytes)
        file_7c9330389ad66857_p0001.png (272,532 bytes)
        file_8116aa30cc61641f_p0001.png (290,418 bytes)
        file_85382a6487ec076f_p0115.png (145,508 bytes)
        file_85382a6487ec076f_p0118.png (199,379 bytes)
        file_85382a6487ec076f_p0119.png (189,273 bytes)
        file_85382a6487ec076f_p0120.png (145,140 bytes)
        file_85382a6487ec076f_p0121.png (80,796 bytes)
        file_85382a6487ec076f_p0122.png (51,298 bytes)
        file_85382a6487ec076f_p0128.png (132,733 bytes)
        file_85382a6487ec076f_p0133.png (145,973 bytes)
        file_85382a6487ec076f_p0135.png (160,021 bytes)
        file_85382a6487ec076f_p0136.png (179,184 bytes)
        file_85382a6487ec076f_p0137.png (214,055 bytes)
        file_85382a6487ec076f_p0138.png (136,088 bytes)
        file_85382a6487ec076f_p0139.png (152,700 bytes)
        file_85382a6487ec076f_p0140.png (92,465 bytes)
        file_8a2bf4f655fd7d1b_p0001.png (340,792 bytes)
        file_8a2bf4f655fd7d1b_p0025.png (225,834 bytes)
        file_8a2bf4f655fd7d1b_p0026.png (400,749 bytes)
        file_9671271fdbbc6c80_p0001.png (250,084 bytes)
        file_a8f894e40a8eb15d_p0001.png (309,984 bytes)
        file_aebf389f9ee23975_p0001.png (327,569 bytes)
        file_af62e9ef6cc30fd7_p0001.png (232,456 bytes)
        file_b013aaa83aea2a39_p0005.png (6,344 bytes)
        file_b2cced5eb37da954_p0001.png (270,991 bytes)
        file_b73aaa3979139e25_p0001.png (263,220 bytes)
        file_b8d64c68a959fff0_p0013.png (125,814 bytes)
        file_b8d64c68a959fff0_p0014.png (140,251 bytes)
        file_b8d64c68a959fff0_p0015.png (123,330 bytes)
        file_b8d64c68a959fff0_p0016.png (152,465 bytes)
        file_b8d64c68a959fff0_p0017.png (180,228 bytes)
        file_b8d64c68a959fff0_p0018.png (123,201 bytes)
        file_b8d64c68a959fff0_p0019.png (142,728 bytes)
        file_b8d64c68a959fff0_p0020.png (139,161 bytes)
        file_b8d64c68a959fff0_p0021.png (182,939 bytes)
        file_b8d64c68a959fff0_p0029.png (175,974 bytes)
        file_b8d64c68a959fff0_p0032.png (116,458 bytes)
        file_b8d64c68a959fff0_p0033.png (136,334 bytes)
        file_b8d64c68a959fff0_p0035.png (109,411 bytes)
        file_b8d64c68a959fff0_p0036.png (113,033 bytes)
        file_b8d64c68a959fff0_p0037.png (124,004 bytes)
        file_b8d64c68a959fff0_p0038.png (119,848 bytes)
        file_b8d64c68a959fff0_p0039.png (133,968 bytes)
        file_b8d64c68a959fff0_p0040.png (189,107 bytes)
        file_b8d64c68a959fff0_p0041.png (131,172 bytes)
        file_b8d64c68a959fff0_p0042.png (118,918 bytes)
        file_b8d64c68a959fff0_p0043.png (164,217 bytes)
        file_b8d64c68a959fff0_p0044.png (162,129 bytes)
        file_b8d64c68a959fff0_p0046.png (152,883 bytes)
        file_b8d64c68a959fff0_p0048.png (187,246 bytes)
        file_b8d64c68a959fff0_p0049.png (149,949 bytes)
        file_b8d64c68a959fff0_p0051.png (157,018 bytes)
        file_b8d64c68a959fff0_p0053.png (191,327 bytes)
        file_b8d64c68a959fff0_p0054.png (171,442 bytes)
        file_b8d64c68a959fff0_p0055.png (149,695 bytes)
        file_b8d64c68a959fff0_p0057.png (156,870 bytes)
        file_b8d64c68a959fff0_p0059.png (191,870 bytes)
        file_b8d64c68a959fff0_p0060.png (169,443 bytes)
        file_b8d64c68a959fff0_p0061.png (136,103 bytes)
        file_b8d64c68a959fff0_p0062.png (98,434 bytes)
        file_bd091e2e0e0c69d6_p0001.png (261,184 bytes)
        file_bfccff56f6cded6d_p0001.png (304,526 bytes)
        file_c3ebb517cbe5e66e_p0010.png (125,076 bytes)
        file_c3ebb517cbe5e66e_p0011.png (91,361 bytes)
        file_c3ebb517cbe5e66e_p0012.png (97,439 bytes)
        file_c3ebb517cbe5e66e_p0014.png (100,888 bytes)
        file_d314e96799881135_p0001.png (221,497 bytes)
        file_e013480ad499fe62_p0001.png (327,363 bytes)
        file_e0ab4475e9f51c1c_p0001.png (271,182 bytes)
        file_e16d4c3191353d85_p0001.png (232,224 bytes)
        file_e29448f3fa3c56a5_p0001.png (267,102 bytes)
        file_ecc65e5488f38890_p0001.png (296,290 bytes)
        file_f5e6d48206fe9666_p0001.png (290,978 bytes)
        file_fd69cf6107ce2a8b_p0001.png (266,910 bytes)
      documents_with_annotations.jsonl (25,409,724 bytes)
      footnote_evidence.jsonl (115,072 bytes)
      index.html (1,454,658 bytes)
      page_review_evidence.jsonl (1,669,435 bytes)
      page_review_tracking.csv (45,491 bytes)
      summary.json (647 bytes)
    knowledge/
      accepted_relation_edges.jsonl (1,415,039 bytes)
      citations.jsonl (3,081,941 bytes)
      communities.json (38,634 bytes)
      diagnostic_checklists.jsonl (15,654,590 bytes)
      diagnostic_review_queue.jsonl (76,594 bytes)
      issue_edges.jsonl (4,610,641 bytes)
      issue_review_queue.jsonl (1,152,567 bytes)
      issues.jsonl (2,033 bytes)
      relation_edges.jsonl (1,430,734 bytes)
      relation_review_queue.jsonl (15,695 bytes)
    legacy_quality_reports/
      ai_provider_preflight.json (398 bytes)
      aura_preflight.json (265 bytes)
      build_issues.jsonl (0 bytes)
      case_ai_quality.json (289,511 bytes)
      checklist_ai_failures.jsonl (95,654 bytes)
      checklist_ai_quality.json (157,238 bytes)
      dense_request.json (1,707 bytes)
      dense_validation.json (2,480 bytes)
      extraction_integrity.json (78 bytes)
      extraction_issues.jsonl (0 bytes)
      final_outputs_validation.json (6,265 bytes)
      kaggle_run.json (514,088 bytes)
      neo4j_validation.json (209 bytes)
      offline_ai_progress.json (218 bytes)
      offline_ai_requests.jsonl (1,825,433 bytes)
      offline_build_progress.json (137 bytes)
      offline_final_run.json (514,324 bytes)
      page_extraction_quality.json (499 bytes)
      parsing_issues.jsonl (13,284 bytes)
      provision_provenance_integrity.json (177 bytes)
      structure_coverage.json (18,868 bytes)
      summary.json (1,735 bytes)
      validation_issues.jsonl (13,504 bytes)
    legacy_review_queues/
      source_review_queue.jsonl (63,673 bytes)
    extension_catalog.jsonl (80,439 bytes)
    huggingface_selected_records.jsonl (576,593 bytes)
    inheritance_locations.jsonl (124,525 bytes)
    legal_metadata_observations.jsonl (68,648 bytes)
  08_Url/
    source_catalog.jsonl (1,032,094 bytes)
    url.csv (1,545,021 bytes)
    url_verification.jsonl (1,970,723 bytes)
```

### 13.4. Từ điển cấu trúc tệp

Danh sách trường là hợp của các khóa cấp ngoài trong từng tệp; không có nghĩa mỗi dòng đều có đủ trường. Các object/list bên trong cần đọc theo schema và evidence của bản ghi. JSONL rỗng là tệp 0 bản ghi, không phải lỗi đọc.

#### `Data/02_processed_data/documents.jsonl`

107 records; JSONL (1 JSON object / line).

Fields: `file_id`, `extension`, `size_bytes`, `sha256`, `source_group`, `document_type_hint`, `binding_default`, `extraction_method`, `text`, `text_chars`, `needs_ocr`, `extraction_error`, `page_provenance`, `page_count`, `extraction_schema`, `text_source`, `non_body_annotations`, `page_content_schema`, `content_selection_schema`, `extraction_settings_fingerprint`, `document_id`, `legacy_path_references`, `local_file_path`, `local_source_status`, `text_alignment`, `source_markdown_sha256`, `normalization`, `provenance_status`, `source_doc_name`, `source_revision`.

#### `Data/02_processed_data/hierarchy_headings.jsonl`

562 records; JSONL (1 JSON object / line).

Fields: `id`, `label`, `number`, `title`, `document_id`, `segment_id`, `coordinate_space`, `segment_char_start`, `segment_char_end`, `line_start`, `line_end`, `document_char_start`, `document_char_end`, `page_start`, `page_end`, `page_status`, `provenance_status`.

#### `Data/02_processed_data/provisions.jsonl`

18,871 records; JSONL (1 JSON object / line).

Fields: `provision_id`, `document_id`, `level`, `number`, `heading`, `text`, `parent_id`, `order`, `chapter`, `section`, `segment_id`, `segment_type`, `article_number`, `clause_number`, `point_number`, `canonical_path`, `char_start`, `char_end`, `line_start`, `line_end`, `span_scope`, `valid_from`, `valid_to`, `coordinate_space`, `segment_char_start`, `segment_char_end`, `document_char_start`, `document_char_end`, `page_start`, `page_end`, `page_status`, `source_unit_type`, `provenance_status`, `provision_identity_id`, `provision_version_id`, `source_document_version_id`, `temporal_status`, `temporal_evidence`, `introduced_by_change_id`, `ended_by_change_id`, `provision_temporal_verified`, `temporal_coverage_as_of`, `excluded_annotation_spans`.

#### `Data/02_processed_data/quarantined_provisions.jsonl`

896 records; JSONL (1 JSON object / line).

Fields: `provision_id`, `document_id`, `level`, `number`, `heading`, `text`, `parent_id`, `order`, `article_number`, `segment_id`, `segment_type`, `clause_number`, `point_number`, `canonical_path`, `char_start`, `char_end`, `line_start`, `line_end`, `span_scope`, `valid_from`, `valid_to`, `chapter`, `section`.

#### `Data/02_processed_data/segments.jsonl`

512 records; JSONL (1 JSON object / line).

Fields: `segment_id`, `document_id`, `segment_type`, `text`, `line_start`, `line_end`, `heading`, `document_char_start`, `document_char_end`, `provenance_status`, `source_doc_name`.

#### `Data/03_case_data/cases.jsonl`

36 records; JSONL (1 JSON object / line).

Fields: `case_id`, `document_id`, `case_number`, `case_type`, `court`, `decision_date`, `dispute`, `facts`, `reasoning`, `decision`, `features`, `search_text`, `title`, `official_document_id`, `citations_law`, `full_text`, `case_sections_review_status`, `record_classification`, `local_file_path`, `local_source_status`, `source_url`, `download_url`, `verification_status`, `source_references`, `old_url`, `url_review_status`, `url_reviewed_at`, `url_validation_level`, `url_review_reason`, `url_review_report`, `url_history`, `url_verification_scope`, `graph_status`, `graph_eligible`, `graph_role`, `graph_quarantine_reasons`, `final_audit_run`, `final_audit_at`, `view_url`, `canonical_url`, `official_url`, `source_verified`, `source_content_verified`, `verified_at`, `temporal_facts_enabled`, `case_extracted_facts_enabled`, `final_url_evidence`, `graph_import_policy`, `raw_source_review`, `source_is_official`, `text_alignment`, `precedent_number`, `precedent_relationship_evidence`, `source_title`, `title_evidence`, `legal_domain`, `scope_evidence`, `citations_law_status`, `citations_case`, `provenance`.

#### `Data/04_metadata/documents.jsonl`

107 records; JSONL (1 JSON object / line).

Fields: `document_id`, `file_id`, `document_number`, `title`, `document_type`, `source_group`, `issuer`, `promulgated_date`, `effective_from`, `effective_to`, `status`, `binding`, `canonical_for_text`, `source_url`, `sha256`, `text_chars`, `needs_ocr`, `instrument_number`, `source_document_number`, `case_number`, `cited_document_numbers`, `version_role`, `language`, `authority_rank`, `metadata_verified`, `temporal_verified`, `metadata_evidence`, `instrument_type`, `legal_status`, `consolidation_as_of`, `status_checked_at`, `valid_from`, `valid_to`, `version_id`, `instrument_id`, `record_classification`, `local_file_path`, `local_source_status`, `source_verification_status`, `source_verified`, `canonical_url`, `download_url`, `current_source_url`, `local_sha256`, `source_references`, `temporal_source_verification`, `sha256_semantics`, `legacy_path_references`, `legacy_provenance`, `provenance`, `old_url`, `url_review_status`, `url_reviewed_at`, `url_validation_level`, `url_review_reason`, `url_review_report`, `url_history`, `url_verification_scope`, `graph_status`, `graph_eligible`, `graph_role`, `graph_quarantine_reasons`, `final_audit_run`, `final_audit_at`, `verification_status`, `view_url`, `official_url`, `source_content_verified`, `verified_at`, `temporal_facts_enabled`, `case_extracted_facts_enabled`, `final_url_evidence`, `graph_import_policy`, `final_scope_review`, `final_date_review`, `final_metadata_review_status`, `legacy_provenance_before_final_review`, `raw_source_review`, `verification_evidence`, `source_is_official`, `hf_source_aliases`, `hf_normalized_text_sha256`, `text_alignment`, `last_url_verification`, `final_manual_identity`, `title_evidence`, `source_confidence`, `source_flags`.

#### `Data/04_metadata/files_manifest.jsonl`

191 records; JSONL (1 JSON object / line).

Fields: `file_id`, `document_id`, `document_type`, `source_group`, `source_url`, `verification_status`, `local_file_path`, `path`, `relative_path`, `sha256`, `size_bytes`, `extension`, `source_status`, `expected_original_sha256`, `legacy_manifest`, `record_kind`, `local_source_status`, `view_url`, `official_url`, `download_url`, `old_url`, `url_reviewed_at`, `canonical_url`, `source_verified`, `verified_at`, `url_review_status`, `url_review_reason`, `url_review_report`, `url_history`, `last_url_verification`, `alternative_local_paths`, `document_ids`, `download_urls`, `original_or_derived`, `source_urls`, `final_audit_run`.

#### `Data/04_metadata/source_catalog.jsonl`

107 records; JSONL (1 JSON object / line).

Fields: `document_id`, `document_number`, `title`, `document_type`, `issuer`, `source_group`, `source_url`, `canonical_url`, `download_url`, `original_url`, `alternative_urls`, `source_provider`, `source_verified`, `verification_status`, `verified_at`, `verification_evidence`, `local_file_path`, `local_source_status`, `sha256`, `expected_original_sha256`, `hf_dataset_url`, `hf_revision`, `source_aliases`, `raw_variants`, `legacy_catalog`, `old_url`, `url_review_status`, `url_reviewed_at`, `url_validation_level`, `url_review_reason`, `url_review_report`, `url_history`, `url_verification_scope`, `graph_status`, `graph_eligible`, `graph_role`, `graph_quarantine_reasons`, `final_audit_run`, `final_audit_at`, `view_url`, `official_url`, `source_content_verified`, `temporal_facts_enabled`, `case_extracted_facts_enabled`, `final_url_evidence`, `graph_import_policy`, `raw_source_review`, `official_url_verification_status`, `source_references`, `source_is_official`, `last_url_verification`, `final_manual_identity`.

#### `Data/05_provenance/footnotes.jsonl`

106 records; JSONL (1 JSON object / line).

Fields: `schema`, `kind`, `marker`, `text`, `bbox`, `page_height`, `font_size`, `body_font_size`, `text_sha256`, `page`, `source_sha256`, `document_char_start`, `document_char_end`, `detection_status`, `review_status`, `file_id`, `relative_path`.

#### `Data/05_provenance/page_audit.jsonl`

3,116 records; JSONL (1 JSON object / line).

Fields: `schema`, `file_id`, `relative_path`, `document_sha256`, `page`, `page_source_sha256`, `status`, `reason_codes`, `method`, `ocr_status`, `native_chars`, `selected_chars`, `cleaned_chars`, `image_coverage`, `native_quality`, `native_ocr_agreement`, `preview_path`, `page_review`, `non_body_text_evidence`.

#### `Data/05_provenance/provision_spans.jsonl`

18,871 records; JSONL (1 JSON object / line).

Fields: `provision_id`, `document_id`, `segment_id`, `coordinate_space`, `segment_char_start`, `segment_char_end`, `document_char_start`, `document_char_end`, `page_start`, `page_end`, `page_status`, `source_unit_type`, `provenance_status`.

#### `Data/06_temporal/legal_changes.jsonl`

25 records; JSONL (1 JSON object / line).

Fields: `change_id`, `operation`, `source_document_id`, `source_provision_id`, `target_instrument_id`, `target_provision_identity_id`, `target_article`, `target_clause`, `target_point`, `resolution_scope`, `effective_from`, `temporal_status`, `evidence_text`, `evidence_span`, `evidence_status`, `method`, `confidence`, `resolution_status`, `candidate_count`.

#### `Data/06_temporal/provision_identities.jsonl`

15,651 records; JSONL (1 JSON object / line).

Fields: `provision_identity_id`, `instrument_id`, `canonical_path`, `temporal_status`.

#### `Data/06_temporal/provision_versions.jsonl`

18,871 records; JSONL (1 JSON object / line).

Fields: `provision_version_id`, `provision_identity_id`, `source_document_version_id`, `valid_from`, `valid_to`, `temporal_status`, `temporal_evidence`, `introduced_by_change_id`, `ended_by_change_id`, `provision_temporal_verified`, `temporal_coverage_as_of`.

#### `Data/07_research_extensions/extension_catalog.jsonl`

217 records; JSONL (1 JSON object / line).

Fields: `local_file_path`, `sha256`, `bytes`, `verification_status`, `note`.

#### `Data/07_research_extensions/extraction_review/documents_with_annotations.jsonl`

95 records; JSONL (1 JSON object / line).

Fields: `file_id`, `path`, `relative_path`, `filename`, `extension`, `size_bytes`, `sha256`, `source_group`, `document_type_hint`, `binding_default`, `extraction_method`, `text`, `text_chars`, `needs_ocr`, `extraction_error`, `page_provenance`, `page_count`, `extraction_schema`, `text_source`, `non_body_annotations`, `page_content_schema`, `content_selection_schema`, `extraction_settings_fingerprint`.

#### `Data/07_research_extensions/extraction_review/footnote_evidence.jsonl`

106 records; JSONL (1 JSON object / line).

Fields: `schema`, `kind`, `marker`, `text`, `bbox`, `page_height`, `font_size`, `body_font_size`, `text_sha256`, `page`, `source_sha256`, `document_char_start`, `document_char_end`, `detection_status`, `review_status`, `file_id`, `relative_path`.

#### `Data/07_research_extensions/extraction_review/page_review_evidence.jsonl`

174 records; JSONL (1 JSON object / line).

Fields: `schema`, `file_id`, `relative_path`, `page`, `source_sha256`, `input_archive_sha256`, `original_status`, `review_status`, `reason_codes`, `finding`, `native_text`, `selected_text`, `cleaned_text`, `raw_selected_available`, `comparison_target`, `native_metrics`, `selected_metrics`, `agreement`, `numeric_difference`, `text_diff`, `preview_path`, `page_notes`, `reviewer`, `reviewed_at`, `review_priority`.

#### `Data/07_research_extensions/extraction_review/page_review_tracking.csv`

174 data rows; 13 columns.

Columns: `file_id`, `relative_path`, `page`, `source_sha256`, `review_priority`, `finding`, `preview_path`, `review_status`, `reviewer`, `reviewed_at`, `decision`, `review_evidence`, `corrected_text_path`.

#### `Data/07_research_extensions/extraction_review/summary.json`

Top-level fields:

| Field | Stored type / size |
|---|---|
| `schema_version` | int |
| `input_archive_sha256` | str |
| `expected_suspicious_pages` | int |
| `audited_pages` | int |
| `source_errors` | list (0 items) |
| `raw_selected_available` | int |
| `finding_counts` | dict (4 items) |
| `geometry_aligned_footnotes` | int |
| `auto_approved_pages` | int |
| `complete_evidence_audit` | bool |
| `note` | str |

#### `Data/07_research_extensions/huggingface_selected_records.jsonl`

15 records; JSONL (1 JSON object / line).

Fields: `doc_name`, `source`, `web_url`, `pdf_url`, `official_document_id`, `official_document_id_normalized`, `number`, `year`, `code`, `id_source`, `category`, `instance_level`, `court`, `court_level`, `issued_date`, `date_source`, `precedent_number`, `is_precedent`, `citations_law`, `citations_case`, `citations_source`, `num_law_citations`, `num_case_citations`, `markdown`, `markdown_chars`, `num_pages`, `confidence`, `flags`.

#### `Data/07_research_extensions/inheritance_locations.jsonl`

317 records; JSONL (1 JSON object / line).

Fields: `source_member`, `local_file_path`, `sha256`, `quality_status`, `note`.

#### `Data/07_research_extensions/knowledge/accepted_relation_edges.jsonl`

2,029 records; JSONL (1 JSON object / line).

Fields: `edge_id`, `source_id`, `target_id`, `type`, `confidence`, `evidence`, `evidence_text`, `evidence_span`, `evidence_status`, `resolution_scope`, `method`.

#### `Data/07_research_extensions/knowledge/citations.jsonl`

4,371 records; JSONL (1 JSON object / line).

Fields: `citation_id`, `source_id`, `raw_text`, `evidence_text`, `evidence_span`, `evidence_status`, `status`, `confidence`, `target_id`, `target_article`, `target_clause`, `target_point`, `method`, `candidate_count`.

#### `Data/07_research_extensions/knowledge/communities.json`

Top-level fields:

| Field | Stored type / size |
|---|---|
| `community_nodes` | list (5 items) |
| `edges` | list (67 items) |
| `algorithm` | str |

#### `Data/07_research_extensions/knowledge/diagnostic_checklists.jsonl`

16,241 records; JSONL (1 JSON object / line).

Fields: `checklist_id`, `provision_id`, `source_provision_id`, `provenance_status`, `evidence_span`, `type`, `question`, `source_text`, `evidence_text`, `fact_slots`, `generator`, `confidence`.

#### `Data/07_research_extensions/knowledge/diagnostic_review_queue.jsonl`

130 records; JSONL (1 JSON object / line).

Fields: `checklist_id`, `provision_id`, `source_provision_id`, `type`, `question`, `source_text`, `evidence_text`, `evidence_span`, `provenance_status`, `generator`, `confidence`.

#### `Data/07_research_extensions/knowledge/issue_edges.jsonl`

7,859 records; JSONL (1 JSON object / line).

Fields: `source_id`, `target_id`, `type`, `score`, `evidence`, `evidence_text`, `evidence_span`, `matched_keywords`, `provenance_status`.

#### `Data/07_research_extensions/knowledge/issue_review_queue.jsonl`

3,835 records; JSONL (1 JSON object / line).

Fields: `source_id`, `target_id`, `type`, `score`, `evidence`, `evidence_text`, `evidence_span`, `matched_keywords`, `provenance_status`.

#### `Data/07_research_extensions/knowledge/issues.jsonl`

17 records; JSONL (1 JSON object / line).

Fields: `issue_id`, `issue_key`, `label_vi`.

#### `Data/07_research_extensions/knowledge/relation_edges.jsonl`

2,073 records; JSONL (1 JSON object / line).

Fields: `edge_id`, `source_id`, `target_id`, `type`, `confidence`, `evidence`, `evidence_text`, `evidence_span`, `evidence_status`, `resolution_scope`, `method`.

#### `Data/07_research_extensions/knowledge/relation_review_queue.jsonl`

44 records; JSONL (1 JSON object / line).

Fields: `edge_id`, `source_id`, `target_id`, `type`, `confidence`, `evidence`, `evidence_text`, `evidence_span`, `evidence_status`, `resolution_scope`, `method`.

#### `Data/07_research_extensions/legacy_quality_reports/ai_provider_preflight.json`

Top-level fields:

| Field | Stored type / size |
|---|---|
| `schema_version` | int |
| `passed` | bool |
| `probes` | list (2 items) |
| `note` | str |

#### `Data/07_research_extensions/legacy_quality_reports/aura_preflight.json`

Top-level fields:

| Field | Stored type / size |
|---|---|
| `schema_version` | int |
| `passed` | bool |
| `attempts` | list (1 items) |
| `checked_at` | str |
| `read_only` | bool |
| `endpoint` | str |
| `reason_code` | str |

#### `Data/07_research_extensions/legacy_quality_reports/build_issues.jsonl`

0 records; JSONL (1 JSON object / line).

0 records; no observed fields.

#### `Data/07_research_extensions/legacy_quality_reports/case_ai_quality.json`

Top-level fields:

| Field | Stored type / size |
|---|---|
| `cases` | list (24 items) |
| `total_cases` | int |
| `ai_attempted` | int |
| `ai_cache_hits` | int |
| `ai_succeeded` | int |
| `complete_cases` | int |
| `partial_cases` | int |
| `unprocessed_cases` | int |
| `run_id` | str |
| `ai_fallback` | int |
| `circuit_open` | bool |
| `stop_reason` | NoneType |
| `complete` | bool |

#### `Data/07_research_extensions/legacy_quality_reports/checklist_ai_failures.jsonl`

690 records; JSONL (1 JSON object / line).

Fields: `provision_id`, `reason`, `source_start`, `source_end`, `rejected_items`, `exception_chain`, `http_status`.

#### `Data/07_research_extensions/legacy_quality_reports/checklist_ai_quality.json`

Top-level fields:

| Field | Stored type / size |
|---|---|
| `mode` | str |
| `planned` | int |
| `attempted` | int |
| `network_calls` | int |
| `cache_hits` | int |
| `succeeded` | int |
| `partial` | int |
| `fallback` | int |
| `skipped` | int |
| `records` | list (250 items) |
| `complete` | bool |
| `circuit_open` | bool |
| `stop_reason` | NoneType |

#### `Data/07_research_extensions/legacy_quality_reports/dense_request.json`

Top-level fields:

| Field | Stored type / size |
|---|---|
| `data_dir` | str |
| `output_dir` | str |
| `extraction` | dict (16 items) |
| `cleaning` | dict (3 items) |
| `parsing` | dict (2 items) |
| `knowledge` | dict (11 items) |
| `retrieval` | dict (10 items) |
| `neo4j` | dict (1 items) |
| `project_root` | str |

#### `Data/07_research_extensions/legacy_quality_reports/dense_validation.json`

Top-level fields:

| Field | Stored type / size |
|---|---|
| `dense_passed` | bool |
| `units` | int |
| `dimensions` | int |
| `model` | str |
| `model_path` | str |
| `model_revision` | str |
| `fingerprint` | str |
| `device` | str |
| `fp16` | bool |
| `max_length` | int |
| `batch_size` | int |
| `elapsed_seconds` | float |
| `resumed_units` | int |
| `duplicate_unit_id_rows` | int |
| `checks` | dict (5 items) |
| `queries` | list (3 items) |
| `note` | str |

#### `Data/07_research_extensions/legacy_quality_reports/extraction_integrity.json`

Top-level fields:

| Field | Stored type / size |
|---|---|
| `schema_version` | int |
| `passed` | bool |
| `documents` | int |
| `errors` | list (0 items) |

#### `Data/07_research_extensions/legacy_quality_reports/extraction_issues.jsonl`

0 records; JSONL (1 JSON object / line).

0 records; no observed fields.

#### `Data/07_research_extensions/legacy_quality_reports/final_outputs_validation.json`

Top-level fields:

| Field | Stored type / size |
|---|---|
| `checked_at` | str |
| `ready_for_offline_v1` | bool |
| `stages` | dict (4 items) |
| `checks` | list (36 items) |
| `metadata_gaps` | list (0 items) |
| `legal_quality_evaluation` | dict (3 items) |
| `source_catalog_quality` | dict (4 items) |
| `semantic_quality` | dict (8 items) |
| `gold_build_id` | str |
| `gold_evaluation` | dict (5 items) |
| `offline_ready_for_online` | bool |

#### `Data/07_research_extensions/legacy_quality_reports/kaggle_run.json`

Top-level fields:

| Field | Stored type / size |
|---|---|
| `pipeline_exit_code` | int |
| `stage` | str |
| `started_at` | str |
| `completed_at` | str |
| `technical_validation` | dict (3 items) |
| `ai_enabled` | bool |
| `ai_max_provisions` | int |
| `aura_loaded` | bool |
| `ai_quality` | dict (3 items) |
| `build_id` | str |

#### `Data/07_research_extensions/legacy_quality_reports/neo4j_validation.json`

Top-level fields:

| Field | Stored type / size |
|---|---|
| `passed` | bool |
| `dataset_id` | str |
| `build_id` | str |
| `nodes` | int |
| `edges` | int |
| `mode` | str |

#### `Data/07_research_extensions/legacy_quality_reports/offline_ai_progress.json`

Top-level fields:

| Field | Stored type / size |
|---|---|
| `cases` | int |
| `checklists` | int |
| `completed` | int |
| `edges` | int |
| `mode` | str |
| `nodes` | int |
| `schema_version` | int |
| `stage` | str |
| `total` | int |
| `updated_at` | str |

#### `Data/07_research_extensions/legacy_quality_reports/offline_ai_requests.jsonl`

5,504 records; JSONL (1 JSON object / line).

Fields: `schema_version`, `run_id`, `stage`, `event`, `updated_at`, `cache_key`, `identity`, `input_bytes`, `elapsed_seconds`, `reason`, `exception_chain`, `http_status`.

#### `Data/07_research_extensions/legacy_quality_reports/offline_build_progress.json`

Top-level fields:

| Field | Stored type / size |
|---|---|
| `schema_version` | int |
| `stage` | str |
| `completed` | int |
| `total` | int |
| `updated_at` | str |

#### `Data/07_research_extensions/legacy_quality_reports/offline_final_run.json`

Top-level fields:

| Field | Stored type / size |
|---|---|
| `schema_version` | int |
| `status` | str |
| `stage` | str |
| `updated_at` | str |
| `started_at` | str |
| `ai_enabled` | bool |
| `ai_max_provisions` | int |
| `load_aura` | bool |
| `resume_ai` | bool |
| `technical_validation` | dict (3 items) |
| `technical_checkpoint` | str |
| `completed_at` | str |
| `final_archive` | str |
| `build_id` | str |
| `ai_quality` | dict (3 items) |
| `quality_status` | str |

#### `Data/07_research_extensions/legacy_quality_reports/page_extraction_quality.json`

Top-level fields:

| Field | Stored type / size |
|---|---|
| `schema` | str |
| `documents` | int |
| `pages` | int |
| `status_counts` | dict (3 items) |
| `reason_counts` | dict (4 items) |
| `review_queue_pages` | int |
| `error_pages` | int |
| `passed` | bool |
| `note` | str |

#### `Data/07_research_extensions/legacy_quality_reports/parsing_issues.jsonl`

42 records; JSONL (1 JSON object / line).

Fields: `severity`, `type`, `document_id`, `duplicate_ids`, `quarantined_rows`, `provision_id`.

#### `Data/07_research_extensions/legacy_quality_reports/provision_provenance_integrity.json`

Top-level fields:

| Field | Stored type / size |
|---|---|
| `schema_version` | int |
| `passed` | bool |
| `provisions` | int |
| `errors` | list (0 items) |
| `scope` | str |

#### `Data/07_research_extensions/legacy_quality_reports/structure_coverage.json`

Top-level fields:

| Field | Stored type / size |
|---|---|
| `schema_version` | int |
| `documents` | list (95 items) |
| `provisions` | int |
| `scope` | str |

#### `Data/07_research_extensions/legacy_quality_reports/summary.json`

Top-level fields:

| Field | Stored type / size |
|---|---|
| `documents` | int |
| `provisions` | int |
| `cases` | int |
| `diagnostic_items` | int |
| `graph_nodes` | int |
| `graph_edges` | int |
| `issues_by_type` | dict (4 items) |
| `ready_for_offline_v1` | bool |
| `note` | str |
| `source_catalog_quality` | dict (4 items) |
| `semantic_quality` | dict (8 items) |
| `offline_ready_for_online` | bool |
| `legal_quality_evaluation` | dict (3 items) |
| `gold_evaluation` | dict (5 items) |

#### `Data/07_research_extensions/legacy_quality_reports/validation_issues.jsonl`

44 records; JSONL (1 JSON object / line).

Fields: `severity`, `type`, `message`, `count`, `document_id`, `duplicate_ids`, `quarantined_rows`, `provision_id`.

#### `Data/07_research_extensions/legacy_review_queues/source_review_queue.jsonl`

95 records; JSONL (1 JSON object / line).

Fields: `file_id`, `relative_path`, `sha256`, `source_group`, `source_provider`, `source_url`, `collected_at`, `official_source`, `binding`, `reviewer`, `reviewed_at`, `review_evidence`, `language`, `catalog_status`, `missing_fields`, `review_reason`.

#### `Data/07_research_extensions/legal_metadata_observations.jsonl`

66 records; JSONL (1 JSON object / line).

Fields: `document_id`, `dataset_url`, `dataset_revision`, `source_id`, `match_method`, `source_metadata`, `verification_status`, `action`.

#### `Data/08_Url/source_catalog.jsonl`

107 records; JSONL (1 JSON object / line).

Fields: `document_id`, `document_number`, `title`, `document_type`, `issuer`, `source_group`, `source_url`, `canonical_url`, `download_url`, `original_url`, `alternative_urls`, `source_provider`, `source_verified`, `verification_status`, `verified_at`, `verification_evidence`, `local_file_path`, `local_source_status`, `sha256`, `expected_original_sha256`, `hf_dataset_url`, `hf_revision`, `source_aliases`, `raw_variants`, `legacy_catalog`, `old_url`, `url_review_status`, `url_reviewed_at`, `url_validation_level`, `url_review_reason`, `url_review_report`, `url_history`, `url_verification_scope`, `graph_status`, `graph_eligible`, `graph_role`, `graph_quarantine_reasons`, `final_audit_run`, `final_audit_at`, `view_url`, `official_url`, `source_content_verified`, `temporal_facts_enabled`, `case_extracted_facts_enabled`, `final_url_evidence`, `graph_import_policy`, `raw_source_review`, `official_url_verification_status`, `source_references`, `source_is_official`, `last_url_verification`, `final_manual_identity`.

#### `Data/08_Url/url.csv`

367 data rows; 38 columns.

Columns: `document_id`, `file_id`, `document_number`, `title`, `document_type`, `source_group`, `issuer`, `document_verification_status`, `graph_status`, `url`, `url_roles`, `is_current_view_url`, `is_current_official_url`, `is_current_download_url`, `current_view_url`, `current_official_url`, `current_download_url`, `old_url`, `url_verification_status`, `last_checked_at`, `http_status`, `final_url`, `content_type`, `url_type`, `source_official`, `document_number_match`, `title_match`, `issuer_match`, `content_match`, `response_sha256`, `response_bytes`, `error_reason`, `document_review_reason`, `redirect_chain_json`, `source_locations_json`, `verification_history_json`, `original_document_number`, `original_title`.

#### `Data/08_Url/url_verification.jsonl`

565 records; JSONL (1 JSON object / line).

Fields: `document_id`, `url`, `url_type`, `checked_at`, `http_status`, `final_url`, `redirect_chain`, `content_type`, `document_number_match`, `title_match`, `source_official`, `verification_status`, `evidence`, `error_reason`, `local_file_path`, `response_sha256`, `response_bytes`, `attachment_links`, `pdf_valid`, `page_count`, `official_precedent_evidence`, `sha256`, `downloaded_at`, `download_status`, `docx_valid`, `pdf_status`, `doc_name`, `text_alignment`, `error`, `response_path`, `kind`, `origin`, `old_url`, `review_run`, `issuer_match`, `content_match`, `review_status`, `validation_level`, `search_found`, `http_ok`, `content_match_stage`, `verified`, `court_match`, `decision_date_match`, `case_type_match`, `page_title`, `tls_compatibility`, `previous_attempt`, `run`, `browser_fulltext_eligible`, `parser_correction_note`, `previous_attempts`, `tls_note`, `content_disposition`, `pdf_signature_valid`, `existing_original_path`, `existing_original_sha256`, `exact_original_sha256_match`.
