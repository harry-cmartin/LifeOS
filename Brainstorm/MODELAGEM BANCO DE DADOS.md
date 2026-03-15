
UserService

![[Pasted image 20260315191455.png]]


FinanceService

![[Pasted image 20260315191514.png]]

TasksService
![[Pasted image 20260315191534.png]]

HealthService

Módulo Health (Treino/Academia)

- **Coleção 1: `Exercises` (O Catálogo Fixo)**
    
    Serve para popular a tela de busca e filtros no React.
    
    JSON
    
    ```
    {
      "_id": "uuid-exercicio",
      "name": "Supino Reto",
      "muscle_group": "Peito",
      "type": "STRENGTH" // Pode ser STRENGTH, CARDIO, MOBILITY
    }
    ```
    
- **Coleção 2: `WorkoutLogs` (O dia a dia do usuário)**
    
     Cada registro é um dia de treino do usuário.
    
    JSON
    
    ```
    {
      "_id": "uuid-do-log",
      "user_id": "uuid-do-usuario-logado",
      "date": "2026-03-15T10:00:00Z",
      "focus": "Peito e Tríceps",
      "exercises_done": [
        {
          "exercise_id": "uuid-supino-reto",
          "name": "Supino Reto", // Salva o nome para evitar joins complexos
          "sets": [
            {"reps": 12, "weight_kg": 60},
            {"reps": 10, "weight_kg": 65},
            {"reps": 8,  "weight_kg": 70}
          ]
        },
        {
          "exercise_id": "uuid-esteira",
          "name": "Esteira",
          "duration_minutes": 20,
          "distance_km": 3.5,
          "heart_rate_avg": 140 
          // Veja como o CARDIO tem campos diferentes da MUSCULAÇÃO. 
          // No SQL isso daria dor de cabeça. No NoSQL é natural!
        }
      ]
    }
    ```
    

Módulo Economic (Macro e Inflação Pessoal)

Aqui o NoSQL vai funcionar como um banco de Séries Temporais (Time-Series) para armazenar histórico massivo sem engasgar.

- **Coleção 1: `MacroIndicatorsHistory`**
    
    JSON
    
    ```
    {
      "_id": "uuid",
      "indicator_type": "SELIC", // SELIC, IPCA, USD_BRL
      "date": "2026-03-15",
      "value": 10.75
    }
    ```
    
- **Coleção 2: `PersonalBaskets` (A sua inflação pessoal)**
    
    Amarra itens ao usuário e cria um array de histórico de preços para gerar o gráfico que será cruzado com a IA do n8n.
    
    JSON
    
    ```
    {
      "_id": "uuid-cesta",
      "user_id": "uuid-do-usuario",
      "items": [
        {
          "item_name": "Gasolina Comum",
          "category": "Transporte",
          "price_history": [
            {"date": "2026-01-01", "price": 5.50},
            {"date": "2026-02-01", "price": 5.75},
            {"date": "2026-03-01", "price": 5.90}
          ]
        },
        {
           "item_name": "Mensalidade Academia",
           "category": "Saúde",
           "price_history": [ ... ]
        }
      ]
    }
    ```
    