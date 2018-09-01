
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
- **Dependency injection** happening through Constructor. To make class become loosely coupled.

**Limitations**
- In main method we are creating the objects for BinarySearchImpl, QuickSort.
- Is their a way so that we can avoid the creating the objects.

# stage3
In this Step we don't want to create the the beans and wire them, like following. Instead we want spring to create the  beans and wire them  accordingly.
~~QuickSort =  new QuickSort();
BinarySearchImpl bsi = new BinarySearchImpl(QuickSort);~~

To create the beans automatically we use @Component annotation.Wherever It finds the @Component annotation , It will create the object.

To inject Dependency we use @Autowired annotation.

ApplicationContext hold the all the beans that it has created.

## /com-spring-basics/src/main/java/com/delete/DeleteApplication.java
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

		ApplicationContext context = SpringApplication.run(DeleteApplication.class, args);
		BinarySearchImpl bsi = context.getBean(BinarySearchImpl.class);
		int result = bsi.search(new int[] { 1, 2, 3 });
		System.out.println("result" + result);

	}
}
```

### /com-spring-basics/src/main/java/com/delete/BinarySearchImpl.java
While creating the bean, It finds the @Autowired then it will create a bean and inject to it. So Dependency Injection becomes simple.


```java
package com.delete;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component
public class BinarySearchImpl {

	
	@Autowired
	@Qualifier(value="bubbleSort") 
	private ISortingAlgorithm sortingAlgorithm;
	    
	public int search(int[] arr) {
		sortingAlgorithm.sort();
		return 3;
	}
}
```











