---
title: "编程题 ACM 模式输入输出总结"
date: 2022-09-25T13:59:22+08:00
categories:
- 算法
tags:
- 算法
- ACM
---

我平常刷题都是在 LeetCode 上进行的，LeetCode 上的题目都是核心代码模式，即题目给出了需要完成的函数声明，你只需要补全函数体，不需要考虑输入输出的问题。但是在参加很多公司的笔试时，发现这些笔试平台大多数采用 ACM 模式，即需要考虑怎么读取题目给出的输入格式，并按要求的格式输出。这篇文章总结了 ACM 模式中比较常见的几种输入输出，希望在后面的笔试中不会出现卡输入输出的问题。

## 输入数字

> 输入包括两个正整数a,b(1 <= a, b <= 1000),输入数据包括多组。

```java
import java.util.*;

public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        while (sc.hasNext()) {
            int a = sc.nextInt();
            int b = sc.nextInt();
            System.out.println(a + b);
        }
    }
}
```

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int a;
    int b;
    while (cin >> a >> b)
        cout << a + b << endl;
    return 0;
}
```

> 输入第一行包括一个数据组数t(1 <= t <= 100)，接下来每行包括两个正整数a,b(1 <= a, b <= 1000)

```java
import java.util.*;

public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int t = sc.nextInt();
        for (int i = 0; i < t; i++) {
            int a = sc.nextInt();
            int b = sc.nextInt();
            System.out.println(a + b);
        }
    }
}
```

```cpp
#include<bits/stdc++.h>
using namespace std;

int main() {
    int n;
    cin >> n;
    while (n > 0) {
        n--;
        int a, b;
        cin >> a >> b;
        cout << a + b << endl;
    }
    return 0;
}
```

> 输入的第一行包括一个正整数t(1 <= t <= 100), 表示数据组数。接下来t行, 每行一组数据。每行的第一个整数为整数的个数n(1 <= n <= 100)。接下来n个正整数, 即需要求和的每个正整数。

```java
import java.util.*;

public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int t, n, sum, num;
        t = sc.nextInt();
        for (int i = 0; i < t; i++) {
            n = sc.nextInt();
            sum = 0;
            for (int j = 0; j < n; j++) {
                num = sc.nextInt();
                sum += num;
            }
            System.out.println(sum);
        }
    }
}
```

```cpp
#include<bits/stdc++.h>
using namespace std;

int main()
{
    int t;
    cin >> t;
    for (int i = 0; i < t; i++) {
        int n, num, sum = 0;
        cin >> n;
        for (int j = 0; j < n; j++) {
            cin >> num;
            sum += num;
        }
        cout << sum << endl;
    }
	return 0;
}
```

> 输入数据有多组, 每行表示一组输入数据。每行的第一个整数为整数的个数n(1 <= n <= 100)。接下来n个正整数, 即需要求和的每个正整数。

```java
import java.util.*;

public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        while (sc.hasNext()) {
            int n = sc.nextInt();
            int sum = 0;
            for (int i = 0; i < n; i++) {
                int num = sc.nextInt();
                sum += num;
            }
            System.out.println(sum);
        }
    }
}
```

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n;
    int num;
    int sum;
    while (cin >> n) {
        sum = 0;
        for (int i = 0; i < n; i++) {
            cin >> num;
            sum += num;
        }
        cout << sum << endl;
    }
    return 0;
}
```

> 输入数据有多组, 每行表示一组输入数据。每行不定有n个整数，空格隔开。(1 <= n <= 100)。

```java
import java.util.*;

public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        while (sc.hasNextLine()) {
            String str = sc.nextLine();
            String[] s = str.split(" ");
            int sum = 0;
            for (String n : s) {
                sum += Integer.parseInt(n);
            }
            System.out.println(sum);
        }
    }
}
```

```cpp
#include<bits/stdc++.h>
using namespace std;

int main() {
    int num;
    int sum;
    while (cin >> num) {
        sum = 0;
        while (true) {
            sum += num;
            if (getchar() == '\n') break;
            cin >> num;
        }
        cout << sum << endl;
    }
    return 0;
}
```

## 输入字符串

> 输入有两行，第一行n，第二行是n个字符串，字符串之间用空格隔开

```java
import java.util.*;

public class Main {
    public static void main(String[] args) {
        Scanner reader = new Scanner(System.in);
        int n = Integer.parseInt(reader.nextLine());
        String[] s = reader.nextLine().split(" ");
        Arrays.sort(s);
        for (int i = 0; i < n - 1; i++) {
            System.out.print(s[i] + " ");
        }
        System.out.println(s[n - 1]);
    }
}
```

```cpp
#include<bits/stdc++.h>
using namespace std;

int main() {
    int n;
    cin >> n;
    string str;
    vector<string> vec;
    while (n--) {
        cin >> str;
        vec.push_back(str);
    }
    sort(vec.begin(), vec.end(), less<string>());
    for (int i = 0; i < vec.size() - 1; i++) {
        cout << str[i] << ' ';
    }
    cout << str[vec.size() - 1] << endl;
    return 0;
}
```

> 多个测试用例，每个测试用例一行。每行通过空格隔开，有n个字符，n＜100

```java
import java.util.*;

public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        while (sc.hasNextLine()) {
            String s = sc.nextLine();
            String[] strs = s.split(" ");
            Arrays.sort(strs);
            for (int i = 0; i < strs.length - 1; i++) {
                System.out.print(strs[i] + " ");
            }
            System.out.println(strs[strs.length - 1]);
        }
    }
}
```

```cpp
#include<bits/stdc++.h>
using namespace std;

int main() {
    string word;
    vector<string> ans;
    while (cin >> word) {
        ans.push_back(word);
        if (getchar() == '\n') {
            sort(ans.begin(), ans.end());
            for (int i = 0; i < ans.size() - 1; i++) {
                cout << ans[i] << ' ';
            }
            cout << ans[ans.size() - 1] << endl;
            ans.clear();
        }
    }
}
```

> 多个测试用例，每个测试用例一行。每行通过,隔开，有n个字符，n＜100

```java
import java.util.*;

public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        while (sc.hasNext()) {
            String str = sc.nextLine();
            String[] strs = str.split(",");
            Arrays.sort(strs);
            for (int i = 0; i < strs.length - 1; i++) {
                System.out.print(strs[i] + ",");
            }
            System.out.println(strs[strs.length - 1]);
        }
    }
}
```

```cpp
#include<bits/stdc++.h>
using namespace std;

int main() {
    string str;
    vector<string> vec;
    while (cin >> str) {
        stringstream stream(str);
        string tmp;
        while (getline(stream, tmp, ',')) {
            vec.push_back(tmp);
        }
        sort(vec.begin(), vec.end());
        for (int i = 0; i < vec.size() - 1; i++) {
            cout << vec[i] << ",";
        }
        cout << vec[vec.size() - 1] << endl;
        vec.clear();
    }
}
```
## 练习

- https://ac.nowcoder.com/acm/contest/5657