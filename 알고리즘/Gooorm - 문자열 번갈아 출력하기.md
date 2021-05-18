### Gooorm - 문자열 번갈아 출력하기

### Level 2

---

**풀이**

**코드**

```java
import java.io.*;
import java.util.*;
class Main {
	public static void main(String[] args) throws Exception {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		String []input = br.readLine().split("");
		LinkedList<String> list = new LinkedList<>();
		for(String str : input){
			list.add(str);
		}
		String answer = "";
		int index = 0;
		while(!list.isEmpty()){
			answer+=list.remove(index);
			
			if(index == 0)
				index = list.size()-1;
			else {
				index=0;
			}
			
		}
		System.out.println(answer);
	}
}
```

