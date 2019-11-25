# SOLID-Principles
SOLID with examples

SOLID

1. S: Single Responsibility Principle -> Принцип единственной ответственности
2. O: Open-Closed Principle           -> Принцип открытости-закрытости
3. L: Liskov Substitution Principle   -> Принцип подстановки Барбары Лисков
4. Interface Segregation Principle    -> Принцип разделения интерфейса
5. Dependency Inversion Principle     -> Принцип инверсии зависимостей

"Single Responsibility Principle"
Класс должен быть ответственен лишь за что-то одно. Если класс отвечает за решение нескольких задач, его подсистемы, реализующие решение этих задач, оказываются связанными друг с другом. Изменения в одной такой подсистеме ведут к изменениям в другой.

Example:
protocol OrderProtocol {
    func takeAnOrder() -> String
}

protocol ConfirmationProtocol {
    func confirmOrder() -> String
}

protocol SenderProtocol {
    func sendOrder() -> String
}

class Order: OrderProtocol {
    func takeAnOrder() -> String {
        return "User make an order"
    }
}

class Confirmation: ConfirmationProtocol {
    func confirmOrder() -> String {
        return "User receive a confirmation letter"
    }
}

class Sender: SenderProtocol {
    func sendOrder() -> String {
        return "User's order was sent"
    }
}

class InternetShop {
    let order: OrderProtocol
    let confirmation: ConfirmationProtocol
    let sender: SenderProtocol
    
    init(order: OrderProtocol, confirmation: ConfirmationProtocol, sender: SenderProtocol) {
        self.order = order
        self.confirmation = confirmation
        self.sender = sender
    }
}

let someShop = InternetShop(order: Order(), confirmation: Confirmation(), sender: Sender())

* Вывод:
Создаем базовый класс, который будет содержать переменные протокольного типа под каждое отдельное действие, таким образом мы разделяем ответственность за каждое действие и возлагаем на отдельный класс. Один класс - одна ответственность 

-----------------------------------------------------------------------------------------------------------------------------

"Open-Closed Principle"
Программные сущности (классы, модули, функции) должны быть открыты для расширения, но не для модификации.

Example:
protocol LanguageKnowledge { /// -> общий протокол для всех классов с каком-то необходимым параметром, в данном случае нас интересует возможность писать на каком-то определенном языке
    func ableToWrite()
}

class IosDeveloper: LanguageKnowledge { /// -> создаем класс, который наследуется от общего протокола, для ios разработчиков
    func ableToWrite() {
        print("This programmer can write on swift language")
    }
}

class BackendDevelper: LanguageKnowledge { /// -> создаем класс, который наследуется от общего протокола, для backend разработчиков
    func ableToWrite() {
        print("This programmer can write on php language")
    }
}

Подобных классов может быть очень много, например под каждый язык
Для того, чтобы собрать команду, мы создаем класс с переменной протокольного типа, которая объединяет в себе все классы, которые подписаны на этот протокол
 
class Team {
    let team: [LanguageKnowledge]
    
    init(team: [LanguageKnowledge]) {
        self.team = team
    }
    
    func teamKnowledges() {
        team.map { $0.ableToWrite()}
    }
}

let iosDev = IosDeveloper()
let backDev = BackendDevelper()

let someTeam = Team(team: [iosDev, backDev])
someTeam.teamKnowledges()

* Вывод:
Таким образом у нас нет строгой привязки ни к команде, ни к языку
Если мы захотим добавить новых разработчиков, то просто необходимо будет создать класс и подписать его на протокол LanguageKnowledge и наш базовый класс Team просто расширится без модификации

-----------------------------------------------------------------------------------------------------------------------------

// Liskov Substitution Principle
print("3. Liskov Substitution Principle example")
/// Наследующий класс должен дополнять, а не замещать поведение базового класса

// Example:

protocol CanFly { /// -> создаем протокол, который будет характеризовать какой-то определенный класс
    var flyInAir: Bool { get }
}

protocol CanSwim { /// -> создаем протокол, который будет характеризовать какой-то определенный класс
    var swimByRiver: Bool { get }
}

class Transport { /// -> создаем базовый класс с переменной 'name', которая является общей для всего транспорта
    let name: String
    
    init(name: String) {
        self.name = name
    }
}

class Airplane: Transport, CanFly { /// -> создаем класс и подписываем на протокол, который характеризует этот класс
    var flyInAir: Bool
    
    init(flyInAir: Bool, name: String) {
        self.flyInAir = flyInAir
        super.init(name: name)
    }
}

class Ferry: Transport, CanSwim {
    var swimByRiver: Bool
    
    init(swimByRiver: Bool, name: String) {
        self.swimByRiver = swimByRiver
        super.init(name: name)
    }
}

let airplane = Airplane(flyInAir: true, name: "Plane")
let ferry = Ferry(swimByRiver: true, name: "Ferry")
print("There are two types of transport - \(airplane.name) and \(ferry.name)")
print("--------------------------------------------")

// Вывод:
/// Мы дополнили базовый класс при помощи протоколов CanSwim и CanFly при этом не меняя его поведение

// Interface Segregation Principle
print("4. Interface Segregation Principle example")
/// Создавайте узкоспециализированные интерфейсы, предназначенные для конкретного клиента. Клиенты не должны зависеть от интерфейсов, которые они не используют

protocol DriverRequirement {
    func ableToDriver()
}

protocol InterpreterRequirement {
    func ableToTranslate()
}

class DriverVacancy: DriverRequirement {
    func ableToDriver() {
        print("The driver is suite for this work")
    }
}

class InterpreterVacancy: InterpreterRequirement {
    func ableToTranslate() {
        print("The interpreter is suite for this work")
    }
}

let driver = DriverVacancy()
driver.ableToDriver()

let interpreter = InterpreterVacancy()
interpreter.ableToTranslate()
print("--------------------------------------------")

// Вывод:
/// Под конкретную вакансию создан конкретный протокол с требованиями именно к этой вакансии и класс, который подписан на этот протокол реализует только тот метод, который его касается

// Dependency inversion Principle
print("5. Dependency inversion Principle example")
/// Модули верхних уровней не должны зависеть от модулей нижних уровней. Оба типа модулей должны зависеть от абстракций

// Example:
protocol ItemsInStock {
    func hasItemToBuy()
}

class Shop {
    var goods: ItemsInStock
    
    init(goods: ItemsInStock) {
        self.goods = goods
    }
}

class ShoesItem: ItemsInStock {
    func hasItemToBuy() {
        print("Shoes are available in the shop")
    }
}

class FoodItem: ItemsInStock {
    func hasItemToBuy() {
        print("Food is available in the shop")
    }
}

let miniShop = Shop(goods: ShoesItem())
miniShop.goods.hasItemToBuy()

let superMarket = Shop(goods: FoodItem())
superMarket.goods.hasItemToBuy()

// Вывод:
/// Класс Shop не должен зависеть от переменной goods, иначе магазин будет продавать только один вид товара
/// Код должен быть гибким и мы должны иметь возможность подставлять разные классы товаров. Все классы должны быть подписаны на протокол ItemsInStock
