# PIMPatches
Contains patches for public repositories to enable PIM 

# How to use PIM Patches

# ROCM platform patches
This section adds instructions for updating rocm 4.0.0 platform libraries to support PIM operations

## ROCK
[ROCK Kernel patch](rocm/0001-FIM-reservation-patch-from-AMD.patch) contains changes required in rocm kernel for reserving PIM specific area
during bootup and provide it to user whenever requested.
### Apply patch
```
git clone -b rocm-4.0.0 https://github.com/RadeonOpenCompute/ROCK-Kernel-Driver.git
cd ROCK-Kernel-Driver
git checkout -b rocm-4.0.0
cp <PIMPatches>/rocm/0001-FIM-reservation-patch-from-AMD.patch
git am 0001-FIM-reservation-patch-from-AMD.patch
```
### Build and Install
```
make rock-dbg_defconfig
make -j33 bzImage LOCALVERSION=-rock4-test
make -j33 bzImage LOCALVERSION=-rock4-test modules
sudo make modules_install
sudo make install
sudo grub-reboot 'Advanced options for Ubuntu>Ubuntu, with Linux 5.6.0-kfd-rock4-test'
sudo reboot
```

## HIP
[HIP Memory registration patch](rocm/0001-Register-FIM-memory-in-memory-Managemenr-of-HIP.patch) contains changes required in HIP for registering PIM memory in HIP for validating it. Otherwise PIM memory is unknown to HIP.
### Apply patch
```
git clone https://github.com/ROCm-Developer-Tools/HIP.git
export HIP_DIR="$(readlink -f HIP)"
cd $HIP_DIR
git checkout -b rocm-4.0.0
cp <PIMPatches>/rocm/0001-Register-FIM-memory-in-memory-Managemenr-of-HIP.patch .
git am 0001-Register-FIM-memory-in-memory-Managemenr-of-HIP.patch
```
### Build and Install
```
mkdir build;cd build
cmake -DCMAKE_BUILD_TYPE=Release -DHIP_COMPILER=clang -DHIP_PLATFORM=rocclr -DCMAKE_PREFIX_PATH="$ROCclr_DIR/build;/opt/rocm-4.0.0/" -DCMAKE_INSTALL_PREFIX=/opt/rocm-4.0.0 -DHSA_PATH=/opt/rocm-4.0.0 ..
make -j8
sudo make install
```

## ROCT-Thunk-Interface
[Interface for PIM memory](rocm/0001-Get-reserved-PIM-Area-to-user-as-block.patch) modifies ROCT-Thunk-Interface code to provide an interface for user to access reserved PIM memory from linux kernel.
### Apply patch
```
git clone -b rocm-4.0.0 https://github.com/RadeonOpenCompute/ROCT-Thunk-Interface.git
cd ROCT-Thunk-Interface/
git checkout -b rocm-4.0.0
cp <PIMPatches>/rocm/0001-Get-reserved-PIM-Area-to-user-as-block.patch .
git am 0001-Get-reserved-PIM-Area-to-user-as-block.patch
```

### Build and Install
```
mkdir build; cd build
cmake -DCMAKE_INSTALL_PREFIX=/opt/rocm-4.0.0 ..
make -j8
sudo make install
```

## MIOpen
[Enable PIM ](rocm/MIOpen-Enable-PIM-Operations.patch) modifies MIOpen framework to utilize PIM during gemm operation. By exporting ENABLE_PIM=1, user can make MIOpen execute gemm operation using PIM.
### Apply patch
```
 git clone -b rocm-4.0.0 https://github.com/ROCmSoftwarePlatform/MIOpen.git
 cd MIOpen/
 git checkout -b rocm-4.0.0
 cp <PIMPatches>/rocm/MIOpen-Enable-PIM-Operations.patch .
 git apply MIOpen-Enable-PIM-Operations.patch
```

### Build and Install
```
mkdir build;cd build/
CXX=/opt/rocm-4.0.0/llvm/bin/clang++ cmake -DBoost_USE_STATIC_LIBS=Off -DMIOPEN_BACKEND=HIP -DCMAKE_PREFIX_PATH=/opt/rocm-4.0.0 -DCMAKE_INSTALL_PREFIX=/opt/rocm-4.0.0 ..
make -j8
sudo make install
```
