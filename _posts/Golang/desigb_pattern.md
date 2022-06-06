1. Builder Pattern
- Builder is a creational design pattern, which allows constructing complex objects step by step.

- https://refactoring.guru/design-patterns/builder/go/example#:~:text=Builder%20is%20a%20creational%20design,using%20the%20same%20construction%20process.

- https://devcharmander.medium.com/design-patterns-in-golang-the-builder-dac468a71194

type Employee struct {
  Name      string
  Role      string
  MinSalary int
  MaxSalary int
}
Under most circumstances a developer would create an instance of the Employee struct in their code and assign a value to each of these fields, but in some circumstances you might want to do more than that. For instance, you might decide that when the Role field is set that you can also assign the min and max salary fields based on the role. This could be achieved using a builder.

type Builder struct {
  e Employee
}

func (b *Builder) Build() Employee {
  return b.e
}

func (b *Builder) Name(name string) *Builder {
  b.e.Name = name
  return b
}

func (b *Builder) Role(role string) *Builder {
  if role == "manager" {
    b.e.MinSalary = 20000
    b.e.MaxSalary = 60000
  }
  b.e.Role = role
  return b
}
Now when we are constructing an Employee we can use the builder to ensure that the min and max salary get set when we set the Role field. We could even add validation in and verify that the role is a valid one - though at that point we would need to also return an error.

Below we can see how this builder might be used:

b := &Builder{}
employee := b.
  Name("Michael Scott").
  Role("manager").
  Build()
fmt.Println(employee)
// {Michael Scott manager 20000 60000}