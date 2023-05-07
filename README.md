# BÁO CÁO ĐỒ ÁN HỌC SÂU - NHẬN DẠNG CHỮ VIẾT TAY

## Thành viên thực hiện
Phạm Minh Thạch - 22C15018

## GIỚI THIỆU ĐỒ ÁN
Ở đồ án này, chúng ta sẽ xây dựng 3 mô hình học sâu để giải quyết bài toán nhận dạng chữ viết tay với thư viện tensorflow và keras.
Các thư mục và file của đồ án được tổ chức như sau:  
- README.md		: Giới thiệu, hướng dẫn cài đặt môi trường  
- report.docx		: Báo cáo  
- report.pdf		: Báo cáo  
- explore_data.ipynb	: Jupyter notebook tìm hiểu tập dữ liệu  
- model_1.ipynb	: Jupyter notebook huấn luyện model 1  
- model_2.ipynb	: Jupyter notebook huấn luyện model 2  
- model_3.ipynb	: Jupyter notebook huấn luyện model 3  

## TÌM HIỂU TẬP DỮ LIỆU
Tập dữ liệu là được chia thành 2 tập: train và test. Tập train gồm 60,000 ảnh trắng đen chữ viết tay từ 0 đến 9. Tập test gồm 10,000 ảnh.

Số lượng mẫu train cho từng nhãn khá đồng đều nhau. Khoảng 10,000 mẫu cho mỗi nhãn.

Cách điểm ảnh có giá trị từ 0 đến 255 và phân bố chủ yếu trong khoảng [0, 25] và [230, 255]. Khoảng [0, 25] là màu sắc của background và [230, 255] là màu sắc của các nét chữ.

## TIỀN XỬ LÝ
Để thuận lợi cho việc tính toán của các mô hình, ta đưa giá trị của các điểm ảnh về khoảng [0,1] theo công thức sau.  
q = 0 nếu p < 127  
q = 1 nếu p >= 127  
Trong đó, p là giá trị ban đầu của điểm ảnh, q là giá trị mới phục vụ cho việc tính toán sau này.  

## PHƯƠNG PHÁP TRAIN MODEL
Cả 3 model sẽ được train theo cùng một phương pháp với các tham số như sau.  
Epoch number:   5  
Batch size:     64  
Optimizer:      Adam  
Loss function:  Categorical Cross Entropy  
Metric:         Accuracy  

## MODEL 1
Model 1 chỉ gồm các fully connected layer với cấu trúc như sau.  
```
model = Sequential()
model.add(Dense(512, activation='relu', input_shape=(28*28,)))
model.add(Dense(10, activation='softmax'))
```
Độ chính xác của model 1 khi thực hiện đánh giá trên tập test là: 0.9777.

## MODEL 2
Model 2 kết hợp convolution layer và linear layer với cấu trúc như bên dưới.  
```
model = Sequential()
model.add(Conv2D(32, (3, 3), activation='relu', input_shape=(28, 28, 1)))
model.add(MaxPooling2D((2, 2)))
model.add(Conv2D(64, (3, 3), activation='relu'))
model.add(MaxPooling2D(2, 2))
model.add(Conv2D(64, (3, 3), activation='relu'))
model.add(Flatten())
model.add(Dense(64, activation='relu'))
model.add(Dense(10, activation='softmax'))
```
Độ chính xác của model 1 khi thực hiện đánh giá trên tập test là: 0.9919.

### MODEL 3
Model 3 kết hợp convolution layer, linear layer và dropout với cấu trúc như bên dưới.
```
model = Sequential()
model.add(Conv2D(32, kernel_size=(3, 3), activation='relu',input_shape=(28,28,1)))
model.add(Conv2D(64, (3, 3), activation='relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))
model.add(Flatten())
model.add(Dense(128, activation='relu'))
model.add(Dropout(0.5))
model.add(Dense(10, activation='softmax', name='preds'))
```
Độ lỗi và độ chính xác của model trên tập train theo từng epoch được thể hiện như biểu đồ ở hình 7 và hình 8. Độ chính xác của model 1 khi thực hiện đánh giá trên tập test là: 0.9919.   

## TỔNG KẾT
Bảng bên dưới cho sự khác biệt về số lượng tham số, thời gian thực hiện trên tập test và độ chính xác của 3 mô hình.
	Số lượng tham số	Thời gian đánh giá	Độ chính xác
Model 1	407,050	2s	0.9777
Model 2	93,322	2.4s	0.9919
Model 3	1,199,882	2.3s	0.9893

|       |Số lượng tham số|Thời gian đánh giá|Độ chính xác|
|-------|----------------|------------------|------------|
|Model 1|407,050|2s|0.9777|
|Model 2|93,322|2.4s|0.9919|
|Model 3|1,199,882|2.3s|0.9893|
