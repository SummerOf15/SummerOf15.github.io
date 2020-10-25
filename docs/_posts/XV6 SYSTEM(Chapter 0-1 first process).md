---

layout: _hight
title: XV6 SYSTEM(Chapter 0-1 first process)
date: 2019-12-06 22:44:26
tags: XV6 operation system

---

### I/O and File descriptors

- A file descriptor is a integer to represent the index of a file. 
- 3 status: 0 (standard input), 1 (standard output), 2 (standard error). These status are used to refer to a console, a file or a pipe.

<!-- more -->

##### 1. fork() #####

 create a new child process which share the memory and data with parent process. It have three returns:

| <0   | create unsuccessfully        |
| ---- | ---------------------------- |
| =0   | return the new subprocess    |
| >0   | Returned to parent or caller |

 ##### 2. wc #####

word count.

```c
int p[2];// records read and write file descriptors
char *argv[2];
argv[0] = "wc";
argv[1] = 0;
pipe(p);// create a pipe
if(fork() == 0) {// in child process, it will be used to read data
    close(0);//close pre-opened read process
    dup(p[0]);//dup the read process
    close(p[0]);//close the read and write process
    close(p[1]);// close write process avoid if there is no 'eof'
    exec("/bin/wc", argv);
} else {
    write(p[1], "hello world\n", 12);
    close(p[0]);
    close(p[1]);
}
```

- pipe

  ```c
  echo hello world | wc
  ```

  which can be implemented without pipes as

  ```c
  echo hello world >/tmp/xyz; wc </tmp/xyz
  ```

  key difference between pipes and temporary files

  - temporary files need to be removed while pipes don't need
  - pipes can pass arbitrarily long streams of data while temporary file needs enough space
  - pipes allow for synchronization while temporary file is sequential.

### the first process

the structure of a process in xv6

```c
// Per−process state
struct proc {
uint sz; // Size of process memory (bytes)
pde_t* pgdir; // Page table, mapping the virtual address to physicial address 
char *kstack; // Bottom of kernel stack for this process
enum procstate state; // Process state, including 'is allocated','ready to run','running','waiting','exiting'
int pid; // Process ID
struct proc *parent; // Parent process
struct trapframe *tf; // Trap frame for current syscall
struct context *context; // swtch() here to run process
void *chan; // If non−zero, sleeping on chan
int killed; // If non−zero, have been killed
struct file *ofile[NOFILE]; // Open files
struct inode *cwd; // Current directory
char name[16]; // Process name (debugging)
};
```

- ![image.png](http://ww1.sinaimg.cn/mw690/006c6RCqgy1g9min8jd0vj30w60ifgt2.jpg)

- **boot process**
  - `1` When the PC turns on, it loads a **boot loader**, and the boot loader will load xv6 kernel. In xv6, the boot loader load the kernel from the address 0x100000, not from 0x80100000 because some small machines **do not have such a high address**; not from 0x0 because **the address range 0xa0000:0x100000 contains I/O devices**(BIOS).
  - `2 ` Before loading the page table, it maps the virtual addresses 0x0-0x400000 to the physical addresses 0x0-0x400000
  - `3` It load the page table and map the address from 0x80000000 in virtual address to 0x0-0x400000 in physical addresses.
  - `4` With the offset, the register can translate the address and it will jump to high address and enter the **main** function. 
  - `5` After **main** initializes several devices and subsystems, it create the first process by calling **userinit**.
  
- X86 registers

  32位CPU所含有的寄存器有：

  4个数据寄存器(EAX、EBX、ECX和EDX)
  2个变址和指针寄存器(ESI和EDI) 2个指针寄存器(ESP和EBP)
  6 segment registers(ES、CS、SS、DS、FS和GS)
  1个指令指针寄存器(EIP) 1个标志寄存器(EFlags)

```c
// Set up first user process.
void
userinit(void)
{
    struct proc *p;
    extern char _binary_initcode_start[], _binary_initcode_size[];

    p = allocproc();
    initproc = p;
    if((p−>pgdir = setupkvm()) == 0)// create page table for kernel use memory mapping
    	panic("userinit: out of memory?");
    // create the page table and copy the binary to the page
    inituvm(p−>pgdir, _binary_initcode_start, (int)_binary_initcode_size);
    p−>sz = PGSIZE;
    memset(p−>tf, 0, sizeof(*p−>tf));
    p−>tf−>cs = (SEG_UCODE << 3) | DPL_USER;
    p−>tf−>ds = (SEG_UDATA << 3) | DPL_USER;
    p−>tf−>es = p−>tf−>ds;
    p−>tf−>ss = p−>tf−>ds;
    p−>tf−>eflags = FL_IF;
    p−>tf−>esp = PGSIZE;
    p−>tf−>eip = 0; // beginning of initcode.S

    safestrcpy(p−>name, "initcode", sizeof(p−>name));
    p−>cwd = namei("/");

    p−>state = RUNNABLE; // set the process as runnable
}
```

- - `6` After **userinit**, **main** will run to **mpmain** to set CPU. It will calls **scheduler()** to start running processes.

```c
// Per−CPU process scheduler.
// Each CPU calls scheduler() after setting itself up.
// Scheduler never returns. It loops, doing:
// − choose a process to run
// − swtch to start running that process
// − eventually that process transfers control
// via swtch back to the scheduler.
 void scheduler(void)
 {
 	struct proc *p;
 	for(;;)
    {
 		// Enable interrupts on this processor.
 		sti();
 		// Loop over process table looking for process to run.
 		acquire(&ptable.lock);
 		for(p = ptable.proc; p < &ptable.proc[NPROC]; p++)
        {
 			if(p−>state != RUNNABLE)
				continue;

            // Switch to chosen process. It is the process’s job
            // to release ptable.lock and then reacquire it
            // before jumping back to us.
            proc = p;
            switchuvm(p); // start using the target process's page table
            p−>state = RUNNING;
            swtch(&cpu−>scheduler, proc−>context);// save and switch
            switchkvm();
            // Process is done running for now.
            // It should have changed its p−>state before coming back.
            proc = 0;
		}
		release(&ptable.lock);
	}
}
```

### Reference

MIT XV6 book: http://pdos.csail.mit.edu/6.828/2012/xv6/book-rev7.pdf

Chinese version:  https://github.com/ranxian/xv6-chinese 