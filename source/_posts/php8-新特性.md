#### PHP8

1. Named arguments

   ```
   function test($name,$age=12,$sex="boy"){
       echo $name.$age.$sex."\n";
   }
   test("hai",sex:"girl");
   ```

2. Attributes

   ```
   #[
   Route("/api/posts/{id}", methods: ["GET"])
   ]
   function testAttributes(){
       $ref = new ReflectionFunction("testAttributes");
       var_dump($ref->getAttributes("Route")[0]);
       var_dump($ref->getAttributes("Route")[0]->getName());
       var_dump($ref->getAttributes("Route")[0]->getArguments());
   }
   testAttributes();
   ```

3. Constructor property promotion

   ```
   class Point {
       public function __construct(
       public float $x = 0.0,
       public float $y = 0.0,
       public float $z = 0.0,
     ) {}
   }
   $point=new Point();
   var_dump($point->x);
   var_dump($point->y);
   var_dump($point->z);
   ```

4. Union types

   ```
   class Number {
       public function __construct(
          private int|float $number
          ) {}
   }
   new Number('88'); 
   new Number('ss88'); // TypeError
   new Number('88ss'); // TypeError
   ```

5. Match expression

   ```
   echo match (8.0) {
    '8.0' => "Oh no!",
     8.0 => "This is what I expected",
   };
   
   1.Match is an expression, meaning its result can be stored in a variable or returned.
   2.Match branches only support single-line expressions and do not need a break; statement.
   3.Match does strict comparisons.
   ```

6. Nullsafe operator

   ```
   $session=null;
   $country = $session?->user?->getAddress()?->country;
   var_dump($country);
   
   $country = $session->user->getAddress()->country; //Fatal error
   var_dump($country);
   ```

7. Saner string to number comparisons

   ```
   0 == 'foobar' // false
   When comparing to a numeric string, PHP 8 uses a number comparison. Otherwise, it converts the number to a string and uses a string comparison.
   
   ```

8. Consistent type errors for internal functions

   ```
   strlen([]); // TypeError: strlen(): Argument #1 ($str) must be of type string, array given
   
   array_chunk([], -1); // ValueError: array_chunk(): Argument #2 ($length) must be greater than 0
   ```

9. JIT

   ![image-20210506152843857](image-20210506152843857.png)

10. JIT基准测试

    > cli
    >
    > ![image-20210506153226230](image-20210506153226230.png)
    >
    > ![image-20210506153311221](image-20210506153311221.png)

    > fpm
    >
    > ![image-20210506153347728](image-20210506153347728.png)

#### 编程规约