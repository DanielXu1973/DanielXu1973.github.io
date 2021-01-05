# LLVM  Porting

#### Get source Code

- 获取llvm代码的两种方式：

  - 直接下载源码包 llvm-project-11.0.0tar.xz，然后解压

    ```
    xz -dc ../package/llvm-project-11.0.0.tar.xz | tar xf
    ```

    

  - 通过git从源码库直接下载，我下载的版本是 

    #(a485a59d2172daaee1d5e734da54fbb243f7d54c, Mon Dec 28 11:24:29 2020)

    ```
    git  clone  https://github.com/llvm/llvm-project.git
    ```

    

- 测试代码

  ```
  cd llvm-project
  mkdir build
  cd build
  
  export PATH=~/usr/local/bin:$PATH  {对cmake的版本有要求，重新编译新版本的cmake后放到了~/usr/local目录下}
  
  cmake -G "Unix Makefiles"  -DLLVM_TARGETS_TO_BUILD=MSP430 -DCMAKE_BUILD_type=debug -DCMAKE_INSTALL_PREFIX=/disk0/usr/local ../llvm
  
  make
  ```

  正常的情况下，上述的编译过程可以完整的结束，表明源码没有问题。

#### 开始移植

开始移植前，我们假定移植的是一个自定义的平台NT380，其架构和MSP430相同。

- 开始配置，并基于出现的错误而添加相应的文件

  ```
  cmake -G "Unix Makefiles"  -DLLVM_TARGETS_TO_BUILD=NT380 -DCMAKE_BUILD_type=debug -DCMAKE_INSTALL_PREFIX=/disk0/usr/local ../llvm/
  ............
  -- Looking for os_signpost_interval_begin - not found
  -- Found PythonInterp: /usr/bin/python (found version "2.7.12")
  -- Constructing LLVMBuild project information
  CMake Error at CMakeLists.txt:767 (message):
    Unexpected failure executing llvm-build: Usage: llvm-build [options]
  
    llvm-build: error: invalid target to enable: 'NT380' (not in project)
  
  
  -- Configuring incomplete, errors occurred!
  
  ```

  通过跟踪发现，是因为没修改LLVMBuild.txt文件导致的错误。

- 修改LLVMBuild.txt

  根据上述错误信息的跟踪，发现需要修改LLVMBuild.txt文件

  从顶层的llvm目录开始，在其下层相关的目录中均存在该文件，其中定义了在该目录下需要扫描及处理的下层目录。

- 添加文件

