---
layout: post
title: Design Pattern in php
comments: false
categories: ["php"]
tags: ["php", "architectural"]
---

Design Pattern có thể thấy là một cấp độ khác trong lập trình ứng dụng, nó là các mẫu thiết kế có sẵn được đúc kết bởi rất nhiều các lập trình viên kinh nghiệm nhằm giải quyết các vấn đề chung. Áp dụng các mẫu có sẵn này vào lập trình giúp ứng dụng dễ duy trì và cập nhật, tuy nhiên nó cũng làm cho những ai không am hiểu về Design Pattern cảm thấy lúng túng. Design Pattern là kiến thức có thể dùng chung cho nhiều các ngôn ngữ hướng đối tượng khác như C#, Java..., chúng được phân ra làm nhiều loại theo chức năng:

- *Creational Design Pattern:* các mẫu này chuyên sử dụng cho khởi tạo đối tượng có thể kể đến như Singleton, Factory, Builder, Prototype...
- *Strutural Design Pattern:* các mẫu liên quan đến cấu trúc, kết cấu các đối tượng, ví dụ Composite, Decorator, Facade, Adapter, Proxy...
- *Behavioral Design Pattern:* các mẫu giải quyết các vấn đề về hành vi đối tượng như Strategy, Iterator, State, Observer...
- *Architectural Design Pattern:* các mẫu liên quan đến kiến trúc ứng dụng: MVC, SOA (Service-Oriented Architecture), microservice...
Tổng cộng có khoảng hơn 20 Design Pattern được áp dụng trong lập trình hướng đối tượng, để có thể nắm bắt được hết cũng mất kha khá thời gian. Trong khuôn khổ bài viết, bạn sẽ làm quen với một số pattern tiêu biểu, qua đó nắm vững được Design Pattern là gì? và Cách áp dụng Design Pattern trong phát triển ứng dụng.

## 1. Singleton Pattern
Tần suất sử dụng: 4/5. Singleton Pattern được sử dụng khá nhiều trong lập trình.

Singleton pattern thuộc về Creational Design Pattern là một mẫu áp dụng cho việc khởi tạo đối tượng, áp dung pattern này khi ứng dụng của bạn muốn tạo ra một thực thể duy nhất từ một class và dùng chung nó cho nhiều trường hợp. Ví dụ, website cần một đối tượng kết nối đến database nhưng chỉ cần duy nhất một đối tượng cho toàn bộ ứng dụng, sử dụng Singleton Pattern sẽ giải quyết được vấn đề này. Để bắt đầu bạn sử dụng một thuộc tính static để đảm bảo rằng chỉ có một thực thể của lớp này tồn tại.

<pre>
	<code>
		class SomeClass {
		  static private $_instance = NULL;
		}
	</code>
</pre>

Trong phần lập trình hướng đối tượng, thuộc tính static được chia sẻ giữa các đối tượng của class, do đó nếu đã có một thực thể của class thì tất cả sẽ tham chiếu đến class đó có thể sử dụng thuộc tính này. Bước tiếp theo là tạo ra một phương thức sẽ tạo ra một instance của class nếu nó chưa tồn tại và trả về instance đó.

<pre>
	<code>
class SomeClass {
  static private $_instance = NULL;
  static function getInstance() {
    if (self::$_instance == NULL) {
      self::$_instance = new SomeClass();
    }
    return self::$_instance;
  }
}
</code>
</pre>

Singleton pattern thường sử dụng một phương thức với cái tên `getInstance()` để kiểm tra thuộc tính `$_instance`, nếu nó có giá trị NULL thì sẽ tạo ra một instance và gán cho thuộc tính này, kết quả là một instance được trả về. Như vậy, class này có thể được sử dụng như sau:

<pre>
$obj1 = SomeClass::getInstance();
</pre>

Trong đối tượng đầu tiên của ứng dụng, thực thể được tạo ra và được gán cho thuộc tính private bên trong nó. Chúng ta cùng bắt đầu với ví dụ về Singleton pattern, tạo ra một file để thiết lập cấu hình. Bạn có thể tham khảo cách tạo môi trường thực hành với PHP giúp thực hiện các đoạn code test dễ dàng hơn. Tạo một file là `singleton.php` trong thư mục `oop/pattern` với nội dung.

<pre>
	<code>
    /* The Config class.
     * The class contains two attributes: $_instance and $settings.
     * The class contains four methods:
     * - __construct()
     * - getInstance()
     * - set()
     * - get()
     */
    class Config {

       static private $_instance = NULL;
       private $_settings = array();

       // Private methods cannot be called
       private function __construct() {}
       private function __clone() {}

       // Phương thức này trả về một thực thể của class
       static function getInstance() {
          if (self::$_instance == NULL) {
             self::$_instance = new Config();
          }
          return self::$_instance;
       }
       // Phương thức này thiết lập cấu hình
       function set($index, $value) {
          $this->_settings[$index] = $value;
       }
       // Phương thức này lấy thiết lập cấu hình
       function get($index) {
          return $this->_settings[$index];
       }
    }
    // Tạo một đối tượng Config
    $config = Config::getInstance();

    // Thiết lập các giá trị trong thuộc tính cấu hình
    $config->set('database_connected', 'true');

    // In giá trị cấu hình
    echo '<p>$config["database_connected"]: ' . $config->get('database_connected') . '</p>';

    // Tạo một đối tượng thứ hai
    $test = Config::getInstance();
    echo '<p>$test["database_connected"]: ' . $test->get('database_connected') . '</p>';

    // Xóa các đối tượng sau khi sử dụng
    unset($config, $test);
	</code>
</pre>

Bạn thấy đấy, mỗi khi tạo ra một đối tượng của class Config nó sẽ kiểm tra và chỉ tạo thực thể (instance) khi chưa có thực thể nào được tạo. Biến $test sẽ cùng tham chiếu đến đối tượng từ class Config do đó các thiết lập cấu hình có thể chia sẻ lại.

## 2. Factory Pattern
Tần suất sử dụng: 5/5, Factory Pattern được sử dụng cực nhiều trong lập trình.

Factory pattern cũng như Singleton pattern thuộc về dạng Creational Design Pattern, tuy nhiên có một chút khác biệt. Singleton pattern áp dụng để tạo và quản lý một đối tượng duy nhất của một class trong khi Factory pattern được sử dụng để có thể tạo ra nhiều đối tượng khác nhau từ nhiều class. Tại sao cần Factory pattern trong khi chúng ta có thể tạo được đối tượng từ Singleton Pattern? Vấn đề là ở chỗ đôi khi chúng ta không biết trước được là muốn tạo đối tượng từ class cụ thể nào, do đó việc chỉ định class để tạo ra đối tượng cần phải được gán động. Factory pattern cần sử dụng một lớp trìu tượng (abstract class) và có một phương thức static, được quy ước tên là Create(), factory(), factoryMethod() hoặc createInstance(). Phương thức này có một tham số để nhận biết dạng đối tượng cần tạo và trả về đối tượng này.

<pre>
	<code>
static function Create($type) {
  // Kiểm tra tham số $type và tạo đối tượng từ class tương ứng để trả về.
  return new SomeClassType();
}
</code>
</pre>

Có vẻ chưa hiểu lắm, không sao, ví dụ tiếp theo sẽ giúp bạn hiểu ngay thôi. Chúng ta quay trở lại ví dụ về các hình chữ nhật (rectangle), hình tam giác (triangle). Chúng ta tạo ra file `factory.php` trong thư mục `oop\pattern` với nội dung như sau:

<pre>
	<code>
    #------------ ĐỊNH NGHĨA CLASS ----------------------#
    /* Định nghĩa class ShapeFactory sử dụng Factory pattern
     * The class contains no attributes.
     * The class contains one method: Create().
     */
    abstract class ShapeFactory {
        // Phương thức static để tạo đối tượng
        static function Create($type, array $sizes) {
            // Xác định dạng đối tượng theo tham số nhận vào
            switch ($type) {
                case 'rectangle':
                    return new Rectangle($sizes[0], $sizes[1]);
                    break;
                case 'triangle':
                    return new Triangle($sizes[0], $sizes[1], $sizes[2]);
                    break;
            }
        }
    }

    /* Định nghĩa lớp trìu tượng Shape
     * Lớp Shape không có thuộc tính
     * Lớp Shape có 2 phương thức trìu tượng:
     * - getArea()
     * - getPerimeter()
    */
    abstract class Shape {
        abstract protected function getArea();
        abstract protected function getPerimeter();
    }

    /* Định nghĩa lớp Triangle
     * Lớp Triangle có 2 thuộc tính:
     * - private $_sides (array)
     * - private $_perimeter (number)
     * Lớp Triangle có 3 phương thức:
     * - _ _construct()
     * - getArea()
     * - getPerimeter()
     */
    class Triangle extends Shape {
        private $_sides = array();
        private $_perimeter = NULL;
        function __construct($s0 = 0, $s1 = 0, $s2 = 0) {
            $this->_sides[] = $s0;
            $this->_sides[] = $s1;
            $this->_sides[] = $s2;

            // Tính toán và thiết lập chu vi hình tam giác
            $this->_perimeter = array_sum($this->_sides);
        }
        // Phương thức tính diện tích hình tam giác từ chu vi và các cạnh
        public function getArea() {
            return (SQRT(($this->_perimeter/2) * (($this->_perimeter/2) - $this->_sides[0]) * (($this->_perimeter/2) - $this->_sides[1]) * (($this->_perimeter/2) - $this->_sides[2])));
        }
        // Phương thức lấy chu vi hình tam giác
        public function getPerimeter() {
            return $this->_perimeter;
        }
    }

    /* Định nghĩa class Rectangle
    * Các thuộc tính của class: width(chiều rộng), height(chiều cao).
    * Các phương thức của lớp:
    * - setSize()
    * - getArea()
    * - getPerimeter()
    * - isSquare()
    */
    class Rectangle {
        // Khai báo các thuộc tính
        public $width = 0;
        public $height = 0;

        // Hàm khởi tạo
        function __construct($w = 0, $h = 0) {
           $this->width = $w;
           $this->height = $h;
        }

        // Phương thức này thiết lập các kích thước của hình chữ nhật
        function setSize($w = 0, $h = 0) {
            $this->width = $w;
            $this->height = $h;
        }

        // Phương thức này tính diện tích hình chữ nhật
        function getArea() {
            return ($this->width * $this->height);
        }

        // Phương thức này tính chu vi hình chữ nhật
        function getPerimeter() {
            return ( ($this->width + $this->height) * 2 );
        }

        // Phương thức này kiểm tra xem hình chữ nhật này có phải là hình vuông
        function isSquare() {
            if ($this->width == $this->height) {
                return true; // Hình chữ nhật
            } else {
                return false; // Không phải hình chữ nhật
            }
        }
    }
    #------------ KẾT THÚC ĐỊNH NGHĨA CLASS ----------------------#

    if (isset($_GET['shape'], $_GET['dimensions'])) {
        // Tạo ra một đối tượng từ với thông số từ query string
        $obj = ShapeFactory::Create($_GET['s'], $_GET['d']);

        echo "<h2>Tạo ra hình {$_GET['shape']}:</h2>";
        echo '<p>Diện tích hình: ' . $obj->getArea() . '</p>';
        echo '<p>Chu vi hình: ' . $obj->getPerimeter() . '</p>';
    } else {
        echo '<p>Cần cung cấp hình dạng và kích thước!</p>';
    }

    // Xóa đối tượng
    unset($obj);
</code>
</pre>

Trong ví dụ này chúng ta đã tạo ra lớp ShapeFactory áp dụng Factory Pattern, nó sẽ nhận các tham số đầu vào để tạo ra một thực thể tương ứng với class cần tạo. Tiếp đó là các class Rectangle (hình chữ nhật) và Triangle (hình tam giác) được mở rộng từ lớp trìu tượng Shape (hình). Tiếp đó, chúng ta sử dụng các query string của URL để đưa vào dạng hình và kích thước hình cần tạo:

- `s` - shape là hình cần tạo: rectangle hoặc triangle
- `d` - dimension là kích thước của hình, nếu là tam giác nhập 3 cạnh, nếu là hình chữ nhật nhập 2 cạnh.

Như vậy với việc áp dụng Factory Pattern chúng ta đã tạo được các đối tượng khác nhau tùy thuộc vào từng tình huống mà không biết trước được như Singleton Pattern.

## 3. Composite pattern
Tần suất sử dụng 4/5, Composite pattern được sử dụng khá nhiều.

Hai pattern đầu thuộc về Creational Design Pattern, tiếp theo chúng ta sẽ làm quen với Composite Pattern nó là Structural Design Pattern, là mẫu thiết kế liên quan đến cấu trúc, kết cấu của các đối tượng. Nó được áp dụng để cấu trúc một class theo tiêu chuẩn hoặc điều chỉnh cấu trúc một class đang tồn tại. Trong thực tế, một form HTML có thể chứa một hoặc nhiều các thành phần form, mỗi thành phần này sẽ có cùng các hành vi như hiển thị, kiểm tra dữ liệu, hiển thị lỗi… Nếu không áp dụng các mẫu thì chúng ta sẽ lặp đi lặp lại code rất nhiều và giải pháp cho vấn đề này là ứng dụng Composite pattern. Chúng ta tạo ra một abstract class:

<pre>
	<code>
abstract class FormComponent {
  abstract function add (FormComponent $obj);
  abstract function remove (FormComponent $obj);
  abstract function display();
  abstract function validate();
  abstract function showError();
}
</code>
</pre>

Lớp trìu tượng ở trên có sử dụng type hinting (cách xác định dạng dữ liệu cho tham số), hai phương thức đầu chính là cách mà Composite pattern sử dụng. Mỗi lớp con sẽ kế thừa từ lớp cha và nó cần phải định nghĩa các phương thức trìu tượng được implement từ lớp cha trìu tượng. Ví dụ:
<pre>
	<code>
class Form extends FormComponent {
  private $_elements = array();
  function add(FormComponent $obj) {
    $this->_elements[] = $obj;
  }
  function display() {
    // Display the entire form.
  }
}
class FormElement extends FormComponent {
  function add(FormComponent $obj) {
    return $obj; // Or false.
  }
  function display() {
    // Display the element.
  }
}
</code>
</pre>

Class Form định nghĩa phương thức add() được implement từ FormConponent, nó cho phép bạn thêm thành phần vào form:

<pre>
	<code>
$form = new Form();
$email = new FormElement();
$form->add($email);
</code>
</pre>

Chú ý là FormElement cũng định nghĩa phương thức add(), nhưng phương thức này không làm gì cả, vì chúng ta không cần thêm thành phần form vào một thành phần form. Thay vào đó, phương thức add() này trả về đối tượng được thêm vào hoặc trả về một giá trị hoặc bung ra một lỗi. Với ví dụ ở dạng mẫu trên, chúng ta vẫn chưa thật sự hiểu rõ về Composite pattern. Một ví dụ cụ thể tiếp theo sẽ giúp bạn hiểu chi tiết. Ví dụ dưới đây về một ứng dụng quản lý các công việc cần làm của một nhóm và của từng thành viên trong nhóm.

<pre>
	<code>
    Ví dụ về Composite pattern
    <?php
    #------------ ĐỊNH NGHĨA CLASS ----------------------#
    /* Định nghĩa class WorkUnit sử dụng Composite pattern
     * Lớp có 2 thuộc tính: tasks, name.
     * Lớp có 5 phương thức: __construct(), getName(), add(), remove(), assignTask(), completeTask().
     */
    abstract class WorkUnit {
        // Các tác vụ cần làm
        protected $tasks = array();
        // Lưu tên nhân viên hoặc tên nhóm
        protected $name = NULL;

        function __construct($name) {
            $this->name = $name;
        }
        function getName() {
            return $this->name;
        }
        // Các phương thức trìu tượng cần thực hiện
        abstract function add(Employee $e);
        abstract function remove(Employee $e);
        abstract function assignTask($task);
        abstract function completeTask($task);
    }

    /* Lớp Team mở rộng từ lớp WorkUnit.
     * Lớp có 1 thuộc tính: _employees.
     * Lớp có 1 phương thức: getCount().
     */
    class Team extends WorkUnit {
        // Lưu các thành viên của nhóm
        private $_employees = array();

        // Thực hiện các phương thức trìu tượng
        function add(Employee $e) {
            $this->_employees[] = $e;
            echo "<p>{$e->getName()} gia nhập nhóm {$this->getName()}.</p>";
        }
        function remove(Employee $e) {
            $index = array_search($e, $this->_employees);
            unset($this->_employees[$index]);
            echo "<p>{$e->getName()} bị đuổi khỏi nhóm {$this->getName()}.</p>";
        }
        function assignTask($task) {
            $this->tasks[] = $task;
            echo "<p>Một tác vụ được gán cho nhóm {$this->getName()}. Nó có thể hoàn thành dễ dàng với {$this->getCount()} thành viên.</p>";
        }
        function completeTask($task) {
            $index = array_search($task, $this->tasks);
            unset($this->tasks[$index]);
            echo "<p>Nhiệm vụ '$task' đã hoàn thành bởi nhóm {$this->getName()}.</p>";
        }
        // Phương thức trả về số thành viên trong nhóm
        function getCount() {
            return count($this->_employees);
        }
    }

    /* Lớp Employee mở rộng từ lớp WorkUnit
     * Lớp không có thuộc tính và phương thức nào
     */
    class Employee extends WorkUnit {
        // Empty functions
        function add(Employee $e) {
            return false;
        }
        function remove(Employee $e) {
            return false;
        }

        // Thực hiện phương thức trìu tượng
        function assignTask($task) {
            $this->tasks[] = $task;
            echo "<p>Một tác vụ được gán cho {$this->getName()}. Tác vụ này phải được hoàn thành bởi một mình {$this->getName()}.</p>";
        }
        function completeTask($task) {
            $index = array_search($task, $this->tasks);
            unset($this->tasks[$index]);
            echo "<p>Nhiệm vụ '$task' được hoàn thành bởi {$this->getName()}. </p>";
        }

    }
    #------------ KẾT THÚC ĐỊNH NGHĨA CLASS ----------------------#

    // Tạo đối tượng
    $fontend = new Team('Fontend');
    $kulit = new Employee('Kulit');
    $evan = new Employee('Evan You');
    $taylor = new Employee('Taylor Otwell');

    // Gán nhân viên vào nhóm fontend
    $fontend->add($kulit);
    $fontend->add($evan);
    $fontend->add($taylor);

    // Gán các tác vụ cho nhóm và nhân viên
    $fontend->assignTask('Xây dựng website');
    $evan->assignTask('Xây dựng fontend');
    // Hoàn thành một tác vụ
    $fontend->completeTask('Xây dựng website');

    // Chuyển Taylor Otwell sang nhóm backend
    $fontend->remove($taylor);

    // Xóa các đối tượng
    unset($fontend, $kulit, $evan, $taylor);
    </code>
</pre>

Trong ví dụ này có nhóm và nhân viên, nhóm cũng có công việc của nhóm và nhân viên có công việc của nhân viên. Áp dụng Composite Pattern giúp chúng ta nhìn nhận Nhóm cũng giống như Nhân viên, và các xử lý trên Nhóm và Nhân viên là tương tự nhau.

## 4. Strategy pattern
Tần suất sử dụng: 4/5, Strategy pattern được sử dụng khá nhiều trong lập trình.

Mỗi dạng Design Pattern sẽ giới thiệu một Pattern tiêu biểu và Behavioral Design Pattern sẽ được bàn luận với pattern cuối cùng là Strategy pattern, mẫu này áp dụng khi làm việc với các hành vi của đối tượng, nó "đánh dấu" cách ứng dụng chạy. Factory pattern có thể thay đổi dạng đối tượng thì Strategy có thể thay đổi thuật toán (hành vi) của đối tượng. Strategy rất hữu ích trong một số trường hợp nơi mà các class là tương tự nhau nhưng không liên quan và khác nhau về hành vi. Ví dụ, chúng ta cần lọc các chuỗi, các bộ lọc khác nhau có thể sử dụng:

- Loại bỏ các thẻ HTML
- Loại bỏ các từ ngữ không phù hợp.
- Loại bỏ các ký tự sử dụng để gửi thư rác thông qua hình thức liên lạc
Thông thường chúng ta sẽ làm 3 giải pháp và áp dụng chúng vào các chuỗi cần lọc. Các bộ lọc có thể được áp dụng một cách khác nhau. Đầu tiên, định nghĩa `interface` với các tính năng cần thiết

<pre>
	<code>
interface Filter {
  function filter($str);
}
</code>
</pre>

Xác định dạng bộ lọc sau đó implement các phiên bản thích hợp của phương thức trong interface:

<pre>
	<code>
class HtmlFilter implements Filter {
  function filter($str) {
    // Loại bỏ mã HTML
    return $str;
  }
}
class SwearFilter implements Filter {
  function filter($str) {
    // Loại bỏ các từ ngữ không phù hợp.
    return $str;
  }
}
</code>
</pre>

Cuối cùng, sử dụng bộ lọc trong một class khác.

<pre>
	<code>
class FormData {
  private $_data = NULL;
  function __construct($input) {
    $this->_data = $input;
  }
  function process(Filter $type) {    
    $this->_data = $type->filter($this->_data);
  }
}
</code>
</pre>

Phương thức process() nhận một đối tượng dạng Filter và thông qua đó dữ liệu được lọc.

<pre>
	<code>
$form = new FormData($someUserInput);
if (!$allowHTML) {
  $form->process(new HtmlFilter());
}
if (!allowSwear) {
  $form->process(new SwearFilter());
}
</code>
</pre>

OK, lý thuyết là như vậy, chúng ta cùng áp dụng vào thực tế, ví dụ về một ứng dụng quản lý sinh viên. Tạo ra file strategy.php trong oop\pattern với nội dung:

<pre>
	<code>
    // Interface Sort định nghĩa phương thức sort()
    interface iSort {
        function sort(array $list);
    }

    // Lớp MultiAlphaSort sắp xếp mảng đa chiều chứa ký tự
    class MultiAlphaSort implements iSort {
        // Cách sắp xếp: tăng dần, giảm dần
        private $_order;

        // Sort index:
        private $_index;

        function __construct($index, $order = 'ascending') {
            $this->_index = $index;
            $this->_order = $order;
        }

        // Phương thức thực hiện sắp xếp
        function sort(array $list) {

            // Change the algorithm to match the sort preference:
            if ($this->_order == 'ascending') {
                uasort($list, array($this, 'ascSort'));
            } else {
                uasort($list, array($this, 'descSort'));
            }
            return $list;
        }
        // Phương thức so sánh hai giá trị
        function ascSort($x, $y) {
            return strcasecmp($x[$this->_index], $y[$this->_index]);
        }
        function descSort($x, $y) {
            return strcasecmp($y[$this->_index], $x[$this->_index]);
        }
    }

    // Class MultiNumberSort sắp xếp một mảng đa chiều
    class MultiNumberSort implements iSort {
        // Cách sắp xếp
        private $_order;

        // Sort index
        private $_index;

        function __construct($index, $order = 'ascending') {
            $this->_index = $index;
            $this->_order = $order;
        }

        // Thực hiện sắp xếp
        function sort(array $list) {
            // Thay đổi thuật toán phù hợp với thiết lập
            if ($this->_order == 'ascending') {
                uasort($list, array($this, 'ascSort'));
            } else {
                uasort($list, array($this, 'descSort'));
            }
            return $list;
        }
        // Phương thức so sánh hai giá trị
        function ascSort($x, $y) {
            return ($x[$this->_index] > $y[$this->_index]);
        }
        function descSort($x, $y) {
            return ($x[$this->_index] < $y[$this->_index]);
        }
    }

    /* Lớp StudentsList
     * Lớp có 1 thuộc tính: _students.
     * Lớp có 3 phương thức:
     * - __construct()
     * - sort()
     * - display()
     */
    class StudentsList {
        // Danh sách sinh viên được sắp xếp 
        private $_students = array();

        function __construct($list) {
            $this->_students = $list;
        }

        // Thực hiện sắp xếp sử dụng một thực thi từ iSort
        function sort(iSort $type) {
            $this->_students = $type->sort($this->_students);
        }

        // Hiển thị danh sách sinh viên dạng HTML
        function display() {
            echo '<ol>';
            foreach ($this->_students as $student) {
                echo "<li>{$student['last_name']} {$student['first_name']} : {$student['grade']}</li>";
            }
            echo '</ol>';
        }

    }

    // Tạo mảng sinh viên, mỗi sinh viên có cấu trúc studentID => array('first_name' => 'First Name', 'last_name' => 'Last Name', 'grade' => XX.X)
    $students = array(
        256 => array('first_name' => 'Tuấn', 'last_name' => 'Trần Đăng', 'grade' => 98.5),
        2   => array('first_name' => 'An', 'last_name' => 'Nguyễn Xuân', 'grade' => 85.1),
        9   => array('first_name' => 'Dương', 'last_name' => 'Nguyễn Ngọc', 'grade' => 94.0),
        364 => array('first_name' => 'Chiến', 'last_name' => 'Hoàng Văn', 'grade' => 85.1),
        68  => array('first_name' => 'Phương', 'last_name' => 'Trần Thanh', 'grade' => 74.6)
    );

    // Tạo đối tượng
    $list = new StudentsList($students);

    // Hiển thị mảng trước khi sắp xếp
    echo '<h2>Danh sách gốc</h2>';
    $list->display();

    // Sắp xếp theo tên
    $list->sort(new MultiAlphaSort('first_name'));
    echo '<h2>Danh sách sắp xếp theo tên</h2>';
    $list->display();

    // Sắp xếp theo điểm
    $list->sort(new MultiNumberSort('grade', 'descending'));
    echo '<h2>Danh sách sắp xếp theo điểm</h2>';
    $list->display();

    // Xóa đối tượng
    unset($list);
	</code>
</pre>

Trong ví dụ này chúng ta thấy rằng cùng là hành vi `sort()` nhưng nó khác nhau ở các thời điểm khác nhau, chỗ thì sắp xếp tên, chỗ thì sắp xếp điểm. Như vậy việc áp dụng `Strategy Pattern` giúp chúng ta thay đổi được hành vi của một đối tượng trong thời gian chạy.

## 5. Model - View - Controller MVC
Tần suất sử dụng 5/5, MVC pattern được dùng thường xuyên trong các framework hiện nay. MVC là một pattern dạng Architectural Design Pattern áp dụng khi xử lý các vấn đề liên quan đến kiến trúc ứng dụng.

Model là nơi lưu trữ dữ liệu người dùng, nó cho phép truy xuất dữ liệu để hiển thị hoặc thu thập dữ liệu. Model là cầu nối giữa thành phần View và Controller trong mẫu thiết kế này. Mục đích quan trọng nhất của nó là kết nối cơ sở dữ liệu, xử lý dữ liệu và chuẩn bị dữ liệu để chuyển đến các thành phần khác.

View là nơi dữ liệu được hiển thị, trong ứng dụng web View là một phần của hệ thống, nơi mà các mã HTML được sinh ra và hiển thị. View cũng là nơi nhận tương tác trực tiếp từ người dùng. Một vấn đề quan trọng là View không được lấy dữ liệu trực tiếp từ Controller mà phải thông qua Model.

Controller quản lý dữ liệu người dùng nhập vào và cập nhật sang Model, Controller chỉ được sử dụng khi có tương tác của người dùng, còn không nó không có giá trị. Controller chỉ đơn giản là thu thập thông tin và sau đó chuyển dữ liệu sang Model, nó không chứa bất kỳ logic nghiệp vụ nào. Controller kết nối với duy nhất một View và một Model tạo thành hệ thống dự liệu chạy theo một chiều (one way data flow system).

Chúng ta cùng đến với một ví dụ cụ thể để thấy được cách áp dụng MVC trong thực tế, trong ví dụ này chúng ta sẽ xây dựng một ứng dụng quản lý game thủ đế chế. Tạo ra thư mục `OOP/MVC` để bắt đầu bạn nhé. Đầu tiên chúng ta tạo ra file `Model.php` với nội dung:

<pre>
	<code>
class Model {
    private $gamers;

    public function __construct() {
        $this->gamers[] = array("name" => "Chim sẻ đi nắng", "adress" => "Đan Phượng, Hà Tây", "city" => "Hà Nội");
        $this->gamers[] = array("name" => "Hồng Anh", "adress" => "Việt Trì, Phú Thọ", "city" => "Phú Thọ");
        $this->gamers[] = array("name" => "Gunny", "adress" => "Nhổn, Hà Nội", "city" => "Hà Nội");
        $this->gamers[] = array("name" => "Bibi", "adress" => "Hà Nội", "city" => "Hà Nội");
        $this->gamers[] = array("name" => "Văn Sự", "adress" => "Hoằng Hóa, Thanh Hóa", "city" => "Thanh Hóa");
    }

    public function getAllGamers() {
        return $this->gamers;
    }

    public function getGamerByName($gamerName) {
        $key = array_search($gamerName, array_column($this->gamers, 'name'));
        return $this->gamers[$key];
    }

    public function getGamersByCity($cityName) {
        $keys = array_keys(array_column($this->gamers, 'city'), $cityName);
        $cityarray = array();
        foreach ($keys as $value) {
            $cityarray[] = $this->gamers[$value];
        }
        return $cityarray;
    }

    public function addGamer($name, $adress, $city) {
        $this->gamers[] = array("name" => $name, "adress" => $adress, "city" => $city);
    }
}
</code>
</pre>

Tiếp theo chúng ta tạo ra View.php với nội dung:

<pre>
	<code>
	<div class="container">
	    <h2><span class="glyphicon glyphicon-user"></span>Danh sách game thủ</h2>
	    <div class="row">
	        <div class="col-md-3">
	        <ul>
	            <?php
	            foreach ($gamers as $g) {
	                echo '<li>';
	                echo '<a href="', $_SERVER['SCRIPT_NAME'], '/PageController/getGamerByName/', $g['name'], '">', $g['name'], '</a><br>';
	                echo $g['adress'], '<br>';
	                echo '--<a href="', $_SERVER['SCRIPT_NAME'], '/PageController/getGamersByCity/', $g['city'], '">', $g['city'], '</a>';
	                echo '</li>';
	            }
	            ?>
	        </ul>
	        </div><!--end col-->
	        <div class="col-md-5">
	        <ul>
	            <?php
	            if (isset($gamer)) {
	                echo 'Thông tin chi tiết về game thủ:';
	                echo '<li>';
	                echo '<a href="', $_SERVER['SCRIPT_NAME'], '/PageController/getGamerByName/', $gamer['name'], '">', $gamer['name'], '</a><br></li>';
	                echo '<li>', $gamer['adress'], '</li>';
	                echo '<li>', $gamer['city'], '</li>';
	            }
	            if (isset($gamersInCities)) {
	                echo 'Các game thủ ở: <b>', $gamersInCities[0]['city'], '</b>';
	                foreach ($gamersInCities as $g) {
	                    echo '<li>';
	                    echo '<a href="', $_SERVER['SCRIPT_NAME'], '/PageController/getGamersByCity/', $g['name'], '">', $g['name'], ' </a>';
	                    echo $g['adress'], ' ';
	                    echo $g['city'];
	                    echo '</li>';
	                }
	            }
	            ?>
	        </ul>
	        </div><!--end col-->
	    </div><!--end row-->
	</div>
	<code>
</pre>

Cuối cùng chúng ta tạo ra Controller.php:

<pre>
	<code>
include_once './Model.php';

class Controller {
    private $data;
    private $model;

    public function handle() {
        $this->model = new Model();

        // Luôn lấy dữ liệu tất cả các game thủ và đưa vào thuộc tính data
        $this->assign("gamers", $this->model->getAllGamers());

        // Tùy thuộc vào đường dẫn lấy các danh sách game thủ theo bộ lọc
        if (isset($_SERVER['PATH_INFO'])) {
            // Ví dụ: index.php/PageController/getPersonByName/Chim sẻ đi nắng
            // Convert chuỗi ký tự thành mảng, giá trị thứ 2 của mảng chứa action, giá trị thứ 3 chứa thông tin của action
            $pathinfo = explode("/", $_SERVER['PATH_INFO']);

            switch ($pathinfo[2]) {
                case "getGamerByName";
                    // $arr[3]="Chim sẻ đi nắng"
                    $this->getGamerByName(urldecode(trim($pathinfo[3])));
                    break;
                case "getGamersByCity":
                    // $arr[3]="Hà Nội"
                    $this->getGamersByCity(urldecode(trim($pathinfo[3])));
                    break;
                default:
                    $this->display("./View.php");
            }
        } else {  
            $this->display("./View.php");
        }
    }

    public function getGamerByName($name) {
        $this->assign("gamer", $this->model->getGamerByName($name));
        $this->display("./View.php");
    }

    public function getGamersByCity($city) {
        $this->assign("gamersInCities", $this->model->getGamersByCity($city));
        $this->display("./View.php");
    }

    private function assign($key,$value){
        $this->data[$key]=$value;
    }

    private function display($htmlPage){
        extract($this->data);        
        include_once $htmlPage;
    }

}
</code>
</pre>

Như vậy, chúng ta đã có đủ 3 thành phần Model, View và Controller, tiếp theo chúng ta sử dụng cả 3 thành phần này để xử lý ứng dụng. Tạo file index.php là nơi tiếp nhận yêu cầu từ người dùng:

<pre>
	<code>
include('./Controller.php');
$controller = new Controller();
$controller->handle();
</code>
</pre>

File này chỉ đơn giản là tạo ra một instance từ lớp Controller. Bạn có thể xem lại sơ đồ MVC ở đầu phần này, đây chính là nơi người dùng gửi yêu cầu HTTP đến hệ thống. Tiếp theo, Controller sẽ thực hiện gửi yêu cầu (gọi phương thức lấy dữ liệu từ Model) đến Model và Model trả về các dữ liệu cần thiết, các yêu cầu này phụ thuộc vào nhập liệu của người dùng là tham số thứ 2 trong đường dẫn, từ đó Controller có thể gọi đến các phương thức khác nhau của Model như getAllGamers, getGamerByName, getGamersByCity. Sau khi có dữ liệu trả về Controller gửi dữ liệu đến View thông qua việc gọi `$this->display("./View.php");` và View trả về định dạng HTML để hiển thị cho người dùng. 

## 6. Lời kết

*Design Pattern* được sử dụng để giải quyết các vấn đề chung, nó không phải được áp dụng để làm phức tạp ứng dụng, do vậy việc hiễu rõ Design Pattern là gì và cách sử dụng Design Pattern như thế nào? là rất quan trọng. Design Pattern là một con dao hai lưỡi, nó có thể giải quyết những vấn đề phức tạp nhưng cũng có thể làm phức tạp một vấn đề đơn giản. Trong một bài viết ngắn không thể tường tận mọi vấn đề của thiết kế mẫu trong lập trình, chúng ta sẽ còn bàn luận lại vấn đề này một cách chi tiết hoặc ở một góc nhìn khác, bạn nhớ đón đọc nhé.