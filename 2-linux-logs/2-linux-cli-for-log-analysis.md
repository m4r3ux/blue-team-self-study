Objetivo - aprender a:
- Filtrar logs rapidamente
- Extrair dados importantes
- Correlacionar eventos
- Identificar ataques
- Trabalhar com grandes volumes de logs

---

Antes de prosseguir, precisamos entender o seguinte:

CLI não é sobre comandos isolados, é sobre combinar comandos com pipelines.

Exemplo:
`cat log.txt | grep "Failed" | awk '{print $11}' | sort | uniq -c`

Isso é o ccoração do trabalho de um analista SOC.

---

## Ferramentas a serem dominadas

| Ferramenta | Função          |
| ---------- | --------------- |
| grep       | filtrar         |
| awk        | extrair campos  |
| sed        | modificar texto |
| cut        | cortar campos   |
| sort       | ordenar         |
| uniq       | contar          |

---

## GREP (Filtro de logs)

Uso básico:
`grep "Failed password" /var/log/auth.log`

Passamos o comando + palavra-chave + caminho

Podemos com esse comando passar múltiplos filtros, como por exemplo:
`grep -E "Failed|Accepted" /var/log/auth.log`

ignorar maiúsculas/minúsculas
`grep -i "error" syslog`

ver contexto (o que aconteceu antes ou depois)
`grep -A 3 "Accepted" /var/log/auth.log`

---

## AWK (o mais poderoso)

O `awk` trabalha com colunas (campos)

Exemplode log
`Mar 16 sshd: Failed password for root from 192.168.1.10 port 22`

Para pegar o IP do atacante, usaríamos por exemplo:
`grep "Failed password" /var/log/auth.log | awk '{print $11}'`

Para pegar o usuário 
`grep "Failed password" /var/log/auth.log | awk '{print $9}'

Ele funciona por colunas e as colunas são separadas pelos espaços dentro do log.

No comando abaixo, por exemplo, pude extrair dos logs falhos o usuário da tentativa, o endereço IP do atacante e a porta pela qual tentou acessar - ainda, com `| sort | uniq -c` consegui contar repetições e padrões, ocmo, qual IP tentou mais ou algo do tipo:
`grep -a "Failed" /var/log/auth.log | awk '{print $9, $11, $13}' | sort | uniq -c`

Adicionando um `| sort -nr` ordenamos do maior pro menor em número de frequência.
`grep -a "Failed" /var/log/auth.log | awk '{print $9, $11, $13}' | sort | uniq -c | sort -nr`



