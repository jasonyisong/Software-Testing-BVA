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

|              |                |
|--------------|----------------|
| Return Value | 0              |
|              | 0.30%          |
|              | 0.50%          |
|              | 0.70%          |



# 2. Identify test coverage items

| TCI  | Parameter    | Boundary Value | Test Case              |
|------|--------------|----------------|------------------------|
| BV1* | Deposit      | Long.MIN_VALUE | To be completed later  |
| BV2* |              | 0              |                        |
| BV3  |              | 1              |                        |
| BV4  |              | 100            |                        |
| BV5  |              | 101            |                        |
| BV6  |              | 1000           |                        |
| BV7  |              | 1001           |                        |
| BV8  |              | Long.MAX_VALUE |                        |
| BV9  | Return Value | 0              |                        |
| BV10 |              | 0.30%          |                        |
| BV11 |              | 0.50%          |                        |
| BV12 |              | 0.70%          |                        |


# 3. Identify test cases

| ID   | Test Cases Covered | Inputs<br/> Deposit | Exp. Result<br/>Return Value|
|------|--------------------|----------------|---------------|
| T2.1 | BV3,10             | 1              | 0.30%         |
| T2.2 | BV4,[10]           | 100            | 0.30%         |
| T2.3 | BV5,11             | 101            | 0.50%         |
| T2.4 | BV6,[11]           | 1000           | 0.50%         |
| T2.5 | BV7,12             | 1001           | 0.70%         |
| T2.6 | BV8,[12]           | Long.MAX_VALUE | 0.70%         |
| T2.7 | BV1*,9             | Long.MIN_VALUE | 0             |
| T2.8 | BV2*,[9]           | 0              | 0             |


# 4. Verify test design to ensure completeness

| TCI  | Parameter    | Boundary Value | Test Case  |
|------|--------------|----------------|------------|
| BV1* | Deposit      | Long.MIN_VALUE | T2.7       |
| BV2* |              | 0              | T2.8       |
| BV3  |              | 1              | T2.1       |
| BV4  |              | 100            | T2.2       |
| BV5  |              | 101            | T2.3       |
| BV6  |              | 1000           | T2.4       |
| BV7  |              | 1001           | T2.5       |
| BV8  |              | Long.MAX_VALUE | T2.6       |
| BV9  | Return Value | 0              | T2.7       |
| BV10 |              | 0.30%          | T2.1       |
| BV11 |              | 0.50%          | T2.3       |
| BV12 |              | 0.70%          | T2.5       |

# 5. Implementation of tests

| ID   | Test Cases Covered | Inputs<br/> Deposit | Exp. Result<br/>Return Value|
|------|--------------------|----------------|---------------|
| T2.1 | BV3,10             | 1              | 0.30%         |
| T2.2 | BV4,[10]           | 100            | 0.30%         |
| T2.3 | BV5,11             | 101            | 0.50%         |
| T2.4 | BV6,[11]           | 1000           | 0.50%         |
| T2.5 | BV7,12             | 1001           | 0.70%         |
| T2.6 | BV8,[12]           | Long.MAX_VALUE | 0.70%         |
| T2.7 | BV1*,9             | Long.MIN_VALUE | 0             |
| T2.8 | BV2*,[9]           | 0              | 0             |

# 6. Execution of tests

Java + TestNG

Test automation with dataproviders

```java
package trade_test_BVA;

import org.testng.annotations.Test;
import static org.testng.Assert.assertEquals;
import org.testng.annotations.DataProvider;

public class InterestCalculatorTest {

	// test data
	private static Object[][] testData = new Object[][] {
			// id, variable1, variable2, ... variableN, expected
		    {"T2.1",				    1,		 0.003},
	            {"T2.2",  	 			  100, 		 0.003},
		    {"T2.3", 				  101, 		 0.005}, 
		    {"T2.4", 				 1000,    	 0.005},
		    {"T2.5",				 1001,		 0.007},
		    {"T2.6", 		 9223372036854775807L, 		 0.007},
		    {"T2.7",		-9223372036854775807L, 		     0}, 
		    {"T2.8", 	   			    0,    	     0},
	};

	@DataProvider(name = "data")
	public Object[][] getTestData() {
		return testData;
	}

	@Test(dataProvider = "data")
	public void test(String id, long variable1,  double expected) {
		 assertEquals(InterestCalculator.interestRate(variable1), expected);
	}
}
```

# 7. Examination of test results

Console log

```text
[RemoteTestNG] detected TestNG version 7.4.0
PASSED: test("T2.6", 9223372036854775807, 0.007)
PASSED: test("T2.2", 100, 0.003)
PASSED: test("T2.4", 1000, 0.005)
PASSED: test("T2.5", 1001, 0.007)
PASSED: test("T2.7", -9223372036854775807, 0)
PASSED: test("T2.8", 0, 0)
PASSED: test("T2.1", 1, 0.003)
PASSED: test("T2.3", 101, 0.005)

===============================================
    Default test
    Tests run: 1, Failures: 0, Skips: 0
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
