## 组件内的守卫（使用 typescript）

说明：需要提前使用registerHooks函数注册钩子函数，才能使得beforeRouteLeave等函数触发。

```vue
<script lang="ts">
import Vue from 'vue'
import Component from 'vue-class-component'
import { Route, RawLocation } from 'vue-router'
Component.registerHooks([
    'beforeRouteLeave',
    'beforeRouteUpdate', // (2.2 新增)
    'beforeRouteLeave',
])
type NextVueCallback<V,B extends any = any> = (vm: V) => B
type NextFuncParams<V> = RawLocation | Error | false | NextVueCallback<V> | void
type NavigationGuardNextFunc<V extends Vue = Vue> = (to?: NextFuncParams<V>) => void
@Component
export default class Order extends Vue {
    private beforeRouteEnter (from: Route, to: Route, next: NavigationGuardNextFunc<Vue>) {
        // 在渲染该组件的对应路由被 confirm 前调用
        // 不！能！获取组件实例 `this`
        // 因为当守卫执行前，组件实例还没被创建
    },
 
    private beforeRouteUpdate (from: Route, to: Route, next: NavigationGuardNextFunc<Vue>) {
        // 在当前路由改变，但是该组件被复用时调用
        // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
        // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
        // 可以访问组件实例 `this`
    },
 
    private beforeRouteLeave(from: Route, to: Route, next: NavigationGuardNextFunc<Vue>) {
        // 导航离开该组件的对应路由时调用
        // 可以访问组件实例 `this`
       const answer = window.confirm('Do you really want to leave? you have unsaved changes!')
       if (answer) {
          next()
       } else {
          next(false)
       }
    }
 
}
</script>
```
