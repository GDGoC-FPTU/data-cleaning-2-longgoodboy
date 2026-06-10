# Testing Guide: Data Cleaning Lab

This guide helps you verify that the lab passes all required cases.

## 1. Run the main script

From the project folder, run:

```bash
python cleaning.py
```

Expected terminal output:

```text
Successfully sanitized data. Output saved to sanitized_sample.json
Original records: 6
Sanitized records: 3
```

## 2. Check the generated file

Open `sanitized_sample.json` and verify:

- The file contains exactly 3 records.
- The remaining IDs are `A001`, `A002`, and `A005`.
- No record contains the `name` field.
- Emails are masked:
  - `v***@gmail.com`
  - `b***@vinuni.edu.vn`
  - `s***@xyz.com`
- Duplicate record `A001` appears only once.
- Record `A003` is removed because `price` is `99999`.
- Record `A004` is removed because `price` is `-50`.

Expected `sanitized_sample.json`:

```json
[
    {
        "id": "A001",
        "email": "v***@gmail.com",
        "product": "Laptop OLED",
        "price": 2500,
        "category": "elec"
    },
    {
        "id": "A002",
        "email": "b***@vinuni.edu.vn",
        "product": "Ergonomic Chair",
        "price": 300,
        "category": "furniture"
    },
    {
        "id": "A005",
        "email": "s***@xyz.com",
        "product": "Smartwatch",
        "price": 400,
        "category": ""
    }
]
```

## 3. Run an automated pass/fail check

Run this command from the project folder:

```bash
python -c "import json; from cleaning import mask_email; data=json.load(open('sanitized_sample.json', encoding='utf-8')); assert len(data)==3; assert [item['id'] for item in data]==['A001','A002','A005']; assert all('name' not in item for item in data); assert [item['email'] for item in data]==['v***@gmail.com','b***@vinuni.edu.vn','s***@xyz.com']; assert all(0 <= item['price'] <= 5000 for item in data); assert mask_email('vana@gmail.com')=='v***@gmail.com'; assert mask_email('invalid-email')=='invalid-email'; print('PASS: all lab test cases passed')"
```

Expected output:

```text
PASS: all lab test cases passed
```

If you see that message, the lab passes the required cases.

## 4. Optional: Full clean re-test

If you want to test from scratch, delete `sanitized_sample.json`, then run:

```bash
python cleaning.py
python -c "import json; data=json.load(open('sanitized_sample.json', encoding='utf-8')); assert len(data)==3; assert [item['id'] for item in data]==['A001','A002','A005']; assert all('name' not in item for item in data); assert all(0 <= item['price'] <= 5000 for item in data); print('PASS: regenerated sanitized_sample.json is valid')"
```

Expected output:

```text
Successfully sanitized data. Output saved to sanitized_sample.json
Original records: 6
Sanitized records: 3
PASS: regenerated sanitized_sample.json is valid
```

## 5. Discussion question answer

Use this answer if the lab requires the ETL vs ELT explanation:

```text
We used ETL for PII masking because private information should be removed or masked before the data is stored. If we used ELT, the raw PII would first be loaded into the database or vector store, which creates a security and privacy risk. ETL reduces exposure by ensuring only sanitized data enters the AI system.
```
