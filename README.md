# Template dos tutoriais

1. Realizar um fork do repositório
1. Clonar esse repositório
1. Instalar dependências 
1. Editar arquivo `docs-src/index.md`
1. Testar com mkdocs

## Instalar dependências 

O site da disciplina faz uso do mkdocs com o tema material, para instalar, execute no bash:

``` sh
pip install -r requirements.txt
```

## Exemplo

O arquivo `docs-src/index.md` possui exemplos de estruturas que podem ser utilizadas no tutorial, mais podem ser encontradas em:

- https://squidfunk.github.io/mkdocs-material/reference/formatting/

Para visualizar o exemplo, acesse:

- https://insper.github.io/Embarcados-Avancados-Template/

## Testando

Para testar abra o terminal na raiz desse repositório e digite:

``` sh
mkdocs serve
```

Agora você pode acessar a página: https://localhost:8000 para verificar o resultado.

## Deploy github




## u-boot.src
if fatload mmc 0:1 $fpgadata soc_system.rbf; then
    fpga load 0 $fpgadata $filesize;
fi;
run bridge_enable_handoff;
run mmcload;
run mmcboot;

### Configuração de bridges e handoffs
bridge_enable_handoff=
    mw $fpgaintf ${fpgaintf_handoff};
    go $fpga2sdram_apply;
    mw $fpga2sdram ${fpga2sdram_handoff};
    mw $axibridge ${axibridge_handoff};
    mw $l3remap ${l3remap_handoff};

### Carregamento de arquivos do cartão MMC/SD
mmcload=
    mmc rescan;
    ${mmcloadcmd} mmc 0:${mmcloadpart} ${loadaddr} ${bootimage};
    ${mmcloadcmd} mmc 0:${mmcloadpart} ${fdtaddr} ${fdtimage};

### Inicialização do sistema operacional
mmcboot=
    setenv bootargs console=ttyS0,115200 root=${mmcroot} rw rootwait;
    bootz ${loadaddr} - ${fdtaddr};


