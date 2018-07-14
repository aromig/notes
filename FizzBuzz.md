# FizzBuzz

## JavaScript
```javascript
for (var i = 1; i <= 100; i++) {
  var fb = '';
  fb += (i % 3) === 0 ? 'Fizz' : '';
  fb += (i %5) === 0 ? 'Buzz' : '';
  console.log(fb != '' ? fb : i);
}
```

```javascript
// recursively
var fizzBuzz = function(n) {
    let array = [];
    let fb = '';

    if (n === 0) return array;

    fb += n % 3 === 0 ? 'Fizz' : '';
    fb += n % 5 === 0 ? 'Buzz' : '';
    array.push(fb !== '' ? fb : n.toString());

    return fizzBuzz(n - 1).concat(array);
};
```

## C#

```c#
for (int i = 1; i <= 100; i++) {
  string fb = "";
  fb += (i % 3) == 0 ? "Fizz" : "";
  fb += (i % 5) == 0 ? "Buzz" : "";
  Console.WriteLine(!string.IsNullOrEmpty(fb) ? fb : i);
}
```
