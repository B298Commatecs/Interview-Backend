# 📄 ReceiptApi
### 🌲 Database schema
![alt text](https://lh3.googleusercontent.com/d/1TQqtUo5g2tOq-E64jjyGHndlIbLj0lVn)
``` sql
Table receipt {
  id integer [primary key]
  linked integer
  client_key integer
  type varchar(24)
  total_sales decimal
  total_discount decimal
  total_tax decimal
  total_amount decimal
  created_at timestamp
}

Table discount {
  id integer
  item_data_key integer
  amount decimal
  description varchar(128)
  rate decimal
}

Table tax {
  id integer
  item_data_key integer
  amount decimal
  tax_type varchar(24)
  rate decimal
}

Table item_data {
  id integer [primary key]
  receipt_key integer
  quantity decimal
  price decimal
}

Table client {
  id integer [primary key]
  name varchar(48)
  phone varchar(24)
  total_purchase decimal
  created_at timestamp
}

Ref: item_data.receipt_key > receipt.id
Ref: discount.item_data_key > item_data.id
Ref: tax.item_data_key > item_data.id
Ref: receipt.linked > receipt.id
Ref: receipt.client_key > client.id
```
### 🅰️ API
- Receipt
    - POST AddReceipt (string type, array item_data, optional int client, optional int linked) return receipt.
    - GET GetReceipt (int id) return receipt
    - GET GetReceipts (string type, int page) return array[10] receipt. 'returns 10 receipts per page'
    - DELETE DeleteReceipt (int id).
- Client
    - POST AddClient (string name, string phone) return client.
    - GET GetClient (string search_text) return client.
    - PUT UpdateClient (optional string name, optional string phone) return client.
    - DELETE DeleteClient (int id).
- Authentication
    - Use whatever you like and make it simple
- Extra APIs (feel free to add any extra api)


### 🧮 Calculation
- **if item_data.discount.rate > 0 then** `item_data[*].discount[*].amount = item_data[*].price * (item_data[*].discount[*].rate * 100)`
- **if item_data.tax.rate > 0 then** `item_data[*].tax[*].amount = (item_data[*].price - Sum(item_data[*].discount[*].amount)) * (item_data[*].tax[*].rate * 100)`
- `receipt.total_sales = Sum(receipt.item_data[*].amount)`
- `receipt.total_discount = Sum(receipt.item_data[*].discount[*].amount)`
- `receipt.total_tax = Sum(receipt.item_data[*].tax[*].amount)`
- `receipt.total_amount = receipt.total_sales - receipt.total_discount + receipt.total_tax`

### 📩 Submit Receipt
There is two main types of receipts `['return', 'sales']` when user submit new receipt he should specify the type of it (only when submitting)
- Sales Receipt
    - Sales receipt can't be linked with other receipts
    - When client is linked with the receipt add the `total_amount` to the `client.total_purchase`
- Return Receipt
    - Must be linked with the same client as the sales receipt
    - Must be linked with sales receipt (include the sales receipt id in the linked field)
    - User can return more than one time as long as the sales receipt have enough quantity 
    - When client is linked with the receipt subtract the `total_amount` from the `client.total_purchase`

### ℹ Info
- Use dotnet 7.0 or higher
- Use Mssql or sql for backend
- Don't use AutoMapper or any other alternative only manual mapping (for this task only)
- Using a custom Authentication System is a Plus
- Adding simple reporting system is a Plus

### 💡 Keep in mind
The target of this Task is measure the quality of the code not the time (take your time)👍

Clean and will commented code is a top priority, the fast code is useless when no one understands what the hell is going 😅

You don't have to make storage system we removed it from the task to save some time 😉

Feel free to dm me anytime for help or task explanation 🤓 

Keep an eye on the repository for any updates on the README
