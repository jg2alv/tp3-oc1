# Cache Flow: an interactive Python cache simulator with FIFO replacement

Cache Flow makes memory placement visible one access at a time. The team-built notebook processes hexadecimal address traces under direct-mapped, set-associative, and fully associative configurations, showing how each access changes the cache and its hit-and-miss counters.

## 1. Academic context and scope

- **Institution:** Universidade Federal de Minas Gerais (UFMG)
- **Degree:** Ciência da Computação
- **Course:** Organização de Computadores 1
- **Assignment:** Trabalho Prático 3
- **Semester:** 2025-01
- **Collaboration:** Team

Cache capacity, line size, and the number of lines per set define the organization. The model uses 32-bit byte addresses and tracks block placement rather than the contents of main memory. The notebook combines the simulator, example traces, saved outputs, tables, and an interactive history slider.

## 2. How to run

Open [TP3_OC1_Simulador_de_Memória_Cache.ipynb](TP3_OC1_Simulador_de_Mem%C3%B3ria_Cache.ipynb) in Google Colab, the platform specified by the assignment, or a compatible Python 3 Jupyter environment with pandas, IPython, and ipywidgets available.

Execute the `Cache` definition first, then the visualization function, then the desired example cell. The display code uses pandas `Styler.map`; a compatible pandas version and working notebook widget support are required. The repository does not pin package versions.

Each example constructs a fresh cache, submits its address list through `process_access`, and passes the history and cache parameters to `imprimir_resultados`. Inputs are defined in the notebook rather than read from standard input. There is no separate command-line entry point.

## 3. Usage example

The first example is already included in the notebook. It uses a 4,096-byte cache, 1,024-byte lines, and four lines per set:

```python
cache_size = 4096
line_size = 1024
associativity = 4

entrada1 = [
    '0x0CB886CA',
    '0x06BC89BA',
    '0x379CBAD1',
    '0x28F3123B',
    '0x06F98ED5'
]

simulador = Cache(cache_size, line_size, associativity)
for addr in entrada1:
    simulador.process_access(addr)

imprimir_resultados(
    simulador.getHistory(), cache_size, line_size, associativity
)
```

The four cache lines form one fully associative set. The fifth distinct block requires replacing the oldest entry under FIFO.

## 4. Implementation decisions

The cache stores a list of lines and a FIFO queue for each set. Empty lines fill in order; once full, the oldest line index is reused. A hit increments its counter without changing FIFO order, keeping this policy distinct from least recently used replacement.

Address decomposition removes offset and set bits to identify the stored block tag. Each access appends an `addr`, `group`, `idx_line`, and `result` record. The `addr` field contains the derived tag, not the original byte address. The visualization reconstructs cache states from that history, letting the slider expose intermediate placement decisions.

## 5. Tests and evaluation

Six example cells contain traces and saved outputs for fully associative, two-way set-associative, and direct-mapped configurations. They provide concrete inspection cases rather than an automated assertion suite or a general performance benchmark.

### Verified results

Reference output provided in the assignment for Example 1:

```text
=== Histórico de Acessos ===
      addr group idx_line result
0x00032E21     0        0   MISS
0x0001AF22     0        1   MISS
0x000DE72E     0        2   MISS
0x000A3CC4     0        3   MISS
0x0001BE63     0        0   MISS

=== Estado Final da Cache ===
GROUP: 0, LINE: 0, ADDR: 0x0001BE63
GROUP: 0, LINE: 1, ADDR: 0x0001AF22
GROUP: 0, LINE: 2, ADDR: 0x000DE72E
GROUP: 0, LINE: 3, ADDR: 0x000A3CC4

HITs: 0
MISSes: 5
```

## 6. Learning and limitations

The main challenge was keeping address decomposition, cache state, replacement order, and history consistent. This connected memory-hierarchy concepts with inspectable state transitions.

The model omits actual block contents, memory latency, and multilevel caches. Its bit-based decomposition assumes suitable power-of-two configurations; the constructor does not comprehensively validate those assumptions.

## 7. Authorship and contribution

**My contribution:** implemented the cache and all hit-and-miss logic, including block identification, set selection, access accounting, FIFO replacement, and explanatory inline comments. The notebook presentation is part of the collective project.
