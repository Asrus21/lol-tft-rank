# 🎮 Riot UUID Registry — LoL + TFT

Sistema que gera um **UUID persistente** vinculado ao PUUID da Riot, com suporte a **League of Legends** e **Teamfight Tactics**. Mesmo que o jogador troque de nick/tag, o UUID customizado permanece igual. Custom commands com variáveis dinâmicas de rank, LP, winrate, top4, etc.

## ✨ Features

- 🔑 UUID persistente baseado no `puuid` da Riot
- 🔄 Detecção automática de mudança de nick/tag
- ⚔️ Suporte completo a **LoL** (Solo/Duo + Flex)
- ♟️ Suporte completo a **TFT** (Ranked, Double Up, Hyper Roll/Turbo)
- 🎯 Custom commands com variáveis: `(player)`, `(rank)`, `(pontos)`, `(top4)`, `(winrate)`, etc
- 💾 PostgreSQL para persistência
- 🎨 UI tema Song Request Queue (dark)

## 🚀 Deploy no Railway

### 1. Sobe o código para o GitHub

```bash
git init
git add .
git commit -m "initial commit"
git remote add origin https://github.com/SEU_USUARIO/seu-repo.git
git push -u origin main
```

### 2. No Railway

1. **New Project** → **Deploy from GitHub repo** → seleciona o repo
2. **+ New** → **Database** → **PostgreSQL** (adiciona Postgres ao projeto)
3. Na aba **Variables** do serviço web, adiciona:
   - `RIOT_API_KEY` — sua chave da Riot ([developer.riotgames.com](https://developer.riotgames.com))
   - `NODE_ENV` = `production`
4. Clica em **+ New Variable Reference** → seleciona o Postgres → `DATABASE_URL`
5. **Settings** → **Networking** → **Generate Domain**

⚠️ **Importante:** Para usar TFT em produção, sua API Key precisa ter acesso ao escopo TFT. Chaves de Development já incluem.

## 🧪 Testar localmente

```bash
npm install
cp .env.example .env
# Edita .env com RIOT_API_KEY e DATABASE_URL
npm start
```

Abre http://localhost:3000

## 📡 Endpoints da API

### `POST /api/register`
Registra ou busca um jogador. Busca PUUID + Summoner IDs (LoL e TFT).

```json
{ "gameName": "asrus", "tagLine": "BR1", "region": "br1" }
```

### `POST /api/command`
Cria/atualiza um comando customizado. **Agora aceita `game_mode`!**

```json
{
  "custom_uuid": "uuid-aqui",
  "command_name": "tftrank",
  "template": "(player) está (rank) com (pontos) LP no TFT",
  "game_mode": "tft"
}
```

**Valores válidos para `game_mode`:**
| Valor | Descrição |
|-------|-----------|
| `lol_solo` | LoL Ranked Solo/Duo (default) |
| `lol_flex` | LoL Ranked Flex |
| `tft` | TFT Ranked |
| `tft_double_up` | TFT Double Up |
| `tft_turbo` | TFT Hyper Roll (Turbo) |

### `GET /api/command/:customUuid/:commandName`
Executa o comando — retorna o texto com variáveis substituídas em tempo real, buscando dados ranked do **modo correto**.

### `GET /api/commands/:customUuid`
Lista todos os comandos do jogador (com seus respectivos modos).

### `DELETE /api/command/:customUuid/:commandName`
Deleta um comando.

## 🎯 Variáveis disponíveis nos templates

### Gerais
| Variável | Descrição |
|----------|-----------|
| `(player)` | Nome atual do jogador |
| `(tag)` | Tag atual |
| `(uuid)` | UUID customizado |
| `(region)` | Região |
| `(modo)` | Modo de jogo do comando |

### Ranked (LoL e TFT)
| Variável | Descrição |
|----------|-----------|
| `(rank)` | Tier + Divisão (ex: `PLATINUM II` ou no Turbo: `BLUE`) |
| `(tier)` | Apenas o tier |
| `(divisao)` | Apenas a divisão (vazio no Turbo) |
| `(pontos)` ou `(lp)` | League Points (ou rated rating no Turbo) |

### Estatísticas
| Variável | Descrição |
|----------|-----------|
| `(vitorias)` | LoL: vitórias / TFT: top 4 |
| `(derrotas)` | LoL: derrotas / TFT: fora do top 4 |
| `(winrate)` | Taxa de vitórias % |
| `(top4)` | TFT: número de top 4 (igual a `(vitorias)`) |
| `(top4rate)` | TFT: % de top 4 |
| `(jogos)` | Total de jogos |

## 💡 Exemplos de comandos

**LoL Solo:**
```
Template: (player) está (rank) com (pontos) LP! WR: (winrate)
Resultado: asrus está PLATINUM II com 50 LP! WR: 58%
```

**TFT Ranked:**
```
Template: (player) está (rank) no TFT com (top4)/(jogos) top4 ((top4rate))
Resultado: asrus está DIAMOND IV no TFT com 45/80 top4 (56%)
```

**TFT Hyper Roll:**
```
Template: (player) está no Hyper Roll: tier (tier) com (pontos) pontos
Resultado: asrus está no Hyper Roll: tier BLUE com 1850 pontos
```

## 💡 Como funciona o UUID persistente

1. Usuário entra com `gameName + tagLine + region`
2. Servidor consulta a **Riot Account API** → recebe `puuid` (identificador imutável)
3. Busca **simultaneamente** o `summonerId` do LoL **E** o `tftSummonerId` do TFT (são IDs diferentes!)
4. Verifica no Postgres se já existe registro para esse `puuid`:
   - **Existe** → atualiza nick/tag/summoner_ids atuais e retorna o `custom_uuid` salvo
   - **Não existe** → gera um `uuid` novo e salva vinculado ao `puuid`
5. O `custom_uuid` **NUNCA muda**, mesmo que o jogador altere nick/tag

## 🔌 Endpoints da Riot utilizados

- `GET /riot/account/v1/accounts/by-riot-id/{name}/{tag}` (cluster: americas/europe/asia/sea)
- `GET /lol/summoner/v4/summoners/by-puuid/{puuid}` (LoL)
- `GET /tft/summoner/v1/summoners/by-puuid/{puuid}` (TFT)
- `GET /lol/league/v4/entries/by-summoner/{summonerId}` (LoL ranked)
- `GET /tft/league/v1/entries/by-summoner/{summonerId}` (TFT ranked)

## 🔐 Sobre a Riot API Key

- A chave **Development** expira em 24h — use para testes
- Para produção, solicite uma **Personal API Key** ou **Production Key**
- Confira em [developer.riotgames.com](https://developer.riotgames.com) que sua key tem permissão para os endpoints TFT
