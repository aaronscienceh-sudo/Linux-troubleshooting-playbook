# Falha de conectividade / resolução DNS — diagnóstico e resolução

## Sintoma
Usuário relata não conseguir acessar um site específico, com mensagem de erro
ao tentar `ping` ou abrir a página no navegador.

## Diagnóstico

**1. Verificar as interfaces de rede da máquina:**
```bash
ip addr
```
`ip addr` (substitui o antigo `ifconfig`) lista todas as interfaces de rede ativas
e seus respectivos endereços IP. Importante identificar:
- Se a interface está com `state UP` (ativa)
- O IP atribuído (`inet`)
- Presença de interfaces adicionais como VPNs (ex: Tailscale), que podem interferir
  no roteamento de tráfego

**2. Testar conectividade básica com um domínio (teste de nome que não existe, para
referência de como o erro se apresenta):**
```bash
ping -c 2 site-que-nao-existe-123456.com
```

**Resultado obtido:**
```
ping: site-que-nao-existe-123456.com: Nome ou serviço desconhecido
```

Esse erro indica falha na **resolução de nome** (DNS) — o sistema não conseguiu
traduzir o domínio para um endereço IP. É importante diferenciar esse cenário de
um domínio real que falha por problema de configuração.

**3. Isolar se o problema é de rede física ou de DNS, testando por IP direto
(sem depender de resolução de nome):**
```bash
ping -c 2 8.8.8.8
```
Se esse teste funcionar, a conectividade de rede está OK — o problema está
isolado na camada de DNS.

**4. Testar a resolução DNS especificamente, com um domínio válido:**
```bash
nslookup google.com
```

**Resultado obtido:**
```
Server:  127.0.0.53
Address: 127.0.0.53#53

Non-authoritative answer:
Name:    google.com
Address: 172.217.162.238
Name:    google.com
Address: 2800:3f0:4001:80c::200e
```

Observação: `127.0.0.53` é o resolvedor DNS local (`systemd-resolved`, comum em
distribuições baseadas em Ubuntu), que repassa a consulta para o DNS configurado
na rede. A resposta trouxe corretamente tanto o endereço IPv4 quanto IPv6 do
domínio, confirmando que a resolução DNS está funcionando normalmente.

**5. Verificar qual servidor DNS a máquina está configurada para usar:**
```bash
cat /etc/resolv.conf
```

## Causa raiz
No teste realizado, o domínio consultado (`site-que-nao-existe-123456.com`)
simplesmente não existe — comportamento esperado do DNS.

Em um cenário real, esse mesmo tipo de erro ("Nome ou serviço desconhecido")
apontaria para uma das seguintes causas prováveis:
- Servidor DNS configurado na máquina está fora do ar ou inacessível
- Configuração de DNS incorreta em `/etc/resolv.conf`
- Falha no serviço `systemd-resolved` (ou equivalente) local
- Problema no DNS do provedor de internet ou da rede corporativa

## Solução

Caso o problema seja de fato um DNS mal configurado ou inacessível (não um domínio
inexistente), a correção seria:

**Opção 1 — reiniciar o serviço de resolução de DNS local:**
```bash
sudo systemctl restart systemd-resolved
```

**Opção 2 — testar com um DNS público confiável, editando `/etc/resolv.conf`
ou a configuração de rede para usar:**
```
nameserver 8.8.8.8
nameserver 1.1.1.1
```

**Verificação pós-correção:**
```bash
nslookup google.com
```
Confirma que a resolução DNS voltou a funcionar corretamente.

## Prevenção
- Monitorar a disponibilidade do servidor DNS configurado
- Manter um DNS público como fallback (secundário) na configuração de rede
- Documentar interfaces de rede adicionais (VPNs, túneis) presentes na máquina,
  já que podem interferir no roteamento e devem ser consideradas na investigação
