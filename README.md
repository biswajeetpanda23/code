Step 1 — Delete old project

Go to:

SEGW

Find:

ZCURRENCY_ODATA_SRV

Delete the project.

Then create a new project.

Step 2 — Create new SEGW project

In SEGW:

Create Project

Use:

Project Name : ZCURRENCY_ODATA
Description   : Currency Conversion OData
Project Type  : Default

Save in your package/request.

Step 3 — Create Entity Type

Right-click:

Data Model
   → Entity Types
   → Create

Name:

CurrencyConversion
Step 4 — Add properties

Create these properties:

Property	Type	Key
ID	Edm.String	✅
Amount	Edm.Decimal	
ForeignCurrency	Edm.String	
LocalCurrency	Edm.String	
ConvertedAmount	Edm.Decimal	

Set:

ID = Key
Step 5 — Create Entity Set

Right-click:

Entity Types
→ CurrencyConversion
→ Create Entity Set

Entity Set:

CurrencyConversionSet
Step 6 — Generate Runtime Objects

Right-click the project:

ZCURRENCY_ODATA
→ Generate Runtime Objects

You should get classes similar to:

ZCL_ZCURRENCY_ODATA_MPC
ZCL_ZCURRENCY_ODATA_DPC
ZCL_ZCURRENCY_ODATA_MPC_EXT
ZCL_ZCURRENCY_ODATA_DPC_EXT
Step 7 — Go to DPC_EXT

Open:

ZCL_ZCURRENCY_ODATA_DPC_EXT

Go to:

Methods

Find:

CURRENCYCONVERSION_CREATE_ENTITY

Redefine it.

Step 8 — Put this code

First use:

METHOD currencyconversion_create_entity.

  DATA: ls_input TYPE zcl_zcurrency_odata_mpc=>ts_currencyconversion,
        lv_local_amount TYPE bapicurr-bapicurr.

  io_data_provider->read_entry_data(
    IMPORTING
      es_data = ls_input
  ).

  CALL FUNCTION 'CONVERT_TO_LOCAL_CURRENCY'
    EXPORTING
      date             = sy-datum
      foreign_amount   = ls_input-amount
      foreign_currency = ls_input-foreigncurrency
      local_currency   = ls_input-localcurrency
      type_of_rate     = 'M'
    IMPORTING
      local_amount     = lv_local_amount.

  ls_input-convertedamount = lv_local_amount.

  er_entity = ls_input.

ENDMETHOD.
Step 9 — Activate

Activate:

ZCL_ZCURRENCY_ODATA_DPC_EXT

Then activate the whole project/runtime objects if required.

Step 10 — Register service

Go to:

/IWFND/MAINT_SERVICE

Click:

Add Service

Enter your Gateway system alias.

Search:

ZCURRENCY_ODATA_SRV

Select it → Add Selected Services.

Step 11 — Test metadata

Go to:

/IWFND/GW_CLIENT

GET:

/sap/opu/odata/SAP/ZCURRENCY_ODATA_SRV/$metadata

Execute.

You should see:

CurrencyConversion
CurrencyConversionSet
Amount
ForeignCurrency
LocalCurrency
ConvertedAmount
Step 12 — Test POST

First get CSRF token:

GET
/sap/opu/odata/SAP/ZCURRENCY_ODATA_SRV/

Header:

X-CSRF-Token = Fetch

Then POST:

/sap/opu/odata/SAP/ZCURRENCY_ODATA_SRV/CurrencyConversionSet

JSON:

{
  "ID": "1",
  "Amount": "100",
  "ForeignCurrency": "USD",
  "LocalCurrency": "INR"
}

Expected result:

Amount           = 100
ForeignCurrency  = USD
LocalCurrency    = INR
ConvertedAmount  = <SAP calculated INR amount>
Start only with Step 1 now.

Go to SEGW and delete the old ZCURRENCY_ODATA_SRV project. Then tell me "deleted", and I'll give you Step 2 only.

all steps

Yes. Let's do the complete project from scratch, simple and with code.

SAP OData Currency Conversion — Full Steps
1. Delete old project

Go to:

SEGW

Delete old:

ZCURRENCY_ODATA_SRV
2. Create new project

SEGW → Create Project

Enter:

Project Name : ZCURRENCY_ODATA
Description  : Currency Conversion OData
Project Type : Default

Save.

3. Create Entity Type

Right-click:

Data Model
 → Entity Types
 → Create

Enter:

Entity Type Name : CurrencyConversion
4. Create properties

Right-click CurrencyConversion → Properties → Create.

Property 1
Name       : ID
Type       : Edm.String
Max Length : 10
Key        : YES
Property 2
Name       : Amount
Type       : Edm.Decimal
Precision  : 15
Scale      : 2
Property 3
Name       : ForeignCurrency
Type       : Edm.String
Max Length : 5
Property 4
Name       : LocalCurrency
Type       : Edm.String
Max Length : 5
Property 5
Name       : ConvertedAmount
Type       : Edm.Decimal
Precision  : 15
Scale      : 2

You should have:

CurrencyConversion
│
├── ID                 KEY
├── Amount
├── ForeignCurrency
├── LocalCurrency
└── ConvertedAmount
5. Create Entity Set

Right-click:

Entity Sets
 → Create

Enter:

Entity Set Name : CurrencyConversionSet
Entity Type     : CurrencyConversion
6. Generate Runtime Objects

Right-click project:

ZCURRENCY_ODATA
 → Generate Runtime Objects

You should get:

ZCL_ZCURRENCY_ODATA_MPC
ZCL_ZCURRENCY_ODATA_MPC_EXT

ZCL_ZCURRENCY_ODATA_DPC
ZCL_ZCURRENCY_ODATA_DPC_EXT
7. Open DPC_EXT

Open:

ZCL_ZCURRENCY_ODATA_DPC_EXT

Go to:

Methods

Find:

CURRENCYCONVERSION_CREATE_ENTITY

Right-click → Redefine.

8. Put the code

Delete the generated RAISE EXCEPTION code.

Use:

METHOD currencyconversion_create_entity.

  DATA: ls_input        TYPE zcl_zcurrency_odata_mpc=>ts_currencyconversion,
        lv_local_amount TYPE bapicurr-bapicurr.

  "Read OData input
  io_data_provider->read_entry_data(
    IMPORTING
      es_data = ls_input
  ).

  "Convert currency
  CALL FUNCTION 'CONVERT_TO_LOCAL_CURRENCY'
    EXPORTING
      date             = sy-datum
      foreign_amount   = ls_input-amount
      foreign_currency = ls_input-foreigncurrency
      local_currency   = ls_input-localcurrency
      type_of_rate     = 'M'
    IMPORTING
      local_amount     = lv_local_amount.

  "Put converted value into response
  ls_input-convertedamount = lv_local_amount.

  "Return response
  er_entity = ls_input.

ENDMETHOD.
9. Activate

Activate:

ZCL_ZCURRENCY_ODATA_DPC_EXT

Also activate:

ZCL_ZCURRENCY_ODATA_MPC_EXT

and the project if required.

10. Test Metadata

Go to:

/IWFND/GW_CLIENT

Enter:

/sap/opu/odata/SAP/ZCURRENCY_ODATA_SRV/$metadata

Select:

GET

Execute.

You should see:

CurrencyConversion
CurrencyConversionSet
ID
Amount
ForeignCurrency
LocalCurrency
ConvertedAmount
11. Register the service

Go to:

/IWFND/MAINT_SERVICE

Click:

Add Service

Enter your system alias.

Search:

ZCURRENCY_ODATA_SRV

Select it.

Click:

Add Selected Services

Choose package/request if SAP asks.

12. Test Entity Set

Go back to:

/IWFND/GW_CLIENT

Use:

GET

URI:

/sap/opu/odata/SAP/ZCURRENCY_ODATA_SRV/CurrencyConversionSet

Execute.

Initially it may return an empty result:

<feed>
</feed>

That's okay.

13. Get CSRF Token

In Gateway Client:

Method
GET
URI
/sap/opu/odata/SAP/ZCURRENCY_ODATA_SRV/
Request Header
X-CSRF-Token    Fetch

Execute.

Then open HTTP Header on the response.

You should see:

x-csrf-token    <long token>
14. POST the currency data

Change method:

POST

URI:

/sap/opu/odata/SAP/ZCURRENCY_ODATA_SRV/CurrencyConversionSet

Add:

Content-Type    application/json
Accept          application/json
X-CSRF-Token    <your generated token>
Body
{
  "ID": "1",
  "Amount": "100",
  "ForeignCurrency": "USD",
  "LocalCurrency": "INR"
}

Execute.

15. What happens internally
POST
   ↓
CurrencyConversionSet
   ↓
CURRENCYCONVERSION_CREATE_ENTITY
   ↓
READ_ENTRY_DATA
   ↓
Amount = 100
ForeignCurrency = USD
LocalCurrency = INR
   ↓
CONVERT_TO_LOCAL_CURRENCY
   ↓
lv_local_amount
   ↓
ConvertedAmount
   ↓
er_entity

Expected response:

{
  "ID": "1",
  "Amount": "100.00",
  "ForeignCurrency": "USD",
  "LocalCurrency": "INR",
  "ConvertedAmount": "...."
}

The exact converted value depends on the exchange rate maintained in your SAP system.

16. If POST gives CSRF error

Do this:

GET
   ↓
X-CSRF-Token = Fetch
   ↓
Execute
   ↓
Copy returned token
   ↓
POST
   ↓
X-CSRF-Token = copied token

In Gateway Client, Use as Request can also help transfer the response information.

17. If conversion gives NO_RATE_FOUND

Check:

OB08

There must be an exchange rate for:

USD → INR

for the relevant date.

Final project structure
ZCURRENCY_ODATA
│
├── Data Model
│   ├── Entity Types
│   │   └── CurrencyConversion
│   │
│   └── Entity Sets
│       └── CurrencyConversionSet
│
├── MPC_EXT
│
└── DPC_EXT
    │
    └── CURRENCYCONVERSION_CREATE_ENTITY
             │
             └── CONVERT_TO_LOCAL_CURRENCY

This is enough for your sir's task: implement CONVERT_TO_LOCAL_CURRENCY in OData.
