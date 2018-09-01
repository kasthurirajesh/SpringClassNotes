**Objective** : Find the number using the binary search. But binary search expects array to be a sorted array.
So we need the following the classes 
1) main method
2) BinarySearchImpl 
3) Sorting algorithm

Traditional way as follows
  ```java
  package com.delete;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@SpringBootApplication
public class DeleteApplication {

	public static void main(String[] args) {

		BinarySearchImpl bsi = new BinarySearchImpl();
		int result = bsi.search(new int[] {1,2,3} , 3);
		System.out.println("result" + result);	
			
      }
}
```
Before searching an elememt, we need to sort the elements. For the sorting the I am using the bubble sort.

```java
package com.delete;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component
public class BinarySearchImpl {
	
	public int search(int[] arr) {

		BubbleSort bubbleSort = new BubbleSort();
		bubbleSort.sort();
 		return 3;
	}
}
```
Bubble sort algorithm as follows
```java
package com.delete;

import org.springframework.stereotype.Component;

@Component
public class BubbleSort {

	public void sort() {
    //Logic for bubble sort
		System.out.println("Bubble sort");
	}
}
```

BinarySearchImpl is tightly coupled with **BubbleSort**. Suppose if I want to use the different sorting algorithm dynamically, Existing code doesn't support.

# Stage 2


To dynamically change the Sorting algorithm We use Interface, and constructor. So that we can handle the dynamic sorting algorithm. then BinarySearchImpl will be **loosely coupled**.

**/com-spring-basics/src/main/java/com/delete/SortingAlgorithm.java**
```java
package com.delete;

public interface ISortingAlgorithm {

	public void sort() ;
		
}
```
QuickSort is implementing the Interface
```java
package com.delete;

import org.springframework.stereotype.Component;

@Component
public class QuickSort implements ISortingAlgorithm {

	public void sort() {
		//Logic for Quick sort
		System.out.println("quick sort");
	}
}
```
Bubble sport is Implememting the interface
```java
package com.delete;

import org.springframework.stereotype.Component;

@Component
public class BubbleSort implements ISortingAlgorithm {

	public void sort() {
		//Logic for bubble sort
		System.out.println("Bubble sort");
	}
}
```


We need to change the **BinarySearchImpl** , To support the dynamic sorting algorithm. Following Implementation can take any sorting algorithm and sort the numbers.So inarySearchImpl become **loosely coupled**.

```java
package com.delete;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component
public class BinarySearchImpl {

	ISortingAlgorithm sortingAlgorithm;

	public BinarySearchImpl(ISortingAlgorithm sortingAlgorithm) {
		super();
		this.sortingAlgorithm = sortingAlgorithm;
	}

	public int search(int[] arr) {
		sortingAlgorithm.sort();
		return 3;
	}
}
```
**/com-spring-basics/src/main/java/com/delete/DeleteApplication.java**
```java
package com.delete;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@SpringBootApplication
public class DeleteApplication {

	public static void main(String[] args) {

		QuickSort QuickSort =  new QuickSort();
		BinarySearchImpl bsi = new BinarySearchImpl(QuickSort);
		int result = bsi.search(new int[] {1,2,3});
		System.out.println("result" + result);
		
	}
}
```

**When we run the application, output as follows**

In console:

      quick sort
      3

**Conclusion**
- BinarySearchImpl is become loosly coupled. And it supports any type of sorting algorithm.
**Limitations**
- In main method we are creating the objects for BinarySearchImpl, QuickSort.
- Is their a way so that we can avoid the creating the objects.



	





