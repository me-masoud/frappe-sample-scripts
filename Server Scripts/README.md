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

## Set value of name1 from another field value (Before Save)

This code, fetches data from another doctype and sets the name1 field from that data
```python

# get_value will get only value of one field in the doctype 
brand = frappe.db.get_value("Def Car Model", doc.car_model, "brand")

# With this line you can change the name1 field to whatever you want
doc.name1 = brand + ' ' + doc.car_model

```

## Portal Pages
make portal pages private (login required):

add this code to custom_portal_name.py under get_context() function
```python
  if frappe.session.user == "Guest":
        frappe.throw("You need to be logged in to access this page", frappe.PermissionError)
```

sample : 
```python
import frappe

def get_context(context):
    if frappe.session.user == "Guest":
        frappe.throw("You need to be logged in to access this page", frappe.PermissionError)

```

## redirect to absolute url
```python
frappe.redirect("https://google.com")
```
sample : 
```python
import frappe

def get_context(context):
    frappe.redirect("https://masoud.me")
```

## Working with Database : 

use : frappe.db
after your changes use commit 

for example: 

```python

frappe.db.set_value()
frappe.db.commit()
```

## Working with Utilities in server script and time
```python
thirty_days_later_date = frappe.utils.add_to_date(frappe.utils.now(), days=30)

gallery = frappe.get_doc({
    'doctype': 'User Remaining Subscription plan',
    'user': doc.name,
    'expire_date':thirty_days_later_date
})

gallery.insert()
```

## Share document with others (Auto sharing )
I could not do this with client script or server script 
I did it with writing code directly into python controller

```python
from frappe.share import add
frappe.share.add("User Remaining Subscription plan", "autogallery@gmail.com-2022-11-08", "work.masoudhosseini@gmail.comaa", write=1, share=1)
```

## Try Cache in python (Frappe )
```python
	try:
		user = frappe.session.user
		bank = frappe.get_single('Zarrinpal Gateway')
		order = frappe.get_last_doc(bank.order_doctype_name, filters={"user": user})
		context.amount = order.final_price
	except frappe.DoesNotExistError:
		frappe.throw("You have no active payment request", frappe.PermissionError)
```


# Workflow
## workflow action
work flow event in client script : use : "after_workflow_action"
```javascript
frappe.ui.form.on("Order", {
    after_workflow_action: function(frm) {
        if (frm.workflow_state == "Submitted"){
        branch = frappe.db.get_last_do('Branch' , frm.doc.branch)
        frappe.call({
		    method:"frappe.share.add",
		    args:{
		        doctype:"Order",
	            name: frm.docname,
	            user: branch.branch_director,
	            read:1,
	            write:1,
	            share:0,
	            everyone:0
		    },
		    callback: function (r) {
				 frappe.msgprint("XXXXX");
			}
		});
		}
    }
    })

```