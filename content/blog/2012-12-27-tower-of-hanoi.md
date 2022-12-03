---
date: '2012-12-27T01:36:28-08:00'
tags:
- computation
- code
title: Tower Of Hanoi
---

Java:

```java
public class Tower {
    private int number;
    private Stack<Integer> disks = new Stack<Integer>();
    public Tower(int number) {
        this.number = number;
    }
    public void add(Integer i) {
        if (!disks.empty() && i >= disks.peek()) throw new IllegalArgumentException();
        disks.push(i);
    }
    private void moveTop(Tower to) {
        Integer i = disks.pop();
        to.disks.push(i);
        System.out.println("Moved " + i + " from Tower " + number + " to Tower " + to.number);
    }
    public void move(int n, Tower to, Tower temp) {
        if (n <= 0) return;
        if (to == null || temp == null) throw new NullPointerException();
        move(n - 1, temp, to);
        moveTop(to);
        temp.move(n - 1, to, this);
    }
    public static void main(String[] args) {
        int n = 4;
        Tower[] t = new Tower[3];
        for (int i = 0; i < 3; ++i) t[i] = new Tower(i + 1);
        for (int i = n; i >= 1; --i) t[0].add(i);
        t[0].move(n, t[2], t[1]);
        System.out.println("Tower 1: " + t[0].disks.toString());
        System.out.println("Tower 2: " + t[1].disks.toString());
        System.out.println("Tower 3: " + t[2].disks.toString());
    }
}
```

Sample output:

```
Moved 1 from Tower 1 to Tower 2
Moved 2 from Tower 1 to Tower 3
Moved 1 from Tower 2 to Tower 3
Moved 3 from Tower 1 to Tower 2
Moved 1 from Tower 3 to Tower 1
Moved 2 from Tower 3 to Tower 2
Moved 1 from Tower 1 to Tower 2
Moved 4 from Tower 1 to Tower 3
Moved 1 from Tower 2 to Tower 3
Moved 2 from Tower 2 to Tower 1
Moved 1 from Tower 3 to Tower 1
Moved 3 from Tower 2 to Tower 3
Moved 1 from Tower 1 to Tower 2
Moved 2 from Tower 1 to Tower 3
Moved 1 from Tower 2 to Tower 3
Tower 1: []
Tower 2: []
Tower 3: [4, 3, 2, 1]
```

Once I wrapped my head around the algorithm I thought it was very beautiful.
