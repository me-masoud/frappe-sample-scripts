# frappe-sample-SERVER-scripts

## Fill a child table from server script in frappe

```python
# get order
order = frappe.get_doc('Order', doc.order)

# get payments 
payments = frappe.get_list('Payment' ,filters={'order': doc.order},
fields=[
    'amount',
    'payment_type' ,
    'transaction_date' ,
    'source_account_name',
    'status'
    ])

# delete child table items 
order.payments_list = []

# Fill child  tables 
for item in payments:
    order.append('payments_list', {
    'amount': item.amount,
    'payment_type' : item.payment_type, 
    'transaction_date' : item.transaction_date,
    'source_account_name' : item.source_account_name,
    'status': item.status 
    })

# save order 
order.save()

``` 