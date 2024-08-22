# IMathAS cheat sheet

## Template 1
![image](https://github.com/user-attachments/assets/0940a403-db9a-47fa-8b68-d31baaf82daa)
### 1. Floats in for loop boundaries

The boundaries of a for loop are rounded. The upper boundary is rounded in the usual way (nearest integer), whereas the lower boundary is rounded using the floor function.
```
$log = ''
for ($i = 0.8..6.5) {
  $log = $log . '$i'
}
//equivalent to ($i = 0..7). Remember that
//boundaries are inclusive.
//output: $log = $i$i$i$i$i$i$i$i (8 times)
```
### 2. 
