# Standard SQL and T-SQL Project

## The project aims to building and use a database of a hypothetical Health Record Management System, HRMS

###  Detail tasks that have been done is summerized here below: 
  #### Creating, Altering, and Droping :
                              o Databse
                              o Tables 
                              o Views 
                              o Stored Procedures 
                              o Functions 
                              o Triggers 
                              o Constraints

  #### DML – Manipulating Data (using INSERT, DELETE, UPDATE) :     
                              o Databse
                              o Tables
                              o Views 
                              o Stored Procedures
                              o Functions (User Defined Functions, Table Valued Vs Scalar Valued) 
                              o Triggers (  FOR/AFTER INSERT, DELETE, UPDATE  
                                            INSTEAD OF INSERT, DELETE, UPDATE ) 
                          
  #### Data retrieval – SELECT Statement :                 
                              o Structure of SELECT statement
                              o Filter Conditions 
                              o String manipulation functions 
                              o Grouping and Aggregate values
                              o Subqueries - Correlated and Uncorrelated Subqueries 
                              o Retrieving data from multiple Tables 
                                    JOIN – CROSS JOIN, INNER JOIN, LEFT JOIN, RIGHT JOIN, FULL JOIN 
                                  
  #### Using CTE to group queries and retrieve datas
  
  ```SQL
  /*
******************** Targeted Technology Institute **********************************
Welcome to Targeted Technology Institute's Practical SQL Server Database Development Training

LEVEL: From Basic to Advanced

Here we're building and use a database for a hypothetical Health Record Management System, HRMS

	STARTING TABLES : Patient, Doctor, Disease and Diagnosis, Medicine, PharmacyPersonel, Prescription, MedicinePrescribed, ...
	---> You may be required to add more tables

	- Please understand the design tables and their relationships
	- If at any point you couldn't insert the given data nor encountered a problem - try to figure out what caused it.
	- As instructed under each insert statement, pls insert some more data into the tables

**************** PROJECT DONE BY: EYASU HILUF GEBRETEKLE *************************************

*/

USE master;
GO

DROP DATABASE IF EXISTS HRMSDB;
GO

CREATE DATABASE HRMSDB;
GO

USE HRMSDB;


-- Creating tables with applicable constraints 
GO
CREATE TABLE Patient
(
	mrn CHAR(5) NOT NULL,
	pFName VARCHAR(30) NOT NULL,
	pLName VARCHAR(30) NOT NULL,
	PDoB DATE NOT NULL,
	insuranceId CHAR(7) NULL, 
	gender CHAR(1) NOT NULL,
	SSN CHAR(11) NULL,
	stAddress VARCHAR(25) NOT NULL,
	city VARCHAR(25) NOT NULL,
	[state] CHAR(2) NOT NULL,
	zipCode CHAR(5) NOT NULL,
	registeredDate DATE NOT NULL DEFAULT GETDATE(),
	CONSTRAINT PK_Patient_mrn PRIMARY KEY (mrn),
	CONSTRAINT CK_Patient_mrn_Format CHECK(mrn LIKE '[A-Z][A-Z][0-9][0-9][0-9]'),
	CONSTRAINT UQ_Patient_insuranceId UNIQUE (insuranceId),
	CONSTRAINT CK_Patient_gender_Format CHECK(gender IN ('F', 'M', 'U')),
	CONSTRAINT CK_Patient_SSN_Format CHECK ((SSN LIKE '[0-9][0-9][0-9][-][0-9][0-9][-][0-9][0-9][0-9][0-9]') AND (SSN NOT LIKE '000-00-0000')),
	CONSTRAINT UQ_Patient_SSN UNIQUE (SSN),
	CONSTRAINT CK_Patient_state_Format CHECK([state] LIKE '[A-Z][A-Z]'),
	CONSTRAINT CK_Pateint_zipCode_Fomrat CHECK((zipCode LIKE '[0-9][0-9][0-9][0-9][0-9]') AND (zipCode NOT LIKE '00000'))
);

GO
CREATE TABLE Employee
(
	empId CHAR(5) NOT NULL,
	empFName VARCHAR(25) NOT NULL,
	empLName VARCHAR(25) NOT NULL,
	SSN CHAR(11) NOT NULL,
	DoB DATE NOT NULL,
	gender CHAR(1) NOT NULL,
	salary DECIMAL(8,2) NULL,
	employedDate DATE NOT NULL,
	strAddress VARCHAR (30) NOT NULL,
	apt VARCHAR(5) NULL,
	city VARCHAR(25) NOT NULL,
	[state] CHAR(2) NOT NULL,
	zipCode CHAR(5) NOT NULL,
	phoneNo CHAR(14) NOT NULL,
	email VARCHAR(50) NULL,
	empType VARCHAR(20) NOT NULL,
	CONSTRAINT PK_Employee_empId PRIMARY KEY (empId)
);

GO
CREATE TABLE Disease
(
	dId INT NOT NULL,	
	dName VARCHAR(100) NOT NULL,
	dCategory VARCHAR(50) NOT NULL,
	dType VARCHAR(40) NOT NULL,
	CONSTRAINT PK_Disease_dId PRIMARY KEY (dId)
);

GO
CREATE TABLE Doctor
(
	empId CHAR(5) NOT NULL, 
	docId CHAR(4) NOT NULL,
	licenseNo CHAR(11) UNIQUE NOT NULL,
	licenseDate DATE NOT NULL,
	[rank] VARCHAR(25) NOT NULL,
	specialization VARCHAR(50) NOT NULL,
	CONSTRAINT PK_Doctor_docId PRIMARY KEY (docId),
	CONSTRAINT FK_Doctor_Employee_empId FOREIGN KEY (empId) REFERENCES Employee (empId) ON DELETE CASCADE ON UPDATE CASCADE
);

GO
CREATE TABLE Diagnosis
(
	diagnosisNo INT NOT NULL,
	mrn CHAR(5) NOT NULL,
	docId CHAR(4) NULL,
	dId INT NOT NULL,
	diagDate DATE DEFAULT GETDATE() NOT NULL,
	diagResult VARCHAR(1000) NOT NULL,
	CONSTRAINT PK_Diagnosis_diagnosisNo PRIMARY KEY (diagnosisNo),
	CONSTRAINT FK_Diagnosis_Patient_mrn FOREIGN KEY (mrn) REFERENCES Patient(mrn),
	CONSTRAINT FK_Diagnosis_Doctor_docId FOREIGN KEY (docId) REFERENCES Doctor(docId) ON DELETE SET NULL ON UPDATE CASCADE,
	CONSTRAINT FK_Diagnosis_Disease_dId FOREIGN KEY (dId) REFERENCES Disease(dId)
);

GO 
CREATE TABLE PharmacyPersonel
(
	empId CHAR(5) NOT NULL,
	pharmacistLisenceNo CHAR (11) NOT NULL,
	licenseDate DATE NOT NULL,
	PCATTestResult INT NULL,
	[level] VARCHAR (40) NOT NULL,
	CONSTRAINT FK_PharmacyPersonel_empId FOREIGN KEY (empId) REFERENCES Employee (empId),
	CONSTRAINT UQ_PharmacyPersonel_pharmacistLisenceNo UNIQUE (pharmacistLisenceNo)
);
GO

CREATE TABLE Medicine
(
	mId SMALLINT NOT NULL,
	brandName VARCHAR(40) NOT NULL,
	genericName VARCHAR(50) NOT NULL,
	qtyInStock INT NOT NULL,
	[use] VARCHAR(50) NOT NULL,
	expDate DATE NOT NULL,
	unitPrice DECIMAL(6,2) NOT NULL,
	CONSTRAINT PK_Medicine_mId PRIMARY KEY (mId)
);
GO


CREATE TABLE Prescription
(
	prescriptionId INT NOT NULL,
	diagnosisNo INT NOT NULL,
	prescriptionDate DATE NOT NULL DEFAULT GETDATE(),
	CONSTRAINT PK_Prescription_prescriptionId PRIMARY KEY (prescriptionId),
	CONSTRAINT FK_Prescription_Diagnosis_diagnosisNo FOREIGN KEY (diagnosisNo) REFERENCES Diagnosis(diagnosisNo) 
);
GO


CREATE TABLE MedicinePrescribed
(
	prescriptionId INT NOT NULL,
	mId SMALLINT NOT NULL,
	dosage VARCHAR(50) NOT NULL,
	numberOfAllowedRefills TINYINT NOT NULL,
	CONSTRAINT PK_MedicinePrescribed_prescriptionId_mId PRIMARY KEY(prescriptionId, mId),
	CONSTRAINT FK_MedicinePrescribed_prescriptionId FOREIGN KEY (prescriptionId) REFERENCES Prescription(prescriptionId),
	CONSTRAINT FK_MedicinePrescribed_mId FOREIGN KEY (mId) REFERENCES Medicine(mId)
);

GO
-- Inserting data into tables of HRMS database, 
INSERT INTO Disease 
	VALUES (1, 'Strep throat', 'Bacterial infections', 'Contageous'), 
		   (2, 'Acquired hemophilia','Blood Diseases','Type2'), 
		   (3, 'Accessory pancreas ','Digestive Diseases','xyz'),
		   (4, 'Cholera', 'Bacterial infections', 'Contageous'),
		   (5, 'Acatalasemia ','Blood Diseases', 'Chronic'),
		   (6, 'Acute fatty liver of pregnancy','Digestive Diseases','Non Contageous'),
-- Insert 5 more diseases --IS DONE
		   (7, 'Maleria','Blood infection','non contigeous'),
		   (8, 'HIV','Sexual Transmitting Disease','acute/contigeous'),
		   (9, 'Gastric','Stomach Ache','mild'),
		   (10, 'Typhoid','Blood infection','mild'),
		   (11, 'Gonorhea','Skin Disease','mild');

GO
INSERT INTO Medicine 
	VALUES (1, 'Xaleto','Rivaroxi...', 200, 'Anticoagulant', '2022-12-31', 67.99), 
		   (2, 'Eliquis', 'Apiza...',500, 'ACE inhitor', '2023-06-06', 23.49),
		   (3, 'Tran..Acid', 'Inhibitor', 600, 'Something','2024-12-31', 9.99),
		   (4, 'Fosamax', 'alendronate tablet', 200, 'treat certain types of bone loss in adults','2023-12-31',58),
		   (5, 'Hexalen capsules','altretamine',150,'Ovarian cancer','2023-12-31',26),
		   (6, 'Prozac','Fluoxetine...', 125, 'Anti-depressent', '2023-12-31', 43.99),
		   (7, 'Glucofage','Metformine...', 223, 'Anti-Diabetic', '2025-10-13', 22.99),
		   (8, 'Advil', 'Ibuprofine', 500, 'Pain killer', '2023-01-01', 15),
		   (9, 'Amoxy','Amoxilcillin', 2000, 'Antibiotics', '2024-12-31', 27 ),
-- Insert 5 more Medicine --IS DONE
		   (10, 'Chloroquine','tablet', 750, 'repels being caught', '2024-10-03', 55 ),
		   (11, 'antiretroviral','ART', 380, 'reduces viral load', '2023-09-19', 32 ),
		   (12, 'Metronidazole','H.pylori', 100, 'Antibiotics', '2025-12-31', 64 ),
		   (13, 'Tyhoid vacine','vacine', 900, 'injection', '2024-12-31', 70 ),
		   (14, 'Cephalosporin','N.gonorrhoeae', 120, 'Antimicrobial resistance', '2023-01-03', 83 );

GO
INSERT INTO Patient
	VALUES	('PA001', 'Kirubel', 'Wassie', '09-12-1985', 'kki95', 'M','031-56-3472','2578 kk st', 'Alexandria', 'VA', '22132','2018-01-01'),
			('PA002', 'Harsha', 'Sagar', '11-19-1980', 'wes41', 'F','289-01-6994','3379 bb st', 'Miami', 'FL', '19376','2017-09-02'),
			('PA003', 'Fekadu', 'Gonfa', '05-20-1990', 'oi591', 'M','175-50-1235','1538 ff st',  'Seattle', 'WA', '35800','2018-01-01'),
			('PA004', 'Arsema', 'Negera', '01-25-1978', 'iuu53', 'F','531-31-3308','2928 aa st', 'Silver Spring', 'MD', '51763','2017-02-01'),
			('PA005', 'John', 'Craig', '12-31-1965', 'iu979', 'M','231-61-8422','1166 jj st', 'Alexandria', 'VA', '22132','2019-01-03'),
			('PA006', 'Maria', 'Michael', '08-12-1979', 'mk216', 'F', '786-32-0912','7866 mm st', 'Silver Spring', 'MD', '51763','2018-01-01'),
			('PA007', 'Derek', 'Nagaraju', '02-18-1975', 'sd025', 'M','120-21-6743','8889 ff st', 'Silver Spring', 'MD', '51763','2019-02-20'),
			('PA008', 'Birtukan', 'Wale', '01-27-1989', 'is489', 'F','013-32-6789','5464 bb st', 'Seattle', 'WA', '35800','2018-01-05'),
			('PA009', 'Yehualashet', 'Lemma', '05-15-1983', 'kl526', 'M', '745-21-0321','3338 yy st', 'Miami', 'FL', '19376','2018-01-01'),
			('PA010', 'Selam', 'Damtew', '09-22-1970', 's5561', 'F', '300-00-2211','4651 ss st', 'Huston', 'TX', '11156','2018-09-10'),
			('PA011', 'Simon', 'Kifle', '08-11-1968', 'dc256', 'M', '333-44-5555','2287 sk st', 'Springfield', 'VA', '22132','2018-01-01'),
			('PA012', 'Nany', 'Tekle', '08-25-1970', 'po724', 'F', '222-88-4444','1313 nt st', 'Springfield', 'VA', '22132','2019-03-01'),
			('PA013', 'Adane', 'Belay', '11-16-1984', 'sa366', 'M', '001-22-3216','4687 ab st', 'Seattle', 'WA', '35800','2017-02-02'),
			('PA014', 'Genet', 'Misikir', '05-28-1982', 'l8773', 'F', '999-44-2299','00468 gm st', 'Richmond', 'VA', '55982','2017-11-12'),
			('PA015', 'Mikiyas', 'Tesfaye', '01-15-1979', 'w6321', 'M', '221-90-8833','5090 mt st', 'Alexandria', 'VA', '22132','2017-11-12'),
-- Insert 5 more patients --IS DONE
			('PA016', 'Tsedale', 'Moges', '08-08-1963', 'ts789', 'F', '325-61-4568','92601 ml st', 'Mary Land', 'DC', '65482','2022-01-12'),
			('PA017', 'Eyasu', 'Gebretekle', '01-15-1981', 'eg548', 'M', '102-12-4658','54610 ct st', 'Carolina', 'DC', '32456','2020-05-09'),
			('PA018', 'Alem', 'Yehun', '01-15-1987', 'ay210', 'M', '958-23-6482','15468 ct st', 'California', 'CA', '78648','2022-11-12'),
			('PA019', 'Meklit', 'Gebretekle', '01-15-2000', 'mk458', 'F', '528-10-4587','25556 ot st', 'Ohio', 'OH', '64813','2023-01-01'),
			('PA020', 'Maereg', 'Gebretekle', '01-15-2010', 'mg904', 'F', '601-87-3468','50049 nt st', 'New York', 'DC', '15649','2021-11-12');
GO
INSERT INTO Employee 
	VALUES ('EMP01', 'Neftalem', 'Medhanie', '050-23-1111', '1971-10-07','M',78000,'2017-03-02','1293 Duncan Avenue', '198','Rockville', 'MD','20871', '(707) 890-3212', 'nef@yahoo.com','P'),
		   ('EMP02', 'Mark', 'Lewis', '060-23-2222', '1972-09-12','M',67500,'2018-12-02','4080 Marshall Street', '800','Washington', 'DC','20021', '(202) 890-9032', 'mark@gmail.com','C'),
		   ('EMP03', 'Dennis', 'Price', '060-21-3333', '1973-10-09','F',89800,'2016-03-02','1331 Edsel Road', 'L21','Woodbridge', 'VA','20321', '(570) 000-2112', 'kathe@gmail.com','M'),
		   ('EMP04', 'Robert', 'Iversen', '070-23-4444', '1974-07-01','M',100000,'2017-09-01','786 Eagle Lane', '234','Columbia', 'MD','20990', '(301) 890-3200', 'rob@yahoo.com','P'),
		   ('EMP05', 'Rosie', 'Seiler', '080-23-5555', '1975-03-07','F',79300,'2016-03-02','123 Ky St', '698','Bethesda', 'MD','20871', '(332) 890-3212', 'rosie@yahoo.com','A'),
		   ('EMP06', 'Emmanuel', 'Kepa', '908-23-6666', '1985-09-15','M',89800,'2018-12-02','5 Poe Lane', '832','Washington', 'DC','20021', '(204) 890-9032', 'emma@gmail.com','A'),
		   ('EMP07', 'Andrew', 'Neftalem', '090-21-7777', '1986-11-03','M',100100,'2015-03-02','1378 Gateway Road', '823','Alexandia', 'VA','20321', '(703) 000-2112', 'dennisned@gmail.com','A'),
		   ('EMP08', 'Liang', 'Porter', '111-23-8888', '1987-01-01','M',78000,'2017-09-01','825 Victoria Street', '109','Columbia', 'MD','20990', '(249) 890-3200', 'lian@yahoo.com','P'),
		   ('EMP09', 'Sarah', 'Kathrin', '222-23-9999', '1988-03-18','F',90800,'2014-03-02','1389 Finwood Road', '007','Germantown', 'MD','20871', '(191) 890-3212', 'rosie@yahoo.fr','P'),
		   ('EMP10', 'Christopher', 'Rasmussen', '333-23-0000', '1989-03-23','M',62000,'2018-12-02','3520 Nash Street', '002','Washington', 'DC','20021', '(320) 890-9032', 'chris@gmail.com','C'),
		   ('EMP11', 'Ruth', 'Kumar', '444-21-1122', '1990-11-24','F',90200,'2019-03-02','4656 Byrd Lane', 'L21','Arlington', 'VA','20321', '(521) 000-2112', 'ruth@gmail.com','A'),
		   ('EMP12', 'Stefan', 'Xu', '444-23-2233', '1990-09-01','M',68000,'2013-09-01','3583 Stadium Drive', '100','Beltsville', 'MD','20990', '(260) 890-3200', 'stef@yahoo.com','M'),
		   ('EMP13', 'Jessamine', 'Seiler', '555-23-3344', '1982-11-28','F',90200,'2018-03-02','1337 Havanna Street', '498','Clarksburg', 'MD','20871', '(101) 890-3212', 'jes@yahoo.co.uk','M'),
		   ('EMP14', 'Enza', 'Kepa', '666-23-4455', '1990-09-30','F',85300,'2011-12-02','2780 Irish Lane', NULL,'Washington', 'DC','20021', '(511) 890-9032', 'enz@gmail.com','C'),
		   ('EMP15', 'Andrew', 'Kumar', '777-21-5566', '1983-10-25','M',120800,'2010-03-02','3048 James Avenue', 'L21','Fairfax', 'VA','20321', '(911) 000-2112', 'andkum@gmail.com','P'),
		   ('EMP16', 'Ermias', 'Henriksen', '888-23-6677', '1983-09-16','M',78000,'2017-09-01','714 Chicago Avenue', NULL,'Laurel', 'MD','20990', '(199) 890-3200', 'ermias@yahoo.com','P'),
		   ('EMP17', 'Petra', 'Seiler', '123-23-3456', '1980-09-07','F',45000,'2018-03-02','123 Ky St', '198','Clarksburg', 'MD','20871', '(101) 890-3212', 'ps@yahoo.com','P'),
		   ('EMP18', 'Peter', 'Kepa', '908-23-3432', '1990-09-07','M',72300,'2018-12-02','907 12 St', NULL,'Washington', 'DC','20021', '(201) 890-9032', 'pk@gmail.com','C'),
	       ('EMP19', 'Dennis', 'Kumar', '093-21-3456', '1983-10-03','M',120800,'2019-03-02','123 Ky St', 'L21','Manassas', 'VA','20321', '(571) 000-2112', 'dk@gmail.com','P'),
		   ('EMP20', 'Gang', 'Xu', '903-23-9056', '1983-09-01','M',79300,'2017-09-01','213 Coles rd', NULL,'Columbia', 'MD','20990', '(240) 890-3200', 'gxu@yahoo.com','P'),
-- Insert 8 more employees --IS DONE
			('EMP21', 'Kebede', 'Tesema', '205-03-4687', '1980-01-14','M',58900,'2015-12-24','780 crank st', 'KT52','Mary Land', 'MD','46870', '(244) 290-4875', 'kt@iol.com','C'),
			('EMP22', 'Gilbert', 'Matono', '234-12-0482', '1999-09-01','M',65200,'2010-09-01','845 fake rd', 'gt56','Arlington', 'VA','54781', '(243) 891-2456', 'gm@gmail.com','P'),
			('EMP23', 'Patricia', 'Armstrong', '501-88-7830', '1995-05-11','F',93000,'2009-09-01','936 fake st', NULL,'Masachusete', 'OH','20113', '(240) 587-2458', 'pa@outlook.com','P'),
			('EMP24', 'Sandler', 'Adam', '302-01-5050', '1996-10-10','M',74360,'2020-04-12','156 another rd', NULL,'Columbia', 'MD','34587', '(240) 760-3200', 'sa@ru.com','P'),
			('EMP25', 'Ana', 'Chausete', '223-70-0564', '1992-11-03','F',35900,'2023-01-01','939 stupid st', NULL,'Beltsville', 'MD','90207', '(240) 521-6320', 'ac@yahoo.com','C'),
			('EMP26', 'Patrick', 'Okello', '654-90-0457', '1988-07-02','M',92300,'2010-07-09','278 confus st', NULL,'Laurel', 'MD','51246', '(241) 489-9250', 'po@yahoo.com','P'),
			('EMP27', 'Sabrina', 'Goerge', '090-15-8705', '1974-08-12','F',82700,'2009-10-05','524 shino rd', NULL,'Manassas', 'VA','77021', '(290) 512-4568', 'sg@gmail.com','P'),
			('EMP28', 'Dylan', 'Sante', '621-72-5247', '2010-07-20','F',35700,'2022-06-13','431 gandi st', NULL,'Woodbridge', 'VA','10354', '(244) 335-5201', 'ds@aol.com','C');
GO
INSERT INTO Doctor 
	VALUES  ('EMP10','MD01', 'KNO-09-6667','2017-09-10', 'Senior','Infectious Disease'),
			('EMP12','MD02', 'BAV-00-9456','2016-08-10', 'senior','Family medicine'),
			('EMP04','MD03', 'LIC-22-0678', '2015-12-31','Senior','Intenal Medicine'),
			('EMP17','MD04', 'KAL-16-5420','2018-09-03','Junior','Cardiologist'),
		    ('EMP15','MD06', 'XYZ-66-7600','2017-02-05', 'Junior','Infectious Disease'),
-- Insert 3 more doctors (from the 8 employees you inserted above) --IS DONE
			('EMP21','MD07', 'JDF-07-5148','2020-12-15', 'Senior','Blood infection'),
			('EMP22','MD08', 'KAS-34-6254','2017-05-21', 'Junior','Transmitive Disease'),
			('EMP23','MD09', 'DHI-52-1652','2017-07-18', 'Junior','Skin Disease');

GO
INSERT INTO Diagnosis 
	VALUES (1, 'PA003', 'MD03', 1, '2017-11-06', 'Positive'),
		   (2, 'PA009', 'MD02', 5, '2018-02-06', 'Positive'),
		   (3, 'PA012', 'MD01', 2, '2015-09-04', 'Negative'),
		   (4, 'PA005', 'MD02', 3, '2019-11-06', 'Negative'),
		   (5, 'PA014', 'MD04', 4, '2014-10-04', 'Negative'),
		   (6, 'PA001', 'MD02', 5, '2017-10-04', 'Positive'),
		   (7, 'PA004', 'MD01', 6, '2016-11-04', 'Positive'),
		   (8, 'PA011', 'MD03', 2, '2016-11-04', 'Positive'),
		   (9, 'PA001', 'MD04', 2, GETDATE(),'Positive'),
		   (10,'PA002', 'MD03', 3, GETDATE(),'Not conclusive'),
		   (11,'PA003', 'MD02', 3, GETDATE(),'Negative'),
		   (12,'PA004', 'MD01', 3, GETDATE(),'Not conclusive'),
		   (13,'PA005', 'MD02', 2, GETDATE(),'Positive'),
		   (14,'PA006', 'MD03', 2, GETDATE(),'Not conclusive'),
		   (15,'PA007', 'MD04', 1, GETDATE(),'Positive'),
		   (16,'PA008', 'MD03', 3, GETDATE(),'Not conclusive'),
-- Insert 10 more diagnosis --IS DONE
		   (17, 'PA017', 'MD08', 8,'2022-11-07', 'Positive'),
		   (18, 'PA020', 'MD07', 7, GETDATE(),'Positive'),
		   (19,'PA015', 'MD06', 5, GETDATE(),'Not conclusive'),
		   (20, 'PA018', 'MD09', 2, '2021-01-14', 'Positive'),
		   (21, 'PA016', 'MD06', 4, GETDATE(),'Positive'),
		   (22,'PA013', 'MD04', 5, GETDATE(),'Not conclusive'),
		   (23, 'PA017', 'MD08', 7, '2016-11-04', 'Positive'),
		   (24, 'PA020', 'MD07', 8, GETDATE(),'Positive'),
		   (25,'PA015', 'MD09', 5, GETDATE(),'Not conclusive'),
		   (26,'PA016', 'MD04', 6, GETDATE(),'Not conclusive');

GO
INSERT INTO Prescription 
	VALUES (10, 1, '2019-01-02'),
		   (11, 2, '2017-06-02'),
		   (12, 3, '2018-01-02'),
		   (13, 9, '2019-04-2'),
		   (14, 13, '2016-09-12'),
		   (15, 7, '2019-03-25'), 
		   (16, 14, '2019-03-26'), 
		   (17, 16, '2019-03-27'), 
		   (18, 8, '2019-03-26'),
-- Insert 10 more precsriptions --IS DONE
		   (19, 26, GETDATE()),
		   (20, 24, GETDATE()),
		   (21, 25, GETDATE()),
		   (22, 21, GETDATE()),
		   (23, 20, GETDATE()),
		   (24, 23, GETDATE()), 
		   (25, 19, GETDATE()), 
		   (26, 18, GETDATE()), 
		   (27, 17, GETDATE());

GO
INSERT INTO MedicinePrescribed 
	VALUES (10, 3,'As you wish', 2),
		   (14, 12,'As you wish', 4),
		   (12, 5,'As you wish', 6),
		   (11, 1,'As you wish', 3),
		   (13, 7,'As you wish', 4),
		   (18, 4, '3 times daily', 3),
		   (17, 8, '2 times daily', 2),
		   (10, 5, '2 times a day', 3), 
		   (12, 13, '3 times daly', 2),
-- Insert 10 more medicens prescribed --IS DONE
		   (18, 10,'Twice a day', 2),
		   (19, 11,'3 per pay', 3),
		   (20,12,'morning only', 1),
		   (22, 4,'Every 6 hour', 8),
		   (23, 7,'During night', 1),
		   (24, 8, '1 After breakfast', 1),
		   (25, 13, '2 times daily', 2),
		   (26, 5, 'Every 8 hours', 3), 
		   (27, 14, '1 before meal', 3);

GO
INSERT INTO PharmacyPersonel 
	VALUES ('EMP02', 'GP-003', '2016-02-06', 86, 'Senior'),
		   ('EMP06', 'CP-073', '2022-04-13', 93, 'Junior'),
		   ('EMP08', 'AB-099', '2017-02-16', 93, 'Junior'),
-- Insert 3 more Pharmacy personnel (from the 8 employees you inserted above) --IS DONE
		   ('EMP16', 'MD-784', '2015-02-06', 75, 'Senior'),
		   ('EMP20', 'OK-654', '2017-04-13', 81, 'Senior'),
		   ('EMP18', 'PL-351', '2021-02-16', 85, 'Junior');
GO
/*
SELECT * FROM Diagnosis
SELECT * FROM Disease 
SELECT * FROM Doctor 
SELECT * FROM Employee 
SELECT * FROM Medicine 
SELECT * FROM MedicinePrescribed
SELECT * FROM Patient 
SELECT * FROM PharmacyPersonel
SELECT * FROM Prescription
*/

/****************** The SELECT Statement *****************************

SELECT statement
	- SELECT statement is used to query tables and views,

SYNTAX :
	SELECT select_list [ INTO new_table ]
	[ FROM table_source ] 
	[ WHERE search_condition ]
	[ GROUP BY group_by_expression ]
	[ HAVING search_condition ]
	[ ORDER BY order_expression [ ASC | DESC ] ]


Has has the following clauses inside, 
	- FROM clause
		: specifies the source of data, table or view
	- WHERE clause
		: search condition / criteria 
	- GROUP BY clause
		: group by list
	- HAVING clause
		: search condition / criteria for out put of GROUP BY clause
	- ORDER BY clause
		: specify the condition to sort results

Sequence of execution/evaluation of SELECT statement. 
	1st : FROM clause
	2nd : WHERE clause
	3rd : GROPUP BY clause
	4th : HAVING clause
	5th : SELECT 
	6th : ORDER BY clause

IMPORTANT : To avoid erros in tables/views name resoulution, it is best to include
	        schema and object name

			If the table/view contains irregular characters such as spaces or other 
			special characters, you need to delimit, or enclose the name, either by 
			using " " or [ ]

			End all statements with semicolon (;) character. It is optional to use semi
			colon to terminate SELECT statement, however future versions will require 
			its use, therefore you should adopt the practice.
*/



--EXERCISE # 01
--Write SELECT statement to retrive the entire columns of Employee table by explicitly listing each column.
--NOTE (1) List all the columns to be retrived on SELECT statement
--NOTE (2) The name of each column must be separated by comma
	SELECT empId,
		   empFName,
		   empLName,
		   SSN,
		   DoB,
		   gender,
		   salary,
		   employedDate,
		   strAddress,
		   apt,
		   city,
		   [state],
		   zipCode,
		   phoneNo,
		   email, 
		   empType 
	FROM Employee

--EXERCISE # 02 
--Repeat the previous exercise, but SSN and DoB must be displated as the 1st and 2nd column
--NOTE : The order of columns list determine their display, regardless of the order of defination in source table.
	SELECT SSN,
		   DoB,
		   empId,
		   empFName,
		   empLName,
		   gender,
		   salary,
		   employedDate,
		   strAddress,
		   apt,
		   city,
		   [state],
		   zipCode,
		   phoneNo,
		   email, 
		   empType 
	FROM Employee


--EXERCISE # 03 
--Write SELECT statement to retrive the entire columns of Employee table using "star" character (*)
--NOTE : This method is frequently used for quick test, refrain using this short cut in production environment
	SELECT * 
	FROM Employee


--EXERCISE # 04 
--Write SELECT statement to retrive empId, empFName, empLName, phoneNo and email columns of Employee table.
	SELECT empId,
		   empFName,
		   empLName,
		   phoneNo,
		   email 
	FROM Employee


/*
--SELECT statement can be used to perform calculations and manipulation of data
	Operators, 
		'+' : Adding or Concatinate
		'-' : Subtract
		'*' : Multiply
		'/' : Divide
		'%' : Modulo
	The result will appear in a new column, repeated once per row of result set
	Note : Calculated expressions must be Scalar, must return only single value
	Note : Calculated expressions can operate on other colums in the same row
*/

	SELECT 5 % 3

--EXAMPLE : 
--Calculate the amount each type of medicine worth in stock
	SELECT *, FORMAT((qtyInStock * unitPrice),'C','en-us') [TOTAL AMOUNT OF EACH MEDICINE]
	FROM Medicine

--EXERCISE # 05 
--The unit price of each medicine is increased by 6%. Provide the list of medecine with 
--their respective rise in price.
	SELECT *, FORMAT((unitPrice * 0.06),'C','en-us') [6% RISE OF EACH MEDICINE]
	FROM Medicine

--EXERCISE # 06
--Calculate and display the old and new prices for each medicine 
	SELECT mId, brandName, UnitPrice, 
		CASE WHEN UnitPrice % 2 = 0 THEN UnitPrice * 0.9 
			ELSE UnitPrice 
		END AS NewUnitPrice, 
		CASE WHEN UnitPrice % 2 = 0 THEN UnitPrice 
			ELSE UnitPrice * 1.1 
		END AS OldUnitPrice
	FROM Medicine
	/*
	SELECT ((unitPrice % 2) = 0) [New Unit Price], ((unitPrice % 2) != 0) [Old Unit Price]
	FROM Medicine
	*/
--, unitPrice % 2 = 0 

--'+' Can be used to as an operator to concatenate (linked together as in a chain) the content of columns
--EXAMPLE : Get Employee Full Name of all employees
	SELECT empFName +' '+ empLName AS FULL_NAME
	FROM Employee

--EXERCISE # 07
--Write a code to display employee's Full Address, and Phone Number
	SELECT empFName +' '+ empLName AS FULL_NAME, 
		   strAddress +' '+city+' '+[state] AS FULL_ADDRESS, 
		   phoneNo
	FROM Employee


--CONCAT Function
--Is string function that joins two or more string values in an end-to-end manner.
--SYNTAX: CONCAT ( string_value1, string_value2, string_value2, ...)
--EXAMPLE : Write a code to display the Full Name of all employees using CONCAT function
	SELECT CONCAT(empFName,' ',empLName) FULL_NAME
	FROM Employee


--EXERCISE # 08
--Write a code to display employee's Full Address, and Phone Number using CONCAT function
	SELECT CONCAT(empFName,' ',empLName) [FULL_NAME], 
		   CONCAT(strAddress,' ',city,' ',[state]) [FULL_ADDRESS], 
		   phoneNo
	FROM Employee

/*
DISTINCT statement
	SQL query results are not truly relational, are not always unique, and not guranteed order
	Even unique rows in a source table can return duplicate values for some columns
	DISTINCT specifies only unique rows to apper in result set
--
*/

--EXAMPLE :
--Write a code to retrive the year of employement of all employees in Employee table
	SELECT CONCAT(empFName,' ',empLName) [FULL_NAME],
		   YEAR(employedDate) [EMPLOYED YEAR]
	FROM Employee

--Notice there are redudant instances on the result set. Execute the following code
--and observer the change of result set


--EXERCISE # 09
--Write a code that retrive the list of dosages from MedicinePrescribed table, use DISTINCT to eliminate duplicates
	SELECT DISTINCT dosage
	FROM MedicinePrescribed

/*
Column Aliases
	- At data display by SELECT statement, each column is named after it's source.
	- However, if required, columns can be relabeled using aliases
	- This is useful for columns created with expressons
	- Provides custom column headers.

Table Aliases
	- Used in FROM clause to provide convenient way of refering table elsewhere 
	  in the query, enhance readability 

*/

--EXAMPLE : 
--Display all columns of Disease table with full name for each column
	SELECT dId,	
		   CONCAT(dName,', ',dCategory,', ',dType) [DISEASE FULL NAME]
	FROM Disease

--EXERCISE # 10 
--Write the code to display the 10% rise on the price of each Medicine, use appropriate column alias for comoputed column
	SELECT *, FORMAT((unitPrice * 0.10),'C','en-us') [10% RISE OF EACH MEDICINE]
	FROM Medicine


--EXERCISE # 11
--Modify the above code to include the new price column with the name 'New Price'
	SELECT *, 
		   FORMAT((unitPrice * 0.10),'C','en-us') [10% RISE OF EACH MEDICINE], 
		   FORMAT((unitPrice + (unitPrice * 0.10)),'C','en-us') [NEW PRICE]
	FROM Medicine


--EXAMPLE :
--Using '=' signs to assign alias to columns

--EXERCISE # 12
--Use '=' to assign alias for the new price of all medecines with price rise by 13%
--Modify the above code to include the new price column with the name 'New Price'

-- NO SOLUTION IS EXPECTED FOR THIS QUESTION AS LONG AS '=' IS NOT USED FOR ALIASING IN MICROSOFT SQL SERVER


--EXAMPLE :
--Using built in function on a column in the SELECT list. Note the name of column is used as input for the function


--EXAMPLE : 
--Write a code to display the the first name and last name of all patients. Use table alias to refer the columns
	SELECT pFName,
		   pLName
	FROM Patient


--EXERCISE # 13 
--Assign the appropriate aliases for columns displayed on previous exercise. 
	SELECT pFName [FIRST NAME],
		   pLName [LAST NAME]
	FROM Patient

/*
Logical Processing Order on Aliases
	- FROM, WHERE, and HAVING clauses process before SELECT 
	- Aliases created in SELECT clauses only visible to ORDER BY clause
	- Expressions aliased in SELECT clause may be repeated elsewere in query
	
	5. SELECT [DISTINCT | ALL] 7.[TOP <value> [PERCENT]] <column list>
	1. FROM
	2. WHERE
	3. GROUP BY
	4. HAVING
	6. ORDER BY
*/

-- September 26, 2020

--EXAMPLE
--The following code will run free from error, since alias dicleration done before alias used by ORDER BY clause


--EXAMPLE
--The following code will err out, since alias decleration done after the execution of WHERE clause
	SELECT empId AS [Employee ID], 
		   empFName AS [First Name], 
		   empLName AS [Last Name]
	FROM Employee
	WHERE empFName LIKE 'R%'


/*
Simple CASE Expression
	- Can be used in SELECT, WHERE, HAVING AND ORDER BY clauses
	- Extends the ability of SELECT clause to manipulate data as it is retrived
	- CASE expression returns a scalar (single-valued) value based on conditional logic

	- In SELECT clause, CASE behaves as calculated column requiring an alias
	- Forms of CASE expressions
		- Simple CASE
			: Compares one value to list of possible values, returns first match
		- Searched CASE
			 : Evaluate a set of logical expressions & returns value found in THEN clause

CASE Statement Syntax:
	CASE <column name>
	WHEN <value one> THEN <output one>
	WHEN <value two> THEN <output two>
	.
	.
	.
	ELSE <default output>
	END <new alias>

*/

--EXAMPLE : 
--Simple CASE 
/* 
First
	ALTER TABLE Disease
	DROP COLUMN dCategory
*/
	SELECT * FROM Disease

	SELECT dId, dName,
			CASE dId
				WHEN 1 THEN 'Bacterial infections'
				WHEN 2 THEN 'Blood Diseases'
				WHEN 3 THEN 'Digestive Diseases'
				ELSE 'NO CATEGORY'
			END AS Category
	FROM Disease

--EXAMPLE : 
--Searched CASE


--EXERCISE # 14 
--Use simple CASE to list the brand names of medecines available as per Medecine Id
	SELECT * FROM Medicine

	SELECT mId, brandName,
			CASE mId
				WHEN 1 THEN 'Anticoagulant'
				WHEN 2 THEN 'ACE inhitor'
				WHEN 3 THEN 'treat certain types of bone loss in adults'
				WHEN 4 THEN 'Anti-depressent'
				WHEN 5 THEN 'Antibiotics'
				ELSE 'No Usage Available'
			END AS Usage
	FROM Medicine

--EXERCISE # 15 
--Searched CASE to identify label the price of a medicine cheap, moderate and expensive. Price of cheap ones
--is less than 15 USD, and expensive ones are above 45 USD. Display Medecine Id, Generic Name, the unit price and 
--category of the price.  

	SELECT * FROM Medicine

	SELECT mId, genericName,unitPrice,
			CASE 
				WHEN unitPrice >= 45.00 THEN 'Expensive'
				WHEN unitPrice >= 15.00 THEN 'Moderately Expensive'
				ELSE 'Cheap'
			END AS Price_Category
	FROM Medicine

	-- Display MRN, Full Name and Gender (as Female and Male) of all Patients -- use case statement for gender
	SELECT * FROM Patient

	SELECT mrn, CONCAT(pFName,' ',pLName) [FULL NAME],
			CASE
				WHEN gender = 'M' THEN 'MALE'
				WHEN gender = 'F' THEN 'FEMALE'
			END AS GENDER
	FROM Patient


/*
SORTING DATA
	- Used to desplay the output of a query in certain order
	- Sorts rows in results for presentation purpose
	- Uses ORDER BY clause
	- ORDER BY clause
		 - The last clause to be logically processed in SELECT statement
		 - Sorts all NULLs together
		 - Refers to columns by name, alias or ordinal position (not recommended)
		 - Declare sort order with ASC or DESC

	- SYNTAX :
			SELECT <select_list>
			FROM <table_source>
			ORDER BY <order_by_list> [ASC|DESC]
*/

-- Get Employees earning less that 75000.00
	SELECT empId [EMPLOYEE ID], 
		   CONCAT(empFName,' ',empLName) [FULL NAME],
		   salary [SALARY]
	FROM Employee
	WHERE salary > 75000.00
	ORDER BY salary

-- Get Patients not in DC Metro Area (not DC, MD, VA)
	SELECT empId [EMPLOYEE ID], 
		   CONCAT(empFName,' ',empLName) [FULL NAME],
		   [state] [STATE] 
	FROM Employee
	WHERE [state] NOT IN ('DC','MD','VA')
	ORDER BY empId


-- Get patients from FL, VA and TX
	SELECT empId [EMPLOYEE ID], 
		   CONCAT(empFName,' ',empLName) [FULL NAME],
		   [state] [STATE] 
	FROM Employee
	WHERE [state] IN ('FL','VA','TX')
	ORDER BY empId

-- Get patients Female Patients from FL, VA and TX
	SELECT mrn [PATIENT ID], 
		   CONCAT(pFName,' ',pLName) [FULL NAME], 
		   gender
	FROM Patient
	WHERE gender = 'F' AND [state] IN ('FL','VA','TX')
	ORDER BY [PATIENT ID]


--ORDER BY clause using column names
--EXAMPLE : Retrive details of Employees in assending order of their first names
	SELECT *
	FROM Employee
	ORDER BY empFName ASC

-- Also order by last name - and return the names as lastname, firstname
	SELECT empId [EMPLOYEE ID], 
		   CONCAT(empLName,' ',empFName) [FULL NAME ]
	FROM Employee
	ORDER BY empLName ASC

--EXERCISE # 16
--Write a code to retrive details of Patients in assending order of last names 
	SELECT mrn [PATIENT ID], 
		   CONCAT(pLName,' ',pFName) [FULL NAME]
	FROM Patient
	ORDER BY pLName ASC


--EXERCISE # 17 
--Write a code to retrive details of Employees in descending order of DoB
--Note : Use column alias on ORDER BY clause
	SELECT empId [EMPLOYEE ID], 
		   CONCAT(empLName,' ',empFName) [FULL NAME ], 
		   DoB [DATE OF BIRTH]
	FROM Employee
	ORDER BY [DATE OF BIRTH] DESC

--EXAMPLE :
--ORDER BY clause can uses more than one column

/* Some Date related built in functions
	GETDATE() -- Returns datetime
	YEAR() -- returns integer
	MONTH() -- returns integer
	DAY() -- returns integer

*/

--EXERCISE # 18
--Display the list of Patients in the order of the year of their treatement. Use their First name to order the ones that came to the hospital on same year. 
	SELECT mrn [EMPLOYEE ID], 
		   CONCAT(pLName,' ',pFName) [FULL NAME], 
		   YEAR(registeredDate) [TREATED YEAR]
	FROM Patient
	ORDER BY [TREATED YEAR] ASC


-- Return the year, month and day separately
	SELECT mrn [EMPLOYEE ID], 
		   CONCAT(pLName,' ',pFName) [FULL NAME], 
		   YEAR(registeredDate) [YEAR],
		   MONTH(registeredDate) [MONTH], 
		   DAY(registeredDate) [DAY]
	FROM Patient
	ORDER BY [YEAR] ASC

/*
FITERING DATA
	- Used to retrive only a subset of all rows stored in the table
	- WHERE clause is used to limit which rows to be retured
	- Data filtered server-side, can reduce network trafic and client memory usage

	-WHERE clause 
		- use predicates, expressed as logical conditions
		- rows which predicate evaluates to TRUE  will be accepted
		- rows of FALSE or UNKNOWNS filitered out
		- in terms of precidence, it follows FROM clause
    *** - can't see the alias declared in SELECT clause
		- can be optimized to use indexes
		 
	SYNTAX : (WHERE clause only )
			WHERE <search_condition>

		- PREDICATES and OPERATORS
			- IN : Determines whether a specified value matches any value in the subquery or list
			- BETWEEN : Specifies an inclusive range to test
			- LIKE : Determines whether a specific character string matches a specified pattern
			- AND : Combines two Boolean expressions and returns TRUE only when both are TRUE
			- OR : Combines two boolean expressions and returns TRUE if either is TRUE.
			- NOT : Reverses the result of a search condition. 

*/

--EXAMPLE :
--Find below a code to get Employee information (across all columns) - who are contractors,
--Note : P: Principal Associate, A: Associate, C: Contractor, M:Manager
	SELECT *
	FROM Employee
	WHERE empType = 'C'


--EXAMPLE :
--Find below a code to get Employee information (across all columns) -  for Contract and Associate
	SELECT *
	FROM Employee
	WHERE empType = 'C' AND empType = 'C'


--EXERCISE # 19
--Find the list of employees with salary above 90,000
	SELECT *
	FROM Employee
	WHERE salary > 90000.00
	ORDER BY salary

--EXERCISE # 20
--Find the list of employees with salary range betwen 85,000 to 90,000 inclusive
	SELECT *
	FROM Employee
	WHERE salary BETWEEN 85000.00 AND 90000.00
	ORDER BY salary


--EXERCISE # 21
--Find the list of patients that had registration to the hospital in the year 2018
--Use 'BETWEEN' operator
	SELECT *
	FROM Patient
	WHERE registeredDate BETWEEN '01-01-2018' AND '12-31-2018'

-- The above two queries are efficient ones
-- The query below will also return the same result - but considered inefficient (issue related to indexing)

--EXERCISE # 22
--Get Employees born in the year 1980 (from 1980-01-01 - 1980-12-31)using 'BETWEEN' operator
	SELECT *
	FROM Employee
	WHERE DoB BETWEEN '1980-01-01' AND '1980-12-31'


--EXERCISE # 23
--Get Employees born in the year 1980 using 'AND' operator
	SELECT *
	FROM Employee
	WHERE YEAR(DoB) = 1980
--We can also use the YEAR() function to do the same for the above exercise - but it is NOT recommended


--EXERCISE # 24
--Write a code that retrives details of Patients that live in Silver Spring.
	SELECT *
	FROM Patient
	WHERE city LIKE 'Silver Spring'


--EXERCISE # 25
--Write a code retrive the information of contractor Employees with salary less than 75000.
	SELECT *
	FROM Employee
	WHERE empType = 'C' AND salary > 75000.00

--EXERCISE # 26
--Write a code to retrive list of medicines with price below 30 USD.
	SELECT *
	FROM Medicine
	WHERE unitPrice < 30.00


--EXERCISE # 27
--Write a code to retrive patients that live in Miami and Seattle
	SELECT *
	FROM Patient
	WHERE city IN ('Miami','Seattle')

--EXERCISE # 28
--Write a code to retrive patients that are not living in Silver Spring
	SELECT *
	FROM Patient
	WHERE city NOT IN ('Silver Spring')

/*
STRING FUNCTIONS

	- CONCATENATE
		- Returns a character string that is the result of concatenating string_exp2 to string_exp1. 
		- SYNTAX
			CONCAT( string_exp1, string_exp2, ...)

	- LEFT
		- Returns the leftmost count characters of string_exp.
		- SYNTAX
			LEFT( string or string_exp, number_of_characters )
		
		Get three characters from the left of 'Richards'
			Ans: 'Ric'

	- RIGHT
		- To extract a substring from a string, starting from the right-most character.
		- SYNTAX
			RIGHT( string or string_exp, number_of_characters )
		
		Get three characters from the right of 'Richards'
			Ans: 'rds'

	- CHARINDEX
		- returns the location of a substring in a char/string, from the left side.
		- The search is NOT case-sensitive.
		- SYNTAX :
			CHARINDEX(substring, string, [start_position] )
			
		-> given a string and character CHARINDEX function returns the relative location (index) of the first occurrence of that character in the string

		e.g. thus the CHARINDEX OF 'g' IN String: 'Johansgurg' is 7 

	-LENGTH
		- Returns the number of characters in string_exp, excluding trailing blanks.
		- SYNTAX
			LEN( string or string_exp )
		e.g. LEN ('Complete') -> 8

	-LTRIM function
		- removes all space characters from the left-hand side of a string.
		- SYNTAX
			LTRIM( string_exp )
	- RTRIM
	- removes all space characters from the right-hand side of a string.
		- SYNTAX
			RTRIM( string_exp )
	- TRIM
	- removes all space characters from the both sides of a string.
		- SYNTAX
			TRIM( string_exp )

Check the following link for complete list of String Functions

	https://docs.microsoft.com/en-us/sql/odbc/reference/appendixes/string-functions?view=sql-server-2017

*/


--EXERCISE # 29
--Write a code to display full name for employees
	SELECT mrn [EMPLOYEE ID], 
		   CONCAT(pFName,' ',pLName) [FULL NAME]
	FROM Patient

--EXERCISE : 30
--Get the last four digists of SSN of all Employees together with their id and full name
	SELECT empId [EMPLOYEE ID], 
		   CONCAT(empFName,' ',empLName) [FULL NAME], 
		   RIGHT(SSN,CHARINDEX('-',SSN)) [LAST # OF SSN]
	FROM Employee

-- Q2 - Write a query to get the Employee with the last four digits of the SSN is '3456' and with DoB = '1980-09-07'
	SELECT empId [EMPLOYEE ID], 
		   CONCAT(empFName,' ',empLName) [FULL NAME],
		   DoB [DATE OF BIRTH], 
		   RIGHT(SSN,CHARINDEX('-',SSN)) [LAST # OF SSN]
	FROM Employee
	WHERE RIGHT(SSN,CHARINDEX('-',SSN)) = '3456' AND DoB = '1980-09-07'

--EXERCISE # 31
--Write a code to retrive the full name and area code of their phone number, use Employee table
	SELECT empId [EMPLOYEE ID], 
		   CONCAT(empFName,' ',empLName) [FULL NAME], 
		   LEFT(phoneNo,5) [AREA CODE]
	FROM Employee


--EXERCISE # 32
--Write a code to retrive the full name and area code of their phone number, (without a bracket). use Employee table
	SELECT empId [EMPLOYEE ID], 
		   CONCAT(empFName,' ',empLName) [FULL NAME], 
		   SUBSTRING(phoneNo,2,3) [AREA CODE w/t BRACES]
	FROM Employee

--EXAMPLE # 33
--Run the following codes and explain the result with the purpose of CHARINDEX function
	SELECT CHARINDEX('O', 'I love SQL')
--THE ABOVE QUERY WILL GIVE US THE MEMORY LOCATION OF A GIVEN STRING/CHARACTER

--EXERCISE # 34
--Modify the above code, so that the output/result will have appopriate column name
	SELECT CHARINDEX('O', 'I love SQL') AS [Index for letter 'O']


--EXAMPLE # 35
--Write a code that return the index for letter 'q' in the sentence 'I love sql'
	SELECT CHARINDEX('q', 'I love SQL') [Index for letter 'Q']

--EXERCISE # 36
--Use the CHARINDEX() function to retrieve the house(building) number of all our employees
	SELECT empId [EMPLOYEE ID], 
		   CONCAT(empFName,' ',empLName) [FULL NAME],
		   apt [APARTEMENT NUMBER]
	FROM Employee

--EXAMPLE : 
--Run the following code and explain the result with the purpose of LEN function
	SELECT LEN('I love SQL')
--THE LEN FUNCTION IS A COUNTING FUNCTION THAT COUNTS THE GIVEN STRING AND RETURNS THE COUNTS OF STRINGS IN THE INTEGER FORMAT

--EXAMPLE : 
--Reterive the email's domain name for the entiere employees. 
--NOTE : Use LEN(), CHARINDEX() and RIGHT()
	SELECT empId [EMPLOYEE ID], 
		   CONCAT(empFName,' ',empLName) [FULL NAME],
		   RIGHT(email,LEN(email) - CHARINDEX('@',email)) [DOMAIN NAME]
	FROM Employee


--EXERCISE # 37
--Assign a new email address for empId=EMP05 as 'sarah.Kathrin@aol.us'
	UPDATE Employee
	SET email = 'sarah.Kathrin@aol.us'
	WHERE empId = 'EMP05'

--EXERCISE # 38
--Using wildcards % and _ ('%' means any number of charactes while '_' means single character)
--mostly used in conditions (WHERE clause or HAVING clause)
--Get Employees whose first name begins with the letter 'P'
	SELECT *
	FROM Employee
	WHERE empFName LIKE 'P%'


--EXERCISE # 39
--Get the list of employees with 2nd letter of their first name is 'a'
	SELECT *
	FROM Employee
	WHERE empFName LIKE 'P%'


--EXERCISE # 40
--Get full name of employees with earning more than 75000. (Add salary information to result set)
	SELECT empId [EMPLOYEE ID], 
		   CONCAT(empFName,' ',empLName) [FULL NAME],
		   salary [SALARY]
	FROM Employee
	WHERE salary > 75000.00
	ORDER BY SALARY

--EXERCISE # 41
--Get Employees who have yahoo email account
--NOTE : the code retrives only the list of employee's with email account having 'yahoo.com'
	SELECT empId [EMPLOYEE ID], 
		   CONCAT(empFName,' ',empLName) [FULL NAME],
		   RIGHT(email, LEN(email) - CHARINDEX('@',email)) [YAHOO DOMAIN ACCOUNT USERS]
	FROM Employee
	WHERE email LIKE '%yahoo.com'


--EXERCISE # 42
--Get Employees who have yahoo email account
--NOTE : Use RIGHT string function. 
	SELECT empId [EMPLOYEE ID], 
		   CONCAT(empFName,' ',empLName) [FULL NAME],
		   RIGHT(email, LEN(email) - CHARINDEX('@',email)) [YAHOO DOMAIN ACCOUNT USERS]
	FROM Employee
	WHERE email LIKE '%yahoo.com'


--EXERCISE # 43
--Get Employees who have yahoo email account
--NOTE : The code must checke only 'yahoo' to retrive the entire employees with yahoo account
	SELECT empId [EMPLOYEE ID], 
		   CONCAT(empFName,' ',empLName) [FULL NAME],
		   RIGHT(email, LEN(email) - CHARINDEX('@',email)) [YAHOO DOMAIN ACCOUNT USERS]
	FROM Employee
	WHERE email LIKE '%yahoo%'


--EXERCISE # 44 
--Create a CHECK constraint on email column of Employee table to check if it's a valid email address
--NOTE : Assume a valid email address contains the '@' character
GO
ALTER TABLE Employee
ADD CONSTRAINT ck_email CHECK(email LIKE '%@%')

/*		Aggregate Functions
	
	COUNT() - returns the number of rows satisfying a given condition (conditions)
	AVG() - Returns the arithimetic mean (Average) on a set of numeric values
	SUM() - Returns the Sum of a set of numeric values
	MIN() - Returns the Minimum from a set of numeric values
	MAX() - Returns the Maximum from a set of numeric values
	STDEV() - Returns the Standard Deveiation of a set of numeric values
	VAR() - Returns the Variance of a set of numeric values
*/


--EXERCISE # 45
--Get total number of Employees
	SELECT COUNT(empId) [Number Of Employees]
	FROM Employee

--EXERCISE # 46
--Get number of Employees not from Maryland
	SELECT COUNT(*) [Num_Employees_Not_From_Maryland]
	FROM Employee
	WHERE city <> 'Mary Land'

--OR
	SELECT COUNT(*) [Num_Employees_Not_From_Maryland]
	FROM Employee
	WHERE city != 'Mary Land'



--EXERCISE # 47
--Get the number of Principal Employees, with emptype = 'P')
	SELECT COUNT(*) [Number_Of_Principal_Employees]
	FROM Employee
	WHERE empType = 'P'


--EXERCISE # 48
--Get the Minimum salary
	SELECT FORMAT(MIN(salary),'C','en-us') [MINIMUM SALARY OF EMPLOYEE]
	FROM Employee


--EXERCISE # 49
--Modify the above code to include the Minimum, Maximum, Average and Sum of the Salaries of all employees
	SELECT FORMAT(MIN(salary),'C','en-us') [MINIMUM SALARY OF EMPLOYEE],
		   FORMAT(MAX(salary),'C','en-us') [MAXIMUM SALARY OF EMPLOYEE],
		   FORMAT(CONVERT(DECIMAL(10,2),AVG(salary)),'C','en-us') [AVG SALARY OF EMPLOYEE],
		   FORMAT(SUM(salary),'C','en-us') [TOTAL SALARY OF EMPLOYEES]
	FROM Employee


--EXERCISE # 50
--Get Average Salary of Female Employees
	SELECT FORMAT(MIN(salary),'C','en-us') [MINIMUM SALARY OF EMPLOYEE],
		   FORMAT(MAX(salary),'C','en-us') [MAXIMUM SALARY OF EMPLOYEE],
		   FORMAT(CONVERT(DECIMAL(10,2),AVG(salary)),'C','en-us') [AVG SALARY OF EMPLOYEE],
		   FORMAT(SUM(salary),'C','en-us') [TOTAL SALARY OF EMPLOYEES]
	FROM Employee


--EXERCISE # 51
--Get Average Salary of Associate Employees (empType = 'A')
	SELECT FORMAT(CONVERT(DECIMAL(10,2),AVG(salary)),'C','en-us') [AVG SALARY OF ASSOCIATE EMPLOYEE]
	FROM Employee
	WHERE empType = 'A'

--EXERCISE # 52
--Get Average salaries for each type of employees?
GO	
	SELECT empType [EMPLYEE TYPE],
		   FORMAT(CONVERT(DECIMAL(10,2),AVG(salary)),'C','en-us') [AVG_SALARY_OF_EACH_TYPE_OF_EMPLOYEE]
	FROM Employee
	GROUP BY empType


--EXERCISE # 53
--Get Average Salary per gender
GO
	SELECT gender [GENDER TYPE],
		   FORMAT(CONVERT(DECIMAL(10,2),AVG(salary)),'C','en-us') [AVG SALARY PER GENDER]
	FROM Employee
	GROUP BY gender


--EXERCISE # 54
--Get Average Salary per sate of residence
GO	
	SELECT [state] [STATE],
		   FORMAT(CONVERT(DECIMAL(10,2),AVG(salary)),'C','en-us') [AVG SALARY PER STATE]
	FROM Employee
	GROUP BY [state]

--EXERCISE # 55
--Get Employees earning less than the average salary of all employees
GO	
	SELECT *
	FROM Employee
	WHERE Salary < (SELECT AVG(Salary) FROM Employee)


/*
NOTE : An aggregate may not appear in the WHERE clause unless it is in a subquery contained in a 
       HAVING clause or a SELECT list, and the column being aggregated is an outer reference.

SUBQUERIES
	- Is SELECT statement nested within another query, (queries within queries)
	- Can be Scalar, multi-valued, or table-valued
		- SCALAR SUBQUERY : return single value, outer queries handle only single result
		- MULTIVALUED : return a column table, outer queries must be able to handle multipe results
		 EXAMPLE:
			SELECT *
			FROM Books
			WHERE CONCAT('|', Authors, '|') LIKE '%|John Smith|%';
	- The result of inner query (subquery) are returned to the outer query
	- Enables to enhance ability to create effective queries
	- Can be either Self-Contained or Correlated
	- SELF-CONTAINED
		- have no dependency to outer query
	- CORRELATED
		- one or more column of subquery depends on the outer query.
		- Inner query receives input from the outer query & conceptually executes once per row in it.
		- Writing correlated subqueries
			- if inner query is scalar, use comparision opetators as '=', '<', '>', and '<>'in	WHERE clause
			- if inner query returns multi-values, use and 'IN' predicate 
			- plan to handle 'NULL' results as required.
		EXAMPLE:
			SELECT e.Name AS EmployeeName, d.Name AS DepartmentName, 
			(SELECT Name FROM Employees WHERE DepartmentID = e.DepartmentID ORDER BY Salary DESC LIMIT 1) AS HighestPaidEmployee
			FROM Employees e
			JOIN Departments d ON e.DepartmentID = d.ID;

*/


--EXERCISE # 58 
--Get list of Employees with earning less than the average salary of Associate Employees
--Note : Use a scalar subquery which is self contained to solve the problem
GO	
	SELECT *
	FROM Employee
	WHERE Salary < (SELECT AVG(Salary) FROM Employee WHERE empType = 'A')
	ORDER BY salary

--EXERCISE # 59 
--Get Principal Employees earning less than the average of Contractors
GO	
	SELECT *
	FROM Employee
	WHERE empType = 'P' AND Salary < (SELECT AVG(Salary) FROM Employee WHERE empType = 'C')
	ORDER BY salary


--EXERCISE # 60 
--Get Principal Employees earning less than or equal to the average salary of Pricipal Employees
GO	
	SELECT *
	FROM Employee
	WHERE empType = 'P' AND Salary <= (SELECT AVG(Salary) FROM Employee WHERE empType = 'P')


--EXERCISE # 61 
--Get Contractors earning less than or equal to the average salary of Contractors
GO	
	SELECT *
	FROM Employee
	WHERE empType = 'C' AND Salary <= (SELECT AVG(Salary) FROM Employee WHERE empType = 'C')


--EXERCISE # 62 
--Get Associate Employees earning less than or equal to the average salary of Associate Employees
GO	
	SELECT *
	FROM Employee
	WHERE empType = 'A' AND Salary <= (SELECT AVG(Salary) FROM Employee WHERE empType = 'A')


--EXERCISE # 63 
--Get Managers earning less than or equal to the average salary of Managers
GO	
	SELECT *
	FROM Employee
	WHERE empType = 'M' AND Salary <= (SELECT AVG(Salary) FROM Employee WHERE empType = 'M')


--EXERCISE # 64
--Get the count of Employees based on the year they were born
GO	
	SELECT YEAR(DoB) [BORN IN THE YEAR],
		   COUNT(*) [NUMBER OF EMPLOYEES]
	FROM Employee
	GROUP BY YEAR(DoB)

--EXERCISE # 65
--Get list of patients diagnoized by each doctor
--NOTE : Use multi-valued subquery to get the list of doctors from 'Doctors' table
--USING THIS REFERENCE
https://learn.microsoft.com/en-us/sql/t-sql/functions/string-agg-transact-sql?view=sql-server-ver16
--We need to use STRING_AGG() to aggregate patients name in comma separated values
GO
	SELECT d.docId, 
				(SELECT STRING_AGG(p.pFName,', ')   
						FROM Diagnosis s 
								JOIN Patient p ON s.mrn = p.mrn 
				 WHERE s.docId = d.docId 
				) AS diagnosedPatients 
	FROM Doctor d
	WHERE d.docId IN (SELECT docId FROM Doctor)
	GROUP BY d.docId

/*
Note : Here is the logical structure of the outer query for the above example
	   Subqueries that have multi-values will follow the same fashion
	
	SELECT docId,MRN,diagDate,diagResult
	FROM Diagnosis AS D
	WHERE D.docId IN ( MD01, MD02, MD03, MD04  )
	ORDER BY DOCID

*/
/*	EXAMPLE:
			SELECT *
			FROM Books
			WHERE CONCAT('|', Authors, '|') LIKE '%|John Smith|%';
*/



--EXERCISE # 67
--Get list of patients diagnoized for each disease type
--NOTE : Use multi-valued subquery to get the list of disease from 'Disease' table
GO	
	SELECT D.dId,D.dType [Disease Type], 
				(SELECT STRING_AGG(p.pFName,', ')   
						FROM Diagnosis Dg 
						JOIN Patient p ON Dg.mrn = p.mrn 
				 WHERE Dg.dId = D.dId 
				) AS diagnosedPatients 
	FROM Disease D
	WHERE D.dId IN (SELECT dId FROM Disease)
	GROUP BY D.dId,D.dType


--EXAMPLE :
--Get Employees who are earning less than or equal to the average salary of their gender
--NOTE : Use correlated subquery
GO	
	SELECT *
	FROM Employee e
	WHERE e.Salary <= (SELECT AVG(Salary) FROM Employee WHERE gender = e.gender)

--OR BELOW CODE WORKS BUT WE DON'T TO SPECIFIY THE VALUE AS LONG AS THEY'RE 2, WE CAN DO THIS IF THE gender COLUMN HAS ANOTHER VALUE 
GO	
	SELECT *
	FROM Employee
	WHERE Salary <= (SELECT AVG(Salary) FROM Employee WHERE gender IN ('M','F'))


--EXERCISE # 68 
--Retrieve all Employees earning less than or equal to their groups averages
--NOTE : Use correlated subquery, 
GO
	SELECT *
	FROM Employee E
	WHERE Salary <= (SELECT AVG(Salary) FROM Employee WHERE empType = E.empType )


--EXAMPLE
--Better way to deal with previous exercise is to use 'JOIN', run the following and confirm 
--try to analizye how the problem is sloved, the next section try to introduce about JOINing tables
-- FIRST WE NEED TO RETRIEVE AVG OF EACH GROUP THEN JOIN THEM WITH OUTER QUERY AND MAKE A COMPARISON
GO
	SELECT E.*
	FROM Employee E 
			JOIN (SELECT empType,AVG(salary) AVG_SALARY 
				  FROM Employee EE 
				  GROUP BY empType
				  ) EE ON E.empType = EE.empType 
	WHERE E.salary <= EE.AVG_SALARY

-- Create one table called - Department (depId, depName, depEstablishmentDate)
-- And also allocate our employees to the departments

GO
	CREATE TABLE Department 
	(
		depId CHAR(4) PRIMARY KEY NOT NULL, 
		depName VARCHAR(40) NOT NULL, 
		depEstablishmentDate DATE
	);
GO 
	INSERT INTO Department VALUES	('KB10', 'Finance', '2010-10-10'), ('VL20', 'Marketing', '2010-01-10'), 
									('HN02', 'Medicine', '2010-01-10'), ('AK12', 'Information Technology', '2015-01-01')

GO
	ALTER TABLE Employee
	ADD department CHAR(4) FOREIGN KEY REFERENCES Department(depId)
GO
	UPDATE Employee
	SET department = 'HN02'
	WHERE empId IN ('EMP10','EMP12','EMP04','EMP17','EMP15')

	UPDATE Employee
	SET department = 'KB10'
	WHERE empId IN ('EMP01', 'EMP02', 'EMP03')

	UPDATE Employee
	SET department = 'VL20'
	WHERE empId IN
	('EMP05',
	'EMP06',
	'EMP07',
	'EMP08',
	'EMP09');
GO
	UPDATE Employee
	SET department = 'AK12'
	WHERE department IS NULL

	SELECT * FROM Employee

--EXERCISE # 56
--Note : The answer for the this exercise will be used as subquery to the next question )
--Get the average salary of all employees
	SELECT AVG(salary) [AVG_SALARY]
	FROM Employee

GO
--EXERCISE # 56.1
-- Get the average salary of employees in each department
	SELECT D.depId,D.depName, FORMAT(CONVERT(DECIMAL(10,2),EE.AVG_SALARY),'C','en-us') AVERAGE_SALARY
	FROM Department D 
				JOIN (SELECT department,AVG(salary) [AVG_SALARY] 
					  FROM Employee
					  GROUP BY department
					  ) EE ON D.depId = EE.department


--EXERCISE # 57
--Get the list of employees with salary less than the average employee's salary
--Note : Use a scalar subquery which is self contained to solve the problem
GO	
	SELECT *
	FROM Employee E
	WHERE E.salary < (SELECT AVG(salary) [AVG_SALARY] FROM Employee)

-- EXERCISE 57 -1
--- Get Employees earning less than or equal to the average salary of thier corresponding departments
--- We do this in diferent ways
--- Let's use Correlated sub-query
--- First let's do this one by one
--- Q1: Get Employees working in department 'AK12' and earning less than or equal to the departmental average salary
GO	
		SELECT *
		FROM Employee E
		WHERE E.department = 'AK12' AND Salary <= (SELECT AVG(Salary) FROM Employee EE WHERE EE.department = 'AK12')

--- Q2: Get Employees working in department 'HN02' and earning less than or equal to the departmental average salary
GO		
		SELECT *
		FROM Employee E
		WHERE E.department = 'HN02' AND Salary <= (SELECT AVG(Salary) FROM Employee EE WHERE EE.department = 'HN02')

--- Q3: Get Employees working in department 'KB10' and earning less than or equal to the departmental average salary
GO
		SELECT *
		FROM Employee E
		WHERE E.department = 'KB10' AND Salary <= (SELECT AVG(Salary) FROM Employee EE WHERE EE.department = 'KB10')

--- Q4: Get Employees working in department 'VL20' and earning less than or equal to the departmental average salary
GO
		SELECT *
		FROM Employee E
		WHERE E.department = 'VL20' AND Salary <= (SELECT AVG(Salary) FROM Employee EE WHERE EE.department = 'VL20')

--- The question is to create one query that returns all the above together
GO	
	    SELECT *
		FROM Employee E
		WHERE E.salary <= (SELECT AVG(Salary) FROM Employee EE WHERE E.department = EE.department)

/*
Using 'EXISTS' Predicate with subqueries
	- It evaluates whether rows exist, but rather than return them, it returns 'TRUE' or 'FALSE'
	- Useful technique for validating data without incurring the overhead of retriving and counting the results
	- Database engine optimize execution for query having this form
*/

--EXAMPLE
--The following code uses 'EXIST' predicate to display the list of doctors that diagnose a patient
GO
	SELECT docId [DOCTOR ID],[rank] [STATUS],specialization [SPECIALIZATION]
	FROM Doctor D
	WHERE EXISTS (
		SELECT 1
		FROM Diagnosis DG
		JOIN Patient P ON P.mrn = DG.mrn
		WHERE DG.docId = D.docId
	)

--EXERCISE # 69
--Modify the above code to display list of doctor/s that had NEVER diagnosed a patient
GO
	SELECT docId [DOCTOR ID],[rank] [STATUS],specialization [SPECIALIZATION]
	FROM Doctor D
	WHERE NOT EXISTS (
		SELECT 1
		FROM Diagnosis DG
		RIGHT JOIN Patient P ON P.mrn = DG.mrn
		WHERE DG.docId = D.docId
	)

--EXERCISE # 70
--Write a code that display the list of medicines which are NOT prescribed to patients
GO
	SELECT M.*
	FROM Medicine M
	LEFT JOIN MedicinePrescribed MP ON M.mId = MP.mId
	LEFT JOIN Prescription P ON MP.prescriptionId = P.prescriptionId
	WHERE P.prescriptionId IS NULL

--EXERCISE # 71
--Write a code that display the list of medicines which are prescribed to patients
GO
	SELECT M.* 
	FROM Medicine M 
	LEFT JOIN MedicinePrescribed MP ON M.mId = MP.mId
	LEFT JOIN Prescription P ON P.prescriptionId=MP.prescriptionId
	WHERE P.prescriptionId IS NOT NULL


/************ Working with Multiple Tables
 
 Usually it is required to query for data stored in multiple locations,
 Creates intermediate virtual tables that will be consumed by subsequent phases or the query

	- FROM clause determines source of table/s to be used in SELECT statement
	- FROM clause can contain tables and operators
*** - Resulst set of FROM clause is virtual table
		: Subsequent logical operations in SELECT statement consume this virtual table
	- FROM clause can establish table aliases for use by subsequent pharses of query
	
	- JOIN : is a means for combining columns from one (self-join) or more tables by using values 
	         common to each.
	
	- Types of JOINs
		 CROSS JOIN : 
			: Combines all rows in both tables (Creates Carticial product)
		 INNER JOIN ( JOIN )
			: Starts with Cartetian product, and applies filter to match rows 
			  between tables based on predicate
			: MOST COMMONLY USED TO SOLVE BUSINESS PROBLEMS.
		 OUTER JOIN
			: Starts with Cartitian product, all rows from designated table preserved,
			  matching rows from other table retrived, Additional NULL's inserted as
			  place holders

			: Types of OUTER JOIN
				: LEFT OUTER JOIN ( LEFT JOIN )
					- All rows of LEFT table is preserved, 
				: RIGHT OUTER JOIN ( RIGHT JOIN )
					- All rows of RIGHT table is preserved, 
				: FULL OUTER JOIN ( FULL JOIN )

*/


--For demonstration purpose, run the following codes that creates two tables, T1 and T2
--After inserting some data on each tables view the cross product, 'CROSS JOIN'  of the two tables
GO
	CREATE TABLE T1 
		( A CHAR(1),
		  B CHAR(1),
		  C CHAR(1)
		 );

GO
	CREATE TABLE T2 
		( A CHAR(1), 
			Y CHAR(1), 
			Z CHAR(1)
			);

GO
	INSERT INTO T1 
		VALUES ('a','b','c'), 
				('d','e','f'), 
				('g','h','i');
GO
	INSERT INTO T2 
		VALUES ('a','m','n'),
			   ('X','Y','Z'), 
			   ('d','x','f');

--First see the content of each table one by one
GO	
	SELECT * FROM T1
	SELECT * FROM T2

--Now get the Cross Product (CROSS JOIN) of T1 and T2
GO  
  SELECT *
  FROM T1 CROSS JOIN T2

--Execute the following to get LEFT OUTER JOIN b/n T1 and T2 with condition columns 'A'
--on both tables have same value
GO  
  SELECT *
  FROM T1 LEFT JOIN T2
  ON T1.A = T2.A

--Execute the following to get RIGHT OUTER JOIN b/n T1 and T2 with condition columns 'A'
--on both tables have same value, Notice the difference with LEFT OUTER JOIN
GO
  SELECT *
  FROM T1 RIGHT JOIN T2
  ON T1.A = T2.A

--Execute the following to get FULL OUTER JOIN b/n T1 and T2 with condition columns 'A'
--on both tables have same value, Again notice the difference with LEFT/RIGHT OUTER JOINs
GO
  SELECT *
  FROM T1 FULL JOIN T2
  ON T1.A = T2.A


--EXERCISE # 72
--Get the CROSS JOIN of Patient and Diagnosis tables
GO
  SELECT *
  FROM Patient P 
  CROSS JOIN Diagnosis D

--EXERCISE # 73
--Get the information of a patient along with its diagnosis. 
--NOTE : First CROSS JOIN Patient and Diagnosis tables, and retrieve only the ones that share the same 'mrn on both tables
GO
  SELECT *
  FROM Patient P 
  CROSS JOIN Diagnosis D
  WHERE P.mrn = D.mrn

--EXERCISE # 74
-- Retrive MRN, Full Name, Diagnosed Date, Disease Id, Result and Doctor Id for Patient, MRN = 'PA002'
GO
  SELECT P.mrn,
		 CONCAT(P.pFName,' ',P.pLName) [FULL NAME], 
		 D.diagDate [DIAGNOSIS DATE],
		 D.dId [DISEASE ID],
		 D.diagResult [DIAGNOSIS RESULT],
		 D.docId [DOCTOR ID]
  FROM Patient P 
  CROSS JOIN Diagnosis D
  WHERE P.mrn = 'PA017'

--EXAMPLE :
--LEFT OUTER JOIN : Returns all rows form the first table, only matches from second table. 
--It assignes 'NULL' on second table that has no matching with first table

--EXERCISE :
--List employees that are NOT doctors by profession
--NOTE : Use LEFT OUTER JOIN as required
GO
  SELECT E.empId [EMPLOYEE ID],
		 CONCAT(E.empFName,' ',E.empLName) [FULL NAME], 
		 E.department [DEPARTEMENT]
  FROM Employee E 
  LEFT JOIN Doctor D ON E.empId = D.empId
  WHERE D.empId IS NULL

--EXAMPLE : 
--RIGHT OUTER JOIN : Returns all rows form the second table, only matches from first table. 
--It assignes 'NULL' on second table that has no matching with second table
--The following query displays the list of doctors that are NOT employees to the hospital

--Obviously all Doctors are employees, hence the result has NO instance.
GO
  SELECT *
  FROM Doctor D 
  RIGHT JOIN Employee E ON D.empId = E.empId
  WHERE E.empId IS NULL
  --ORDER BY D.docId

--EXAMPLE : The following query displays the list of doctors that had NEVER diagnosed a patient
GO
	SELECT *
	FROM Doctor D
	RIGHT JOIN Diagnosis DG ON D.docId = DG.docId
	--GROUP BY D.docId
	WHERE DG.docId IS NULL
	
--EXERCISE # 75
--Display the list of medicines that are prescribed by any of the doctor. (Use RIGHT OUTER JOIN)
GO
	SELECT  M.mId, 
			M.brandName, 
			D.docId, 
			MP.dosage
	FROM Medicine M
	RIGHT JOIN MedicinePrescribed MP ON M.mId = MP.mId 
	INNER JOIN Prescription P ON P.prescriptionId = MP.prescriptionId 
	INNER JOIN Diagnosis D ON D.diagnosisNo = P.diagnosisNo

/* -- THIS CAN BE DONE WITH INNER JOIN TOO ---
SELECT M.mId, M.brandName
FROM Medicine M
INNER JOIN MedicinePrescribed MP ON M.mId = MP.mId
INNER JOIN Prescription P ON MP.prescriptionId = P.prescriptionId
INNER JOIN Diagnosis D ON P.diagnosisNo = D.diagnosisNo
*/

--EXERCISE # 76
--Display the list of medicines that which NOT prescribed by any of the doctors. (Use RIGHT OUTER JOIN)
GO
	SELECT  M.mId, 
			M.brandName, 
			D.docId, 
			MP.dosage
	FROM Medicine M
	RIGHT JOIN MedicinePrescribed MP ON M.mId = MP.mId 
	INNER JOIN Prescription P ON P.prescriptionId = MP.prescriptionId 
	INNER JOIN Diagnosis D ON D.diagnosisNo = P.diagnosisNo
	WHERE P.prescriptionId IS NULL

--EXERCISE # 77 
--Get Patients with their diagnosis information: MRN, Full Name, Insurance Id, Diagnosed Date, Disease Id and Doctor Id
--You can get this information from Patient and Diagnosis tables
GO
  SELECT P.mrn,
		 CONCAT(P.pFName,' ',P.pLName) [FULL NAME],
		 P.insuranceId [INSURANCE ID], 
		 D.diagDate [DIAGNOSIS DATE],
		 D.dId [DISEASE ID],
		 D.docId [DOCTOR ID]
  FROM Patient P JOIN Diagnosis D
  ON P.mrn = D.mrn

--EXERCISE # 78 
--Get Doctors who have ever diagonosed a patient(s) with the diagnosis date, mrn 
--and Disease Id and result of the patient who is diagnosed
--The result should include Doctor Id, Specialization, Diagnosis Date, mrn of 
--the patient, Disease Id, Result
GO
  SELECT D.docId [DOCTOR ID],
		 D.specialization [SPECIALIZATION], 
		 DG.diagDate [DIAGNOSIS DATE],
		 DG.mrn,DG.dId [DISEASE ID],
		 DG.diagResult [DIAGNOSIS RESULT]
  FROM Doctor D JOIN Diagnosis DG
  ON D.docId = DG.docId

--EXERCISE # 79
--Add the Full Name of the Doctors to the above query.
--HINT : Join Employee table with the existing table formed by joining Doctor & Diagnosis tables on previous exercise
GO
  SELECT CONCAT(E.empFName,' ',E.empLName) [FULL DOCTOR NAME],
		 D.docId [DOCTOR ID],
		 D.specialization [SPECIALIZATION], 
		 DG.diagDate [DIAGNOSIS DATE],
		 DG.mrn,
		 DG.dId [DISEASE ID],
		 DG.diagResult [DIAGNOSIS RESULT]
  FROM Doctor D 
  JOIN Diagnosis DG ON D.docId = DG.docId 
  JOIN Employee E ON E.empId = D.empId


--EXERCISE # 80
--Add the Full Name of the Patients to the above query.
GO
  SELECT CONCAT(E.empFName,' ',E.empLName) [FULL DOCTOR NAME],
		 D.docId [DOCTOR ID],D.specialization [SPECIALIZATION], 
		 DG.diagDate [DIAGNOSIS DATE],
		 DG.mrn, 
		 CONCAT(P.pFName,' ',P.pLName) [FULL PATIENT NAME],
		 DG.dId [DISEASE ID],
		 DG.diagResult [DIAGNOSIS RESULT]
  FROM Doctor D 
  JOIN Diagnosis DG ON D.docId = DG.docId 
  JOIN Employee E ON E.empId = D.empId
  JOIN Patient P ON P.mrn = DG.mrn


--EXERCISE # 81
--Add the Disease Name to the above query
GO
  SELECT CONCAT(E.empFName,' ',E.empLName) [FULL DOCTOR NAME],
		D.docId [DOCTOR ID],D.specialization [SPECIALIZATION],
		DG.diagDate [DIAGNOSIS DATE],
		DG.mrn, 
		CONCAT(P.pFName,' ',P.pLName) [FULL PATIENT NAME],
		DG.dId [DISEASE ID],
		DS.dName [DISEASE NAME,
		DG.diagResult [DIAGNOSIS RESULT]
  FROM Doctor D 
  JOIN Diagnosis DG ON D.docId = DG.docId 
  JOIN Employee E ON E.empId = D.empId
  JOIN Patient P ON P.mrn = DG.mrn
  JOIN Disease DS ON DS.dId = DG.dId

--EXERCISE # 82
--Join tables as required and retrive PresciptionId, DiagnosisId, PrescriptionDate, MedicineId and Dosage
GO
  SELECT pr.prescriptionId,
		 pr.diagnosisNo,
		 pr.prescriptionDate,
		 mp.mId,mp.dosage
  FROM Prescription pr
  JOIN MedicinePrescribed mp ON pr.prescriptionId = mp.prescriptionId


--EXERCISE # 83
--Retrive PresciptionId, DiagnosisId, PrescriptionDate, MedicineId, Dosage and Medicine Name
GO
  SELECT pr.prescriptionId,
		 pr.diagnosisNo,
		 pr.prescriptionDate,
		 mp.dosage,
		 md.brandName
  FROM Prescription pr
  JOIN MedicinePrescribed mp ON pr.prescriptionId = mp.prescriptionId
  JOIN Medicine md ON md.mId = mp.mId

--EXERCISE # 84
-- Get the MRN, Full Name and Number of times each Patient is Diagnosed
GO
  SELECT P.mrn, 
		 CONCAT(P.pFName,' ',P.pLName) [FULL NAME], 
		 COUNT(DS.mrn) DIAGNOSED_TIMES
  FROM Patient P 
  JOIN Diagnosis DS ON P.mrn = DS.mrn
  GROUP BY P.mrn, CONCAT(P.pFName,' ',P.pLName)
  ORDER BY P.mrn

--EXERCISE # 85
--Get Full Name and number of times every Doctor Diagnosed Patients
GO
	SELECT DG.docId,
			CONCAT(E.empFName,' ',E.empLName) [FULL NAME],
		   COUNT(DG.mrn) [DIAGNOSED TIMES]
	FROM Diagnosis DG
	JOIN Doctor D ON DG.docId = D.docId
	JOIN Employee E ON E.empId = D.empId
	GROUP BY DG.docId,CONCAT(E.empFName,' ',E.empLName)
	ORDER BY DG.docId


--EXERCISE # 86
--Patient diagnosis and prescribed Medicine Information 
--MRN, Patient Full Name, Medicine Name, Prescibed Date and Doctor's Full Name
GO
	SELECT P.mrn, 
		   CONCAT(P.pFName,' ',P.pLName) [PATIENTS FULL NAME], 
		   M.brandName, 
		   PR.prescriptionDate, 
		   CONCAT(E.empFName,' ',E.empLName) [DOCTORS FULL NAME]
	FROM Patient P 
	JOIN Diagnosis DG ON P.mrn = DG.mrn
	JOIN Prescription PR ON PR.diagnosisNo = DG.diagnosisNo
	JOIN MedicinePrescribed MP ON MP.mId = PR.prescriptionId
	JOIN Medicine M ON M.mId = MP.mId
	JOIN Doctor D ON D.docId = DG.docId
	JOIN Employee E ON E.empId = D.empId


/*
OR 

SELECT	DG.mrn [MRN], CONCAT(P.pFName,' ',P.pLName) [Pateint Full Name], M.brandName [Medicine Name], PR.prescriptionDate,
		CONCAT(E.empFName,' ',E.empLName) [Doctor Full Name]
		FROM Doctor DR 
		JOIN Diagnosis DG ON DR.docId = DG.docId 
		JOIN Employee E ON DR.empId = E.empId 
		JOIN Patient P ON DG.mrn = P.mrn 
		JOIN Prescription PR ON DG.diagnosisNo = PR.diagnosisNo 
		JOIN MedicinePrescribed MP ON PR.prescriptionId = MP.prescriptionId 
		JOIN Medicine M ON MP.mId = M.mId
*/




--********** SOME JOIN EXERCISES ***************

--	Write Queries for the following

	-- 1- Get Patients' information: MRN, Patient Full Name, and Diagnosed Date of those diagnosed for disease with dId = 3
		--(Use filter in Where clause in addition to Joining tables Patient and Diagnosis)

			SELECT P.mrn, 
				   CONCAT(P.pFName,' ',P.pLName) AS [PATIENT NAME],
				   D.diagDate
			FROM PATIENT AS P 
			JOIN DIAGNOSIS AS D ON P.MRN = D.MRN
			WHERE D.DID=3


	-- 2- Get the Employee Id, Full Name and Specializations for All Doctors
		
			SELECT CONCAT(E.empFName,' ',E.empLName) AS [EMPLOYEE NAME], 
				   E.empId, 
				   D.Specialization
			FROM Employee AS E 
			JOIN Doctor AS D on e.empId=D.empId

	-- 3- Get Disease Ids (dId) and the number of times Patients are diagnosed for those diseases
	    --(Use only Diagnosis table for this)
			-- Can you put in the order of (highest to lowest) based on the number of times people diagnosed for the disease?
			-- Can you get the top most prevalent disease?

			SELECT did, count(did)
			FROM Diagnosis
			GROUP BY did
			ORDER BY count(did)

			SELECT TOP (2) did, count(did)
			FROM Diagnosis
			GROUP BY did
			ORDER BY count(did) DESC


	-- 4- Get Medicines (mId) and the number of times they are prescribed. 
		--(Use only the MedicinePrescribed table)
		--Also get the mId of medicine that is Prescribed the most

			SELECT mId, count(mId)
			FROM MedicinePrescribed
			GROUP BY mId
			ORDER BY count(mId)

			--Medicine that is prescribed the most
			SELECT TOP (2) mId, count(mId)
			FROM MedicinePrescribed
			GROUP BY mId
			ORDER BY count(mId) desc


	-- 5- Can you add the name of the medicines the above query (question 4)? 
		--(Join MedicinePrescribed and Medicine tables for this)

			SELECT MP.mId, 
				   COUNT(MP.mId), 
				   M.BrandName, 
				   M.genericName
			FROM MedicinePrescribed AS MP JOIN Medicine as M ON MP.mId=M.mId
			GROUP BY MP.mId,m.BrandName, M.genericName
			ORDER BY COUNT(MP.mId)


	-- 6- Alter the table PharmacyPersonel and Add a column ppId - which is a primary key. You may use INT as a data type
			
			--Add the new column ppId with the INT data type (NOTE: WE CAN'T ADD THE TABLE WITHOUT INSERTING DATA (IDENTITY)
				ALTER TABLE PharmacyPersonel
				ADD ppId INT IDENTITY(1,1) PRIMARY KEY NOT NULL;

				SELECT * FROM PharmacyPersonel 

	-- 7- Create one table called MedicineDispense with the following properties
		/*  MedicineDispense(
							dispenseNo - pk, 
							presciptionId and mId - together fk
							dispensedDate - defaults to today
							ppId - foreign key referencing the ppId of PharmacyPersonnel table
						)
		*/

			CREATE TABLE MedicineDispense
			 (
				 dispenseNo INT NOT NULL,
				 prescriptionId INT NOT NULL,
				 mId SMALLINT NOT NULL,
				 dispensedDate DATE DEFAULT GETDATE(),
				 ppId INT NOT NULL,
				 CONSTRAINT PK_MedicineDispense_dispenseNo PRIMARY KEY (dispenseNo),
				 CONSTRAINT FK_MedicineDispense_presciptionId_mId FOREIGN KEY (prescriptionId,mId) REFERENCES MedicinePrescribed (prescriptionId,mId),
				 CONSTRAINT FK_MedicineDispense_ppId FOREIGN KEY (ppId) REFERENCES PharmacyPersonel(ppId)		
			  )

			SELECT * FROM MedicineDispense
			
	-- 8- Add four Pharmacy Personnels (add four rows of data to the PharmacyPersonnel table) 
		-- Remember PharmacyPersonnel are Employees and every row you insert into the PharmacyPersonnel table should each reference one Employee from Employee table
		   INSERT INTO PharmacyPersonel (empId, pharmacistLisenceNo,licenseDate, PCATTestResult, [level])
				VALUES ('EMP25','ET-039','2018-02-06', 87, 'Out Patient'),
					   ('EMP26','PI-621','2019-11-12',  72, 'In Patient'),
					   ('EMP27','RF-452','2016-04-13',  90, 'Store Manager'),
					   ('EMP28','US-666', '2014-06-19', 67, 'Duty Manager')
			
			SELECT * FROM PharmacyPersonel
			SELECT * FROM Employee

	-- 9- Add six MedicineDispense data
		/* 
		NB: This insert had an error while am inserting and gave me the below error. So, to fix the error i tried to feed all combination of  (prescriptionId,mId) datas from 'MedicinePrescribed' table. The error will raise if doesn't have a combination.
		
		ERROR:
		The INSERT statement conflicted with the FOREIGN KEY constraint "FK_MedicineDispense_presciptionId_mId". The conflict occurred in database "HRMSDB", table "dbo.MedicinePrescribed".
		*/
		INSERT INTO MedicineDispense (dispenseNo, prescriptionId, mId, dispensedDate, ppId)
						VALUES (1, 22,  4, '2021-03-11', 4),
							   (2, 23,  7, '2019-09-21', 5),
							   (3, 24,  8, '2018-08-26', 7),
							   (4, 25, 13, '2021-04-04', 8),
							   (5, 26,  5, '2022-03-23', 9),
							   (6, 27, 14, '2022-09-28', 10)

			SELECT * FROM MedicineDispense
			SELECT * FROM dbo.MedicinePrescribed

					   
/*
	SET OPERATIONS :
					-UNION
					-UNION ALL
					-INTERSECT
					-EXCEPT
		UNION/UNION ALL are used to put together compatible result sets; .i.e. same number of columns 
		and corresponding columns have compatible data types
		The main difference between UNION/UNION ALL is that;
		UNION: returns only unique records from both result sets
		UNION ALL: keeps all records, including duplicates from both result sets
		INTERSECT: applied on compatible result sets, intersect returns records common on both
		EXCEPT: given as result set A EXCEPT result set B, returns records in result set A but not in B

*/


-- Set Operations on Result Sets
-- To Exemplify this in more detail, let's create below two tables

		CREATE TABLE HotelCust
		(
			fName VARCHAR(20),
			lName VARCHAR(20),
			SSN CHAR(11),
			DoB DATE
		);
		GO

		CREATE TABLE RentalCust
		(
			firstName VARCHAR(20),
			lastName VARCHAR(20),
			social CHAR(11),
			DoB DATE,
			phoneNo CHAR(12)
		);
GO

--TRUNCATE TABLE RentalCust
GO	
		INSERT INTO HotelCust 
			VALUES	('Dennis', 'Gogo', '123-45-6789', '2000-01-01'), 
					('Belew', 'Haile', '210-45-6789', '1980-09-10'),
					('Nathan', 'Kasu', '302-45-6700', '1989-02-01'), 
					('Kumar', 'Sachet', '318-45-3489', '1987-09-20'),
					('Mahder', 'Nega', '123-02-0089', '2002-01-05'), 
					('Fiker', 'Johnson', '255-22-6033', '1978-05-10'),
					('Alemu', 'Tesema', '240-29-6035', '1982-05-16')

GO
		INSERT INTO RentalCust 
			VALUES	('Ujulu', 'Obang', '000-48-6789', '2001-01-01','908-234-0987'), 
					('Belew', 'Haile', '210-45-6789', '1980-09-10', '571-098-2312'),
					('Janet', 'Caleb', '903-00-4700', '1977-02-01', '204-123-0987'), 
					('Kumar', 'Sachet', '318-45-3489', '1987-09-20', '555-666-7788'),
					('Mahder', 'Nega', '123-02-0089', '2002-01-05', '301-678-9087'),
					('John', 'Miller', '792-02-0789', '2005-10-25', '436-678-4567')




--To use UNION, the two tables must be UNION compatable
--EXAMPLE : Execute the following and explain the result, 

--EXERCISE # 85 
--Correct the above code and use 'UNION' operator to get the list of all customers in HotelCustomrs and RentalCustomer 
-- the UNION will only list VALUES without DUPLICATION. 
GO
	SELECT fName,lName,DoB
	FROM HotelCust 
	
	UNION
	
	SELECT firstName, lastName, DoB
	FROM RentalCust


--EXERCISE # 86 
--Use UNION ALL operator instead of UNION and explain the differece on the result/output
--We can use JOIN ALL to list datas with DUPLICATED VALUES.
GO
	SELECT fName,lName,DoB
	FROM HotelCust 
	
	UNION ALL
	
	SELECT firstName, lastName, DoB
	FROM RentalCust
 

--EXERCISE # 87 
--Get list of customers in both Hotel and Rental Customers ( INTERSECT )
-- lists only the one WHO has a DUPLICATE VALUES. in other terms customers who uses Rental and Hotel 
GO
	SELECT fName,lName,DoB
	FROM HotelCust 
	
	INTERSECT
	
	SELECT firstName, lastName, DoB
	FROM RentalCust

--EXERCISE # 88 
--Get list of customers who are Hotel Customers but not Rental ( EXCEPT )
GO
	SELECT fName,lName,DoB
	FROM HotelCust 
	
	EXCEPT
	
	SELECT firstName, lastName, DoB
	FROM RentalCust


--EXERCISE # 89
--Get list of customers who are Rental Customers but not Hotel  (EXCEPT )
GO	
	SELECT firstName, lastName, DoB
	FROM RentalCust

	EXCEPT 

	SELECT fName,lName,DoB
	FROM  HotelCust
	

/*********************   STORED PROCEDURES  **************************************
	
- STORED PROCEDURES (Procedure or Proc for short) are named database 
	- Are collections of TSQL statement stored in a database
	- Can return results, manipulate data, and perform adminstrative actions on server
	- Stored procedures can include
		- Insert / Update / Delete
	- objects that encapsualte T-SQL Code (DDL, DML, DQL, DCL)
	- Can be called with EXECUTE (EXEC) to run the encapsulated code
	- Can accept parameters
	- Used as interface layer between a database and application.

	- Used for retrival / insertion / updating and deleting with complex validation
		: 	Views and Table Valued Functions are used for simple retrival
		
	SYNTAX : 
		CREATE PROC <proc name> 
		[optional Parameter list] 
		AS 
			<t-sql code>
		
		GO;

*/

--NOTE : use 'usp' as prefix for new procedures, to mean ' User created Stored Procedure'

--EXAMPLE # 01 
--Write a code that displays the list of patients and the dates they were diagnosed
GO	 
			SELECT P.mrn [PATIENT ID], 
				   CONCAT(P.pFName,' ',P.pLName) [FULL NAME], 
				   D.diagDate [DIAGNOSED DATE]
			FROM Patient P 
			JOIN
			Diagnosis D ON P.mrn = D.mrn
	
--EXAMPLE # 02
--Customize the above code to creates a stored proc to gets the same result
GO
	DROP PROC IF EXISTS usp_Display_Patients_Diagnosed_Dates;
GO
	CREATE PROC usp_Display_Patients_Diagnosed_Dates
		AS 
			SELECT P.mrn [PATIENT ID], 
				   CONCAT(P.pFName,' ',P.pLName) [FULL NAME], 
				   D.diagDate [DIAGNOSED DATE]
			FROM Patient P 
			JOIN
			Diagnosis D ON P.mrn = D.mrn
	
--EXAMPLE # 03
--Execute the newly created stored procedure, using EXEC
GO
	EXEC usp_Display_Patients_Diagnosed_Dates

--EXAMPLE # 04
--Modify the above procedure to disply patients that was diagnosed in the year 2018
GO
	DROP PROC IF EXISTS usp_Display_Patients_Diagnosed_Year;
GO
	CREATE PROC usp_Display_Patients_Diagnosed_Year
		AS 
			SELECT P.mrn [PATIENT ID], 
				   CONCAT(P.pFName,' ',P.pLName) [FULL NAME], 
				   D.diagDate [DIAGNOSED DATE]
			FROM Patient P 
			JOIN
			Diagnosis D ON P.mrn = D.mrn
			WHERE YEAR(D.diagDate) = 2018
GO	
	EXEC usp_Display_Patients_Diagnosed_Year

--EXAMPLE # 05
--Drop the procedure created in the above example
GO	
	DROP PROC IF EXISTS usp_Display_Patients_Diagnosed_Year;


--EXAMPLE # 06 [ Procedure with parameter/s ]
--Create a proc that returns Doctors who diagnosed Patients in a given year
GO
	DROP PROC IF EXISTS usp_Doctors_Diagnosed_Patients_Year;
GO
	CREATE PROC usp_Doctors_Diagnosed_Patients_Year(@year INT)
	AS	
			SELECT P.mrn [PATIENT ID], 
				   CONCAT(P.pFName,' ',P.pLName) [FULL NAME], 
				   D.diagDate [DIAGNOSED DATE]
			FROM Patient P 
			JOIN
			Diagnosis D ON P.mrn = D.mrn
			WHERE YEAR(D.diagDate) = @year
GO
	EXEC usp_Doctors_Diagnosed_Patients_Year 2014


--EXAMPLE # 07 [ Procedure with DEFAULT values for parameter/s]
--Create a proc that returns Doctors who diagnosed Patients in a given year. The same procedure 
--will display a message 'Diagnosis Year Missing' if the year is not given as an input. 
--NOTE : If no specific year is entered, NULL is a default value for the parameter
GO
	DROP PROC IF EXISTS usp_Doctors_Diagnosed_Patients_Year2;
GO
	CREATE PROC usp_Doctors_Diagnosed_Patients_Year2(@year INT = NULL)
	AS	
		BEGIN 
			IF @year IS NULL
				BEGIN
					SELECT P.mrn [PATIENT ID], 
						   CONCAT(P.pFName,' ',P.pLName) [FULL NAME], 
						   D.diagDate [DIAGNOSED DATE]
					FROM Patient P 
					JOIN
					Diagnosis D ON P.mrn = D.mrn
				END
			ELSE
				BEGIN
					SELECT P.mrn [PATIENT ID], 
						   CONCAT(P.pFName,' ',P.pLName) [FULL NAME], 
						   D.diagDate [DIAGNOSED DATE]
					FROM Patient P 
					JOIN
					Diagnosis D ON P.mrn = D.mrn
					WHERE YEAR(D.diagDate) = @year
				END
		END
-- SO WE CAN CALL THE PROCEDURE WITH/WITHOUT VALUES
GO
	EXEC usp_Doctors_Diagnosed_Patients_Year2
GO
	EXEC usp_Doctors_Diagnosed_Patients_Year2 2019



--EXERCISE # 01 
--Create a stored procedure that returns the the average salaries for each type of employees.
--NOTE : use 'usp' as prefix for new procedures, to mean ' user created stored procedure'
GO
	DROP PROC IF EXISTS usp_Employee_Avg_Salary;
GO	
	CREATE PROC usp_Employee_Avg_Salary
	AS
		SELECT empType [EMPLOYEE_TYPE],
			   FORMAT(CONVERT(DECIMAL(10,2),AVG(salary)),'C','en-us') [AVERAGE_SALARY_BY_EACH_TYPE_OF_EMPLOYEES]
		FROM Employee
		GROUP BY empType
GO
	EXEC usp_Employee_Avg_Salary


--EXERCISE # 02
--Create a stored procedure to get list of employees earning less than the average salary of 
--all employees
--NOTE : use 'usp' as prefix for new procedures, to mean ' user created stored procedure'
GO
	DROP PROC IF EXISTS usp_Employee_Earn_Less_Than_Avg_Salary_Of_All_Employees;
GO
	CREATE PROC usp_Employee_Earn_Less_Than_Avg_Salary_Of_All_Employees
	AS	
		SELECT *
		FROM Employee
		WHERE salary < (SELECT AVG(salary) FROM Employee)
		ORDER BY salary DESC
GO
	EXEC usp_Employee_Earn_Less_Than_Avg_Salary_Of_All_Employees


--EXERCISE # 03
--Create a procedure that returns list of Contractors that earn less than average salary of Principals
GO
	DROP PROC IF EXISTS usp_Lists_Contractors_Earn_Less_Than_Avg_Salary_Of_Principals;
GO
	CREATE PROC usp_Lists_Contractors_Earn_Less_Than_Avg_Salary_Of_Principals
	AS
		SELECT *
		FROM Employee
		WHERE empType = 'C' AND salary < (SELECT AVG(salary) FROM Employee WHERE empType = 'P')
		ORDER BY salary DESC
GO
	EXEC usp_Lists_Contractors_Earn_Less_Than_Avg_Salary_Of_Principals

--EXERCISE # 04 (*)
--Create a proc that returns Doctors who diagnosed Patients in a year 2017
--NOTE : (1) The result must include DocId, Full Name, Specialization, Email Address and DiagnosisDate
GO
	DROP PROC IF EXISTS usp_Doctors_Who_Diagnosed_Patients_in_2017;
GO
	CREATE PROC usp_Doctors_Who_Diagnosed_Patients_in_2017(@year INT)
	AS
		SELECT D.docId,
			   CONCAT(E.empFName,' ',E.empLName) [FULL NAME],
			   D.specialization [SPECIALIZATION],
			   E.email [EMAIL],
			   DG.diagDate [DIAGNOSED DATE]
		FROM Doctor D
		JOIN Diagnosis DG ON D.docId = DG.docId
		JOIN Employee E ON E.empId = D.empId
		WHERE YEAR(DG.diagDate) = @year
GO
	EXEC  usp_Doctors_Who_Diagnosed_Patients_in_2017 2017


--EXERCISE # 05 (*)
--Create a stored proc that returns list of patients diagnosed by a given doctor. 
GO
	DROP PROC IF EXISTS usp_List_Patients_Diagnosed_By_Doctors;
GO
	CREATE PROC usp_List_Patients_Diagnosed_By_Doctors
	AS
		SELECT *
		FROM Patient P
		JOIN Diagnosis DG ON P.mrn = DG.mrn
		JOIN Doctor D ON D.docId = DG.docId
GO
	EXEC  usp_List_Patients_Diagnosed_By_Doctors


--EXERCISE # 06 (*)
--Create a stored procedure that returns the average salary of Employees with a given empType
GO
	DROP PROC IF EXISTS usp_Avg_Salary_Of_Employee_By_Type;
GO
	CREATE PROC usp_Avg_Salary_Of_Employee_By_Type
	AS
		SELECT empType [EMPLOYEE TYPE],
			   FORMAT(CONVERT(DECIMAL(10,2),AVG(salary)),'C','en-us') [AVERAGE SALARY]
		FROM Employee
		GROUP BY empType
GO
	EXEC usp_Avg_Salary_Of_Employee_By_Type


--EXERCISE # 07 (*)
--Create a stored Proc that returns the number of diagnosis each doctor made in a 
--given month of the year -> pass both month and year as integer values
GO
	DROP PROC IF EXISTS usp_Number_Of_Diagnosed_By_Each_Doctor;
GO
	CREATE PROC usp_Number_Of_Diagnosed_By_Each_Doctor(@month INT, @year INT)
	AS
		BEGIN
			SELECT docId,
				   MONTH(diagDate) [MONTH],
				   YEAR(diagDate) [YEAR],
				   COUNT(docId) [NUMBER OF DIAGNOSIS MADE]
			FROM Diagnosis
			WHERE MONTH(diagDate) = @month AND YEAR(diagDate) = @year
			GROUP BY docId, MONTH(diagDate), YEAR(diagDate)
		END
GO
	EXEC usp_Number_Of_Diagnosed_By_Each_Doctor 3,2023

--Putting the parameters in correct order when the procedure is defined
----Sequence of the parmeters matter

--Assigning the parameters when the procedure is called, 
----Sequene of the parameters does't matter



/*
	USING STORED PROCEDURES FOR DML
 
Stored procs are mainly used to perform DML on tables or views
Stored Procs to insert data into a table
*/


--EXAMPLE # 08
--Create a proc that is used to insert data into the Disease table
GO
	DROP PROC IF EXISTS usp_Insert_Data_Into_Disease;
GO
	CREATE PROC usp_Insert_Data_Into_Disease
	AS
		BEGIN
			INSERT INTO Disease (dId, dName, dCategory, dType)
						VALUES (12, 'Flu', 'Bacterial infection', 'Contageous')
		END
GO
	EXEC usp_Insert_Data_Into_Disease
SELECT * FROM Disease

--EXERCISE # 09
--Create a procedure to insert data into Doctors table,
GO
	DROP PROC IF EXISTS usp_Insert_Data_Into_Doctor;
GO
	CREATE PROC usp_Insert_Data_Into_Doctor
	AS
		BEGIN
			INSERT INTO Doctor (empId, docId, licenseNo, licenseDate, [rank], specialization)
						VALUES ('EMP01', 'MD05', 'LNO-62-5146', '2019-05-21', 'Senior', 'Pydric')
		END
GO
	EXEC usp_Insert_Data_Into_Doctor

--Confirm for the insertion of new record using SELECT statement

	SELECT * FROM Doctor
	order by 2

--EXERCISE # 10
--Create a stored Proc to deletes a record from RentalCust table with a given SSN 
GO
	DROP PROC IF EXISTS usp_Delete_SSN_From_RentalCust;
GO
	CREATE PROC usp_Delete_SSN_From_RentalCust(@ssn CHAR(11))
	AS
		BEGIN TRANSACTION DeleteSSN
			IF @ssn IS NOT NULL 

					DELETE RentalCust
					WHERE social = @ssn
			ELSE
					ROLLBACK TRANSACTION DeleteSSN
					PRINT 'The provided SSN(social number) does not exist'

GO
	EXEC usp_Delete_SSN_From_RentalCust '210-45-6789'
	EXEC usp_Delete_SSN_From_RentalCust '210-45-0000'  --bogus SSN

	SELECT * FROM RentalCust

--EXERCISE # 11
--Create the stored procedure that delete a record of a customer in HotelCust table for a given SSN
--The procedure must display 'invalid SSN' if the given ssn is not found in the table
GO
	DROP PROC IF EXISTS usp_Delete_SSN_From_HotelCust;
GO
	CREATE PROC usp_Delete_SSN_From_HotelCust(@ssn CHAR(11))
	AS
		BEGIN TRANSACTION DeleteSSN
			IF @ssn IS NOT NULL 

					DELETE HotelCust
					WHERE SSN = @ssn
			ELSE
					ROLLBACK TRANSACTION DeleteSSN
					PRINT 'The provided SSN(social number) is invalid'

GO
	EXEC usp_Delete_SSN_From_HotelCust '240-29-6035'
	EXEC usp_Delete_SSN_From_HotelCust '210-45-0000'  --bogus SSN

	SELECT * FROM HotelCust

--EXERCISE # 12
--Write a stored procedure to delete a record from RentalCust for a given SSN. If the SSN is not found
--the procedure deletes the entire rows in the table.
--NOTE : First take backup for Employee table before performing this task. 
GO
	ALTER PROC usp_Delete_SSN_From_RentalCust(@ssn CHAR(11))
	AS
		BEGIN TRANSACTION DeleteSSN
			IF @ssn IS NOT NULL 

					DELETE RentalCust
					WHERE social = @ssn
			ELSE
					TRUNCATE TABLE RentalCust

GO
	EXEC usp_Delete_SSN_From_RentalCust '210-45-6789'
	EXEC usp_Delete_SSN_From_RentalCust '210-45-0000'  --bogus SSN

	SELECT * FROM RentalCust

	
--EXERCISE # 13
--Write a code that displays the list of customers with the middle two numbers of their SS is 45
GO
	DROP PROC IF EXISTS usp_Display_AllCust_With_SSN_45
GO
	CREATE PROC usp_Display_AllCust_With_SSN_45
	AS
			SELECT firstName,
				   lastName,
				   social 
			FROM RentalCust
			WHERE social LIKE '%-45-%'
		UNION 
			SELECT fName,
				   lName,
				   SSN 
			FROM HotelCust
			WHERE SSN LIKE '%-45-%'
GO
	EXEC usp_Display_AllCust_With_SSN_45
	   	  

--EXERCISE # 14
--Create a Proc that Deletes record/s from RentalCust table, by accepting ssn as a parameter. 
--The deletion can only happen if the middle two numbers of SSN is 45
GO
	ALTER PROC usp_Delete_SSN_From_RentalCust(@ssn CHAR(11))
	AS
		BEGIN TRANSACTION DeleteSSN
			IF @ssn IS NOT NULL 
					DELETE RentalCust
					WHERE social = @ssn AND social LIKE '%-45-%'
			ELSE
					ROLLBACK TRANSACTION DeleteSSN
					PRINT 'The provided SSN(social number) is invalid'

GO
	EXEC usp_Delete_SSN_From_RentalCust '210-45-6789'
	EXEC usp_Delete_SSN_From_RentalCust '903-00-4700'  --OTHER SSN

	--SELECT left(social,len(social) - CHARINDEX('-',social)) [SSN]
	--FROM RentalCust
	select * from RentalCust

--EXERCISE # 15
--Create a procedure that takes two numeric characters, and delete row/s from RentalCust table 
--if the middle two characters of the customer/s socal# are same as the passed characters 
GO
	DROP PROC IF EXISTS usp_Accept2CHAR_Delete_SSN_From_RentalCust;
GO
	CREATE PROC usp_Accept2CHAR_Delete_SSN_From_RentalCust(@ssn CHAR(2))
	AS
	BEGIN 
		SET NOCOUNT ON;
		BEGIN TRANSACTION DeleteSSN;
			IF @ssn IS NOT NULL 
				BEGIN
					IF LEN(@ssn) <= 2 
						BEGIN
							COMMIT TRANSACTION DeleteSSN;
							DELETE FROM RentalCust
							WHERE social LIKE '%' + @ssn + '%';
                        END
					ELSE
						BEGIN
							ROLLBACK TRANSACTION DeleteSSN;
							PRINT 'The provided number exceeds than the definition'
						END
				END
	END;
GO
	EXEC usp_Accept2CHAR_Delete_SSN_From_RentalCust '45'
	EXEC usp_Accept2CHAR_Delete_SSN_From_RentalCust '99'  --OTHER SSN
	select * from RentalCust

--EXERCISE # 16
--STORED PROCEDURES to update a table
--Create an stored procedure that updates the phone Number of a rental customer, for a given customer
--Note : The procedure must take two parameters social and new phone number
GO
	DROP PROC IF EXISTS usp_Update_Phone_RentalCust
GO
	CREATE PROC usp_Update_Phone_RentalCust(@social VARCHAR(20),@phone CHAR(12))
	AS
		--BEGIN 
			--SET NOCOUNT ON;
			--BEGIN TRANSACTION UPDATE_SSN_PHONE;
				--BEGIN
				UPDATE RentalCust
				SET phoneNo = @phone
				WHERE social = @social
GO
	EXEC usp_Update_Phone_RentalCust '000-48-6789','111-222-3333'
	SELECT * FROM RentalCust

--EXERCISE # 17
--Create a stored procedure that takes backup for RentalCust table into RentalCust_Archive table
--Note : RentalCustArchive table must be first created.
GO
	DROP PROC IF EXISTS usp_Backup_RentalCust
GO
	CREATE PROC usp_Backup_RentalCust
	AS
		BEGIN
			CREATE TABLE RentalCust_Archive 
				(
					fName VARCHAR(20) NULL,
					lName VARCHAR(20) NULL,
					SSN CHAR(11) NULL,
					DoB DATE NULL,
					phoneNo CHAR(12) NULL
				);
			
			-- BELOW CODE SIMPLY COPIES
			INSERT INTO RentalCust_Archive (fName, lName, SSN, DoB, phoneNo)
			SELECT firstName, lastName, social, DoB, phoneNo FROM RentalCust;
			
			--BELOW CODE WILL UPDATE WITH LATEST DATA 
			UPDATE RentalCust_Archive
			SET fName = RentalCust.firstName,
				lName = RentalCust.lastName,
				SSN = RentalCust.social,
				DoB = RentalCust.DoB,
				phoneNo = RentalCust.phoneNo
			FROM RentalCust
		    WHERE RentalCust.social = RentalCust_Archive.SSN;
	END;
GO
	EXEC usp_Backup_RentalCust
	SELECT * FROM RentalCust_Archive


--EXERCISE # 18
--Create a stored procedure that takes backup for HotelCust table into HotelCustArchive table
--Note : Use 'EXECUTE' command to automatically create and populate HotelCustArchive table.
GO
	DROP PROC IF EXISTS usp_Backup_HotelCust
GO
	CREATE PROC usp_Backup_HotelCust
	AS
		BEGIN
			CREATE TABLE HotelCustArchive 
				(
					fName VARCHAR(20) NULL,
					lName VARCHAR(20) NULL,
					SSN CHAR(11) NULL,
					DoB DATE NULL,
				);
			
			-- BELOW CODE SIMPLY COPIES
			INSERT INTO HotelCustArchive (fName, lName, SSN, DoB)
			SELECT fName, lName, SSN, DoB FROM HotelCust;

			--BELOW CODE WILL UPDATE WITH LATEST DATA 
			UPDATE HotelCustArchive
			SET fName = HotelCust.fName,
				lName = HotelCust.lName,
				SSN = HotelCust.SSN,
				DoB = HotelCust.DoB
			FROM HotelCust
		    WHERE HotelCust.SSN = HotelCustArchive.SSN;
	END;
GO
	EXEC usp_Backup_HotelCust
	SELECT * FROM HotelCustArchive;

	   
--Exercise - 17
--Recreate the above stored proc such that, it shouldn't delete (purge the data) before making
--sure the data is copied. Hint: use conditions (IF...ELSE) and TRY ... CATCH clauses

-- I DON'T UNDERSTAND THE QUESTION AND PASTED THE ABOVE CODE AS IT IS.
GO
	ALTER PROC usp_Backup_HotelCust
	AS
		BEGIN
			CREATE TABLE HotelCustArchive 
				(
					fName VARCHAR(20) NULL,
					lName VARCHAR(20) NULL,
					SSN CHAR(11) NULL,
					DoB DATE NULL,
				);
			
			-- BELOW CODE SIMPLY COPIES
			INSERT INTO HotelCustArchive (fName, lName, SSN, DoB)
			SELECT fName, lName, SSN, DoB FROM HotelCust;

			--BELOW CODE WILL UPDATE WITH LATEST DATA 
			UPDATE HotelCustArchive
			SET fName = HotelCust.fName,
				lName = HotelCust.lName,
				SSN = HotelCust.SSN,
				DoB = HotelCust.DoB
			FROM HotelCust
		    WHERE HotelCust.SSN = HotelCustArchive.SSN;
	END;
GO
	EXEC usp_Backup_HotelCust
	SELECT * FROM HotelCustArchive;

-- A simpler version of the above Stored Proc - with no dynamic date value appending to table name


/*
--************************************  VIEWS  *****************************************************

	VIEWS
		- Quite simply VIEW is saved query, or a copy of stored query, stored on the server
		- Is Virtual Table - that encapsulate SELECT queriey/es
		- It doesn't store data persistantly, but creates a Virtual table.
		- Can be used as a source for queries in much the same way as tables themselves
				: Can also be used to Join with tables
		- Replaces commonly run query/ies
		- Can't accept input parameters (Unlike Table Valued Functions (TVFs))

		- Components
			: A name
			: Underlaying query

		- Advantages
			- Hides the complexity of queries, (large size of codding)
			- Used as a mechanism to implement ROW and COLUMN level security
			- Can be used to present aggregated data and hide detail data

		- SYNTAX 
			: To create a view: 

					CREATE VIEW <view name> 
					AS 
						<Select statement>
					GO


			: To modify

					ALTER VIEW <view name>
					AS 
						<Select statement>
					GO
				 

			: To drop
					DROP VIEW statement
					AS 
						<Select statement>
					GO
					
	At view creation, 'GO' acts as delimiter to form a batch.  

	To display the code, use 'sp_helptext' for a stored procedure
		Syntax : 'sp_helptext < view_name >

*/


--EXAMPLE - View # 01 
--Write a code that displays patient's MRN, Full Name, Address, Disease Id and Disease Name

		SELECT P.mrn, 
			   CONCAT(P.pFName,' ',P.pLName) [FULL NAME], 
			   CONCAT(P.stAddress,' ',P.city,' ',P.[state],' ',P.zipCode) [ADDRESS],
			   D.dId [Disease ID],
			   D.dName [Disease Name]
		FROM Patient P
		JOIN Diagnosis DG ON P.mrn = DG.mrn
		JOIN Disease D ON D.dId = DG.dId
		

--EXAMPLE - View # 02
--Create simple view named vw_PatientDiagnosed using the above code.  
GO
	DROP VIEW IF EXISTS vw_PatientDiagnosed;
GO
	CREATE VIEW vw_PatientDiagnosed
	AS
		SELECT P.mrn, 
			   CONCAT(P.pFName,' ',P.pLName) [FULL NAME], 
			   CONCAT(P.stAddress,' ',P.city,' ',P.[state],' ',P.zipCode) [ADDRESS],
			   D.dId [Disease ID],
			   D.dName [Disease Name]
		FROM Patient P
		JOIN Diagnosis DG ON P.mrn = DG.mrn
		JOIN Disease D ON D.dId = DG.dId
		

--EXAMPLE - View # 03
--Check the result of vw_PatientDiagnosed by SELECT statement
GO
	SELECT * FROM vw_PatientDiagnosed

--EXAMPLE - View # 04
--Use vw_PatientDiagnosed and retrieve only the patients that came from MD
--Note : It is possible to filter Views based on a criteria, similar with tables
--GO
	--DROP VIEW IF EXISTS vw_PatientDiagnosed;
GO
	ALTER VIEW vw_PatientDiagnosed
	AS
		SELECT P.mrn, 
			   CONCAT(P.pFName,' ',P.pLName) [FULL NAME], 
			   CONCAT(P.stAddress,' ',P.city,' ',P.[state],' ',P.zipCode) [ADDRESS],
			   D.dId [Disease ID],
			   D.dName [Disease Name]
		FROM Patient P
		JOIN Diagnosis DG ON P.mrn = DG.mrn
		JOIN Disease D ON D.dId = DG.dId
		WHERE P.[state] = 'MD'
	

--EXAMPLE - View # 05
--Modify vw_PatientDiagnosed so that it returns the patients diagnosed in year 2017 
GO
	ALTER VIEW vw_PatientDiagnosed
	AS
		SELECT P.mrn, 
			   CONCAT(P.pFName,' ',P.pLName) [FULL NAME], 
			   CONCAT(P.stAddress,' ',P.city,' ',P.[state],' ',P.zipCode) [ADDRESS],
			   D.dId [Disease ID],
			   D.dName [Disease Name],
			   YEAR(DG.diagDate) [DIAGNOSED YEAR]
		FROM Patient P
		JOIN Diagnosis DG ON P.mrn = DG.mrn
		JOIN Disease D ON D.dId = DG.dId
		WHERE YEAR(DG.diagDate) = 2017


--EXAMPLE - View # 06
--Check the result of modified vw_PatientDiagnosed by SELECT statement
GO	
	SELECT * FROM vw_PatientDiagnosed;

--EXAMPLE - View # 07 
--Use sp_helptext to view the code for vw_PatientDiagnosed
--sp_helptext vw_PatientDiagnosed
GO
	sp_helptext vw_PatientDiagnosed;


--EXAMPLE - View # 08
--Drop vw_PatientDiagnosed
GO
	DROP VIEW IF EXISTS vw_PatientDiagnosed;

--EXERCISE - View # 01
--Create a view that returns Employees that live in state of Maryland, (Employee empId, FullName, DOB)
GO
	DROP VIEW IF EXISTS vw_Employee_Lives_Maryland
GO
	CREATE VIEW vw_Employee_Lives_Maryland
	AS
		SELECT empId, 
			   CONCAT(empFName,' ', empLName) [FULL NAME],
			   [state] [STATE],
			   DoB
		FROM Employee
		WHERE [state] = 'MD'

--EXERCISE - View # 02
--Create view that displays mId, Medicine ID and the number of times each medicine was prescribed.
GO
	DROP VIEW IF EXISTS vw_MedicinePrescribed;
GO
	CREATE VIEW vw_MedicinePrescribed
	AS
		SELECT mId,
			   COUNT(mId) [NUMBER OF TIMES PRESCRIBED]
		FROM MedicinePrescribed
		GROUP BY mId


--EXERCISE - View # 03
--Join vw_MedicinePrescribed with Medicine table and get mId, brandName, genericName and 
--number of times each medicine was prescribed  
GO
	DROP VIEW IF EXISTS vw_Number_Medecine_Prescribed;
GO
	CREATE VIEW vw_Number_Medecine_Prescribed
	AS
		SELECT MP.mId, 
			   M.brandName,
			   M.genericName,
			   MP.[NUMBER OF TIMES PRESCRIBED]
		FROM vw_MedicinePrescribed MP
		JOIN Medicine M ON M.mId = MP.mId
		GROUP BY MP.mId, M.brandName, M.genericName,MP.[NUMBER OF TIMES PRESCRIBED]

			   
--EXERCISE - View # 04
--Create a view that displays all details of a patient along with his/her diagnosis details
GO
	DROP VIEW IF EXISTS vw_Detail_Diagnosis;
GO
	CREATE VIEW vw_Detail_Diagnosis
	AS
		SELECT P.mrn [PATIENT ID],
			   CONCAT(P.pFName,' ',P.pLName) [PATIENT FULL NAME],
			   D.docId,
			   DG.diagDate,
			   DG.diagResult
		FROM Patient P
		JOIN Diagnosis DG ON P.mrn = DG.mrn
		JOIN Doctor D ON D.docId = DG.docId


--EXERCISE - View # 05
--Use the view created for 'EXERCISE - View # 04' to get the full detail of the doctors
GO
	DROP VIEW IF EXISTS vw_MedecinePrescribed_By_Doctors;
GO
	CREATE VIEW vw_MedecinePrescribed_By_Doctors
	AS
		SELECT D.docId,
			   CONCAT(E.empFName,' ',E.empLName) [DOCTOR FULL NAME],
			   D.licenseDate,
			   D.[rank],
			   D.specialization,
			   vw_DT.[PATIENT ID],
			   vw_DT.[PATIENT FULL NAME],
			   vw_DT.diagDate,
			   vw_DT.diagResult
		FROM Doctor D
		JOIN Employee E ON D.empId = E.empId
		JOIN vw_Detail_Diagnosis vw_DT ON D.docId = vw_DT.docId
GO
		SELECT * FROM vw_MedecinePrescribed_By_Doctors


--EXERCISE - View # 06 (*)
--Create the view that returns Contract employees only, empType='C'
GO
	DROP VIEW IF EXISTS vw_Contract_Employee;
GO
	CREATE VIEW vw_Contract_Employee
	AS
		SELECT *
		FROM Employee
		WHERE empType = 'C' 

	SELECT * FROM vw_Contract_Employee

--EXERCISE - View # 07 (*)
--Create the view that returns list of female employees that earn more 
--than the average salary of male employees
GO
	DROP VIEW IF EXISTS vw_Avg_Gender_Earning;
GO
	CREATE VIEW vw_Avg_Gender_Earning
	AS
		SELECT *
		FROM Employee E
		WHERE E.gender = 'F' AND E.salary > (SELECT AVG(salary) FROM Employee EE WHERE EE.gender = 'M')
		--ORDER BY E.salary DESC  --NOT ALLOWED IN VIEW

	SELECT * FROM vw_Avg_Gender_Earning

--EXERCISE - View # 08 (*)
--Create the view that returns list of employees that are NOT doctors
GO
	DROP VIEW IF EXISTS vw_Emps_Not_Doctors;
GO
	CREATE VIEW vw_Emps_Not_Doctors
	AS
		SELECT E.*
		FROM Employee E
		LEFT JOIN Doctor D ON E.empId = D.empId
		WHERE D.empId IS NULL

	SELECT * FROM vw_Emps_Not_Doctors


--EXERCISE - View # 09 (*)
--Create the view that returns list of employees that are NOT pharmacy personel
GO
	DROP VIEW IF EXISTS vw_Emps_Not_Pharma;
GO
	CREATE VIEW vw_Emps_Not_Pharma
	AS
		SELECT E.*
		FROM Employee E
		LEFT JOIN PharmacyPersonel P ON E.empId = P.empId
		WHERE P.empId IS NULL

	SELECT * FROM vw_Emps_Not_Doctors


--EXERCISE - View # 10 (*)
--Create the view that returns empid, full name, dob and ssn of doctors and pharmacy personels
GO
	DROP VIEW IF EXISTS vw_Emps_Pharma_Person;
GO
	CREATE VIEW vw_Emps_Pharma_Person
	AS
		SELECT CONCAT(E.empFName,' ',E.empLName) [FULL NAME],
			   E.DoB,
			   E.SSN,
			   P.*
		FROM Employee E
		JOIN PharmacyPersonel P ON E.empId = P.empId
		
	SELECT * FROM vw_Emps_Pharma_Person


--EXERCISE - View # 11 (*)
--Create the view that returns list of medicines that are NOT never prescribed. 
GO
	DROP VIEW IF EXISTS vw_Medicine_Not_Prescribed;
GO
	CREATE VIEW vw_Medicine_Not_Prescribed
	AS
		SELECT M.*
		FROM Medicine M
		LEFT JOIN MedicinePrescribed MD ON M.mId = MD.mId
		WHERE MD.mId IS NULL

	SELECT * FROM vw_Medicine_Not_Prescribed

--EXERCISE - View # 12 (*)
--Create the view that returns list of patients that are NOT diagnosed for a disease 'Cholera'
GO
	DROP VIEW IF EXISTS vw_Patient_Not_Diagnosed;
GO
	CREATE VIEW vw_Patient_Not_Diagnosed
	AS
		SELECT P.*
		FROM Patient P
		LEFT JOIN Diagnosis DG ON P.mrn = DG.mrn
		JOIN Disease D ON D.dId = DG.dId
		WHERE DG.mrn IS NULL AND D.dName = 'Cholera'

	SELECT * FROM vw_Patient_Not_Diagnosed

--EXERCISE - View # 13 (*)
--Create the view that returns list of employees that earn less than employees averge salary
GO
	DROP VIEW IF EXISTS vw_Emp_Earn_Less_Avg;
GO
	CREATE VIEW vw_Emp_Earn_Less_Avg
	AS
		SELECT E.* 
		FROM Employee E 
		WHERE salary < (SELECT AVG(salary) FROM Employee)

	SELECT * FROM vw_Emp_Earn_Less_Avg


--EXERCISE - View # 14 (*)
--Create simple view on Disease table vw_Disease that dispaly entire data
GO
	DROP VIEW IF EXISTS vw_Disease;
GO
	CREATE VIEW vw_Disease
	AS
		SELECT * 
		FROM Disease 
		
	SELECT * FROM vw_Disease


--EXERCISE - View # 15 (*)
--Create view that returns list of doctors that had NEVER done any diagnossis. 
GO
	DROP VIEW IF EXISTS vw_Doctors_Not_Diagnose;
GO
	CREATE VIEW vw_Doctors_Not_Diagnose
	AS
		SELECT D.* 
		FROM Doctor D
		LEFT JOIN Diagnosis DG ON D.docId = DG.docId
		WHERE DG.docId IS NULL

	SELECT * FROM vw_Doctors_Not_Diagnose
	   	  

--EXERCISE - View # 16 (*)
--Use view, vw_Disease, to insert one instance/record in Disease table
GO
		INSERT INTO vw_Disease 
				     VALUES (13, 'Corona','Transmitive Disease','Contigeous')
		
		SELECT * FROM vw_Disease
		

--EXERCISE - View # 17 (*)
--Use view, vw_Disease, to delete a record intered on previous exercise.
GO
	DELETE vw_Disease
	WHERE dName = 'Corona'
		
	SELECT * FROM vw_Disease
	
--EXERCISE - View # 18 (*)
--Create simple view on Medicine table vw_Medicine that dispaly entire data
GO
	DROP VIEW IF EXISTS vw_Medicine;
GO
	CREATE VIEW vw_Medicine
	AS
		SELECT *
		FROM Medicine

	SELECT * FROM vw_Medicine
	

--EXERCISE - View # 19 (*)
--Insert data into the Medicine table using vw_Medicine
GO
	INSERT INTO vw_Medicine 
					VALUES (15, 'Asprine', 'No name', 2000, 'Pain killer','2024-02-09', 0.35)

	SELECT * FROM vw_Medicine
/*
NOTE : Data insertion by views on more than one tables (joined) is not supported
     : Data insertion on joined tables is supported by triggers
*/

--EXERCISE - View # 20 (*)
--Create a view, vw_PatientAndDiagnosis, by joining patient and diagnosis tables and 
--try to insert data into vw_PatientDiagnosis view and explain your observation
GO
	DROP VIEW IF EXISTS vw_PatientAndDiagnosis;
GO
	CREATE VIEW vw_PatientAndDiagnosis
	AS
		SELECT P.*
		FROM Patient P
		JOIN Diagnosis DG ON P.mrn = DG.mrn
	
		SELECT * FROM vw_PatientAndDiagnosis

GO
	INSERT INTO vw_PatientAndDiagnosis
					VALUES ('PA037', 'KK','LL','2023-01-01','ar000','F','521-25-5641','21541 kk cc', 'Origon','OR','11111','2023-02-02')


/************************   USER DEFINED FUNCTIONS, UDFs  **********************************************

User Defined Functions, UDFs

		- Can return a scalar value (single value), inline table-valued OR multi table-valued

		
		User Defined Functions, UDFs 
			: Is compiled and executed everytime/whenever it is called.
			: The function must return a value 
***			: Function allows only SELECT statement in it.
*** 		: Function can be embedded in a SELECT statement.
*** 		: Functins can be used in the SQL statements anywhere in the WHERE/HAVING/SELECT sections 


	 - Difference with stored procedures,
		
		Stored Procedure, SPs
			: Are pre-compiled objects saved, in database, which gets executed whenever it is called.
			: Returing a value is optional, can return zero or n values.
*** 		: Allows SELECT as well as DML(INSERT/UPDATE/DELETE) statement
*** 		: Cannot be utilized in a SELECT statement 
*** 		: Cannot be used in the SQL statements anywhere in the WHERE/HAVING/SELECT sections	


	 - Types of User Defined Functions :
			- Scalar Functions
			- Inline Table-Valued Functins, TVFs
			- Multi-Statement Table-Valued Functions
	
	 - Scalar Fuctions
		- May or may not have parameter, but always return SINGLE (Scalar) value
		- Return value can be of any data type, except text, ntext, image, cusror and timestamp.
		- Example :
			- System functions are scalar functions

	- SYNTAX:
		
***		To create (Scalar Function)
			CREATE FUNCTION function_name (@Para1 DataType, @Para2 DataType........)
			RETURNS Return_Data_Type      --scalar
			AS
				BEGIN
					<statements>   --Function Body
					RETURN <value>
				END

***		To create (Table Valued Function)			
			CREATE FUNCTION function_name (@Para1 DataType, @Para2 DataType........)
			RETURNS TABLE                -- table valued
			AS
			 RETURN
			  (
				 <statements> 
	           )

		
		TO modify/alter
			ALTER FUNCTION function_name

		
		TO drop/delete
			DROP FUNCTION function_name


*/



--EXAMPLE : UDF # 01
--Create a scalar valued function that returns the total number of Employees
--Note : UDF without parameter
GO
	DROP FUNCTION IF EXISTS fn_Total_Employee;
GO
	CREATE FUNCTION fn_Total_Employee()
	RETURNS INT
	AS
		BEGIN
		DECLARE @total INT;
			SELECT @total = COUNT(empId)
			FROM Employee
			RETURN @total;
		END

--Now call the function to get the result
--While calling a udf, don't forget the precede it with the name of the schema that contains it
GO
	SELECT dbo.fn_Total_Employee()


--EXAMPLE : UDF # 02
--Create a udf that returns the average salary of a given employee type
--Note : UDF with parameter
GO
	DROP FUNCTION IF EXISTS fn_Avg_Salary_By_Type;
GO
	CREATE FUNCTION fn_Avg_Salary_By_Type(@type CHAR(1))
	RETURNS DECIMAL(10,2)
	AS
		BEGIN
			DECLARE @average_salary DECIMAL(10,2)
			SELECT @average_salary = CONVERT(DECIMAL(10,2), AVG(salary))
			FROM Employee
			WHERE empType = @type
			RETURN @average_salary
		END
--Now call the function with different empTypes
SELECT dbo.fn_Avg_Salary_By_Type('C')

--EXAMPLE : TVF # 01
--Create a user defined function, tvf_GetEmpTypeAndAVGSalary, that returns empType and their 
--corresponding Average Salaries. ( Creating UDF that returns a table )
GO
	DROP FUNCTION IF EXISTS tvf_GetEmpTypeAndAVGSalary;
GO
	CREATE FUNCTION tvf_GetEmpTypeAndAVGSalary()
	RETURNS TABLE
	AS
		RETURN
		(
			SELECT empType, 
				   CONVERT(DECIMAL(10,2),AVG(salary)) [AVERAGE SALARY]
			FROM Employee
			GROUP BY empType
		)
--Now call your function to get the result
GO
	SELECT * FROM dbo.tvf_GetEmpTypeAndAVGSalary() 


--EXAMPLE - TVF # 02
--Create TVF, tvf_TopHighlyPayedEmployees, that can retrieve the full name and dob of the 
--top 'x' number of highly paied employees.  
GO
	DROP FUNCTION IF EXISTS tvf_TopHighlyPayedEmployees;
GO
	CREATE FUNCTION tvf_TopHighlyPayedEmployees(@n INT)
	RETURNS TABLE
	AS
		RETURN
		(
			SELECT TOP (@n) WITH TIES CONCAT(empFName,' ',empLName) [FULL NAME],
								  DoB,
								  salarY [TOP SALARY]
			FROM Employee
			ORDER BY salary DESC
		)
GO
	SELECT * FROM  dbo.tvf_TopHighlyPayedEmployees(8)

--EXERCISE : UDF # 01
--Get the list of employees that earn less than the average salary of their own employee type. 
--Note : Use udf, that takes empType and returns Salary average, within WHERE clause to define 
--the criteria. 

GO
	DROP FUNCTION IF EXISTS fn_Employee_Earn_Less_Avg_EmpType;
GO
	CREATE FUNCTION fn_Employee_Earn_Less_Avg_EmpType(@type CHAR(1))
	RETURNS MONEY
	AS
		BEGIN
			DECLARE @average_salary MONEY;
		
				SELECT @average_salary = CONVERT(MONEY,AVG(salary)) 
				FROM Employee
				WHERE empType = @type
		    
				RETURN @average_salary
		END
GO
	SELECT *
	FROM Employee
	WHERE salary < dbo.fn_Employee_Earn_Less_Avg_EmpType('C')

--EXERCISE : UDF # 02
--Use the tdf, tvf_GetEmpTypeAndAVGSalary, created on 'EXAMPLE : TVF # 01' and join with 
--Employee table to get Employees that earn less than the average salaries of their corresponding types
GO
	CREATE FUNCTION tvf_GetEmpTypeAndAVGSalary()
	RETURNS TABLE
	AS
		RETURN
		(
			SELECT *
			FROM Employee E 
			JOIN
					(	SELECT empType,
							   CONVERT(MONEY,AVG(salary)) [AVERAGE SALARY]
						FROM Employee
						GROUP BY empType
					)AvgSal ON E.empType = AvgSal.empType
			WHERE E.salary < AvgSal.[AVERAGE SALARY]
		)
GO
	SELECT * FROM dbo.tvf_GetEmpTypeAndAVGSalary()

--EXERCISE - UDF # 03
--Disply the full name, gender, dob and AGE of all employees.
--NOTE : Create udf that returns AGE by taking DoB as an input parameter
GO
	DROP FUNCTION IF EXISTS fn_Get_Age;
GO
	CREATE FUNCTION fn_Get_Age(@dob DATE)
	RETURNS INT
	AS
		BEGIN
			DECLARE @age INT;
				SELECT @age = DATEDIFF(YEAR,@dob,GETDATE())-1 
				FROM Employee
				RETURN @age
		END
GO
	SELECT CONCAT(empFName,' ',empLName) [FULL NAME],
							   gender,
					           DoB, 
							   dbo.fn_Get_Age(DoB) [AGE]
	FROM Employee
	WHERE empId = 'EMP01'

--EXERCISE - UDF # 04
--Disply list of Doctor's id along with the number of patients each one had diagnosed. 
--(use a function to determine number of patients diagnosed )
GO
	DROP FUNCTION IF EXISTS fn_Get_Numberof_Patients;
GO
	CREATE FUNCTION fn_Get_Numberof_Patients(@docId CHAR(4))
	RETURNS INT
	AS
		BEGIN
			DECLARE @COUNT INT;
				SELECT @COUNT = COUNT(mrn)
				FROM Diagnosis
				WHERE docId = @docId;

				RETURN @COUNT
		END
GO
	SELECT docId [DOCTORS ID],
		   dbo.fn_Get_Numberof_Patients(docId) [Numberof_Patients]
	FROM Diagnosis
	GROUP BY docId


--EXERCISE - UDF # 05 (*)
--Edit the above code, 'EXERCISE - UDF # 04' to display only the doctors that diagnosed
--more than 2 patients. 
GO
	ALTER FUNCTION fn_Get_Numberof_Patients(@docId CHAR(4))
	RETURNS INT
	AS
		BEGIN
			DECLARE @COUNT INT;
				SELECT @COUNT = COUNT(mrn)
				FROM Diagnosis
				WHERE docId = @docId AND @COUNT > 2;

				RETURN @COUNT
		END
GO
	SELECT docId [DOCTORS ID],
		   dbo.fn_Get_Numberof_Patients(docId) AS Numberof_Patients
	FROM Diagnosis
	GROUP BY docId
	HAVING dbo.fn_Get_Numberof_Patients(docId) > 2

--EXERCISE - UDF # 06 (*)
--Disply the list of states along with the number of employees living in. 
--(use a function to calculate the number of employees )
--Method (1)

GO
	DROP FUNCTION IF EXISTS fn_NumberOf_Emp_State_Living;
GO
	CREATE FUNCTION fn_NumberOf_Emp_State_Living()
	RETURNS TABLE
	AS
		RETURN
			(
				SELECT [state] [STATES], 
					   COUNT(empId) [Number of Employees Lives]
				FROM Employee
				GROUP BY [state]
			)
GO
	SELECT *
	from dbo.fn_NumberOf_Emp_State_Living()

--Method (2)

--EXERCISE - UDF # 07 (*)
--Edit the above code, 'EXERCISE - UDF # 07' to display a state with highest number 
--of employees. 
GO
	ALTER FUNCTION fn_NumberOf_Emp_State_Living()
	RETURNS TABLE
	AS
		RETURN
			(
				WITH cte_Count 
					AS 
					(
					   SELECT [state] [STATES], 
					   COUNT(empId) [Number_of_Employees_Lives]
					   FROM Employee
					   GROUP BY [state]
					)
			SELECT MAX(Number_of_Employees_Lives) [HIGHEST INHABITANT] 
			FROM cte_Count
			--GROUP BY STATES	
			)
GO
	SELECT *
	from dbo.fn_NumberOf_Emp_State_Living()



--EXERCISE - UDF # 08 (*)
--Disply the list of medicines along with the number of time it was prescribed. Use a function
--to deterime the number of times each medicine was prescribed.
--Method (1)
GO
	DROP FUNCTION IF EXISTS fn_Medicine_Priscription_Times;
GO
	CREATE FUNCTION fn_Medicine_Priscription_Times()
	RETURNS TABLE 
	AS
		RETURN
			(
				SELECT mId, COUNT(prescriptionId) [PRESCRIBED TIMES]
				FROM MedicinePrescribed
				GROUP BY mId
			)
GO
	SELECT * FROM fn_Medicine_Priscription_Times()
--Method (2)

--EXERCISE - UDF # 09 (*)
--Modify the code for 'EXERCISE - UDF # 08' to include the name and brand of medicine as an output.
--Using Method(1)
GO
	ALTER FUNCTION fn_Medicine_Priscription_Times()
	RETURNS TABLE 
	AS
		RETURN
			(
				SELECT M.genericName,M.brandName,MP.mId, COUNT(MP.prescriptionId) [PRESCRIBED TIMES]
				FROM MedicinePrescribed MP
				JOIN Medicine M ON M.mId = MP.mId
				GROUP BY MP.mId,M.genericName,M.brandName
			)
GO
	SELECT * FROM fn_Medicine_Priscription_Times()

--Using Method (2)

--EXERCISE - UDF # 10 (*)
--Create a function, UDF, that perform addition, subtraction, multiplication and divisioin. 
GO
	DROP FUNCTION IF EXISTS fn_Calculator;
GO
	CREATE FUNCTION fn_Calculator(@num1 INT,@op CHAR(1), @num2 INT)
	RETURNS DECIMAL(10,2)
	AS
		BEGIN
			DECLARE @Result DECIMAL(10,2);
			
			SET @Result = CASE 
						WHEN @op = '+' THEN @num1 + @num2
						WHEN @op = '-' THEN @num1 - @num2
						WHEN @op = '*' THEN @num1 * @num2
						WHEN @op = '/' THEN CAST(@num1 AS DECIMAL(10,2)) / CAST(@num2 AS DECIMAL(10,2))
					  END;
			
			RETURN @Result;
		END
GO
	SELECT dbo.fn_Calculator(2, '/', 3)

--EXERCISE - TVF # 01
--Create TVF that can retrive the employee id, full name and dob of the ones that earn 
--more than a given salary.
GO
	DROP FUNCTION IF EXISTS tvf_Emp_Earn_More_Than_Salary;
GO
	CREATE FUNCTION tvf_Emp_Earn_More_Than_Salary(@Salary DECIMAL(10,2))
	RETURNS TABLE
	AS
		RETURN
			(
				 SELECT empId,
					   CONCAT(empFName,' ',empLName) [FULL NAME],
					   DoB,
					   salary
				FROM Employee
				WHERE salary > @Salary
			)
GO
	SELECT * FROM dbo.tvf_Emp_Earn_More_Than_Salary(72000)
	SELECT * FROM Employee


--EXERCISE - TVF # 02
--Create TVF that can retrive the number of times each medicine was prescribed. 
GO 
	DROP FUNCTION IF EXISTS tvf_Medicine_Prescribed_Times;
GO
	CREATE FUNCTION tvf_Medicine_Prescribed_Times()
	RETURNS TABLE
	AS 
		RETURN
			(
				SELECT mId [MEDICINE ID], 
					   COUNT(prescriptionId) [PRESCRIBED TIMES]
				FROM MedicinePrescribed
				GROUP BY mId
			)
GO
	SELECT * FROM dbo.tvf_Medicine_Prescribed_Times()

--Include BrandName and GenericName of each medicine
GO
	ALTER FUNCTION tvf_Medicine_Prescribed_Times()
	RETURNS TABLE
	AS 
		RETURN
			(
				SELECT MP.mId [MEDICINE ID], 
					   M.brandName,
					   M.genericName,
					   COUNT(MP.prescriptionId) [PRESCRIBED TIMES]
				FROM MedicinePrescribed MP
				JOIN Medicine M ON M.mId = MP.mId
				GROUP BY MP.mId,M.brandName,M.genericName
			)
GO
L	SELECT * FROM dbo.tvf_Medicine_Prescribed_Times()

--EXERCISE - TVF # 03
--Create TVF that can return a list of employees with a given gender.
GO
	DROP FUNCTION IF EXISTS tvf_Emp_Of_Given_Gender;
GO
	CREATE FUNCTION tvf_Emp_Of_Given_Gender(@gender CHAR(1))
	RETURNS TABLE
	AS
		RETURN
			(
				SELECT *
				FROM Employee
				WHERE gender = @gender
			)
GO
	SELECT * FROM dbo.tvf_Emp_Of_Given_Gender('F')

--EXERCISE - TVF # 04
--Create TVF that can return full name of patients that are diagnosed with a given disease.
--Note : The diseased is identified by name only.
GO
	DROP FUNCTION IF EXISTS tvf_Patients_With_Given_Disease;
GO
	CREATE FUNCTION tvf_Patients_With_Given_Disease(@DiseaseName VARCHAR(100))
	RETURNS TABLE
	AS
		RETURN
			(
				SELECT CONCAT(P.pFName,' ',P.pLName) [FULL NAME]
				FROM Patient P
				JOIN Diagnosis DG ON P.mrn = DG.mrn
				JOIN Disease D ON D.dId = DG.dId
				WHERE D.dName = @DiseaseName
			)
GO			
	SELECT * FROM tvf_Patients_With_Given_Disease('Strep throat');

/*******************    COMMON TABLE EXPRESSIONS (CTEs)        ***************************************


                           COMMON TABLE EXPRESSIONS (CTEs)

		- It is a TEMPORARY result set, that can be referenced within a SELECT, INSERT,
		  UPDATE or DELETE statements that immediately follows the CTE.
		- Is a named table expressions defined in a query
		- Provides a mechanism for defining a subquery that may then be used elsewhere in a query
*****	- Should be used after the statement that created it
		- Defined at the beginning of a query and may be refernced multiple times in the outer query
		- Defined in 'WITH clause'
		- Does not take parameter (unlike views, functions and stored procedures)
		- Does not reside in database (unlike views, functions and stored procedures)

	Syntax:

		WITH <CTE_name> [Optional Columns list corresponding to the values returned from inner select]
		AS	(
				<Select Statement>
			)
		...
		...
		...
		< SELECT query where the CTE is utilized >

*/


--EXAMPLE - CTE # 01
--Create a CTE that returns medicines and number of times they are prescribed (mId, NumberOfPrescriptions)
--Then join the created CTE with Medicine table to get the name and number of prescription of the medecines
GO
	WITH cte_Medicine_Prescribed_Times
	AS
		(
			SELECT mId,
				   COUNT(prescriptionId) [NumberOfPrescribed]
			FROM MedicinePrescribed
			GROUP BY mId
		)
	
	SELECT M.brandName,
		   CT.NumberOfPrescribed
	FROM cte_Medicine_Prescribed_Times CT
	JOIN Medicine M ON CT.mId = M.mId


--EXAMPLE - CTE # 02
--Create CTE that returns the average salaries of each type of employees
GO
	WITH cte_Return_Avg_Salary_Per_EmpType
	AS
		(
			SELECT empType [EMPLOYEE TYPE],
				   AVG(salary) [AVERAGE SALARY]
			FROM Employee
			GROUP BY empType
		)
	SELECT * FROM cte_Return_Avg_Salary_Per_EmpType


--EXERCISE - CTE # 01
--Modify the above code to sort the output by empType in descending order. 
GO
	WITH cte_Return_Avg_Salary_Per_EmpType
	AS
		(
			SELECT empType [EMPLOYEE TYPE],
				   AVG(salary) [AVERAGE SALARY]
			FROM Employee
			GROUP BY empType
		)
	SELECT * FROM cte_Return_Avg_Salary_Per_EmpType
	ORDER BY [EMPLOYEE TYPE] DESC


--EXERCISE - CTE # 02
--Create CTE to display PrescriptionId, DiagnossisNo, Prescription Date for each patient. Then use  
--the created CTE to retrive the dossage and number of allowed refills. 
GO
	WITH cte_Patient_Data
	AS
		(
			SELECT prescriptionId,
				   diagnosisNo,
				   prescriptionDate
			FROM Prescription
		)
	SELECT MD.dosage [DOSAGE],
		   MD.numberOfAllowedRefills
	FROM cte_Patient_Data ct
	JOIN MedicinePrescribed MD ON MD.prescriptionId = ct.prescriptionId
		
--EXERCISE - CTE # 03 (*)
--Create CTE to display the list of patients. The result must include mrn, full name, gender, dob and ssn.
GO
	WITH cte_List_Patients
	AS
		(
			SELECT mrn,
				   CONCAT(pFName,' ',pLName) [FULL NAME],
				   gender,
				   PDoB,
				   SSN
			FROM Patient
		)
	SELECT * FROM cte_List_Patients


--EXERCISE - CTE # 04 (*)
--Modify the above script to make use of the CTE to display the name of a disease
--each patient is diagnosed 
GO
	WITH cte_List_Patients
	AS
		(
			SELECT P.mrn,
				   CONCAT(P.pFName,' ',P.pLName) [FULL NAME],
				   P.gender,
				   P.PDoB,
				   P.SSN,
				   D.dName [DISEASE NAME DIAGNOSED]
			FROM Patient P
			JOIN Diagnosis DG ON P.mrn = DG.mrn
			JOIN Disease D ON D.dId = DG.dId
		)
	SELECT * FROM cte_List_Patients



--EXERCISE - CTE # 05 (*)
--Create CTE to display DiagnossisNo, DiagnossisDate and Disease Type of all Diagnossis made. Later use the CTE 
--to include the rank of the specialization and rank of the doctor. 
GO
	WITH cte_Diagnosed_Made
	AS
		(
			SELECT D.docId,
				   DG.diagnosisNo,
				   DG.diagDate,
				   DI.dType
			FROM Diagnosis DG
			JOIN Disease DI ON DI.dId = DG.dId
			JOIN Doctor D ON DG.docId = D.docId
		)
	SELECT CTE.*,D.[rank], D.specialization
	FROM Doctor D
	JOIN cte_Diagnosed_Made CTE ON D.docId = CTE.docId


--EXERCISE - CTE # 06 (*)
--Modify the above code, to incude doctor's full name and dob. 
GO
	WITH cte_Diagnosed_Made
	AS
		(
			SELECT D.docId,
				   CONCAT(E.empFName,' ',E.empLName) [FULL NAME],
				   E.DoB,
				   DG.diagnosisNo,
				   DG.diagDate,
				   DI.dType
			FROM Diagnosis DG
			JOIN Disease DI ON DI.dId = DG.dId
			JOIN Doctor D ON DG.docId = D.docId
			JOIN Employee E ON E.empId = D.empId
		)
	SELECT CTE.*,D.[rank], D.specialization
	FROM Doctor D
	JOIN cte_Diagnosed_Made CTE ON D.docId = CTE.docId


--EXERCISE - CTE # 07 (*)
--Create CTE that returns the average salaries of each type of employees. Then use the same CTE
--to display the list of employees that earn less than their respective employee type average salaries
GO
	WITH cte_Avg_Salary_EmpType
	AS
		(
			SELECT empType,
				   AVG(salary) [AVERAGE_SALARIES]
			FROM Employee
			GROUP BY empType
		)
	SELECT E.*
	FROM Employee E
	JOIN cte_Avg_Salary_EmpType CT ON E.empType = CT.empType
	WHERE E.salary < CT.AVERAGE_SALARIES

--EXERCISE - CTE # 08 (*)
--Create CTE that calculates the average salaries for each type of employees
GO
	WITH cte_Avg_Salary_EmpType
	AS
		(
			SELECT empType,
				   AVG(salary) [AVERAGE_SALARIES]
			FROM Employee
			GROUP BY empType
		)
	SELECT * FROM cte_Avg_Salary_EmpType



--EXERCISE - CTE # 09 (*)
--Use the CTE created for 'EXERCISE - CTE # 09' and provide the list of employees that earn less more than
--the average salary of their own gender.
GO
	WITH cte_Avg_Salary_EmpType
	AS
		(
			SELECT gender,
				   AVG(salary) [AVERAGE_SALARIES]
			FROM Employee
			GROUP BY gender
		)
	SELECT E.*
	FROM Employee E
	JOIN cte_Avg_Salary_EmpType CT ON E.gender = CT.gender
	WHERE E.salary < CT.AVERAGE_SALARIES


--EXERCISE - CTE # 10 (*)
--Create CTE that calculates the average salaries for female employees. Use the created CTE to display
--list of male employees that earn less than the average salary of female employees.
GO
	WITH cte_Avg_Salary_For_Woman
	AS
		(
			SELECT AVG(salary) [AVERAGE_SALARIES]
			FROM Employee
			WHERE gender = 'F'
		)
	SELECT E.*
	FROM Employee E,cte_Avg_Salary_For_Woman CT
	WHERE E.gender ='M' AND E.salary < CT.AVERAGE_SALARIES

/*********************************    TRIGGERS    ***********************************************

TRIGGERS - 
	- Are a special type of stored procedures that are fired/executed automatically in response
	  to a triggering action
	- Helps to get/capture audit information
	- Like stored procedures, views or functions, triggers encapsulate code.  
	- Triggers get fired (and run) by themselves only when the event for which they are 
	  created for occurs, i.e. We do not call and run triggers, they get fired/run 
	  by by their own


	Types of triggers
		- DML triggers
			: Fires in response to DML events (Insert/Delete/Update)
		- DDL triggers
			: Fires in response to DDL events (Create/Alter/Drop)
		- login triggers
			: Fires in response to login events

	DML Triggers
		There are two types of DML Triggers: AFTER/FOR and INSTEAD OF, and there are 
		three DML events (INSERT, DELETE and UPDATE) under each type - 

			FOR/AFTER	INSERT
						DELETE
						UPDATE

			INSTEAD OF	INSERT
						DELETE
						UPDATE

		
	Syntax: 
		Whenever a trigger is created, it must be created for a given table, and for a 
		specific event, 
		
		CREATE TRIGGER <trigger name>                         -- name of the trigger
		ON <table name>										  -- name of table
		<FOR/AFTER | INSTEAD OF INSERT/DELETE/UPDATE>         -- specific event 
		AS 
			BEGIN
				<your t-sql statement>
			END	


	Uses for triggers:
		- Enforce business rules
		- Validate input data
		- Generate a unique value for a newly-inserted row in a different file.
		- Write to other files for audit trail purposes
		- Query from other files for cross-referencing purposes
		- Access system functions
		- Replicate data to different files to achieve data consistency

*/

	
/*
The use of 'inserted' table and 'deleted' table by triggers.

	- DML trigger statements use two special tables: the deleted table and the inserted tables. 
	- SQL Server automatically creates and manages these two tables. 
	- They are temporary, that only lasts within the life of trigger
	- These two tables can be used to test the effects of certain data modifications 
	  and to set conditions for DML trigger actions.

	- In DML triggers, the inserted and deleted tables are primarily used to perform the following:
		- Extend referential integrity between tables.
		- Insert or update data in base tables underlying a view.
		- Test for errors and take action based on the error.
		- Find the difference between the state of a table before and after a data 
		  modification and take actions based on that difference.


**** - The deleted table stores copies of the affected rows during DELETE and UPDATE statements.
***  - The inserted table stores copies of the affected rows during INSERT and UPDATE statements. 
*/




--EXERCISE - TRIGGER # 01 (FOR/AFTER) UPDATE
--Create a trigger that displays a message 'Disease table is updated' when the table gets updated 
GO
	SELECT *
	INTO tempTable
	FROM Disease
	WHERE 1=0  --IT'S OKAY WHETHER YOU ASSIGN IT OR NOT
GO
	DROP TRIGGER IF EXISTS trMsg_Update_Disease_Table;
GO
	CREATE TRIGGER trMsg_Update_Disease_Table
	ON Disease
	AFTER UPDATE
	AS
		BEGIN
			INSERT INTO tempTable
			SELECT *
			FROM inserted
			PRINT 'Disease table is updated'
		END

--First check the content dType for dId=5 before updating
	SELECT * FROM Disease
	
--Update the table
	UPDATE Disease
	SET dType = 'UPDATED with contigeous'
	WHERE dId = 12

--Check the table after update, 
	SELECT * FROM Disease

	SELECT * FROM tempTable

--Drop/Delete a trigger
GO
	DROP TRIGGER trMsg_Update_Disease_Table

--EXERCISE - TRIGGER # 02 (FOR/AFTER INSERT)
--Create a trigger that displays a message 'New disease is inserted' when a new record gets 
--instered on disease table
GO
	DROP TRIGGER IF EXISTS trMsg_Insert_Disease_Table;
GO
	CREATE TRIGGER trMsg_Insert_Disease_Table
	ON Disease
	AFTER INSERT
	AS
		BEGIN
			INSERT INTO tempTable
			SELECT *
			FROM inserted
			PRINT 'New Disease is Inserted'
		END

--Inserting disease
	INSERT INTO Disease VALUES (13,'Brain Tumoir', 'Cerebral disease','Severe')

	SELECT * FROM Disease
	SELECT * FROM tempTable

--EXERCISE - TRIGGER # 03 (FOR/AFTER) DELETE
--Create a trigger that displays a message 'A record is deleted from HotelCust table' when a new record gets 
--deleted from HotelCust table
GO
	SELECT *
	INTO tempTable2
	FROM HotelCust
	WHERE 1=0  --IT'S OKAY WHETHER YOU ASSIGN IT OR NOT
GO
	DROP TRIGGER IF EXISTS trMsg_Delete_HotelCust_Table;
GO
	CREATE TRIGGER trMsg_Delete_HotelCust_Table
	ON HotelCust
	AFTER DELETE
	AS
		BEGIN
			BEGIN TRANSACTION

			INSERT INTO tempTable2
			SELECT *
			FROM deleted
			PRINT 'A record is deleted from HotelCust table'
		END

--Deleting HotelCust
	DELETE HotelCust
	WHERE SSN = '240-29-6035'

	SELECT * FROM HotelCust
	SELECT * FROM tempTable2


--EXERCISE - TRIGGER # 04 (INSTEAD OF INSERT)(*)
--Create a trigger that displays a message 'A new record was about to be inserted into HotelCust table' when 
--a new record was about to be inserted on HotelCust table
GO
	DROP TRIGGER IF EXISTS trMsg_Insert_HotelCust_Table;
GO
	CREATE TRIGGER trMsg_Insert_HotelCust_Table
	ON HotelCust
	INSTEAD OF INSERT
	AS
		BEGIN
		
			DECLARE @ssn CHAR(11);
		
			SELECT @ssn = i.SSN
			FROM inserted i

			IF NOT EXISTS (SELECT TOP 1 * FROM HotelCust WHERE SSN = @ssn)
				BEGIN
					BEGIN TRANSACTION
			
					INSERT INTO HotelCust
					SELECT * 
					FROM inserted i
					PRINT 'A new record was about to be inserted into HotelCust table'
				END
			ELSE
				BEGIN
					PRINT 'Duplicate Insertion is not allowed'
					ROLLBACK TRANSACTION
				END
		END

--Inserting into HotelCust
	INSERT INTO HotelCust VALUES ('Alem','Chane', '240-29-6035','1981-11-13')
	
	SELECT * FROM HotelCust
	
--EXERCISE - TRIGGER # 05 (INSTEAD OF UPDATE) (*)
--Create a trigger that displays a message 'A record was about to be UPDATED into Medicine table' when 
--a record was about to be updated on Medicine table
GO
	DROP TRIGGER IF EXISTS trMsg_Update_Medicine_Table;
GO
	CREATE TRIGGER trMsg_Update_Medicine_Table
	ON Medicine
	INSTEAD OF UPDATE
	AS
		BEGIN
			DECLARE @mid CHAR(11);
			DECLARE @use VARCHAR(50);

			SELECT @mid = i.mId, @use = i.[use]
			FROM inserted i

			IF EXISTS (SELECT TOP 1 * FROM Medicine WHERE mId = @mid AND [use] != @use)
				BEGIN
					BEGIN TRANSACTION
						UPDATE Medicine
						SET [use] = @use
						WHERE mId = @mid
						PRINT 'A record was about to be UPDATED into Medicine table'
				END
			ELSE
				BEGIN
					PRINT 'There is no Medicine ID Associated with Medicine name. Updating Aborted'
					ROLLBACK TRANSACTION
				END
		END

--Inserting into HotelCust
GO	
	UPDATE Medicine
	SET [use] = 'Rough Throat Reliver'
	WHERE mId = 3

	SELECT * FROM Medicine
	
--EXERCISE - TRIGGER # 06 (INSTEAD OF DELETE) (*)
--Create a trigger that displays a message 'A record was about to be DELETED from RentalCust table' when 
--a record was about to be deleted on RentalCust table
GO
	DROP TRIGGER IF EXISTS trMsg_Delete_RentalCust_Table;
GO
	CREATE TRIGGER trMsg_Delete_RentalCust_Table
	ON RentalCust
	INSTEAD OF DELETE
	AS
		BEGIN
			DECLARE @ssn CHAR(11);

			SELECT @ssn = d.social 
			FROM deleted d

			IF EXISTS (SELECT TOP 1 * FROM RentalCust WHERE social = @ssn)
				BEGIN
					BEGIN TRANSACTION					
						DELETE RentalCust
						WHERE social = @ssn
						PRINT 'A record was about to be DELETED into RentalCust table'
				END
			ELSE
				BEGIN
					PRINT 'There is no specified SSN number in the Database'
					ROLLBACK TRANSACTION
				END
		END

--Inserting into HotelCust
GO	
	DELETE RentalCust
	WHERE social = '792-02-0789'

	SELECT * FROM RentalCust

--EXERCISE - TRIGGER # 07  (Using 'inserted' table)
--Create a trigger that displays the inserted record on RentalCust table
GO
	SELECT *
	INTO tempInserted
	FROM RentalCust
GO
	DROP TRIGGER IF EXISTS trMsg_Display_Inserted
GO
	CREATE TRIGGER trMsg_Display_Inserted
	ON RentalCust
	FOR INSERT
	AS
		BEGIN
			INSERT INTO tempInserted
			SELECT *
			FROM inserted
		END
GO
	SELECT * FROM tempInserted

--EXAMPLE - TRIGGER # 08  (Using 'deleted' table)
GO
	SELECT *
	INTO tempDeleted
	FROM RentalCust
GO
	DROP TRIGGER IF EXISTS trMsg_Display_Deleted
GO
	CREATE TRIGGER trMsg_Display_Deleted
	ON RentalCust
	FOR DELETE
	AS
		BEGIN
			INSERT INTO tempInserted
			SELECT *
			FROM deleted
		END
GO
	SELECT * FROM tempInserted


--EXAMPLE - TRIGGER # 09  (Using 'deleted' table)
--Create following audit table RentalCustDeleteAudit, that maintains/captures the records deleted/removed from HotelCust
--and create a trigger that inititate data backup when a record is deleted from HotelCust table 
--Note : Use FOR/AFTER DELETE trigger as required.
GO
	SELECT *
	INTO HotelCustDeleteAudit
	FROM HotelCust
GO
	DROP TRIGGER IF EXISTS trMsg_Audit_HotelCust_Table	
GO
	CREATE TRIGGER trMsg_Audit_HotelCust_Table
	ON HotelCust
	FOR DELETE
	AS
		BEGIN
			INSERT INTO HotelCustDeleteAudit
			SELECT *
			FROM deleted 
		END
GO
--DELETING RECORDS CAN BE CALLED HERE

	SELECT * FROM HotelCustDeleteAudit

--EXERCISE - TRIGGER # 10  (Using 'inserted' table)
--Create Audit table, RentalCustInsertAudit, that maintains/captures the records inserted 
--on RentalCust table. The audit table also capture the date the insertion took place
--Note : Use FOR/AFTER INSERT trigger as required.
GO
	CREATE TABLE RentalCustInsertAudit (
				Id INT IDENTITY(1,1) PRIMARY KEY,
				firstName VARCHAR(20),
				lastName VARCHAR(20),
				SSN CHAR(11),
				DoB DATE,
				phoneNo CHAR(12),		
				DateInserted DATETIME DEFAULT(GETDATE())
	       );
GO  
	DROP TRIGGER IF EXISTS trMsg_Audit_RentalCust_Table	
GO
	CREATE TRIGGER trMsg_Audit_RentalCust_Table
	ON RentalCust
	FOR INSERT
	AS
		BEGIN
			INSERT INTO RentalCustInsertAudit(firstName,lastName,SSN,DoB,phoneNo)
			SELECT i.firstName,i.lastName,i.social,i.DoB,i.phoneNo
			FROM inserted i
		END
GO
--INSERTING RECORDS CAN BE CALLED HERE
GO
	INSERT INTO RentalCust(firstName, lastName, social, DoB, phoneNo)
				   VALUES ('EYASU','HILUF','123-45-6789','1981-11-13','204-123-4567')
	
	SELECT * FROM RentalCustInsertAudit
    
	SELECT * FROM RentalCust

--EXERCISE - TRIGGER # 11  (AFTER INSERT TRIGGER, Application for Audit) (*)
--Create Audit table, Medicine_Insert_Audit, that maintains/captures the new mId, BrandName, 
--the person that made the entry, the date/time of data entry.
--Use system function 'SYSTEM_USER' to get login detail of the user that perform data insertion
GO 
	CREATE TABLE Medicine_Insert_Audit (
				medicneId SMALLINT,
				brandName VARCHAR(40),
				personMadeEntry VARCHAR(50),
				DateInserted DATETIME DEFAULT(GETDATE())
	       );
GO 
--select sys.sysusers.name 
--select SYSTEM_USER;
--SELECT CURRENT_USER;  
-- I CAN DEINFE IN THE TABLE personMadeEntry CHAR(30) NOT NULL DEFAULT CURRENT_USER

--Now create a trigger that automagically inserts data into the MedicineInsertAudit table, 
--whenever data inserted into Medicine table
GO
	DROP TRIGGER IF EXISTS trMsg_Audit_Medicine_Insert	
GO
	CREATE TRIGGER trMsg_Audit_Medicine_Insert
	ON Medicine 
	AFTER INSERT
	AS
		BEGIN
			INSERT INTO Medicine_Insert_Audit(medicneId,brandName,personMadeEntry)
			SELECT i.mId,i.brandName, SYSTEM_USER
			FROM inserted i;
		END
GO

--Test how the trigger works by inserting one row data to Medicine table
GO
	INSERT INTO Medicine
				VALUES(15,'COLDCAP','CAFFEINE ANHYDROUS',10,'FOR COLD AND FLU','2023-11-09',15.00)

	SELECT * FROM Medicine_Insert_Audit
	SELECT * FROM Medicine

--EXERCISE - TRIGGER # 12 (AFTER DELETE TRIGGER, Application for Archive) (*)
--Create and use archieve table, Disease_Delete_Archive, that archieves the disease information
--deleted from Disease table. The same table must also capture the time and the person 
--that does the delete.
--NOTE : Use system function SYSTEM_USER to return the system user.      
GO 
	CREATE TABLE Disease_Delete_Archive (
				medicneId SMALLINT,
				brandName VARCHAR(40),
				genericName VARCHAR(50),
				qtyInStock INT,
				[Use] VARCHAR(50),
				expDate DATE,
				personMadeDeletion VARCHAR(50),
				DateInserted DATETIME DEFAULT(GETDATE())
	       );
GO 
	DROP TRIGGER IF EXISTS trMsg_Audit_Medicine_Delete	
GO
	CREATE TRIGGER trMsg_Audit_Medicine_Delete
	ON Medicine 
	AFTER DELETE
	AS
		BEGIN
			INSERT INTO Disease_Delete_Archive(medicneId,brandName,genericName,qtyInStock,[Use],expDate,personMadeDeletion)
			SELECT d.mId,d.brandName,d.genericName,d.qtyInStock,d.[use],d.expDate, SYSTEM_USER
			FROM deleted d;
		END
GO

--DELETING one row data to Medicine table goes here below
GO
	DELETE Medicine
	WHERE mId = 15

	SELECT * FROM Disease_Delete_Archive

	SELECT * FROM Medicine

--EXERCISE - TRIGGER # 13  (*)
--Create a trigger that displays the following kind of message when a record is deleted from HotelCust table.
--Assume the record for 'Abebe' is deleted, the trigger should display
--			'A record for Abebe is deleted from HotelCust table'  

GO
	DROP TRIGGER IF EXISTS trMsg_Display_Deletion_Alert
GO
	CREATE TRIGGER trMsg_Display_Deletion_Alert
	ON HotelCust
	FOR DELETE
	AS
		BEGIN
			DECLARE @FIRSTNAME VARCHAR(20);
				SELECT @FIRSTNAME = d.fName
				FROM deleted d
			PRINT 'A record for ' + @FIRSTNAME + ' has been deleted from HotelCust table'
		END
GO
	DELETE HotelCust
	WHERE fName = 'Fiker'

	SELECT * FROM HotelCust

--EXERCISE - TRIGGER # 14 (AFTER UPDATE TRIGGER, Application for audit) (*)
--Create an After Update Trigger on Employee table that documents the old and new salary information of 
--an Employee and the date salary update was changed. 
--First create EmployeePromotionTracker (empId, empId, oldSalary, newSalary)
GO 
	CREATE TABLE EmployeePromotionTracker (
				empId CHAR(5),
				firsName VARCHAR(25),
				lastName VARCHAR(25),
				oldSalary DECIMAL(8,2),
				newSalary DECIMAL(8,2),
				salaryUpdatedDate DATETIME DEFAULT(GETDATE())
	       );
GO  
	DROP TRIGGER IF EXISTS trMsg_Audit_Old_New_Salary	
GO
	CREATE TRIGGER trMsg_Audit_Old_New_Salary
	ON Employee 
	AFTER UPDATE
	AS
		BEGIN
			DECLARE @oldSalary DECIMAL(8,2);
			DECLARE @empID CHAR(5);

			SELECT @oldSalary = d.salary, @empID = d.empId
			FROM deleted d
			JOIN inserted i ON d.empId = i.empId

			INSERT INTO EmployeePromotionTracker (empId, firsName,lastName, oldSalary, newSalary)
			SELECT i.empId,i.empFName,i.empLName, @oldSalary, i.salary
			FROM inserted i
			WHERE i.empId = @empID
		END
GO
--Now check the tigger by updating one employee salary
--First check the data in both tables
GO
	UPDATE Employee
	SET salary = 75000.00  --OLD SALARY WAS 35700
	WHERE empId = 'EMP28'

	SELECT * FROM EmployeePromotionTracker

	SELECT * FROM Employee

--EXERCISE - TRIGGER # 15  ( AFTER UPDATE )
--Create the following audit table, RentalCust_Update_Audit, and code a trigger that 
--can maintains/captures the updated record/s of RentalCust table.
 
--Assume both the fist name and DoB changed during update, The 'Message' column should capture 
--a customized statement as follows
--	  'First Name changed from 'Abebe' to 'Kebede', DoB changed from 'old DoB' to 'new DoB'

--Similarly the 'Message' column should be able to capture all the changes that took place.
GO
	CREATE TABLE RentalCust_Update_Audit
   	(
		firstName VARCHAR(20),
		lastName VARCHAR(20),
		SSN CHAR(11),
		dateOfBirth DATE,
		phoneNo CHAR(12),
		auditMessages VARCHAR(500) 
	);
GO 
	DROP TRIGGER IF EXISTS trMsg_Audit_Update_Changes	
GO
  CREATE TRIGGER trMsg_Audit_Update_Changes
  ON RentalCust 
  AFTER UPDATE
  AS
	BEGIN
	 DECLARE @oldFirstName VARCHAR(20);
	 DECLARE @oldLastName VARCHAR(20);
	 DECLARE @oldSSN CHAR(11);
	 DECLARE @oldDateOfBirth DATE;
	 DECLARE @oldPhoneNo CHAR(12);
			
	 SELECT @oldFirstName = d.firstName, 
	     	@oldLastName = d.lastName, 
			@oldSSN = d.social, 
			@oldDateOfBirth = d.DoB, 
			@oldPhoneNo = d.phoneNo
	 FROM deleted d
	JOIN inserted i ON d.social = i.social

	INSERT INTO RentalCust_Update_Audit (firstName, lastName, SSN, dateOfBirth, phoneNo, auditMessages)
	SELECT i.firstName,i.lastName,i.social,i.DoB,i.phoneNo, 
	     auditMessages = CASE 
							WHEN @oldFirstName != i.firstName THEN 'First Name changed from '+ @oldFirstName +' to '+ i.firstName
							WHEN @oldLastName != i.lastName THEN 'Last Name changed from '+ @oldLastName +' to '+ i.lastName
							WHEN @oldSSN != i.social THEN 'SSN changed from '+ @oldSSN +' to '+ i.social
							WHEN @oldDateOfBirth != i.DoB THEN 'Date of Birth changed from '+ CAST(@oldDateOfBirth AS VARCHAR(10)) +' to '+ CAST(i.DoB AS VARCHAR(10))
							WHEN @oldPhoneNo != i.phoneNo THEN 'Phone Number changed from '+ @oldPhoneNo +' to '+ i.phoneNo
		   			     END
			FROM inserted i
			WHERE i.social = @oldSSN
   END
GO
	UPDATE RentalCust
	SET firstName = 'Tsebaot'
	WHERE social = '123-45-6789'
		
	SELECT * FROM RentalCust_Update_Audit

	SELECT * FROM RentalCust

--EXERCISE - TRIGGER # 16 (*)
--Create Medicine_Audit table, with two columns having Integer with IDENDITY function and description - nvarchar(500) 
--data types. The table must insert a description for the changes made on Medicine table during UPDATE
GO
	CREATE TABLE Medicine_Audit
   	(
	    auditNumber int IDENTITY(1,1),
		Descriptions NVARCHAR(500) 
	);
GO 
	DROP TRIGGER IF EXISTS trMsg_Audit_Update_Changes_ofDoctor	
GO
  CREATE TRIGGER trMsg_Audit_Update_Changes_ofDoctor
  ON Medicine
  AFTER UPDATE
  AS
	BEGIN
	 DECLARE @BRANDNAME VARCHAR(40);
	 DECLARE @GENERICNAME CHAR(50);
	 DECLARE @QTYINSTOCK INT;
	 DECLARE @USE VARCHAR(50);
	 DECLARE @EXPIRATIONDATE DATE;
	 DECLARE @UNITPRICE DECIMAL(6,2);
			
	 SELECT @BRANDNAME = d.brandName, 
			@GENERICNAME = d.genericName, 
			@QTYINSTOCK = d.qtyInStock, 
			@USE = d.[use],
			@EXPIRATIONDATE = d.expDate,
			@UNITPRICE = d.unitPrice
	 FROM deleted d
	JOIN inserted i ON d.mId = i.mId;

	INSERT INTO Medicine_Audit(Descriptions)
	SELECT Descriptions = CASE
		WHEN @BRANDNAME != i.brandName THEN 'Brand name '+@BRANDNAME+' has been modified with '+i.brandName
		WHEN @GENERICNAME != i.genericName  THEN 'Generic name '+@GENERICNAME+  ' for brand name '+@BRANDNAME+' has been modified with '+i.genericName
		WHEN @QTYINSTOCK != i.qtyInStock THEN 'Quantity in Stock '+CAST(@QTYINSTOCK AS VARCHAR(10))+  ' for brand name '+@BRANDNAME+' has been modified with '+CAST(i.qtyInStock AS VARCHAR(10))
		WHEN @USE != i.[use] THEN 'Usage of medicine '+@USE+ ' for brand name '+@BRANDNAME+' has been modified with '+i.[use]
		WHEN @EXPIRATIONDATE != i.expDate THEN 'Expiration date '+CAST(@EXPIRATIONDATE AS VARCHAR(10))+ ' for brand name '+@BRANDNAME+ ' has been modified with '+CAST(i.expDate AS VARCHAR(10))
		WHEN @UNITPRICE != i.unitPrice THEN 'Unit Price '+CAST(@UNITPRICE AS VARCHAR(10))+ ' for brand name '+@BRANDNAME+ ' has been modified with '+CAST(i.unitPrice AS VARCHAR(10))
	  END
	FROM inserted i

	END
GO
	UPDATE Medicine
	SET [use] = 'FOR COLD, FLU, HEADACHE, MOUTH WATERING'  --previous use is 'FOR COLD AND FLU'
	WHERE mId = 15;

	SELECT * FROM Medicine_Audit;

	SELECT * FROM Medicine;


--EXERCISE - TRIGGER # 18 (After Delete Trigger ) (*)
--Create a trigger that archieve the list of Terminated/Deleted Employees.

--Problem analysis :
--The employee can be a Doctor, PharmacyPersonel or Just Employee. Since, Doctor and 
--PharmacyPersonnel tables depend on the Employee table, we need to first redfine the foreign keys
--to allow employee delete - with cascading effect. Then the next challenge is, deleting a Doctor
--also affecs the Diagnosis table, we should redefine the foreign key between Doctor and Diagnosis
--table to allow the delete - with SET NULL effect


--Steps to follow,
--Step (1): Drop the Foreign Key Constraints from the Doctor, PharmacyPersonel, Diagnosis, and MedicineDispense tables
--          and redefine FK constraints for all child tables
--Step (2): Create the Employee_Terminated_Archive table
--Step (3): Create the trigger that inserts the deleted employee data into Employee_Terminated_Archive

---NOW START DOING ONE BY ONE

--Step (1): Drop the Foreign Key Constraints from the Doctor, PharmacyPersonel, Diagnosis, and MedicineDispense tables
--          and redefine FK constraints for all child tables
GO
	ALTER TABLE Doctor
	DROP CONSTRAINT FK_Doctor_Employee_empId
GO
	ALTER TABLE PharmacyPersonel
	DROP CONSTRAINT FK_PharmacyPersonel_empId
--GO
--	ALTER TABLE Diagnosis 
--	ALTER COLUMN docId CHAR(4) NULL  --it's already SET NULL
GO 
	ALTER TABLE Diagnosis 
	DROP CONSTRAINT FK_Diagnosis_Doctor_docId 
GO
	ALTER TABLE MedicineDispense
	ALTER COLUMN ppId INT NULL 
GO 
	ALTER TABLE MedicineDispense
	DROP CONSTRAINT FK_MedicineDispense_ppId

--: Now redefine the Constraints
GO
	ALTER TABLE Doctor
	ADD CONSTRAINT FK_Doctor_Employee_empId FOREIGN KEY (empId) REFERENCES Employee (empId) ON UPDATE CASCADE ON DELETE CASCADE
GO
	ALTER TABLE PharmacyPersonel
	ADD CONSTRAINT FK_PharmacyPersonel_empId FOREIGN KEY (empId) REFERENCES Employee (empId) ON UPDATE CASCADE ON DELETE CASCADE
GO
	ALTER TABLE Diagnosis 
	ADD CONSTRAINT FK_Diagnosis_Doctor_docId FOREIGN KEY (docId) REFERENCES Doctor(docId) ON UPDATE CASCADE ON DELETE SET NULL
GO	
	ALTER TABLE MedicineDispense
	ADD  CONSTRAINT FK_MedicineDispense_ppId FOREIGN KEY (ppId) REFERENCES PharmacyPersonel(ppId) ON UPDATE CASCADE ON DELETE SET NULL


--Step 2: Create the Employee_Terminated_Archive table
GO
	SELECT *
	INTO Employee_Terminated_Archive
	FROM Employee
	WHERE 1 = 0

	SELECT * FROM Employee_Terminated_Archive

--Step3: Create the trigger that inserts the deleted employee data into Employee_Terminated_Archive table 
--by checking the role of the employee
GO
	DROP TRIGGER IF EXISTS trMsg_Archive_Deleted_Employee
GO
	CREATE TRIGGER trMsg_Archive_Deleted_Employee
	ON Employee
	AFTER DELETE
	AS
		BEGIN
			INSERT INTO Employee_Terminated_Archive
			SELECT *
			FROM deleted
		END

--Now test the trigger at work  by deleting an Employee - employee data will be archived, but 
--the employee role wont be set as expected
GO
	DELETE Employee
	WHERE empId = 'EMP29'

	SELECT * FROM Employee_Terminated_Archive
	SELECT * FROM Employee

--EXERCISE - TRIGGER # 19 (*)
--Modify the above trigger and archieve table to include the role of the employee if he/she is Doctor 
--or PharmacyPersonel, and the date the employee was terminated.
GO
   ALTER TABLE Employee_Terminated_Archive
   ADD [role] VARCHAR(20), 
       terminatedDate DATE DEFAULT GETDATE()

--Step3: Create the trigger that inserts the deleted employee data into Employee_Terminated_Archive table by checking the role of the employee
GO
	ALTER TRIGGER trMsg_Archive_Deleted_Employee
	ON Employee
	AFTER DELETE
	AS
		BEGIN
			DECLARE @Doctor CHAR(5);
			DECLARE @PharmacyPersonel CHAR(5);

			SELECT @Doctor = dr.empId
			FROM Doctor dr			
			WHERE dr.empId IN (SELECT empId FROM deleted);
			
			SELECT @PharmacyPersonel = PP.empId
			FROM PharmacyPersonel PP
			WHERE PP.empId IN (SELECT empId FROM deleted);

			INSERT INTO Employee_Terminated_Archive
			SELECT d.*, GETDATE(), CASE 
									  WHEN @Doctor = d.empId THEN 'Doctor'
						              WHEN @PharmacyPersonel = d.empId THEN 'Pharmacy Personel'
									  ELSE 'Unknown'
								   END AS ROLES
			FROM deleted d;
		END

--Now test the trigger at work  by deleting an Employee - employee data will be archived, but 
--the employee role wont be set as expected
GO
	DELETE Employee
	WHERE empId = 'EMP23'

	SELECT * FROM Employee
	SELECT * FROM Employee_Terminated_Archive

--EXAMPLE - TRIGGER (Instead of Delete Trigger )
--The above requirement can better be fulfilled within INSTEAD OF DELETE trigger as follows:
--DROP TRIGGER trg_Instead_Of_Delete_Employee_Termination 
GO
	DROP TRIGGER IF EXISTS trg_Instead_Of_Delete_Employee_Termination
GO
	CREATE TRIGGER trg_Instead_Of_Delete_Employee_Termination
	ON Employee_Terminated_Archive
	INSTEAD OF DELETE
	AS
		BEGIN
			DECLARE @Doctor CHAR(5);
			DECLARE @PharmacyPersonel CHAR(5);

			SELECT @Doctor = dr.empId
			FROM Doctor dr
			WHERE dr.empId IN (SELECT empId FROM deleted);
			
			SELECT @PharmacyPersonel = PP.empId
			FROM PharmacyPersonel PP
			WHERE PP.empId IN (SELECT empId FROM deleted);

			INSERT INTO Employee_Terminated_Archive
			SELECT d.empId,d.empFName,d.empLName,d.SSN,d.DoB,d.gender,d.salary,d.employedDate,d.strAddress,d.apt,d.city,
                   d.[state],d.zipCode,d.phoneNo,d.email,d.empType,d.department, 
				   GETDATE(), [role] = CASE 
										  WHEN @Doctor = d.empId THEN 'Doctor'
										  WHEN @PharmacyPersonel = d.empId THEN 'Pharmacy Personel'
										  ELSE 'Unknown'
									   END
			FROM deleted d;
		END

--Now test deleting an employee that is a 1) Doctor 2) PharmacyPersonel and 3)any other employee
GO
	DELETE Employee
	WHERE empId = 'EMP08'

	SELECT * FROM Doctor  -- EMP10 is a Doctor
	SELECT * FROM PharmacyPersonel -- EMP08 is Pharmacy Personel
	SELECT * FROM Employee
	SELECT * FROM Employee_Terminated_Archive


--EXERCISE - TRIGGER # 20 ( Instead of Delete Trigger ) (*)
--Create an Instead of delete trigger on Diagnosis table, that simply prints a warning 'You can't delete Diagnosis data!'
GO
	DROP TRIGGER IF EXISTS trMsg_Revoke_Delete_On_Diagnosis_Table
GO
	CREATE TRIGGER trMsg_Revoke_Delete_On_Diagnosis_Table
	ON Diagnosis
	INSTEAD OF DELETE
	AS
		BEGIN
			PRINT 'WARNING: You can not delete Diagnosis data!'
		END
--INITIATING DELETE
GO
	DELETE Diagnosis

--CHECK THAT IF THEY ARE THERE
	SELECT * FROM Diagnosis

/*=================  THE BELOW QUESTION IS REPEATED ====================================== 
--EXERCISE - TRIGGER # 19 - TRIGGER # 06  ( AFTER UPDATE )
--Create the following audit table, RentalCust_Update_Audit, and code a trigger that 
--can maintains/captures the updated record/s of RentalCust table. 
*/

--EXERCISE - TRIGGER # 21 ( Using triggers to insert data into multiple tables )
--Inserting data into a view that is created from multiple base tables
--First create the view - e.g. a view on Doctor and Employee tables ( vw_DoctorEmployee )
GO
	DROP VIEW IF EXISTS vw_DoctorEmployee;
GO
	CREATE VIEW vw_DoctorEmployee
	AS
		SELECT E.empId,	E.empFName,E.empLName,E.SSN,E.DoB,E.gender,E.salary,E.employedDate,E.strAddress,E.apt,E.city,
		       E.[state],E.zipCode,E.phoneNo,E.email,E.empType,E.department,
	           D.docId,D.licenseNo,D.licenseDate,D.[rank],D.specialization
		FROM Employee E
		JOIN Doctor D ON D.empId = E.empId

-- There are times where we want to insert data into tables through views,
-- Assume that we hired a doctor (an employee) having all the employee and doctor information; 
-- for this we want to insert data into both tables through the view vw_DoctorEmployee
-- But this will not go as intended, because of multiple base tables.... see below
GO
	
	INSERT INTO vw_DoctorEmployee 
	VALUES ('EMP29', 'Joshua', 'Guru', '854-25-5874', '2081-10-02','M',90000,'2022-02-18','209 smu st', NULL,'SilverSpring', 'MD','90254', '(240) 814-5789', 'josh@hotmail.com','P','HN02','MD10', 'TRS-21-9578','2020-11-9', 'Junior','Internal Disease');

--Testing if it can insert into the view
GO
	CREATE TRIGGER trMsg_InsertInto_Multiple_Table
	ON vw_DoctorEmployee
	INSTEAD OF INSERT
	AS
		BEGIN
			SELECT * FROM inserted
		END
--Above insert statement will error out as follows
--Error: 
--Msg 4405, Level 16, State 1, Line 1763
--View or function 'vw_DoctorEmployee' is not updatable because the modification affects multiple base tables.

--Now create an instead of insert trigger on the view vw_DoctorEmployee -> that takes the data and inserts into 
--the tables individually
GO
	DROP TRIGGER IF EXISTS trMsg_InsertInto_Multiple_Table;
GO
	CREATE TRIGGER trMsg_InsertInto_Multiple_Table
	ON vw_DoctorEmployee
	INSTEAD OF INSERT
	AS
		BEGIN
			DECLARE @EMPID CHAR(5);
			SELECT @EMPID = i.empId
			FROM inserted i;
			 
			INSERT INTO Employee(empId,empFName,empLName,SSN,DoB,gender,salary,employedDate,strAddress,
				                 apt,city,[state], zipCode,phoneNo,email,empType,department)
			SELECT i.empId,i.empFName,i.empLName,i.SSN,i.DoB,i.gender,i.salary,i.employedDate,i.strAddress,
				   i.apt,i.city,i.[state], i.zipCode,i.phoneNo,i.email,i.empType,i.department
			FROM inserted i;

			INSERT INTO Doctor(empId, docId,licenseNo,licenseDate,[rank],specialization)
			SELECT @EMPID, i.docId,i.licenseNo,i.licenseDate,i.[rank],i.specialization
			FROM inserted i;
		END
		
-- Now retry the above insert statement
GO
	INSERT INTO vw_DoctorEmployee 
	VALUES ('EMP29', 'Joshua', 'Guru', '854-25-5874', '2081-10-02','M',90000,'2022-02-18','209 smu st', NULL,'SilverSpring', 'MD','90254', '(240) 814-5789', 'josh@hotmail.com','P','HN02','MD10', 'TRS-21-9578','2020-11-9', 'Junior','Internal Disease');
	
	SELECT * FROM vw_DoctorEmployee;
	
	SELECT * FROM Doctor;
	
	SELECT * FROM Employee;
  ```

