# Lenguajes

## Técnicas

 - Anónimos y cierres: Java lambdas, Ruby procs y blocks, Scala
 - Mixins:
 	- [Ruby modules](http://ruby-doc.com/docs/ProgrammingRuby/html/tut_modules.html)
 	- [Scala traits](http://docs.scala-lang.org/tutorials/tour/traits)
	 	- Ejemplo: [Traits exercise](https://www.scala-exercises.org/std_lib/traits)
	 	- Ejemplo: [Stackable Traits pattern](http://www.artima.com/scalazine/articles/stackable_trait_pattern.html)
	 	- Lectura: [Herencia vs. composición con Scala mixins](http://baddotrobot.com/blog/2014/09/22/scala-mixins/)
 - Reflexión
 - Metaprogramación

## Paradigmas
 - Objetos
 - Eventos
 - Funcional


## Ejercicios

1. Interfaces funcionales - [Formateo de informes](#informes)
2. Cambio de interfaz - [Adaptadores](#adaptadores)
3. [Ruby from other languages](https://www.ruby-lang.org/en/documentation/ruby-from-other-languages/)
4. [Scala tour](http://docs.scala-lang.org/tutorials/tour/tour-of-scala)
5. [Scala exercises](https://www.scala-exercises.org/std_lib/)


<a id="informes">
# Ejercicio 1 - Interfaces funcionales
</a>
## Formateo de informes

### Versión en ruby

```ruby
class Report
  attr_reader :title, :text
  attr_accessor :formatter
  def initialize(formatter)
    @title = 'Informe mensual'
    @text = ['Todo marcha', 'muy bien.']
    @formatter = formatter
  end
  def output_report()
    @formatter.output_report(self)
  end
end
class HTMLFormatter
  def output_report(context)
    puts('<html>')
    puts(' <head>')
    # Output The rest of the report ...
    puts(" <title>#{context.title}</title>")
    puts(' </head>')
    puts(' <body>')
    context.text.each do |line|
      puts(" <p>#{line}</p>")
    end
    puts(' </body>')
    puts('</html>')
  end
end
	
class PlainTextFormatter
  def output_report(context)
    puts("***** #{context.title} *****")
    context.text.each do |line|
```

#### Procs
- Proc = objeto función = objeto que solo contiene un trozo de código 
 
```ruby
hello = lambda do
```

#### Blocks

- Code block = Función anónima, cierre o lambda
- Es la parte `do` ... `end`
- Simplificada como  `{` ... `}`

```ruby
hello = lambda {
```

- Parametrizable

```ruby
multiply = lambda {|x, y| x * y}

n = multiply.call(20, 3)
```

#### Llamada 

```ruby
name = 'John'
```


#### Paso de bloques como parámetros

- Simplemente, se añade al final de la llamada a un método 
- ¿Dónde se llama al bloque? Donde el método indique con `yield`
- El bloque (realmente un objeto `Proc`) se pasa como una especie de parámetro invisible
 
```ruby
def run_it
```
```ruby
run_it do
```
Salida:

```
Before the yield
```

- Llamada a un bloque con parámetros:

```ruby
def run_it_with_parameter
```
Salida:

```
Before the yield
```

- Hacer explícito el bloque pasado como parámetro: _ampersand_

```ruby
def run_it_with_parameter(&block)
```
Y para convertir un `Proc` en un bloque pasado como parámetro:

```ruby
my_proc = lambda {|x| puts("The value of x is #{x}")}
```

### Versión con interfaces funcionales (Ruby procs + blocks)

```ruby
class Report
	
	
```

Formateo HTML:

```ruby
HTML_FORMATTER = lambda do |context|

report = Report.new &HTML_FORMATTER
```

Formateo de texto:

```ruby
report = Report.new do |context|
```

<a id="adaptadores">
# Ejercicio 2 - Cambio de interfaz
</a>

## Adaptadores

###Tipos de adaptador:

- Adaptadores de clase

	![Adaptador de clases](./figuras/classAdapter.png)

- Adaptadores de objeto

	![Adaptador de objetos](./figuras/objectAdapter.png)	

### Implementaciones:

- Herencia
- Composición
- Herencia múltiple
- Mixins

### Mixins

En POO, un __mixin__ es una clase con métodos disponibles para otras clases sin tener que ser madre de estas otras (es decir, sin usar la herencia)

- Es una alternativa a la herencia múltiple
- Es una interfaz con métodos ya implementados
- No se _heredan_ sino que se _incluyen_
- Es una forma de implementar el principio de inversión de dependencias (DIP)

### Ejemplo en ruby: `modules` e `include`

Una manera de implementar un `Comparable` en ruby mediante el módulo [Comparable](https://ruby-doc.org/core-2.2.3/Comparable.html):

```ruby
class Student
  include Comparable # The class Student inherits Comparable module using include keyword
  attr_accessor :name, :score

  def initialize(name, score)
    @name = name
    @score = score
  end

  # Including the Comparison module, requires the implementing class to define the <=> comparison operator
  # Here's the comparison operator. We compare 2 student instances based on their scores.

  def <=>(other)
    @score <=> other.score
  end

  # Here's the good bit - I get access to <, <=, >,>= and other methods of the Comparable Interface for free.
end

s1 = Student.new("Peter", 100)
s2 = Student.new("Jason", 90)
s3 = Student.new("Maria", 95)

s1 > s2 #true
s1 <= s2 #false
s3.between?(s1,s2) #true
```

- La clase que incluye el módulo `Comparable` tiene que implementar:
	- el método `<=>`: es un método que incluye los siguientes operadores/métodos: `<, <=, ==, >, >=, between?` 
	- el atributo-criterio de comparación
	- En `x <=> y`, `x` es el receptor del mensaje/método e `y` es el argumento


## Módulos Ruby


### Interfaz americana

```ruby
class Renderer
		
```
```ruby
class TextObject
	
```

### Interfaz británica

```ruby
class BritishTextObject
	
```

### Adaptador de interfaz: versión clásica

```ruby
# british_text_object.rb
class BritishTextObjectAdapter < TextObject
	
```

### Adaptador de interfaz: versión ruby

```ruby
# Make sure the original class is loaded
	
```

- `require` carga la clase original
- la reescritura de métodos modifica la clase, no la declara de nuevo
- se puede hacer incluso con las clases built-in de la biblioteca de Ruby


### Adaptador de interfaz: instancias únicas 

```ruby
bto = BritishTextObject.new('hello', 50.8, :blue)
```
o bien:

```ruby
def bto.color
# ...
```

- Modifica el comportamiento solo de 1 instancia
- Ruby llama a esto _singleton methods_ y _singleton class_ (no es exactamente lo mismo que el patrón singleton del GoF)
- Ruby primero busca los métodos definidos en una clase singleton y luego en la clase regular que ha sido redefinida

## Scala Traits

### Ejemplo: Similarity

```scala
trait Similarity {
  def isSimilar(x: Any): Boolean
  def isNotSimilar(x: Any): Boolean = !isSimilar(x)
}
```
```scala
class Point(xc: Int, yc: Int) extends Similarity {
  var x: Int = xc
  var y: Int = yc
  def isSimilar(obj: Any) =
    obj.isInstanceOf[Point] &&
    obj.asInstanceOf[Point].x == x
}
object TraitsTest extends App {
  val p1 = new Point(2, 3)
  val p2 = new Point(2, 4)
  val p3 = new Point(3, 3)
  println(p1.isNotSimilar(p2))  //false
  println(p1.isNotSimilar(p3))  //true
  println(p1.isNotSimilar(2))   //true
}
```

- Polimorfismo usa la herencia
- Mixin es un mecanismo de reutilización de código

¿Usar traits con comportamiento va contra el principio general de que la [herencia de comportamiento](https://en.wikipedia.org/wiki/Composition_over_inheritance#Benefits) es una mala idea?

- Odersky llama _mixin traits_ a los traits con comportamiento
- Para ser un mixin genuino, debería mezclar comportamiento y no interfaces heredadas
- Leer [Scala Mixins: The right way](http://baddotrobot.com/blog/2014/09/22/scala-mixins/)

### Ejemplo: Iterator

Mixin-class composition

```scala
abstract class AbsIterator {
  type T
  def hasNext: Boolean
  def next: T
}

trait RichIterator extends AbsIterator {
  def foreach(f: T => Unit) { while (hasNext) f(next) }
}

class StringIterator(s: String) extends AbsIterator {
  type T = Char
  private var i = 0
  def hasNext = i < s.length()
  def next = { val ch = s charAt i; i += 1; ch }
}    

object StringIteratorTest {
  def main(args: Array[String]) {
    class Iter extends StringIterator(args(0)) with RichIterator
    val iter = new Iter
    iter foreach println
  }
}
```    
