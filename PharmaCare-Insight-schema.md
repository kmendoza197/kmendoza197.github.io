# PharmaCare Insight Database Schema

* This is the code used to build the database `PharmaCare Insight` using SQLite.

``` sql
-- Represents the patients that have consumed a Phase IV drug developed by the pharmaceutical company
CREATE TABLE patients (
    "id" INTEGER NOT NULL,
    "first_name" TEXT NOT NULL,
    "middle_initial" TEXT,
    "last_name" TEXT NOT NULL,
    "sex" TEXT CHECK ("sex" IN ('female', 'male')) NOT NULL,
    "date_of_birth" NUMERIC NOT NULL,
    PRIMARY KEY ("id")
);

-- Represents the healthcare providers that are prescribing a Phase IV drug developed by the pharmaceutical company
CREATE TABLE healthcare_providers (
    "id" INTEGER NOT NULL,
    "first_name" TEXT NOT NULL,
    "last_name" TEXT NOT NULL,
    "specialty" TEXT NOT NULL,
    "practice_name" TEXT NOT NULL,
    PRIMARY KEY ("id")
);

-- Represents the Phase IV drugs developed by the pharmaceutical company
CREATE TABLE drugs (
    "id" INTEGER NOT NULL,
    "generic_name" TEXT NOT NULL,
    "brand_name" TEXT NOT NULL,
    "release_date" NUMERIC NOT NULL,
    "condition_treated" TEXT NOT NULL,
    PRIMARY KEY ("id")
);

-- Represents the Phase IV drug prescriptions from healthcare providers to patients
CREATE TABLE prescriptions (
    "id" INTEGER NOT NULL,
    "patient_id" INTEGER NOT NULL,
    "healthcare_provider_id" INTEGER NOT NULL,
    "drug_id" INTEGER NOT NULL,
    "date" NUMERIC NOT NULL,
    PRIMARY KEY ("id"),
    FOREIGN KEY "patient_id" REFERENCES "patients"("id"),
    FOREIGN KEY "drug_id" REFERENCES "drugs"("id"),
    FOREIGN KEY "healthcare_provider_id" REFERENCES "healthcare_providers"("id")
);

-- Represents any averse drug reactions that were experiences by patients
CREATE TABLE averse_drug_reactions (
    "id" INTEGER NOT NULL,
    "patient_id" INTEGER NOT NULL,
    "drug_id" INTEGER NOT NULL,
    "report_date" NUMERIC NOT NULL,
    "reaction_type" TEXT NOT NULL,
    "severity" TEXT CHECK("severity" IN ('mild', 'moderate', 'severe')),
    "outcome" TEXT CHECK("outcome" IN ('recovered', 'recovering', 'fatal')),
    PRIMARY KEY ("id"),
    FOREIGN KEY "patient_id" REFERENCES "patients"("id"),
    FOREIGN KEY "drug_id" REFERENCES "drugs"("id")
);

-- Represents the safety data sheets for the Phase IV drugs
CREATE TABLE safety_data_sheets (
    "id" INTEGER NOT NULL,
    "drug_id" INTEGER NOT NULL,
    "warnings_precautions" TEXT NOT NULL,
    "emergency_procedures" TEXT NOT NULL,
    PRIMARY KEY ("id"),
    FOREIGN KEY ("drug_id") REFERENCES "drugs"("id")
);

-- Create indexes for common searches
CREATE INDEX idx_prescriptions_patient_id ON prescriptions(patient_id);
CREATE INDEX idx_prescriptions_drug_id ON prescriptions(drug_id);
CREATE INDEX idx_drug_id ON averse_drug_reactions ("drug_id");
```
