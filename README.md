# InversifyCpp
C++ inversion of control and dependency injection container library.

## Features
- Compile time registration
- Compile time binding
- Compile time resolution

## Integration

```cpp

#include <mosure/inversify.hpp>

// for convenience
namespace inversify = mosure::inversify;

```

### Examples

#### Declare Interfaces

```cpp

#include <memory>


struct IFizz {
    virtual ~IFizz() = default;

    virtual void buzz() = 0;
}

using IFizzPtr = std::unique_ptr<IFizz>;

```

```cpp

namespace types {
    inversify::Symbol foo { "Foo" };
    inversify::Symbol bar { "Bar" };
    inversify::Symbol fizz { "Fizz" };
    inversify::Symbol fizzPtr { "FizzPtr" };
}

```


#### Declare Classes and Dependencies

```cpp

struct Fizz : IFizz {
    Fizz(int foo, double bar);

    void buzz() override;
};

// template<> inversify::Symbols
// inversify::injectable<Fizz>::dependencies = { types::foo, types::bar };

```


#### Creating and Configuring a Container

```cpp

inversify::Container container {};

container.bind<int>(types::foo).toConstantValue(10);
container.bind<double>(types::bar).toDynamicValue(
    [](inversify::context) {
        return 1.618;
    }
);

// container.bind<IFizz>(types::fizz).to<Fizz>().inSingletonScope();
// container.bind<IFizzPtr>(types::fizzPtr).to<Fizz*>();

container.bind<IFizz>(types::fizz).to(
    [](inversify::Context ctx) {
        auto foo = ctx.container.get<int>(types::foo);
        auto bar = ctx.container.get<double>(types::bar);

        Fizz fizz { foo, bar };

        return fizz;
    }
);

```


#### Resolving Dependencies

```cpp

auto fizz = container.get<IFizz>(types::fizz);
fizz.buzz();

```
