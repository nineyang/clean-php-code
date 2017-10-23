## 前言
前几天在[GitHub](https://github.com/php-cpm/clean-code-php)看到一篇写`PHP`简洁之道的译文，觉得还不错，所以转在了自己的[博客](http://www.hellonine.top/index.php/archives/70/)中，只不过有一些地方好像没有翻译，再加上排版上的一些小问题，所以决定自己翻译一遍。
原文地址:https://github.com/jupeter/clean-code-php

---

## 变量

- 使用更有意义和更加直白的命名方式

>不友好的:
>
>```php
>$ymdstr = $moment->format('y-m-d');
>```
>
>友好的:
>
>```php
>$currentDate = $moment->format('y-m-d');
>```

- 对于同一实体使用相同变量名

>不友好的:
>
>```php
>getUserInfo();
>getUserData();
>getUserRecord();
>getUserProfile();
>```
>
>友好的:
>
>```php
>getUser();
>```

- 使用可以查找到的变量

>我们读的代码量远比我们写过的多。因此，写出可阅读和便于搜索的代码是及其重要的。在我们的程序中写出一些难以理解的变量名
>到最后甚至会让自己非常伤脑筋。
>因此，让你的名字便于搜索吧。
>
>不友好的:
>
>```php
>// 这里的448代表什么意思呢？
>$result = $serializer->serialize($data, 448);
>```
>
>友好的:
>
>```php
>$json = $serializer->serialize($data, JSON_UNESCAPED_SLASHES | JSON_PRETTY_PRINT | >JSON_UNESCAPED_UNICODE);
>```

- 使用解释型变量

>不友好的
>
>```php
>$address = 'One Infinite Loop, Cupertino 95014';
>$cityZipCodeRegex = '/^[^,]+,\s*(.+?)\s*(\d{5})$/';
>preg_match($cityZipCodeRegex, $address, $matches);
>
>saveCityZipCode($matches[1], $matches[2]);
>```
>
>不至于那么糟糕的:
>
>>稍微好一些，但是这取决于我们对正则的熟练程度。
>
>```php
>$address = 'One Infinite Loop, Cupertino 95014';
>$cityZipCodeRegex = '/^[^,]+,\s*(.+?)\s*(\d{5})$/';
>preg_match($cityZipCodeRegex, $address, $matches);
>
>[, $city, $zipCode] = $matches;
>saveCityZipCode($city, $zipCode);
>```
>
>友好的:
>
>>通过对子模式的重命名减少了我们对正则的熟悉和依赖程度。
>
>```php
>$address = 'One Infinite Loop, Cupertino 95014';
>$cityZipCodeRegex = '/^[^,]+,\s*(?<city>.+?)\s*(?<zipCode>\d{5})$/';
>preg_match($cityZipCodeRegex, $address, $matches);
>
>saveCityZipCode($matches['city'], $matches['zipCode']);
>```

- 嵌套无边，回头是岸

>太多的`if else`嵌套会让你的代码难以阅读和维护。更加直白的代码会好很多。
>
>1. demo1
>
>不友好的:
>
>```php
>function isShopOpen($day): bool
>{
>    if ($day) {
>        if (is_string($day)) {
>            $day = strtolower($day);
>            if ($day === 'friday') {
>                return true;
>            } elseif ($day === 'saturday') {
>                return true;
>            } elseif ($day === 'sunday') {
>                return true;
>            } else {
>                return false;
>            }
>        } else {
>            return false;
>        }
>    } else {
>        return false;
>    }
>}
>```
>
>友好的:
>
>```php
>function isShopOpen(string $day): bool
>{
>    if (empty($day)) {
>        return false;
>    }
>
>    $openingDays = [
>        'friday', 'saturday', 'sunday'
>    ];
>
>    return in_array(strtolower($day), $openingDays, true);
>}
>```
>
>2. demo2
>
>不友好的:
>```php
>function fibonacci(int $n)
>{
>    if ($n < 50) {
>        if ($n !== 0) {
>            if ($n !== 1) {
>                return fibonacci($n - 1) + fibonacci($n - 2);
>            } else {
>                return 1;
>            }
>        } else {
>            return 0;
>        }
>    } else {
>        return 'Not supported';
>    }
>}
>```
>
>友好的:
>
>```php
>function fibonacci(int $n): int
>{
>    if ($n === 0 || $n === 1) {
>        return $n;
>    }
>
>    if ($n > 50) {
>        throw new \Exception('Not supported');
>    }
>
>    return fibonacci($n - 1) + fibonacci($n - 2);
>}
>```

- 避免使用不合理的变量名
>
>别让其他人去猜你的变量名的意思。
>更加直白的代码会好很多。
>
>不友好的:
>
>```php
>$l = ['Austin', 'New York', 'San Francisco'];
>
>for ($i = 0; $i < count($l); $i++) {
>    $li = $l[$i];
>    doStuff();
>    doSomeOtherStuff();
>    // ...
>    // ...
>    // ...
>    // 等等，这个$li是什么意思?
>    dispatch($li);
>}
>```
>
>友好的:
>
>```php
>$locations = ['Austin', 'New York', 'San Francisco'];
>
>foreach ($locations as $location) {
>    doStuff();
>    doSomeOtherStuff();
>    // ...
>    // ...
>    // ...
>    dispatch($location);
>}
>```

> 别添加没必要的上下文
>
>如果你的类或对象的名字已经传达了一些信息，那么请别在变量名中重复。
>
>不友好的
>
>```php
>class Car
>{
>    public $carMake;
>    public $carModel;
>    public $carColor;
>
>    //...
>}
>```
>
>友好的
>
>```php
>class Car
>{
>    public $make;
>    public $model;
>    public $color;
>
>    //...
>}
>```

- 用参数默认值代替短路运算或条件运算

>不友好的
>
>这里不太合理，因为变量`$breweryName`有可能是`NULL`。
>
>```php
>function createMicrobrewery($breweryName = 'Hipster Brew Co.'): void
>{
>    // ...
>}
>```
>
>不至于那么糟糕的
>
>这种写法要比上一版稍微好理解一些，但是如果能控制变量值获取会更好。
>
>```php
>function createMicrobrewery($name = null): void
>{
>    $breweryName = $name ?: 'Hipster Brew Co.';
>    // ...
>}
>```
>
>友好的
>
>如果你仅支持 `PHP 7+`，那么你可以使用[类型约束](http://php.net/manual/en/functions.arguments.php#functions.arguments.type-declaration)并且保证`$http://php.net/manual/en/functions.arguments.php#functions.arguments.type-declaration`变量不会为`NULL`。
>
>```php
>function createMicrobrewery(string $breweryName = 'Hipster Brew Co.'): void
>{
>    // ...
>}
>```

---

## 函数

- 函数参数应该控制在两个以下
>
>限制函数的参数对于在对函数做测试来说相当重要。有超过三个可选的参数会给你的测试工作量带来倍速增长。
>
>最理想的情况是没有参数。1-2个参数也还凑合，但是三个参数就应该避免了。参数越多，我们需要维护的就越多。通常，如果你的函>数有超过2个的参数，那么你的这个函数需要处理的事情就太多了。如果的确需要这么多参数，那么在大多数情况下， 用一个对象来处理可能会更合适。
>
>不友好的:
>
>```php
>function createMenu(string $title, string $body, string $buttonText, bool $cancellable): void
>{
>    // ...
>}
>```
>
>友好的:
>
>```php
>class MenuConfig
>{
>    public $title;
>    public $body;
>    public $buttonText;
>    public $cancellable = false;
>}
>
>$config = new MenuConfig();
>$config->title = 'Foo';
>$config->body = 'Bar';
>$config->buttonText = 'Baz';
>$config->cancellable = true;
>
>function createMenu(MenuConfig $config): void
>{
>    // ...
>}
>```

- 一个函数只做一件事情

>在软件工程行业，这是最重要的准则。当函数所处理的事情超过一件，他就会变得难以实现，测试和理解。当你能让一个函数仅仅负责一个事情，他们就会变得容易重构并且理解起来越清晰。光是执行这样一条原则就能让你成为开发者中的佼佼者了。
>
>不友好的:
>```php
>function emailClients(array $clients): void
>{
>    foreach ($clients as $client) {
>        $clientRecord = $db->find($client);
>        if ($clientRecord->isActive()) {
>            email($client);
>        }
>    }
>}
>```
>
>友好的:
>
>```php
>function emailClients(array $clients): void
>{
>    $activeClients = activeClients($clients);
>    array_walk($activeClients, 'email');
>}
>
>function activeClients(array $clients): array
>{
>    return array_filter($clients, 'isClientActive');
>}
>
>function isClientActive(int $client): bool
>{
>    $clientRecord = $db->find($client);
>
>    return $clientRecord->isActive();
>}
>```

- 函数名应该说到做到

>不友好的:
>
>```php
>class Email
>{
>    //...
>
>    public function handle(): void
>    {
>        mail($this->to, $this->subject, $this->body);
>    }
>}
>
>$message = new Email(...);
>// 这是什么？这个`handle`方法是什么？我们现在应该写入到一个文件吗？
>$message->handle();
>```
>
>友好的:
>
>```php
>class Email 
>{
>    //...
>
>    public function send(): void
>    {
>        mail($this->to, $this->subject, $this->body);
>    }
>}
>
>$message = new Email(...);
>// 清晰并且显而易见
>$message->send();
>```

- 函数应该只有一层抽象

>当你的函数有超过一层的抽象时便意味着这个函数做了太多事情。解耦这个函数致使其变得可重用和更易测试。

>不友好的:
>
>```php
>function parseBetterJSAlternative(string $code): void
>{
>    $regexes = [
>        // ...
>    ];
>
>    $statements = explode(' ', $code);
>    $tokens = [];
>    foreach ($regexes as $regex) {
>        foreach ($statements as $statement) {
>            // ...
>        }
>    }
>
>    $ast = [];
>    foreach ($tokens as $token) {
>        // lex...
>    }
>
>    foreach ($ast as $node) {
>        // parse...
>    }
>}
>```
>
>同样不太友好:
>
>>我们已经从函数中拆分除了一些东西出来，但是`parseBetterJSAlternative()`这个函数还是太复杂以至于难以测试。
>
>```php
>function tokenize(string $code): array
>{
>    $regexes = [
>        // ...
>    ];
>
>    $statements = explode(' ', $code);
>    $tokens = [];
>    foreach ($regexes as $regex) {
>        foreach ($statements as $statement) {
>            $tokens[] = /* ... */;
>        }
>    }
>
>    return $tokens;
>}
>
>function lexer(array $tokens): array
>{
>    $ast = [];
>    foreach ($tokens as $token) {
>        $ast[] = /* ... */;
>    }
>
>    return $ast;
>}
>
>function parseBetterJSAlternative(string $code): void
>{
>    $tokens = tokenize($code);
>    $ast = lexer($tokens);
>    foreach ($ast as $node) {
>        // parse...
>    }
>}
>```
>
>友好的
>
>>最优解就是把`parseBetterJSAlternative()`函数依赖的东西分离出来。

>```php
>class Tokenizer
>{
>    public function tokenize(string $code): array
>    {
>        $regexes = [
>            // ...
>        ];
>
>        $statements = explode(' ', $code);
>        $tokens = [];
>        foreach ($regexes as $regex) {
>            foreach ($statements as $statement) {
>                $tokens[] = /* ... */;
>            }
>        }
>
>       return $tokens;
>    }
>}
>
>class Lexer
>{
>    public function lexify(array $tokens): array
>    {
>        $ast = [];
>        foreach ($tokens as $token) {
>            $ast[] = /* ... */;
>        }
>
>       return $ast;
>    }
>}
>
>class BetterJSAlternative
>{
>    private $tokenizer;
>    private $lexer;
>
>    public function __construct(Tokenizer $tokenizer, Lexer $lexer)
>    {
>        $this->tokenizer = $tokenizer;
>        $this->lexer = $lexer;
>    }
>
>    public function parse(string $code): void
>    {
>        $tokens = $this->tokenizer->tokenize($code);
>        $ast = $this->lexer->lexify($tokens);
>        foreach ($ast as $node) {
>            // parse...
>        }
>    }
>}
>```

- 不要在函数中带入`flag`相关的参数

>当你使用`flag`时便意味着你的函数做了超过一件事情。前面我们也提到了，函数应该只做一件事情。如果你的代码取决于一个`boolean`，那么还是把这些内容拆分出来吧。
>
>不友好的

>```php
>function createFile(string $name, bool $temp = false): void
>{
>    if ($temp) {
>        touch('./temp/'.$name);
>    } else {
>        touch($name);
>    }
>}
>```
>
>友好的

>```php
>function createFile(string $name): void
>{
>    touch($name);
>}
>
>function createTempFile(string $name): void
>{
>    touch('./temp/'.$name);
>}
>```

- 避免函数带来的副作用

>当函数有数据的输入和输出时可能会产生副作用。这个副作用可能被写入一个文件，修改一些全局变量，或者意外的把你的钱转给一个陌生人。
>
>此刻，你可能有时候会需要这些副作用。正如前面所说，你可能需要写入到一个文件中。你需要注意的是把这些你所做的东西在你的掌控之下。别让某些个别函数或者类写入了一个特别的文件。对于所有的都应该一视同仁。有且仅有一个结果。
>
>重要的是要避免那些譬如共享无结构的对象，使用可以写入任何类型的可变数据，不对副作用进行集中处理等常见的陷阱。如果你可以做到，你将会比大多数程序猿更加轻松。
>
>
>不友好的
>
>```php
>// 全局变量被下面的函数引用了。
>// 如果我们在另外一个函数中使用了这个`$name`变量，那么可能会变成一个数组或者程序被打断。
>$name = 'Ryan McDermott';
>
>function splitIntoFirstAndLastName(): void
>{
>    global $name;
>
>    $name = explode(' ', $name);
>}
>
>splitIntoFirstAndLastName();
>
>var_dump($name); // ['Ryan', 'McDermott'];
>```
>
>友好的
>
>```php
>function splitIntoFirstAndLastName(string $name): array
>{
>    return explode(' ', $name);
>}
>
>$name = 'Ryan McDermott';
>$newName = splitIntoFirstAndLastName($name);
>
>var_dump($name); // 'Ryan McDermott';
>var_dump($newName); // ['Ryan', 'McDermott'];
>```

- 避免写全局方法

>在大多数语言中，全局变量被污染都是一个不太好的实践，因为当你引入另外的包时会起冲突并且使用你的`API`的人知道抛出了一个异常才明白。我们假设一个简单的例子：如果你想要一个配置数组。你可能会写一个类似于`config()`的全局的函数，但是在引入其他包并在其他地方尝试做同样的事情时会起冲突。
>
>不友好的
>
>```php
>function config(): array
>{
>    return  [
>        'foo' => 'bar',
>    ]
>}
>```
>
>不友好的
>
>```php
>class Configuration
>{
>    private $configuration = [];
>
>    public function __construct(array $configuration)
>    {
>        $this->configuration = $configuration;
>    }
>
>    public function get(string $key): ?string
>    {
>        return isset($this->configuration[$key]) ? $this->configuration[$key] : null;
>    }
>}
>```


>>通过创建`Configuration`类的实例来引入配置

>```php
>$configuration = new Configuration([
>    'foo' => 'bar',
>]);
>```

>>至此，你就可以是在你的项目中使用这个配置了。

- 避免使用单例模式
>单例模式是一种[反模式](https://en.wikipedia.org/wiki/Singleton_pattern)。为什么不建议使用:
> 1. 他们通常使用一个**全局实例**，为什么这么糟糕？因为**你隐藏了依赖关系**在你的项目的代码中，而不是通过接口暴露出来。你应该有[意识](https://en.wikipedia.org/wiki/Code_smell)的去避免那些全局的东西。
> 2. 他们违背了**单一职责原则**：他们会自己**控制自己的生命周期**。
> 3. 这种模式会自然而然的使代码[耦合](https://en.wikipedia.org/wiki/Coupling_%28computer_programming%29)在一起。这会让他们在测试中，很多情况下都**理所当然的不一致**。
> 4. 他们持续在整个项目的生命周期中。另外一个严重的打击是**当你需要排序测试的时候**，在单元测试中这会是一个不小的麻烦。为什么？因为每个单元测试都应该依赖于另外一个。

>不友好的

>```php
>class DBConnection
>{
>    private static $instance;
>
>    private function __construct(string $dsn)
>    {
>        // ...
>    }
>
>    public static function getInstance(): DBConnection
>    {
>        if (self::$instance === null) {
>            self::$instance = new self();
>        }
>
>        return self::$instance;
>    }
>
>    // ...
>}

>$singleton = DBConnection::getInstance();
>```

>友好的

>```php
>class DBConnection
>{
>    public function __construct(string $dsn)
>    {
>        // ...
>    }
>
>     // ...
>}
>```

>>使用[DSN](http://php.net/manual/en/pdo.construct.php#refsect1-pdo.construct-parameters)配置来创建一个`DBConnection`类的单例。

>```php
>$connection = new DBConnection($dsn);
>```

>>此时，在你的项目中必须使用`DBConnection`的单例。

- 对条件判断进行包装

>不友好的

>```php
>if ($article->state === 'published') {
>    // ...
>}
>```

>友好的

>```php
>if ($article->isPublished()) {
>    // ...
>}
>```

- 避免对条件取反
>不友好的
>
>```php
>function isDOMNodeNotPresent(\DOMNode $node): bool
>{
>    // ...
>}

>if (!isDOMNodeNotPresent($node))
>{
>    // ...
>}
>```

>友好的

>```php
>function isDOMNodePresent(\DOMNode $node): bool
>{
>    // ...
>}

>if (isDOMNodePresent($node)) {
>    // ...
>}
>```

- 避免太多的条件嵌套

>这似乎是一个不可能的任务。很多人的脑海中可能会在第一时间萦绕“如果没有`if`条件我还能做什么呢？”。答案就是，在大多数情况下，你可以使用多态去处理这个难题。此外，可能有人又会说了，“即使多态可以做到，但是我们为什么要这么做呢？”，对此我们的解释是，一个函数应该只做一件事情，这也正是我们在前面所提到的让代码更加整洁的原则。当你的函数中使用了太多的`if`条件时，便意味着你的函数做了超过一件事情。牢记：要专一。

>不友好的:

>```php
>class Airplane
>{
>    // ...
>
>    public function getCruisingAltitude(): int
>    {
>        switch ($this->type) {
>            case '777':
>                return $this->getMaxAltitude() - $this->getPassengerCount();
>            case 'Air Force One':
>                return $this->getMaxAltitude();
>            case 'Cessna':
>                return $this->getMaxAltitude() - $this->getFuelExpenditure();
>        }
>    }
>}
>```

>友好的:

>```php
>interface Airplane
>{
>    // ...
>
>    public function getCruisingAltitude(): int;
>}

>class Boeing777 implements Airplane
>{
>    // ...
>
>    public function getCruisingAltitude(): int
>    {
>        return $this->getMaxAltitude() - $this->getPassengerCount();
>    }
>}

>class AirForceOne implements Airplane
>{
>    // ...
>
>    public function getCruisingAltitude(): int
>    {
>        return $this->getMaxAltitude();
>    }
>}

>class Cessna implements Airplane
>{
>    // ...
>
>    public function getCruisingAltitude(): int
>    {
>        return $this->getMaxAltitude() - $this->getFuelExpenditure();
>    }
>}
>```

- 避免类型检测 (part 1)

>`PHP`是一门弱类型语言，这意味着你的函数可以使用任何类型的参数。他在给予你无限的自由的同时又让你困扰，因为有有时候你需要做类型检测。这里有很多方式去避免这种事情，第一种方式就是统一`API`。

>不友好的：

>```php
>function travelToTexas($vehicle): void
>{
>    if ($vehicle instanceof Bicycle) {
>        $vehicle->pedalTo(new Location('texas'));
>    } elseif ($vehicle instanceof Car) {
>        $vehicle->driveTo(new Location('texas'));
>    }
>}
>```

>友好的：

```php
function travelToTexas(Traveler $vehicle): void
{
    $vehicle->travelTo(new Location('texas'));
}
```

- 避免类型检测 (part 2)

>如果你正使用诸如字符串、整型和数组等基本类型，且要求版本是PHP 7+，不能使用多态，需要类型检测，那你应当考虑[类型声明](http://php.net/manual/en/functions.arguments.php#functions.arguments.type-declaration)或者严格模式。它提供了基于标准PHP语法的静态类型。手动检查类型的问题是做好了需要好多的废话，好像为了安全就可以不顾损失可读性。保持你的`PHP`代码整洁，写好测试，保持良好的回顾代码的习惯。否则的话，那就还是用PHP严格类型声明和严格模式来确保安全吧。

>不友好的:

>```php
>function combine($val1, $val2): int
>{
>    if (!is_numeric($val1) || !is_numeric($val2)) {
>        throw new \Exception('Must be of type Number');
>    }
>
>    return $val1 + $val2;
>}
>```

>友好的:

>```php
>function combine(int $val1, int $val2): int
>{
>    return $val1 + $val2;
>}
>```

- 移除那些没有使用的代码

>没有再使用的代码就好比重复代码一样糟糕。在你的代码库中完全没有必要保留。如果确定不再使用，那就把它删掉吧！如果有一天你要使用，你也可以在你的版本记录中找到它。

>不友好的:

>```php
>function oldRequestModule(string $url): void
>{
>    // ...
>}

>function newRequestModule(string $url): void
>{
>    // ...
>}

>$request = newRequestModule($requestUrl);
>inventoryTracker('apples', $request, 'www.inventory-awesome.io');
>```

>友好的:

>```php
>function requestModule(string $url): void
>{
>    // ...
>}

>$request = requestModule($requestUrl);
>inventoryTracker('apples', $request, 'www.inventory-awesome.io');
>```

---

## 对象和数据结构

- 使用对象封装
 
>在`PHP`中你可以设置`public`，`protected`，和`private`关键词来修饰你的方法。当你使用它们，你就可以在一个对象中控制这些属性的修改权限了。

>* 当你想要对对象的属性进行除了“获取”之外的操作时，你不必再去浏览并在代码库中修改权限。
>* 当你要做一些修改属性的操作时，你更易于在代码中做逻辑验证。
>* 封装内部表示。
>* 当你在做获取和设置属性的操作时，更易于添加`log`或`error`的操作。
>* 当其他`class`继承了这个基类，你可以重写默认的方法。
>* 你可以为一个服务延迟的去获取这个对象的属性值。

>不太友好的:

>```php
>class BankAccount
>{
>    public $balance = 1000;
>}
>
>$bankAccount = new BankAccount();
>
>// Buy shoes...
>$bankAccount->balance -= 100;
>```

>友好的:

>```php
>class BankAccount
>{
>    private $balance;
>
>    public function __construct(int $balance = 1000)
>    {
>      $this->balance = $balance;
>    }
>
>    public function withdraw(int $amount): void
>    {
>        if ($amount > $this->balance) {
>            throw new \Exception('Amount greater than available balance.');
>        }
>
>        $this->balance -= $amount;
>    }
>
>    public function deposit(int $amount): void
>    {
>        $this->balance += $amount;
>    }
>
>    public function getBalance(): int
>    {
>        return $this->balance;
>    }
>}

>$bankAccount = new BankAccount();

>// Buy shoes...
>$bankAccount->withdraw($shoesPrice);

>// Get balance
>$balance = $bankAccount->getBalance();
>```

- 在对象的属性上可以使用`private/protected`限定

>* `public`修饰的方法和属性同上来说被修改是比较危险的，因为一些外部的代码可以轻易的依赖于他们并且你没办法控制哪些代码依赖于他们。**对于所有用户的类来说，在类中可以修改是相当危险的。**
>* `protected`修饰器和`public`同样危险，因为他们在继承链中同样可以操作。二者的区别仅限于权限机制，并且封装保持不变。**对于所有子类来说，在类中修改也是相当危险的。**
>* `private`修饰符保证了代码只有在**自己类的内部修改才是危险的。**

>因此，当你在需要对外部的类设置权限时使用`private`修饰符去取代`public/protected`吧。

>如果需要了解更多信息你可以读[Fabien Potencier](https://github.com/fabpot)写的这篇[文章](http://fabien.potencier.org/pragmatism-over-theory-protected-vs-private.html)

>不太友好的:

>```php
>class Employee
>{
>    public $name;

>    public function __construct(string $name)
>    {
>        $this->name = $name;
>    }
>}

>$employee = new Employee('John Doe');
>echo 'Employee name: '.$employee->name; // Employee name: John Doe
>```

>友好的:

>```php
>class Employee
>{
>    private $name;

>    public function __construct(string $name)
>    {
>        $this->name = $name;
>    }

>    public function getName(): string
>    {
>        return $this->name;
>    }
>}

>$employee = new Employee('John Doe');
>echo 'Employee name: '.$employee->getName(); // Employee name: John Doe
>```

- 组合优于继承

>正如`the Gang of Four`在著名的[*Design Patterns*](https://en.wikipedia.org/wiki/Design_Patterns)中所说，你应该尽可能的使用组合而不是继承。不管是使用组合还是继承都有很多的优点。最重要的一个准则在于当你本能的想要使用继承时，不妨思考一下组合是否能让你的问题解决的更加优雅。在某些时候确实如此。

>你可能会这么问了，“那到底什么时候我应该使用继承呢？”这完全取决你你手头上的问题，下面正好有一些继承优于组合的例子：

> 1. 你的继承表达了“是一个”而不是“有一个”的关系(Human->Animal vs. User->UserDetails)。
> 2. 你可能会重复的使用基类的代码(Humans can move like all animals)。
> 3. 你渴望在修改代码的时候通过基类来统一调度(Change the caloric expenditure of all animals when they move)。

>不友好的:

>```php
>class Employee 
>{
>    private $name;
>    private $email;

>    public function __construct(string $name, string $email)
>    {
>        $this->name = $name;
>        $this->email = $email;
>    }

>    // ...
>}

>// 这里不太合理的原因在于并非所有的职员都有`tax`这个特征。

>class EmployeeTaxData extends Employee 
>{
>    private $ssn;
>    private $salary;
    
>    public function __construct(string $name, string $email, string $ssn, string $salary)
>    {
>        parent::__construct($name, $email);

>        $this->ssn = $ssn;
>        $this->salary = $salary;
>    }

>    // ...
>}
>```

>友好的:

>```php
>class EmployeeTaxData 
>{
>    private $ssn;
>    private $salary;

>    public function __construct(string $ssn, string $salary)
>    {
>        $this->ssn = $ssn;
>        $this->salary = $salary;
>    }

>    // ...
>}

>class Employee 
>{
>    private $name;
>    private $email;
>    private $taxData;

>    public function __construct(string $name, string $email)
>    {
>        $this->name = $name;
>        $this->email = $email;
>    }

>    public function setTaxData(string $ssn, string $salary)
>    {
>        $this->taxData = new EmployeeTaxData($ssn, $salary);
>    }

>    // ...
>}
>```

- 避免链式调用(连贯接口)

>在使用一些[链式方法](https://en.wikipedia.org/wiki/Method_chaining)时，这种[连贯接口](https://en.wikipedia.org/wiki/Fluent_interface)可以不断地指向当前对象让我们的代码显得更加清晰可读。

>通常情况下，我们在构建对象时都可以利用他的上下文这一特征，因为这种模式可以减少代码的冗余，不过在[PHPUnit Mock Builder]((https://phpunit.de/manual/current/en/test-doubles.html)或者[Doctrine Query Builder](http://docs.doctrine-project.org/projects/doctrine-dbal/en/latest/reference/query-builder.html)所提及的，有时候这种方式会带来一些麻烦：
> 1. 破坏[封装](https://en.wikipedia.org/wiki/Encapsulation_%28object-oriented_programming%29)
> 2. 破坏[设计](https://en.wikipedia.org/wiki/Decorator_pattern)
> 3. 难以[测试](https://en.wikipedia.org/wiki/Mock_object)
> 4. 可能会难以阅读

>如果需要了解更多信息你可以读[Marco Pivetta](https://github.com/Ocramius)写的这篇[文章](https://ocramius.github.io/blog/fluent-interfaces-are-evil/)

>友好的:

>```php
>class Car
>{
>    private $make = 'Honda';
>    private $model = 'Accord';
>    private $color = 'white';

>    public function setMake(string $make): self
>    {
>        $this->make = $make;

>        // NOTE: Returning this for chaining
>        return $this;
>    }

>    public function setModel(string $model): self
>    {
>        $this->model = $model;

>        // NOTE: Returning this for chaining
>        return $this;
>    }

>    public function setColor(string $color): self
>    {
>        $this->color = $color;

>        // NOTE: Returning this for chaining
>        return $this;
>    }

>    public function dump(): void
>    {
>        var_dump($this->make, $this->model, $this->color);
>    }
>}

>$car = (new Car())
>  ->setColor('pink')
>  ->setMake('Ford')
>  ->setModel('F-150')
>  ->dump();
>```

>不友好的:

>```php
>class Car
>{
>    private $make = 'Honda';
>    private $model = 'Accord';
>    private $color = 'white';

>    public function setMake(string $make): void
>    {
>        $this->make = $make;
>    }

>    public function setModel(string $model): void
>   {
>        $this->model = $model;
>    }

>    public function setColor(string $color): void
>    {
>        $this->color = $color;
>    }

>    public function dump(): void
>    {
>        var_dump($this->make, $this->model, $this->color);
>    }
>}

>$car = new Car();
>$car->setColor('pink');
>$car->setMake('Ford');
>$car->setModel('F-150');
>$car->dump();
>```

---

## SOLID

>**SOLID**最开始是由Robert Martin提出的五个准则，并最后由Michael Feathers命名的简写，这五个是在面对对象设计中的五个基本原则。

> * S: 职责单一原则 (SRP)
> * O: 开闭原则 (OCP)
> * L: 里氏替换原则 (LSP)
> * I: 接口隔离原则 (ISP)
> * D: 依赖反转原则 (DIP)

- 职责单一原则 (SRP)

>正如Clean Code所述，“修改类应该只有一个理由”。我们总是喜欢在类中写入太多的方法，就像你在飞机上塞满你的行李箱。在这种情况下你的类没有高内聚的概念并且留下了很多可以修改的理由。尽可能的减少你需要去修改类的时间是非常重要的。如果在你的单个类中有太多的方法并且你经常修改的话，那么如果其他代码库中有引入这样的模块的话会非常难以理解。

>不友好的:

>```php
>class UserSettings
>{
>    private $user;

>    public function __construct(User $user)
>    {
>        $this->user = $user;
>    }

>    public function changeSettings(array $settings): void
>    {
>        if ($this->verifyCredentials()) {
>            // ...
>        }
>    }

>    private function verifyCredentials(): bool
>    {
>        // ...
>    }
>}
>```

>友好的:

>```php
>class UserAuth 
>{
>    private $user;

>    public function __construct(User $user)
>    {
>        $this->user = $user;
>    }
    
>    public function verifyCredentials(): bool
>    {
>        // ...
>    }
>}

>class UserSettings 
>{
>    private $user;
>    private $auth;

>    public function __construct(User $user) 
>    {
>        $this->user = $user;
>        $this->auth = new UserAuth($user);
>    }

>    public function changeSettings(array $settings): void
>    {
>        if ($this->auth->verifyCredentials()) {
>            // ...
>        }
>    }
>}
>```

- 开闭原则 (OCP)

>正如Bertrand Meyer所说，“软件开发应该对扩展开发，对修改关闭。”这是什么意思呢？这个原则的意思大概就是说你应该允许其他人在不修改已经存在的功能的情况下去增加新功能。

>不友好的

>```php
>abstract class Adapter
>{
>    protected $name;

>    public function getName(): string
>    {
>        return $this->name;
>    }
>}

>class AjaxAdapter extends Adapter
>{
>    public function __construct()
>    {
>       parent::__construct();

>        $this->name = 'ajaxAdapter';
>    }
>}

>class NodeAdapter extends Adapter
>{
>    public function __construct()
>    {
>        parent::__construct();

>        $this->name = 'nodeAdapter';
>    }
>}

>class HttpRequester
>{
>    private $adapter;

>    public function __construct(Adapter $adapter)
>    {
>        $this->adapter = $adapter;
>    }

>    public function fetch(string $url): Promise
>    {
>        $adapterName = $this->adapter->getName();

>        if ($adapterName === 'ajaxAdapter') {
>            return $this->makeAjaxCall($url);
>        } elseif ($adapterName === 'httpNodeAdapter') {
>            return $this->makeHttpCall($url);
>        }
>    }

>    private function makeAjaxCall(string $url): Promise
>    {
>        // request and return promise
>    }

>    private function makeHttpCall(string $url): Promise
>    {
>        // request and return promise
>    }
>}
>```

>友好的:

>```php
>interface Adapter
>{
>    public function request(string $url): Promise;
>}

>class AjaxAdapter implements Adapter
>{
>    public function request(string $url): Promise
>    {
>        // request and return promise
>    }
>}

>class NodeAdapter implements Adapter
>{
>    public function request(string $url): Promise
>    {
>        // request and return promise
>    }
>}

>class HttpRequester
>{
>    private $adapter;

>    public function __construct(Adapter $adapter)
>    {
>        $this->adapter = $adapter;
>    }

>    public function fetch(string $url): Promise
>    {
>        return $this->adapter->request($url);
>    }
>}
>```

- 里氏替换原则 (LSP)

>这本身是一个非常简单的原则却起了一个不太容易理解的名字。这个原则通常的定义是“如果S是T的一个子类，那么对象T可以在没有任何警告的情况下被他的子类替换（例如：对象S可能代替对象T）一些更合适的属性。”好像更难理解了。

>最好的解释就是说如果你有一个父类和子类，那么你的父类和子类可以在原来的基础上任意交换。这个可能还是难以理解，我们举一个正方形-长方形的例子吧。在数学中，一个矩形属于长方形，但是如果在你的模型中通过继承使用了“is-a”的关系就不对了。

>不友好的:

>```php
>class Rectangle
>{
>    protected $width = 0;
>    protected $height = 0;

>    public function render(int $area): void
>    {
>        // ...
>    }

>    public function setWidth(int $width): void
>    {
>        $this->width = $width;
>    }

>    public function setHeight(int $height): void
>    {
>        $this->height = $height;
>    }

>    public function getArea(): int
>    {
>       return $this->width * $this->height;
>    }
>}

>class Square extends Rectangle
>{
>    public function setWidth(int $width): void
>    {
>        $this->width = $this->height = $width;
>    }

>    public function setHeight(int $height): void
>    {
>        $this->width = $this->height = $height;
>    }
>}

>function renderLargeRectangles(array $rectangles): void
>{
>    foreach ($rectangles as $rectangle) {
>        $rectangle->setWidth(4);
>        $rectangle->setHeight(5);
>        $area = $rectangle->getArea(); // BAD: Will return 25 for Square. Should be 20.
>        $rectangle->render($area);
>    }
>}

>$rectangles = [new Rectangle(), new Rectangle(), new Square()];
>renderLargeRectangles($rectangles);
>```

>友好的:

>```php
>abstract class Shape
>{
>    protected $width = 0;
>    protected $height = 0;

>    abstract public function getArea(): int;

>    public function render(int $area): void
>   {
>        // ...
>    }
>}

>class Rectangle extends Shape
>{
>    public function setWidth(int $width): void
>    {
>        $this->width = $width;
>    }

>    public function setHeight(int $height): void
>    {
>        $this->height = $height;
>    }

>    public function getArea(): int
>    {
>        return $this->width * $this->height;
>    }
>}

>class Square extends Shape
>{
>    private $length = 0;

>    public function setLength(int $length): void
>    {
>        $this->length = $length;
>    }

>    public function getArea(): int
>    {
>        return pow($this->length, 2);
>    }
>}
>
>
>function renderLargeRectangles(array $rectangles): void
>{
>    foreach ($rectangles as $rectangle) {
>        if ($rectangle instanceof Square) {
>            $rectangle->setLength(5);
>        } elseif ($rectangle instanceof Rectangle) {
>            $rectangle->setWidth(4);
>            $rectangle->setHeight(5);
>        }
>
>        $area = $rectangle->getArea(); 
>        $rectangle->render($area);
>    }
>}

>$shapes = [new Rectangle(), new Rectangle(), new Square()];
>renderLargeRectangles($shapes);
>```

- 接口隔离原则 (ISP)

>ISP的意思就是说“使用者不应该强制使用它不需要的接口”。

>当一个类需要大量的设置是一个不错的例子去解释这个原则。为了方便去调用这个接口需要做大量的设置，但是大多数情况下是不需要的。强制让他们使用这些设置会让整个接口显得臃肿。

>不友好的:

>```php
>interface Employee
>{
>    public function work(): void;

>    public function eat(): void;
>}

>class Human implements Employee
>{
>    public function work(): void
>    {
>        // ....working
>    }

>    public function eat(): void
>    {
>        // ...... eating in lunch break
>    }
>}

>class Robot implements Employee
>{
>    public function work(): void
>    {
>        //.... working much more
>    }

>    public function eat(): void
>    {
>        //.... robot can't eat, but it must implement this method
>    }
>}
>```

>友好的:

>>并非每一个工人都是职员，但是每一个职员都是工人。

>```php
>interface Workable
>{
>    public function work(): void;
>}

>interface Feedable
>{
>    public function eat(): void;
>}

>interface Employee extends Feedable, Workable
>{
>}

>class Human implements Employee
>{
>    public function work(): void
>    {
>        // ....working
>    }

>    public function eat(): void
>    {
>        //.... eating in lunch break
>    }
>}

>// robot can only work
>class Robot implements Workable
>{
>    public function work(): void
>    {
>        // ....working
>    }
>}
>```

- 依赖反转原则 (DIP)

>这个原则有两个需要注意的地方：
> 1. 高阶模块不能依赖于低阶模块。他们都应该依赖于抽象。
> 2. 抽象不应该依赖于实现，实现应该依赖于抽象。

>第一点可能有点难以理解，但是如果你有使用过像`Symfony`的`PHP`框架，你应该有见到过依赖注入这样的原则的实现。尽管他们是不一样的概念，`DIP`让高阶模块从我们所知道的低阶模块中分离出去。可以通过`DI`这种方式实现。一个巨大的好处在于它解耦了不同的模块。耦合是一个非常不好的开发模式，因为它会让你的代码难以重构。

>不友好的:

>```php
>class Employee
>{
>    public function work(): void
>    {
>        // ....working
>    }
>}

>class Robot extends Employee
>{
>    public function work(): void
>    {
>        //.... working much more
>    }
>}

>class Manager
>{
>    private $employee;

>    public function __construct(Employee $employee)
>    {
>        $this->employee = $employee;
>    }

>    public function manage(): void
>    {
>        $this->employee->work();
>    }
>}
>```

>友好的

>```php
>interface Employee
>{
>    public function work(): void;
>}

>class Human implements Employee
>{
>    public function work(): void
>    {
>        // ....working
>    }
>}

>class Robot implements Employee
>{
>    public function work(): void
>    {
>        //.... working much more
>    }
>}

>class Manager
>{
>    private $employee;

>    public function __construct(Employee $employee)
>    {
>        $this->employee = $employee;
>    }

>    public function manage(): void
>    {
>        $this->employee->work();
>    }
>}
>```

## 别重复你的代码 (DRY)

>尝试去研究[DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)原则。

>尽可能别去复制代码。复制代码非常不好，因为这意味着将来有需要修改的业务逻辑时你需要修改不止一处。

>想象一下你在经营一个餐馆并且你需要经常整理你的存货清单：你所有的土豆，洋葱，大蒜，辣椒等。如果你有多个列表来管理进销记录，当你用其中一些土豆做菜时你需要更新所有的列表。如果你只有一个列表的话只有一个地方需要更新!

>大多数情况下你有重复的代码是因为你有超过两处细微的差别，他们大部分都是相同的，但是他们的不同之处又不得不让你去分成不同的方法去处理相同的事情。移除这些重复的代码意味着你需要创建一个可以用一个方法/模块/类来处理的抽象。

>使用一个抽象是关键的，这也是为什么在类中你要遵循`SOLID`原则的原因。一个不优雅的抽象往往比重复的代码更糟糕，所以要谨慎使用！说了这么多，如果你已经可以构造一个优雅的抽象，那就赶紧去做吧！别重复你的代码，否则当你需要修改时你会发现你要修改许多地方。

>不友好的:

>```php
>function showDeveloperList(array $developers): void
>{
>    foreach ($developers as $developer) {
>        $expectedSalary = $developer->calculateExpectedSalary();
>        $experience = $developer->getExperience();
>        $githubLink = $developer->getGithubLink();
>        $data = [
>            $expectedSalary,
>            $experience,
>            $githubLink
>        ];

>        render($data);
>    }
>}

>function showManagerList(array $managers): void
>{
>    foreach ($managers as $manager) {
>        $expectedSalary = $manager->calculateExpectedSalary();
>        $experience = $manager->getExperience();
>        $githubLink = $manager->getGithubLink();
>        $data = [
>            $expectedSalary,
>            $experience,
>            $githubLink
>        ];

>        render($data);
>    }
>}
>```

>友好的:

>```php
>function showList(array $employees): void
>{
>    foreach ($employees as $employee) {
>        $expectedSalary = $employee->calculateExpectedSalary();
>        $experience = $employee->getExperience();
>        $githubLink = $employee->getGithubLink();
>        $data = [
>            $expectedSalary,
>            $experience,
>            $githubLink
>        ];

>        render($data);
>    }
>}
>```

>非常优雅的:

>>如果能更简洁那就更好了。

>```php
>function showList(array $employees): void
>{
>    foreach ($employees as $employee) {
>        render([
>            $employee->calculateExpectedSalary(),
>            $employee->getExperience(),
>            $employee->getGithubLink()
>        ]);
>    }
>}
>```

## 原文地址
[clean-code-php](https://github.com/jupeter/clean-code-php)


**文章首发地址：[我的博客](http://www.hellonine.top)**
