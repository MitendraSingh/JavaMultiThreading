# MultiThreading
 Multithreading
 -Scheduler
 -Monitors
 
 Runnable interface is a functional Interface(Interface that consists only one abstract method). </br>
 Thread is class. </br>

**Implementation 1:** 
```
 class MultiThreadingDemo extends Thread {
	 void run(){
		 System.out.println("running thread");
	 }
	 public static void main(){
		 MultiThreadingDemo object = new MultiThreadingDemo();
		 object.start();
		 
	 }
 }
```
**Implementation 2: ** 
```
 class MultiThreadingDemo implements Runnable {
	 void run(){
		 System.out.println("running thread");
	 }
	 public static void main(){
		 MultiThreadingDemo object = new MultiThreadingDemo();
		 Thread objectThread= new Thread(object);
		 objectThread.start();
		 
	 }
 }
```
 
 Question 1- if we remove run method from Implementation 1 then what will happen ? </br>
 Answer:- Nothing will happen. Blank thread started. It will never gives error because Thread class  </br>
		implements the run method in it's self.  </br>
		
Question 2- if we remove run method from Implementation 2 then what will happen ?  </br>
Answer:- it will gives the error because Runnable is the functional interface have run as abstract method  </br>
		in it.  </br>
Question 3 :- Why Implementation 1 does not create the object of Thread class but Implementation 2 needs  </br>
				to create the object of  thread class?  </br>
Answer :- Reason for this in Implementation 1   thread is created by extending thread class. Thread class internaly   </br>
		initialize through constructor chaining.  </br>
		
Question 4: How to create thread using lamda expression?  </br>
```
class MultiThreading {
	public static void main(String args[]){
		Runnable runObject = ()-> { System.out.println("runObject is running ");};
		Thread threaadObject= new Thread(runObject);
		threaadObject.start();
		
	}
}
```

**noted:- lambda expression is only used with functional interface.**

Question 5: what happen when 2 times start method called ?  </br>
Answer:- IllegalThreadStateException - if the thread was already started.  </br>

Question 6: difference between sleep and wait ?  </br>
```
-sleep is the method of Thread class   | -wait is the method of object class.
-sleep method is static.				| -wait methods is non static.
-This Method is used to pause the 		| -This method is defined in object class. 
 execution of current thread for a		|	  It tells the calling thread (a.k.a Current Thread) 
 specified time in Milliseconds. 		|	  to wait until another thread invoke’s the notify()
 Here, Thread does not lose its 		|	  or notifyAll() method for this object,
 ownership of the monitor and resume’s	|	  The thread waits until it reobtains the ownership of 
 it’s execution							|	  the monitor and Resume’s Execution.									  
```									   
									  
Question 6: write a program to one thread will print even other thread will print odd.  </br>
```
Answer: this can be done with wait(), notify() and semeaphore 									  
			for wait and notify
        1. declare static variable for counting.
		2. declare static object for same class.
		3. create threadEven for printing even no.
		4 create another threadOdd for printing odd no.
		5. for 3rd line override run method.
		   - create synchronized block with object level lock i.e created at line 2
		   - apply while loop having condition (count <10)
		   - check the condition (count %2 !=0) if true then call object.wait()
		   - print value of count and then increase the value of count++
		   - call object.notify();
		5. for 4rd line override run method.
		   - create synchronized block with object level lock i.e created at line 2
		   - apply while loop having condition (count <10)
		   - check the condition (count %2 ==0) if true then call object.wait()
		   - print value of count and then increase the value of count++
		   - call object.notify();
		   
		   
		   public class DemoThread {
				volatile static int count = 1;
				static DemoThread demothread=new DemoThread();
				public static void main(String args[]){

					Thread thread = new Thread(new Runnable() {
						@Override
						public void run() {
							synchronized (demothread) {
								while (count < 10) {
									if (count % 2 != 0) {
										try {
											demothread.wait();
										} catch (InterruptedException e) {
											System.out.println(e);
										}
									}
									System.out.println("Printing Even : " + count);
									count++;
									demothread.notify();

								}
							}
					}
					});

					Thread thread1 = new Thread(new Runnable() {
						@Override
						public void run() {
							synchronized (demothread) {
								while (count < 10) {
									if (count % 2 == 0) {
										try {
											demothread.wait();
										} catch (InterruptedException e) {
											System.out.println(e);
										}
									}
									System.out.println("Printing odd : " + count);
									count++;
									demothread.notify();

								}
							}
						}
					});
					thread.start();
					thread1.start();
				}
			}
	```								  
Question: join() method.  </br>

Question: countdown latch  </br>

In Java multithreading, a Future represents the result of an asynchronous computation. </br>
	It acts as a placeholder (or promise) for a value that will be available sometime in the  
	future once a task has completed execution. 
	
	Key Points about Future:

	Defined in java.util.concurrent package. 

	Represents the result of a computation that may not be complete yet. 

	Used with ExecutorService to submit tasks that return results. 

	Provides methods to check whether the task is completed, canceled, or still running. 
	
	
	| Method                                  | Description                                                                      |
	| --------------------------------------- | -------------------------------------------------------------------------------- |
	| `get()`                                 | Waits (blocks) until the computation is done and returns the result.             |
	| `get(long timeout, TimeUnit unit)`      | Waits for a maximum time, throws `TimeoutException` if not finished.             |
	| `cancel(boolean mayInterruptIfRunning)` | Attempts to cancel execution of the task.                                        |
	| `isCancelled()`                         | Returns `true` if the task was canceled before completion.                       |
	| `isDone()`                              | Returns `true` if the task is completed (normally, canceled, or with exception). |

	
	CompletableFuture (Java 8+): supports async callbacks, chaining, combining multiple tasks.
	
	
	What is CompletableFuture?

		An extension of Future that allows you to manually complete tasks and chain asynchronous computations.

		Supports non-blocking callbacks → you don’t need to call get() and wait.

		Helps in parallel execution and combining multiple tasks.

		Provides a fluent API for asynchronous programming.
		
		
	import java.util.concurrent.*;

	public class CompletableFutureExample {
		public static void main(String[] args) throws Exception {
			// Run an async task
			CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
				try {
					Thread.sleep(2000); // simulate long task
				} catch (InterruptedException e) {
					throw new RuntimeException(e);
				}
				return "Hello from async task!";
			});

			// Do something else while the task runs
			System.out.println("Main thread working...");

			// Attach callback (non-blocking)
			future.thenAccept(result -> 
				System.out.println("Result: " + result)
			);

			// Prevent main thread from exiting early
			Thread.sleep(3000);
		}
	}
	
	output: 
	Main thread working...
	Result: Hello from async task!
	
	| Method                  | Description                                           |
	| ----------------------- | ----------------------------------------------------- |
	| `supplyAsync(Supplier)` | Run a task asynchronously and return a result.        |
	| `runAsync(Runnable)`    | Run a task asynchronously without returning a result. |
	| `thenApply(fn)`         | Transform the result when complete.                   |
	| `thenAccept(consumer)`  | Consume result (no return).                           |
	| `thenRun(runnable)`     | Run extra action after completion.                    |
	| `thenCombine()`         | Combine two `CompletableFuture` results.              |
	| `allOf()`               | Wait for multiple futures to complete.                |
	| `anyOf()`               | Complete when the first future completes.             |
	| `exceptionally(fn)`     | Handle errors.                                        |



	Example: Chaining Futures

	CompletableFuture.supplyAsync(() -> "Hello")
		.thenApply(msg -> msg + " World")
		.thenApply(String::toUpperCase)
		.thenAccept(System.out::println);


	output:
	HELLO WORLD
	
	Combining Two Futures
	
	CompletableFuture<Integer> future1 = CompletableFuture.supplyAsync(() -> 10);
	CompletableFuture<Integer> future2 = CompletableFuture.supplyAsync(() -> 20);

	CompletableFuture<Integer> combined = future1.thenCombine(future2, (a, b) -> a + b);

	System.out.println("Sum = " + combined.get()); // 30
	
	
	Why CompletableFuture > Future?

	✅ Non-blocking (no need for get() unless you want to).

	✅ Manual completion: complete(value).

	✅ Chaining and composition of multiple tasks.

	✅ Exception handling built-in.
	
	
	*******************
	The Producer-Consumer problem is one of the most common problems in multithreading. Let’s break it down.
	********************
	
	What is the Producer-Consumer Problem?

	You have two types of threads:

		Producer → generates data and puts it into a shared buffer.

		Consumer → takes data from the buffer and processes it.

	The challenge:

		If buffer is full, producer must wait.

		If buffer is empty, consumer must wait.

	Requires synchronization to avoid race conditions.

	✅ Implementation Approaches

		Using wait() and notify() (classic way).

		Using BlockingQueue (modern & simpler).
		
		
		
	Using wait() and notify()
	
	
	import java.util.LinkedList;
	import java.util.Queue;

	class Buffer {
		private Queue<Integer> queue = new LinkedList<>();
		private int capacity = 5;

		public synchronized void produce(int value) throws InterruptedException {
			while (queue.size() == capacity) {
				wait(); // wait until space is available
			}
			queue.add(value);
			System.out.println("Produced: " + value);
			notifyAll(); // notify consumer
		}

		public synchronized int consume() throws InterruptedException {
			while (queue.isEmpty()) {
				wait(); // wait until item is available
			}
			int value = queue.poll();
			System.out.println("Consumed: " + value);
			notifyAll(); // notify producer
			return value;
		}
	}

	public class ProducerConsumerWaitNotify {
		public static void main(String[] args) {
			Buffer buffer = new Buffer();

			// Producer thread
			Thread producer = new Thread(() -> {
				for (int i = 1; i <= 10; i++) {
					try {
						buffer.produce(i);
						Thread.sleep(500); // simulate delay
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
				}
			});

			// Consumer thread
			Thread consumer = new Thread(() -> {
				for (int i = 1; i <= 10; i++) {
					try {
						buffer.consume();
						Thread.sleep(1000); // simulate processing
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
				}
			});

			producer.start();
			consumer.start();
		}
	}
	
	
	
	Using BlockingQueue (Simpler & Recommended)

		Java provides BlockingQueue (like ArrayBlockingQueue, LinkedBlockingQueue) to handle synchronization automatically.
		
	import java.util.concurrent.*;

	public class ProducerConsumerBlockingQueue {
		public static void main(String[] args) {
			BlockingQueue<Integer> queue = new ArrayBlockingQueue<>(5);

			Runnable producer = () -> {
				for (int i = 1; i <= 10; i++) {
					try {
						queue.put(i); // blocks if queue is full
						System.out.println("Produced: " + i);
						Thread.sleep(500);
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
				}
			};

			Runnable consumer = () -> {
				for (int i = 1; i <= 10; i++) {
					try {
						int value = queue.take(); // blocks if queue is empty
						System.out.println("Consumed: " + value);
						Thread.sleep(1000);
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
				}
			};

			new Thread(producer).start();
			new Thread(consumer).start();
		}
	}
	
	| Approach              | Pros                                 | Cons                              |
	| --------------------- | ------------------------------------ | --------------------------------- |
	| `wait()` + `notify()` | Low-level control, good for learning | Complex, error-prone              |
	| `BlockingQueue`       | Easy, less code, safer               | Less flexibility for custom logic |
