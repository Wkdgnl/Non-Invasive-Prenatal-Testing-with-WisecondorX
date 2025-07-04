WisecondorX – Hướng dẫn cài đặt và sử dụng
📌 Giới thiệu
WisecondorX là công cụ phân tích số lượng bản sao dựa trên dữ liệu giải trình tự toàn bộ hệ gen (shallow WGS). Công cụ này đặc biệt hiệu quả trong chẩn đoán tiền sản không xâm lấn (NIPT) và phân tích copy number variation (CNV).
WisecondorX cải tiến từ bản gốc WISECONDOR với tốc độ xử lý nhanh hơn, hỗ trợ dự đoán giới tính và nhiễm sắc thể giới.
🧰 Yêu cầu hệ thống & môi trường
Hệ điều hành: Linux hoặc WSL (Ubuntu)
Python ≥ 3.6
R (khuyên dùng R v4.3.3)

Công cụ liên quan:
bwa, samtools, fasterq-dump, prefetch
conda để quản lý môi trường

📦 Thư viện cần thiết
Python: scikit-learn, numpy, pandas, matplotlib, pysam, scipy
R: jsonlite, DNAcopy

⚙️ Cài đặt WisecondorX
✅ Cài qua pip (phiên bản mới nhất):
pip install -U git+https://github.com/CenterForMedicalGeneticsGhent/Wisecondorx
✅ Cài qua conda (có sẵn dependency R):
conda install -c conda-forge -c bioconda wisecondorx

📁 Chuẩn bị dữ liệu
1. Tải dữ liệu .sra từ NCBI:
prefetch --max-size 50G SRRxxxxxxx
2. Chuyển .sra sang .fastq:
fasterq-dump SRRxxxxxxx --outdir fastq_files --split-files

🧬 Tạo tập mẫu tham chiếu
Sau khi có ít nhất 10 .npz từ các mẫu âm tính tiến hành tạo dữ liệu tham chiếu:
wisecondorx newref *.npz reference.npz

🧪 Dự đoán bất thường mẫu thử
wisecondorx predict sample1.npz reference.npz output_sample1 \
  --zscore 3 \
  --bed \
  --plot
--zscore 3: ngưỡng phát hiện bất thường
--bed: xuất kết quả dạng bảng
--plot: xuất biểu đồ .png

📤 Các tệp đầu ra
Tệp	                                      Mô tả
output_sample1_statistics.txt	    Thống kê tổng quan mẫu
output_sample1_bins.bed	          Thông tin từng bin
output_sample1_segments.bed	      Các đoạn copy number khác biệt
output_sample1_aberrations.bed	  Đoạn có bất thường (z-score cao)
chr*.png	                        Biểu đồ CNV theo từng nhiễm sắc thể

Diễn giải kết quả
ratio.mean ≈ 0: bản sao bình thường
z-score > ±3: nghi ngờ bất thường
CPA score: đo mức độ bất thường toàn genome
chrX, chrY:
  z ≈ -1000: không phát hiện → mất NST (ví dụ Turner)
  z ≫ 0: tăng bản sao (ví dụ Triple X)

📌 Ví dụ: Nhận định mẫu bình thường
ratio.mean ≈ 0, z-score thấp, không có đoạn bất thường trong .bed
→ không có dấu hiệu bất thường về số lượng bản sao.
📌 Ví dụ: Nhận định hội chứng Down (trisomy 21)
chr21 z-score ≫ 3, ratio.mean > 0.2 → nghi ngờ tăng bản sao ở NST 21
📌 Ví dụ: Turner
chrX z ≈ -1000, chrY z ≈ -1000 → mất 1 NST giới tính → Turner (45,X)
