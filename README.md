# Software-Testing-BVA

Study Notes - SOFTWARE TESTING of Maynooth Universtiy  by Dr. Joe Timoney

# Boundary Values Analysis

- ST ACTIVITIES

1. Analysis of SW / spec that informs the test
2. Identify test coverage items
3. Identify test cases
4. Verify test design to ensure completeness
5. Implementation of tests
6. Execution of tests
7. Examination of test results

# EP design case - example

The bank customer enters how much money is deposited (represented as an integer) in a bank savings account. Based on the amount of money, the different interest rates are displayed:

a) 0.3% interest rate if the amount is between €1 and €100.

b) 0.5% interest rate if the amount is between €101 and €1000. 

c) 0.7% interest rate if the amount is above €1000.

The initial balance of the account is €0 because of a lack of information. The deposited amount must be a positive number. Any error leads to a return value of 0. 

# 1. Analysis of SW / spec that informs the test

| Parameter    | Minimum Value  | Maximum Value   |
|--------------|----------------|-----------------|
| Deposit      | Long.MIN_VALUE | 0               |
|              | 1              | 100             |
|              | 101            | 1000            |
|              | 1001           | Long.MAX_VALUE  |

|--------------|----------------|-----------------|
| Return Value | 0              		  |
|              | 0.30%                            |
|              | 0.50%                            |
|              | 0.70%                            |



# 2. Identify test coverage items

| TCI  | Parameter    | Equivalence Partition | Test Case              |
|------|--------------|-----------------------|------------------------|
| EP1* | Deposit      | (*)Long.MIN_VALUE..0  | To be completed later  |
| EP2  |              | 1..100                |                        |
| EP3  |              | 101..1000             |                        |
| EP4  |              | 1001..Long.MAX_VALUE  |                        |
| EP5  | Return Value | 0                     |                        |
| EP6  |              | 0.30%                 |                        |
| EP7  |              | 0.50%                 |                        |
| EP8  |              | 0.70%                 |                        |


# 3. Identify test cases

| Case | Parameter    | Equivalence Partition | Test  |
|------|--------------|-----------------------|-------|
| EP1* | Deposit      | (*)Long.MIN_VALUE..0  |       |
| EP2  |              | 1..100                |       |
| EP3  |              | 101..1000             |       |
| EP4  |              | 1001..Long.MAX_VALUE  |       |
| EP5  | Return Value | 0                     |       |
| EP6  |              | 0.30%                 |       |
| EP7  |              | 0.50%                 |       |
| EP8  |              | 0.70%                 |       |

| Parameter    | Equivalence Partition | Equivalence Value  |
|--------------|-----------------------|--------------------|
| Deposit      | (*)Long.MIN_VALUE..0  | -100               |
|              | 1..100                | 50                 |
|              | 101..1000             | 150                |
|              | 1001..Long.MAX_VALUE  | 2000               |
| Return Value | 0                     | 0                  |
|              | 0.30%                 | 0.30%              |
|              | 0.50%                 | 0.50%              |
|              | 0.70%                 | 0.70%              |


| ID   | TCI Covered | Inputs<br/>Equivalence Value | Exp. Result<br/>Return Value  |
|------|-------------|-------------------|---------------|
| T1.1 | EP2,6       | 50                | 0.30%         |
| T1.2 | EP3,7       | 150               | 0.50%         |
| T1.3 | EP4,8       | 2000              | 0.70%         |
| T1.4 | EP1*,5      | -100              | 0             |


# 4. Verify test design to ensure completeness

| TCI  | Parameter    | Equivalence Partition | Test Case  |
|------|--------------|-----------------------|------------|
| EP1* | Deposit      | (*)Long.MIN_VALUE..0  | T1.4       |
| EP2  |              | 1..100                | T1.1       |
| EP3  |              | 101..1000             | T1.2       |
| EP4  |              | 1001..Long.MAX_VALUE  | T1.3       |
| EP5  | Return Value | 0                     | T1.4       |
| EP6  |              | 0.30%                 | T1.1       |
| EP7  |              | 0.50%                 | T1.2       |
| EP8  |              | 0.70%                 | T1.3       |

# 5. Implementation of tests

| ID   | TCI Covered | Inputs<br/>Equivalence Value | Exp. Result<br/>Return Value  |
|------|-------------|-------------------|---------------|
| T1.1 | EP2,6       | 50                | 0.30%         |
| T1.2 | EP3,7       | 150               | 0.50%         |
| T1.3 | EP4,8       | 2000              | 0.70%         |
| T1.4 | EP1*,5      | -100              | 0             |

# 6. Execution of tests

Java + TestNG

Test automation with dataproviders

```java
package trade;

import org.testng.annotations.Test;
import static org.testng.Assert.assertEquals;
import org.testng.annotations.DataProvider;

public class InterestCalculatorTest {

	// test data
	private static Object[][] testData = new Object[][] {
			// id, variable1, variable2, ... variableN, expected
		    {"T1.1",	   50,		 0.003},
		    {"T1.2",  	  150, 		 0.005},
		    {"T1.3", 	 2000, 		 0.007}, 
		    {"T1.4", 	 -100,    	     0},
	};

	@DataProvider(name = "data")
	public Object[][] getTestData() {
		return testData;
	}

	@Test(dataProvider = "data")
	public void test(String id, double variable1,  double expected) {
		 assertEquals(InterestCalculator.interestRate(variable1), expected);
	}
}
```

# 7. Examination of test results

Console log

```text
[RemoteTestNG] detected TestNG version 7.4.0
PASSED: test("T1.2", 150, 0.005)
PASSED: test("T1.4", -100, 0)
PASSED: test("T1.1", 50, 0.003)
PASSED: test("T1.3", 2000, 0.007)

===============================================
    Default test
    Tests run: 1, Failures: 0, Skips: 0
===============================================


===============================================
Default suite
Total tests run: 4, Passes: 4, Failures: 0, Skips: 0
===============================================

```


# EP FAULT MODEL

The equivalence partition fault model is where entire ranges of values are not processed correctly. 

These faults can be associated with incorrect decisions in the code, or missing sections of functionality.

By testing with at least one value from every equivalence partition, where every value should be
processed in the same way, EP testing attempts to find these faults.

# STRENGTHS AND WEAKNESSES OF EP

Strengths:
- A good basic level of testing
- Good for data processing applications where input can be easily identified, is distinct and allows for easy partitioning
- Provides a structured means for identifying basic test coverage items.

Weaknesses:
- No testing of correct processing on partition edges
- No combination testing

# KEY POINTS ON EP

EP is used to test basic software functionality
- Each ranges of values with 'equivalent processing' is a test coverage item
- A representative value from each range is selected as test data

# My thoughts

It is very suitable for the core computing function test of trading system
