#### Linux shell 数组长度极端方式及区别

###### 区别方式1：为两者添加双引号

```bash
root@VM-16-5-ubuntu:~/check_database# arr=({A..Z});for i in "${arr[@]}"; do echo $i; done; for j in "${arr[*]}"; do echo $j; done;
A
B
C
D
E
F
G
H
I
J
K
L
M
N
O
P
Q
R
S
T
U
V
W
X
Y
Z
A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
root@VM-16-5-ubuntu:~/check_database# arr=({A..Z});for i in "${arr[@]}"; do echo $i; done; 
A
B
C
D
E
F
G
H
I
J
K
L
M
N
O
P
Q
R
S
T
U
V
W
X
Y
Z
root@VM-16-5-ubuntu:~/check_database# arr=({A..Z});for i in ${arr[@]}; do echo $i; done; 
A
B
C
D
E
F
G
H
I
J
K
L
M
N
O
P
Q
R
S
T
U
V
W
X
Y
Z
root@VM-16-5-ubuntu:~/check_database# arr=({A..Z}); for j in "${arr[*]}"; do echo $j; done;
A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
root@VM-16-5-ubuntu:~/check_database# arr=({A..Z}); for j in ${arr[*]}; do echo $j; done;
A
B
C
D
E
F
G
H
I
J
K
L
M
N
O
P
Q
R
S
T
U
V
W
X
Y
Z
```
