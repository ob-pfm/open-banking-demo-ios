# PFM SDK Backend Android

The Open Banking PFM SDK uses Client classes and with **Promises** to get responses from the Open Banking PFM API in an easier way and structured as data models.

# Table of contents

* [Users Client](#users-client)
* [Banks Client](#banks-client)
* [Categories Client](#categories-client)
* [Accounts Client](#accounts-client)
* [Transactions Client](#transactions-client)
* [Budgets Client](#budgets-client)
* [Bank Aggregation Client](#bank-aggregation-client)
* [Consents Client](#consents-client)
* [Credits Client](#credits-client)
* [Insights Client](#insights-client)
* [Helpers](#helpers)
* [Error Codes](#error-codes)

# Users Client

This client contains the available actions related to users. In order to successfully integrate the following functions, it is important to previously load the users explained in the installation guide.

How to use
----------

### Get User

Given a valid user ID, fetches the information of an user.

```swift
let usersService = OpenBankingPFMAPI.usersClient()
let userId = 123456

// Remote call
usersService.get(withID: userId) { (result:Result<OBUser>) in
    switch result {
    case .success(let result):
// Set the parsed user into your datasource
        self.dataSource = [result]
    case .failure(let error):
        print("Error: \(error.localizedDescription)")
    }
}
```

Output:

```swift
class OBUser: Codable {
    public var id: Int
    public var cpf: String
    public var dateCreated: Int
}
```

Possible Errors:

* [Error 400](#error-400)
* [Error 404](#error-404)
* [Error 500](#error-500)

# Banks Client

This client contains the available actions related to banks.

How to use
----------

### Available Banks

Returns the list of available banks to be able to add and manage movements, money and balance with the bank.

```swift
var banksService = OpenBankingPFMAPI.banksClient()
let userId = 123456

// Set the userId
banksService = banksService.userId(userId)

// Remote call
banksService.getAvailables { [weak self] (result: Result<OBBanksResponse<[OBBank]>>) in
    switch result {
    case .success(let result):
// Set the parsed banks into your datasource
        self?.dataSource = result.banks
    case .failure(let error):
        print("Error: \(error.localizedDescription)")
    }
}
```

Output:

```swift
class OBBanksResponse<T: Codable>: Codable {
    public let banks: [OBBank]?
}

class OBBank: Codable {
    public var name: String
    public var bankId: String
    public var financialEntityId: Int
    public var imagePath: String?
    public var isBankAggregation: Bool
}
```

Possible Errors:

* [Error 400](#error-400)
* [Error 401](#error-404)
* [Error 500](#error-500)

# Categories Client

This client contains the available actions related to the classification of transactions and budgets.

How to use
----------

### List Categories

Fetches a list of categories, sorted by ID in descending order

```swift
var categoriesService = OpenBankingPFMAPI.categoriesClient()
let userId = 123456
let cursor = nil

// Optional parameter
categoriesService = categoriesService.userId(userId)
// Optional parameter
categoriesService = categoriesService.cursor(cursor)

// Remote call
categoriesService.getList { (result: Result<OBBodyResponse<[OBCategory]>>) in
    switch result {
    case .success(let result):
// Set the parsed categories into your datasource
        self?.dataSource = result.data
    case .failure(let error):
        print("Error: \(error.localizedDescription)")
    }
}
```

The cursor param is optional. If a cursor is specified, the list starts with the item that has that ID. If a user ID is specified, both system and user categories are fetched. If a user ID is not specified, only system categories are fetched.

Output:

```swift
class OBCategory: Codable {
    public let id: Int?
    public let userId: Int?
    public var name: String
    public var color: String?
    public var parentCategoryId: Int?
    public var isUserCategory: Bool
    public var imagePath: String?
    public var dateCreated: Int?
}
```

Possible Errors:

* [Error 400](#error-400)
* [Error 404](#error-404)
* [Error 500](#error-500)

### Get Category

Given a valid category ID, fetches the information of a category.

```swift
var categoriesService = OpenBankingPFMAPI.categoriesClient()
let categoryId = 2

// Remote call
categoriesService.get(withID: id) { [weak self] (result: Result<OBCategory>)in
    switch result {
    case .success(let result):
// Set the parsed category into your datasource
        self?.dataSource = [result]
    case .failure(let error):
        print("Error: \(error.localizedDescription)")
    }
}
```

Output:

```swift
class OBCategory: Codable {
    public let id: Int?
    public let userId: Int?
    public var name: String
    public var color: String?
    public var parentCategoryId: Int?
    public var isUserCategory: Bool
    public var imagePath: String?
    public var dateCreated: Int?
}
```

Possible Errors:

* [Error 400](#error-400)
* [Error 404](#error-404)
* [Error 500](#error-500)

### Create Category

Creates a category. If a user ID is not specified, the category is considered as a system category. If a parent category ID is specified, the category is considered a subcategory. 

```swift
var categoryService = OpenBankingPFMAPI.categoriesClient()

let newCategory = OBCategory(userId: 123456, //The ID of the user that owns the category
           name: "Category name", //The name of the category. It's required.
           color: "FFFFFF", //The color of the category
           parentCategoryId: 1 //The ID of the parent category that owns this subcategory
)

// Remote call
categoryService.create(newCategory) { (result: Result<OBCategory>) in
    switch result {
    case .success(let result):
        print(result)
    case .failure(let error):
        print("Error: \(error.localizedDescription)")
    }
}
```

Output:

```swift
class OBCategory: Codable {
    public let id: Int?
    public let userId: Int?
    public var name: String
    public var color: String?
    public var parentCategoryId: Int?
    public var isUserCategory: Bool
    public var imagePath: String?
    public var dateCreated: Int?
}
```

Possible Errors:

* [Error 400](#error-400)
* [Error 404](#error-404)
* [Error 500](#error-500)

### Update Category

Given a valid category id updates a category. You can pass an object with the properties to update ( `name`: _string_, `color`: _string_, `parentCategoryId`: \[_number_, nil] ).

```swift
var categoryService = OpenBankingPFMAPI.categoriesClient()

let editedCategory = OBCategory(userId: 123456,
           name: "Ball Sports", 
           color: "FFFFFF", 
           parentCategoryId: 1 
)

// Remote call
categoryService.edit(editedCategory) { (result: Result<OBCategory>) in
    switch result {
    case .success(let result):
        print(result)
    case .failure(let error):
        print("Error: \(error.localizedDescription)")
    }
}
```

Output:

```swift
class OBCategory: Codable {
    public let id: Int?
    public let userId: Int?
    public var name: String
    public var color: String?
    public var parentCategoryId: Int?
    public var isUserCategory: Bool
    public var imagePath: String?
    public var dateCreated: Int?
}
```

Possible Errors:

* [Error 400](#error-400)
* [Error 404](#error-404)
* [Error 500](#error-500)

### Delete Category

Given a valid category id deletes a category.

```swift
var categoryService = OpenBankingPFMAPI.categoriesClient()
let categoryId = 123456

// Remote call
categoriesService.delete(withID: id) { (result: Result<Bool>) in
    switch result {
    case .success(let result):
        print(result)
    case .failure(let error):
        print("Error: \(error.localizedDescription)")
    }
}
```

If the category was removed, the response will be returned as true.

Possible Errors:

* [Error 400](#error-400)
* [Error 404](#error-404)
* [Error 500](#error-500)

# Accounts Client

This client helps in managing users' bank accounts.

How to use
----------

### List Accounts

Fetches a list of accounts per user, sorted by ID in descending order.

```swift
var accountsService = OpenBankingPFMAPI.accountsClient()
let userId = 123456 // Required
let isBankAggregation = true
let cursor = 0

// Optional parameter
accountsService = accountsService.isBankAggregation(isBankAggregation)
// Optional parameter
accountsService = accountsService.cursor(cursor)

// Remote call
accountsService.userId(userId).getList { (result: Result<OBBodyResponse<[OBAccount]>>) in
    switch result {
    case .success(let result):
// Set the parsed accounts into your datasource
        self?.dataSource = result.data
    case .failure(let error):
        print("Error: \(error.localizedDescription)")
    }
}
```

The ID of the user that owns the accounts is required.

Output:

```swift
class OBAccount: Codable {
    let id: Int?
    let providerId: String?
    let financialEntityId: Int?
    var nature: String
    var name: String
    var number: String
    var balance: Double
    var chargeable: Bool
    let dateCreated: Int?
    let lastUpdated: Int?
    let userId: Int?
    let isBankAggregation: Bool?
}
```

Possible Errors:

* [Error 400](#error-400)
* [Error 404](#error-404)
* [Error 500](#error-500)

### Get Account

Given a valid account ID, fetches the information of an account.

```swift
let accountsService = OpenBankingPFMAPI.accountsClient()
let accountId = 320023867

// Remote call
accountsService.get(withID: id) { (result: Result<OBAccount>) in
    switch result {
    case .success(let result):
// Set the parsed account into your datasource
        self?.dataSource = [result]
    case .failure(let error):
        print("Error: \(error.localizedDescription)")
    }
}
```

Output:

```swift
class OBAccount: Codable {
    let id: Int?
    let providerId: String?
    let financialEntityId: Int?
    var nature: String
    var name: String
    var number: String
    var balance: Double
    var chargeable: Bool
    let dateCreated: Int?
    let lastUpdated: Int?
    let userId: Int?
    let isBankAggregation: Bool?
}
```

Possible Errors:

* [Error 400](#error-400)
* [Error 404](#error-404)
* [Error 500](#error-500)

### Create Account

Creates an account. A previously created user and a financial entity is required. You have to import the Account Payload Model to create a new one.

The possible values for nature property are "__Debit__" , "__Credit card__" , "__Credit__" , "__Investment__" , "__Mortgage__"

```swift
let accountsService = OpenBankingPFMAPI.accountsClient()

let newAccountData = OBAccount(financialEntityId: 743443,
          nature: "Debit",
          name: "Premium",
          number: "1111 1111 1111 1111",
          balance: 1000,
          chargeable: false,
          userId: 1115164)

// Remote call
accountsService.create(account) { (result: Result<OBAccount>) in
    switch result {
    case .success(let result):
// Set the parsed account into your datasource
        self?.datasource = [result]
    case .failure(let error):
        print("Error: \(error.localizedDescription)")
    }
}
```

Output:

```swift
class OBAccount: Codable {
    let id: Int?
    let providerId: String?
    let financialEntityId: Int?
    var nature: String
    var name: String
    var number: String
    var balance: Double
    var chargeable: Bool
    let dateCreated: Int?
    let lastUpdated: Int?
    let userId: Int?
    let isBankAggregation: Bool?
}
```

Possible Errors:

* [Error 400](#error-400)
* [Error 404](#error-404)
* [Error 500](#error-500)

### Update Account

Updates an account. You can pass an object with the properties to update ( `nature`: _string_, `name`: _string_, `number`: _string_, `balance`: _number_, `chargeable`: _boolean_ ).

```swift
let accountsService = OpenBankingPFMAPI.accountsClient()

// Using some existing account to update
account.name = "Gold"

// Remote call
accountsService.edit(account) { (result: Result<OBAccount>) in
    switch result {
    case .success(let result):
        print(result)
    case .failure(let error):
        print("Error: \(error.localizedDescription)")
    }
}
```

Output:

```swift
class OBAccount: Codable {
    let id: Int?
    let providerId: String?
    let financialEntityId: Int?
    var nature: String
    var name: String
    var number: String
    var balance: Double
    var chargeable: Bool
    let dateCreated: Int?
    let lastUpdated: Int?
    let userId: Int?
    let isBankAggregation: Bool?
}
```

Possible Errors:

* [Error 400](#error-400)
* [Error 404](#error-404)
* [Error 500](#error-500)

### Delete Account

Deletes an account and all its information, including transactions.

```
accountsClient
    .delete(accountId)
    .then((data) => console.log(data))
    .catch((error) => console.log(error));
```

Possible Errors:

* [Error 400](#error-400)
* [Error 404](#error-404)
* [Error 500](#error-500)

# Transactions Client

This client helps in the management of financial movements within an account.

How to use
----------

### List Transactions

Fetches a list of transactions per account. You can pass a filter options object as parameter if is not passed all transactions are listed.

```
const filterOptions = {
    categoryId: 12, //optional
    description: 'netflix', //optional
    charge: true, //optional
    minAmount: 0, //optional
    maxAmount: 1000, //optional
    dateFrom: 1587567125458, //optional
    dateTo: 1587567125458, //optional
    page: 0, //optional
    size: 100, //optional
    field: 'executionDate', //optional
    order: 'desc' //optional
}; // optional

transactionsClient
    .getList(accountId, filterOptions)
    .then((data) => console.log(data))
    .catch((error) => console.log(error));
```

Output:

```
[
    Transaction {
        id: 123,
        date: 1587567125458,
        charge: true,
        description: "UBER EATS",
        amount: 1234.56,
        categoryId: 123,
        dateCreated: 1587567125458,
        lastUpdated: 1587567125458,
        isBankAggregation: true
    },
    Transaction {
        id: 456,
        date: 1587567145458,
        charge: true,
        description: "RAPPI",
        amount: 1234.56,
        categoryId: 123,
        dateCreated: 1646259197099,
        lastUpdated: 1646259197099,
        isBankAggregation: true
    }
    ...
]
```

Possible Errors:

* [Error 400](#error-400)
* [Error 404](#error-404)
* [Error 500](#error-500)

### Get Transaction

Given a valid transaction ID, fetches the information of a transaction.

```
transactionsClient
    .get(transactionId)
    .then((data) => console.log(data))
    .catch((error) => console.log(error));
```

Output:

```
Transaction {
    id: 123,
    date: 1587567125458,
    charge: true,
    description: "UBER EATS",
    amount: 1234.56,
    categoryId: 123,
    dateCreated: 1587567125458,
    lastUpdated: 1587567125458,
    isBankAggregation: true
}
```

Possible Errors:

* [Error 400](#error-400)
* [Error 404](#error-404)
* [Error 500](#error-500)

### Create Transaction

Creates a transaction. A previosuly created account is required. You have to import the Transaction Payload Model to create a new one.

```
import { TransactionPayload } from "open-banking-pfm-sdk";
...
const newTransactionData = new TransactionPayload(
    {
    accountId: 487870282, //The ID of the account that holds the transaction. It´s required.
    amount: 1111, //The amount of the transaction. It´s required.
    charge: true, //A flag that indicates if the transaction is a charge or a deposit. It´s required.
    date: 1678255200000, //The date of the transaction. It´s required.
    description: 'Transaction Test', //The description of the transaction. It´s required.
    categoryId: 16 //The category ID of the transaction
    }
);

transactionsClient.create(newTransactionData)
    .then((data) => console.log(data))
    .catch((error) => console.log(error));
```

Output:

```
Transaction {
    id: 789,
    date: 1587567145458,
    charge: true,
    description: "Transaction Test",
    amount: 1111,
    categoryId: 16,
    dateCreated: 1587567145458,
    lastUpdated: 1587567145458,
    isBankAggregation: false
}
```

Possible Errors:

* [Error 400](#error-400)
* [Error 404](#error-404)
* [Error 500](#error-500)

### Update Transaction

Given a valid transaction id updates a transaction. You can pass an object with the properties to update ( `amount`: _number_, `charge`: _boolean_, `date`: _number_, `description`: _string_, `categoryId`: _number_ ).

```
const modifiedTransactionData = { description: 'Edited Transaction Test' };
transactionsClient
    .edit(transactionId, modifiedTransactionData)
    .then((data) => console.log(data))
    .catch((error) => console.log(error));
```

Output:

```
Transaction {
    id: 789,
    date: 1587567145458,
    charge: true,
    description: "Edited Transaction Test",
    amount: 1111,
    categoryId: 16,
    dateCreated: 1587567145458,
    lastUpdated: 1587567145458,
    isBankAggregation: false
}
```

Possible Errors:

* [Error 400](#error-400)
* [Error 404](#error-404)
* [Error 500](#error-500)

### Delete Transaction

Deletes a transaction and all its information.

```
transactionsClient
    .delete(transactionId)
    .then((data) => console.log(data))
    .catch((error) => console.log(error));
```

Possible Errors:

* [Error 400](#error-400)
* [Error 404](#error-404)
* [Error 500](#error-500)

### Delete all transactions from an account

Deletes all transactions of the account by its identifier.

```
transactionsClient
    .deleteAll(accountId)
    .then((data) => console.log(data))
    .catch((error) => console.log(error));
```

Possible Errors:

* [Error 400](#error-400)
* [Error 404](#error-404)
* [Error 500](#error-500)

# Budgets Client

This client has the actions that allow you to manage the representations of the user's budget plans.

How to use
----------

### List Budgets

Fetches a list of budgets per user, sorted by ID in descending order.

```
budgetsClient
    .getList(userId, cursor)
    .then((data) => console.log(data))
    .catch((error) => console.log(error));
```

The cursor param is optional. If a cursor is specified, the list starts with the item that has that ID.

Output:

```
[
    Budget {
        id: 982908114,
        categoryId: 11,
        name: "Sports",
        amount: 1111.00,
        warningPercentage: 0.70,
        spent: 0.00,
        leftToSpend: 1111.00,
        status: "ok",
        dateCreated: 1677714920234,
        lastUpdated: 1677714920234
    },
    Budget {
        id: 982908115,
        categoryId: 11,
        name: "Travelling",
        amount: 1111.00,
        warningPercentage: 0.70,
        spent: 0.00,
        leftToSpend: 1111.00,
        status: "ok",
        dateCreated: 1677714920234,
        lastUpdated: 1677714920234
    }
    ...
]
```

Possible Errors:

* [Error 400](#error-400)
* [Error 404](#error-404)
* [Error 500](#error-500)

### Get Budget

Given a valid budget ID, fetches the information of a budget.

```
const budgetId = 982908114;
budgetsClient
    .get(budgetId)
    .then((data) => console.log(data))
    .catch((error) => console.log(error));
```

Output:

```
Budget {
    id: 982908114,
    categoryId: 11,
    name: "Sports",
    amount: 1111.00,
    warningPercentage: 0.70,
    spent: 0.00,
    leftToSpend: 1111.00,
    status: "ok",
    dateCreated: 1677714920234,
    lastUpdated: 1677714920234
}
```

Possible Errors:

* [Error 400](#error-400)
* [Error 404](#error-404)
* [Error 500](#error-500)

### Create Budget

Creates a budget. A previously created user and a category is required. There can be only one budget per category.

```
import { BudgetPayload } from "open-banking-pfm-sdk";
...
const newBudgetData = new BudgetPayload(
    {
        amount: 100, //The amount of the budget. It's required.
        categoryId: 62, //The category ID of the budget. It's required.
        name: "Donaciones", //The name of the budget. It's required.
        userId: 1859616, //The ID of the user that owns the budget. It's required.
        warningPercentage: 0.5 //The percentage where the budget status changes from stable to warning. This is optinal field. By default is 0.7.
    }
);

budgetsClient.create(newBudgetData)
    .then((data) => console.log(data))
    .catch((error) => console.log(error));
```

Output:

```
Budget {
    id: 234924782,
    categoryId: 62,
    name: "Donaciones",
    amount: 100.00,
    warningPercentage: 0.70,
    spent: 0.00,
    leftToSpend: 100.00,
    status: "ok",
    dateCreated: 1678317204886,
    lastUpdated: 1678317204886
}
```

Possible Errors:

* [Error 400](#error-400)
* [Error 404](#error-404)
* [Error 500](#error-500)

### Update Budget

Update a budget. You can pass an object with the properties to update ( `amount`: _number_, `name`: _string_, `warningPercentage`: _number_ ).

```
const modifiedBudgetData = { amount: 500 };

budgetsClient
    .edit(budgetId, modifiedBudgetData)
    .then((data) => console.log(data))
    .catch((error) => console.log(error));
```

Output:

```
Budget {
    id: 234924782,
    categoryId: 62,
    name: "Donaciones",
    amount: 500.00,
    warningPercentage: 0.70,
    spent: 0.00,
    leftToSpend: 100.00,
    status: "ok",
    dateCreated: 1678317204886,
    lastUpdated: 1678317204886
}
```

Possible Errors:

* [Error 400](#error-400)
* [Error 404](#error-404)
* [Error 500](#error-500)

### Delete Budget

Deletes a budget and all its information.

```
budgetsClient
    .delete(budgetId)
    .then((data) => console.log(data))
    .catch((error) => console.log(error));
```

If the budget was removed, the response will be returned as true.

Possible Errors:

* [Error 400](#error-400)
* [Error 404](#error-404)
* [Error 500](#error-500)

# Bank Aggregation Client

This is the same client as [Banks Client](#banks-client), next contains the available actions related to bank aggregation process.

How to use
----------

### Create Bank Consent

Given a bank ID and a user ID, create consent and retrieve the url for ask it.

```
const bankId = 'c55a1362-93fd-49eb-86ae-d82205b861c5'; //The ID of the bank to request
const userId = 487969842; //The ID of the user to request.
const time = 3; //Term of expiration of consent expressed in months: 3, 6 or 12.

banksClient
    .createConsent(bankId, userId, time)
    .then((data) => console.log(data))
    .catch((error) => console.log(error));
```

Output:

```
{
    bankId: "c55a1362-93fd-49eb-86ae-d82205b861c5",
    userId: 487969842,
    url: "https://auth1.tboz-csbx.staging.ozoneapi-br.com/auth?client_id=75deaf3f-2597-48a0-bf70"
}
```

Possible Errors:

* [Error 400](#error-400)
* [Error 401](#error-401)
* [Error 404](#error-404)
* [Error 500](#error-500)

### Authorize Bank Consent

The code, token and consent state are received to authorize of the consent. In the background, the consent is consumed.

This function not requires the ApiKey, so in the client constructor you don't have to specify it.

```
import { BanksClient } from 'open-banking-pfm-sdk';
const serverUrl = 'https://pfmapiserver.com/api/v1/';
const banksClient = new BanksClient ({ serverUrl });
```

```
const code = 'c55a136207f52861-97e8-4329-8bc5-c324089de119'; //The authCode given by the bank
const id_token = 'eyJhbGciOiJQUzI1NiIsImtpZCI6IkpWdlBicE12bjVaVFJKaGhTaFFGcllJVElHQklVZj'; //The token given by the bank
const state = '3fa85f64-5717-4562-b3fc-2c963f66afa6'; //The state given by the bank.

banksClient
    .authorize(code, id_token, state)
    .then((data) => console.log(data))
    .catch((error) => console.log(error));
```

Output:

Possible Errors:

* [Error 400](#error-400)
* [Error 401](#error-401)
* [Error 404](#error-404)
* [Error 500](#error-500)

### Get Bank aggregation status

Action to obtain bank aggregation status. The status can be __'CONSENT\_REQUESTED' , 'CONSENT\_AUTHORISED', 'CONSENT\_REJECTED' , 'CONSENT\_DELETED' , 'AGGREGATION\_STARTED' , 'AGGREGATION\_COMPLETED' , 'PROCESS\_FAILED'__

```
const bankId = 'c55a1362-93fd-49eb-86ae-d82205b861c5'; //The ID of the bank to request
const userId = 487969842; //The ID of the user to request.
banksClient
    .getAggregationStatus(bankId, userId)
    .then((data) => console.log(data))
    .catch((error) => console.log(error));
```

Output:

```
{
    bankId: "c55a1362-93fd-49eb-86ae-d82205b861c5",
    userId: 487969842,
    status: "CONSENT_REQUESTED"
}
```

Possible Errors:

* [Error 400](#error-400)
* [Error 401](#error-401)
* [Error 404](#error-404)
* [Error 500](#error-500)

### Bank aggregation status with Long Polling

Starts a long polling to get bank aggregation status.

```
const options = {
    bankId: 2230303, //The ID of the bank to request. Is required
    userId: 1111, //The ID of the user to request. Is required.
    onResponse: () => null, //Triggered when the response is received.
    onError: () => null //Triggered when a server error occurs
    time: 5000 //Interval between requests
};

banksClient
    .aggregationStatusSubscribe(options)
    .then((data) => console.log(data))
    .catch((error) => console.log(error));
```

To stop the long polling:

```
banksClient.aggregationStatusUnsubscribe();
```

### Get Bank Resources

Consult the resources granted to the user for the given bank and start in the background the aggregation of the resources that the user is authorised to user.

```
const bankId = 'c55a1362-93fd-49eb-86ae-d82205b861c5'; //The ID of the bank to request
const userId = 487969842; //The ID of the user to request.

banksClient
    .getResources(bankId, userId)
    .then((data) => console.log(data))
    .catch((error) => console.log(error));
```

Output:

```
{
    bankId: "c55a1362-93fd-49eb-86ae-d82205b861c5",
    userId: 487969842,
    resources: [
        "ACCOUNT", "CREDIT_CARD_ACCOUNT"
    ]
}
```

Possible Errors:

* [Error 400](#error-400)
* [Error 401](#error-401)
* [Error 404](#error-404)
* [Error 500](#error-500)

### Synchronize

Start of the resources that the user is authorised for.

```
const bankId = 'c55a1362-93fd-49eb-86ae-d82205b861c5'; //The ID of the bank to request
const userId = 487969842; //The ID of the user to request.

banksClient
    .synchronize(bankId, userId)
    .then((data) => console.log(data))
    .catch((error) => console.log(error));
```

Output:

Possible Errors:

* [Error 400](#error-400)
* [Error 401](#error-401)
* [Error 404](#error-404)
* [Error 500](#error-500)

### Consume Bank Consent (deprecated)

Given a authCode, token and state, consume consent and retrieve consent's status.

```
const authCode = 'c55a136207f52861-97e8-4329-8bc5-c324089de119'; //The authCode given by the bank
const token = 'eyJhbGciOiJQUzI1NiIsImtpZCI6IkpWdlBicE12bjVaVFJKaGhTaFFGcllJVElHQklVZj'; //The token given by the bank
const state = '3fa85f64-5717-4562-b3fc-2c963f66afa6'; //The state given by the bank.

banksClient
    .consumeConsent(authCode, token, state)
    .then((data) => console.log(data))
    .catch((error) => console.log(error));
```

Output:

```
{
    status: "CONSENT_AUTHORISED"
}
```

Possible Errors:

* [Error 400](#error-400)
* [Error 401](#error-401)
* [Error 404](#error-404)
* [Error 500](#error-500)

### Aggregate Banks (deprecated)

Retrieve a list of banks that have successfully completed the bank aggregation process.

```
const userId = 10; //The ID of the user to request.

banksClient
    .getAggregates(userId)
    .then((data) => console.log(data))
    .catch((error) => console.log(error));
```

Output:

```
[
    BankAggregated {
        consentId: "urn:consumption:aac-649a4ef6-d4b4-43a8-b32e-1aba5a0355fd",
        originBankName: null,
        customerIdentification: null,
        cpf: "76109277673",
        targetInstitution: "consumptionSandboxAuth1234",
        deadline: 0,
        expirationDay: 1686184974479,
        status: "CONSENT_REQUESTED",
        isSynchronized: null
    },
...
]
```

Possible Errors:

* [Error 400](#error-400)
* [Error 401](#error-401)
* [Error 404](#error-404)
* [Error 500](#error-500)

# Consents Client

This client helps in Open Banking Consents endpoints.

How to use
----------

### Get Consent

Given a valid consent ID, fetches the information of a consent.

```
consentsClient
    .get(consentId)
    .then((data) => console.log(data))
    .catch((error) => console.log(error));
```

Output:

```
ConsentDetail {
    consentId: "urn:consumption:aac-372ed338-76e7-4616-b793-e792b74215d7",
    originBankName: "test304 test304 test304",
    customerIdentification: null,
    cpf: null,
    BankConsent: {
        bankId: 885402086,
        name: "Consumption Sandbox Auth Server"
    },
    purpose: "Finanças Pessoais",
    deadline: 3,
    status: "ACTIVATED",
    isSynchronized: false
},
```

Possible Errors:

* [Error 400](#error-400)
* [Error 401](#error-401)
* [Error 404](#error-404)
* [Error 500](#error-500)

### Get Consent List

Fetches a list of consents per user.

```
const filterOptions = {
    type: 'ACTIVATED' | 'PENDING' | 'EXPIRED' | 'CANCELLED', //Optional
    status: 'RECEIVED' | 'TRANSMITTED' //Optional
};

consentsClient
    .getList(userId, filterOptions)
    .then((data) => console.log(data))
    .catch((error) => console.log(error));
```

Output:

```
[
Consent {
    consentId: "urn:consumption:aac-372ed338-76e7-4616-b793-e792b74215d7",
    expirationDate: 1688513720224,
    BankConsent: {
        bankId: 885402086,
        name: "Consumption Sandbox Auth Server"
    },
    status: "ACTIVATED"
    }
]
```

Possible Errors:

* [Error 400](#error-400)
* [Error 401](#error-401)
* [Error 404](#error-404)
* [Error 500](#error-500)

### Cancel Consent

A consent is canceled given the identifier.

```
consentsClient
    .delete(consentId)
    .then((data) => console.log(data))
    .catch((error) => console.log(error));
```

If the consent was cancelled, the response will be returned as true.

Possible Errors:

* [Error 400](#error-400)
* [Error 404](#error-404)
* [Error 500](#error-500)

# Credits Client

This client helps in managing users' credit accounts.

How to use
----------

### List Credits

Fetches a list of credit accounts per user.

```
creditsClient
    .getList(userId)
    .then((data) => console.log(data))
    .catch((error) => console.log(error));
```

The ID of the user that owns the accounts is required.

Output:

```
{
    "data": [
        {
            "id": 927241071,
            "providerId": "141000000000000000000013",
            "financialEntityId": 941746456,
            "nature": "Credit card",
            "name": "Cartão INFINITE",
            "number": "Cartão INFINITE",
            "balance": 8247.81,
            "chargeable": false,
            "dateCreated": 1682652624964,
            "lastUpdated": 1682652624964,
            "lineName": "CREDITO_A_VISTA",
            "availableAmount": 8247.8111,
            "limitAmount": 49856.0111,
            "usedAmount": 41609.2111,
            "isBankAggregation": true
        }
    ],
    "nextCursor": null,
    "totalBalance": {
        "availableAmount": 8247.8111,
        "limitAmount": 49856.0111,
        "usedAmount": 41609.2111
    }
}
```

Possible Errors:

* [Error 400](#error-400)
* [Error 404](#error-404)
* [Error 500](#error-500)

# Insights Client

Insights and datapoints about user financial information.

How to use
----------

### Resume

Given a valid user ID, fetches a resume of the financial information of a user. It contains expenses, incomes and balances.

```
const insightsOptions = {
    accountId:525742481,//Mandatory
    dateFrom: number,//Optional
    dateTo: number;//Optional
}

insightsClient
    .getResume(userId, insightsOptions)
    .then((data) => console.log(data))
    .catch((error) => console.log(error));
```

Output:

```
Resume {
    incomes: [],
    expenses: [
        IncomeExpense {
            date: 1672552800000,
            categories: [
                CategoryResume {
                    categoryId: 2,
                    amount: 720.82,
                    subcategories: [
                        SubcategoryInsights {
                            categoryId: 16,
                            amount: 720.82,
                            transactionsByDate: [
                                TransactionsByDate {
                                    date: 1674194400000,
                                    transactions: [
                                        Transaction {
                                            id: 796541939,
                                            date: 1674194400000,
                                            charge: true,
                                            description: 'Clk*Compao Plaza',
                                            amount: 720.82,
                                            categoryId: 16,
                                            dateCreated: 1676572948884,
                                            lastUpdated: 1676981093696
                                        }
                                    ]
                                }
                            ]
                        }
                    ]
                },
                CategoryResume {
                    categoryId: 4,
                    amount: 433.82,
                    subcategories: [ [SubcategoryInsights] ]
                },
                CategoryResume {
                    categoryId: 7,
                    amount: 369.82,
                    subcategories: [ [SubcategoryInsights] ]
                }
            ],
            amount: 1524.46
        },
        IncomeExpense {
            date: 1675231200000,
            categories: [Array],
            amount: 12006.02
        }
    ],
    balances: [
        Balance { incomes: 0, expenses: 12006.02, date: 1675231200000 },
        Balance { incomes: 0, expenses: 1524.46, date: 1672552800000 }
    ]
}
```

Possible Errors:

* [Error 400](#error-400)
* [Error 404](#error-404)
* [Error 500](#error-500)

### Analysis

Given a valid user ID, fetches an analysis of the financial information of a user. It contains expenses, incomes and balances. It receives an object as parameter, accountId is required.

```
const insightsOptions = {
    accountId:525742481,//Required
    dateFrom: number,//Optional
    dateTo: number;//Optional
}
insightsClient
    .getAnalysis(userId, insightsOptions)
    .then((data) => console.log(data))
    .catch((error) => console.log(error));
```

Output:

```
[
    Analysis {
        date: 1672552800000,
        categories: [
            CategoryAnalysis {
                categoryId: 2,
                amount: 720.82,
                subcategories: [
                    SubcategoryAnalysis {
                        categoryId: 16,
                        average: 720.82,
                        quantity: 1,
                        amount: 720.82,
                        transactions: [
                            TransactionAnalysis {
                                description: 'Clk*Compao Plaza',
                                average: 720.82,
                                quantity: 1,
                                amount: 720.82
                            }
                        ]
                    }
                ]
            },
            CategoryAnalysis {
                categoryId: 4,
                amount: 433.82,
                subcategories: [ [SubcategoryAnalysis] ]
            },
            CategoryAnalysis {
                categoryId: 7,
                amount: 369.82,
                subcategories: [ [SubcategoryAnalysis] ]
            }
        ]
    },
    Analysis {
        date: 1675231200000,
        categories: [
            [CategoryAnalysis],
            [CategoryAnalysis],
            [CategoryAnalysis],
            [CategoryAnalysis],
            [CategoryAnalysis],
            [CategoryAnalysis],
            [CategoryAnalysis]
        ]
    },
    Analysis {
        date: 1677650400000,
        categories: [ [CategoryAnalysis] ]
    }
]
```

Possible Errors:

* [Error 400](#error-400)
* [Error 404](#error-404)
* [Error 500](#error-500)

# Helpers

Helpers are functions that can complement the use of clients.

How to use
----------

### Build Clients

This function is used to instantiate all clients with a single call.

```
import { buildClients } from 'open-banking-pfm-sdk';
//The constructor receives an object with two properties, the first is the api key to validate access to all of its functions, the second is the API Server Url, both are required.
const apiKey = 'XXXX-XXXX-XXXX';
const serverUrl = 'https://pfmapiserver.com/api/v1/';
const clients = new buildClients({ apiKey, serverUrl });
console.log(clients);
```

Output:

```
ClientDictionary {
    accountsClient: AccountsClient,
    banksClient: BanksClient,
    budgetsClient: IBudgetsClient;
    categoriesClient: ICategoriesClient;
    transactionsClient: TransactionsClient,
    usersClient: UsersClient,
    creditsClient: CreditsClient,
    consentsClient: ConsentsClient
}
```

# Error Codes

Error 400
---------

Something in your request was wrong.

```
{
  "errors": [
    {
      "code": "string",
      "title": "string",
      "detail": "string"
    }
  ]
}
```

Error 401
---------

Invalid authorization.

Error 404
---------

The requested param was not found.

```
{
    "errors": [
        {
          "code": "string",
          "title": "string",
          "detail": "string"
        }
    ]
}
```

Error 500
---------

Something in your request was wrong.