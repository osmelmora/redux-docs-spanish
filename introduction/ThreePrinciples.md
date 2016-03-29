# Tres Principios

Redux se puede describir por medio de tres principios fundamentales:

### Una única fuente de la verdad

**El [estado](../Glosary.md#state) completo de la aplicación se almacena en un objeto dentro de un solo [depósito (store)](../Glossary.md#store).**

Esto simplifica la creación de aplicaciones universales, ya que el estado puede serializarse e hidratarse desde el servidor al cliente sin esfuerzo extra. Un árbol u objecto de estado único también facilita la depuración o la introspección de la aplicación; además permite persistir el estado de la aplicación en desarrollo, acelerando el ciclo de desarrollo. Incluso algunas funcionalidades que tradicionalmente han sido difíciles de implementar - Hacer/Deshacer, por ejemplo - se vuelven tribiales sus implementaciones si todo el estado está almacenado en un sólo árbol (objecto).

```js
console.log(store.getState())

/* Imprime
{
  visibilityFilter: 'SHOW_ALL',
  todos: [
    {
      text: 'Consider using Redux',
      completed: true,
    },
    {
      text: 'Keep all state in a single tree',
      completed: false
    }
  ]
}
*/
```

### El estado es de sólo lectura

**La única forma de mutar el estado es emitiendo una [acción](../Glossary.md#action), un objeto describiendo lo sucedido.**

Esto asegura que ni las vistas ni las llamadas - o callbacks - a través de la red van a escribir directamente en el estado bajo ninguna circunstancia, en cambio, expresan un intento de mutar, porque todas las mutaciones están centralizadas y ocurren una por una en un orden estricto, sin conflictos por los cuales procuparse. Como las acciones son simples objetos, se pueden loggear, serializar, almacenar, y reproducir más tarde para depurar o para hacer pruebas.

```js
store.dispatch({
  type: 'COMPLETE_TODO',
  index: 1
})

store.dispatch({
  type: 'SET_VISIBILITY_FILTER',
  filter: 'SHOW_COMPLETED'
})
```

### Los cambios se hacen con funciones puras

**Para especificar como el árbol de estado es transformado por las acciones, se escriben [reductores](../Glossary.md#reducer) puros.**

Los reductores son simples funciones puras que toman como argumentos el estado previo y la acción, y retornan un nuevo estado. Teniendo en cuenta que se devuelve un objeto con el nuevo estado, en vez de mutar el estado previo. Se puede empezar con un sólo reductor, y mientras la aplicación crece, se pueden dividir en pequeños reductores que controlan partes específicas del árbol de estado. Debido a que los reductores son sólo funciones, se puede controlar el orden en el que se llaman, pasarles datos adicionales, o incluso reutilizarlos para tareas comunes como la paginación.

```js

function visibilityFilter(state = 'SHOW_ALL', action) {
  switch (action.type) {
    case 'SET_VISIBILITY_FILTER':
      return action.filter
    default:
      return state
  }
}

function todos(state = [], action) {
  switch (action.type) {
    case 'ADD_TODO':
      return [
        ...state,
        {
          text: action.text,
          completed: false
        }
      ]
    case 'COMPLETE_TODO':
      return state.map((todo, index) => {
        if (index === action.index) {
          return Object.assign({}, todo, {
            completed: true
          })
        }
        return todo
      })
    default:
      return state
  }
}

import { combineReducers, createStore } from 'redux'
let reducer = combineReducers({ visibilityFilter, todos })
let store = createStore(reducer)
```

Y así ya Ud sabe de qué se trata Redux!
