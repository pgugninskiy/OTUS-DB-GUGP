# OTUS-DB-GUGP
# 1.Схема базы данных
Эту схему я делал на другом учебном курсе, в рамках курса по "Системному анализу advance" (т.к. работаю системным аналитиком).
Теперь буду развиваться дальше и хочу перейти к её реальному воплощению))





@startuml

skinparam linetype ortho

package "Partners" <<Folder>> {
entity Partner {
  * Organization
  * City
  * INN
  * KPP
  * LegalAddress
  * OKVED
  * Status (sign of activation)
  * TypeBusiness
  * Turnover
  * Large : boolean
  * Tariff
}

entity Restaurant {
   * Name
   * Address
   * ConditionRestaurant
}
entity Table {
   * Restaurant
   * Number
   * NumberSeats
   * Reservation
}
entity Tariff {
   * Name
   * Commission
   * Turnover
}
}
package "Users" <<Folder>> {
entity User {
   * Name
   * Mail
   * Telephone
   * Password
}
entity Reservation {
   * User
   * Restaurant
   * Date
   * NumberGuests
   * Status
}
}
package "Platform" <<Folder>> {
entity Employee {
   * Name
   * Mail
   * Login
   * Password
}
}
package References <<Rectangle>> {
enum "Partner Status" {
   * Created
   * Activated
   * Locked
   * Removed

}
enum "Booking Status" {
   * Created
   * Confirmed
   * NotConfirmed
   * Cancel

}
enum "Restaurant status" {
   * Available
   * NotAvailable
}
enum "Tariff name" {
   * General
   * Individual
}
enum "Type of business" {
   * Restaurant
   * DiningRoom
   * Net
}
}

  Partner "*" --> "1"  Restaurant 
  Partner "*" --> "1" "Partner Status"
  Partner "*" <-- "1" Employee
  Partner "*" --> "1" Tariff
  
  User  "1" <-- "*" Reservation 
  
  Restaurant "*" --> "1" "Restaurant status"
  Restaurant "1" <-- "*" Reservation 
  Restaurant "*" --> "1" "Type of business"
  
  Table  "*" --> "*" Restaurant
  
  Reservation  "*" --> "1" "Booking Status"
  
  Tariff  "*" --> "1" "Tariff name"
@enduml


# 2. Примеры бизнес задач
* Бронирование столика в ресторане
* Заведение аккаунта для Партнера 
* Заведение аккаунта для Пользователя
* Выбор тарифа для обслуживания Партнера
* Предоставление информации о том, работает сейчас ресторана или нет.
* Предоставление информации о статусе бронирования.


# 3. Рекомендации к репликации
Так как с нашим приложением, возможно будет работать одновременно более 100, а возможно и более 1000 пользователей, то можно предположить, что запросов к БД будет много (_планируется, что запросы будут на создание заявки для бронирования столика и получение статуса бронирования_)

Таким образом, рекомендации к репликации данных будут следующие:
1. Нам нужна репликация с многочисленными ведущими
2. Нам потребуется балансировщик нагрузки, который будет обеспечивать равномерное распределение запросов на чтение и запись между компьютерами

# 4. Рекомендации к резервному копированию данных
1. Планируется выполнять резервное копирование данных ежедневно
2. Время выполнения копирования с 5 до 9 утра
3. Хранить копию можно как вариант, в облачном сервисе отечественных компаний
