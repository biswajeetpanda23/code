METHOD currencyconversi_create_entity.

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
      date             = ls_input-date
      foreign_amount   = ls_input-amount
      foreign_currency = ls_input-foreigncurrency
      local_currency   = ls_input-localcurrency
      type_of_rate     = 'M'
    IMPORTING
      local_amount     = lv_local_amount.

  "Put converted amount into response
  ls_input-localamount = lv_local_amount.

  "Return response to OData
  er_entity = ls_input.

ENDMETHOD.
