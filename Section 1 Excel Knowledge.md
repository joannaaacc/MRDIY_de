## Section 1: Excel Knowledge

### Question 2 Data Cleaning:
Select All,
Format as Table,
Filter out 'Cancelled' in column [Status],
Remove rows with 'Cancelled' in Status

<img width="1039" height="184" alt="{74AEAFA3-7650-4BE7-945D-5A2FA497477B}" src="https://github.com/user-attachments/assets/916cad90-b1a0-45f3-937c-8844a5098da7" />



To make sure no duplicates [Order ID], select Order ID under conditional formatting, highlight cell rules, duplicate values.

<img width="1025" height="557" alt="{5F407714-23D8-48CF-9210-F4384957F067}" src="https://github.com/user-attachments/assets/0daf1217-a836-4e14-be8c-41c7ea1bb671" />

**Notes: No duplicate Order ID after deleting 'Cancelled' in Status**

### Question 3 Formula Calculation:
-	Calculate the total revenue generated using the formula: SUMIF to sum Total Price where the Status is "Completed".

**=SUMIF(Table1[Status], "Completed", Table1[Total Sales amt])**

-	Find the average Quantity ordered for "Product A" using AVERAGEIF.

**=AVERAGEIF(Table1[Product], "Product A", Table1[Quantity])**

-	Identify the maximum Total Price for orders made in the "East" region using MAXIFS.

**=MAXIFS(Table1[Total Sales amt], Table1[Region], "East")**
  
-	Count the number of orders that were shipped using COUNTIF.

**=COUNTIF(Table1[Status], "Shipped")**

<img width="1033" height="583" alt="{5BFEE8B2-D1E8-4064-8215-28615FEB308F}" src="https://github.com/user-attachments/assets/674d1f2b-854b-4704-bbe1-ac1b9adf3292" />

### Question 4 Pivot Table:
- Create a pivot table to show the sum of Total Price for each Product by Region.
- Include a filter to view data for specific statuses like "Completed" or "Pending".

Insert, Pivot Table, Table/Range: Table1
Choose where to place the PivotTable: Existing Worksheet / any cells
In the PivotTable Fields Pane, drag as image below:

<img width="1147" height="669" alt="{EF2D4E55-3245-4BD9-A931-2F401F60F464}" src="https://github.com/user-attachments/assets/14dab41b-f63f-4047-bf39-b45d08925b05" />


**Expected Outcome**

<img width="607" height="221" alt="{1F2BE35A-B4E3-4159-84E1-47F27CF8F981}" src="https://github.com/user-attachments/assets/2c7c7e8a-20db-4e25-b02e-290cb7b51529" />


### Question 5 Charting:
- Using the pivot table created, generate a bar chart to compare the total sales for each Product in different regions.
**Expected Outcome**

<img width="600" height="363" alt="{0C5EAF79-0AE2-4337-BD66-B4889262F4C0}" src="https://github.com/user-attachments/assets/29caf5b4-dec9-4313-916a-888f4bcf8ca8" />

### Question 6 Lookups:
- Using VLOOKUP, find the Unit Price for "Order ID" 1005.

**=VLOOKUP(1005, Table1, 7, FALSE)**
  

### Question 7 Formatting:
<img width="1032" height="473" alt="{ADA961C2-C54C-41E1-B664-66D20C62815B}" src="https://github.com/user-attachments/assets/39a86a22-1b54-4798-aa88-c5a3889ced11" />
