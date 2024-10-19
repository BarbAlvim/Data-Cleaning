-- Data cleaning

-- Remove Duplicates
-- Standardize the data (if have issues with spellings or things like that)
-- Null Values or blank values 
-- Remove Any Columns

-- Remove Duplicates

SELECT *
FROM layoffs;

SELECT *, 
row_number() over(partition by company, industry, total_laid_off,percentage_laid_off, 'date') AS row_num
FROM layoffs;

WITH duplicate_cte AS(
SELECT *, 
row_number() over(partition by company, industry, total_laid_off,percentage_laid_off, 'date', stage, country, funds_raised_millions) AS row_num
FROM layoffs
)
SELECT * 
FROM duplicate_cte
WHERE row_num > 1;

-- delete 
-- FROM duplicate_cte
-- WHERE row_num > 1    it does not work in this case

CREATE TABLE `layoffs1` (
  `company` text,
  `location` text,
  `industry` text,
  `total_laid_off` int DEFAULT NULL,
  `percentage_laid_off` text,
  `date` text,
  `stage` text,
  `country` text,
  `funds_raised_millions` int DEFAULT NULL,
  `row_num` INT
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;

SELECT *
FROM layoffs1;
 
 INSERT INTO layoffs1
 SELECT *, 
row_number() over(partition by company, industry, total_laid_off,percentage_laid_off, 'date', stage, country, funds_raised_millions) AS row_num
FROM layoffs;

-- if you want to check a specific colum do this
 -- SELECT *
--  FROM layoffs
--  WHERE company = 'oda'; 
 
 SELECT * 
FROM layoffs1
WHERE row_num = 2;

 
DELETE 
FROM layoffs1
WHERE row_num = 2;

 SELECT * 
FROM layoffs1
WHERE row_num = 1 ;

-- Remove Duplicates
-- Standardize the data (if have issues with spellings or things like that)
-- Null Values or blank values 
-- Remove Any Columns

-- CREATE TABLE layoffs_stagings
-- LIKE layoffs; 

-- INSERT layoffs_stagings
-- SELECT *
-- FROM layoffs;

-- Standardize the data (if have issues with spellings or things like that)

SELECT company, TRIM(company) -- trim remove the whit space in the end
FROM layoffs1;

UPDATE layoff1
set company = TRIM(company);

SELECT DISTINCT country, trim(TRAILING '.' FROM country) -- revove the dot from the end of country
FROM layoffs1
ORDER BY 1;

UPDATE layoffs1
SET country = trim(TRAILING '.' FROM country)
WHERE country LIKE 'United States%' ; 

SELECT `date`,
str_to_date(`date`,' %m/%d/%y' )
FROM layoffs1;

UPDATE layoffs1
set `date` = str_to_date(`date`,' %m/%d/%y' );

-- SELECT *
-- FROM layoffs1
-- WHERE industry LIKE 'Crypto%'; 

-- UPDATE layoffs1
-- set industry = 'Crypto'
-- WHERE industry LIKE 'Crypto%';

-- UPDATE layoff1
-- set company = TRIM(company)

-- SELECT *
-- FROM layoffs1
-- WHERE total_laid_off is NULL
-- AND percentage_laid_off is null;

SELECT *
FROM layoffs1
WHERE company = 'Airbnb';

SELECT *
FROM layoffs1 t1
JOIN layoffs1 t2
	ON t1.company = t2.company
    AND t1.location = t2.location
WHERE (t1.industry IS NULL OR t1.industry = '')
AND t2.industry IS NOT NULL;

update layoffs1 t1
JOIN layoffs1 t2
	ON t1.company = t2.company
SET t1.industry = t2.industry
WHERE t1.industry IS NULL 
AND t2.industry IS NOT NULL;


select *
FROM layoffs1;

ALTER TABLE layoffs1
DROP COLUMN row_num
