# Ruby: руководство по стилю RM+

# Вступление

Это руководство по оформлению кода на языке программирования Ruby и фреймворке
Ruby On Rails 3 в контексте разработки плагинов для системы управления проектами
Redmine, разработанное в компании [RM+](http://www.rmplus.pro).

В качестве основы для руководства были взяты [«The Ruby Style Guide»](https://github.com/bbatsov/ruby-style-guide)
и [дополнение к нему для Rails](https://github.com/bbatsov/rails-style-guide),
а [также русский перевод](https://github.com/arbox/ruby-style-guide/blob/master/README-ruRU.md)
The Ruby Style Guide.

## Оглавление

1. [Организация исходного кода](#Организация-исходного-кода)
2. [Синтаксис](#Синтаксис)
3. [Наименование](#Hаименование)
4. [Комментарии](#Комментарии)
5. [Пометки в комментариях](#Пометки-в-комментариях)
6. [Классы и модули](#Классы-и-модули)
7. [Исключения](#Исключения)
8. [Коллекции](#Коллекции)
9. [Строки](#Строки)
10. [Регулярные выражения](#Регулярные-выражения)
11. [Процентные литералы](#Процентные-литералы)
12. [Метапрограммирование](#Метапрограммирование)
13. [Разное](#Разное)
14. [Инструментарий](#Инструментарий)

## 1. Организация исходного кода

1.1. <a name="utf-8"></a> Используйте кодировку `UTF-8`.<sup>[[ссылка](#utf-8)]</sup>

1.2. <a name="spaces-indentation"></a> Используйте два пробела на уровень
  отступа (т.е. мягкую табуляцию). Никаких знаков табуляции.
  <sup>[[ссылка](#spaces-indentation)]</sup>

  ```Ruby
  # плохо (четыре пробела)
  def some_method
      do_something
  end

  # хорошо
  def some_method
    do_something
  end
  ```

1.3. <a name="no-semicolon"></a> Не используйте `;` для разделения инструкций и
  выражений. Отсюда следует, что каждая инструкция должна занимать свою отдельную строку.
  <sup>[[ссылка](#no-semicolon)]</sup>

  ```Ruby
  # плохо
  puts 'foobar'; # лишняя точка с запятой

  puts 'foo'; puts 'bar' # two expressions on the same line

  # хорошо
  puts 'foobar'

  puts 'foo'
  puts 'bar'

  puts 'foo', 'bar' # частное правило - аргументы puts можно перечислять через запятую
  ```

  !Выбросить!

1.4. <a name="no-single-line-methods"></a>
  Избегайте методов в одну строку, за исключением пустых методов.
<sup>[[link](#no-single-line-methods)]</sup>

  ```Ruby
  # плохо
  def too_much; something; something_else; end

  # сносно (обратите внимание, что первая `;` обязательна)
  def no_braces_method; body end

  # сносно (обратите внимание, что вторая `;` опциональна)
  def no_braces_method; body; end

  # сносно (корректный синтаксис, но отсутствие `;` создает трудности при прочтении)
  def some_method() body end

  # хорошо
  def some_method
    body
  end

  # хорошо
  def no_op; end
  ```

  !Никогда не использовать точку с запятой!
  кроме
    render_404; return


1.5. <a name="spaces-operators"></a> Вставляйте пробелы вокруг операторов, после
  запятых, двоеточий и точек с запятыми,  вокруг `{` и перед `}`.
  <sup>[[ссылка](#spaces-operators)]</sup>

  ```Ruby
  sum = 1 + 2
  a, b = 1, 2
  [1, 2, 3].each { |e| puts e }
  class FooError < StandardError; end
  ```

  Единственным исключением для операторов является оператор возведения в степень:

  ```Ruby
  # плохо
  e = M * c ** 2

  # хорошо
  e = M * c**2
  ```

  Относительно `{` и `}`:

  Для литерала хеша популярны два стиля:

  ```Ruby
  # хорошо (пробел после { и до })
  { one: 1, two: 2 }

  # хорошо (пробелы отсутствуют после { и перед })
  {one: 1, two: 2}
  ```

  Первый вариант несколько проще для чтения.
  Второй вариант обладает тем преемуществом, что создается видимое различие
  между блоками и хэшами, когда хэш использутся в блоке.

  В случае включаемых в строки выражений рекомендуется не отбивать фигурные скобки пробелами:

  ```Ruby
  # хорошо
  "string#{expr}"

  # плохо
  "string#{ expr }"
  ```

  Хэши - с пробелами по бокам, пустые без пробелов ({}).

1.6. <a name="no-spaces-braces"></a> Не используйте пробел после `(`, `[` или перед `]`, `)`.
  <sup>[[ссылка](#no-spaces-braces)]</sup>

  ```Ruby
  some(arg).other
  [1, 2, 3].size
  ```

1.7. <a name="no-space-bang"></a> Не используйте пробел после `!`.
  <sup>[[ссылка](#no-space-bang)]</sup>

  ```Ruby
  # плохо
  ! something

  # хорошо
  !something
  ```

1.8. <a name="no-space-inside-range-literals"></a>Записывайте литералы диапазонов
  без пробелов.
  <sup>[[link](#no-space-inside-range-literals)]</sup>

    ```Ruby
    # плохо
    1 .. 3
    'a' ... 'z'

    # хорошо
    1..3
    'a'..'z'
    ```

1.9. <a name="indent-conditional-assignment"></a>
  Do not assign result of 'if' expression. Assigning 'case' expression results is permitted.
  When assigning the result of a 'case' conditional expression to a variable,
  preserve the usual alignment of its branches.
<sup>[[link](#indent-conditional-assignment)]</sup>

  ```Ruby
  # bad - pretty convoluted
  kind = case year
  when 1850..1889 then 'Blues'
  when 1890..1909 then 'Ragtime'
  when 1910..1929 then 'New Orleans Jazz'
  when 1930..1939 then 'Swing'
  when 1940..1950 then 'Bebop'
  else 'Jazz'
  end

  # bad - 'if' operator
  result = if some_cond
    calc_something
  else
    calc_something_else
  end

  # good
  kind = case year
         when 1850..1889 then 'Blues'
         when 1890..1909 then 'Ragtime'
         when 1910..1929 then 'New Orleans Jazz'
         when 1930..1939 then 'Swing'
         when 1940..1950 then 'Bebop'
         else 'Jazz'
         end

  # bad
  result = if some_cond
             calc_something
           else
             calc_something_else
           end

  # bad
  kind =
    case year
    when 1850..1889 then 'Blues'
    when 1890..1909 then 'Ragtime'
    when 1910..1929 then 'New Orleans Jazz'
    when 1930..1939 then 'Swing'
    when 1940..1950 then 'Bebop'
    else 'Jazz'
    end

    #bad
  result =
    if some_cond
      calc_something
    else
      calc_something_else
    end
  ```

1.10. <a name="empty-lines-between-methods"></a> Используйте пустые строки для отбивки методов друг от друга и
  выделения логических частей кода.
  <sup>[[ссылка](#empty-lines-between-methods)]</sup>

  ```Ruby
  def some_method
    data = initialize(options)

    data.manipulate!

    data.result
  end

  def some_method
    result
  end
  ```

1.11. <a name="no-trailing-params-comma"></a> Не ставьте запятую после последнего параметра в вызове метода.
  <sup>[[ссылка](#no-trailing-params-comma)]</sup>

  ```Ruby
  # плохо
  some_method(
               size,
               count,
               color,
             )

  # плохо
  some_method(size, count, color, )

  # хорошо
  some_method(size, count, color)
  ```

1.12. <a name="spaces-around-equals"></a>
  `Обсудить с Василем`
  Вставляйте пробелы вокруг оператора присваивания `=`, когда
  назначаете параметрам метода значения по умолчанию:
  <sup>[[ссылка](#spaces-around-equals)]</sup>


  ```Ruby
  # хорошо
  def some_method(arg1=:default, arg2=nil, arg3=[])
    # do something...
  end
  ```

1.13. <a name="no-trailing-backslash"></a>
  Не используйте символ продления строки кода `\`.
  <sup>[[ссылка](#no-trailing-backslash)]</sup>

  ```Ruby
  # плохо
  result = 1 - \
           2

  # плохо
  result = 1 \
           - 2

  # плохо
  long_string = 'First part of the long string' \
                ' and second part of the long string'

  ```

1.14. <a name="consistent-multi-line-chains"></a>
  Используйте единый стиль многострочных цепочек
  вызова методов: точка перед вызовом метода в начале строки.
  <sup>[[ссылка](#consistent-multi-line-chains)]</sup>

    ```Ruby
    # плохо - нужно посмотреть на предыдущую строку, чтобы понять
    # смысл последующей
    one.two.three.
      four

    # хорошо - сразу ясно, что происходит во второй строке
    one.two.three
       .four
    ```

1.15. <a name="no-double-indent"></a> Выравнивайте параметры вызова метода, если
  вызов занимает более одной строки. Если выравнивание невозможно из-за
  ограничений на длину строки, то используйте одинарный отступ.
  <sup>[[ссылка](#no-double-indent)]</sup>

  ```Ruby
  # первоначальный вариант (строка слишком длинная)
  def send_mail(source)
    Mailer.deliver(to: 'bob@example.com', from: 'us@example.com', subject: 'Important message', body: source.text)
  end

  # плохо (двойной отступ)
  def send_mail(source)
    Mailer.deliver(
        to: 'bob@example.com',
        from: 'us@example.com',
        subject: 'Important message',
        body: source.text)
  end

  # хорошо
  def send_mail(source)
    Mailer.deliver(to: 'bob@example.com',
                   from: 'us@example.com',
                   subject: 'Important message',
                   body: source.text)
  end

  # хорошо (одинарный отступ)
  def send_mail(source)
    Mailer.deliver(
      to: 'bob@example.com',
      from: 'us@example.com',
      subject: 'Important message',
      body: source.text
    )
  end
  ```

1.16. <a name="align-multiline-arrays"></a>
  Выравнивайте элементы литералов массива,
  если они занимают несколько строк.
  <sup>[[ссылка](#align-multiline-arrays)]</sup>

  ```Ruby
  # плохо
  menu_item = ['Spam', 'Spam', 'Spam', 'Spam', 'Spam', 'Spam', 'Spam', 'Spam',
    'Baked beans', 'Spam', 'Spam', 'Spam', 'Spam', 'Spam']

  # плохо
  menu_item =
    ['Spam', 'Spam', 'Spam', 'Spam', 'Spam', 'Spam', 'Spam', 'Spam',
     'Baked beans', 'Spam', 'Spam', 'Spam', 'Spam', 'Spam']

  # ок
  menu_item = [
    'Spam', 'Spam', 'Spam', 'Spam', 'Spam', 'Spam', 'Spam', 'Spam',
    'Baked beans', 'Spam', 'Spam', 'Spam', 'Spam', 'Spam'
  ]

  # хорошо
  menu_item = ['Spam', 'Spam', 'Spam', 'Spam', 'Spam', 'Spam', 'Spam', 'Spam',
               'Baked beans', 'Spam', 'Spam', 'Spam', 'Spam', 'Spam']
  ```

1.17. <a name="rdoc-conventions"></a>
  Используйте устоявшиеся правила RDoc для описания интерфейсов. Не отделяйте
  блок комментария от начала определения метода `def` пустой строкой.

  ```Ruby
  # Configuration options are:
  #
  # * +column+ - specifies the column name to use for keeping the position integer (default: +position+)
  # * +scope+ - restricts what is to be considered a list. Given a symbol, it'll attach <tt>_id</tt>
  #   (if it hasn't already been added) and use that as the foreign key restriction. It's also possible
  #   to give it an entire string that is interpolated if you need a tighter scope than just a foreign key.
  #   Example: <tt>acts_as_list :scope => 'todo_list_id = #{todo_list_id} AND completed = 0'</tt>
  def acts_as_list(options = {})
    ...
  ```

  <sup>[[ссылка](#rdoc-conventions)]</sup>

1.18. <a name="no-trailing-whitespace"></a>
  Не оставляйте пробелы в конце строки.
  <sup>[[ссылка](#no-trailing-whitespace)]</sup>

1.19. <a name="no-block-comments"></a>
  Не пользуйтесь блочными комментариями. Их нельзя разместить на необходимом уровне отступа.
  К тому же они не так читаемы, как обычные.
  <sup>[[ссылка](#no-block-comments)]</sup>

  ```Ruby
  # плохо
  =begin
  строка комментария
  еще одна строка комментария
  =end

  # хорошо
  # строка комментария
  # другая строка комментария
  ```

## Syntax

2.1. <a name="double-colons"></a>
  Используйте `::` только для обращения к константам (в том числе к классам и модулям)
  и конструкторам класса (например, `Array()` или `Nokogiri::HTML()`).
  Никогда не используйте `::` для вызова методов.
  <sup>[[ссылка](#double-colons)]</sup>

  ```Ruby
  # плохо
  SomeClass::some_method
  some_object::some_method

  # хорошо
  SomeClass.some_method
  some_object.some_method
  SomeModule::SomeClass::SOME_CONST
  SomeModule::SomeClass()
  ```

2.2. <a name="no-then"></a>
  Не используйте `then` для условных операторов `if/unless`, если они не однострочные.
  <sup>[[ссылка](#no-then)]</sup>

  ```Ruby
  # плохо
  if some_condition then
    # некоторое действие
  end

  # хорошо
  if some_condition
    # некоторое действие
  end

  # хорошо
  if simple_condition then return; end
  ```

2.3. <a name="same-line-condition"></a> Всегда записывайте условие для `if/unless`
  на той же строке, что и сам оператор `if/then` в многострочном условии.
  <sup>[[ссылка](#same-line-condition)]</sup>

  ```Ruby
  # плохо
  if
    some_condition
    do_something
    do_something_else
  end

  # хорошо
  if some_condition
    do_something
    do_something_else
  end
  ```

2.4. <a name="ternary-operator"></a> Предпочитайте тернарный оператор (`?:`)
  однострочным конструкциям с `if/then/else/end`, он короче.
  <sup>[[ссылка](#ternary-operator)]</sup>

  ```Ruby
  # плохо
  result = if some_condition then something else something_else end

  # хорошо
  result = some_condition ? something : something_else
  ```

2.5. <a name="no-nested-ternary"></a>
  Используйте только одно выражение в каждой
  ветви тернарного оператора. Отсюда следует, что лучше избегать вложенных
  тернарных операторов. При возникновении такой необходимости применяйте
  конструкции с `if/else`.
  <sup>[[ссылка](#no-nested-ternary)]</sup>

  ```Ruby
  # плохо
  some_condition ? (nested_condition ? nested_something : nested_something_else) : something_else

  # хорошо
  if some_condition
    nested_condition ? nested_something : nested_something_else
  else
    something_else
  end
  ```

2.6. <a name="use-if-case-returns"></a>
  Не используйте присваивание на блок 'if'.
  <sup>[[ссылка](#use-if-case-returns)]</sup>

  ```Ruby
  # хорошо
  if condition
    result = x
  else
    result = y
  end

  # плохо
  result =
    if condition
      x
    else
      y
    end
  ```

2.7. <a name="bang-not-not"></a>
  Используйте `!` вместо `not`.
  <sup>[[ссылка](#bang-not-not)]</sup>

  ```Ruby
  # плохо (необходимы скобки из-за неоднозначности приоритетов операторов)
  x = (not something)

  # хорошо
  x = !something
  ```

2.8. <a name="no-bang-bang"></a>
  Не используйте `!!`.
  <sup>[[ссылка](#no-bang-bang)]</sup>

  ```Ruby
  # плохо
  x = 'test'
  # неявная проверка на nil
  if !!x
    # некоторое выражение
  end

  x = false
  # двойное отрицание бессмысленно для булевых значений
  !!x # => false

  # хорошо
  x = 'test'
  if x.present?
    # некоторое выражение
  end
  ```

2.9. <a name="no-and-or-or"></a>
  Не используйте ключевые слова `and` и `or` в условиях, т.к. они отличаются по
  приоритету от '&&' и '||'.
  Всегда используйте `&&` и `||`.
  <sup>[[ссылка](#no-and-or-or)]</sup>

  ```Ruby
  # плохо

  # булево выражение
  if some_condition and some_other_condition
    do_something
  end

  # управление потоком исполнения
  document.saved? or document.save!



  # хорошо

  # булево выражение
  if some_condition && some_other_condition
    do_something
  end

  # управление потоком исполнения
  document.saved? || document.save!
  ```

2.10. <a name="no-multiline-ternary"></a>
  Избегайте многострочных тернарных операторов `?:`. Используйте вместо них `if/unless`.
  <sup>[[ссылка](#no-multiline-ternary)]</sup>

2.11. <a name="no-multiline-if-modifiers"></a>
  Избегайте `if/unless` в конце нетривиального многострочного блока.
  <sup>[[ссылка](#no-multiline-if-modifiers)]</sup>

  ```Ruby
  # плохо
  10.times do
    # multi-line body omitted
  end if some_condition

  # хорошо
  if some_condition
    10.times do
      # multi-line body omitted
    end
  end
  ```

2.12. <a name="unless-for-negatives"></a>
  Используйте `unless` для простых условий с отрицанием и `if` c `!` для сложных.
  Используйте `||` для управления потоком исполнения.
  <sup>[[ссылка](#unless-for-negatives)]</sup>

  ```Ruby
  # плохо
  do_something if !some_condition

  # плохо
  do_something if not some_condition

  # хорошо
  do_something unless some_condition

  # тоже хорошо
  some_condition || do_something
  ```

2.13. <a name="no-else-with-unless"></a>
  Не используйте `unless` вместе с `else`.
  Перепишите такие выражение с положительной проверкой.
  <sup>[[ссылка](#no-else-with-unless)]</sup>

  ```Ruby
  # плохо
  unless success?
    puts 'failure'
  else
    puts 'success'
  end

  # хорошо
  if success?
    puts 'success'
  else
    puts 'failure'
  end
  ```

2.14. <a name="no-dsl-parens"></a>
  Не используйте скобки при вызове методов,
  являющихся частью таких DSL, как Rake, Rails, RSpec, методов, имеющих
  статус ключевого слова, например, `attr_reader`, `puts` и при вызове
  аксессоров. Используйте скобки при вызове прочих методов.
  <sup>[[ссылка](#no-dsl-parens)]</sup>

  ```Ruby
  class Person
    attr_reader :name, :age

  end

  temperance = Person.new('Temperance', 30)
  temperance.name

  puts temperance.age

  x = Math.sin(y)
  array.delete(e)

  bowling.score.should == 0
  ```

2.15. <a name="no-dsl-decorating"></a>
  Не используйте фигурные скобки для ограничения хешей,
  передаваемых методу, и скобки вокруг параметров для методов,
  являющихся частью DSL.
  <sup>[[ссылка](#no-dsl-decorating)]</sup>

  ```Ruby
  class Person < ActiveRecord::Base
    # плохо
    validates(:name, { presence: true, length: { within: 1..10 } })

    # хорошо
    validates :name, presence: true, length: { within: 1..10 }
  end
  ```

2.16. <a name="single-line-blocks"></a>
  Используйте преимущественно `{...}` в случае
  одностроных блоков, а `do...end` в случае многострочных блоков (многострочные
  последовательности вызовов методов всегда выглядят ужасно). Старайтесь
  применять `do...end` для логических операций и определений методов (например,
  для Rakefile и некоторых DSL). Не используйте `do...end` в цепочках вызовов.
  <sup>[[ссылка](#single-line-blocks)]</sup>

  ```Ruby
  names = ['Bozhidar', 'Steve', 'Sarah']

  # ок
  names.each do |name|
    puts name
  end

  # хорошо
  names.each { |name| puts name }

  # плохо
  names.select do |name|
    name.start_with?('S')
  end.map { |name| name.upcase }

  # хорошо
  names.select { |name| name.start_with?('S') }.map { |name| name.upcase }
  ```

2.17. <a name="no-explicit-return"></a>
  Избегайте `return` там, где это не требуется для управления потоком выполнения.
  <sup>[[link](#no-explicit-return)]</sup>

  ```Ruby
  # плохо
  def some_method(some_arr)
    return some_arr.size
  end

  # хорошо
  def some_method(some_arr)
    some_arr.size
  end
  ```

2.18. <a name="no-self-unless-required"></a>
  Используйте self для доступа к атрибутам модели в методах экземпляра класса
  <sup>[[link](#no-self-unless-required)]</sup>

  ```Ruby
  # плохо
  def ready?
    if last_reviewed_at > last_updated_at
      worker.update(content, options)
      self.status = :in_progress
    end
    status == :verified
  end

  # хорошо
  def ready?
    if self.last_reviewed_at > self.last_updated_at
      self.worker.update(self.content, self.options)
      self.status = :in_progress
    end
    self.status == :verified
  end
  ```

2.19. <a name="safe-assignment-in-condition"></a>
  Не используйте значения, возвращаемые оператором присваивания `=` в условных выражениях.
<sup>[[link](#safe-assignment-in-condition)]</sup>

  ```Ruby
  # bad (+ a warning)
  if v = array.grep(/foo/)
    do_something(v)
    ...
  end

  # bad
  if (v = array.grep(/foo/))
    do_something(v)
    ...
  end

  # good
  v = array.grep(/foo/)
  if v
    do_something(v)
    ...
  end
  ```

2.20. <a name="double-pipe-for-uninit"></a>
   Используйте `||=` для инициализации непроинициализированных переменных.
<sup>[[link](#double-pipe-for-uninit)]</sup>

  ```Ruby
  # плохо
  name = name ? name : 'Bozhidar'

  # плохо
  name = 'Bozhidar' unless name

  # хорошо - присваивание происходит только, если name - nil или false
  name ||= 'Bozhidar'
  ```

2.21. <a name="no-double-pipes-for-bools"></a>
  Не используйте `||=` для инициализации булевых переменных.
<sup>[[link](#no-double-pipes-for-bools)]</sup>

  ```Ruby
  # плохо - присвоит true даже если переменная была false
  enabled ||= true

  # хорошо
  enabled = true if enabled.nil?
  ```

* <a name="double-amper-preprocess"></a>
  Use `&&=` to preprocess variables that may or may not exist. Using `&&=`
  will change the value only if it exists, removing the need to check its
  existence with `if`.
<sup>[[link](#double-amper-preprocess)]</sup>

  ```Ruby
  # bad
  if something
    something = something.downcase
  end

  # bad
  something = something ? something.downcase : nil

  # ok
  something = something.downcase if something

  # good
  something = something && something.downcase

  # better
  something &&= something.downcase
  ```

* <a name="no-case-equality"></a>
  Avoid explicit use of the case equality operator `===`. As its name implies
  it is meant to be used implicitly by `case` expressions and outside of them it
  yields some pretty confusing code.
<sup>[[link](#no-case-equality)]</sup>

  ```Ruby
  # bad
  Array === something
  (1..100) === 7
  /something/ === some_string

  # good
  something.is_a?(Array)
  (1..100).include?(7)
  some_string =~ /something/
  ```

* <a name="no-cryptic-perlisms"></a>
  Avoid using Perl-style special variables (like `$:`, `$;`, etc. ). They are
  quite cryptic and their use in anything but one-liner scripts is discouraged.
  Use the human-friendly aliases provided by the `English` library.
<sup>[[link](#no-cryptic-perlisms)]</sup>

  ```Ruby
  # bad
  $:.unshift File.dirname(__FILE__)

  # good
  require 'English'
  $LOAD_PATH.unshift File.dirname(__FILE__)
  ```

* <a name="parens-no-spaces"></a>
  Never put a space between a method name and the opening parenthesis.
<sup>[[link](#parens-no-spaces)]</sup>

  ```Ruby
  # bad
  f (3 + 2) + 1

  # good
  f(3 + 2) + 1
  ```

* <a name="parens-as-args"></a>
  If the first argument to a method begins with an open parenthesis, always
  use parentheses in the method invocation. For example, write `f((3 + 2) + 1)`.
<sup>[[link](#parens-as-args)]</sup>

* <a name="always-warn-at-runtime"></a>
  Always run the Ruby interpreter with the `-w` option so it will warn you if
  you forget either of the rules above!
<sup>[[link](#always-warn-at-runtime)]</sup>

* <a name="lambda-multi-line"></a>
  Use the new lambda literal syntax for single line body blocks. Use the
  `lambda` method for multi-line blocks.
<sup>[[link](#lambda-multi-line)]</sup>

  ```Ruby
  # bad
  l = lambda { |a, b| a + b }
  l.call(1, 2)

  # correct, but looks extremely awkward
  l = ->(a, b) do
    tmp = a * 7
    tmp * b / 50
  end

  # good
  l = ->(a, b) { a + b }
  l.call(1, 2)

  l = lambda do |a, b|
    tmp = a * 7
    tmp * b / 50
  end
  ```

* <a name="proc"></a>
  Prefer `proc` over `Proc.new`.
<sup>[[link](#proc)]</sup>

  ```Ruby
  # bad
  p = Proc.new { |n| puts n }

  # good
  p = proc { |n| puts n }
  ```

* <a name="proc-call"></a>
  Prefer `proc.call()` over `proc[]` or `proc.()` for both lambdas and procs.
<sup>[[link](#proc-call)]</sup>

  ```Ruby
  # bad - looks similar to Enumeration access
  l = ->(v) { puts v }
  l[1]

  # also bad - uncommon syntax
  l = ->(v) { puts v }
  l.(1)

  # good
  l = ->(v) { puts v }
  l.call(1)
  ```

* <a name="underscore-unused-vars"></a>
  Prefix with `_` unused block parameters and local variables. It's also
  acceptable to use just `_` (although it's a bit less descriptive). This
  convention is recognized by the Ruby interpreter and tools like RuboCop and
  will suppress their unused variable warnings.
<sup>[[link](#underscore-unused-vars)]</sup>

  ```Ruby
  # bad
  result = hash.map { |k, v| v + 1 }

  def something(x)
    unused_var, used_var = something_else(x)
    # ...
  end

  # good
  result = hash.map { |_k, v| v + 1 }

  def something(x)
    _unused_var, used_var = something_else(x)
    # ...
  end

  # good
  result = hash.map { |_, v| v + 1 }

  def something(x)
    _, used_var = something_else(x)
    # ...
  end
  ```

* <a name="global-stdout"></a>
  Use `$stdout/$stderr/$stdin` instead of `STDOUT/STDERR/STDIN`.
  `STDOUT/STDERR/STDIN` are constants, and while you can actually reassign
  (possibly to redirect some stream) constants in Ruby, you'll get an
  interpreter warning if you do so.
<sup>[[link](#global-stdout)]</sup>

* <a name="warn"></a>
  Use `warn` instead of `$stderr.puts`. Apart from being more concise and
  clear, `warn` allows you to suppress warnings if you need to (by setting the
  warn level to 0 via `-W0`).
<sup>[[link](#warn)]</sup>

* <a name="sprintf"></a>
  Favor the use of `sprintf` and its alias `format` over the fairly cryptic
  `String#%` method.
<sup>[[link](#sprintf)]</sup>

  ```Ruby
  # bad
  '%d %d' % [20, 10]
  # => '20 10'

  # good
  sprintf('%d %d', 20, 10)
  # => '20 10'

  # good
  sprintf('%{first} %{second}', first: 20, second: 10)
  # => '20 10'

  format('%d %d', 20, 10)
  # => '20 10'

  # good
  format('%{first} %{second}', first: 20, second: 10)
  # => '20 10'
  ```

* <a name="array-join"></a>
  Favor the use of `Array#join` over the fairly cryptic `Array#*` with
<sup>[[link](#array-join)]</sup>
  a string argument.

  ```Ruby
  # bad
  %w(one two three) * ', '
  # => 'one, two, three'

  # good
  %w(one two three).join(', ')
  # => 'one, two, three'
  ```

* <a name="splat-arrays"></a>
  Use `[*var]` or `Array()` instead of explicit `Array` check, when dealing
  with a variable you want to treat as an Array, but you're not certain it's an
  array.
<sup>[[link](#splat-arrays)]</sup>

  ```Ruby
  # bad
  paths = [paths] unless paths.is_a? Array
  paths.each { |path| do_something(path) }

  # good
  [*paths].each { |path| do_something(path) }

  # good (and a bit more readable)
  Array(paths).each { |path| do_something(path) }
  ```

* <a name="ranges-or-between"></a>
  Use ranges or `Comparable#between?` instead of complex comparison logic when
  possible.
<sup>[[link](#ranges-or-between)]</sup>

  ```Ruby
  # bad
  do_something if x >= 1000 && x <= 2000

  # good
  do_something if (1000..2000).include?(x)

  # good
  do_something if x.between?(1000, 2000)
  ```

* <a name="predicate-methods"></a>
  Favor the use of predicate methods to explicit comparisons with `==`.
  Numeric comparisons are OK.
<sup>[[link](#predicate-methods)]</sup>

  ```Ruby
  # bad
  if x % 2 == 0
  end

  if x % 2 == 1
  end

  if x == nil
  end

  # good
  if x.even?
  end

  if x.odd?
  end

  if x.nil?
  end

  if x.zero?
  end

  if x == 0
  end
  ```

* <a name="no-non-nil-checks"></a>
  Don't do explicit non-`nil` checks unless you're dealing with boolean
  values.
<sup>[[link](#no-non-nil-checks)]</sup>

    ```ruby
    # bad
    do_something if !something.nil?
    do_something if something != nil

    # good
    do_something if something

    # good - dealing with a boolean
    def value_set?
      !@some_boolean.nil?
    end
    ```

* <a name="no-BEGIN-blocks"></a>
  Avoid the use of `BEGIN` blocks.
<sup>[[link](#no-BEGIN-blocks)]</sup>

* <a name="no-END-blocks"></a>
  Never use `END` blocks. Use `Kernel#at_exit` instead.
<sup>[[link](#no-END-blocks)]</sup>

  ```ruby
  # bad
  END { puts 'Goodbye!' }

  # good
  at_exit { puts 'Goodbye!' }
  ```

* <a name="no-flip-flops"></a>
  Avoid the use of flip-flops.
<sup>[[link](#no-flip-flops)]</sup>

* <a name="no-nested-conditionals"></a>
  Avoid use of nested conditionals for flow of control.
<sup>[[link](#no-nested-conditionals)]</sup>

  Prefer a guard clause when you can assert invalid data. A guard clause
  is a conditional statement at the top of a function that bails out as
  soon as it can.

  ```Ruby
  # bad
  def compute_thing(thing)
    if thing[:foo]
      update_with_bar(thing)
      if thing[:foo][:bar]
        partial_compute(thing)
      else
        re_compute(thing)
      end
    end
  end

  # good
  def compute_thing(thing)
    return unless thing[:foo]
    update_with_bar(thing[:foo])
    return re_compute(thing) unless thing[:foo][:bar]
    partial_compute(thing)
  end
  ```

  Prefer `next` in loops instead of conditional blocks.

  ```Ruby
  # bad
  [0, 1, 2, 3].each do |item|
    if item > 1
      puts item
    end
  end

  # good
  [0, 1, 2, 3].each do |item|
    next unless item > 1
    puts item
  end
  ```

## Naming

> The only real difficulties in programming are cache invalidation and
> naming things. <br/>
> -- Phil Karlton

* <a name="english-identifiers"></a>
  Name identifiers in English.
<sup>[[link](#english-identifiers)]</sup>

  ```Ruby
  # bad - identifier using non-ascii characters
  заплата = 1_000

  # bad - identifier is a Bulgarian word, written with Latin letters (instead of Cyrillic)
  zaplata = 1_000

  # good
  salary = 1_000
  ```

* <a name="snake-case-symbols-methods-vars"></a>
  Use `snake_case` for symbols, methods and variables.
<sup>[[link](#snake-case-symbols-methods-vars)]</sup>

  ```Ruby
  # bad
  :'some symbol'
  :SomeSymbol
  :someSymbol

  someVar = 5

  def someMethod
    ...
  end

  def SomeMethod
   ...
  end

  # good
  :some_symbol

  def some_method
    ...
  end
  ```

* <a name="camelcase-classes"></a>
  Use `CamelCase` for classes and modules.  (Keep acronyms like HTTP, RFC, XML
  uppercase.)
<sup>[[link](#camelcase-classes)]</sup>

  ```Ruby
  # bad
  class Someclass
    ...
  end

  class Some_Class
    ...
  end

  class SomeXml
    ...
  end

  # good
  class SomeClass
    ...
  end

  class SomeXML
    ...
  end
  ```

* <a name="snake-case-files"></a>
  Use `snake_case` for naming files, e.g. `hello_world.rb`.
<sup>[[link](#snake-case-files)]</sup>

* <a name="snake-case-dirs"></a>
  Use `snake_case` for naming directories, e.g.
  `lib/hello_world/hello_world.rb`.
<sup>[[link](#snake-case-dirs)]</sup>

* <a name="one-class-per-file"></a>
  Aim to have just a single class/module per source file. Name the file name
  as the class/module, but replacing CamelCase with snake_case.
<sup>[[link](#one-class-per-file)]</sup>

* <a name="screaming-snake-case"></a>
  Use `SCREAMING_SNAKE_CASE` for other constants.
<sup>[[link](#screaming-snake-case)]</sup>

  ```Ruby
  # bad
  SomeConst = 5

  # good
  SOME_CONST = 5
  ```

* <a name="bool-methods-qmark"></a>
  The names of predicate methods (methods that return a boolean value) should
  end in a question mark.  (i.e. `Array#empty?`). Methods that don't return a
  boolean, shouldn't end in a question mark.
<sup>[[link](#bool-methods-qmark)]</sup>

* <a name="dangerous-method-bang"></a>
  The names of potentially *dangerous* methods (i.e. methods that modify
  `self` or the arguments, `exit!` (doesn't run the finalizers like `exit`
  does), etc.) should end with an exclamation mark if there exists a safe
  version of that *dangerous* method.
<sup>[[link](#dangerous-method-bang)]</sup>

  ```Ruby
  # bad - there is no matching 'safe' method
  class Person
    def update!
    end
  end

  # good
  class Person
    def update
    end
  end

  # good
  class Person
    def update!
    end

    def update
    end
  end
  ```

* <a name="safe-because-unsafe"></a>
  Define the non-bang (safe) method in terms of the bang (dangerous) one if
  possible.
<sup>[[link](#safe-because-unsafe)]</sup>

  ```Ruby
  class Array
    def flatten_once!
      res = []

      each do |e|
        [*e].each { |f| res << f }
      end

      replace(res)
    end

    def flatten_once
      dup.flatten_once!
    end
  end
  ```

* <a name="reduce-blocks"></a>
  When using `reduce` with short blocks, name the arguments `|a, e|`
  (accumulator, element).
<sup>[[link](#reduce-blocks)]</sup>

* <a name="other-arg"></a>
  When defining binary operators, name the argument `other`(`<<` and `[]` are
  exceptions to the rule, since their semantics are different).
<sup>[[link](#other-arg)]</sup>

  ```Ruby
  def +(other)
    # body omitted
  end
  ```

* <a name="map-fine-select-reduce-size"></a>
  Prefer `map` over `collect`, `find` over `detect`, `select` over `find_all`,
  `reduce` over `inject` and `size` over `length`. This is not a hard
  requirement; if the use of the alias enhances readability, it's ok to use it.
  The rhyming methods are inherited from Smalltalk and are not common in other
  programming languages. The reason the use of `select` is encouraged over
  `find_all` is that it goes together nicely with `reject` and its name is
  pretty self-explanatory.
<sup>[[link](#map-fine-select-reduce-size)]</sup>

* <a name="count-vs-size"></a>
  Don't use `count` as a substitute for `size`. For `Enumerable` objects other
  than `Array` it will iterate the entire collection in order to determine its
  size.
<sup>[[link](#count-vs-size)]</sup>

  ```Ruby
  # bad
  some_hash.count

  # good
  some_hash.size
  ```

* <a name="flat-map"></a>
  Use `flat_map` instead of `map` + `flatten`.  This does not apply for arrays
  with a depth greater than 2, i.e.  if `users.first.songs == ['a', ['b','c']]`,
  then use `map + flatten` rather than `flat_map`.  `flat_map` flattens the
  array by 1, whereas `flatten` flattens it all the way.
<sup>[[link](#flat-map)]</sup>

  ```Ruby
  # bad
  all_songs = users.map(&:songs).flatten.uniq

  # good
  all_songs = users.flat_map(&:songs).uniq
  ```

* <a name="reverse-each"></a>
  Use `reverse_each` instead of `reverse.each`. `reverse_each` doesn't do a
  new array allocation and that's a good thing.
<sup>[[link](#reverse-each)]</sup>

  ```Ruby
  # bad
  array.reverse.each { ... }

  # good
  array.reverse_each { ... }
  ```

## Comments

> Good code is its own best documentation. As you're about to add a
> comment, ask yourself, "How can I improve the code so that this
> comment isn't needed?" Improve the code and then document it to make
> it even clearer. <br/>
> -- Steve McConnell

* <a name="no-comments"></a>
  Write self-documenting code and ignore the rest of this section. Seriously!
<sup>[[link](#no-comments)]</sup>

* <a name="english-comments"></a>
  Write comments in English.
<sup>[[link](#english-comments)]</sup>

* <a name="hash-space"></a>
  Use one space between the leading `#` character of the comment and the text
  of the comment.
<sup>[[link](#hash-space)]</sup>

* <a name="english-syntax"></a>
  Comments longer than a word are capitalized and use punctuation. Use [one
  space](http://en.wikipedia.org/wiki/Sentence_spacing) after periods.
<sup>[[link](#english-syntax)]</sup>

* <a name="no-superfluous-comments"></a>
  Avoid superfluous comments.
<sup>[[link](#no-superfluous-comments)]</sup>

  ```Ruby
  # bad
  counter += 1 # Increments counter by one.
  ```

* <a name="comment-upkeep"></a>
  Keep existing comments up-to-date. An outdated comment is worse than no
  comment at all.
<sup>[[link](#comment-upkeep)]</sup>

> Good code is like a good joke - it needs no explanation. <br/>
> -- Russ Olsen

* <a name="refactor-dont-comment"></a>
  Avoid writing comments to explain bad code. Refactor the code to make it
  self-explanatory. (Do or do not - there is no try. --Yoda)
<sup>[[link](#refactor-dont-comment)]</sup>

### Comment Annotations

* <a name="annotate-above"></a>
  Annotations should usually be written on the line immediately above the
  relevant code.
<sup>[[link](#annotate-above)]</sup>

* <a name="annotate-keywords"></a>
  The annotation keyword is followed by a colon and a space, then a note
  describing the problem.
<sup>[[link](#annotate-keywords)]</sup>

* <a name="indent-annotations"></a>
  If multiple lines are required to describe the problem, subsequent lines
  should be indented two spaces after the `#`.
<sup>[[link](#indent-annotations)]</sup>

  ```Ruby
  def bar
    # FIXME: This has crashed occasionally since v3.2.1. It may
    #   be related to the BarBazUtil upgrade.
    baz(:quux)
  end
  ```

* <a name="rare-eol-annotations"></a>
  In cases where the problem is so obvious that any documentation would be
  redundant, annotations may be left at the end of the offending line with no
  note. This usage should be the exception and not the rule.
<sup>[[link](#rare-eol-annotations)]</sup>

  ```Ruby
  def bar
    sleep 100 # OPTIMIZE
  end
  ```

* <a name="todo"></a>
  Use `TODO` to note missing features or functionality that should be added at
  a later date.
<sup>[[link](#todo)]</sup>

* <a name="fixme"></a>
  Use `FIXME` to note broken code that needs to be fixed.
<sup>[[link](#fixme)]</sup>

* <a name="optimize"></a>
  Use `OPTIMIZE` to note slow or inefficient code that may cause performance
  problems.
<sup>[[link](#optimize)]</sup>

* <a name="hack"></a>
  Use `HACK` to note code smells where questionable coding practices were used
  and should be refactored away.
<sup>[[link](#hack)]</sup>

* <a name="review"></a>
  Use `REVIEW` to note anything that should be looked at to confirm it is
  working as intended. For example: `REVIEW: Are we sure this is how the client
  does X currently?`
<sup>[[link](#review)]</sup>

* <a name="document-annotations"></a>
  Use other custom annotation keywords if it feels appropriate, but be sure to
  document them in your project's `README` or similar.
<sup>[[link](#document-annotations)]</sup>

## Classes & Modules

* <a name="consistent-classes"></a>
  Use a consistent structure in your class definitions.
<sup>[[link](#consistent-classes)]</sup>

  ```Ruby
  class Person
    # extend and include go first
    extend SomeModule
    include AnotherModule

    # inner classes
    CustomErrorKlass = Class.new(StandardError)

    # constants are next
    SOME_CONSTANT = 20

    # afterwards we have attribute macros
    attr_reader :name

    # followed by other macros (if any)
    validates :name

    # public class methods are next in line
    def self.some_method
    end

    # followed by public instance methods
    def some_method
    end

    # protected and private methods are grouped near the end
    protected

    def some_protected_method
    end

    private

    def some_private_method
    end
  end
  ```

* <a name="file-classes"></a>
  Don't nest multi line classes within classes. Try to have such nested
  classes each in their own file in a folder named like the containing class.
<sup>[[link](#file-classes)]</sup>

  ```Ruby
  # bad

  # foo.rb
  class Foo
    class Bar
      # 30 methods inside
    end

    class Car
      # 20 methods inside
    end

    # 30 methods inside
  end

  # good

  # foo.rb
  class Foo
    # 30 methods inside
  end

  # foo/bar.rb
  class Foo
    class Bar
      # 30 methods inside
    end
  end

  # foo/car.rb
  class Foo
    class Car
      # 20 methods inside
    end
  end
  ```

* <a name="modules-vs-classes"></a>
  Prefer modules to classes with only class methods. Classes should be used
  only when it makes sense to create instances out of them.
<sup>[[link](#modules-vs-classes)]</sup>

  ```Ruby
  # bad
  class SomeClass
    def self.some_method
      # body omitted
    end

    def self.some_other_method
    end
  end

  # good
  module SomeClass
    module_function

    def some_method
      # body omitted
    end

    def some_other_method
    end
  end
  ```

* <a name="module-function"></a>
  Favor the use of `module_function` over `extend self` when you want to turn
  a module's instance methods into class methods.
<sup>[[link](#module-function)]</sup>

  ```Ruby
  # bad
  module Utilities
    extend self

    def parse_something(string)
      # do stuff here
    end

    def other_utility_method(number, string)
      # do some more stuff
    end
  end

  # good
  module Utilities
    module_function

    def parse_something(string)
      # do stuff here
    end

    def other_utility_method(number, string)
      # do some more stuff
    end
  end
  ```

* <a name="liskov"></a>
  When designing class hierarchies make sure that they conform to the [Liskov
  Substitution
  Principle](http://en.wikipedia.org/wiki/Liskov_substitution_principle).
<sup>[[link](#liskov)]</sup>

* <a name="solid-design"></a>
  Try to make your classes as
  [SOLID](http://en.wikipedia.org/wiki/SOLID_\(object-oriented_design\)) as
  possible.
<sup>[[link](#solid-design)]</sup>

* <a name="define-to-s"></a>
  Always supply a proper `to_s` method for classes that represent domain
  objects.
<sup>[[link](#define-to-s)]</sup>

  ```Ruby
  class Person
    attr_reader :first_name, :last_name

    def initialize(first_name, last_name)
      @first_name = first_name
      @last_name = last_name
    end

    def to_s
      "#{@first_name} #{@last_name}"
    end
  end
  ```

* <a name="attr_family"></a>
  Use the `attr` family of functions to define trivial accessors or mutators.
<sup>[[link](#attr_family)]</sup>

  ```Ruby
  # bad
  class Person
    def initialize(first_name, last_name)
      @first_name = first_name
      @last_name = last_name
    end

    def first_name
      @first_name
    end

    def last_name
      @last_name
    end
  end

  # good
  class Person
    attr_reader :first_name, :last_name

    def initialize(first_name, last_name)
      @first_name = first_name
      @last_name = last_name
    end
  end
  ```

* <a name="attr"></a>
  Avoid the use of `attr`. Use `attr_reader` and `attr_accessor` instead.
<sup>[[link](#attr)]</sup>

  ```Ruby
  # bad - creates a single attribute accessor (deprecated in 1.9)
  attr :something, true
  attr :one, :two, :three # behaves as attr_reader

  # good
  attr_accessor :something
  attr_reader :one, :two, :three
  ```

* <a name="struct-new"></a>
  Consider using `Struct.new`, which defines the trivial accessors,
  constructor and comparison operators for you.
<sup>[[link](#struct-new)]</sup>

  ```Ruby
  # good
  class Person
    attr_accessor :first_name, :last_name

    def initialize(first_name, last_name)
      @first_name = first_name
      @last_name = last_name
    end
  end

  # better
  Person = Struct.new(:first_name, :last_name) do
  end
  ````

* <a name="no-extend-struct-new"></a>
  Don't extend a `Struct.new` - it already is a new class. Extending it
  introduces a superfluous class level and may also introduce weird errors if
  the file is required multiple times.
<sup>[[link](#no-extend-struct-new)]</sup>

* <a name="factory-methods"></a>
  Consider adding factory methods to provide additional sensible ways to
  create instances of a particular class.
<sup>[[link](#factory-methods)]</sup>

  ```Ruby
  class Person
    def self.create(options_hash)
      # body omitted
    end
  end
  ```

* <a name="duck-typing"></a>
  Prefer [duck-typing](http://en.wikipedia.org/wiki/Duck_typing) over
  inheritance.
<sup>[[link](#duck-typing)]</sup>

  ```Ruby
  # bad
  class Animal
    # abstract method
    def speak
    end
  end

  # extend superclass
  class Duck < Animal
    def speak
      puts 'Quack! Quack'
    end
  end

  # extend superclass
  class Dog < Animal
    def speak
      puts 'Bau! Bau!'
    end
  end

  # good
  class Duck
    def speak
      puts 'Quack! Quack'
    end
  end

  class Dog
    def speak
      puts 'Bau! Bau!'
    end
  end
  ```

* <a name="no-class-vars"></a>
  Avoid the usage of class (`@@`) variables due to their "nasty" behavior in
  inheritance.
<sup>[[link](#no-class-vars)]</sup>

  ```Ruby
  class Parent
    @@class_var = 'parent'

    def self.print_class_var
      puts @@class_var
    end
  end

  class Child < Parent
    @@class_var = 'child'
  end

  Parent.print_class_var # => will print "child"
  ```

  As you can see all the classes in a class hierarchy actually share one
  class variable. Class instance variables should usually be preferred
  over class variables.

* <a name="visibility"></a>
  Assign proper visibility levels to methods (`private`, `protected`) in
  accordance with their intended usage. Don't go off leaving everything `public`
  (which is the default). After all we're coding in *Ruby* now, not in *Python*.
<sup>[[link](#visibility)]</sup>

* <a name="indent-public-private-protected"></a>
  Indent the `public`, `protected`, and `private` methods as much the method
  definitions they apply to. Leave one blank line above the visibility modifier
  and one blank line below in order to emphasize that it applies to all methods
  below it.
<sup>[[link](#indent-public-private-protected)]</sup>

  ```Ruby
  class SomeClass
    def public_method
      # ...
    end

    private

    def private_method
      # ...
    end

    def another_private_method
      # ...
    end
  end
  ```

* <a name="def-self-singletons"></a>
  Use `def self.method` to define singleton methods. This makes the code
  easier to refactor since the class name is not repeated.
<sup>[[link](#def-self-singletons)]</sup>

  ```Ruby
  class TestClass
    # bad
    def TestClass.some_method
      # body omitted
    end

    # good
    def self.some_other_method
      # body omitted
    end

    # Also possible and convenient when you
    # have to define many singleton methods.
    class << self
      def first_method
        # body omitted
      end

      def second_method_etc
        # body omitted
      end
    end
  end
  ```

## Exceptions

* <a name="fail-method"></a>
  Signal exceptions using the `fail` method. Use `raise` only when catching an
  exception and re-raising it (because here you're not failing, but explicitly
  and purposefully raising an exception).
<sup>[[link](#fail-method)]</sup>

  ```Ruby
  begin
    fail 'Oops'
  rescue => error
    raise if error.message != 'Oops'
  end
  ```

* <a name="no-explicit-runtimeerror"></a>
  Don't specify `RuntimeError` explicitly in the two argument version of
  `fail/raise`.
<sup>[[link](#no-explicit-runtimeerror)]</sup>

  ```Ruby
  # bad
  fail RuntimeError, 'message'

  # good - signals a RuntimeError by default
  fail 'message'
  ```

* <a name="exception-class-messages"></a>
  Prefer supplying an exception class and a message as two separate arguments
  to `fail/raise`, instead of an exception instance.
<sup>[[link](#exception-class-messages)]</sup>

  ```Ruby
  # bad
  fail SomeException.new('message')
  # Note that there is no way to do `fail SomeException.new('message'), backtrace`.

  # good
  fail SomeException, 'message'
  # Consistent with `fail SomeException, 'message', backtrace`.
  ```

* <a name="no-return-ensure"></a>
  Never return from an `ensure` block. If you explicitly return from a method
  inside an `ensure` block, the return will take precedence over any exception
  being raised, and the method will return as if no exception had been raised at
  all. In effect, the exception will be silently thrown away.
<sup>[[link](#no-return-ensure)]</sup>

  ```Ruby
  def foo
    begin
      fail
    ensure
      return 'very bad idea'
    end
  end
  ```

* <a name="begin-implicit"></a>
  Use *implicit begin blocks* where possible.
<sup>[[link](#begin-implicit)]</sup>

  ```Ruby
  # bad
  def foo
    begin
      # main logic goes here
    rescue
      # failure handling goes here
    end
  end

  # good
  def foo
    # main logic goes here
  rescue
    # failure handling goes here
  end
  ```

* <a name="contingency-methods"></a>
  Mitigate the proliferation of `begin` blocks by using *contingency methods*
  (a term coined by Avdi Grimm).
<sup>[[link](#contingency-methods)]</sup>

  ```Ruby
  # bad
  begin
    something_that_might_fail
  rescue IOError
    # handle IOError
  end

  begin
    something_else_that_might_fail
  rescue IOError
    # handle IOError
  end

  # good
  def with_io_error_handling
     yield
  rescue IOError
    # handle IOError
  end

  with_io_error_handling { something_that_might_fail }

  with_io_error_handling { something_else_that_might_fail }
  ```

* <a name="dont-hide-exceptions"></a>
  Don't suppress exceptions.
<sup>[[link](#dont-hide-exceptions)]</sup>

  ```Ruby
  # bad
  begin
    # an exception occurs here
  rescue SomeError
    # the rescue clause does absolutely nothing
  end

  # bad
  do_something rescue nil
  ```

* <a name="no-rescue-modifiers"></a>
  Avoid using `rescue` in its modifier form.
<sup>[[link](#no-rescue-modifiers)]</sup>

  ```Ruby
  # bad - this catches exceptions of StandardError class and its descendant classes
  read_file rescue handle_error($!)

  # good - this catches only the exceptions of Errno::ENOENT class and its descendant classes
  def foo
    read_file
  rescue Errno::ENOENT => ex
    handle_error(ex)
  end
  ```

* <a name="no-exceptional-flows"></a>
  Don't use exceptions for flow of control.
<sup>[[link](#no-exceptional-flows)]</sup>

  ```Ruby
  # bad
  begin
    n / d
  rescue ZeroDivisionError
    puts 'Cannot divide by 0!'
  end

  # good
  if d.zero?
    puts 'Cannot divide by 0!'
  else
    n / d
  end
  ```

* <a name="no-blind-rescues"></a>
  Avoid rescuing the `Exception` class.  This will trap signals and calls to
  `exit`, requiring you to `kill -9` the process.
<sup>[[link](#no-blind-rescues)]</sup>

  ```Ruby
  # bad
  begin
    # calls to exit and kill signals will be caught (except kill -9)
    exit
  rescue Exception
    puts "you didn't really want to exit, right?"
    # exception handling
  end

  # good
  begin
    # a blind rescue rescues from StandardError, not Exception as many
    # programmers assume.
  rescue => e
    # exception handling
  end

  # also good
  begin
    # an exception occurs here

  rescue StandardError => e
    # exception handling
  end
  ```

* <a name="exception-ordering"></a>
  Put more specific exceptions higher up the rescue chain, otherwise they'll
  never be rescued from.
<sup>[[link](#exception-ordering)]</sup>

  ```Ruby
  # bad
  begin
    # some code
  rescue Exception => e
    # some handling
  rescue StandardError => e
    # some handling that will never be executed
  end

  # good
  begin
    # some code
  rescue StandardError => e
    # some handling
  rescue Exception => e
    # some handling
  end
  ```

* <a name="file-close"></a>
  Release external resources obtained by your program in an ensure block.
<sup>[[link](#file-close)]</sup>

  ```Ruby
  f = File.open('testfile')
  begin
    # .. process
  rescue
    # .. handle error
  ensure
    f.close if f
  end
  ```

* <a name="standard-exceptions"></a>
  Favor the use of exceptions for the standard library over introducing new
  exception classes.
<sup>[[link](#standard-exceptions)]</sup>

## Collections

* <a name="literal-array-hash"></a>
  Prefer literal array and hash creation notation (unless you need to pass
  parameters to their constructors, that is).
<sup>[[link](#literal-array-hash)]</sup>

  ```Ruby
  # bad
  arr = Array.new
  hash = Hash.new

  # good
  arr = []
  hash = {}
  ```

* <a name="percent-w"></a>
  Prefer `%w` to the literal array syntax when you need an array of words
  (non-empty strings without spaces and special characters in them).  Apply this
  rule only to arrays with two or more elements.
<sup>[[link](#percent-w)]</sup>

  ```Ruby
  # bad
  STATES = ['draft', 'open', 'closed']

  # good
  STATES = %w(draft open closed)
  ```

* <a name="percent-i"></a>
  Prefer `%i` to the literal array syntax when you need an array of symbols
  (and you don't need to maintain Ruby 1.9 compatibility). Apply this rule only
  to arrays with two or more elements.
<sup>[[link](#percent-i)]</sup>

  ```Ruby
  # bad
  STATES = [:draft, :open, :closed]

  # good
  STATES = %i(draft open closed)
  ```

* <a name="no-trailing-array-commas"></a>
  Avoid comma after the last item of an `Array` or `Hash` literal, especially
  when the items are not on separate lines.
<sup>[[link](#no-trailing-array-commas)]</sup>

  ```Ruby
  # bad - easier to move/add/remove items, but still not preferred
  VALUES = [
             1001,
             2020,
             3333,
           ]

  # bad
  VALUES = [1001, 2020, 3333, ]

  # good
  VALUES = [1001, 2020, 3333]
  ```

* <a name="no-gappy-arrays"></a>
  Avoid the creation of huge gaps in arrays.
<sup>[[link](#no-gappy-arrays)]</sup>

  ```Ruby
  arr = []
  arr[100] = 1 # now you have an array with lots of nils
  ```

* <a name="first-and-last"></a>
  When accessing the first or last element from an array, prefer `first` or
  `last` over `[0]` or `[-1]`.
<sup>[[link](#first-and-last)]</sup>

* <a name="set-vs-array"></a>
  Use `Set` instead of `Array` when dealing with unique elements. `Set`
  implements a collection of unordered values with no duplicates. This is a
  hybrid of `Array`'s intuitive inter-operation facilities and `Hash`'s fast
  lookup.
<sup>[[link](#set-vs-array)]</sup>

* <a name="symbols-as-keys"></a>
  Prefer symbols instead of strings as hash keys.
<sup>[[link](#symbols-as-keys)]</sup>

  ```Ruby
  # bad
  hash = { 'one' => 1, 'two' => 2, 'three' => 3 }

  # good
  hash = { one: 1, two: 2, three: 3 }
  ```

* <a name="no-mutable-keys"></a>
  Avoid the use of mutable objects as hash keys.
<sup>[[link](#no-mutable-keys)]</sup>

* <a name="hash-literals"></a>
  Use the Ruby 1.9 hash literal syntax when your hash keys are symbols.
<sup>[[link](#hash-literals)]</sup>

  ```Ruby
  # bad
  hash = { :one => 1, :two => 2, :three => 3 }

  # good
  hash = { one: 1, two: 2, three: 3 }
  ```

* <a name="no-mixed-hash-syntaces"></a>
  Don't mix the Ruby 1.9 hash syntax with hash rockets in the same hash
  literal. When you've got keys that are not symbols stick to the hash rockets
  syntax.
<sup>[[link](#no-mixed-hash-syntaces)]</sup>

  ```Ruby
  # bad
  { a: 1, 'b' => 2 }

  # good
  { :a => 1, 'b' => 2 }
  ```

* <a name="hash-key"></a>
  Use `Hash#key?` instead of `Hash#has_key?` and `Hash#value?` instead of
  `Hash#has_value?`. As noted
  [here](http://blade.nagaokaut.ac.jp/cgi-bin/scat.rb/ruby/ruby-core/43765) by
  Matz, the longer forms are considered deprecated.
<sup>[[link](#hash-key)]</sup>

  ```Ruby
  # bad
  hash.has_key?(:test)
  hash.has_value?(value)

  # good
  hash.key?(:test)
  hash.value?(value)
  ```

* <a name="hash-fetch"></a>
  Use `Hash#fetch` when dealing with hash keys that should be present.
<sup>[[link](#hash-fetch)]</sup>

  ```Ruby
  heroes = { batman: 'Bruce Wayne', superman: 'Clark Kent' }
  # bad - if we make a mistake we might not spot it right away
  heroes[:batman] # => "Bruce Wayne"
  heroes[:supermann] # => nil

  # good - fetch raises a KeyError making the problem obvious
  heroes.fetch(:supermann)
  ```

* <a name="hash-fetch-defaults"></a>
  Introduce default values for hash keys via `Hash#fetch` as opposed to using
  custom logic.
<sup>[[link](#hash-fetch-defaults)]</sup>

  ```Ruby
  batman = { name: 'Bruce Wayne', is_evil: false }

  # bad - if we just use || operator with falsy value we won't get the expected result
  batman[:is_evil] || true # => true

  # good - fetch work correctly with falsy values
  batman.fetch(:is_evil, true) # => false
  ```

* <a name="use-hash-blocks"></a>
  Prefer the use of the block instead of the default value in `Hash#fetch`.
<sup>[[link](#use-hash-blocks)]</sup>

  ```Ruby
  batman = { name: 'Bruce Wayne' }

  # bad - if we use the default value, we eager evaluate it
  # so it can slow the program down if done multiple times
  batman.fetch(:powers, get_batman_powers) # get_batman_powers is an expensive call

  # good - blocks are lazy evaluated, so only triggered in case of KeyError exception
  batman.fetch(:powers) { get_batman_powers }
  ```

* <a name="hash-values-at"></a>
  Use `Hash#values_at` when you need to retrieve several values consecutively
  from a hash.
<sup>[[link](#hash-values-at)]</sup>

  ```Ruby
  # bad
  email = data['email']
  nickname = data['nickname']

  # good
  email, username = data.values_at('email', 'nickname')
  ```

* <a name="ordered-hashes"></a>
  Rely on the fact that as of Ruby 1.9 hashes are ordered.
<sup>[[link](#ordered-hashes)]</sup>

* <a name="no-modifying-collections"></a>
  Never modify a collection while traversing it.
<sup>[[link](#no-modifying-collections)]</sup>

## Strings

* <a name="string-interpolation"></a>
  Prefer string interpolation and string formatting instead of string
  concatenation:
<sup>[[link](#string-interpolation)]</sup>

  ```Ruby
  # bad
  email_with_name = user.name + ' <' + user.email + '>'

  # good
  email_with_name = "#{user.name} <#{user.email}>"

  # good
  email_with_name = format('%s <%s>', user.name, user.email)
  ```

* <a name="pad-string-interpolation"></a>
  Consider padding string interpolation code with space. It more clearly sets
  the code apart from the string.
<sup>[[link](#pad-string-interpolation)]</sup>

  ```Ruby
  "#{ user.last_name }, #{ user.first_name }"
  ```

* <a name="consistent-string-literals"></a>
  Adopt a consistent string literal quoting style. There are two popular
  styles in the Ruby community, both of which are considered good - single
  quotes by default (Option A) and double quotes by default (Option B).
<sup>[[link](#consistent-string-literals)]</sup>

  * **(Option A)** Prefer single-quoted strings when you don't need
    string interpolation or special symbols such as `\t`, `\n`, `'`,
    etc.

    ```Ruby
    # bad
    name = "Bozhidar"

    # good
    name = 'Bozhidar'
    ```

  * **(Option B)** Prefer double-quotes unless your string literal
    contains `"` or escape characters you want to suppress.

    ```Ruby
    # bad
    name = 'Bozhidar'

    # good
    name = "Bozhidar"
    ```

  The second style is arguably a bit more popular in the Ruby
  community. The string literals in this guide, however, are
  aligned with the first style.

* <a name="no-character-literals"></a>
  Don't use the character literal syntax `?x`. Since Ruby 1.9 it's basically
  redundant - `?x` would interpreted as `'x'` (a string with a single character
  in it).
<sup>[[link](#no-character-literals)]</sup>

  ```Ruby
  # bad
  char = ?c

  # good
  char = 'c'
  ```

* <a name="curlies-interpolate"></a>
  Don't leave out `{}` around instance and global variables being interpolated
  into a string.
<sup>[[link](#curlies-interpolate)]</sup>

  ```Ruby
  class Person
    attr_reader :first_name, :last_name

    def initialize(first_name, last_name)
      @first_name = first_name
      @last_name = last_name
    end

    # bad - valid, but awkward
    def to_s
      "#@first_name #@last_name"
    end

    # good
    def to_s
      "#{@first_name} #{@last_name}"
    end
  end

  $global = 0
  # bad
  puts "$global = #$global"

  # good
  puts "$global = #{$global}"
  ```

* <a name="no-to-s"></a>
  Don't use `Object#to_s` on interpolated objects. It's invoked on them
  automatically.
<sup>[[link](#no-to-s)]</sup>

  ```Ruby
  # bad
  message = "This is the #{result.to_s}."

  # good
  message = "This is the #{result}."
  ```

* <a name="concat-strings"></a>
  Avoid using `String#+` when you need to construct large data chunks.
  Instead, use `String#<<`. Concatenation mutates the string instance in-place
  and is always faster than `String#+`, which creates a bunch of new string
  objects.
<sup>[[link](#concat-strings)]</sup>

  ```Ruby
  # good and also fast
  html = ''
  html << '<h1>Page title</h1>'

  paragraphs.each do |paragraph|
    html << "<p>#{paragraph}</p>"
  end
  ```

* <a name="heredocs"></a>
  When using heredocs for multi-line strings keep in mind the fact that they
  preserve leading whitespace. It's a good practice to employ some margin based
  on which to trim the excessive whitespace.
<sup>[[link](#heredocs)]</sup>

  ```Ruby
  code = <<-END.gsub(/^\s+\|/, '')
    |def test
    |  some_method
    |  other_method
    |end
  END
  # => "def test\n  some_method\n  other_method\nend\n"
  ```

## Regular Expressions

> Some people, when confronted with a problem, think
> "I know, I'll use regular expressions." Now they have two problems.<br/>
> -- Jamie Zawinski

* <a name="no-regexp-for-plaintext"></a>
  Don't use regular expressions if you just need plain text search in string:
  `string['text']`
<sup>[[link](#no-regexp-for-plaintext)]</sup>

* <a name="regexp-string-index"></a>
  For simple constructions you can use regexp directly through string index.
<sup>[[link](#regexp-string-index)]</sup>

  ```Ruby
  match = string[/regexp/]             # get content of matched regexp
  first_group = string[/text(grp)/, 1] # get content of captured group
  string[/text (grp)/, 1] = 'replace'  # string => 'text replace'
  ```

* <a name="non-capturing-regexp"></a>
  Use non-capturing groups when you don't use captured result of parentheses.
<sup>[[link](#non-capturing-regexp)]</sup>

  ```Ruby
  /(first|second)/   # bad
  /(?:first|second)/ # good
  ```

* <a name="no-perl-regexp-last-matchers"></a>
  Don't use the cryptic Perl-legacy variables denoting last regexp group
  matches (`$1`, `$2`, etc). Use `Regexp.last_match[n]` instead.
<sup>[[link](#no-perl-regexp-last-matchers)]</sup>

  ```Ruby
  /(regexp)/ =~ string
  ...

  # bad
  process $1

  # good
  process Regexp.last_match[1]
  ```

* <a name="no-numbered-regexes"></a>
  Avoid using numbered groups as it can be hard to track what they contain.
  Named groups can be used instead.
<sup>[[link](#no-numbered-regexes)]</sup>

  ```Ruby
  # bad
  /(regexp)/ =~ string
  ...
  process Regexp.last_match[1]

  # good
  /(?<meaningful_var>regexp)/ =~ string
  ...
  process meaningful_var
  ```

* <a name="limit-escapes"></a>
  Character classes have only a few special characters you should care about:
  `^`, `-`, `\`, `]`, so don't escape `.` or brackets in `[]`.
<sup>[[link](#limit-escapes)]</sup>

* <a name="caret-and-dollar-regexp"></a>
  Be careful with `^` and `$` as they match start/end of line, not string
  endings.  If you want to match the whole string use: `\A` and `\z` (not to be
  confused with `\Z` which is the equivalent of `/\n?\z/`).
<sup>[[link](#caret-and-dollar-regexp)]</sup>

  ```Ruby
  string = "some injection\nusername"
  string[/^username$/]   # matches
  string[/\Ausername\z/] # doesn't match
  ```

* <a name="comment-regexes"></a>
  Use `x` modifier for complex regexps. This makes them more readable and you
  can add some useful comments. Just be careful as spaces are ignored.
<sup>[[link](#comment-regexes)]</sup>

  ```Ruby
  regexp = /
    start         # some text
    \s            # white space char
    (group)       # first group
    (?:alt1|alt2) # some alternation
    end
  /x
  ```

* <a name="gsub-blocks"></a>
  For complex replacements `sub`/`gsub` can be used with block or hash.
<sup>[[link](#gsub-blocks)]</sup>

## Percent Literals

* <a name="percent-q-shorthand"></a>
  Use `%()`(it's a shorthand for `%Q`) for single-line strings which require
  both interpolation and embedded double-quotes. For multi-line strings, prefer
  heredocs.
<sup>[[link](#percent-q-shorthand)]</sup>

  ```Ruby
  # bad (no interpolation needed)
  %(<div class="text">Some text</div>)
  # should be '<div class="text">Some text</div>'

  # bad (no double-quotes)
  %(This is #{quality} style)
  # should be "This is #{quality} style"

  # bad (multiple lines)
  %(<div>\n<span class="big">#{exclamation}</span>\n</div>)
  # should be a heredoc.

  # good (requires interpolation, has quotes, single line)
  %(<tr><td class="name">#{name}</td>)
  ```

* <a name="percent-q"></a>
  Avoid `%q` unless you have a string with both `'` and `"` in it. Regular
  string literals are more readable and should be preferred unless a lot of
  characters would have to be escaped in them.
<sup>[[link](#percent-q)]</sup>

  ```Ruby
  # bad
  name = %q(Bruce Wayne)
  time = %q(8 o'clock)
  question = %q("What did you say?")

  # good
  name = 'Bruce Wayne'
  time = "8 o'clock"
  question = '"What did you say?"'
  ```

* <a name="percent-r"></a>
  Use `%r` only for regular expressions matching *more than* one '/'
  character.
<sup>[[link](#percent-r)]</sup>

  ```Ruby
  # bad
  %r(\s+)

  # still bad
  %r(^/(.*)$)
  # should be /^\/(.*)$/

  # good
  %r(^/blog/2011/(.*)$)
  ```

* <a name="percent-x"></a>
  Avoid the use of `%x` unless you're going to invoke a command with
  backquotes in it(which is rather unlikely).
<sup>[[link](#percent-x)]</sup>

  ```Ruby
  # bad
  date = %x(date)

  # good
  date = `date`
  echo = %x(echo `date`)
  ```

* <a name="percent-s"></a>
  Avoid the use of `%s`. It seems that the community has decided `:"some
  string"` is the preferred way to create a symbol with spaces in it.
<sup>[[link](#percent-s)]</sup>

* <a name="percent-literal-braces"></a>
  Prefer `()` as delimiters for all `%` literals, except `%r`. Since parentheses
  often appear inside regular expressions in many scenarios a less common
  character like `{` might be a better choice for a delimiter, depending on the
  regexp's content.
<sup>[[link](#percent-literal-braces)]</sup>

  ```Ruby
  # bad
  %w[one two three]
  %q{"Test's king!", John said.}

  # good
  %w(one two three)
  %q("Test's king!", John said.)
  ```

## Metaprogramming

* <a name="no-metaprogramming-masturbation"></a>
  Avoid needless metaprogramming.
<sup>[[link](#no-metaprogramming-masturbation)]</sup>

* <a name="no-monkey-patching"></a>
  Do not mess around in core classes when writing libraries.  (Do not
  monkey-patch them.)
<sup>[[link](#no-monkey-patching)]</sup>

* <a name="block-class-eval"></a>
  The block form of `class_eval` is preferable to the string-interpolated
  form.  - when you use the string-interpolated form, always supply `__FILE__`
  and `__LINE__`, so that your backtraces make sense:
<sup>[[link](#block-class-eval)]</sup>

  ```ruby
  class_eval 'def use_relative_model_naming?; true; end', __FILE__, __LINE__
  ```

  - `define_method` is preferable to `class_eval{ def ... }`

* <a name="eval-comment-docs"></a>
  When using `class_eval` (or other `eval`) with string interpolation, add a
  comment block showing its appearance if interpolated (a practice used in Rails
  code):
<sup>[[link](#eval-comment-docs)]</sup>

  ```ruby
  # from activesupport/lib/active_support/core_ext/string/output_safety.rb
  UNSAFE_STRING_METHODS.each do |unsafe_method|
    if 'String'.respond_to?(unsafe_method)
      class_eval <<-EOT, __FILE__, __LINE__ + 1
        def #{unsafe_method}(*args, &block)       # def capitalize(*args, &block)
          to_str.#{unsafe_method}(*args, &block)  #   to_str.capitalize(*args, &block)
        end                                       # end

        def #{unsafe_method}!(*args)              # def capitalize!(*args)
          @dirty = true                           #   @dirty = true
          super                                   #   super
        end                                       # end
      EOT
    end
  end
  ```

* <a name="no-method-missing"></a>
  Avoid using `method_missing` for metaprogramming because backtraces become
  messy, the behavior is not listed in `#methods`, and misspelled method calls
  might silently work, e.g. `nukes.launch_state = false`. Consider using
  delegation, proxy, or `define_method` instead. If you must use
  `method_missing`:
<sup>[[link](#no-method-missing)]</sup>

  - Be sure to [also define `respond_to_missing?`](http://blog.marc-andre.ca/2010/11/methodmissing-politely.html)
  - Only catch methods with a well-defined prefix, such as `find_by_*` -- make your code as assertive as possible.
  - Call `super` at the end of your statement
  - Delegate to assertive, non-magical methods:

    ```ruby
    # bad
    def method_missing?(meth, *args, &block)
      if /^find_by_(?<prop>.*)/ =~ meth
        # ... lots of code to do a find_by
      else
        super
      end
    end

    # good
    def method_missing?(meth, *args, &block)
      if /^find_by_(?<prop>.*)/ =~ meth
        find_by(prop, *args, &block)
      else
        super
      end
    end

    # best of all, though, would to define_method as each findable attribute is declared
    ```

## Misc

* <a name="always-warn"></a>
  Write `ruby -w` safe code.
<sup>[[link](#always-warn)]</sup>

* <a name="no-optional-hash-params"></a>
  Avoid hashes as optional parameters. Does the method do too much? (Object
  initializers are exceptions for this rule).
<sup>[[link](#no-optional-hash-params)]</sup>

* <a name="short-methods"></a>
  Avoid methods longer than 10 LOC (lines of code). Ideally, most methods will
  be shorter than 5 LOC. Empty lines do not contribute to the relevant LOC.
<sup>[[link](#short-methods)]</sup>

* <a name="too-many-params"></a>
  Avoid parameter lists longer than three or four parameters.
<sup>[[link](#too-many-params)]</sup>

* <a name="private-global-methods"></a>
  If you really need "global" methods, add them to Kernel and make them
  private.
<sup>[[link](#private-global-methods)]</sup>

* <a name="instance-vars"></a>
  Use module instance variables instead of global variables.
<sup>[[link](#instance-vars)]</sup>

  ```Ruby
  # bad
  $foo_bar = 1

  # good
  module Foo
    class << self
      attr_accessor :bar
    end
  end

  Foo.bar = 1
  ```

* <a name="alias-method"></a>
  Avoid `alias` when `alias_method` will do.
<sup>[[link](#alias-method)]</sup>

* <a name="optionparser"></a>
  Use `OptionParser` for parsing complex command line options and `ruby -s`
  for trivial command line options.
<sup>[[link](#optionparser)]</sup>

* <a name="time-now"></a>
  Prefer `Time.now` over `Time.new` when retrieving the current system time.
<sup>[[link](#time-now)]</sup>

* <a name="functional-code"></a>
  Code in a functional way, avoiding mutation when that makes sense.
<sup>[[link](#functional-code)]</sup>

* <a name="no-arg-mutations"></a>
  Do not mutate arguments unless that is the purpose of the method.
<sup>[[link](#no-arg-mutations)]</sup>

* <a name="three-is-the-number-thou-shalt-count"></a>
  Avoid more than three levels of block nesting.
<sup>[[link](#three-is-the-number-thou-shalt-count)]</sup>

* <a name="be-consistent"></a>
  Be consistent. In an ideal world, be consistent with these guidelines.
<sup>[[link](#be-consistent)]</sup>

* <a name="common-sense"></a>
  Use common sense.
<sup>[[link](#common-sense)]</sup>

## Tools

Here's some tools to help you automatically check Ruby code against
this guide.

### RuboCop

[RuboCop](https://github.com/bbatsov/rubocop) is a Ruby code style
checker based on this style guide. RuboCop already covers a
significant portion of the Guide, supports both MRI 1.9 and MRI 2.0
and has good Emacs integration.

### RubyMine

[RubyMine](http://www.jetbrains.com/ruby/)'s code inspections are
[partially based](http://confluence.jetbrains.com/display/RUBYDEV/RubyMine+Inspections)
on this guide.

# Contributing

Nothing written in this guide is set in stone. It's my desire to work
together with everyone interested in Ruby coding style, so that we could
ultimately create a resource that will be beneficial to the entire Ruby
community.

Feel free to open tickets or send pull requests with improvements. Thanks in
advance for your help!

You can also support the project (and RuboCop) with financial
contributions via [gittip](https://www.gittip.com/bbatsov).

[![Support via Gittip](https://rawgithub.com/twolfson/gittip-badge/0.2.0/dist/gittip.png)](https://www.gittip.com/bbatsov)

## How to Contribute?

It's easy, just follow the [contribution guidelines](https://github.com/bbatsov/ruby-style-guide/blob/master/CONTRIBUTING.md).

# License

![Creative Commons License](http://i.creativecommons.org/l/by/3.0/88x31.png)
This work is licensed under a [Creative Commons Attribution 3.0 Unported License](http://creativecommons.org/licenses/by/3.0/deed.en_US)

# Spread the Word

A community-driven style guide is of little use to a community that
doesn't know about its existence. Tweet about the guide, share it with
your friends and colleagues. Every comment, suggestion or opinion we
get makes the guide just a little bit better. And we want to have the
best possible guide, don't we?

Cheers,<br/>
[Bozhidar](https://twitter.com/bbatsov)
