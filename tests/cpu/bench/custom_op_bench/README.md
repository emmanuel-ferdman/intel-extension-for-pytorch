# Running benchmarks for Intel Extension for PyTorch Custom OPs
Evaluate performance for custom operator with [launcher](../../../../docs/tutorials/performance_tuning/launch_script.md).
## Prepare envrioment
Follow [performance_tuning_guide](../../../../docs/tutorials/performance_tuning/tuning_guide.md) to install Memory_Allocator(you can choose Tcmalloc or Jemalloc).
Install intel-openmp:

```
pip install intel-openmp=2024.1.2
```

## Evaluate [Interaction](../../../../intel_extension_for_pytorch/cpu/nn/interaction.py)

1.Inference: 1 instance per core in real world scenario

```
export OMP_NUM_THREADS=1
export CORES=`lscpu | grep Core | awk '{print $4}'`
python -m intel_extension_for_pytorch.cpu.launch --node-id 0 interaction.py --num-instance=$CORES --inference # for fp32
python -m intel_extension_for_pytorch.cpu.launch --node-id 0 interaction.py --num-instance=$CORES --inference --bf16 # for bf16
```
unset OMP_NUM_THREADS
2.Training: 1 instance on 1 socket in real world scenario

```
python -m intel_extension_for_pytorch.cpu.launch --node-id 0 interaction.py # for fp32
python -m intel_extension_for_pytorch.cpu.launch --node-id 0 interaction.py --bf16 # for bf16
```

## Evaluate IPEX fused optimizer
```
python -m intel_extension_for_pytorch.cpu.launch --node-id 0 optimizer.py --optimizer sgd # for sgd
python -m intel_extension_for_pytorch.cpu.launch --node-id 0 optimizer.py --optimizer lamb # for lamb
python -m intel_extension_for_pytorch.cpu.launch --node-id 0 optimizer.py --optimizer adagrad # for adagrad
python -m intel_extension_for_pytorch.cpu.launch --node-id 0 optimizer.py --optimizer adam # for adam
```

## Evaluate IPEX [MergedEmbeddingBag](../../../../intel_extension_for_pytorch/nn/modules/merged_embeddingbag.py)
```
export CORES=`lscpu | grep Core | awk '{print $4}'`
export BATCHSIZE=$((128*CORES))
python -m intel_extension_for_pytorch.cpu.launch --node-id 0 merged_embeddingbag.py --inference  --batch-size=${BATCHSIZE}
python -m intel_extension_for_pytorch.cpu.launch --node-id 0 merged_embeddingbag.py --inference --with-cat --batch-size=${BATCHSIZE}

python -m intel_extension_for_pytorch.cpu.launch --node-id 0 merged_embeddingbag.py  --batch-size=${BATCHSIZE} --optimizer=sgd
python -m intel_extension_for_pytorch.cpu.launch --node-id 0 merged_embeddingbag.py  --batch-size=${BATCHSIZE} --optimizer=adagrad
```
