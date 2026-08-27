# Disco cheio — diagnóstico e resolução

## Sintoma
Sistema apresentando lentidão / avisos de pouco espaço em disco. Necessário identificar
o que está consumindo o espaço e liberar armazenamento.

## Diagnóstico

### Cenário A — investigação sem suspeito prévio (fluxo real de troubleshooting)

Quando não se sabe de antemão qual pasta está consumindo o espaço, a investigação
começa no diretório raiz e desce nível por nível:

**1. Verificar o uso geral de disco por partição:**
```bash
df -h
```
Identifica qual partição está com pouco espaço (ex: `/` em 95% de uso).

**2. Listar as maiores pastas do sistema, já ordenadas:**
```bash
du -sh /* 2>/dev/null | sort -rh | head -10
```
- `du -sh /*` calcula o tamanho de cada pasta principal (`/home`, `/var`, `/usr`, etc.)
- `2>/dev/null` oculta erros de "permissão negada" em pastas de sistema restritas
- `sort -rh` ordena do maior para o menor consumo
- `head -10` mostra apenas as 10 maiores, facilitando a leitura

**3. Entrar na pasta identificada como maior consumidora e repetir o processo:**
```bash
cd /pasta-identificada
du -sh */ 2>/dev/null | sort -rh | head -10
```
Repetir esse passo, descendo nível por nível, até chegar na pasta ou arquivo específico
responsável pelo consumo.

### Cenário B — quando já se suspeita de uma pasta específica

**1. Verificar o uso geral de disco por partição:**
```bash
df -h
```

**2. Confirmar o tamanho da pasta suspeita e suas subpastas:**
```bash
cd ~
du -sh */
```
`du` (disk usage) calcula o tamanho de arquivos e pastas.
- `-s` resume o total de cada pasta (em vez de listar cada arquivo individualmente)
- `-h` exibe em formato legível (GB/MB)
- `*/` aplica o comando em todas as subpastas do diretório atual

**Resultado obtido (teste prático):**
```
260K  Área de trabalho/
4,0K  Documentos/
234M  Downloads/
2,4M  Imagens/
44K   Modelos/
4,0K  Músicas/
4,0K  Público/
2,1G  teste-disco/   ← maior consumidor identificado
4,0K  Vídeos/
```

## Causa raiz
A pasta `teste-disco/` continha um arquivo de 2,1GB não removido, consumindo
espaço significativo de armazenamento.

## Solução
```bash
rm -rf ~/teste-disco
```
`rm -rf` remove a pasta e todo o seu conteúdo de forma recursiva (`-r`) e sem
solicitar confirmação por arquivo (`-f`).

**Verificação pós-correção:**
```bash
df -h
```
Confirma que o espaço em disco foi liberado.

## Prevenção
- Monitorar espaço em disco periodicamente com `df -h` ou ferramentas de alerta automatizado
- Configurar rotação/limpeza automática de logs e arquivos temporários
- Investigar pastas de grande volume antes que o disco atinja capacidade crítica (ex: >85% de uso)

## ⚠️ Nota de segurança
O comando `rm -rf` é irreversível — não há lixeira. Sempre confirmar o caminho exato
antes de executar, especialmente evitando rodá-lo em `/` ou `~` sem um subdiretório
específico.
