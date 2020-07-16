## 3.4. Dự đoán liên khung
Với mỗi khối dự đoán liên khung CU, tham số chuyển động bao gồm: các vector chuyển động, chỉ số khung tham khảo, và chỉ số danh sách khung tham khảo, và các thông tin bổ xung cho các tính năng mã hóa mới của VVC để tạo ra dự đoán liên khung. Tham số chuyển động có thể được truyền đi hoặc tự nội suy ra. Khi một CU được mã hóa ở dạng Skip, thì CU tương ứng với một PU mà có phần tử phần dư là không đáng kể, không có mã hóa khác biệt vector chuyển động hay chỉ số khung tham khảo. Mã hóa kiểu gộp sẽ chỉ rõ các tham số chuyển động của CU hiện tại sẽ được tham khảo từ các CU lân cận từ các ứng viên CU miền không gian, thời gian, và them một hoạch định mới trong VVC. Mốt gộp có thể dùng cho bất kỳ CU được dự đoán liên khung nào chứ không chỉ cho dự đoán kiểu skip. Nếu không dung mã hóa kiểu gộp, thông tin có thể được truyền đi như tham số chuyển động cho từng CU. 
Dựa trên những đặc tính mã hóa HEVC, VVC giới thiệu và cải thiện các công cụ mã hóa liên khung mới gồm
-	Dự đoán gộp mở rộng 
-	Mã hóa gộp với MVD(MMVD) 
-	MVD đối xứng (MMVD) 
-	Bù chuyển động tinh (Affine motion compensated prediction) 
-	Dự đoán vector chuyển động thời gian trên tiểu khung. (Subblock-based temporal motion vector prediction)
-	Vector chuyển động với độ phân giải thích nghi 
-	Lưu trữ Trường chuyển động: lưu MV luma ở 1/16 và nén trường vector 8x8 
-	Dự doán hai chiều với trọng số mức CU (BCW)
-	Dự đoán hai chiều với optical flow
-	Tinh chỉnh vector chuyển động phía giải mã (Decoder side motion vector refinement – DMVR)
-	Phân tách hình học 
-	Dự đoán kết hợp nội và liên khung (Combined inter and intra prediction) 
Phần dưới đây sẽ trình bày chi tiết về các phương pháp dự đoán liên khung trong VVC. 


## 3.4    Inter prediction

For each inter-predicted CU, motion parameters consisting of motion vectors, reference picture indices and reference picture list usage index, and additional information needed for the new coding feature of VVC to be used for inter-predicted sample generation. The motion parameter can be signalled in an explicit or implicit manner. When a CU is coded with skip mode, the CU is associated with one PU and has no significant residual coefficients, no coded motion vector delta or reference picture index. A merge mode is specified whereby the motion parameters for the current CU are obtained from neighbouring CUs, including spatial and temporal candidates, and additional schedules introduced in VVC. The merge mode can be applied to any inter-predicted CU, not only for skip mode. The alternative to merge mode is the explicit transmission of motion parameters, where motion vector, corresponding reference picture index for each reference picture list and reference picture list usage flag and other needed information are signalled explicitly per each CU.

Beyond the inter coding features in HEVC, VVC includes a number of new and refined inter prediction coding tools listed as follows: 

–  Extended merge prediction

–  Merge mode with MVD (MMVD)

–  AMVP mode with symmetric MVD signalling

–  Affine motion compensated prediction

–  Subblock-based temporal motion vector prediction (SbTMVP)

–  Adaptive motion vector resolution (AMVR) 

–  Motion field storage: $1/16$th luma sample MV storage and $8 \times 8$ motion field compression

–  Bi-prediction with CU-level weight (BCW)

–  Bi-directional optical flow (BDOF) 

–  Decoder side motion vector refinement (DMVR)

–  Triangle partition prediction

–  Combined inter and intra prediction (CIIP)

The following text provides the details on the inter prediction methods specified in VVC.
