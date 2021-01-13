### springboot와 react를 이용한 채팅 서비스

---

1. [Create React App](#Create-React-App)
2. [React 내부에서 로그인](#React-내부에서-로그인)
3. [Socket 연결](#Socket-연결)

---

1. ### Create React App

	1. **프로젝트 생성**

		* 프로젝트 생성 명령어 : `create-react-app front-end` 
		* `Router` 추가 명령어 : `yarn add react-router-dom`

	2. **폴더 생성**

		<img src="https://user-images.githubusercontent.com/52272332/102685778-52300000-4226-11eb-94cf-3e087f835213.png" alt="image" style="zoom:67%;" />

2. **React 내부에서 로그인**

	1. **설계**

		* React 내부에서 로그인 과정을 수행한다.
		* **텀프로젝트 완성을 위해 간단하게 구현하기 위함.**
		* **나중에 `Spring Security`의 JWT방식으로 바꿀 예정**
		* **React 에서 저장된 User정보를 그대로 서버에 전달하고 서버는 받은 유저 정보를 그대로 다시 소켓으로 보낼 예정**

	2. **구현**

		1. `App.js`

			```jsx
			class App extends React.Component {
			    render() {
			        return (
			            <div>
			                <RouterComponent/>
			            </div>
			        );
			    }
			}
			export default App;
			```

		2. `RouterComponent`

			```jsx
			import React,{useState} from "react";
			import { BrowserRouter , Route, Switch,Link } from "react-router-dom";
			import ChatRoomListContainer from './containers/ChatRoomListContainer';
			import ChatRoomContainer from './containers/ChatRoomContainer';
			import AuthRoute from "./components/login/AuthRoute";
			import LoginContainer from "./containers/LoginContainer";
			
			const AppRouter = () => {
			    const [user, setUser] = useState(null);
			    const [location, setLocation] = useState(null);
			    const authenticated = user != null;
			    // 무한으로 redirect 되는걸 막기 위해 가장 최상단에 /login을 배치한다.
			    return (
			        <div>
			            <BrowserRouter>
			                <div style={style}>
			                    <Switch>
			                        <Route
			                            path="/login"
			                            render={props => (
			                                <LoginContainer />
			                                )}
			                        />
			                        <Route path="/chat/room/enter/" component={ChatRoomContainer}/>
			                        <AuthRoute
			                            authenticated={authenticated}
			                            path="/"
			                            render={props => <ChatRoomListContainer user={user} {...props} />}
			                        />
			                    </Switch>
			                </div>
			            </BrowserRouter>
			        </div>
			    );
			};
			const style = {
			    margin: "10px",
			};
			
			export default AppRouter;
			```

			1. `BrowserRouter`
				* **`HTML5`의 `history API`를 활용하여 업데이트 합니다.**
				* 따라서 `request, response`로 동작하는 **동적인 페이지를 제작할때 적합합니다.**
			2. `Router`
				* **요청받은 pathname에 해당하는 컴포넌트를 랜더링합니다.**
			3. `Switch`
				* path의 충돌을 방지하고 관리합니다.
				* **`<Swtich>` 내부에 `<Route>`를 넣어서 관리합니다.**
				* **제일 위에서부터 아래로 내려오면서 맞는 Route를 찾는 방식으로 충돌을 막아줍니다.**

		3. `LoginModules`

			```jsx
			import {createAction, handleActions} from "redux-actions";
			
			const LOGOUT = 'loginModules/LOGOUT';
			const SETTINGUSER = 'loginModules/SETTINGUSER';
			
			export const logout = createAction(LOGOUT);
			export const settingUser = createAction(SETTINGUSER, user => ({user}))
			
			const initialState = {
			    user : {
			        name: "test",
			        authentication: false,
			        role: "",
			    },
			};
			const loginModules = handleActions({
			        [LOGOUT]: (state) => ({...state, ...state.authentication = false, ...state.username = ""}),
			        [SETTINGUSER]: (state, {payload: user}) => ({
			            ...state=user,
			            // 유저 정보를 한번에 가져온다.
			        }),
			    },
			    initialState,
			);
			
			export default loginModules;
			```

			* **로그인 성공시 store의 user값을 변경하는 Action과 로그아웃용 handleActions**

		4. `LoginContainer.js`

			```jsx
			import React, {useState} from 'react';
			import {connect} from 'react-redux';
			import { settingUser} from '../modules/loginModules';
			import LoginForm from "../components/login/LoginForm";
			import {signIn} from "../components/login/Auth";
			
			const ChatRoomContainer = ({settingUser}) => {
			
			    // 인증용
			    const [user, setUser] = useState(null);
			    const [location, setLocation] = useState(null);
			    const authenticated = user != null;
			
			    const login = ({ name, password }) => setUser(signIn({ name, password,settingUser }));
			    return (
			        <div>
			            <LoginForm authenticated={authenticated} login={login} settingUser={settingUser}  location={location}/>
			        </div>
			    );
			};
			
			export default connect(
			    {
			        settingUser,
			    },
			)(ChatRoomContainer);
			```

		5. `Auth.js`

			```jsx
			const users = [
			    {id : "t", name: "정인환", password: "123", role: "professor", authentication: true, studentId: 33333},
			    {id : "a", name: "송진호", password: "123", role: "student", authentication: true, studentId: 1592018},
			    {id : "b", name: "노윤호", password: "123", role: "student", authentication: true, studentId: 1592008},
			    {id : "c", name: "신성식", password: "123", role: "student", authentication: true, studentId: 1592020},
			]
			
			export function signIn({name, password, settingUser}) {
			    const user = users.find(
			        (user) => user.id === name && user.password === password
			    )
			    if (user === undefined) {
			        // 없으면 Error 처리
			        throw new Error()
			    } else {
			        // 이거하나로 다 저장된다.
			        settingUser(user);
			        return user
			    }
			}
			```

			* `const login = ({ name, password }) => setUser(signIn({ name, password,settingUser }));` 값을 `LoginForm` component로 넘겨준다.

		6. `LoginForm`

			```jsx
			import React, { useState } from "react"
			import { Redirect } from "react-router-dom"
			import Avatar from '@material-ui/core/Avatar';
			import Button from '@material-ui/core/Button';
			import CssBaseline from '@material-ui/core/CssBaseline';
			import TextField from '@material-ui/core/TextField';
			import FormControlLabel from '@material-ui/core/FormControlLabel';
			import Checkbox from '@material-ui/core/Checkbox';
			import Link from '@material-ui/core/Link';
			import Grid from '@material-ui/core/Grid';
			import Box from '@material-ui/core/Box';
			import Typography from '@material-ui/core/Typography';
			import { makeStyles } from '@material-ui/core/styles';
			import Container from '@material-ui/core/Container';
			
			function Copyright() {
			    return (
			        <Typography variant="body2" color="textSecondary" align="center">
			            {'Copyright © '}
			            <Link color="inherit" href="https://material-ui.com/">
			                Song jin ho
			            </Link>{' '}
			            {new Date().getFullYear()}
			            {'.'}
			        </Typography>
			    );
			}
			
			const useStyles = makeStyles((theme) => ({
			    paper: {
			        marginTop: theme.spacing(8),
			        display: 'flex',
			        flexDirection: 'column',
			        alignItems: 'center',
			    },
			    avatar: {
			        margin: theme.spacing(1),
			        backgroundColor: theme.palette.secondary.main,
			    },
			    form: {
			        width: '100%', // Fix IE 11 issue.
			        marginTop: theme.spacing(1),
			    },
			    submit: {
			        margin: theme.spacing(3, 0, 2),
			    },
			}));
			
			function LoginForm({ authenticated, login, location,settingUser}) {
			    const [name, setName] = useState("")
			    const [password, setPassword] = useState("")
			    const classes = useStyles();
			    const handleClick = () => {
			        try {
			            const user = login({ name, password });
			            // catch가 안잡히면 로그인 성공이니까 여기서 세팅한다.
			            console.log(user);
			        } catch (e) {
			            alert("Failed to login")
			            setName("")
			            setPassword("")
			        }
			    }
			
			    const { from } = location || { from: { pathname: "/" } }
			
			    if (authenticated) return <Redirect to={from} />
			
			    return (
			        <Container component="main" maxWidth="xs">
			            <CssBaseline />
			            <div className={classes.paper}>
			                <Avatar className={classes.avatar}>
			                </Avatar>
			                <Typography component="h1" variant="h5">
			                    Sign in
			                </Typography>
			                <form className={classes.form} noValidate>
			                    <TextField
			                        variant="outlined"
			                        margin="normal"
			                        required
			                        fullWidth
			                        value={name}
			                        onChange={({ target: { value } }) => setName(value)}
			                        type="text"
			                        placeholder="name"
			                        autoFocus
			                    />
			                    <TextField
			                        variant="outlined"
			                        margin="normal"
			                        required
			                        fullWidth
			                        value={password}
			                        onChange={({ target: { value } }) => setPassword(value)}
			                        type="password"
			                        placeholder="password"
			                    />
			                    <Button
			                        type="submit"
			                        fullWidth
			                        variant="contained"
			                        color="primary"
			                        className={classes.submit}
			                        onClick={handleClick}
			                    >
			                        Sign In
			                    </Button>
			
			                </form>
			            </div>
			            <Box mt={8}>
			                <Copyright />
			            </Box>
			        </Container>
			    );
			}
			
			export default LoginForm
			```

			* **`material ui`** 추가하기 : `yarn add @material-ui/core`

		7. **실행결과**

			1. 로그인 화면

				![image](https://user-images.githubusercontent.com/52272332/102967749-f4f1c280-4535-11eb-9f25-a498d79b0ccb.png)

			2. **로그인 성공 후 redux 값 확인하기**

				* `yarn add redux react-redux`
				* `yarn add redux-actions` 추가합니다.

				전

				<img src="https://user-images.githubusercontent.com/52272332/102968222-d4763800-4536-11eb-8858-2625cd814260.png" alt="image" style="zoom:50%;" />

				후

				<img src="https://user-images.githubusercontent.com/52272332/102968240-dcce7300-4536-11eb-8968-1ad28e384dff.png" alt="image" style="zoom:50%;" />

	3. ### 채팅방 서버 생성

		1. **Spring boot project 생성**
			* 

