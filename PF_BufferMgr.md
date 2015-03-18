本wiki介绍PF模块中PF\_BufferMgr的功能


PF\_BufferMgr，负责文件的读写和缓存。

从使用者的角度看，每个文件分成若干个page。每个文件有一个标识符file descriptor。
RC GetPage(int fd
> , PageNum pageNum
> , char ppBuffer
> , int bMultiplePins = TRUE);
允许读取文件fd的第pageNum页的内容，返回结果是缓存的指针。ppBuffer.
bMultiplePins这个参数没什么用。因为永远都是TRUE。

RC AllocatePage(int fd, PageNum pageNum, char ppBuffer);
分配文件fd的第pageNum页。如果是想向文件写入一些东西，但是那一页还不存在。那就应该用着个函数。

RC MarkDirty(int fd, PageNum pageNum);
这个函数比较技术，是标记一个页pageNum需要保存到磁盘上。如果不掉用这个函数，PF\_BufferMgr就不知道需要把这一页写回磁盘。

RC UnpinPage(int fd, PageNum pageNum);
Unpin就是主动地把这个已经在缓存中的页从缓存中清除。否则，就让PF\_BufferMgr自己来管理清除。

RC FlushPages(int fd);
就是把这个文件fd的所有缓存中的页都写到磁盘上去。

RC ForcePages(int fd, PageNum pageNum);
把这一页内容写到磁盘上去，但是不把缓存清除。可能有时候需要这样。不过搜了一下代码，只有测试的时候用了。那就还是不要删掉了。

RC ClearBuffer();
清除所有缓存。就相当把PF\_BufferMgr整个清零了一样。除了人没丢什么都丢了。不过这个方法会做检查

RC PrintBuffer();