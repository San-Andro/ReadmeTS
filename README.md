# Шаблоны проектирования

Этот репозиторий содержит документацию по 15 популярным шаблонам проектирования (design patterns). Каждый шаблон описан с примерами реализации, плюсами, минусами и рекомендациями по использованию.

---

## Содержание

1. [Одиночка (Singleton)](#одиночка-singleton)
2. [Фабрика (Factory)](#фабрика-factory)
3. [Наблюдатель (Observer)](#наблюдатель-observer)
4. [Стратегия (Strategy)](#стратегия-strategy)
5. [Декоратор (Decorator)](#декоратор-decorator)
6. [Адаптер (Adapter)](#адаптер-adapter)
7. [Мост (Bridge)](#мост-bridge)
8. [Компоновщик (Composite)](#компоновщик-composite)
9. [Фасад (Facade)](#фасад-facade)
10. [Приспособленец (Flyweight)](#приспособленец-flyweight)
11. [Заместитель (Proxy)](#заместитель-proxy)
12. [Цепочка обязанностей (Chain of Responsibility)](#цепочка-обязанностей-chain-of-responsibility)
13. [Команда (Command)](#команда-command)
14. [Итератор (Iterator)](#итератор-iterator)
15. [Состояние (State)](#состояние-state)

---

## Одиночка (Singleton)

### Описание
Шаблон **Одиночка** гарантирует, что у класса есть только один экземпляр, и предоставляет глобальную точку доступа к этому экземпляру.

### Где и когда использовать
Когда в системе должен быть только один экземпляр класса (например, подключение к базе данных, логгер).
Когда нужно контролировать доступ к общему ресурсу.

### Где и когда не нужно
Если объект не должен быть глобальным или если создание нескольких экземпляров допустимо.
В многопоточных приложениях без синхронизации (может привести к созданию нескольких экземпляров).

### Плюсы
Гарантирует единственный экземпляр класса.
Предоставляет глобальный доступ к экземпляру.

### Минусы
Нарушает принцип единственной ответственности (SRP).
Усложняет тестирование из-за глобального состояния.

### Пример реализации

```typescript
class Singleton {
    private static instance: Singleton;

    private constructor() {}

    public static getInstance(): Singleton {
        if (!Singleton.instance) {
            Singleton.instance = new Singleton();
        }
        return Singleton.instance;
    }

    public log(message: string): void {
        console.log(`[LOG]: ${message}`);
    }
}

const instance1 = Singleton.getInstance();
const instance2 = Singleton.getInstance();

console.log(instance1 === instance2);
```

## Фабрика (Factory)
### Описание
Шаблон Фабрика предоставляет интерфейс для создания объектов, но позволяет подклассам изменять тип создаваемых объектов.

### Где и когда использовать
Когда заранее неизвестно, объекты каких типов нужно создавать.
Когда система должна быть независимой от процесса создания объектов.

### Где и когда не нужно
Если создание объектов простое и не требует гибкости.
Если использование фабрики усложняет код без необходимости.

### Плюсы
Упрощает добавление новых типов объектов.
Инкапсулирует процесс создания объектов.

### Минусы
Может привести к созданию большого количества подклассов.

### Пример реализации
```typescript
Copy
interface Product {
    operation(): string;
}

class ConcreteProductA implements Product {
    operation(): string {
        return "Product A";
    }
}

class ConcreteProductB implements Product {
    operation(): string {
        return "Product B";
    }
}

class Factory {
    public createProduct(type: string): Product {
        switch (type) {
            case "A":
                return new ConcreteProductA();
            case "B":
                return new ConcreteProductB();
            default:
                throw new Error("Invalid product type");
        }
    }
}

const factory = new Factory();
const productA = factory.createProduct("A");
console.log(productA.operation()); 
```

## Наблюдатель (Observer)

### Описание
Шаблон Наблюдатель позволяет объектам подписываться на события и получать уведомления об их изменении.

### Где и когда использовать
Когда нужно уведомлять объекты об изменении состояния другого объекта.
В системах, где важно поддерживать слабую связанность между компонентами.

### Где и когда не нужно
Если уведомления происходят слишком часто, что может привести к проблемам с производительностью.
Если подписчики не нужны или их слишком много.

### Плюсы
Упрощает добавление новых подписчиков.
Ослабляет связанность между объектами.

### Минусы
Может привести к утечкам памяти, если подписки не отменяются.
Уведомления могут быть неожиданными для подписчиков.

### Пример реализации

```typescript
interface Observer {
    update(message: string): void;
}

class ConcreteObserver implements Observer {
    constructor(private name: string) {}

    update(message: string): void {
        console.log(`${this.name} received: ${message}`);
    }
}

class Subject {
    private observers: Observer[] = [];

    public addObserver(observer: Observer): void {
        this.observers.push(observer);
    }

    public notifyObservers(message: string): void {
        for (const observer of this.observers) {
            observer.update(message);
        }
    }
}

const subject = new Subject();
const observer1 = new ConcreteObserver("Observer 1");
const observer2 = new ConcreteObserver("Observer 2");

subject.addObserver(observer1);
subject.addObserver(observer2);

subject.notifyObservers("Hello, Observers!");
```

## Стратегия (Strategy)

### Описание
Шаблон Стратегия позволяет определять семейство алгоритмов, инкапсулировать каждый из них и делать их взаимозаменяемыми.

### Где и когда использовать
Когда нужно использовать разные варианты одного алгоритма.
Когда класс содержит множество условных операторов для выбора поведения.

### Где и когда не нужно
Если алгоритмы редко меняются или их всего один.
Если добавление стратегий усложняет код без необходимости.

### Плюсы
Упрощает добавление новых алгоритмов.
Убирает дублирование кода.

### Минусы
Может привести к увеличению числа классов.

### Пример реализации
```typescript
interface Strategy {
    execute(a: number, b: number): number;
}

class AddStrategy implements Strategy {
    execute(a: number, b: number): number {
        return a + b;
    }
}

class SubtractStrategy implements Strategy {
    execute(a: number, b: number): number {
        return a - b;
    }
}

class Context {
    constructor(private strategy: Strategy) {}

    public setStrategy(strategy: Strategy): void {
        this.strategy = strategy;
    }

    public executeStrategy(a: number, b: number): number {
        return this.strategy.execute(a, b);
    }
}

const context = new Context(new AddStrategy());
console.log(context.executeStrategy(5, 3)); // 8

context.setStrategy(new SubtractStrategy());
console.log(context.executeStrategy(5, 3)); // 2
```

## Декоратор (Decorator)

### Описание
Шаблон Декоратор позволяет динамически добавлять объектам новые функциональные возможности, оборачивая их в объекты-декораторы.

### Где и когда использовать
Когда нужно добавлять поведение объектам на лету, не изменяя их код.
Когда наследование не подходит из-за большого количества комбинаций.

### Где и когда не нужно
Если добавляемое поведение редко используется.
Если декораторы усложняют код без необходимости.

### Плюсы
Гибкость в добавлении функциональности.
Соответствует принципу открытости/закрытости (Open/Closed Principle).

### Минусы
Может привести к созданию большого числа маленьких классов.

### Пример реализации

```typescript
interface Component {
    operation(): string;
}

class ConcreteComponent implements Component {
    operation(): string {
        return "ConcreteComponent";
    }
}

class Decorator implements Component {
    constructor(protected component: Component) {}

    operation(): string {
        return this.component.operation();
    }
}

class ConcreteDecoratorA extends Decorator {
    operation(): string {
        return `ConcreteDecoratorA(${super.operation()})`;
    }
}

class ConcreteDecoratorB extends Decorator {
    operation(): string {
        return `ConcreteDecoratorB(${super.operation()})`;
    }
}

const component = new ConcreteComponent();
const decorated = new ConcreteDecoratorB(new ConcreteDecoratorA(component));
console.log(decorated.operation()); 
```

## Адаптер (Adapter)

### Описание
Шаблон Адаптер позволяет объектам с несовместимыми интерфейсами работать вместе.

### Где и когда использовать
Когда нужно интегрировать старый код с новым.

Когда нужно использовать сторонний класс, но его интерфейс не соответствует вашим требованиям.

### Где и когда не нужно
Если интерфейсы уже совместимы.

Если можно изменить код одного из классов.

### Плюсы
Упрощает интеграцию разных систем.

Сохраняет принцип открытости/закрытости.

### Минусы
Может усложнить код из-за дополнительных классов.

### Пример реализации

```typescript
class OldSystem {
    oldRequest(): string {
        return "Old System";
    }
}

interface NewSystem {
    newRequest(): string;
}

class Adapter implements NewSystem {
    private oldSystem: OldSystem;

    constructor(oldSystem: OldSystem) {
        this.oldSystem = oldSystem;
    }

    newRequest(): string {
        return `Adapter: ${this.oldSystem.oldRequest()}`;
    }
}

const oldSystem = new OldSystem();
const adapter = new Adapter(oldSystem);
console.log(adapter.newRequest()); // Adapter: Old System
```

## Мост (Bridge)

### Описание
Шаблон Мост разделяет абстракцию и реализацию, позволяя им изменяться независимо.

### Где и когда использовать
Когда нужно разделить монолитный класс на несколько частей.

Когда нужно расширять классы в двух независимых измерениях.

### Где и когда не нужно
Если абстракция и реализация тесно связаны.

Если система простая и не требует гибкости.

### Плюсы
Упрощает добавление новых функций.

Уменьшает связанность между классами.

### Минусы
Может усложнить архитектуру.

### Пример реализации

```typescript
interface Implementation {
    operation(): string;
}

class ConcreteImplementationA implements Implementation {
    operation(): string {
        return "ConcreteImplementationA";
    }
}

class ConcreteImplementationB implements Implementation {
    operation(): string {
        return "ConcreteImplementationB";
    }
}

class Abstraction {
    constructor(protected implementation: Implementation) {}

    operation(): string {
        return `Abstraction: ${this.implementation.operation()}`;
    }
}

const implementationA = new ConcreteImplementationA();
const abstraction = new Abstraction(implementationA);
console.log(abstraction.operation());
```

## Компоновщик (Composite)

### Описание
Шаблон Компоновщик позволяет сгруппировать объекты в древовидные структуры и работать с ними как с единым объектом.

### Где и когда использовать
Когда нужно работать с иерархией объектов.

Когда клиентский код должен работать как с отдельными объектами, так и с их группами.

### Где и когда не нужно
Если структура данных простая и не требует иерархии.

### Плюсы
Упрощает работу с древовидными структурами.

Позволяет добавлять новые типы компонентов.

### Минусы
Может усложнить систему.

### Пример реализации

```typescript
interface Component {
    operation(): string;
}

class Leaf implements Component {
    operation(): string {
        return "Leaf";
    }
}

class Composite implements Component {
    private children: Component[] = [];

    add(component: Component): void {
        this.children.push(component);
    }

    operation(): string {
        return `Composite: ${this.children.map(child => child.operation()).join(", ")}`;
    }
}

const leaf1 = new Leaf();
const leaf2 = new Leaf();
const composite = new Composite();
composite.add(leaf1);
composite.add(leaf2);

console.log(composite.operation());
```

## Фасад (Facade)

### Описание
Шаблон Фасад предоставляет простой интерфейс для работы со сложной подсистемой.

### Где и когда использовать
Когда нужно упростить взаимодействие с комплексной системой.

Когда нужно скрыть сложность системы от клиента.

### Где и когда не нужно
Если клиенту нужен полный доступ к функциональности системы.

### Плюсы
Упрощает использование системы.

Снижает связанность между клиентом и подсистемой.

### Минусы
Может стать "божественным объектом", если злоупотреблять.

### Пример реализации

```typescript
class SubsystemA {
    operationA(): string {
        return "SubsystemA";
    }
}

class SubsystemB {
    operationB(): string {
        return "SubsystemB";
    }
}

class Facade {
    private subsystemA: SubsystemA;
    private subsystemB: SubsystemB;

    constructor() {
        this.subsystemA = new SubsystemA();
        this.subsystemB = new SubsystemB();
    }

    operation(): string {
        return `Facade: ${this.subsystemA.operationA()}, ${this.subsystemB.operationB()}`;
    }
}

const facade = new Facade();
console.log(facade.operation()); // Facade: SubsystemA, SubsystemB
```

## Приспособленец (Flyweight)

### Описание
Шаблон Приспособленец позволяет эффективно использовать общие объекты, уменьшая использование памяти.

### Где и когда использовать
Когда в системе много похожих объектов.

Когда нужно уменьшить использование памяти.

### Где и когда не нужно
Если объекты уникальны и не могут быть общими.

### Плюсы
Экономит память.

Упрощает работу с большим количеством объектов.

### Минусы
Может усложнить код.

### Пример реализации

```typescript
class Flyweight {
    constructor(private sharedState: string) {}

    operation(uniqueState: string): void {
        console.log(`Flyweight: Shared (${this.sharedState}) and Unique (${uniqueState})`);
    }
}

class FlyweightFactory {
    private flyweights: { [key: string]: Flyweight } = {};

    getFlyweight(sharedState: string): Flyweight {
        if (!this.flyweights[sharedState]) {
            this.flyweights[sharedState] = new Flyweight(sharedState);
        }
        return this.flyweights[sharedState];
    }
}

const factory = new FlyweightFactory();
const flyweight = factory.getFlyweight("shared");
flyweight.operation("unique"); // Flyweight: Shared (shared) and Unique (unique)
```

## Заместитель (Proxy)

### Описание
Шаблон Заместитель предоставляет объект-заменитель, который контролирует доступ к другому объекту.

### Где и когда использовать
Когда нужно контролировать доступ к объекту.

Когда нужно лениво инициализировать объект.

### Где и когда не нужно
Если доступ к объекту не требует контроля.

### Плюсы
Упрощает управление доступом.

Позволяет лениво инициализировать объекты.

### Минусы
Может замедлить выполнение программы.

### Пример реализации

```typescript
interface Subject {
    request(): void;
}

class RealSubject implements Subject {
    request(): void {
        console.log("RealSubject: Handling request.");
    }
}

class Proxy implements Subject {
    private realSubject: RealSubject;

    request(): void {
        if (!this.realSubject) {
            this.realSubject = new RealSubject();
        }
        this.realSubject.request();
    }
}

const proxy = new Proxy();
proxy.request(); // RealSubject: Handling request.
```

## Цепочка обязанностей (Chain of Responsibility)

### Описание
Шаблон Цепочка обязанностей позволяет передавать запросы по цепочке обработчиков.

### Где и когда использовать
Когда нужно обрабатывать запросы несколькими объектами.

Когда порядок обработчиков может меняться.

### Где и когда не нужно
Если запрос всегда обрабатывается одним объектом.

### Плюсы
Упрощает добавление новых обработчиков.

Уменьшает связанность между объектами.

### Минусы
Запрос может остаться необработанным.

### Пример реализации

```typescript
interface Handler {
    setNext(handler: Handler): Handler;
    handle(request: string): string | null;
}

abstract class AbstractHandler implements Handler {
    private nextHandler: Handler | null = null;

    setNext(handler: Handler): Handler {
        this.nextHandler = handler;
        return handler;
    }

    handle(request: string): string | null {
        if (this.nextHandler) {
            return this.nextHandler.handle(request);
        }
        return null;
    }
}

class ConcreteHandlerA extends AbstractHandler {
    handle(request: string): string | null {
        if (request === "A") {
            return `ConcreteHandlerA: Handling ${request}`;
        }
        return super.handle(request);
    }
}

class ConcreteHandlerB extends AbstractHandler {
    handle(request: string): string | null {
        if (request === "B") {
            return `ConcreteHandlerB: Handling ${request}`;
        }
        return super.handle(request);
    }
}

const handlerA = new ConcreteHandlerA();
const handlerB = new ConcreteHandlerB();
handlerA.setNext(handlerB);

console.log(handlerA.handle("B")); // ConcreteHandlerB: Handling B
```

## Команда (Command)

### Описание
Шаблон Команда инкапсулирует запрос в виде объекта, позволяя параметризовать клиентов с различными запросами.

### Где и когда использовать
Когда нужно параметризовать объекты выполняемыми действиями.

Когда нужно поддерживать отмену операций.

### Где и когда не нужно
Если система простая и не требует гибкости.

### Плюсы
Упрощает добавление новых команд.

Поддерживает отмену операций.

### Минусы
Может привести к увеличению числа классов.

### Пример реализации

```typescript
interface Command {
    execute(): void;
}

class SimpleCommand implements Command {
    constructor(private payload: string) {}

    execute(): void {
        console.log(`SimpleCommand: ${this.payload}`);
    }
}

class Receiver {
    action(): void {
        console.log("Receiver: Performing action.");
    }
}

class ComplexCommand implements Command {
    constructor(private receiver: Receiver, private a: string, private b: string) {}

    execute(): void {
        console.log(`ComplexCommand: ${this.a}, ${this.b}`);
        this.receiver.action();
    }
}

const receiver = new Receiver();
const command = new ComplexCommand(receiver, "A", "B");
command.execute();
// ComplexCommand: A, B
// Receiver: Performing action.
```

## Итератор (Iterator)

### Описание
Шаблон Итератор позволяет последовательно обходить элементы коллекции, не раскрывая её внутреннюю структуру.

### Где и когда использовать
Когда нужно обходить сложные структуры данных.

Когда нужно скрыть реализацию коллекции.

### Где и когда не нужно
Если коллекция простая и не требует сложного обхода.

### Плюсы
Упрощает обход коллекций.

Скрывает детали реализации коллекции.

### Минусы
Может быть избыточным для простых коллекций.

### Пример реализации

```typescript
interface Iterator<T> {
    next(): T;
    hasNext(): boolean;
}

class ConcreteIterator implements Iterator<string> {
    private collection: string[];
    private position: number = 0;

    constructor(collection: string[]) {
        this.collection = collection;
    }

    next(): string {
        return this.collection[this.position++];
    }

    hasNext(): boolean {
        return this.position < this.collection.length;
    }
}

const collection = ["A", "B", "C"];
const iterator = new ConcreteIterator(collection);

while (iterator.hasNext()) {
    console.log(iterator.next()); // A, B, C
}
```

## Состояние (State)

### Описание
Шаблон Состояние позволяет объекту изменять своё поведение в зависимости от внутреннего состояния.

### Где и когда использовать
Когда поведение объекта зависит от его состояния.

Когда в коде много условных операторов, зависящих от состояния.

### Где и когда не нужно
Если состояний мало и они редко меняются.

### Плюсы
Упрощает добавление новых состояний.

Убирает дублирование кода.

### Минусы
Может привести к увеличению числа классов.

### Пример реализации

```typescript

interface State {
    handle(context: Context): void;
}

class ConcreteStateA implements State {
    handle(context: Context): void {
        console.log("State A");
        context.setState(new ConcreteStateB());
    }
}

class ConcreteStateB implements State {
    handle(context: Context): void {
        console.log("State B");
        context.setState(new ConcreteStateA());
    }
}

class Context {
    private state: State;

    constructor(state: State) {
        this.state = state;
    }

    setState(state: State): void {
        this.state = state;
    }

    request(): void {
        this.state.handle(this);
    }
}

const context = new Context(new ConcreteStateA());
context.request(); // State A
context.request(); // State B
```
