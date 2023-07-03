---
tags: SOLID, Clean Code
---
# SOLID

Book Note: [https://github.com/JuanCrg90/Clean-Code-Notes](https://github.com/JuanCrg90/Clean-Code-Notes#chapter7)

## 1. Single Responsibility
> One class should have one, and only one responsibility.

```go
type Reservation struct {
	GetReservationDate() string
	CalculateCancellationFee() int
	Cancel()
	GetCustomerDetails() []Customer
}
```

## 2. Open/Closed Principle
> You should be able to extend a class behavior without modifying it.

In the future, if we have a new reservation type, as long as it implements the **CalculateFee**() method of the reservation interface, the **CalculateFee**() method should be able to calculate the cancellation fee.
```go
interface Reservation {
	CalculateFee() float64
}

type Trip struct {
	reservations []Reservation
}

func (t *Trip) CalculateTotalCancellationFee() float64 {
	total := 0.0
	for _, r := range t.reservations {
		total += r.CalculateFee() // diff with type of Reservation
	}
	return total
}
```

## 3. Liskov Substitution Principle (L) (thay thế)
> _Derived types must be substitutable for their base types_

object **o1** with the type of **S -** object **o2** with the type of **T**
program: P defined in terms of T (which means the programs are using o2)
after that, the behavior of P is unchanged + o1 is substituted for o2 (change o2 → o1)
```go
interface ActionPort {
	do()
}

type S struct {}
func (s *S) do() {}

type T struct {}
func (t *T) do() {}

func main() {
	var action ActionPort
	// t1 ->  using S
	action = &S{}

	// t2 -> using T
	action = &T{}
}
```

## 4. Interface Segregation Principle (I)
> _Many client-specific interfaces are better than one general-purpose interface_

- If the base class collects all behaviors, the derived class has to implement methods that don’t make sense to them
- TO AVOID, this principal recommends having an interface for each type of client.
```go
type Reservation interface {
	GetReservationDate() string
	CalculateCancellationFee() float64
}
type HotelReservation interface {
	Reservation
	ChangeType()
}
type FlightReservation interface {
	Reservation
	AddExtraLuggageAllowance(pieces int)
}
```

## 5.Dependency inversion (D)
> _Depend on abstraction, not on concretion_
- High-level modules should depend only on interfaces.

For example, there are **ServiceLayer** and **CommunicationLayer**, each package should have an interface. After that when we want to inject **CommunicationLayer** into **ServiceLayer**
This allows **ServiceLayer** to function just knowing **CommunicationLayerInterface** (don’t know how the functions inside Communication are implemented)

# Clean code
### Principle
- **Naming**: Vấn đề muôn thuở là đặt tên biến, hàm rõ nghĩa, descriptive
- **Split function**: Split function đủ nhỏ vs tên descriptive để document code và lộ rõ cấu trúc logic sườn (Figure.1)
- **Func DO ONE THING**: trick hay nhất là luôn sn logic là một set các TO theo thứ tự xử lý nhất định, mỗi TO sẽ DO 1 THING.
- **Func No FLAG**: Từ điều 3 thì function không nên nhận Flag như Args.
- **Never Nested**: Biến đoạn code phức tạp thành đơn giản bằng cách giảm Nested Code
[https://youtu.be/CFRhGnuXG-4](https://youtu.be/CFRhGnuXG-4)
- **TOP DOWN read**: Thứ tự xử lý, khai báo nên xếp theo chiều đọc top to bottom cho thuận tiện người đọc
- **Law of Demeter:** Module không nên biết rõ về nội tình của đối tượng nó tương tác tính toán. [https://github.com/JuanCrg90/Clean-Code-Notes#the-law-of-demeter](https://github.com/JuanCrg90/Clean-Code-Notes#the-law-of-demeter)

### Error handling
- **EXCEPTION** **than returns**: Nên sử dụng Exception để trả Error break func nhiều cấp hơn returns nhiều cấp, return nên là result OK, Failed trả về từ Func.
- **CATCH** **exit** **CONSISTENT**: Xử lý catch nên rời func vs state thống nhất bất kể xử lý lỗi ở đâu (thông qua error instance)
- **ERROR** **ENOUGH** **informative**: Lỗi trả về thông qua Error nên chứa đủ dữ liệu để có thể debug, trace ra vấn đề.
- **DON'T** **RETURNS**/**PASS** **NULL**: Không nên returns Null hoặc pass Null vào func theo ý đầu #EXCEPTION than returns