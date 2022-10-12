# frappe-sample-CLIENT-scripts

##Working with Lists
#####use ```frappe.db.get_list``` method:
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
