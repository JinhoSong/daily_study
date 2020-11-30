### redux-actions

---

1. [작업 환경 설정](#작업-환경-설정)
2. [UI 준비](#UI-준비)
3. [리덕스 관련 코드 작성하기](#리덕스-관련-코드-작성하기)

---

1. ### 작업 환경 설정

	* `yarn create react-app react-redux-st`
	* `yarn add redux react-redux`

2. ### UI 준비

	1. **구조**

		* 리액트 프로젝트에서 `redux` 사용시 가장 많이 사용하는 패턴은 **프레젠테이셔널 컴포넌트와 컨테이너 컴포넌트를 분리하는 것입니다.**
			1. **프레젠테이셔널 컴포넌트**
				* 상태관리가 이루어지지 않고 그거 받아온 `props`를 화면에 보여주는 컴포넌트
			2. **컨테이너 컴포넌트**
				* 리덕스와 연동되어 있는 컴포넌트
				* 리덕스로부터 상태를 받아 오기도 하고 리덕스 스토어에 `action dispatch`를 하기도 합니다..
		* **필수 사항은 아니지만 코드의 재사용성과 관심사 분리에 용이하다.**

		![image](https://user-images.githubusercontent.com/52272332/98456558-7083f400-21c2-11eb-8d7c-3e7a23c68769.png)

3. ### 리덕스 관련 코드 작성하기

	1. **구조**

		* 리덕스 관련 코드는 코드들을 각각 다른 파일에 작성하는 방법이 있고
		* 기능별로 묶어서 파일 하나에 작성하는 방법도 있습니다.
		* 여기선 묶어서 사용하는 `Ducks`패턴을 사용합니다.

		<img src="https://user-images.githubusercontent.com/52272332/98459293-e432fa80-21dc-11eb-9760-f5acd32c0e21.png" alt="image" style="zoom:50%;" />

	2. **`modules`**

		* `modules/counter.js`

			```js
			// 액션 타입 정의
			const INCREASE = 'counter/INCREASE';
			const DECREASE = 'counter/DECREASE';
			const RESET = 'counter/RESET';
			
			//액션 함수 설정
			export const increase = () => ({type: INCREASE});
			export const decrease = () => ({type: DECREASE});
			export const reset = () => ({type: RESET});
			
			// 초기값 설정
			const initialState = {
			    number: 0
			};
			
			
			//리듀서 함수 설정
			function counter(state = initialState, action) {
			    switch (action.type) {
			        case INCREASE :
			            return {
			                number: state.number + 1
			            };
			        case DECREASE :
			            return {
			                number: state.number - 1
			            };
			        case RESET :
			            return {
			               number : state.number =0
			            };
			        default:
			            return state;
			    }
			}
			
			export default counter;
			```

		* `modules/todos.js`

			```js
			const CHANGE_INPUT = 'todos/CHANGE_INPUT';
			const INSERT = 'todos/INSERT';
			const TOGGLE = 'todos/TOGGLE';
			const REMOVE = 'todos/REMOVE';
			
			export const changeInput = input => ({
			    type: CHANGE_INPUT,
			    input
			});
			let id = 3;
			export const insert = text => ({
			    type: INSERT,
			    todo: {
			        id: id++,
			        text,
			        done: false
			    }
			});
			
			export const toggle = id => ({
			    type: TOGGLE,
			    id
			});
			
			export const remove = id => ({
			    type: REMOVE,
			    id
			});
			
			const initialState = {
			    input: '',
			    todos: [{
			        id: 1,
			        text: '1',
			        done: true
			    },
			        {
			            id :2,
			            text : '2',
			            done: false
			        }
			    ]
			};
			
			function todos(state = initialState, action) {
			    switch (action.type) {
			        case CHANGE_INPUT :
			            return {
			                ...state,
			                input: action.input
			            };
			        case INSERT :
			            return {
			                ...state,
			                todos : state.todos.concat(action.todo)
			            };
			        case TOGGLE:
			            return {
			                ...state,
			                todos: state.todos.map(todo =>
			                todo.id === action.id ? { ...todo,done: !todo.done} : todo)
			            };
			        case REMOVE :
			            return {
			                ...state,
			                todos : state.todos.filter(todo => todo.id !== action.id)
			            };
			        default:
			            return state;
			    }
			}
			
			export default todos;
			```

		* **해당 모듈들은 다음과 같은 기능을 한다**

			1. 액션 타입 정의
			2. 액션 생성 함수
			3. 리듀서 함수 작성 

		* `modules/index.js`

			```js
			import {combineReducers} from "redux";
			import counter from './counter';
			import todos from './todos';
			
			const rootReducer = combineReducers({
			    counter,
			    todos,
			});
			
			export default rootReducer;
			```

			1. 나중에 `createStore` 함수를 사용하여 스토어를 만들 때는 **리듀서는 하나만 사용해야합니다.** -> 리덕스의 3가지 규칙 중 **단일 스토어**에 해당합니다.
			2. 그렇기에 2개의 리듀서를 하나로 합쳐주는 작업을 수행합니다.
			3. `combineReducers`라는 유틸 함수를 사용하여 쉽게 처리 가능
			4. 나중에 `import rootReducer from './modules';`해서 사용하면됩니다.

	3. **스토어 생성**

		* `src/index.js`

			```js
			import React from 'react';
			import ReactDOM from 'react-dom';
			import {createStore} from "redux"; // store를 만들고
			import {Provider} from 'react-redux';
			import {composeWithDevTools} from 'redux-devtools-extension';
			import './index.css';
			import App from './App';
			import * as serviceWorker from './serviceWorker';
			import rootReducer from "./modules";
			
			const store = createStore(rootReducer, composeWithDevTools());
			
			ReactDOM.render(
			    <Provider store={store}>
			        <App/>
			    </Provider>,
			    document.getElementById('root')
			);
			
			serviceWorker.unregister();
			```

			* `Provider`로 묶고 `store`를 전달해주면된다.
			* `yarn add redux-devtools-extension`을 추가해준다. 

	4. **`container`**

		* `CounterContainer.js`

			```js
			import React from 'react';
			import Counter from '../components/Counter';
			import {connect} from 'react-redux';
			import {decrease, increase,reset} from "../modules/counter";
			
			const CounterContainer = ({number, increase, decrease,reset}) => {
			    return (
			        <div>
			            <Counter number={number} onDecrease={decrease} onIncrease={increase} reset={reset}/>
			        </div>
			    );
			};
			
			export default connect(
			    state => ({
			        number: state.counter.number,
			    }),
			    {
			        increase,
			        decrease,
			        reset,
			    },
			)(CounterContainer);
			```

			1. 컴포넌트를 리덕스와 연동하려면 `react-redux` 에서 제공하는 `connect`함수를 사용해야 합니다.
			2. `connect(mapStateToPros, mapDispatchToProps)(연동할 컴포넌트)`
			3. `mapStateToProps` : 리덕스 스토어 안의 **상태**를 컴포넌트의 `props`로 넘겨주기 위한 함수
			4. `mapDispatchToProps` : **액션 생성 함수**를 컴포넌트의 `props`로 넘겨주기 위해 사용하는 함수 

		* `TodosContainer.js`

			```js
			import React from 'react';
			import {connect} from 'react-redux';
			import {changeInput,insert,toggle,remove} from "../modules/todos";
			import Todos from '../components/Todos';
			
			const TodosContainer = ({
			    input,
			    todos,
			    changeInput,
			    insert,
			    toggle,
			    remove,
			}) => {
			    return (
			        <div>
			            <Todos
			                input={input}
			                todos={todos}
			                onChangeInput={changeInput}
			                onInsert={insert}
			                onToggle={toggle}
			                onRemove={remove}
			            />
			        </div>
			    );
			};
			
			export default connect(
			    //비구조화 할당을 통해 todos를 분리하여
			    // state.todos.input 대신 todos.input을 사용
			    ({ todos }) => ({
			        input : todos.input,
			        todos: todos.todos,
			    }),
			    {
			        changeInput,
			        insert,
			        toggle,
			        remove,
			    },
			)(TodosContainer);
			```

	5. `components`

		* `Counter.js`

			```js
			import React from 'react';
			
			const Counter = ({ number, onIncrease, onDecrease,reset}) => {
			    return (
			        <div>
			            <h1>{number}</h1>
			            <div>
			                <button onClick={onIncrease}>+1</button>
			                <button onClick={onDecrease}>-1</button>
			                <button onClick={reset}>초기화</button>
			            </div>
			        </div>
			    );
			};
			
			export default Counter;
			```

		* `Todos.js`

			```js
			import React from 'react';
			import {connect} from "react-redux";
			
			const TodoItem = ({todo, onToggle, onRemove}) => {
			    return (
			        <div>
			            <input
			                type="checkbox"
			                onClick={() => onToggle(todo.id)}
			                checked={todo.done}
			                readOnly={true}
			            />
			            <span style={{textDecoration: todo.done ? 'line-through' : 'none'}}>{todo.text}</span>
			
			            <button onClick={() => onRemove(todo.id)}>삭제</button>
			        </div>
			    );
			};
			
			const Todos = ({
			                   input,
			                   todos,
			                   onChangeInput,
			                   onInsert,
			                   onToggle,
			                   onRemove,
			                   number
			               }) => {
			    const onSubmit = e => {
			        e.preventDefault();
			        onInsert(input);
			        onChangeInput(''); // 넣고나서는 초기화
			    };
			    const onChange = e => {
			        onChangeInput(e.target.value);
			    }
			    return (
			        <div>
			            <form onSubmit={onSubmit}>
			                <input value={input} onChange={onChange}/>
			                <button type="submit">등록</button>
			            </form>
			            <div>
			                {todos.map(todo => (
			                    <TodoItem
			                        todo={todo}
			                        key={todo.id}
			                        onToggle={onToggle}
			                        onRemove={onRemove}
			                    />
			                ))}
			            </div>
			            <h2> Todos.js에서 확인하는 number : {number}</h2>
			        </div>
			    );
			};
			
			//export default Todos;
			
			export default connect(
			    state => ({
			        number: state.counter.number,
			    }),
			)(Todos);
			```

4. ### 결과화면

	<img src="https://user-images.githubusercontent.com/52272332/98460223-a6869f80-21e5-11eb-97f5-7133cc5b8cdf.png" alt="image" style="zoom:33%;" />

5. ### 리덕스 더 편하게 사용하기

	1. `redux-action`

		* `yarn add redux-actions` 추가합니다.

	2. `modules`

		1. `counter.js`

			```js
			import {createAction, handleActions} from "redux-actions";
			
			const INCREASE = 'counter/INCREASE';
			const DECREASE = 'counter/DECREASE';
			const RESET = 'counter/RESET';
			
			// 간단하게 객체 생성하기
			export const increase = createAction(INCREASE);
			export const decrease = createAction(DECREASE);
			export const reset = createAction(RESET);
			
			const initialState = {
			    number: 0
			};
			// 간단하게 함수 생성하기
			const counter = handleActions({
			        [INCREASE]: (state, action) => ({number: state.number + 1}),
			        [DECREASE]: (state, action) => ({number: state.number - 1}),
			        [RESET]: (state, action) => ({number: 0})
			    },
			    initialState,
			);
			
			export default counter;
			```

			* `createAction` : 매번 객체를 직접 만들어 줄 필요 없이 간단하게 액션 생성 함수를 선언할 수 있습니다.
			* `handleActions` : 함수의 첫 번째 파라미터에 각 액션에 대한 업데이트 함수를 넣어 주고, 두번째 파라미터에는 초기 상태를 넣어 줍니다. 

		2. `todos.js`

			```js
			import {createAction, handleActions} from "redux-actions";
			
			const CHANGE_INPUT = 'todos/CHANGE_INPUT';
			const INSERT = 'todos/INSERT';
			const TOGGLE = 'todos/TOGGLE';
			const REMOVE = 'todos/REMOVE';
			
			export const changeInput = createAction(CHANGE_INPUT, input => input);
			
			let id = 3;
			export const insert = createAction(INSERT, text => ({
			    id: id++,
			    text,
			    done: false,
			}));
			
			export const toggle = createAction(TOGGLE, id => id);
			
			export const remove = createAction(REMOVE, id => id);
			
			const initialState = {
			    input: '',
			    todos: [{
			        id: 1,
			        text: '1',
			        done: true
			    },
			        {
			            id: 2,
			            text: '2',
			            done: false
			        }
			    ]
			};
			const todos = handleActions(
			    {
			        [CHANGE_INPUT]: (state, {payload: input}) => ({...state, input}),
			        [INSERT]: (state, {payload: todo}) => ({...state, todos: state.todos.concat(todo),}),
			        [TOGGLE]: (state, {payload: id}) => ({
			            ...state,
			            todos: state.todos.map(todo => todo.id === id ? {...todo, done: !todo.done} : todo)
			        }),
			        [REMOVE]: (state, {payload: id}) => ({...state, todos: state.todos.filter(todo => todo.id !== id),}),
			    },
			    initialState,
			);
			
			export default todos;
			```

			* `counter`와의 차이점은 `props`로 전달할 값이 있느냐 없느냐가 가장 큰 차이입니다.
			* `insert`의 경우 `todo`객체를 액션 객체 안에 넣어 주어야 하기 때문에 두 번째 파라미터에 `text`를 넣으면 `todo` 객체가 반환되는 함수를 넣어 주었습니다.
			* 

