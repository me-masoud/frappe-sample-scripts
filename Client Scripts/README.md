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
## Calculate total price per each row
```javascript
frappe.ui.form.on("Order Item CT", {
    unit_price(frm) {
        calculate_total_price_per_item(frm)
    },
    count(frm) {
       calculate_total_price_per_item(frm)
    }

});

function calculate_total_price_per_item(frm){
      frm.doc.order_items.forEach(order_item => {
            order_item.total = order_item.count * order_item.item_unit_price;
            frm.refresh();
        });
}
```
## Use this to get fetch value from a specific doctype : 
 ```get_value```
 
 ## Fetching child tables
 The following function will allow you to copy the values from a child table and paste them into another.
 ```javascript
frappe.ui.form.on("[TARGETDOCTYPE]", {
    "[TRIGGER]": function(frm) {
        frappe.model.with_doc("[SOURCEDOCTYPE]", frm.doc.[TRIGGER], function() {
            var tabletransfer= frappe.model.get_doc("[SOURCEDOCTYPE]", frm.doc.[TRIGGER])
            $.each(tabletransfer.[SOURCECHILDTABLE], function(index, row){
                var d = frm.add_child("[TARGETCHILDTABLE]");
                d.[TARGETFIELD1] = row.[SOURCEFIELD1];
                d.[TARGETFIELD2] = row.[SOURCEFIELD2];
                frm.refresh_field("[TARGETCHILDTABLE]");
            });
        });
    }
});

```


   - [TARGETDOCTYPE] - The doctype that is being worked in.
        - [TARGETCHILDTABLE] - The table to be populated. This value is the name of the table field in the parent doctype. 
        - [TARGETFIELD] - The field name in the target child table.
   - [SOURCEDOCTYPE] - The doctype that contains the information to be pulled
        - [SOURCECHILDTABLE] - The table to pull information from. This value is the name of the table field in the parent doctype. *[SOURCEFIELD] - The field name to pull data from within the table.
   - [TRIGGER] - What causes the function to activate.

## Hide Add row button for type Table (Child table)

```javascript
frappe.ui.form.on('Sale Order', {
	setup(frm) {
		frm.get_field('sale_order_added_cost').grid.cannot_add_rows = true;
	}
})
```

## Fill select field type 

```javascript
frm.set_df_property('time_shedules_select', 'options', ['option a', 'option b']);
    frm.refresh_field('time_shedules_select');
```

## Get from child table and fill into a select box

```javascript
    // get week day 
    let day = new Date(frm.doc.delivery_date);
    let week_day = day.getDay()
    let week_day_name = week_days[week_day]


     frappe.model.with_doc('Def Delivery Method', frm.doc.delivery_method).then(({time_schedules}) => {
         const durations = []
         
        time_schedules.forEach((time_schedule) => {
            if(time_schedule.week_days === week_day_name) {
                durations.push(time_schedule.week_days)
            }
        })
        frm.set_df_property('time_shedules_select', 'options', durations);
     })
     
    frm.refresh_field('time_shedules_select');
``` 

## Throw error message in client script 

```javascript
frappe.throw(' your message ')
```

### Print Message in dialog
```javascript
        frappe.msgprint('Your message 🎉 ');
```

message and throw difference:
message will not terminate process but throw does.

## Add filter for linked document in another doctype

```javascript
frappe.ui.form.on("Bank Reconciliation", "onload", function(frm) {
    frm.set_query("bank_account", function() {
        return {
            "filters": {
                "account_type": "Bank",
                "group_or_ledger": "Ledger"
            }
        };
    });
});
```

## Child table event (How to call an event on parent doctype)

```javascript
// this code is located inside `todo.js`

frappe.ui.form.on('Dynamic Link', { // The child table is defined in a DoctType called "Dynamic Link"
    links_add(frm, cdt, cdn) { // "links" is the name of the table field in ToDo, "_add" is the event
        // frm: current ToDo form
        // cdt: child DocType 'Dynamic Link'
        // cdn: child docname (something like 'a6dfk76')
        // cdt and cdn are useful for identifying which row triggered this event

        frappe.msgprint('A row has been added to the links table 🎉 ');
    }
});
```

## Updation of Total field based on change in Child table
I have a Doctype (Order) and inside of that I have a field (order_items) with type "table",
Now I need to call a function for Order on every update on this child table(order_items).

```javascript
frappe.ui.form.on('Order',  {
    refresh(frm, cdt,cdn){
        var d = locals[cdt][cdn];
        d.order_items.forEach(function(item){
        calculate_final_price(frm) // my function
    });
    }
})
```

## Call event on adding or removing row in child table 
official document for this issue : 
<a href="https://frappeframework.com/docs/v14/user/en/api/form#child-table-events" target="_blank">Click Here</a>
```javascript
frappe.ui.form.on('Order Item CT', { 
    order_items_add(frm, cdt, cdn) { 
        calculate_final_price(frm)
    },
    order_items_remove(frm, cdt, cdn) { 
        calculate_final_price(frm)
    }
});
```

## Change doc filed and properties of a field in document
Official Document : <a href="https://frappeframework.com/docs/v14/user/en/api/form#frmset_df_property">Click Here</a>
```javascript
// change the fieldtype of description field to Text
frm.set_df_property('description', 'fieldtype', 'Text');

// set the options of the status field to only be [Open, Closed]
frm.set_df_property('status', 'options', ['Open', 'Closed'])

// set a field as mandatory
frm.set_df_property('title', 'reqd', 1)

// set a field as read only
frm.set_df_property('status', 'read_only', 1)
``` 

## How to Call backend function (server script) in client script 
Official Document : <a href="https://frappeframework.com/docs/v14/user/en/api/form#frmcall">Click Here</a>

```python
class ToDo(Document):
    @frappe.whitelist()
    def get_linked_doc(self, throw_if_missing=False):
        if not frappe.db.exists(self.reference_type, self.reference_name):
            if throw_if_missing:
                frappe.throw('Linked document not found')

        return frappe.get_doc(self.reference_type, self.reference_name)
```

```javascript
frm.call('get_linked_doc', { throw_if_missing: true })
    .then(r => {
        if (r.message) {
            let linked_doc = r.message;
            // do something with linked_doc
        }
    })
```

## Web Forms 
### Get Value in Client Script in Web Forms
```javascript
value = frappe.web_form.get_value([fieldname]);
```

### Visibility depends on script in field type

```javascript
eval:doc.field_name === field_value
```

# Doctype Script
## in field configuration -> Display depends on

```javascript
eval:doc.type == 'xxx'
```  