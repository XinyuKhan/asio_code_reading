# execution_context 类代码阅读

## Related Files

```bash
asio\include\asio\execution_context.hpp
asio\include\asio\impl\execution_context.hpp
asio\include\asio\impl\execution_context.ipp
```
## Namespace

```c++
namespace asio;
```
## Description

```c++

/// A context for function object execution.
/**
 * An execution context represents a place where function objects will be
 * executed. An @c io_context is an example of an execution context.
 *
 * @par The execution_context class and services
 *
 * Class execution_context implements an extensible, type-safe, polymorphic set
 * of services, indexed by service type.
 *
 * Services exist to manage the resources that are shared across an execution
 * context. For example, timers may be implemented in terms of a single timer
 * queue, and this queue would be stored in a service.
 *
 * Access to the services of an execution_context is via three function
 * templates, use_service(), add_service() and has_service().
 *
 * In a call to @c use_service<Service>(), the type argument chooses a service,
 * making available all members of the named type. If @c Service is not present
 * in an execution_context, an object of type @c Service is created and added
 * to the execution_context. A C++ program can check if an execution_context
 * implements a particular service with the function template @c
 * has_service<Service>().
 *
 * Service objects may be explicitly added to an execution_context using the
 * function template @c add_service<Service>(). If the @c Service is already
 * present, the service_already_exists exception is thrown. If the owner of the
 * service is not the same object as the execution_context parameter, the
 * invalid_service_owner exception is thrown.
 *
 * Once a service reference is obtained from an execution_context object by
 * calling use_service(), that reference remains usable as long as the owning
 * execution_context object exists.
 *
 * All service implementations have execution_context::service as a public base
 * class. Custom services may be implemented by deriving from this class and
 * then added to an execution_context using the facilities described above.
 *
 * @par The execution_context as a base class
 *
 * Class execution_context may be used only as a base class for concrete
 * execution context types. The @c io_context is an example of such a derived
 * type.
 *
 * On destruction, a class that is derived from execution_context must perform
 * <tt>execution_context::shutdown()</tt> followed by
 * <tt>execution_context::destroy()</tt>.
 *
 * This destruction sequence permits programs to simplify their resource
 * management by using @c shared_ptr<>. Where an object's lifetime is tied to
 * the lifetime of a connection (or some other sequence of asynchronous
 * operations), a @c shared_ptr to the object would be bound into the handlers
 * for all asynchronous operations associated with it. This works as follows:
 *
 * @li When a single connection ends, all associated asynchronous operations
 * complete. The corresponding handler objects are destroyed, and all @c
 * shared_ptr references to the objects are destroyed.
 *
 * @li To shut down the whole program, the io_context function stop() is called
 * to terminate any run() calls as soon as possible. The io_context destructor
 * calls @c shutdown() and @c destroy() to destroy all pending handlers,
 * causing all @c shared_ptr references to all connection objects to be
 * destroyed.
 */
```

### 方法对象运行上下文
所谓 ***执行上下文***（***execution context***）表示一个用于方法对象执行的环境。 ***io_context*** 是一个 ***执行上下文*** 的示例类。
#### **execution_context** 类和**服务** （**services**）

***execution_context*** 类实现了一个**可扩展的（extensible）**、**类型安全的（type-safe）**、
**多态的（polymorphic）** 并使用**服务类型（service type）**索引的**服务集合（service set）**

服务存在的目的是用来管理在一个执行上下文中共享的的资源。例如，定时器可能基于一个单一的定时器队列来实现，那么这个队列会被存储在一个服务对象中。

对于执行上下文中的服务的访问是通过 ***use_service()*** ,  ***add_service()*** 和 ***has_service()*** 这三个方法模板来实现。

在一个 ***use_service\<Service\>()*** 的调用中，类型模板参数用于选择一个服务类型并且访问该类型的成员。如果Service所指示的服务类型不在该执行上下文中，那么一个该服务类型的对象将会被创建并且添加到该执行上下文中。c++程序可以通过 ***has_service\<Service\>()*** 模板方法的调用来检查该执行上下文是否已经实现了这个特定类型的服务。

可以通过 ***add_service\<Service\>()*** 函数模板的调用来显式地将服务对象（***Service objects***）加入到执行上下文中。如果添加的服务已经存在于该执行上下文中， ***service_already_exists*** 异常会被抛出；如果该服务的拥有者不是该执行上下文对象， ***invalid_service_owner*** 异常会被抛出。

