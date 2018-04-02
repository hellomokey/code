#int to string
	int val = rand();
	stringstream ss;//int to string
	string str;
    ss<<((val >> 24)&0xff);
    ss>>str;
	string ret=str;
	ret += ".";
	ss.clear();
#sprintf
* 功能<br>
把格式化的数据写入某个字符串缓冲区。

* 头文件<br>
stdio.h

* 原型<br>
int sprintf( char *buffer, const char *format, [ argument] … );

* 参数列表<br>
	- buffer：char型指针，指向将要写入的字符串的缓冲区。
	- format：格式化字符串。
	- [argument]...：可选参数，可以是任何类型的数据。
* 返回值<br>
返回写入buffer 的字符数，出错则返回-1. 如果 buffer 或 format 是空指针，且不出错而继续，函数将返回-1，并且 errno 会被设置为 EINVAL。<br>
sprintf 返回以format为格式argument为内容组成的结果被写入buffer 的字节数，结束字符‘\0’不计入内。即，如果“Hello”被写入空间足够大的buffer后，函数sprintf 返回5。同时buffer的内容将被改变。

>sprintf(s, "%d", 123); //产生"123"

#fstream
##std::fstream::open

* member constant | opening mode |
* app	(append) 
* |- Set the stream's position indicator to the end of the stream before each output operation. |
* ate	(at end) 
* |-Set the stream's position indicator to the end of the stream on opening.|
* binary	(binary) 
* |- Consider stream as binary rather than text.
* in	(input) 
* |- Allow input operations on the stream.
* out	(output) 
* |- Allow output operations on the stream.
* trunc	(truncate) 
* |- Any current content is discarded, assuming a length of zero on opening.


#std::istream::read
public member function
<istream> <iostream>
>istream& read (char* s, streamsize n);

Read block of data<br>
Extracts n characters from the stream and stores them in the array pointed to by s.<br>
This function simply copies a block of data, without checking its contents nor appending a null character at the end.

#std::istream::getline
istream& getline (char* s, streamsize n );<br>
istream& getline (char* s, streamsize n, char delim );<br>
Get line<br>
Extracts characters from the stream as unformatted input and stores them into s as a c-string, until either the extracted character is the delimiting character, or n characters have been written to s (including the terminating null character).

The delimiting character is the newline character ('\n') for the first form, and delim for the second: when found in the input sequence, it is extracted from the input sequence, but discarded and not written to s.

The function will also stop extracting characters if the end-of-file is reached. If this is reached prematurely (before either writing n characters or finding delim), the function sets the eofbit flag.

The failbit flag is set if the function extracts no characters, or if the delimiting character is not found once (n-1) characters have already been written to s. Note that if the character that follows those (n-1) characters in the input sequence is precisely the delimiting character, it is also extracted and the failbit flag is not set (the extracted sequence was exactly n characters long).

A null character ('\0') is automatically appended to the written sequence if n is greater than zero, even if an empty string is extracted.