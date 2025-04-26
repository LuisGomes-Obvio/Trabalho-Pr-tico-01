# Trabalho-Pr-tico-01
Neste jogo, o jogador assume o papel de um papa-formigas que persegue uma formiga enquanto ela tenta recolher folhas para a sua casa, ao mesmo tempo que tenta evitar o inimigo. O jogador controla o papa-formigas com o cursor do rato e tenta apanhar a formiga, que possui um comportamento automático e é controlada pelo programa, fazendo com que por exemplo ela se mova mais rapidamente caso o papa-formigas se aproxime dela.

# Pré-Requesitos
- MonoGame SDK
- Visual Studio 2022 (ou superior)
- .NET Framework/Core

# Créditos 
O jogo foi criado por Ionut Tivadar.
Repositório do jogo: https://github.com/itivadar/LilAnt-Game

# Membros do Grupo:
- Luís Gomes - 31473
- Samuel Fernandes - 31470
- David Barbosa - 31461

# Interfaces dos Componentes
## IGameObjects
```csharp
﻿using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Content;
using Microsoft.Xna.Framework.Graphics;

namespace TheTirelessLilAnt
{
    public interface IGameObject 
    {         
        Vector2 Position { get; set; }
        Vector2 Velocity { get; set; }
        Vector2 Direction { get; set; }

        float Rotation { get;}
        Vector2 Origin { get; set; }

        int Height { get; }
        int Width { get; }

        bool Visible { get; set; }

        void HandleInput();
        void Update(GameTime time);
        void Draw(SpriteBatch spriteBatch);
        void LoadContent(ContentManager contentManager);
        void UnloadContent();

    }
}
```

IGameObject é uma interface que permite, individualmente, atribuir a todos os objetos do uma jogo os mesmos tipos de características representados em baixo:
-Posição;    ```csharp   Vector2 Position { get; set; } ```
-Velocidade;    ```Vector2 Velocity { get; set; }```
-Direção;    ```Vector2 Direction { get; set; } ```
-Rotação;    ```float Rotation { get;} ```
-Ponto de Origem;    ```Vector2 Origin { get; set; } ```
-Tamanho do Sprite;   ```int Height { get; } 
                          int Width { get; }```
-Visibilidade;    ```bool Visible { get; set; }```

Além disso possui uma série de métodos ou instruções que permitem carregar conteúdos, atualizar o estado dos objetos, desenhá-los no ecrã e lidar com comandos do jogador.

Isto facilita a organização do código e permite que os objetos do jogo funcionem de uma maneira consistente.
