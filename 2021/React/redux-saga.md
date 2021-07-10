## redux-saga
1. 作用
  > 让redux的副作用（即依赖/影响外部环境的不纯的部分）处理起来更优雅
2. 为什么redux-saga 或者 dva 使用generator语法
  - 总的来说，其非常适合流程控制的场景
  - yield让描述串行/并行的异步操作变得很优雅
  - 以同步形式获取异步操作结果，且以同步的方式捕获错误
3. 基本流程
```js
// 单个saga文件-------------------------------------------------
import { call, put, all, takeEvery } from 'redux-saga/effects';
import { apiGetTaskOrderByTaskId } from '../../services';
import { NewToast } from '../../components';
import * as Actions from '../../actions/rescue/completedOrderList';

const defErrDec = '应用异常，请稍后再试';
// 创建单个saga，可以创建多个
function* getTaskOrderByTaskId({ payload }) {
  try{
    const {postData} = payload;
    const resData = yield call(apiGetTaskOrderByTaskId, {postData}) // 发送请求
    if(resData.code === 0) {
      let {data: taskDetailData = {}} = resData;
      taskDetailData = taskDetailData || {};
      yield put(Actions.setDetailState(taskDetailData)) // 拿到请求后，触发action
    } else {
      NewToast.show(resData.message || defErrDec);
    }
  } catch(e) {
    NewToast.show(e.message || defErrDec);
  }
}
// 注册到saga上，供外部组件调用
export default function* (){
  yield all([
    takeEvery('getTaskOrderByTaskId', getTaskOrderByTaskId),
  ]);
}

// 最外层saga--------------------------------------------------------
import { fork } from 'redux-saga/effects'
import waitOrderList from './rescue/waitOrderList';
import startTask from './rescue/startTask';
// 整合所有saga
export default function* rootSage(){
    yield fork(waitOrderList);
    yield fork(startTask);
}

// 组件中使用--------------------------------------------------------
function mapDispatchToProps(dispatch) { // 注入到props
  return {
    getCompletedTaskOrderListData: payload => {
      dispatch({
        type: 'getCompletedTaskOrderListData',
        payload
      })
    }
  }
}
// 使用时
getListData(type, searchText) {
  // type: 0: 正常获取数据，1: 重新加载数据
  const {getCompletedTaskOrderListData} = this.props;
  getCompletedTaskOrderListData({
    postData: {
      pageNum: this.state.pageNum,
      pageSize: this.state.pageSize,
      technicianPhone: global.mobile,
      taskNo: searchText || ''
    },
    type: type
  })
}

```
---