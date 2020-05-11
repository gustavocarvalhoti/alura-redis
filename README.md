# Treinamento Alura - Redis I: Armazenando chaves e valores
https://cursos.alura.com.br/course/nosql-chave-valor-com-redis-1

## O que o Redis resolve?
Banco de dados chave-valor muito rápido, seria o resumo da tabela.
Ele guarda essas informações na memória, se desligar o PC perde as informações.

## Instalar
```bash
Criar container
$ docker run --name redis-treinamento-alura -p 6379:6379 -d redis:alpine
Entrar no container
$ docker exec -it redis-treinamento-alura sh
$ redis-cli
```

## Comandos
```bash
echo "REDIS"            -> Verifica o serviço e já retorna
SET "total_cursos" 105  -> Setar
GET "total_cursos"      -> Pegar
DEL "total_cursos"      -> Remover
KEYS *                  -> Lista Todas as chaves
KEYS "resultado:10*"    -> Lista filtrando
SET resultado:17-05-2020:megasena "2,3,4,5"     -> Padrão
MSET resultado:21-05-2020:megasena "2,3,4,5" resultado:22-05-2020:megasena "2,3,4,5"    -> Seta mais valores
KEYS "resultado:2?-05-2020:megasena"            -> Todas as chaves 20 ... 29 "? = 1 caracter"
KEYS "resultado:??-??-????:megasena"
KEYS "resultado:?[12]-??-????:megasena"         ->[12] Significa 1 ou 2
HSET resultado:24-05-2015:megasena "ganhadores" "Gustavo, Michelle"
HSET resultado:24-05-2015:megasena "numeros" "1,2,3"    -> Criando um objeto|hash -> megasena : { numeros: [], ganhadores: [] }
HDEL resultado:24-05-2015:megasena "numeros"            -> Deleta esse campo do objeto
HGETALL resultado:24-05-2015:megasena                   -> Tudo no Hash

Apagar usuário com um tempo definido
HSET "sessao:usuario:1675" "nome" "gustavo"
HSET "sessao:usuario:1675" "total_de_produtos" "3"
EXPIRE "sessao:usuario:1675" 60                         -> Remover depois de 60 segundos
TTL "sessao:usuario:1675"                               -> Verificar o tempo que falta

Gravando acesso de usuários
SET pagina:/contato:25-05-2020 1
INCR pagina:/contato:25-05-2020                         -> Adiciona mais 1
DECR pagina:/contato:25-05-2020                         -> value--
INCRBY pagina:/contato:25-05-2020 14                    -> Adiciona o valor que quer adicionar

SET compras:25-05-2020:valor 10
INCRBYFLOAT compras:25-05-2020:valor 9.99               -> Add float
INCRBYFLOAT "compras:25-05-2020:valor" -9.99            -> Funciona negatico também
GET compras:25-05-2020:valor

Bitset - Armazena os usuários logados - sequência de bits.
O comando SETBIT permite que seja representada uma sequência de valores binários, ou bitmaps.
Um bitmap funciona como um array que armazena zeros e uns associados a um offset.
SETBIT acesso:25-05-2015 15 1                           -> Usuário 15 acessou o sistema
SETBIT acesso:25-05-2015 16 1
GETBIT acesso:25-05-2015 15                             -> Retorna 1
SETBIT acesso:25-05-2015 1 1
SETBIT acesso:25-05-2015 2 1
SETBIT acesso:26-05-2015 1 1
SETBIT acesso:26-05-2015 3 1
SETBIT acesso:27-05-2015 3 1
SETBIT acesso:27-05-2015 2 1
SETBIT acesso:27-05-2015 1 1
BITCOUNT acesso:27-05-2015                              -> Conta os bits daquele dia
BITOP AND acesso:25-and-26-06-2015 acesso:25-05-2015 acesso:26-05-2015  -> Cria a condição, usuáro que acessou dia 25 e 26
GETBIT acesso:25-and-26-06-2015 1                       -> Verifica a condição, somente o usuário 1 retorna 1 - true
BITOP OR acesso:25-or-26-06-2015 acesso:25-05-2015 acesso:26-05-2015
BITCOUNT  acesso:25-or-26-06-2015                       -> Acessos nesse período
```
