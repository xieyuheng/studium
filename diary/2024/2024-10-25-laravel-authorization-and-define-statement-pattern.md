---
title: laravel authorization
date: 2024-10-25
---

在读 [laravel authorization 的文档](https://laravel.com/docs/authorization) 的时候，看到这样一段话：

> Laravel provides two primary ways of authorizing actions: **gates**
> and **policies**. Think of gates and policies like **routes** and
> **controllers**. Gates provide a simple, closure-based approach to
> authorization while policies, like controllers, group logic around a
> particular model or resource. In this documentation, we'll explore
> gates first and then examine policies.

定义的例子：

```php
Gate::define('update-post', function (User $user, Post $post) {
    return $user->id === $post->user_id;
});

Gate::define('update-post', [PostPolicy::class, 'update']);
```

不是简单地用函数来做抽象，
而是把函数绑定到一些全局的名字上，
然后再灵活地调用这些函数。

比如，可以用 `before` 来处理 administrator 的权限：

```php
Gate::before(function (User $user) {
    if ($user->isAdministrator()) {
        return true;
    }
});
```

在定义 **routes** 和 **controllers** 的时候，
是把 url pattern 绑定到函数上。

- 调用函数的方式可以很复杂，
  比如实现 middleware 机制等等。

这种模式应该叫什么名字？
这种模式很像是在实现解释器时，
对 define 一类的 statement 的实现。
也许这个模式就应该叫 Define Statement。

注意，在实现 propagator 时，在做浅嵌入时，
我也用了类似的方法：

```typescript
export const squarer = definePrimitive(2, square)
export const sqrter = definePrimitive(2, sqrt)

export const quadratic = definePropagator(2, (r, s) => {
  squarer(r, s)
  sqrter(s, r)
})
```

在 JS 中实现 generic function 时也是如此：

```typescript
export const add = defineGeneric()
defineHandler(add, [isNumber, isNumber], (x, y) => x + y)
defineHandler(add, [isInterval, isInterval], intervalAdd)
defineHandler(add, [isInterval, isNumber], coercing(toInterval, intervalAdd))
defineHandler(add, [isNumber, isInterval], coercing(toInterval, intervalAdd))

export const sub = defineGeneric()
defineHandler(sub, [isNumber, isNumber], (x, y) => x - y)
defineHandler(sub, [isInterval, isInterval], intervalSub)
defineHandler(sub, [isInterval, isNumber], coercing(toInterval, intervalSub))
defineHandler(sub, [isNumber, isInterval], coercing(toInterval, intervalSub))
```

# Define Statement 在 HTTP server 中的使用

在实现 server 时，我选择了更简单的 API：

```typescript
export const server = createServer({
  async fetch(request) {
    return dispatch(request, {
      "POST /password-register/:username": PasswordRegister,
      "POST /password-login/:username": PasswordLogin,
      "DELETE /users/:username": UserDelete,
      "HEAD /users/:username": UserHas,
      "GET /users/:username": UserGet,
      "PATCH /users/:username": UserPatch,
      "GET /ping": Ping,
    })
  },
})
```

而不是和 laravel 或 express 一样，
使用 Define Statement 来实现 route。

在实现 authorization 时，
我也不想用 Define Statement，
想直接用函数抽象来实现。
