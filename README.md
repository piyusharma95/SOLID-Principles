# SOLID Principle

## SRP: 
#### A class should have one and only one reason to change, meaning that a class should have only one job.
	
#### From:
```
class AreaCalculator
{
    protected $shapes;

    public function __construct($shapes = [])
    {
        $this->shapes = $shapes;
    }

    public function sum()
    {
        foreach ($this->shapes as $shape) {
            if (is_a($shape, 'Square')) {
                $area[] = pow($shape->length, 2);
            } elseif (is_a($shape, 'Circle')) {
                $area[] = pi() * pow($shape->radius, 2);
            }
        }

        return array_sum($area);
    }

    public function output()
    {
        return implode('', [
          '',
              'Sum of the areas of provided shapes: ',
              $this->sum(),
          '',
      ]);
    }
}
```
#### To:
```
class SumCalculatorOutputter
{
    protected $calculator;

    public function __constructor(AreaCalculator $calculator)
    {
        $this->calculator = $calculator;
    }

    public function JSON()
    {
        $data = [
          'sum' => $this->calculator->sum(),
      ];

        return json_encode($data);
    }

    public function HTML()
    {
        return implode('', [
          '',
              'Sum of the areas of provided shapes: ',
              $this->calculator->sum(),
          '',
      ]);
    }
}

$shapes = [
  new Circle(2),
  new Square(5),
  new Square(6),
];

$areas = new AreaCalculator($shapes);
$output = new SumCalculatorOutputter($areas);

echo $output->JSON();
echo $output->HTML();

```

## Open Closed Principle: 
#### Objects or entities should be open for extension but closed for modification.

#### Open/closed principle is intended to mitigate risk when introducing new functionality. Since you don't modify existing code you can be assured that it wouldn't be broken. It reduces maintenance cost and increases product stability.

#### From:
```
class Bike {
    public void service() {
        System.out.println("Bike servicing strategy performed.");
    }
}

class Car {
    public void service() {
        System.out.println("Car servicing strategy performed.");
    }
}

class Garage {
    public void serviceBike(Bike bike) {
        bike.service();
    }

    public void serviceCar(Car car) {
        car.service();
    }
}
```
#### To:
```
interface Vehicle {
    void service();
}

class Bike implements Vehicle {
    @Override
    public void service() {
        System.out.println("Bike servicing strategy performed.");
    }
}

class Car implements Vehicle {
    @Override
    public void service() {
        System.out.println("Car servicing strategy performed.");
    }
}

class Garage {
    public void service(Vehicle vehicle) {
        vehicle.service();
    }
}
```

## Liskov Substitution: 
#### Let q(x) be a property provable about objects of x of type T. Then q(y) should be provable for objects y of type S where S is a subtype of T.
	
e.g.
```
In mathematics, a Square is a Rectangle. Indeed it is a specialization of a rectangle. The "is a" makes you want to model this with inheritance. However if in code you made Square derive from Rectangle, then a Square should be usable anywhere you expect a Rectangle. This makes for some strange behavior.

Imagine you had SetWidth and SetHeight methods on your Rectangle base class; this seems perfectly logical. However if your Rectangle reference pointed to a Square, then SetWidth and SetHeight doesn't make sense because setting one would change the other to match it. In this case Square fails the Liskov Substitution Test with Rectangle and the abstraction of having Square inherit from Rectangle is a bad one.
```

## Interface Segregation: 
#### A client should never be forced to implement an interface that it doesn’t use, or clients shouldn’t be forced to depend on methods they do not use.
	
#### From:
```
interface Shape{
    public int getLength();
    public int getWidth();
    public int getRadius();
    public double getArea();
}

class Rectangle implements Shape{
    int length;
    int width;
    public Rectangle(int length, int width){
        this.length = length;
        this.width = width;
    }
    public int getLength(){
        return length;
    }
    public int getWidth(){
        return width;
    }
    public int getRadius(){
        // Not applicable
        return 0;
    }
    public double getArea(){
        return width * length;
    }
}
class Square implements Shape{
    int length;
    
    public Square(int length){
        this.length = length;
    }
    public int getLength(){
        return length;
    }
    public int getWidth(){
        // Not applicable
        return 0;
    }
    public int getRadius(){
        // Not applicable
        return 0;
    }
    public double getArea(){
        return length * length;
    }
}

class Circle implements Shape{
    int radius;
    public Circle(int radius){
        this.radius = radius;
    }
    public int getLength(){
        // Not applicable
        return 0;
    }
    public int getWidth(){
        // Not applicable
        return 0;
    }
    public int getRadius(){
        return radius;
    }
    public double getArea(){
        return 3.14* radius * radius;
    }
}
```
#### To:
```
interface Length{
    public int getLength();
}
interface Width{
    public int getWidth();
}
interface Radius{
    public int getRadius();
}
interface Area {
    public double getArea();
}


class Rectangle implements Length,Width,Area{
    int length;
    int width;
    public Rectangle(int length, int width){
        this.length = length;
        this.width = width;
    }
    public int getLength(){
        return length;
    }
    public int getWidth(){
        return width;
    }
    
    public double getArea(){
        return width * length;
    }
}
class Square implements Length,Area{
    int length;
    
    public Square(int length){
        this.length = length;
    }
    public int getLength(){
        return length;
    }
    
    public double getArea(){
        return length * length;
    }
}

class Circle implements Radius,Area{
    int radius;
    public Circle(int radius){
        this.radius = radius;
    }
    
    public int getRadius(){
        return radius;
    }
    public double getArea(){
        return 3.14* radius * radius;
    }
}
```

## Dependency Inversion: 
#### Entities must depend on abstractions, not on concretions. It states that the high-level module must not depend on the low-level module, but they should depend on abstractions.
	
#### From:
```
class MySQLConnection
{
    public function connect()
    {
        // handle the database connection
        return 'Database connection';
    }
}

class PasswordReminder
{
    private $dbConnection;

    public function __construct(MySQLConnection $dbConnection)
    {
        $this->dbConnection = $dbConnection;
    }
}
```
#### To:
```
interface DBConnectionInterface
{
    public function connect();
}

class MySQLConnection implements DBConnectionInterface
{
    public function connect()
    {
        // handle the database connection
        return 'Database connection';
    }
}
class PasswordReminder
{
    private $dbConnection;

    public function __construct(DBConnectionInterface $dbConnection)
    {
        $this->dbConnection = $dbConnection;
    }
}

```
