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

## Change child table value base on their changes 
for example now we want to calculate every single row total price base on count and unit price: 

```javascript
frappe.ui.form.on("Order Product CT", {
    unit_price(frm, cdt, cdn) {
        frm.doc.order_products.forEach(order_product => {
            if(order_product.count !== 0) {
                if(order_product.name === cdn) {
                    order_product.total_price = order_product.count * order_product.unit_price;
                    frm.refresh();
                }
            }
        });
    },
    
    count(frm, cdt, cdn) {
        frm.doc.order_products.forEach(order_product => {
            if(order_product.unit_price !== 0) {
                if(order_product.name === cdn) {
                    order_product.total_price = order_product.count * order_product.unit_price;
                    frm.refresh();
                }
            }
        });
    }
});
```

### Use this to get fetch value from a specific doctype : 
 ```get_value```

