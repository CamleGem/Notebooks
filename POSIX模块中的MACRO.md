# POSIX模块中的几种宏和常量的说明

## Constants
* WNOHANG  
在子进程更改状态之前，不要挂起调用进程，而是立即返回。

* WUNTRACED  
捕获已停止的子进程。


## Macros
* WIFEXITED(${^CHILD_ERROR_NATIVE})  
如果子进程正常退出，则返回true (通过exit() 或者程序一直执行到main末尾 )

* WEXITSTATUS  
WEXITSTATUS(${^CHILD_ERROR_NATIVE}) 返回子进程的正常退出状态 (只有在 WIFEXITED(${^CHILD_ERROR_NATIVE}) 为真时才有意义)

* WIFSIGNALED  
WIFSIGNALED(${^CHILD_ERROR_NATIVE}) 如果子进程由于信号而终止，则返回true

* WTERMSIG  
WTERMSIG(${^CHILD_ERROR_NATIVE})   返回子进程终止的信号(只有在WIFSIGNALED(${^CHILD_ERROR_NATIVE})为真时才有意义)

* WIFSTOPPED  
WIFSTOPPED(${^CHILD_ERROR_NATIVE}) 如果子进程当前已停止，则返回true(只有当你为waipid设置了WUNTRACED flag才能正常工作)

* WSTOPSIG  
WSTOPSIG(${^CHILD_ERROR_NATIVE})   返回停止子进程的信号(只有在WIFSTOPPED(${^CHILD_ERROR_NATIVE})为真时才有意义)

