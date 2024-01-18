# In- / Out-Parameter

```c++
void in(const int &x); // x is an in-parameter
void out(int &x); // x is an out-parameter
```

# Zeiger / Referenzen

```c++
struct Person {
  std::string m_name;

  Person(std::string &&name)
  : m_name(std::move(name))
  {}
};

void main() {
  Person alice("Alice"); // on stack
  Person *bob = new Person("Bob"); // on heap
  std::unique_ptr<Person> carol = std::make_unique<Person>("Carol"); // unique pointer
  std::shared_ptr<Person> david = std::make_shared<Person>("David"); // shared pointer
  std::weak_ptr<Person> weak = david; // weak pointer
  std::unique_ptr<Person> moved = std::move(carol); // unique pointer moved from carol

  std::cout << alice.m_name << std::endl; // "Alice"
  std::cout << bob->m_name << std::endl; // "Bob"
  std::cout << carol->m_name << std::endl; // SIGSEGV -> pointer was moved
  std::cout << david->m_name << std::endl; // "David"
  if (auto temp = weak.lock()) {
    std::court << temp->m_name << std::endl; // "David"
  }
  std::cout << moved->m_name << std::endl; // "Carol"

  delete bob;
}
```

# l-values / r-values

```c++
void fn(int &a); // l-value
void fn(int &&a); // r-value
void fn(int a); // both

void test(int &a) {
  std::cout << "lvalue" << std::endl;
}

void test(int &&a) {
  std::cout << "rvalue" << std::endl;
}

void main() {
  int x = 3;
  test(x); // lvalue
  test(5); // rvalue
  test(x+5); // rvalue
  test(++x); // lvalue
  test(x++); // rvalue
  test(std::move(x)); // rvalue
}
```

# Vererbung

```c++
class Person {
  int m_age;
public:
  Person(int age): m_age(age) {}
}

class Student : public Person {
  int m_number;
public:
  Student(int age, int number): Person(age), m_number(number) {}
}

// cast
Person *person = new Person();
Student *student = new Student();
Person *p1 = student;
Student *p2 = static_cast<Student*>(p1);
Student *p3 = static_cast<Student*>(person); // ungültig
Student *p4 = dynamic_cast<Student*>(p1);
Student *p5 = dynamic_cast<Student*>(person); // p5 = nullptr

shared_ptr<Person> spPerson = make_shared<Person>();
shared_ptr<Person> spStudent = make_shared<Student>();
auto sp1 = static_pointer_cast<Student>(spStudent);
auto sp2 = dynamic_pointer_cast<Student>(spStudent);
auto sp3 = dynamic_pointer_cast<Student>(spPerson); // sp3 = nullptr

// RTTI
type_info &t = typeid(*person);
```

## Zugriffsrechte

| Basisklasse | geerbt als | abgeleitete Klasse |
| - | - | - |
| public<br>protected<br>private | public | public<br>protected<br>no access |
| public<br>protected<br>private | protected | protected<br>protected<br>no access |
| public<br>protected<br>private | private | private<br>private<br>no access |

# Templates

```c++
template<typename T>
T min(T a, T b) {/*...*/}

template<typename T> // T ist irgendein Typ
template<class T> // T ist eine Klasse
template<size_t S> // S ist ein Wert vom Typ size_t

// Variadic templates
template<typename... Ts> class C {}
size_t types = sizeof...(Ts);

template<typename... Ts> void func(const Ts&... vs) {}
size_t params = sizeof...(vs);

template<typename First, typename... Rest>
void logging(const First &first, const Rest&... rest) {
  std::cout << '[' << sizeof...(rest) << "] ";
  std::cout << first << ", ";
  logging(rest...);
}

// Universalreferenz
template<typename T> void f(T &&x) {}
template<typename T> void g(T &&y) {
  f(std::forward<T>(y));
}
```

# Konzepte

```c++
template<typename T>
concept Addable = requires (T a, T b) {
  a+b;
};

template<typename T>
concept TypeTest = requires {
  typename T::ElementType;
};

template<typename T, template<typename> class S>
concept TemplateTest = requires {
  typename S<T>;
};

template<typename T>
concept InvokeIntegral = requires (T a) {
  { a() } -> std::integral;
};
```

# Lambdas

```c++
[](int x, int y) -> int {return x + y;}
```

| | |
| - | - |
| `[bias]` | Zugriff auf `bias` by-value |
| `[&bias]` | Zugriff auf `bias` by-reference |
| `[=]` | Zugriff auf alles by-value |
| `[&]` | Zugriff auf alles by-reference |
| `[this]` | Zugriff auf alle Member by-pointer |
| `[=, &bias]` | Zugriff auf `bias` by-reference, auf alles andere by-value |
