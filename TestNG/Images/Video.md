# Notes from the Recorded Video

![img_1.png](t8.png)

![img.png](t9.png)
![img.png](img.png)

![img_1.png](img_1.png)

![img_2.png](img_2.png)


what is a framework?
> A framework is a set of tools, libraries, and best practices that provide a structured approach to work on a specific type of project. 


![img_3.png](img_3.png)
![img_4.png](img_4.png)
![img_5.png](img_5.png)
![img_6.png](img_6.png)

**by default the testng will execute the tests in alphabetical order**

**including and excluding the test case**

always run the test cases from the configuration file


![img_7.png](img_7.png)


technically we can have multiple assertion statements within a single test but it is not a best practice because then i will again need to explictly come to my code to check, what is failing and we won't be confident or sure about the test cases we wrote.

![img_8.png](img_8.png)

![img_9.png](img_9.png)

![img_10.png](img_10.png)

so if the depends upon method is enabled and the dependency fails our test case gets ignored but if we will have the attribute `alwaysRun = true` then it always will run irrespective of if dependency fails or not
![img_11.png](img_11.png)


![img_12.png](img_12.png)

when you mention the invocation attribute this defines how many times do you need to invoke a particular method along with that we can also specify the theadpool size, then out program will use those number of parallel threads to process that function invocation in according to the number of invocations required
