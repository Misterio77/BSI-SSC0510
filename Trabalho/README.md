# Trabalho - Arquitetura RISC-V

Vídeo do trabalho aqui (Esperando drive processar o upload)

- Enzo Bustamante, 9863437
- Gabriel Fontes, 10856803
- Giovanna Fardini, 10260671
- Thales Damasceno, 11816150
- Vinicius Baca, 10788589

## Introdução

- Arquitetura RISC - _load/store_; 
- _Open Standard Instruction Set Architecture_ (ISA) - menor risco de obsolência, permite intercambiação em _hardware_ e _software_;
- Licenças _open source_ permissivas;
- Instruções divididas em: padrão (não podem ter conflito entre si) e não padrão (extensões que podem ser especializadas e conflitarem entre si).
- Projetado para suportar customização e especialização
    - Instruções podem ser estendidas pela adição de um ou mais conjuntos, mas instruções nativas não podem ser redefinidas;
- Instruções de base inteira de 32 bits e reconhecimento de instruções de tamanho variável múltiplas de 16 bits. 
    - Duas variações - RV32I e RV64I (e desenvolvimento de uma versão de 128 bits ainda em estudo).

![figure 1: RISC-V instruction length encoding](https://i.imgur.com/nJFsy0a.png)


## Ordenação de bytes
- Sistema de memória _little endian_ (mais comum no mercado e natural para desenvolvedores);
- Possibilidade em aberto de adição de estrutura não padrão _big endian_

![figure 2](https://i.imgur.com/GHqXoqK.png)

## Instruções
- 4 formatos de instruções atômicos (R, I, S, U);
- Mantém os registradores fonte e destino sempre na mesma posição e ordem no código de todas as instruções.

![figure 3](https://i.imgur.com/IYpFnLj.png)

## Instruções de codificação imediata
- +2 variações no formato de instruções (B, J) para tratar codificação imediata;
- B é similar à S, exceto que os bits do meio ficam em posições fixas e o bit de menor ordem no formato S corresponderá ao bit de maior ordem no formato B;
- U é similar à J, exceto que em U os 20 bits reservados são shiftados 12 bits para à esquerda enquanto que em J são shiftados somente 1 bit à esquerda

![figure 4](https://i.imgur.com/KkQAymw.png)

## Instruções de computação de inteiros
- Codificadas como:
    - registrador-imediata: usa o tipo I - ADDI, SLTI (set less than immediate), SLTIU para tipo _unsigned_, ANDI, ORI, XORI, instruções de _shift_ por uma constante.
    - registrador-registrador: usa o tipo R - ADD, SUB, AND, OR, XOR, SLT, SLTU, SLL, SRL, SRA e NOP.

Registrador-imediata:

![figure 5](https://i.imgur.com/Jh6SIBm.png)
![figure 6](https://i.imgur.com/YSKeT8D.png)

Registrador-registrador:

![figure 7](https://i.imgur.com/i02iHln.png)
![figure 8](https://i.imgur.com/FOKf86F.png)

## Instruções de transferência de controle
- 2 tipos: 
    - Jumps incondicionais
        - JAL ( jump and link - tipo J), JALR (jump and link register - tipo I), 
    - Desvios condicionais (sempre são do tipo B).
        - BEQ e BNE (igual ou diferente, respectivamente), BLT e BLTU (menor que), BGE e BGEU (maior que). 

- Jumps incondicionais: usam endereçamento relativo de PC como suporte a código de posição independente.
    - Pilhas de previsão de endereço de retorno: codificadas implicitamente via o número dos registradores usados.
        - JAL só dá um push quando rd = x1/x5.
        - JALR dá push/pop de acordo com a tabela abaixo.

![figure 9](https://i.imgur.com/NF37qvI.png)

Jumps incondicionais:

![figure 10](https://i.imgur.com/bN2HFlf.png)
![figure 11](https://i.imgur.com/0oABh64.png)

Desvios condicionais:

![figure 12](https://i.imgur.com/9eONGRf.png)

## Instruções de leitura e escrita na memória
- Somente as instruções de load/store tem permissão de acessar a memória;
- O RV32I libera um espaço de memória ao usuário de 32 bits que é byte-addressed e little-endian;
- Load é do tipo I e store é do tipo S.
- As instruções devem estar alinhadas para cada tipo de dados, a base ISA fornece suporte para acessos desalinhados, mas existe perda de performance.

![figure 13](https://i.imgur.com/mP3CQYV.png)

## Modelo de memória
- Modelo de memória relaxado - mais compatível com futuras extensões e maior performance com implementações mais simples;
- RISC-V permite a execução de threads  (hart) concorrentes em um único espaço de endereço de usuário;
- Cada thread tem seu próprio PC, registrador de estado e executa uma sequência de instruções independentes;
- As threads podem se comunicar por chamadas ou por sistemas de memória compartilhada;
- Tem uma instrução ‘FENCE’ para garantir que operações na memória de diferentes threads ocorram na ordem correta.

![figure 14](https://i.imgur.com/BF5Mujl.png)

## Instruções de controle e estado
- Usadas para acessar funcionalidades que podem precisar de acesso autorizado e são codificadas como tipo I;
- 2 classes:
    - CSRs - controle de leitura-edição-escrita atômica de registradores de controle e  estado.
    - Outras instruções privilegiadas.
- CSRRW - troca os valores nos CSRs e registradores de inteiros;
- CSRRS - lê, seta os bits nos CSRs e salva em um registrador de inteiros;
- CSRRC - lê e limpa os bits nos CSRs.
- CSRWI/CSRRSI/CSRRCI - análogas, mas obtém os valores a partir de imediatos;

![figure 15](https://i.imgur.com/w0Snrj2.png)

## Instruções de chamada e interrupção
- ECALL - usada para fazer uma requisição ao SO;
- EBREAK - usada por debuggers.

![figure 16](https://i.imgur.com/vBn7eJQ.png)

## Outras versões do RISC-V
- RV32E - versão reduzida do RV32I para sistemas embarcados (reduz o número de registradores para 16 e remove os contadores;
- RV64I - suporta espaço de endereçamento de 64 bits;
- RV128I (em desenvolvimento) - suporta espaço de endereçamento de 128 bits, sendo uma extrapolação dos RV32I e RV64I.

## Placas comerciais
- SiFive (https://sifive.com):
    - Fundadores fazem parte da equipe criadora da arquitetura
    - System On Chip SiFive Freedom (capazes de rodar Linux):
        - HiFive Unmatched: 16GB DDR4, MicroSD e M.2, com slot PCIe
        - HiFive Unleashed: 8GB DDR4, MicroSD (descontinuado)
    - Micro Controlador: HiFive1
- BeagleBoard (https://beagleboard.org):
    - BeagleV
- Outras empresas: https://riscv.org/exchange/boards/

## Software disponível
Bastante coisa crucial já funciona:

- Emulação: QEMU e muitos outros…
- Toolchain: LLVM, Binutils…
- Compiladores e Libs C: LLVM, GCC, Clang, Glibc, Musl…
- Bootloaders: U-Boot, Coreboot…
- Outros compiladores e Runtimes: Go, OCaml, OpenJDK, Rust, NodeJS, Mono, Ada, Free Pascal…
- E muito mais: https://riscv.org/exchange/software/

## Linux e BSDs
Algumas distros já podem executar bem em hardware (normalmente da SiFive) ou em VMs:

- Debian: https://wiki.debian.org/RISC-V
- Ubuntu: https://wiki.ubuntu.com/RISC-V
- Gentoo: https://wiki.gentoo.org/wiki/Project:RISC-V
- Fedora: http://fedoraproject.org/wiki/Architectures/RISC-V
- NixOS: https://github.com/NixOS/nixpkgs/issues/101651
- Parabola: https://github.com/oaken-source/parabola-cross-bootstrap
- FreeBSD: https://wiki.freebsd.org/riscv
