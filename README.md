# Treinamento Alura - Redis I: Armazenando chaves e valores
https://cursos.alura.com.br/course/nosql-chave-valor-com-redis-1

# Treinamento Alura - Redis II: Estruturas e recursos na sua base NoSQL 
https://www.alura.com.br/curso-online-nosql-chave-valor-com-redis-2

## O que o Redis resolve?
Banco de dados chave-valor muito rápido, seria o resumo da tabela.
Ele guarda essas informações na memória, se desligar o PC perde.

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

# Apagar usuário com um tempo definido
HSET "sessao:usuario:1675" "nome" "gustavo"
HSET "sessao:usuario:1675" "total_de_produtos" "3"
EXPIRE "sessao:usuario:1675" 60                         -> Remover depois de 60 segundos
TTL "sessao:usuario:1675"                               -> Verificar o tempo que falta

# Gravando acesso de usuários
SET pagina:/contato:25-05-2020 1
INCR pagina:/contato:25-05-2020                         -> Adiciona mais 1
DECR pagina:/contato:25-05-2020                         -> value--
INCRBY pagina:/contato:25-05-2020 14                    -> Adiciona o valor que quer adicionar

SET compras:25-05-2020:valor 10
INCRBYFLOAT compras:25-05-2020:valor 9.99               -> Add float
INCRBYFLOAT "compras:25-05-2020:valor" -9.99            -> Funciona negatico também
GET compras:25-05-2020:valor

# Bitset - Armazena os usuários logados - sequência de bits.
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

# Listas, ordenação e limites
LPUSH ultimas_noticias "Notícia 0"      <- Coloca na esquerda, posição 0, ultima noticia
LPUSH ultimas_noticias "Notícia 1"      <- Coloca antes de todos que existem
LPUSH ultimas_noticias "Notícia 2"
LPUSH ultimas_noticias "Notícia 5" "Notícia 6" "Notícia 7"
RPUSH ultimas_noticias "Notícia 4"      <- Colocar no fim
Nesse caso ele exibe, Notícia 2, Notícia 1, Notícia 0
LTRIM ultimas_noticias 0 2              <- Somente os 3 últimos, posição 0, 1, 2
LINDEX ultimas_noticias 0               <- Mostra o que tem no índice 0, Notícia 2
LLEN ultimas_noticias                   <- Tamanho da lista
LRANGE ultimas_noticias 0 2             <- Exibe do índice 0 até 2

# Fila de processamento de tarefas
RPUSH "fila:confirma-email" "gustavocarvalho_ti@gmail.com"
RPUSH "fila:confirma-email" "michelle@gmail.com"
RPUSH "fila:confirma-email" "duda@gmail.com"
RPUSH "fila:confirma-email" "davi@gmail.com"
LPOP fila:confirma-email                <- Retorna o primeiro da lista e remove ele, a fila andou
BLPOP fila:confirma-email 30            <- Se não tem nada ele espera 30 segundos para retornar uma resposta, ajuda no consumo de recursos, se chegou ele já responde
BLPOP fila:confirma-email 0             <- Até chegar ele trava a conexão
BRPOP fila:confirma-email 0

# Conjunto, não tem uma ordem, não adiciona repetido, relacionamento entre elementos
SADD "relacionamentos:guilherme" "daniela" "carlos" "ana" "lucia"
SCARD "relacionamentos:guilherme"       <- Quantos elementos tem dentro
SMEMBERS "relacionamentos:guilherme"    <- Lista os elementos
SISMEMBER "relacionamentos:guilherme" "ana" <- A ana faz parte desse conjunto?
SREM "relacionamentos:guilherme" "daniela"  <- Remove
# Lista os amigos em comum entre os dois conjuntos - "lucia"
SADD "relacionamentos:carlos" "paulo" "lucia" "guilherme"
SINTER "relacionamentos:guilherme" "relacionamentos:carlos"
# Lista os amigos que o guilherme tem e a daniela não tem
SADD "relacionamentos:marcela" "ana" "daniela"
SDIFF "relacionamentos:guilherme" "relacionamentos:marcela"  <- Diferença entre dois conjuntos
SUNION "relacionamentos:guilherme" "relacionamentos:marcela" <- União dos conjuntos

# Mostrar informações ordenadas
SET jogador:1 50076
TYPE "jogador:1"                                            <- Descobre o tipo
DEL "jogador:1"
HSET "jogador:1" "pontos" 50076 "nome" "gustavo"            <- Salva em tabela, tipo Hash
HSET "jogador:2" "pontos" 76564 "nome" "carlos"
HGETALL "jogador:1"
HINCRBY "jogador:1" "pontos" 10                             <- Adiciona 10 pontos
HINCRBYFLOAT "jogador:1" "pontos" 10.5
# Adiciona na lista ordenada
ZADD pontuacoes 50076 gustavo 65543 carlos 33786 daniela 8754 paulo
ZCARD pontuacoes                                            <- Quantos elementos dentro
ZRANGE pontuacoes 0 3 WITHSCORES                            <- Lista os 4 primeiros do ranking na forma crescente
ZREVRANGE pontuacoes 0 3 WITHSCORES                         <- Decrescente, inverte o de cima
ZREVRANGE pontuacoes 0 -1 WITHSCORES                        <- (ultimo elemento -1), (penúltimo elemento -1)
ZREVRANGE pontuacoes -3 -1 WITHSCORES                       <- Os 3 últimos
ZSCORE pontuacoes gustavo                                   <- Lista os pontos do gustavo
ZRANK pontuacoes gustavo                                    <- Descobre posição no ranking crescente
ZREVRANK pontuacoes gustavo                                 <- Descobre posição no ranking decrescente
ZINCRBY pontuacoes 5000 gustavo                             <- Add mais pontos para o Gustavo
DEL pontuacoes                                              <- Deletar
```
