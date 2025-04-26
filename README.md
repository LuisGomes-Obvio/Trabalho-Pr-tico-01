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
- Posição;    ```csharp   Vector2 Position { get; set; } ```
- Velocidade;    ```Vector2 Velocity { get; set; }```
- Direção;    ```Vector2 Direction { get; set; } ```
- Rotação;    ```float Rotation { get;} ```
- Ponto de Origem;    ```Vector2 Origin { get; set; } ```
- Tamanho do Sprite;   ```int Height { get; } 
                          int Width { get; }```
- Visibilidade;    ```bool Visible { get; set; }```

Além disso possui uma série de métodos ou instruções que permitem carregar conteúdos, atualizar o estado dos objetos, desenhá-los no ecrã e lidar com comandos do jogador.
```csharp
        void HandleInput();
        void Update(GameTime time);
        void Draw(SpriteBatch spriteBatch);
        void LoadContent(ContentManager contentManager);
        void UnloadContent();
```

Isto facilita a organização do código e permite que os objetos do jogo funcionem de uma maneira consistente.

## IGameManager

```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Content;
using Microsoft.Xna.Framework.Graphics;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace TheTirelessLilAnt.Components
{
    public interface IGameManager
    {
        IEnumerable<IGameObject> GameObjects { get; }

        void AddObject(IGameObject gameObject);

        void UpdateObjects(GameTime gameTime);
        void DrawObjects(SpriteBatch spritebatch);
        void UnloadObjects();
        void LoadObjects(ContentManager contentManager);
    }
}
```
Distintamente da interface anterior, esta utiliza a interface IGameObject para criar uma lista de objetos e fazer a gestão de cada um deles.
Enquanto a IGameObject é uma interface que define o comportamento individual de cada objeto, a IGameManager permite organizar e controlar vários objetos ao mesmo tempo.

Possui métodos que nos permitem adicionar novos objetos ao jogo, atualizar o seu estado a cada frame, desenhá-los no ecrã, carregar os seus conteúdos e libertar os recursos quando já não forem necessários.
```csharp
  IEnumerable<IGameObject> GameObjects { get; }

        void AddObject(IGameObject gameObject);

        void UpdateObjects(GameTime gameTime);
        void DrawObjects(SpriteBatch spritebatch);
        void UnloadObjects();
        void LoadObjects(ContentManager contentManager);
```
# Classes
## BaseGameObject
BaseGameObject é uma classe abstrata que implementa a interface IGameObject e fornece uma implementação básica e comum para todos os objetos do jogo.
Ela define propriedades essenciais como posição, velocidade, direção, rotação, ponto de origem, visibilidade e o tamanho do sprite, além de permitir a manipulação do sprite associado ao objeto. Inclui também métodos para atualizar, desenhar, carregar, descarregar e lidar com interações do usuário.

```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Content;
using Microsoft.Xna.Framework.Graphics;
using System;

namespace TheTirelessLilAnt.Components
{
  public abstract class BaseGameObject : IGameObject
  {
    /// <summary>
    /// Position of the object on the screen represint by a single point.
    /// </summary>
    public Vector2 Position { get; set; }

    /// <summary>
    /// Velocity with the entity is moving on the screen.
    /// </summary>
    public Vector2 Velocity { get; set; }

    /// <summary>
    /// Direction of object movement.
    /// </summary>
    public Vector2 Direction { get; set; }

    /// <summary>
    /// The texture drawn on the screen.
    /// </summary>
    protected Texture2D Texture { get; set; }

    /// <summary>
    /// The height of the drawn texture.
    /// </summary>
    public int Height => Texture is null ? 0 : Texture.Height;

    /// <summary>
    /// The width of the drawn texture.
    /// </summary>
    public int Width => Texture is null ? 0 : Texture.Width;

    /// <summary>
    /// Determines if the object is visible on the screen.
    /// </summary>
    public bool Visible { get; set; }

    /// <summary>
    /// The pivot used for rotation. 
    /// </summary>
    public Vector2 Origin { get; set; }

    /// <summary>
    /// The angle at which the texture is rotated.
    /// </summary>
    public float Rotation { get; set; }

    /// <summary>
    /// Called evey frame. Incoporated the logic for the entity.
    /// </summary>
    /// <param name="gameTime"></param>
    public abstract void Update(GameTime gameTime);

    /// <summary>
    /// Initialize the proprieties values with the common ones used.
    /// </summary>
    private void InitValues()
    {
      Direction = Vector2.Zero;
      Position = Vector2.Zero;
      Velocity = Vector2.Zero;
      Visible = true;
      Origin = new Vector2(Texture.Width / 2, Texture.Height / 2);
    }

    /// <summary>
    /// Draws the given texture with the specified proprieties.
    /// </summary>
    /// <param name="spriteBatch"></param>
    public virtual void Draw(SpriteBatch spriteBatch)
    {
      spriteBatch.Draw(Texture,
                       new Rectangle((int)Position.X, (int)Position.Y, Texture.Width, Texture.Height),
                       null,
                       Color.White,
                       Rotation,
                       Origin,
                       SpriteEffects.None,
                       0);
    }

    public BaseGameObject(Texture2D texture)
    {
      Texture = texture;
      InitValues();
    }

    /// <summary>
    /// Used for handle user input.
    /// </summary>
    public virtual void HandleInput()
    {
    }

    /// <summary>
    /// Loads the resources used by an entity.
    /// </summary>s
    public virtual void LoadContent(ContentManager contentManager)
    {

    }

    /// <summary>
    /// Unloads the content.
    /// </summary>
    public virtual void UnloadContent()
    {
    }
  }
}
```
## Game Manager
A classe abstrata GameManager implementa a interface IGameManager e gerencia a lista de objetos do jogo. Ela mantém uma lista de objetos do tipo IGameObject e utiliza métodos para adicionar, atualizar, desenhar, carregar e descarregar esses objetos.
A classe permite organizar o comportamento de todos os objetos do jogo ao mesmo tempo, realizando as atualizações e renderizações de acordo com as condições definidas. Além disso, ela assegura que todos os objetos visíveis sejam desenhados na tela e permite que os recursos sejam carregados e descarregados corretamente.

```csharp
﻿using ExampleGame;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Content;
using Microsoft.Xna.Framework.Graphics;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace TheTirelessLilAnt.Components
{
    public class GameManager : IGameManager
    {
        private List<IGameObject> _objects;

        /// <summary>
        /// Lists of all object in the game. 
        /// </summary>
        public IEnumerable<IGameObject> GameObjects => _objects;
        

        public GameManager()
        {
            _objects = new List<IGameObject>();
        }

        /// <summary>
        /// Adds an new entity to the game objects collection. 
        /// </summary>
        /// <param name="gameObject"></param>
        public void AddObject(IGameObject gameObject)
        {
            if (gameObject is null) return;
            _objects.Add(gameObject);
        }
        /// <summary>
        /// Calls the Update() of every visible game object.
        /// </summary>
        public void DrawObjects(SpriteBatch spritebatch)
        {
            var visibleObjects = GameObjects.Where(gameObject => gameObject.Visible);
            foreach (IGameObject gameObject in visibleObjects)
            {
                gameObject.Draw(spritebatch);
            }
        }

        /// <summary>
        /// Calls the Update() of every game objects. 
        /// </summary>
        public void UpdateObjects(GameTime gameTime)
        {
            foreach (IGameObject gameObject in GameObjects)
            {
                gameObject.HandleInput();
                gameObject.Update(gameTime);
            }
        }

        /// <summary>
        /// Unloads the content of the game objects.
        /// </summary>
        public void UnloadObjects()
        {
            foreach (IGameObject gameObject in GameObjects)
            {
                gameObject.UnloadContent();
            }
        }

        /// <summary>
        /// Loads resources of every game object.
        /// </summary>
        public void LoadObjects(ContentManager contentManager)
        {
            foreach (IGameObject gameObject in GameObjects)
            {
                gameObject.LoadContent(contentManager);
            }
        }       
    }
}
```

# Base Game Objects

## Home
A casa (Home) é um Base Game Object e serve como base para a formiga guardar folhas, sendo esta estática e encontra-se no canto inferior direito do mapa.
```csharp
﻿using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;
using TheTirelessLilAnt.Components;

namespace TheTirelessLilAnt.GameEntitites
{
  public class Home : BaseGameObject
  {
    public Home(Texture2D texture, int maxWidth, int maxHeight) : base(texture)
    {
      //The Home object will have static position on the corner of the screen.
      var posX = maxWidth - Width / 2;
      var posY = maxHeight - Height / 2;
      Position = new Vector2(posX, posY);
    }

    //Saddly enough, the home have nothing to update :( 
    public override void Update(GameTime gameTime)
    {
    }
  }
}
```
## Leaf
A folha (Leaf) é um Base Game Object e serve como pontos para a formiga sendo que assim que esta é coletada pela formiga (a formiga leva a para a casa) uma nova irá ser criada no mapa, sendo a sua localização sempre aleatória.

```csharp
﻿using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;
using System;
using TheTirelessLilAnt.Components;

namespace TheTirelessLilAnt.GameEntitites
{
    class Leaf : BaseGameObject
    {
        private Random _rand;
        private int _maxWidth;
        private int _maxHeight;

        public Leaf(Texture2D texture, int maxWidth, int maxHeight): base(texture)
        {
            _maxWidth = maxWidth;
            _maxHeight = maxHeight;
            _rand = new Random();
            RandomizePosition();
        }

        /// <summary>
        /// Called everytime when the Leafs needs to be drawn.
        /// </summary>
        public override void Draw(SpriteBatch spritebatch)
        {
            base.Draw(spritebatch);
        }


        public override void Update(GameTime gameTime)
        {
           
        }

        /// <summary>
        /// Gets a random position for the leaf. 
        /// The position will be randomize above the main diagonal of the screen.
        /// </summary>
        public void RandomizePosition()
        {
            var newY = _rand.Next(Height /2 , _maxHeight - Height / 2);
            var newX = _rand.Next(0, Math.Min(newY, _maxWidth)) +Width / 2;
            Position = new Vector2(newX, newY);
            Rotation = (float)(_rand.Next() % (3 * Math.PI));
        }
    }
}
```
## Ant
A formiga (Ant) é um Base Game Object e o alvo principal do jogador, esta possui várias ações e mecânicas: 
- Modo de espera - A formiga fica parada à espera a observar o rato do jogador se este estiver perto ela entra no modo de fugir, caso contrário depois de 1.2 segundos ela entra no modo procurar a folha;

```csharp


```



