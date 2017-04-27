# FizzBuzz

## JavaScript
```javascript
for (var i = 1; i <= 100; i++) {
  var fb = "";
  fb += (i % 3) === 0 ? "fizz" : "";
  fb += (i %5) === 0 ? "buzz" : "";
  console.log(fb != "" ? test : i);
}
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
