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

IGameObject é uma interface que permite, individualmente, atribuir a todos os objetos do uma jogo os mesmos tipos de características representadas em baixo:
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
Ao contrário da interface anterior, esta utiliza a interface IGameObject para criar uma lista de objetos e fazer a gestão de cada um deles.
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
BaseGameObject é uma classe abstrata que implementa a interface IGameObject e fornece uma implementação comum para todos os objetos do jogo.
Ela define as propriedades ou características essenciais de cada objeto como posição, velocidade, direção, rotação, ponto de origem, visibilidade e o tamanho do sprite, assim como permite a manipulação do sprite associado ao objeto. 

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
```
Inclui também métodos para atualizar, desenhar, carregar, descarregar e lidar com interações do jogador.
```csharp
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
A classe abstrata GameManager implementa a interface IGameManager e gere a lista de objetos do jogo. Ela possui uma lista de objetos do tipo IGameObject e utiliza métodos para adicionar, atualizar, desenhar, carregar e descarregar esses objetos.
Ela permite organizar o comportamento de todos os objetos do jogo ao mesmo tempo, realizando as atualizações e renderizações de acordo com as condições definidas, ela assegura que todos os objetos visíveis sejam desenhados na tela e que sejam carregados e descarregados corretamente.

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

## Maths

A classe “Maths.cs” vai adicionar um novo comando à livraria “TheTirelessLilAnt.Components” chamado “Maths.ManhattanDistance()” que permite calcular a distância Manhattan entre dois pontos dados.

A distância Manhattan é a distância entre dois pontos num plano que é calculada ao somar a diferença x e a diferenca y dos dois pontos.

Esta vai permitir ao jogo saber a que distância da formiga o rato do Jogador está, e tomar as decisões certas em função do valor retornado.

```csharp
using Microsoft.Xna.Framework;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace TheTirelessLilAnt.Components
{
    static class Maths
    {
        /// <summary>
        /// Calculates the Manhattan distance between two points in plane.
        /// </summary>
        /// <param name="pointA">The given point A in plane</param>
        /// <param name="pointB">The given point B </param>
        /// <returns>Manhattan distance between point A and point B.</returns>
        public static float ManhattanDistance(Vector2 pointA, Vector2 pointB)
        {
            return Math.Abs(pointB.X - pointA.X) + Math.Abs(pointB.Y - pointA.Y);
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
<details>
    <summary>Clique aqui para ver a função completa</summary>
    
```csharp 
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Content;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Input;
using System;
using System.Collections.Generic;
using TheTirelessLilAnt.Components;

namespace TheTirelessLilAnt.GameEntitites
{
  /// <summary>
  /// Describing the behavior for the Lil Ant entity.
  /// </summary>
  class LilAnt : BaseGameObject
  {
    #region Constants
    /// <summary>
    /// Seconds to wait in the Wait state
    /// </summary>
    private const float SecondsToWait = 1.2f;

    /// <summary>
    /// The distance when the ant starts to run away.
    /// </summary>
    private const int DangerDistance = 120;

    /// <summary>
    /// The distance in which the Ant will keep running away from the cursor.
    /// </summary>
    private const int AlertZone = 130;
    #endregion

    #region Private Fields
    private Vector2 _mousePosition;
    private Vector2 _acceleration;

    private Dictionary<string, string> _statesToFunnyString;
    private Leaf _leaf;
    private Home _home;
    private Action<GameTime> _currentStateAction;
    private float secondsWaited = 0f;

    private SpriteFont spriteFont;

    private int _leafGathered = 0;
    #endregion
    #region Public Methods
    public LilAnt(Texture2D texture, Vector2 position) : base(texture)
    {
      Position = position;
      _mousePosition = new Vector2();
      _acceleration = new Vector2(0.65f, 0.65f);
      _currentStateAction = FindLeafState;
      MapStateToFunnyString();
    }
    /// <summary>
    /// Retrieves the home object.
    /// </summary>
    /// <returns></returns>
    public LilAnt AddHome(Home home)
    {
      _home = home;
      return this;
    }

    /// <summary>
    /// Adds the leaf object and returns a new ant object.
    /// </summary>
    /// <param name="leaf"></param>
    /// <returns></returns>
    public LilAnt AddLeaf(Leaf leaf)
    {
      _leaf = leaf;
      return this;
    }

    /// <summary>
    /// Adds the leaf object and returns a new ant object.
    /// </summary>
    /// <param name="contentManager">The ContentManager used for resource loading</param>
    public override void LoadContent(ContentManager contentManager)
    {
      spriteFont = contentManager.Load<SpriteFont>("fontDesc");
      base.LoadContent(contentManager);
    }

    /// <summary>
    /// Called every frame when the texture needs to be drawn.
    /// </summary>
    /// <param name="spriteBatch">The SpriteBatch used for drawing texture.</param>
    public override void Draw(SpriteBatch spriteBatch)
    {
      KeepInScreen(spriteBatch);
      var stateString = _statesToFunnyString[_currentStateAction.Method.Name];

      spriteBatch.DrawString(spriteFont, $"The Lil Ant is {stateString}.", Vector2.Zero, Color.White);
      spriteBatch.DrawString(spriteFont, $"Leafs gathered: {_leafGathered}", new Vector2(0, 25), Color.White);
      base.Draw(spriteBatch);
    }

    /// <summary>
    /// Handle the inputs coming from the user. 
    /// </summary>
    public override void HandleInput()
    {
      var mouseState = Mouse.GetState();
      _mousePosition.X = mouseState.X;
      _mousePosition.Y = mouseState.Y;

      base.HandleInput();
    }

    /// <summary>
    /// Includes the logic for ant moving. 
    /// </summary>
    /// <param name="gameTime">Provides a snapshot of timing values.</param>
    public override void Update(GameTime gameTime)
    {
      var deltaTime = (float)gameTime.ElapsedGameTime.TotalSeconds;
      _currentStateAction(gameTime);

      //Linear movement
      Position += Velocity * Direction * deltaTime;
    }

    /// <summary>
    /// Accelerates the movement of the Ant.
    /// </summary>
    /// <param name="accX">Acceleration on X-axis</param>
    /// <param name="accY">Acceleration on Y-axis</param>
    public void Accelerate(float accX, float accY)
    {
      _acceleration = new Vector2(accX, accY);
      Velocity += _acceleration;
    }

    #endregion Public Methods

    #region States Actions
    /// <summary>
    /// In the FindLeafState, the Ant will go find the leaf and run away from the mouse cursos if nearby.
    /// Transition to:
    ///   - RunAwayState: on mouse cursor nearby.
    ///   - GoHomeState: on home nearby.
    /// </summary>
    private void FindLeafState(GameTime gameTime)
    {
      LookAt(_leaf.Position);
      Velocity = new Vector2(1f, 1f);
      if (_leaf.Visible == false)
      {
        _leaf.RandomizePosition();
        _leaf.Visible = true;
      }

      if (EnemyIsInDangerZone())
      {
        _currentStateAction = RunAwayState;
      }

      if (Maths.ManhattanDistance(Position, _leaf.Position) <= 30)
      {
        _currentStateAction = GoHomeState;
      }
    }

    /// <summary>
    /// In the GoHomeState, the Ant will go home having the leaf.
    /// Ant will ignore the cursor in this state.
    /// Transition to:
    ///   - WaitState: on timer elapsed.
    /// </summary>
    private void GoHomeState(GameTime gameTime)
    {
      LookAt(_home.Position);
      Velocity = new Vector2(0.7f, 0.7f);
      GrabLeaf();

      if (Maths.ManhattanDistance(Position, _home.Position) <= 40)
      {
        _leaf.Visible = false;
        _leafGathered++;
        _currentStateAction = WaitState;
      }
    }

    /// <summary>
    /// In WaitState, the Ant will have the movement slowed until the timer elapsed.
    /// Transition to:
    ///   - RunAwayState: on mouse cursor inside the danger zone.
    ///   - FindLeafState: on timer elapsed.
    /// </summary>
    private void WaitState(GameTime gameTime)
    {
      var deltaTime = (float)gameTime.ElapsedGameTime.TotalSeconds;
      secondsWaited += deltaTime;
      ApplyFriction();

      if (EnemyIsInDangerZone())
      {
        secondsWaited = 0;
        _currentStateAction = RunAwayState;
      }

      if (secondsWaited >= SecondsToWait)
      {
        secondsWaited = 0f;
        _currentStateAction = FindLeafState;
      }
    }

    /// <summary>
    /// In RunAwayState, the Ant will stop finding the leaf and running away from the mouse cursor.
    /// Transition to:
    ///   - WaitState: on mouse cursor outside of the danger zone.
    /// </summary>
    private void RunAwayState(GameTime gameTime)
    {
      LookAwayFrom(_mousePosition);
      Accelerate(0.65f, 0.65f);

      if (!EnemyIsInAlertZone())
      {
        _currentStateAction = WaitState;
      }
    }


    #endregion

    #region Private Methods
    /// <summary>
    /// Sets the point at which the Ant will be looking at.
    /// </summary>
    private void LookAt(Vector2 point)
    {
      Direction = point - Position;
      Rotation = (float)(RotateAwayFrom(point) - Math.PI);
    }

    /// <summary>
    /// Sets the point at which the Ant will be looking away from.
    /// </summary>
    private void LookAwayFrom(Vector2 point)
    {
      Direction = Position - point;
      Rotation = RotateAwayFrom(point);
    }

    /// <summary>
    /// Slows the Ant movement everytime it is called. 
    /// </summary>
    private void ApplyFriction()
    {
      Velocity *= 0.97f;
    }

    /// <summary>
    /// Checks if the mouse cursor is the danger zone.
    /// </summary>
    private bool EnemyIsInDangerZone()
    {
      if (Maths.ManhattanDistance(Position, _mousePosition) <= DangerDistance)
      {
        return true;
      }
      return false;
    }

    /// <summary>
    /// Checks if the mouse cursor is the alert zone.
    /// </summary>
    private bool EnemyIsInAlertZone()
    {
      if (Maths.ManhattanDistance(Position, _mousePosition) <= AlertZone)
      {
        return true;
      }
      return false;
    }

    /// <summary>
    /// Rotates the Ant facing away from a certain point.
    /// </summary>
    /// <param name="enemy">The given point.</param>
    /// <returns>The new rotation of the texture. </returns>
    private float RotateAwayFrom(Vector2 enemy)
    {
      return (float)Math.Atan2(enemy.Y - Position.Y, enemy.X - Position.X) + (float)Math.PI;
    }

    /// <summary>
    /// Keeps the Ant in the screen.
    /// The Ant will be intentionally allowed to be half hidden in the margin. 
    /// </summary>
    /// <param name="spriteBatch"></param>
    private void KeepInScreen(SpriteBatch spriteBatch)
    {
      var maxWidth = spriteBatch.GraphicsDevice.Viewport.Width;
      var maxHeight = spriteBatch.GraphicsDevice.Viewport.Height;

      if (Position.X < 0) Position = new Vector2(0, Position.Y);
      if (Position.X > maxWidth) Position = new Vector2(maxWidth, Position.Y);
      if (Position.Y < 0) Position = new Vector2(Position.X, 0);
      if (Position.Y > maxHeight) Position = new Vector2(Position.X, maxHeight);
    }

    /// <summary>
    /// Will attach the leaf to the Ant.
    /// </summary>
    private void GrabLeaf()
    {
      _leaf.Rotation = Rotation;

      var newX = Position.X + Width / 2 + _leaf.Width / 2 - 50;
      var newY = Position.Y - Height / 2 - _leaf.Height / 2 + 65;
      _leaf.Position = new Vector2(newX, newY);
    }

    /// <summary>
    /// Maps the boring states to some funny representation to be displayed to the user.
    /// </summary>
    private void MapStateToFunnyString()
    {
      _statesToFunnyString = new Dictionary<string, string>
           {
               {"GoHomeState", "going home"},
               {"WaitState","waiting"},
               {"RunAwayState","running awaay. You dont catch her" },
               {"FindLeafState", "gathering leafs" },
           };
    }
    #endregion Private Methods
  }
}
```
</details>
- Modo procurar a folha - faz com que a formiga entre em modo de espera mas ao invés de olhar para o rato do jogador ela irá olhar para a folha, caso o rato do jogador se encontre perto dela ela irá entrar no modo de fugir;

```csharp
   private void FindLeafState(GameTime gameTime)
    {
      LookAt(_leaf.Position);
      Velocity = new Vector2(1f, 1f);
      if (_leaf.Visible == false)
      {
        _leaf.RandomizePosition();
        _leaf.Visible = true;
      }

      if (EnemyIsInDangerZone())
      {
        _currentStateAction = RunAwayState;
      }

      if (Maths.ManhattanDistance(Position, _leaf.Position) <= 30)
      {
        _currentStateAction = GoHomeState;
      }
    }
 ```
  
- Modo de ir para casa - Após encontrar a folha e alcançá-la a formiga volta para casa armazenar a folha assim ganhando um ponto, se esta vir o jogador está perto,  ela ganha velocidade;
```csharp
  private void GoHomeState(GameTime gameTime)
    {
      LookAt(_home.Position);
      Velocity = new Vector2(0.7f, 0.7f);
      GrabLeaf();

      if (Maths.ManhattanDistance(Position, _home.Position) <= 40)
      {
        _leaf.Visible = false;
        _leafGathered++;
        _currentStateAction = WaitState;
      }
    }
```
- Modo de espera - A formiga fica parada à espera a observar o rato do jogador se este estiver perto ela entra no modo de fugir, caso contrário depois de 1.2 segundos ela entra no modo procurar a folha;

```csharp
 private void WaitState(GameTime gameTime)
    {
      var deltaTime = (float)gameTime.ElapsedGameTime.TotalSeconds;
      secondsWaited += deltaTime;
      ApplyFriction();

      if (EnemyIsInDangerZone())
      {
        secondsWaited = 0;
        _currentStateAction = RunAwayState;
      }

      if (secondsWaited >= SecondsToWait)
      {
        secondsWaited = 0f;
        _currentStateAction = FindLeafState;
      }
    }

```
- Modo de fugir - A formiga entra neste modo assim que o jogador chega na zona de perigo, onde ele ganha velocidade e vai estar sempre a afastar-se do jogador até este se encontar longe dela;
```csharp
   private void RunAwayState(GameTime gameTime)
    {
      LookAwayFrom(_mousePosition);
      Accelerate(0.65f, 0.65f);

      if (!EnemyIsInAlertZone())
      {
        _currentStateAction = WaitState;
      }
    }
```

- Zona de perigo - se o rato do Jogador se encontrar perto da formiga e esta o detetar a formiga irá ganhar velocidade e começar fugir, sendo essa área a de perigo;
```csharp
private bool EnemyIsInDangerZone()
    {
      if (Maths.ManhattanDistance(Position, _mousePosition) <= DangerDistance)
      {
        return true;
      }
      return false;
    }
```

- Zona de alerta - se a formiga já estiver a fugir e o jogador aproximar ainda mais o rato ela não irá parar de correr;
```csharp
private bool EnemyIsInAlertZone()
    {
      if (Maths.ManhattanDistance(Position, _mousePosition) <= AlertZone)
      {
        return true;
      }
      return false;
    }
```

Para que todos estes modos sejam possiveis, foram criados vários métodos que permitem as seguintes ações:

- Saber para onde a formiga está a olhar;
```csharp
/// <summary>
    /// Sets the point at which the Ant will be looking at.
    /// </summary>
    private void LookAt(Vector2 point)
    {
      Direction = point - Position;
      Rotation = (float)(RotateAwayFrom(point) - Math.PI);
    }
```
- Fazer com que a formiga olhe na direção oposta;
```csharp
   /// <summary>
    /// Sets the point at which the Ant will be looking away from.
    /// </summary>
    private void LookAwayFrom(Vector2 point)
    {
      Direction = Position - point;
      Rotation = RotateAwayFrom(point);
    }
```
- Abrandar a formiga;
```csharp
    /// <summary>
    /// Slows the Ant movement everytime it is called. 
    /// </summary>
    private void ApplyFriction()
    {
      Velocity *= 0.97f;
    }

```
- Verificar se o rato está na zona de perigo;
```csharp
    /// <summary>
    /// Checks if the mouse cursor is the danger zone.
    /// </summary>
    private bool EnemyIsInDangerZone()
    {
      if (Maths.ManhattanDistance(Position, _mousePosition) <= DangerDistance)
      {
        return true;
      }
      return false;
    }

```
- Verificar se o rato está na zona de alerta;
```csharp
    /// <summary>
    /// Checks if the mouse cursor is the alert zone.
    /// </summary>
    private bool EnemyIsInAlertZone()
    {
      if (Maths.ManhattanDistance(Position, _mousePosition) <= AlertZone)
      {
        return true;
      }
      return false;
    }

```
- Fazer com que a formiga olhe na direção oposta de um ponto específico;
```csharp
    /// <summary>
    /// Rotates the Ant facing away from a certain point.
    /// </summary>
    /// <param name="enemy">The given point.</param>
    /// <returns>The new rotation of the texture. </returns>
    private float RotateAwayFrom(Vector2 enemy)
    {
      return (float)Math.Atan2(enemy.Y - Position.Y, enemy.X - Position.X) + (float)Math.PI;
    }
```
- Garantir que a formiga permaneça na tela;
```csharp
    /// <summary>
    /// Keeps the Ant in the screen.
    /// The Ant will be intentionally allowed to be half hidden in the margin. 
    /// </summary>
    /// <param name="spriteBatch"></param>
    private void KeepInScreen(SpriteBatch spriteBatch)
    {
      var maxWidth = spriteBatch.GraphicsDevice.Viewport.Width;
      var maxHeight = spriteBatch.GraphicsDevice.Viewport.Height;

      if (Position.X < 0) Position = new Vector2(0, Position.Y);
      if (Position.X > maxWidth) Position = new Vector2(maxWidth, Position.Y);
      if (Position.Y < 0) Position = new Vector2(Position.X, 0);
      if (Position.Y > maxHeight) Position = new Vector2(Position.X, maxHeight);
    }
```
- Agarrar a folha;
```csharp
    /// <summary>
    /// Will attach the leaf to the Ant.
    /// </summary>
    private void GrabLeaf()
    {
      _leaf.Rotation = Rotation;

      var newX = Position.X + Width / 2 + _leaf.Width / 2 - 50;
      var newY = Position.Y - Height / 2 - _leaf.Height / 2 + 65;
      _leaf.Position = new Vector2(newX, newY);
    }
```
- Escrever os estados da formiga;
```csharp
    /// <summary>
    /// Maps the boring states to some funny representation to be displayed to the user.
    /// </summary>
    private void MapStateToFunnyString()
    {
      _statesToFunnyString = new Dictionary<string, string>
           {
               {"GoHomeState", "going home"},
               {"WaitState","waiting"},
               {"RunAwayState","running awaay. You dont catch her" },
               {"FindLeafState", "gathering leafs" },
           };
    }
```
# Main

```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Input;
using TheTirelessLilAnt.Components;
using TheTirelessLilAnt.GameEntitites;

namespace ExampleGame
{
    /// <summary>
    /// This is the main type for your game.
    /// </summary>
    public class LilAntGameMain : Game
    {
       private GraphicsDeviceManager _graphics;
       private SpriteBatch _spriteBatch;
       private IGameManager _gameManager;

       private Rectangle _mainFrame;
       private Texture2D _backgroundGrassTexture; 

       private  Home _antHome;
       private  Leaf _leaf;
       private  LilAnt _lilAnt;

        public LilAntGameMain()
        {          
            _graphics = new GraphicsDeviceManager(this);
            Content.RootDirectory = "Content";
            Window.AllowUserResizing = false;

            _gameManager = new GameManager();
        }

        /// <summary>
        /// Allows the game to perform any initialization it needs to before starting to run.
        /// This is where it can query for any required services and load any non-graphic
        /// related content.  Calling base.Initialize will enumerate through any components
        /// and initialize them as well.
        /// </summary>
        protected override void Initialize()
        {
            base.Initialize();
            this.IsMouseVisible = true;
            Window.Title = "Lil Ant";
            _graphics.PreferredBackBufferHeight = 720;
            _graphics.PreferredBackBufferWidth = 1024;
            _graphics.ApplyChanges();
            _mainFrame =  new Rectangle(0, 0, GraphicsDevice.Viewport.Width, GraphicsDevice.Viewport.Height);
        }

        /// <summary>
        /// LoadContent will be called once per game and is the place to load
        /// all of your content.
        /// </summary>
        protected override void LoadContent()
        { 
            var anteater = Content.Load<Texture2D>("anteater");
            var leaftexture = Content.Load<Texture2D>("leaf");
            var homeTexture = Content.Load<Texture2D>("home");
            var antTexture = Content.Load<Texture2D>("ant80");
            _backgroundGrassTexture = Content.Load<Texture2D>("ground");

            _leaf = new Leaf(leaftexture, 1024, 720);
            _antHome = new Home(homeTexture, 1024, 720);
            _lilAnt = new LilAnt(antTexture, new Vector2(100, 100))
                             .AddHome(_antHome)
                             .AddLeaf(_leaf);

            _gameManager.AddObject(_lilAnt);
            _gameManager.AddObject(_leaf);
            _gameManager.AddObject(_antHome);

            Mouse.SetCursor(MouseCursor.FromTexture2D(anteater, 40, 40));

            // Create a new SpriteBatch, which can be used to draw textures.
            _spriteBatch = new SpriteBatch(GraphicsDevice);  
            _gameManager.LoadObjects(Content);
        }

        /// <summary>
        /// UnloadContent will be called once per game and is the place to unload
        /// game-specific content.
        /// </summary>
        protected override void UnloadContent()
        {
            _gameManager.UnloadObjects();
        }

        /// <summary>
        /// Allows the game to run logic such as updating the world,
        /// checking for collisions, gathering input, and playing audio.
        /// </summary>
        /// <param name="gameTime">Provides a snapshot of timing values.</param>
        protected override void Update(GameTime gameTime)
        {
            _gameManager.UpdateObjects(gameTime);

            if (GamePad.GetState(PlayerIndex.One).Buttons.Back == ButtonState.Pressed || Keyboard.GetState().IsKeyDown(Keys.Escape))
                Exit();

            base.Update(gameTime);
        }

        /// <summary>
        /// This is called when the game should draw itself.
        /// </summary>
        /// <param name="gameTime">Provides a snapshot of timing values.</param>
        
        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);
            _spriteBatch.Begin();           
            _spriteBatch.Draw(_backgroundGrassTexture, _mainFrame, Color.White);
            _gameManager.DrawObjects(_spriteBatch);
            _spriteBatch.End();

            base.Draw(gameTime);
        }
    }
}
```
A classe “LilAntGameMain.cs” é a classe que vai inicializar, construir e desenhar o jogo. Para isso, começa por fazer o construtor do jogo ao criar o gestor gráfico, definir o caminho para o ficheiro com o conteúdo do jogo, e criar o gestor do jogo que vai gerir todos os objetos do jogo (folha, formiga, etc…).
```csharp
public LilAntGameMain()
{          
    _graphics = new GraphicsDeviceManager(this);
    Content.RootDirectory = "Content";
    Window.AllowUserResizing = false;

    _gameManager = new GameManager();
}
```
Adicionalmente, vai impedir o utilizador de redimensionar a janela do jogo ```Window.AllowUserResizing = false;```

De seguida, vai fazer algumas inicializações que são precisas para assegurar o bom funcionamento do programa e uma boa experiência para o jogador.
```csharp
protected override void Initialize()
{
    base.Initialize();
    this.IsMouseVisible = true;
    Window.Title = "Lil Ant";
    _graphics.PreferredBackBufferHeight = 720;
    _graphics.PreferredBackBufferWidth = 1024;
    _graphics.ApplyChanges();
    _mainFrame =  new Rectangle(0, 0, GraphicsDevice.Viewport.Width, GraphicsDevice.Viewport.Height);
}
```

Depois, vai carregar as texturas do jogo, criar os vários elementos do jogo (folha, formiga e casa), e aplicar a textura de um papa-formigas ao cursor do rato.
```csharp
protected override void LoadContent()
{ 
    var anteater = Content.Load<Texture2D>("anteater");
    var leaftexture = Content.Load<Texture2D>("leaf");
    var homeTexture = Content.Load<Texture2D>("home");
    var antTexture = Content.Load<Texture2D>("ant80");
    _backgroundGrassTexture = Content.Load<Texture2D>("ground");

    _leaf = new Leaf(leaftexture, 1024, 720);
    _antHome = new Home(homeTexture, 1024, 720);
    _lilAnt = new LilAnt(antTexture, new Vector2(100, 100))
                     .AddHome(_antHome)
                     .AddLeaf(_leaf);

    _gameManager.AddObject(_lilAnt);
    _gameManager.AddObject(_leaf);
    _gameManager.AddObject(_antHome);

    Mouse.SetCursor(MouseCursor.FromTexture2D(anteater, 40, 40));

    // Create a new SpriteBatch, which can be used to draw textures.
    _spriteBatch = new SpriteBatch(GraphicsDevice);  
    _gameManager.LoadObjects(Content);
}
```

Para terminar, vai atualizar os objetos do jogo anteriormente carregados, e verificar se o jogador deseja sair do jogo na funcao “Update()”,  e desenhar as texturas e GameObjects anteriormente carregados no ecrã.
```csharp
protected override void Update(GameTime gameTime)
{
    _gameManager.UpdateObjects(gameTime);

    if (GamePad.GetState(PlayerIndex.One).Buttons.Back == ButtonState.Pressed || Keyboard.GetState().IsKeyDown(Keys.Escape))
        Exit();

    base.Update(gameTime);
}
```
```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);
    _spriteBatch.Begin();           
    _spriteBatch.Draw(_backgroundGrassTexture, _mainFrame, Color.White);
    _gameManager.DrawObjects(_spriteBatch);
    _spriteBatch.End();

    base.Draw(gameTime);
}
```

