## Giới thiệu

Nội dung: lĩnh vực khai thác nhị phân (binary exploitation) và kỹ thuật dịch ngược (reverse engineering). 

Nếu bạn đã quen thuộc với mã assembly, khai thác nhị phân, reverse engineering và các công cụ như Ghidra, Pwntools, GDB, bạn có thể bỏ qua phần này (và bất kỳ nội dung nào khác mà bạn đã biết).

## Khai thác nhị phân (Binary Exploitation)

Nhị phân (binary) là gì?

Nhị phân là mã đã được biên dịch. Khi lập trình viên viết mã bằng một ngôn ngữ như C, code C không phải là thứ được chạy trực tiếp. Nó được biên dịch thành một tệp nhị phân, và tệp nhị phân này được thực thi. Khai thác nhị phân (binary exploit) là quá trình khai thác một tệp nhị phân, nhưng điều đó có nghĩa là gì?

Trong rất nhiều mã nguồn, bạn sẽ tìm thấy lỗi (bugs). Hãy nghĩ về bug như một sai sót trong code, cho phép thực hiện những chức năng ngoài dự kiến. Là một kẻ tấn công, chúng ta có thể tận dụng bug này để tấn công tệp nhị phân và ép nó thực hiện những gì chúng ta muốn, bằng cách đạt được quyền thực thi mã. Điều này có nghĩa là chúng ta có thể khiến tệp nhị phân thực thi đoạn mã mà chúng ta chỉ định, và từ đó xâm nhập vào mã nguồn.

## Kỹ thuật dịch ngược (Reverse Engineering)

Reverse engineering là gì?

Reverse engineering là quá trình tìm hiểu cách mà một thứ gì đó hoạt động. Đây là một phần quan trọng trong khai thác nhị phân, vì hầu hết thời gian bạn chỉ được cung cấp một tệp nhị phân mà không biết nó làm gì. Bạn phải tìm ra cách hoạt động của nó để có thể tấn công.

## Mục tiêu

Hầu hết thời gian, mục tiêu của bạn là đạt được quyền thực thi mã trên một máy và mở một shell. Nếu bạn có một mục tiêu khác, nó thường được ghi rõ ở dòng đầu tiên của bản viết (writeup). Trong gần như mọi trường hợp mà mục tiêu không phải là mở shell, thì mục tiêu sẽ là lấy được cờ CTF (Capture the Flag) gắn với thử thách từ tệp nhị phân.

## Bạn cần biết gì trước khi tham gia khóa học này?

Có một số lĩnh vực mà nếu bạn biết sẽ rất có ích. Nếu bạn biết lập trình, điều đó sẽ giúp ích. Nếu bạn biết lập trình các ngôn ngữ cấp thấp như C, điều đó còn tốt hơn. Ngoài ra, hiểu cơ bản về cách sử dụng Linux cũng rất hữu ích. Nhưng thực tế, điều duy nhất bạn thực sự cần là khả năng tra cứu thông tin trên Google và tự tìm câu trả lời.

## Tại sao lại chọn các thử thách CTF?

Lý do tôi chọn các thử thách CTF để dạy khai thác nhị phân và reverse engineering là vì hầu hết các thử thách chỉ chứa một phần nhỏ kiến thức khai thác. Với cách này, có thể chia chúng thành các chủ đề nhỏ như tràn bộ đệm (`buffer overflow`), gọi `shellcode` hoặc khai thác `fastbin`, để dạy theo dạng một khóa học thông thường.

## Môi trường

nên sử dụng một máy ảo Ubuntu
