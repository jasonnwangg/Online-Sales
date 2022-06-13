# Online-Sales

# Read the data in

options(warn=-1)
library(sqldf)
consumer<-read.csv("consumer.csv",header=TRUE)
pop_up<- read.csv("pop_up.csv",header=TRUE)
purchase<-read.csv("purchase.csv",header=TRUE)
email<-read.csv("email.csv",header=TRUE)

# Exploring various tables


# Display first 5 rows of all the four tables

sqldf("SELECT * FROM consumer LIMIT 5;")
sqldf("SELECT * FROM pop_up LIMIT 5;")
sqldf("SELECT * FROM purchase LIMIT 5;")
sqldf("SELECT * FROM email LIMIT 5;")

# Descriptive statistics on consumer table

sqldf("
      SELECT COUNT(*) AS gender_count,
      AVG(age) AS average_age,
      gender FROM consumer 
      GROUP BY gender
      ")

# The number of female consumers is roughly three times the number of male consumers. 
# The average age of male consumers is slightly higher than the female but they're both around 30.

# How many consumers are there in each loyalty status group?
# What is the average age of consumers in each group?

sqldf("
      SELECT COUNT(*) AS loyalty_count,
      AVG(age) AS average_age,
      loyalty_status FROM consumer
      GROUP BY loyalty_status
      ")

# The average age has a positive correlation with loyalty status. Most consumers are at level 2.

# Inspect the pop_up table

sqldf("
      SELECT COUNT(*) AS consumer_count,
      pop_up AS pop_up, 
      saved_discount AS discount_code FROM pop_up
      GROUP BY pop_up, saved_discount
      ")

# Half of the consumers did not receive the pop up.
# Roughly half of those who received also added the discount code.

# How much did consumers spend on their total sales during their online purchase?

sqldf("
      SELECT AVG(sales_amount_total) AS total_sales FROM purchase
      ")

#The average spend per customer is 135.2142.

# How many consumers of the total opened the email blast?

sqldf("
      SELECT COUNT(*) AS consumer_count,
      opened_email FROM email
      GROUP BY opened_email
      ")

# 716 of the 9032 consumers opened the email blast.

# Was the pop-up advertisement successful?

sqldf("
      SELECT SUM(sales_amount_total) AS sum_sales,
      AVG(sales_amount_total) AS avg_sales,
      pop_up FROM purchase, pop_up
      WHERE purchase.consumer_id=pop_up.consumer_id
      GROUP BY pop_up
      ")

# No. Pop ups don't really help to increase sales because both the sum 
# and average sales are higher with those who did not receive pop ups.

# Did the consumer who spend the least during online shopping open the pop_up message?

sqldf("
      SELECT consumer_id FROM purchase
      ORDER BY sales_amount_total LIMIT 1
      ")

sqldf("
      SELECT consumer_id, pop_up FROM pop_up WHERE consumer_id='5887286353'
      ")

sqldf("
      SELECT consumer_id, pop_up FROM pop_up WHERE consumer_id = (?)
      ")

sqldf("
      SELECT consumer_id, pop_up FROM pop_up WHERE consumer_id = 
      (SELECT consumer_id FROM purchase
      ORDER BY sales_amount_total LIMIT 1)
      ")

# The consumer who spent the least during online shopping did not open the pop_up message.

# Was the email blast successful?

sqldf("
      SELECT SUM(sales_amount_total) AS sum_sales,
      AVG(sales_amount_total) AS avg_sales, 
      opened_email FROM purchase, email
      WHERE purchase.consumer_id=email.consumer_id
      GROUP BY opened_email
      ")

#Yes. The average sales for those who opened the email is almost twice of those who didn't.

# Did the consumer who spend the most during online shopping opened the email message?

sqldf("
      SELECT consumer_id FROM purchase
      ORDER BY sales_amount_total DESC LIMIT 1
      ")

sqldf("
      SELECT consumer_id, opened_email FROM email
      WHERE consumer_id='5955534353'
      ")

sqldf("
      SELECT consumer_id, opened_email FROM email WHERE consumer_id IN (?)
      ")

sqldf("
      SELECT consumer_id, opened_email from email WHERE consumer_id IN 
      (SELECT consumer_id FROM purchase
      ORDER BY sales_amount_total DESC LIMIT 1)
      ")

#Yes. The consumer who spent the most during online shopping opened the email message.
