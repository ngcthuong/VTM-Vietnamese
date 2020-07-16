## 3.4.1. Dự đoán gộp mở rộng 
VVC xây dựng danh sách ứng viên gộp với năm loại ứng viên theo tư tự 
1)	MVP không gian: từ các CU lân cận chiều không gian 
2)	MVP thời gian: từ các CU ở cùng vị trí chiều thời gian 
3)	MVP lịch sử: lưu trữ trong các bảng FIFO 
4)	MVP trung bình từng cặp 
5)	Vector chuyển động Không. 

Kích thước của danh sách gộp được truyền header của thông số dãy với kích thước tối đa là 6. Vỡi mỗi CU mã hóa dạng Gộp, chỉ số của ứng viên gộp tốt nhất được mã hóa bằng mã nhị phân rút ngắn. Nội dung của kết quả nén chỉ số gộp đầu tiên được sử dụng và mã hóa dạng bypass cho các  bin khác. 
Quá trình xây dựng từng loại ứng viên được trình bày chi tiết trong chương này. Tương tự như HEVC, VVC hỗ trợ quá trình xây dựng ứng viên song song với tất cả CUs ở một số kích thước.

### 3.4.1    Extended merge prediction

In VVC, the merge candidate list is constructed by including the following five types of candidates in order:

1. Spatial MVP from spatial neighbour CUs

2. Temporal MVP from collocated CUs

3. History-based MVP from an FIFO table

4. Pairwise average MVP

5. Zero MVs.

The size of merge list is signalled in slice header and the maximum allowed size of merge list is 6. For each CU code in merge mode, an index of best merge candidate is encoded using truncated unary binarization (TU). The first bin of the merge index is coded with context and bypass coding is used for other bins.

The generation process of each category of merge candidates is provided in this session.

#### 3.4.1. Xây dựng ứng viên chiều không gian 
Quá trình xây dựng ứng viên chiều không gian của VVC giống với HEVC trừ một điểm là vị trí ứng viên thứ nhất và thứ hai được đổi chỗ. Có tối đa bốn ứng viên được họn trong tổng số 5 ứng viên thể hiện trong hình 21. Thứ tự kiểm tra các ứng viên là B0, A0, B1, A1 và B2. Lưu ý, ứng viên B2 chỉ được xem xét khi có ít nhất một trong bốn ứng viên ban đầu không có mặt (do thuộc một slice hoặc tile khác) hoặc được mã hóa nội khung. 

Sau khi thêm ứng viên A1, quá trình kiểm tra dư thừa sẽ được tiến hành nhằm đảm bảo không có sự trùng lặp về vector chuyển động trong danh sách để tăng tính hiệu quả mã hóa. Hơn nữa, để giảm độ phức tạp tính toán, VVC không kiểm tra hết các trường hợp cặp (ví dụ ở đây là 5*4*3*2 = 120 trường hợp), mà chỉ kiểm tra các cặp có mũi tên trong hình 22. Ứng viên chỉ được thêm vào nếu không có trùng lặp về thông tin chuyển động.  


#### 3.4.1.1   Spatial candidates derivation

The derivation of spatial merge candidates in VVC is same to that in HEVC except the positions of first two merge candidates are swapped. A maximum of four merge candidates are selected among candidates located in the positions depicted in Figure 21. The order of derivation is $B_0, A_0, B_1, A_1$ and $B_2$. Position $B_2$ is considered only when one or more than one CUs of position $B_0, A_0, B_1, A_1$ are not available (e.g. because it belongs to another slice or tile) or is intra coded. After candidate at position $A_1$ is added, the addition of the remaining candidates is subject to a redundancy check which ensures that candidates with same motion information are excluded from the list so that coding efficiency is improved. To reduce computational complexity, not all possible candidate pairs are considered in the mentioned redundancy check. Instead only the pairs linked with an arrow in Figure 22 are considered and a candidate is only added to the list if the corresponding candidate used for redundancy check has not the same motion information.

 

​                                ![Fig21](imgs\Fig21.PNG)

Figure 21– Positions of spatial merge candidate

 

 ![Fig22](\imgs\Fig22.png)

Figure 22 – Candidate pairs considered for redundancy check of spatial merge candidates

#### 3.4.1.2 Xây dựng ứng viên thời gian 
VVC chỉ thêm một ứng viên thời gian. Từ ứng viên ở cùng vị chí ở khung tham khảo, VVC sẽ tìm ra một véc tơ chuyển động được điều chỉnh.  Danh sách khung tham khảo của khung được sử dụng để xây dựng ứng viên thời gian sẽ được thông báo qua slice header. Vector chuyển động được điều chỉnh được minh họa trong đường nét đứt với hệ số điểu chỉnh được xác định bằng khoảng cách giữa chỉ số khung td và tb. Chỉ số tb là khoảng cách giữa chỉ số khung tham khảo của (curr_ref) của khung hiện tại (curr_pic), td là khoảng cách giữa khung cùng vị trí (col_pic) và khung tham khảo tương ứng. 


#### 3.4.1.2 Temporal candidates derivation

In this step, only one candidate is added to the list. Particularly, in the derivation of this temporal merge candidate, a scaled motion vector is derived based on co-located CU belonging to the collocated referenncee picture. The reference picture list to be used for derivation of the co-located CU is explicitly signalled in the slice header. The scaled motion vector for temporal merge candidate is obtained as illustrated by the dotted line in Figure 23, which is scaled from the motion vector of the co-located CU using the POC distances, $tb$ and $td$, where $tb$ is defined to be the POC difference between the reference picture of the current picture and the current picture and td is defined to be the POC difference between the reference picture of the co-located picture and the co-located picture. The reference picture index of temporal merge candidate is set equal to zero. 

![Fig23](\imgs\Fig23.PNG)

Figure 23 – Illustration of motion vector scaling for temporal merge candidate

The position for the temporal candidate is selected between candidates $C_0$ and $C_1$, as depicted in Figure 24. If CU at position $C_0$ is not available, is intra coded, or is outside of the current row of CTUs, position $C_1$ is used. Otherwise, position $C_0$ is used in the derivation of the temporal merge candidate.

  ![Fig24](imgs\Fig24.png)

Figure 24 – Candidate positions for temporal merge candidate, C0 and C1


#### 3.4.1.3. Xây ựng ứng liên gộp lịch sử
Ứng viên gộp lịch sử HMVP được them thứ 3 sau ứng viên không gian và thời gian. HMVP lưu thông tin vào bảng dùng như MVP cho CU hiện tại. VVC duy trì bảng với nhiều ứng viên HMVP xuyên xuốt quá trình mã hóa giải mã. Bảng HMVP được giải phóng mỗi khi bắt đầu một hàng CTU mới. Bất cứ khi nào không phải khối phụ CU và được mã hóa liên khung, các thông tin chuyển động ở ứng viên cuối cùng trong bảng HMVP cũ được dùng làm ứng viên trong bảng mới. 
VVC thiết lập kích thước bảng HMVP bằng 6, tương ứng với tối đa 6 ứng viên MVP lịch sử (HMVP). Với điều kiện ràng buộc đến trước ra trước, khi thêm một ứng viên mới, VVC tiến hành kiểm tra dư thừa thông tin chuyển động. Nếu có trùng lặp thông tin chuyển động, ứng viên đã có trong bảng và trùng thông tin sẽ bị xóa, tất cả các ứng viên khác sẽ được dịch chuyển về hướng đầu ra. 
Ứng viên HMVP có thể được dùng trong quá trình xây dựng ứng viên gộp. Các ứng viên được thêm gần nhất trong bảng HMVP được kiểm tra theo thứ tự, và thêm vào danh sách ứng viên ngay sau ứng viên MVP thời gian (TMVP). Kiểm tra dư thừa trên ứng viên HMVP so với các wungs viên không gian hoặc thời gian. 
Để giảm số cặp kiểm tra dư thừa, VVC thực hiện 
1.	Số lượng ứng viên HMPV tạo danh sách gộp giới hạn bởi: (N <= 4) ? M: (8 – N). Giá trị N tương ứng số lượng ứng viên hiện có danh sách gộp, M là số lượng ứng viên HMVP hiện có. 
2.	Nếu tổng số ứng viên trong danh sách merge đạt ngưỡng tối đa – 1, không dùng các ứng viên từ HMVP. 

#### 3.4.1.3 History-based merge candidates derivation 

The history-based MVP (HMVP) merge candidates are added to merge list after the spatial MVP and TMVP. In this method, the motion information of a previously coded block is stored in a table and used as MVP for the current CU. The table with multiple HMVP candidates is maintained during the encoding/decoding process. The table is reset (emptied) when a new CTU row is encountered. Whenever there is a non-subblock inter-coded CU, the associated motion information is added to the last entry of the table as a new HMVP candidate.

The HMVP table size *S* is set to be 6, which indicates up to 6 History-based MVP (HMVP) candidates may be added to the table. When inserting a new motion candidate to the table, a constrained first-in-first-out (FIFO) rule is utilized wherein redundancy check is firstly applied to find whether there is an identical HMVP in the table. If found, the identical HMVP is removed from the table and all the HMVP candidates afterwards are moved forward,

HMVP candidates could be used in the merge candidate list construction process. The latest several HMVP candidates in the table are checked in order and inserted to the candidate list after the TMVP candidate. Redundancy check is applied on the HMVP candidates to the spatial or temporal merge candidate. 

To reduce the number of redundancy check operations, the following simplifications are introduced:

1)   Number of HMPV candidates is used for merge list generation is set as $(N <= 4 ) \space ? \space M: (8 – N)$, wherein $N$ indicates number of existing candidates in the merge list and $M$ indicates number of available HMVP candidates in the table.

2)   Once the total number of available merge candidates reaches the maximally allowed merge candidates minus 1, the merge candidate list construction process from HMVP is terminated.

#### 3.4.1.4. Xây dựng ứng viên gộp trung bình từng cặp 
Ứng viên trung bình từng cặp được hình thành bởi việc tính trung bình các cặp ứng viên định trước trong danh sách ứng viên gộp. Các cặp định trước được xác định bởi vị chí trong bảng gộp: {(0, 1), (0, 2), (1, 2), (0, 3), (1, 3), (2, 3)}. Vector chuyển động trung bình được tính độc lập cho từng danh sách. Vector chuyển động trung bình được tính bằng giá trị trung bình của hai vector (nếu sẵn có) cho dù không chỉ tới cùng một khung tham khảo, hoặc một vector có sẵn, khoặc để không hợp lệ nếu không có vector có sẵn. 
Khi danh sách gộp vẫn chưa đầy sau khi thêm các cặp trung bình, vector không MVP được thêm vào tới khi đầy danh sách gộp. 

#### 3.4.1.4 Pair-wise average merge candidates derivation 

Pairwise average candidates are generated by averaging predefined pairs of candidates in the existing merge candidate list, and the predefined pairs are defined as $\{(0, 1), (0, 2), (1, 2), (0, 3), (1, 3), (2, 3)\}$, where the numbers denote the merge indices to the merge candidate list. The averaged motion vectors are calculated separately for each reference list. If both motion vectors are available in one list, these two motion vectors are averaged even when they point to different reference pictures; if only one motion vector is available, use the one directly; if no motion vector is available, keep this list invalid.

When the merge list is not full after pair-wise average merge candidates are added, the zero MVPs are inserted in the end until the maximum merge candidate number is encountered.

3.4.1.5. Vùng ước lượng gộp 
Vùng ước lượng gộp (Merge estimation region – MER) cho phép xây dựng độc lập danh sách gộp cho các CU ở cùng một vùng MER. Một khối ứng viên nằm trong cùng một MER với CU hiện tại sẽ không được tính đến khi xây dựng danh sách merge cho CU hiện tại. Thêm vào đó, VVC chỉ cập nhập danh sách dự đoán vector chuyển động dựa trên lịch sử nếu 
( xCb + cbWidth ) >> Log2ParMrgLevel,  lớn hơn,   xCb >> Log2ParMrgLevel 
và 
( yCb + cbHeight ) >> Log2ParMrgLevel ,  lớn hơn,  ( yCb >> Log2ParMrgLevel )
Với (xCb, yCb) là vị trí trên cùng bên trái của mẫu luma của CU hiện tại trong khung hình, (cbWidth, cbHeight) là kích thước CU. Kích thước của MER được lựa chọn và gửi đi bởi bộ mã hóa thông qua biến log2_parallel_merge_level_minus2 trong tập tham số chuỗi. 
