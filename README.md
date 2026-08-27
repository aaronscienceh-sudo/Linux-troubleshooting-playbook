# Linux Troubleshooting Playbook

Coleção de cenários reais de troubleshooting em Linux, documentados no formato
**sintoma → diagnóstico → causa raiz → solução → prevenção**, simulando o
raciocínio de investigação usado no dia a dia de suporte técnico e infraestrutura.

## Objetivo

Este repositório foi criado como parte da minha preparação prática para atuar em
Cloud Support / IT Support, documentando problemas comuns reproduzidos em ambiente
real (Linux), com os comandos exatos utilizados, seus outputs e o raciocínio por
trás de cada etapa da investigação.

## Cenários documentados

| # | Cenário | Ferramentas principais |
|---|---------|------------------------|
| 01 | [Disco cheio](./01-disk-space/problema.md) | `df`, `du`, `sort` |
| 02 | [Processo consumindo CPU excessiva](./02-high-cpu-process/problema.md) | `top`, `kill` |
| 03 | [Falha de conectividade / DNS](./03-network-connectivity/problema.md) | `ip addr`, `ping`, `nslookup`, `resolv.conf` |

## Formato de cada cenário

Cada pasta contém um arquivo `problema.md` estruturado da seguinte forma:

- **Sintoma** — o que foi reportado/observado
- **Diagnóstico** — comandos utilizados, na ordem da investigação, com outputs reais
- **Causa raiz** — o que realmente causou o problema
- **Solução** — comando(s) de correção
- **Prevenção** — como evitar que o problema se repita

## Sobre

Projeto em construção contínua — novos cenários serão adicionados conforme
avanço na prática de troubleshooting.

**Aaron Alves**
[LinkedIn](https://linkedin.com/in/aaron-alves-00b07629a) · [GitHub](https://github.com/aaronscienceh-sudo)
