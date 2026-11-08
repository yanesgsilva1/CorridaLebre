# CorridaLebre

Jogo multiplayer (2 a 10 jogadores em rede) feito com [libGDX](https://libgdx.com/).

> **O labirinto gira ao contrário de quem corre.**

> Guia completo para jogadores (regras, conexão e como o multiplayer funciona): [COMO-JOGAR.md](COMO-JOGAR.md)

## A ideia

O labirinto é circular: uma horta de cenouras no centro, cercada por 6 anéis concêntricos que giram.
O que tira o jogo do "labirinto que se mexe sozinho" é que **o movimento dos anéis vem das próprias lebres**:

- **Efeito roda de hamster:** correr ao longo de um anel empurra o anel para o lado oposto.
  Uma lebre sozinha usa isso para alinhar portas; várias no mesmo anel viram um cabo de guerra
  e podem desalinhar as portas das outras de propósito.
- **Paredes duplas:** entre dois anéis há duas camadas de parede, cada uma girando com o seu anel.
  Só dá para passar quando as aberturas das duas se alinham.
- **Lua cheia:** a cada ~22 s um anel pisca e, 4 s depois, muda de forma e inverte o giro.
- **Cenoura pesa:** pegar uma cenoura na horta deixa a lebre mais lenta e com o dobro de peso nos anéis.
  Ela só conta quando é levada de volta ao prado (a toca, na borda externa).
- **Bote:** uma investida curta (ESPAÇO). Se acertar quem está carregando cenoura, rouba a cenoura.

Vence quem guardar **2 cenouras** primeiro, ou quem tiver mais quando os **2 minutos** acabarem.

## Controles

| Tecla | Ação |
|---|---|
| WASD / setas | correr |
| ESPAÇO | bote (rouba cenoura) |
| ENTER | (host) começar / recomeçar a corrida |
| M / N | liga/desliga música / efeitos sonoros (a escolha fica salva) |
| ESC | sair para o menu |

## Sons e música

Todos os sons e as duas músicas (`noite`, no menu e no treino, e `corrida`, durante a partida) são
**sintetizados do zero** pelo script `tools/gerar-sons.js`. Nenhum sample externo é usado.
Os arquivos já gerados estão em `assets/sons` e `assets/musica`. Para mudar algo, edite o script e rode:

```bash
node tools/gerar-sons.js
```

Os sons das outras lebres ficam mais baixos com a distância e saem do lado da tela em que elas estão.
Há sons para: pegar, guardar e roubar cenoura, bote, atordoamento, aviso da lua cheia, anel mudando,
largada, vitória/fim, jogador entrando e contagem dos 10 segundos finais.

## Como jogar em rede

1. Um jogador abre o jogo e clica em **Criar sala**. O IP dele aparece na tela (porta TCP `7777`).
2. Os outros digitam esse IP no campo **IP do host** e clicam em **Entrar** (até 10 lebres no total).
3. Antes de começar, todos podem treinar livremente. O host aperta **ENTER** para valer.

Em rede local (mesmo Wi-Fi) funciona direto. Talvez o Firewall do Windows pergunte se deve liberar o Java:
aceite no PC do host. Para jogar pela internet, o host precisa redirecionar a porta 7777 no roteador
ou usar uma VPN de jogos (Radmin VPN, ZeroTier, Tailscale…).

## Rodar e gerar o executável

```bash
gradlew.bat lwjgl3:run     # roda o jogo
gradlew.bat lwjgl3:jar     # gera lwjgl3/build/libs/CorridaLebre-1.0.0.jar (java -jar ...)
```

## Organização do código

- `core/.../sim/Maze.java`: geometria do labirinto polar e geração das câmaras e portas.
- `core/.../sim/World.java`: simulação autoritativa (física dos anéis, colisão, cenouras, botes, lua cheia).
- `core/.../net/GameServer.java`: servidor TCP que roda dentro do jogo do host, a 30 ticks/s.
- `core/.../net/GameClient.java`: conexão do jogador. Interpola os dois últimos estados recebidos.
- `core/.../MenuScreen.java`, `PlayScreen.java`, `Art.java`: telas e desenho procedural (sem imagens).
