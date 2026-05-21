# Rank LoL — asrus.app

Ferramenta para streamers da Twitch exibirem o próprio rank de **League of Legends** no chat, em tempo real, através de comandos customizados.

---

## O que o site faz

Gera comandos prontos para colar no **StreamElements** (ou qualquer bot que aceite `$(customapi ...)`). Quando alguém digita o comando no chat da Twitch, o bot responde com o seu rank atual, LP, vitórias e mais — sempre atualizado.

---

## Funcionalidades

### Busca de jogador
- Por **Nick + Tag + Região**
- Ou por um **UUID** já gerado anteriormente

### UUID persistente
Cada jogador recebe um UUID único que **não muda**, mesmo que você troque de nick ou tag no jogo. O comando criado no StreamElements continua funcionando para sempre, sem precisar atualizar nada.

### Montar e testar comandos
Você escolhe o nome do comando, monta uma frase usando variáveis, e o site mostra:

- **Como ficará na Twitch** (preview da resposta do bot)
- **O comando pronto** `$(customapi ...)` para copiar e colar no StreamElements

### Filas suportadas
- Ranked Solo/Duo
- Ranked Flex

### Sintaxe flexível dos templates
Você pode usar parênteses ou chaves — os dois funcionam:

```
(player) está (rank) com (pontos) pontos
{player} está {rank} com {pontos} pontos
```

### Variáveis disponíveis

**Geral:** `(player)` · `(tag)` · `(region)`

**Ranked:** `(rank)` · `(tier)` · `(divisao)` · `(pontos)` · `(lp)`

**Estatísticas:** `(vitorias)` · `(derrotas)` · `(winrate)` · `(jogos)`

### Elos em português
A Riot retorna tudo em inglês, mas o site traduz automaticamente:

| Riot | Exibido |
|---|---|
| IRON | Ferro |
| BRONZE | Bronze |
| SILVER | Prata |
| GOLD | Ouro |
| PLATINUM | Platina |
| EMERALD | Esmeralda |
| DIAMOND | Diamante |
| MASTER | Mestre |
| GRANDMASTER | Grão-Mestre |
| CHALLENGER | Desafiante |

E as divisões aparecem em número (`II` vira `2`, `III` vira `3`...). Mestre, Grão-Mestre e Desafiante aparecem sem divisão.

---

## Exemplo

Template no painel:
```
(player) está (rank) com (pontos) pontos · (winrate) de winrate
```

No chat aparece:
```
asrus está Prata 2 com 50 pontos · 58% de winrate
```

---

## Disclaimer

asrus.app isn't endorsed by Riot Games and doesn't reflect the views or opinions of Riot Games or anyone officially involved in producing or managing Riot Games properties. Riot Games and all associated properties are trademarks or registered trademarks of Riot Games, Inc.
