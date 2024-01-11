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

# Lambdas

```
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
