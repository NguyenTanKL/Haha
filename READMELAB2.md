<center><b>Lab 2</b></center>
<center> <i>Process & Multithread Process</i>
</center>
<h3>1. Process's memory </h3>
<p>Thông thường, một chương trình UNIX sẽ được chia thành nhiều phân đoạn. Các phân đoạn tiêu chuẩn là: <i>code segment, data segment, BSS(block started by symbol), and stack segment</i>.
</p>

![Layout memory segment](/assets/Layout_memory_segment.png "Layout memory segment")

<p> Trong LINUX, 1 process có thể được thực thi ở 2 chế độ - <i>user mode</i> and <i>kernel mode</i>. Một process thường được thực thi ở chế độ <i>user mode</i>, nhưng có thể switch thành <i>kernel mode</i> thông qua gọi <b>system call</b>. </p>

<p>Khi process running ở user mode, nó được gọi là <i> Userland </i>. Khi process running ở kernal mode, nó được gọi là <i> Kernel Space </i></p>

<ul>
    <li><p>Code segment chứa code binary của chương trình. Nó chứa tất cả các hàm mà ta viết ở trong chương trình. Do đó khi biết địa chỉ của một hàm, ta có thể biết chỉ ra được vùng Code segment.</p></li>
    <li><p>Data segment chứa initialized global variables của chương trình. Để biết được địa chỉ vùng Data segment, ta khai báo một biến global variable và in địa chỉ của nó. Địa chỉ của biến phải nằm trong vùng địa chỉ của Data segment</p></li>
    <li><p></p>BSS chứa các unitialized global variables của process. Để lấy địa chỉ vùng BSS ta chỉ cần lấy địa chỉa của biến.</li>
    <li><p></p>Automatic variables (local variables) nằm trên stack. Tương tự lấy địa chỉ vùng stack chỉ cần lấy địa chỉ biến.</li>
    <li><p>Dynamically nằm trên heap during process runtime</p></li>
</ul>
<h3>2. Stack</h3>
<p>Stack là vùng nhớ quan trọng trong process. Dùng để chứa các data tạm thời được sử dụng trong process. Nó được đặt theo nghĩa của tên - thêm vào cuối lấy ra trước.</p>
<p>Stack được thiết kế 1 cách phù hợp để giải quyết các vấn đề gọi hàm. Mỗi khi gọi hàm, sẽ có 1 stack frame mới được tạo ra. Vùng stack này sẽ nhớ địa chỉ trả về, các tham số, hàm,...</p>
<p>Trong Linux, Stack bắt đầu ở địa chỉ cao sau đó sẽ phát triển dần xuống và tăng kích thước. Mỗi khi một hàm được gọi, process sẽ tạo ra stack frame mới cho function. Frame này sẽ được giải phóng sau khi gọi xong.</p>

![Stack Example address decrease](/assets/StackEx.png "Stack example address decrease")

<p>Tương tự như heap, stack có pointer là <i>stack pointer</i> (trong heap là <i>program break</i>). Stack pointer được chứa trong thanh ghi Stack pointer bên trong processor. không gian Stack bị giới hạn, chúng ta không thể mở rộng stack vượt quá không gian cho phép. Nếu làm vậy, sẽ gây ra lỗi stack overflow. Để điều chỉnh kích thước stack mặc định, sử dụng command: </p>

`ulimit -s`

<h3>3. Interprocess Communication</h3>
<p>Các lý do cần cooperating processes:</p>
<ul>
    <li><b>Information sharing</b>. Bởi vì các ứng dụng có thể sử dụng chung một mẩu thông tin, chúng ta phải cung cấp môi trường để cho phép đồng thời access vùng Information đó. </li>
    <li><b>Computation speedup</b>. Chia một task thành các subtasks để thực thi parallel giúp nhanh hơn. </li>
    <li> <b>Modularity</b>. Chia system thành các modul thực thi bởi các processes giúp nhanh hơn.</li>
</ul>
<p>Có nhiều cách Communication. Tuy nhiên, <b>Trong bài lab này, chúng ta sẽ tập trung vào phương pháp shared memory</b></p>
<h3>4. Introduction to thread</h3>
<p>Thread là một đơn vị cơ bản của CPU utiization, nó bao gồm thread ID, program counter, register set và stack.</p>

![Shared memory vs message passing model](/assets/ShareMemoryVSMessagePassingModel.png "Shared memory vs mesage passing model")

<p>Nó chia sẻ code section, data section và resources với các thread khác trong cùng một process. Một process truyền thống chỉ chứa 1 thread. Nếu một process chứa nhiều threads, nó có thể thực hiện nhiều task tại một thời điểm.</p>

![Single - Multi - Thread](/assets/Single-Multi-Thread.png "Single-Multi-Thread")

<p>Lợi ích của việc sử dụng multi thread được chia thành 4 loại chính:</p>
<ul>
    <li>Responsiveness: Thực hiện nhiều task trong 1 lúc. Đáp ứng nhu cầu về thời gian.</li>
    <li>Resource sharing: Không mất thời gian tạo lại resource.</li>
    <li>Economy: Nhanh, tiện, tiết kiệm.</li>
    <li>Scalability: Khả năng mở rộng xử lí. Chia nhiều thread, xử lí nhiều công việc.</li>
</ul>

<p><b>Multicore Programming</b> Trong lịch sử phát triển computer design, để đáp ứng nhu cầu về mặt hiệu suất, single-CPU system phát triển thành multi-CPU system. Trong những năm gần đây, xu hướng phát triển design system chuyển sang nhiều cores trên 1 chip. Mỗi core như là một process tách biệt đối với hệ điều hành. Dù là các cores được tích hợp trên nhiều chip khác nhau hay trên cùng một chip, chúng ta gọi chung là hệ thống multicore hoặc multiprocessor. Multithread cung cấp cơ chế cho việc sử dụng hiệu quả hơn đối với multicore và cải thiện concurrency.</p>

![Parallel Multicore System](/assets/ParallelMulticore.png "Parallel Multicore System")

<p>Trên hệ thống multicores, <i>concurrency</i> có nghĩa là nhiều threads có thể thực thi song song, vì vậy hệ thống có thể gán các thread khác nhau cho từng core.</p>
<h3>5. How to transfer data between processes?</h3>
<h4>5.1 Shared Memory</h4>
<p>Process chỉ định vùng shared memory segment thông qua <i><b>shmget("SHared Memory GET").</b></i></p>

`int shmget(key_t key, size_t size, int shmflg);`

<ul>
    <li>Tham số <i>key</i>: giá trị key của shmget() là một số nguyên duy nhất được sử dụng để xác định một vùng nhớ chung cụ thể. Khi một tiến trình muốn sử dụng một vùng nhớ chung, nó sẽ sử dụng hàm shmget() với key tương ứng để truy cập vào vùng nhớ đó.Nếu hai hoặc nhiều tiến trình sử dụng cùng một giá trị key trong shmget(), chúng sẽ truy cập vào cùng một vùng nhớ chung. Điều này có thể dẫn đến các vấn đề về đồng bộ hóa dữ liệu hoặc gây ra lỗi không mong muốn trong chương trình.Để tránh việc này, một cách để đảm bảo rằng các tiến trình sử dụng các vùng nhớ chung riêng biệt là sử dụng giá trị key là IPC_PRIVATE trong shmget(). Khi sử dụng giá trị này, hệ thống sẽ tạo ra một key mới duy nhất cho mỗi tiến trình gọi shmget(), đảm bảo rằng mỗi tiến trình sẽ truy cập vào một vùng nhớ chung riêng biệt và tránh xung đột. 
    </li>
    <li>Tham số <i>size</i> chỉ định số bytes của segment. Bởi vì segments thì được phân vùng theo trang, số byte thật sự sẽ được làm tròn lên số trang.
    </li>
    <li>Tham số <i>flag</i> chỉ định các option của shmget. Bao gồm:
        <ul>
            <li>IPC_CREATE: biểu thị một segment mới được tạo ra. </li>
            <li>IPC_EXCL: thường được sử dụng với IPC_CREATE, huỷ shmget nếu segment key đã tồn tại. Nếu không dùng flag này và key đã tồn tại, shmget trả về segment đã tồn tại trước đó thay vì tạo ra cái mới.</li>
            <li>Mode flags: giá trị này thì được tạo ra bởi 9 bits biểu thị sự cho phép truy cập của owner, group và others. </li>
        </ul>
    </li>
</ul>

<p>Để việc shared memory segment available, phải dùng <b>shmat()</b> để gắn vào các tiến trình. </p>

`void *shmat(int shmid, const void *shmaddr, int shmflag);`

<ul>
    Hàm này chứa 3 tham số như sau:
    <li>định danh shared memory segment SHMID được trả về bởi <b>shmget()</b>. </li>
    <li>con trỏ <i>shmaddr</i> chỉ ra không gian địa chỉ cần được map shared memory. Nếu chỉ định NULL, Linux sẽ chọn một địa chỉ bất kì available. </li>
    <li> <i>shmflg</i> là cờ dùng để chọn các option của hàm <b>shmat</b>. </li>
</ul>

<p>Sau khi shared memory segment xong, segment nên được tháo ra khỏi các process thông qua <b>shmdt</b>. Truyền vào tham số địa chỉ được trả về bởi <b>shmat</b>. Nếu segment đã được giải quyết xong và đây là process cuối cùng sử dụng nó, nó sẽ bị removed (nó ở đây là shared memory segment). Examples: Thực thi 2 processes bên dưới bằng 2 terminals. Ở process writer, bạn có thể nhập vào một chuỗi và được lưu trữ, trả về bởi process reader. </p>

![Writer.c](/assets/writer.png "Writer.c")

![Reader.c](/assets/read.png "Reader.c")

<h4>5.2 Pipe</h4>
<p>Pipe thực sự là một phương pháp thông dụng để chuyển đổi data giữa các processor. Ví dụ, "pipe" operator "|" có thể được sử dụng để chuyển đổi output từ một command sang vai trò input của command khác. Ví dụ:</p>

`#The output from "history" will be input to the grep command.`

`history | grep "a"`

<p>Về mặt chương trình C, thư viện chuẩn có tên "unistd.h" định nghĩa hàm bên dưới để tạo ra 1 pipe. Function này tạo ra một pipe, một kênh dữ liệu một chiều có thể được sử dụng cho interprocess communication. Mảng pipefd được sử dụng để trả về 2 file descriptors đề cập đến các đầu của pipe. Dữ liệu được ghi vào đầu ghi của pipe sẽ được đệm bởi kernel cho đến khi nó được đọc bởi đầu đọc của pipe. </p>

`int pipe(int pipefd[2]);`

<p>Pipe có thể được sử dụng một chiều như sau:</p>

![1waycommunication.c](/assets/1waycommunication.png "1waycommunication.c")

<p>Ở chương trình trên, đầu tiên process cha sẽ tạo ra pipeline và gọi fork() để tạo process con. Sau đó process cha sẽ viết một message đẩy vào pipeline. Cùng thời điểm đó, proacess con sẽ đọc data từ pipeline. Chú ý rằng, cả write() và read() cần biết kích thước của message. Ví dụ về pipe:</p>

![pipe1.png](/assets/pipe1.png "pipe1")

![pipe2.png](/assets/pipe2.png "pipe2")

<h3>6. How to create multiple threads?</h3>
<h4>Thread libraries</h4>
<p>Thread library cung cấp API cho programmer để tạo và quản lý threads. Có 2 cách cơ bản để hiện thực 1 thread library. Có 3 thread libraries chính được sử dụng ngày nay: POSIX Pthreads, Windows, và Java. Trong bài lab này, chúng ta sẽ sử dụng POSIX Pthread trên Linux và Mac OS để thực hành với multithreading programming.</p>
<p><b>Creating threads</b></p>

`pthread_create(thread, attr, start_routine, arg)`

<p>Ban đầu, mặc định chương trình <i>main()</i> sẽ chứa single thread. Tất cả các threads còn lại phải được tạo một cách tường minh bởi programmer.</p>
<ul>
    <li> <b>thread:</b> con trỏ thread là định danh duy nhất của new thread, được trả về bởi chương trình con.</li>
    <li> <b>attr:</b> con trỏ attr là các thông số dùng để set thuộc tính thread. NULL để lấy giá trị mặc định. </li>
    <li> <b>start:</b> con trỏ start thủ tục C mà thread sẽ thực thi mỗi khi thread được khởi tạo. </li>
    <li> <b>arg:</b> là một đối số được truyền vào textttstart_routine. Nó phải được truyền tham chiếu bởi con trỏ void. Nếu không có argument truyền vào, sử dụng NULL.</li>
</ul>
<p>Ví dụ:</p>

![creat thread](/assets/createthread.png "create thread")

<p> <b>Passiang argument to Thread </b>Chúng ta có thể truyền structure vào thread như ví dụ bên dưới: </p>

![Passing argument to Thread](/assets/passingargtothread.png "Passing argument to thread")

<p> <b>Joining and Detaching Threads </b>"Joining" là một cách để hoàn thành đồng bộ hoá giữa các threads, Ví dụ:</p>

![Joining threads](/assets/joinning_thread.png "Joining threads")

<ul>
    <li> <b>Pthread_join()</b> sẽ chặn gọi thread cho đến khi các chương trình con exit. </li>
    <li> Programmer sẽ nhận được kết quả trả về khi các thread gọi pthread_exit() </li>
    <li>Chỉ gọi pthread_join() 1 lần. Sẽ gây ra lỗi logic nếu gọi pthread_joint() nhiều lần trên cùng 1 thread.</li>
</ul>

<p> <b>Problem:</b> Constructing a multithreaded program that calculates the summation of a
    non-negative integer in a separate thread. </p>

![Example Joining Thread 1](/assets/examplejointhread1.png "Example Joining Thread 1")

![Example Joining Thread 2](/assets/examplejointhread2.png "Example Joining Thread 2")