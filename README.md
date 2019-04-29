# sql-bank
cts

BANK

1. Write a query to display account number, customer’s number, customer’s firstname, lastname, account opening date. Display the records sorted in ascending order based on account number.

A) SELECT a.account_number,c.customer_number,c.firstname,c.lastname,a.account_opening_date 
   FROM customer_master c JOIN account_master a ON c.customer_number=a.customer_number
   ORDER BY a.account_number;
   
 -------------------------------------------------------------------------------------------------------------------------------
 
 2. Write a query to display the number of customer’s from Delhi. Give the count an alias name of Cust_Count.

A) SELECT count(customer_number) Cust_Count FROM customer_master WHERE customer_city='Delhi';

---------------------------------------------------------------------------------------------------------------------------

3. Write a query to display the customer number, customer firstname, account number for the customer’s whose accounts were created after 15th of any month. Display the records sorted in ascending order based on customer number and then by account number.

A) SELECT c.customer_number,c.firstname,a.account_number FROM account_master a join customer_master c ON      c.customer_number=a.customer_number WHERE day(a.account_opening_date)>'15' ORDER BY c.customer_number,a.account_number;

-------------------------------------------------------------------------------------------------------------------------------


4. Write a query to display customer number, customer's first name, account number where the account status is terminated. Display the      records sorted in ascending order based on customer number and then by account number.

A) SELECT c.customer_number,c.firstname,a.account_number
   FROMaccount_master a JOIN customer_master c 
   ON c.customer_number=a.customer_number
   WHERE a.account_status='Terminated'
   ORDER BY c.customer_number,a.account_number;
  
----------------------------------------------------------------------------------------------------------------------------------

5. Write a query to display the total number of withdrawals and total number of deposits being done by customer whose customer number ends with 001. The query should display transaction type and the number of transactions. Give an alias name as Trans_Count for number of transactions. Display the records sorted in ascending order based on transaction type.

A)  SELECT transaction_type,count(transaction_number) Trans_Count
    FROM account_master am JOIN transaction_details td
    ON am.account_number=td.account_number
   WHERE customer_number like '%001'
   GROUP BY transaction_type
   ORDER BY transaction_type;
   
  -------------------------------------------------------------------------------------------------------------------------------
  
  6. Write a query to display the number of customers who have registration but no account in the bank. Give the alias name as     Count_Customer for number of customers.
  
A) SELECT count(customer_number) Count_Customer FROM customer_master
   WHERE customer_number NOT IN (SELECT customer_number FROM account_master);
   
   -----------------------------------------------------------------------------------------------------------------------
   
 7. Write a query to display account number and total amount deposited by each account holder (Including the opening balance). Give the total amount deposited an alias name of Deposit_Amount.  Display the records in sorted order based on account number.
 
A) SELECT a.account_number,a.opening_balance+sum(t.transaction_amount)
   FROM account_master a JOIN transaction_details t ON a.account_number=t.account_number
   WHERE t.transaction_type='Deposit' GROUP BY t.account_number;
   
  -----------------------------------------------------------------------------------------------------------------------------
  
  8. Write a query to display the number of accounts opened in each city .The Query should display Branch City and number of accounts as No_of_Accounts.For the branch city where we don’t have any accounts opened display 0. Display the records in sorted order based on branch city.
  
A) SELECT branch.branch_city, count(account.account_number) No_of_Accounts 
   FROM branch_master LEFT JOIN account_master
   ON account.branch_id=branch.branch_id 
   GROUP BY branch.branch_city ORDER BY branch_city;
   
-----------------------------------------------------------------------------------------------------------------------------------

9. Write  a query to display the firstname of the customers who have more than 1 account. Display the records in sorted order based on firstname.

A) SELECT c.firstname FROM 
   customer_master c JOIN account_master a ON a.customer_number=c.customer_number
   GROUP BY a.customer_number HAVING count(a.account_number)>1;
   
---------------------------------------------------------------------------------------------------------------------------------------

10. Write a query to display the customer number, customer firstname, customer lastname who has taken loan from more than 1 branch. Display the records sorted in order based on customer number.

A) SELECT c.customer_number,c.firstname,c.lastname FROM
   customer_master c JOIN loan_details l ON c.customer_number=l.customer_number
   GROUP BY l.customer_number HAVING count(l.branch_id)>1
   ORDER BY c.customer_number;

--------------------------------------------------------------------------------------------------------------------------------------

11. Write a query to display the customer’s number, customer’s firstname, customer’s city and branch city where the city of the customer and city of the branch is different. Display the records sorted in ascending order based on customer number.

A) SELECT c.customer_number,c.firstname,c.customer_city,b.branch_city FROM
   Customer_master c JOIN Account_master a ON c.customer_number=a.customer_number
   JOIN Branch_master b ON b.branch_id=a.branch_id
   WHERE b.branch_city<>c.customer_city
   ORDER BY c.customer_number;
   
---------------------------------------------------------------------------------------------------------------------------------

12.  Write a query to display the number of clients who have asked for loans but they don’t have any account in the bank though they are registered customers. Give the count an alias name of Count.

A)  SELECT count(c.customer_number)Count FROM customer_master c JOIN loan_details l
    ON c.customer_number=l.customer_number
    WHERE c.customer_number NOT IN (SELECT customer_number FROM account_master);
    
 ----------------------------------------------------------------------------------------------------------------------------------
 
 13. Write a query to display the account number who has done the highest transaction. For example the account A00023 has done 5 transactions i.e. suppose 3 withdrawal and 2 deposits. Whereas the account A00024 has done 3 transactions i.e. suppose 2 withdrawals and 1 deposit. So account number of A00023 should be displayed. In case of multiple records, display the records sorted in ascending order based on account number.
 
A) SELECT account_number FROM transaction_details 
   GROUP BY account_number
   HAVING count(transaction_number)>=ALL
   (SELECT count(transaction_number) FROM transaction_details 
   GROUP BY account_number) ORDER BY account_number;
   
   ------------------------------------------------------------------------------------------------------------------------------
   
   14.  Write a query to show the branch name,branch city where we have the maximum customers. For example the branch B00019 has 3 customers, B00020 has 7 and B00021 has 10. So branch id B00021 is having maximum customers. If B00021 is Koramangla branch Bangalore, Koramangla branch should be displayed along with city name Bangalore. In case of multiple records, display the records sorted in ascending order based on branch name.
   
A)  SELECT b.branch_name,b.branch_city FROM
    Branch_master b JOIN account a ON a.branch_id=b.branch_id
    GROUP BY a.branch_id HAVING count(a.customer_number)>=ALL
    (SELECT count(customer_number) FROM 
    Account_master GROUP BY branch_id)
    ORDER BY b.branch_name;
    
    --------------------------------------------------------------------------------------------------------------------------------
    
    15. Write a query to display all those account number, deposit, withdrawal where withdrawal is more than deposit amount. Hint: Deposit should include opening balance as well. For example A00011 account opened with Opening Balance 1000 and  A00011 deposited 2000 rupees on 2012-12-01 and 3000 rupees on 2012-12-02. The same account i.e A00011 withdrawn 3000 rupees on 2013-01-01 and 7000 rupees on 2013-01-03. So the total deposited amount is 6000 and total withdrawal amount is 10000. So withdrawal amount is more than deposited amount for account number A00011. Display the records sorted in ascending order based on account number.

A) SELECT td.account_number,
   sum(CASE WHEN  transaction_type='Deposit' THEN transaction_amount END)
   +(SELECT opening_balance 
   FROM account_master where account_number=td.account_number) Deposit,
   sum(CASE WHEN transaction_type='Withdrawal' THEN transaction_amount END) Withdrawal
   FROM transaction_details td
   GROUP BY td.account_number
   HAVING Withdrawal > Deposit
   ORDER BY td.account_number;
   
            (or)

  SELECT ifnull(t1.account_number,t2.account_number) account_number,
  t2.d Deposit,ifnull(t1.w,0) Withdrawal FROM
  (SELECT account_number,transaction_type,sum(transaction_amount) w from transaction_details
  WHERE transaction_type='Withdrawal' GROUP BY account_number) t1 
  RIGHT JOIN
  (SELECT a.account_number,a.opening_balance+sum(t.transaction_amount) d
  FROM account_master a JOIN transaction_details t ON a.account_number=t.account_number
  WHERE t.transaction_type='Deposit'GROUP BY t.account_number) t2
  ON t1.account_number=t2.account_number 
  WHERE ifnull(t1.w,0)>t2.d
  ORDER BY account_number;
  
-------------------------------------------------------------------------------------------------------------------------------

16. Write a query to show the balance amount  for account number that ends with 001. Note: Balance amount includes account opening balance also. Give alias name as Balance_Amount. For example A00015 is having an opening balance of 1000. A00015 has deposited 2000 
on 2012-06-12 and deposited 3000 on 2012-07-13. The same account has drawn money of 500 on 2012-08-12 , 500 on 2012-09-15, 1000 on 
2012-12-17. So balance amount is 4000 i.e (1000 (opening balance)+2000+3000 ) – (500+500+1000).

A) SELECT  ifnull((SUM(CASE WHEN transaction_type='Deposit' 
   THEN transaction_amount END)) -
   (SUM(CASE WHEN transaction_type='Withdrawal' 
   THEN transaction_amount END))+(select opening_balance 
   from account_master where account_number like '%001'),(SUM(CASE WHEN transaction_type='Deposit' 
   THEN transaction_amount END))+(select opening_balance 
   from account_master where account_number like '%001'))  AS Balance_Amount
   FROM transaction_details where account_number like '%001';
   
                      (or)

   SELECT ifnull(t1.account_number,t2.account_number) account_number,
    t2.d-ifnull(t1.w,0) Balance_Amount FROM
   (SELECT account_number,transaction_type,sum(transaction_amount) w from transaction_details
   WHERE transaction_type='Withdrawal' GROUP BY account_number) t1 
   RIGHT JOIN
  (SELECT a.account_number,a.opening_balance+sum(t.transaction_amount) d
  FROM account a JOIN transaction_details t ON a.account_number=t.account_number
  WHERE t.transaction_type='Deposit'GROUP BY t.account_number) t2
  ON t1.account_number=t2.account_number 
   WHERE ifnull(t1.account_number,t2.account_number) LIKE '%001'
   ORDER BY account_number;
--------------------------------------------------------------------------------------------------------------------------------

17. Display the customer number, customer's first name, account number and number of transactions  being made by the customers from each account. Give the alias name for number of transactions as Count_Trans. Display the records sorted in ascending order based on customer number and then by account number.

A) SELECT c.customer_number,c.firstname,t.account_number, count(t.account_number) Count_Trans 
   FROM transaction_details t JOIN account_master a ON a.account_number=t.account_number
   JOIN customer c ON c.customer_number=a.customer_number
   GROUP BY t.account_number ORDER BY c.customer_number, a.account_number;
   
-------------------------------------------------------------------------------------------------------------------------------

18.  Write a query to display the customer’s firstname who have multiple accounts (atleast  2 accounts).  Display the records sorted in ascending order based on customer's firstname.

A) SELECT c.firstname FROM
   Customer_master c JOIN account_master a ON c.customer_number=a.customer_number
   GROUP BY a.customer_number HAVING count(a.account_number)>1
   ORDER BY c.firstname;

--------------------------------------------------------------------------------------------------------------------------------------

19. Write a query to display the customer number, firstname, lastname for those client where total loan amount taken is maximum and at least taken from 2 branches. For example the customer C00012 took a loan of 100000 from bank branch with id B00009 and C00012 Took a loan of 500000 from bank branch with id B00010. So total loan amount for customer C00012 is 600000. C00013 took a loan of 100000 from bank branch B00009 and 200000 from bank branch B00011. So total loan taken is 300000. So loan taken by C00012 is more then C00013.

A) SELECT ld.customer_number, firstname, lastname
   FROM customer_master cm JOIN loan_details ld
   ON cm.customer_number=ld.customer_number
   GROUP BY customer_number
   HAVING count(branch_id)>=2 AND sum(loan_amount)>=
   ALL(SELECT sum(loan_amount) FROM loan GROUP BY customer_number);
   
  ------------------------------------------------------------------------------------------------------------------------------------
  
  20. Write a query to display the customer’s number, customer’s firstname, branch id and loan amount for people who have taken loans. Display the records sorted in ascending order based on customer number and then by branch id and then by loan amount.

A) SELECT c.customer_number,c.firstname,l.branch_id,l.loan_amount FROM
   Customer_master c JOIN loan_details l ON c.customer_number=l.customer_number
   ORDER BY c.customer_number,l.branch_id,l.loan_amount;
   
----------   --------------------------------------------------------------------------------------------------------------------------
   
   21. Write a query to display city name and count of branches in that city. Give the count of branches an alias name of Count_Branch.   Display the records sorted in ascending order based on city name.

A) SELECT branch_city,count(branch_id) Count_Branch FROM
   Branch_master GROUP BY branch_city
   ORDER BY branch_city;
   
----------------------------------------------------------------------------------------------------------------------------
 
 22.  Write a query to display account id, customer’s firstname, customer’s lastname for the customer’s whose account is Active. Display the records sorted in ascending order based on account id /account number.

A) SELECT a.account_number,c.firstname,c.lastname FROM
   Customer_master c JOIN account_master a ON c.customer_number=a.customer_number and a.account_status='Active'
   ORDER BY a.account_number;
   
----------------------------------------------------------------------------------------------------------------------------------

23. Write a query to display customer’s number, first name and middle name. For the customers who don’t have middle name,  display their last name as middle name. Give the alias name as Middle_Name.  Display the records sorted in ascending order based on customer number.

A) SELECT customer_number,firstname,ifnull(middlename,lastname) Middle_name FROM
   Customer_master ORDER BY customer_number;
   
------------------------------------------------------------------------------------------------------------------------------------

24. Write a query to display the customer number , firstname, customer’s date of birth . Display the records sorted in ascending order of date of birth year and within that sort by firstname in ascending order.

A) SELECT customer_number,firstname,customer_date_of_birth FROM
   Customer_master ORDER BY year(customer_date_of_birth),customer_number;
   
---------------------------------------------------------------------------------------------------------------------------------

25. Write a query to display the customers firstname, city and account number whose occupation are not into Business, Service or Student. Display the records sorted in ascending order based on customer first name and then by account number.

A) SELECT c.firstname,c.customer_city,a.account_number FROM 
   Customer_master c JOIN account_master a ON a.customer_number=c.customer_number
  WHERE c.occupation NOT IN ('Service','Student','Business')
   ORDER BY c.firstname,a.account_number;
   
------------------------------------------------------------------------------------------------------------------------------------


   



















