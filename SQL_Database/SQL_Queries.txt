/* Queries from Part1-Section 1 */

/* Displays the amount of books in each wishlist, other useful information about the wishlist, and the ID of the customer who created the wishlist */
SELECT W.WishlistID, W.WishlistName, W.AccountID, COUNT(B.ISBN)
FROM Displayed_In AS D, Wishlist AS W, BOOK AS B
WHERE D.WishlistID = W.WishlistID AND B.ISBN = D.ISBN
GROUP BY W.WishlistID, W.WishlistName, W.AccountID;

/* Displays a complete list of customers, how many books they have purchased, the amount they have spent on purchases, and additional customer information */
SELECT Cu.AccountID, Cu.FirstName, Cu.LastName, Cu.Email, SUM(T.Amount), COUNT(T.TransID) 
FROM Trans AS T, Customer AS Cu, Contained_In AS Co
WHERE Cu.AccountID = T.AccountID AND T.TransID = Co.TransID
GROUP BY Cu.AccountID, Cu.FirstName, Cu.LastName, Cu.Email;

/* Displays the five most frequently recommended books in the bookstore */
SELECT B.ISBN, B.Title, B.Price, B.FirstName, B.LastName, COUNT(B.ISBN)
FROM Book AS B, Held_In AS H
WHERE B.ISBN = H.ISBN
GROUP BY B.ISBN, B.Title, B.Price, B.FirstName, B.LastName
ORDER BY COUNT(B.ISBN) DESC, B.Title ASC
LIMIT 5;


/* Queries from Checkpoint 2 */

/* Finds the titles of all books by Pratchett that cost less than $10 */
SELECT Title
FROM BOOK
WHERE LastName = "Pratchett" AND Price < 10;

/* Gives all the titles and their dates of purchase made by a single customer named Darius Kharazi */
SELECT B.Title, T.Date
FROM BOOK AS B, CONTAINED_IN AS Co, TRANS AS T, CUSTOMER AS Cu
WHERE Co.ISBN = B.ISBN AND Co.TransID = T.TransID AND T.AccountID = Cu.AccountID AND Cu.LastName = "Kharazi" AND Cu.FirstName = "Darius";

/* Finds the titles and ISBNs for all books with less than 5 copies in stock */
SELECT DISTINCT Title, ISBN
FROM BOOK
WHERE Quantity < 5;

/* Gives all the customers who purchased a book by Pratchett and the titles of Pratchett books they purchased */
SELECT Cu.AccountID, Cu.FirstName, Cu.LastName, B.Title
FROM BOOK AS B, CONTAINED_IN AS Co, TRANS AS T, CUSTOMER AS Cu
WHERE Co.ISBN = B.ISBN AND Co.TransID = T.TransID AND T.AccountID = Cu.AccountID AND B.LastName = "Pratchett";

/* Finds the total number of books purchased by a single customer named Darius Kharazi */
SELECT Cu.AccountID, Cu.FirstName, Cu.LastName, COUNT(T.TransID)
FROM CONTAINED_IN AS Co, TRANS AS T, CUSTOMER AS Cu
WHERE Co.TransID = T.TransID AND T.AccountID = Cu.AccountID AND Cu.AccountID = 3;

/* Find the customer who has purchased the most books and the total number of books they have purchased */
SELECT Cu.AccountID, Cu.FirstName, Cu.LastName, COUNT(T.TransID)
FROM CONTAINED_IN AS Co, TRANS AS T, CUSTOMER AS Cu
WHERE Co.TransID = T.TransID AND T.AccountID = Cu.AccountID
GROUP BY T.TransID
ORDER BY COUNT(T.TransID) DESC, Cu.AccountID ASC, Cu.FirstName ASC, Cu.LastName ASC
LIMIT 1;

/* Counts the number of books written by all authors */
SELECT ISBN, FirstName, LastName, COUNT(ISBN)
FROM BOOK
GROUP BY LastName;

/* Finds the first and last name of authors who have written books in 2003 */
SELECT FirstName, LastName
FROM BOOK
WHERE Year = "2003";

/* Displays the titles of books in customers’ wish lists named “TheList” */
SELECT B.Title
FROM BOOK AS B, DISPLAYED_IN AS D, WISHLIST AS W
WHERE B.ISBN = D.ISBN AND D.WishlistID = W.WishListID AND WishlistName = "TheList";


/* Queries from Checkpoint 3 */

/* Provides a list of customer names, along with the total dollar amount each customer has spent */
SELECT Cu.FirstName, Cu.LastName, SUM(T.Amount)
FROM TRANS AS T, CUSTOMER AS Cu
WHERE T.AccountID = Cu.AccountID
GROUP BY Cu.AccountID, Cu.FirstName, Cu.LastName;

/* Provides a list of customer names and e-mail addresses for customers who have spent more than the average customer */
SELECT Cu.FirstName, Cu.LastName, Cu.Email, SUM(T.Amount)
FROM TRANS AS T, CUSTOMER as Cu
WHERE T.AccountID = Cu.AccountID
GROUP BY Cu.AccountID, Cu.FirstName, Cu.LastName
HAVING SUM(T.Amount) > (SELECT AVG(amt)
				      FROM (SELECT SUM(Tr.Amount) AS amt
					        FROM TRANS AS Tr, CUSTOMER as Cus
				                  WHERE Tr.AccountID = Cus.AccountID
					        GROUP BY Cus.AccountID, Cus.FirstName, Cus.LastName));

/* Provides a list of the titles in the database and associated total copies sold to customers, sorted from the title that has sold the most individual copies to the title that has sold the least */
SELECT B.Title, COUNT(B.ISBN)
FROM BOOK AS B, CONTAINED_IN AS C
WHERE B.ISBN = C.ISBN
GROUP BY B.Title
ORDER BY  COUNT(B.ISBN) DESC;

/* Provides a list of the titles in the database and associated dollar totals for copies sold to customers, sorted from the title that has sold the highest dollar amount to the title that has sold the smallest */
SELECT Title, QuantitySold
FROM BOOK
ORDER BY QuantitySold DESC;

/* Finds the most popular author in the database (the one who has sold the most books) */
SELECT FirstName, LastName, SUM(QuantitySold)
FROM BOOK
GROUP BY LastName, FirstName
ORDER BY SUM(QuantitySold) DESC
LIMIT 1;

/* Finds the most profitable author in the database for this store (the one who has brought in the most money) */
SELECT FirstName, LastName, SUM(QuantitySold*Price)
FROM BOOK
GROUP BY LastName, FirstName
ORDER BY SUM(QuantitySold*Price) DESC
LIMIT 1;

/* Provides a list of customer information for customers who purchased anything written by the most profitable author in the database */
SELECT Cu.FirstName, Cu.LastName, Cu.Email
FROM BOOK AS B, CONTAINED_IN AS Co, TRANS AS T, CUSTOMER AS Cu
WHERE B.ISBN = Co.ISBN AND Co.TransID = T.TransID AND T.AccountID = Cu.AccountID AND ((B.LastName) IN (SELECT LastName
				FROM BOOK
				GROUP BY LastName, FirstName
				ORDER BY SUM(QuantitySold*Price) DESC
				LIMIT 1));

/* Provides the list of authors who wrote the books purchased by the customers who have spent more than the average customer */
SELECT B.FirstName, B.LastName
FROM TRANS AS T, CUSTOMER as Cu, CONTAINED_IN AS Co, BOOK AS B
WHERE T.AccountID = Cu.AccountID AND T.TransId = Co.TransID AND Co.ISBN = B.ISBN
GROUP BY Cu.AccountID, Cu.LastName, Cu.FirstName
HAVING SUM(T.Amount) > (SELECT AVG(amt)
				FROM (SELECT SUM(Tr.Amount) AS amt
					FROM TRANS AS Tr, CUSTOMER as Cus
				            WHERE Tr.AccountID = Cus.AccountID
					GROUP BY Cus.AccountID, Cus.FirstName, Cus.LastName));

/* Counts the number of books written by each author */
SELECT LastName, COUNT(Title)
FROM BOOK
GROUP BY LastName;

/* Gets the last name of the customers who have books currently being shipped */
SELECT C.LastName
FROM CUSTOMER AS C, TRANS AS T
WHERE C.AccountID = T.AccountID AND T.Shipping = 1;

/* Display the titles of books in the customer’s wish list with an ID of “396” */
SELECT B.Title
FROM WISHLIST AS W, DISPLAYED_IN AS D, BOOK AS B
WHERE W.WishlistID = D.WishlistID AND D.ISBN = B.ISBN AND W.WishlistID = 396;