## 设计模式
### 1.单例模式
    
    class Singleton{
         private static $instance;
         public static function getInstance(){
            if(!(self::$instance instanceof self)){
                self::$instance = new self();
            } 
            return self::$instance;
         }
     }
     $first = Singleton::getInstance();
     $second = Singleton::getInstance();
     $first->a = "zs";
### 2.工厂模式
    interface mysql{
         public function connect();
     }
     
     class mysqli2 implements mysql{
         public function connect() {
             echo "mysqli";
         }
     }
     class pdo2 implements mysql{
         public function connect() {
             echo "pdo";
         }
     }
     class mysqlFactory{
         static public function factory($class_name){
             return new $class_name();
         }
     }
     
     $obj = mysqlFactory::factory('pdo2');
     $obj->connect();
### 3.策略模式
    abstract class Strategy{
         public abstract function doAction($money);
     }
     
     class ManJianStrategy extends Strategy{
         public function doAction($money) {
             echo "满减算法：原价{$money}元";
         }
     }
     
     class DaZheStrategy extends Strategy{
         public function doAction($money) {
             echo "打折算法：原价{$money}元";
         }
     }
     
     class StrategyFind{
         private $strategy_mode;
         public function __construct($mode) {
             $this->strategy_mode = $mode;
         }
         public function get($money){
             $this->strategy_mode->doAction($money);
         }
     }
     
     
     $mode = new StrategyFind(new ManJianStrategy());
     $mode->get(100);
### 4.适配器模式
    /**
      * 目标角色
      */
     interface Target2 {
      
         /**
          * 源类也有的方法1
          */
         public function sampleMethod1();
      
         /**
          * 源类没有的方法2
          */
         public function sampleMethod2();
     }
      
     /**
      * 源角色
      */
     class Adaptee2 {
      
         /**
          * 源类含有的方法
          */
         public function sampleMethod1() {
             echo 'Adaptee sampleMethod1 <br />';
         }
     }
      
     /**
      * 类适配器角色
      */
     class Adapter2 implements Target2 {
      
         private $_adaptee;
      
         public function __construct(Adaptee $adaptee) {
             $this->_adaptee = $adaptee;
         }
      
         /**
          * 委派调用Adaptee的sampleMethod1方法
          */
         public function sampleMethod1() {
             $this->_adaptee->sampleMethod1();
         }
      
         /**
          * 源类中没有sampleMethod2方法，在此补充
          */
         public function sampleMethod2() {
             echo 'Adapter sampleMethod2 <br />';
         }
     }
      
     class Client {
      
         /**
          * Main program.
          */
         public static function main() {
             $adaptee = new Adaptee2();
             $adapter = new Adapter2($adaptee);
             $adapter->sampleMethod1();
             $adapter->sampleMethod2();
         }
     }
### 5.门面模式
    interface Shape{
         public function draw();
     }
     
     class Circle implements Shape{
         public function draw() {
             echo "画一个原型";
         }
     }
     class Rectangle implements Shape{
         public function draw() {
             echo "画一个矩形";
         }
     }
     
     class Square implements Shape{
         public function draw() {
             echo "画一个正方形";
         }
     }
     
     class ShapeMark{
         public $circle;
         public $rectangle;
         public $square;
         public function __construct() {
             $this->circle = new Circle();
             $this->square = new Square();
             $this->rectangle = new Rectangle();
         }
         
         public function drawCircle(){
             $this->circle->draw();
         }
         public function drawSquare(){
             $this->square->draw();
         }
         public function drawRectangle(){
             $this->rectangle->draw();
         }
     }
     
     $shapemark = new ShapeMark();
     $shapemark->drawCircle();
     $shapemark->drawRectangle();
     $shapemark->drawSquare();
