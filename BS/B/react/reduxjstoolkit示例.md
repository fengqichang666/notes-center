### `@reduxjs/toolkit`示例

```tsx
// store/features/user.ts
import { createSlice } from '@reduxjs/toolkit';
import { getItem, removeItem, setItem } from '@/utils/storage.ts';
import { StorageEnum } from '#/enum.ts';
import { UserInfo, UserToken } from '#/entity.ts';

export const userSlice = createSlice({
    name: 'user',
    initialState: {
        userToken: getItem<UserToken>(StorageEnum.Token) || {},
        userInfo: getItem<UserInfo>(StorageEnum.User) || {}
    },
    reducers: {
        setUserInfo: (state, action) => {
            state.userInfo = action.payload;
            setItem(StorageEnum.User, action.payload);
        },
        setUserToken: (state, action) => {
            state.userToken = action.payload;
            setItem(StorageEnum.Token, action.payload);
        },
        clearUserInfoAndToken: (state) => {
            state.userInfo = {}
            state.userToken = {}
            removeItem(StorageEnum.User);
            removeItem(StorageEnum.Token);
        }
    }
});
export const { setUserInfo, setUserToken, clearUserInfoAndToken } = userSlice.actions;
export default userSlice.reducer;

```

```tsx
//store/index.ts
import { configureStore } from "@reduxjs/toolkit";
import userSlice from "./features/user";
// 注册
const store = configureStore({
    // 合并多个Slice
  reducer: {
    user: userSlice
  },
}
);
// 导出
export default store;

```

```tsx
import { Button, Form, type FormProps, Input, Col, Row } from 'antd';
import { useNavigate } from 'react-router-dom';
import { login } from '@/api/employee';
import { useDispatch, useSelector } from 'react-redux';
import { setUserToken } from '@/store/features/user';
type FieldType = {
    username?: string;
    password?: string;
};

export default function Login() {
    const dispatch = useDispatch()
    // 获取
    const token = useSelector((state: any) => state.user.userToken)
    const navigate = useNavigate()
    const onFinish: FormProps<FieldType>["onFinish"] = async (values) => {
        const data = await login(values)
        //修改
        dispatch(setUserToken(
            data.token
        ))
        navigate('/layout')
    };

    const onFinishFailed: FormProps<FieldType>["onFinishFailed"] = (errorInfo) => {
        console.log('Failed:', errorInfo);
    };
    return (
        <Row>
            <Col span={12} offset={12}>

                <Form
                    name="basic"
                    labelCol={{ span: 8 }}
                    wrapperCol={{ span: 16 }}
                    style={{ maxWidth: 600 }}
                    onFinish={onFinish}
                    onFinishFailed={onFinishFailed}
                    autoComplete="off"
                >
                    <Form.Item<FieldType>
                        label="Username"
                        name="username"
                        rules={[{ required: true, message: 'Please input your username!' }]}
                    >
                        <Input />
                    </Form.Item>

                    <Form.Item<FieldType>
                        label="Password"
                        name="password"
                        rules={[{ required: true, message: 'Please input your password!' }]}
                    >
                        <Input.Password />
                    </Form.Item>


                    <Form.Item wrapperCol={{ offset: 14, span: 10 }}>
                        <Button type="primary" htmlType="submit">
                            Submit
                        </Button>
                    </Form.Item>
                </Form>
            </Col>
        </Row>
    )
}

```

