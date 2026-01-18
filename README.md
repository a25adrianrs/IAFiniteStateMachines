# IA FiniteStateMachine

Para este ejercicio añadi un nuevo **STATE** llamado **SEARCH** basicamente lo que hace es que cuando el enemigo pierde de vista al Player ejecuta este nuevo estado y lo busca en la ultima posición
en la que lo vio, y en el caso de que no lo encuentre su estado cambia a **PATROL** y en caso de que si ha **PURSUE**.

 ```csharp
public class Search : State
```
 - Dentro de la clase creo tres variables dos de ellas ya definidas llamadas :
     - ```csharp  float searchDuration = 5.0f; // Duración del tiempo de busqueda del enemigo```
     - ```csharp float searchTimer = 0.0f;    // Temporizador para controlar el tiempo de búsqueda```
     
    Las cuales se usaran para establecer cuanto tiempo durara y controlar el estado de SEARCH.
   
   Y una tercera :
   - ```csharp Vector3 lasKnowPosition; // Ultima posición conocida del jugador```
     La cual es una variable de Vector3 donde almacenaremos la ultima posición del Jugador.

 -  Creé el metodo del constructor de la clase Search:
   ```csharp
  public Search(GameObject _npc, NavMeshAgent _agent, Animator _animator, Transform _player)
            : base(_npc, _agent, _animator, _player)
    {
        name = STATE.SEARCH; // Definimos el nombre del estado como SEARCH
        agent.speed = 2.5f; // Velocidad a la qu se movera el agente durante la busqueda
        agent.isStopped = false; // Nos aseguramos que el agente no este detenido
        lasKnowPosition = _player.position; // Introducimos la ultima posición del jugador para que el agente busque
    }
```
 -  Cuando el Agente entra en el estado SEARCH lo primero se ejecuta el metodo Enter, en el cual:
    ```csharp
     public override void Enter()
    {
        animator.SetTrigger("isWalking");   // Activamos la clase de caminar
        agent.SetDestination(lasKnowPosition); // Indicamos al Agente que debe buscar en la última posición conocida del Player
        searchTimer = 0.0f; // Reiniciamos el temporizador de búsqueda
        base.Enter(); // Se activa el metodo enter desde la clase base
    }
    ```
   - En el metodo UPDATE empezamos por inicializar el contador de tiempo que establece la duración del estado, en el cual si
     el Agente visualiza de nuevo al jugador entrara en estado **PURSUE** y en caso de que nó y se tiempo establecido sea superado volvera a su        estado base de **PATROL**.

     ```csharp
     public override void Update(){
        searchTimer += Time.deltaTime;

        // Si el agente vuelve a visualizar al jugador se inicia de nuevo la persecución
        if (CanSeePlayer())
        {
            nextState = new Pursue(npc, agent, animator, player);
            stage = EVENT.EXIT; // El Estado SEARCH finaliza para que PURSUE pueda activarse
            return;
        }

        // Si ya paso el tiempo preestablecido para la busqueda , el estado vuelve a cambiar a PATROL
        if (searchTimer >= searchDuration)
        {
            nextState = new Patrol(npc, agent, animator, player);
            stage = EVENT.EXIT;
        }}
     ```
     - Por ultimo queda el metodo Exit el cual al igual que en el resto de estados limpia las animaciones.
       ```csharp
       public override void Update(){
        searchTimer += Time.deltaTime;

        // Si el agente vuelve a visualizar al jugador se inicia de nuevo la persecución
        if (CanSeePlayer())
        {
            nextState = new Pursue(npc, agent, animator, player);
            stage = EVENT.EXIT; // El Estado SEARCH finaliza para que PURSUE pueda activarse
            return;
        }

        // Si ya paso el tiempo preestablecido para la busqueda , el estado vuelve a cambiar a PATROL
        if (searchTimer >= searchDuration)
        {
            nextState = new Patrol(npc, agent, animator, player);
            stage = EVENT.EXIT;
        }}
       ```
