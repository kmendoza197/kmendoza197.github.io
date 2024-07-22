# PharmaCare Insight Common Queries

This file showcases common queries that can be implemented using the database architecture of `PharmaCare Insight`.

``` sql
-- What the brand names for the drugs that are being used to treat a specific condition?
SELECT "brand_name"
FROM "drugs"
WHERE "condition_treated" = 'Irritable Bowel Syndrome'
;

-- How many adverse effects have been recorded for this specific drug?
SELECT COUNT(*)
FROM "averse_drug_reactions"
WHERE "drug_id" = (
    SELECT "id"
    FROM "drugs"
    WHERE "brand_name" = 'Lyrica'
);

-- What are the brand names of top 5 drugs with the most averse effects?
SELECT "brand_name"
FROM "drugs"
WHERE "id" IN (
    SELECT "drug_id"
    FROM "averse_drug_reactions"
    GROUP BY "drug_id"
    ORDER BY COUNT(*) DESC
    LIMIT 5
);

-- What are the patients' names that reported having an specific reaction type to a drug that treated a certain condition?

SELECT "first_name", "middle_initial", "last_name"
FROM "patients"
JOIN "prescriptions" ON "patients"."id" = "prescription"."patient_id"
JOIN "drugs" ON "prescriptions"."drug_id" = "drugs"."id"
JOIN "averse_drug_reactions" ON "prescriptions"."drug_id" = "averse_drug_reactions"."drug_id" AND "prescriptions"."patient_id" = "averse_drug_reactions"."patient_id"
WHERE "condition_treated" = 'arthritis'
AND "reaction_type" = 'allergic reaction'
;

-- Updating when a patient has recovered from an averse drug reaction

UPDATE "averse_drug_reactions"
SET "outcome" = 'recovered'
WHERE "patient_id" = (
    SELECT "id"
    FROM "patients"
    WHERE "first_name" = 'Joseph' AND "middle_initial" = 'M' AND "last_name" = 'Clark')
;
```
