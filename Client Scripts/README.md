# frappe-sample-CLIENT-scripts

## Working with Lists
##### use ```frappe.db.get_list``` method Frappe (ERPNext):
```javascript
frappe.db.get_list('Purchase Order', {
	filters: {
		supplier: d.supplier,
	},
	fields: ['name'],
	limit: 500,
}).then(res => {
	console.log(res)
});
```

#
##### Fetch value from another doc type and set in another document In Frappe (ERPNext):
```javascript
frappe.ui.form.on('Sale Order', {
	product(frm) {
	    frappe.db.get_value('Trading Board', {"product":frm.doc.product},'sales_price')
	    .then((data) => {
	        var price = data.message.sales_price
	        frm.set_value('unit_price' , price)
	        frm.set_value('minimum_today_unit_price',price)
	    })
	}
})
```
