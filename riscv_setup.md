## Simulate
Check if some global level make is needed. For example in davide's project, from root ```riscv```, launch
``` 
make all
```
Set up env path to Robert's toolchain:
```
bash
export RISCV=/home/balasr/.riscv
```
Launch interrupt example from ```riscv/tb/core```:
```
make interrupt-vism-run-gui
```
