# PFM SDK Backend iOS

The Open Banking PFM SDK uses Client classes and with **Promises** to get responses from the Open Banking PFM API in an easier way and structured as data models.

# Table of contents

* [Installation](#installation)
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

# Installation

## Cocoapods

Make sure you are running the latest version of [CocoaPods](https://cocoapods.org/) by running:

```shell
gem install cocoapods

# (or if the above fails)
sudo gem install cocoapods
```

Update your local specs repo by running:

```shell
pod repo update
```

**Note:** This step is optional, if you updated the specs repo recently.

Add the following lines to your `Podfile`:

```ruby
pod 'OpenBankingCore'
pod 'OpenBankingPFMSDK'
```

Run `pod install` and you're all set!

# Configuration

# Users Client

This client contains the available actions related to users. In order to successfully integrate the following functions, it is important to previously load the users explained in the installation guide.

How to use
----------

### Get User

Given a valid user ID, fetches the information of an user.

```swift
var usersService = OpenBankingPFMAPI.usersClient()
// In order to prevent bad requests
usersService.clearQueryItems()

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

<details>
<summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

### Get Financial Entities

Fetches the information of the user's financial entities.

```swift
var usersService = OpenBankingPFMAPI.usersClient()
// In order to prevent bad requests
usersService.clearQueryItems()

// Optional parameter
if let isBankAggregation = isBankAggregation {
    usersService = usersService.isBankAggregation(isBankAggregation)
}

usersService.getFinancialEntities { [weak self] (result: Result<FinancialEntitiesArray<OBFinancialEntity>>) in
    switch result {
    case .success(let result):
        self?.dataSource = result
        completion(true)
    case .failure(let error):
        print(error)
        completion(false)
    }
}
```

Output:

```swift
class OBFinancialEntity: Codable {
    public let lastUpdated: Double?
    public let id: Double?
    public let code: String?
    public let dateCreated: Double?
    public let imagePath: String?
    public let isBankAggregation: Bool?
    public let name: String?
}
```

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

# Banks Client

This client contains the available actions related to banks.

How to use
----------

### Available Banks

Returns the list of available banks to be able to add and manage movements, money and balance with the bank.

```swift
var banksService = OpenBankingPFMAPI.banksClient()
// In order to prevent bad requests
banksService.clearQueryItems()

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

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 401

Invalid authorization.

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

# Categories Client

This client contains the available actions related to the classification of transactions and budgets.

How to use
----------

### List Categories

Fetches a list of categories, sorted by ID in descending order

```swift
var categoriesService = OpenBankingPFMAPI.categoriesClient()
// In order to prevent bad requests
categoryService.clearQueryItems()

let userId = 123456
let isUserCategory:Bool? = true

// Optional parameter
categoriesService = categoriesService.userId(userId)
// Optional parameter
if let isUserCategory = isUserCategory {
// In order to filter the categories (true = users categories, false = default categories, nil = both)
    categoriesService = categoriesService.isUserCategory(isUserCategory)
}

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

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

### Get Category

Given a valid category ID, fetches the information of a category.

```swift
var categoriesService = OpenBankingPFMAPI.categoriesClient()
// In order to prevent bad requests
categoryService.clearQueryItems()

let categoryId = 2

// Remote call
categoriesService.get(withID: categoryId) { [weak self] (result: Result<OBCategory>)in
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

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

### Create Category

Creates a category. If a user ID is not specified, the category is considered as a system category. If a parent category ID is specified, the category is considered a subcategory. 

```swift
var categoryService = OpenBankingPFMAPI.categoriesClient()
// In order to prevent bad requests
categoryService.clearQueryItems()

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

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

### Update Category

Given a valid category id updates a category. You can pass an object with the properties to update ( `name`: _string_, `color`: _string_, `parentCategoryId`: \[_number_, nil] ).

```swift
var categoryService = OpenBankingPFMAPI.categoriesClient()
// In order to prevent bad requests
categoryService.clearQueryItems()

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

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

### Delete Category

Given a valid category id deletes a category.

```swift
var categoryService = OpenBankingPFMAPI.categoriesClient()
// In order to prevent bad requests
categoryService.clearQueryItems()

let categoryId = 123456

// Remote call
categoriesService.delete(withID: categoryId) { (result: Result<Bool>) in
    switch result {
    case .success(let result):
        print(result)
    case .failure(let error):
        print("Error: \(error.localizedDescription)")
    }
}
```

If the category was removed, the response will be returned as true.

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

# Accounts Client

This client helps in managing users' bank accounts.

How to use
----------

### List Accounts

Fetches a list of accounts per user, sorted by ID in descending order.

```swift
var accountsService = OpenBankingPFMAPI.accountsClient()
// In order to prevent bad requests
accountsService.clearQueryItems()

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

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

### Get Account

Given a valid account ID, fetches the information of an account.

```swift
var accountsService = OpenBankingPFMAPI.accountsClient()
// In order to prevent bad requests
accountsService.clearQueryItems()

let accountId = 320023867

// Remote call
accountsService.get(withID: accountId) { (result: Result<OBAccount>) in
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

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

### Create Account

Creates an account. A previously created user and a financial entity is required. You have to import the Account Payload Model to create a new one.

The possible values for nature property are "__Debit__" , "__Credit card__" , "__Credit__" , "__Investment__" , "__Mortgage__"

```swift
var accountsService = OpenBankingPFMAPI.accountsClient()
// In order to prevent bad requests
accountsService.clearQueryItems()

let newAccountData = OBAccount(financialEntityId: 743443,
          nature: "Debit",
          name: "Premium",
          number: "1111 1111 1111 1111",
          balance: 1000,
          chargeable: false,
          userId: 1115164)

// Remote call
accountsService.create(newAccountData) { (result: Result<OBAccount>) in
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

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

### Update Account

Updates an account. You can pass an object with the properties to update ( `nature`: _string_, `name`: _string_, `number`: _string_, `balance`: _number_, `chargeable`: _boolean_ ).

```swift
var accountsService = OpenBankingPFMAPI.accountsClient()
// In order to prevent bad requests
accountsService.clearQueryItems()

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

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

### Delete Account

Deletes an account and all its information, including transactions.

```swift
var accountsService = OpenBankingPFMAPI.accountsClient()
// In order to prevent bad requests
accountsService.clearQueryItems()

let accountId = 320023867

// Remote call
accountsService.delete(withID: accountId) { (result:Result<Bool>) in
    switch result {
    case .success(let result):
        print(result)
    case .failure(let error):
        print("Error: \(error.localizedDescription)")
    }
}
```

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

# Transactions Client

This client helps in the management of financial movements within an account.

How to use
----------

### List Transactions

Fetches a list of transactions per account. You can pass a filter options object as parameter if is not passed all transactions are listed.

```swift
var transactionsService = OpenBankingPFMAPI.transactionsClient()
// In order to prevent bad requests
transactionsService.clearQueryItems()
let accountId = 123456

// FILTER PARAMETERS
// Optional parameter
if let page = page {
// To ask for a specific page
    transactionsService = transactionsService.page(page)
}
// Optional parameter
if let size = size {
// To indicate the number of items to show by page
    transactionsService = transactionsService.size(size)
}
// Optional parameter
if let field = field {
    transactionsService = transactionsService.field(field)
}
// Optional parameter
if let order = order {
    transactionsService = transactionsService.order(order)
}
// Optional parameter
if let categoryId = categoryId {
// To ask for a specific category to filter
    transactionsService = transactionsService.categoryId(categoryId)
}
// Optional parameter
if let charge = charge {
// To filter by type of transaction(true = charge, false = deposit, nil = both)
    transactionsService = transactionsService.charge(charge)
}
// Optional parameter
if let minAmount = minAmount {
// The minimum amount of the transaction.
    transactionsService = transactionsService.minAmount(minAmount)
}
// Optional parameter
if let maxAmount = maxAmount {
// The maximum amount of the transaction.
    transactionsService = transactionsService.maxAmount(maxAmount)
}
// Optional parameter
if let dateFrom = dateFrom {
// The minimum date of the transaction, in UNIX format by milliseconds
    transactionsService = transactionsService.dateFrom(dateFrom)
}
// Optional parameter
if let dateTo = dateTo {
// The maximum date of the transaction, in UNIX format by milliseconds
    transactionsService = transactionsService.dateTo(dateTo)
}
// Optional parameter
if let description = description {
// The description of the transaction. It can be partial.
    transactionsService = transactionsService.description(description)
}
// Optional parameter
if let isBankAggregation = isBankAggregation {
    transactionsService = transactionsService.isBankAggregation(isBankAggregation)
}

// Remote call
transactionsService.accountIds(accountId).getList { (result:Result<OBBodyResponse<[OBTransaction]>>) in
    switch result {
    case .success(let result):
// Set the parsed transactions into your datasource
        self?.dataSource = result.data
    case .failure(let error):
        print("Error: \(error.localizedDescription)")
    }
}
```

Output:

```swift
struct OBTransaction: Codable {
    public var id: Int?
    public var accountId: Int?
    public var date: Int
    public var charge: Bool
    public var transactionDescription: String
    public var amount: Double
    public var categoryId: Int?
    public var dateCreated: Int?
    public var lastUpdated: Int?
    public var isBankAggregation: Bool?
}
```

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

### Get Transaction

Given a valid transaction ID, fetches the information of a transaction.

```swift
var transactionsService = OpenBankingPFMAPI.transactionsClient()
// In order to prevent bad requests
transactionsService.clearQueryItems()

let transactionId = 123456

// Remote call
transactionsService.get(withID: transactionId) { (result: Result<OBTransaction>) in
    switch result {
    case .success(let result):
// Set the parsed transaction into your datasource
        self?.dataSource = [result]
    case .failure(let error):
        print("Error: \(error.localizedDescription)")
    }
}
```

Output:

```swift
struct OBTransaction: Codable {
    public var id: Int?
    public var accountId: Int?
    public var date: Int
    public var charge: Bool
    public var transactionDescription: String
    public var amount: Double
    public var categoryId: Int?
    public var dateCreated: Int?
    public var lastUpdated: Int?
    public var isBankAggregation: Bool?
}
```

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

### Create Transaction

Creates a transaction. A previously created account is required. You have to import the Transaction Payload Model to create a new one.

```swift
var transactionsService = OpenBankingPFMAPI.transactionsClient()
// In order to prevent bad requests
transactionsService.clearQueryItems()

let newTransaction = OBTransaction(accountId: 487870282, //The ID of the account that holds the transaction. It´s required.,
                                   date: 1678255200000, //The date of the transaction. It´s required.,
                                   charge: true, //A flag that indicates if the transaction is a charge or a deposit. It´s required.,
                                   description: "Transaction Test", //The description of the transaction. It´s required.,
                                   amount: 1111) //The amount of the transaction. It´s required.

// Remote call
transactionsService.create(newTransaction) { (result: Result<OBTransaction>) in
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
struct OBTransaction: Codable {
    public var id: Int?
    public var accountId: Int?
    public var date: Int
    public var charge: Bool
    public var transactionDescription: String
    public var amount: Double
    public var categoryId: Int?
    public var dateCreated: Int?
    public var lastUpdated: Int?
    public var isBankAggregation: Bool?
}
```

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

### Update Transaction

Given a valid transaction id updates a transaction. You can pass an object with the properties to update ( `amount`: _number_, `charge`: _boolean_, `date`: _number_, `description`: _string_, `categoryId`: _number_ ).

```swift
var transactionsService = OpenBankingPFMAPI.transactionsClient()
// In order to prevent bad requests
transactionsService.clearQueryItems()

// Using some existing transaction
transaction.transactionDescription = "Edited Transaction Test"

// Remote call
transactionService.edit(transaction) { (result: Result<OBTransaction>) in
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
struct OBTransaction: Codable {
    public var id: Int?
    public var accountId: Int?
    public var date: Int
    public var charge: Bool
    public var transactionDescription: String
    public var amount: Double
    public var categoryId: Int?
    public var dateCreated: Int?
    public var lastUpdated: Int?
    public var isBankAggregation: Bool?
}
```

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

### Delete Transaction

Deletes a transaction and all its information.

```swift
var transactionsService = OpenBankingPFMAPI.transactionsClient()
// In order to prevent bad requests
transactionsService.clearQueryItems()

let transactionId = 123456

// Remote call
transactionsService.delete(withID: transactionId) { (result:Result<Bool>) in
    switch result {
    case .success(let result):
        print(result)
    case .failure(let error):
        print("Error: \(error.localizedDescription)")
    }
}
```

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

# Budgets Client

This client has the actions that allow you to manage the representations of the user's budget plans.

How to use
----------

### List Budgets

Fetches a list of budgets per user, sorted by ID in descending order.

```swift
var budgetsService = OpenBankingPFMAPI.budgetsClient()
// In order to prevent bad requests
budgetsService.clearQueryItems()

let userId = 123456

// Remote call
budgetsService.userId(userId).getList { (result:Result<OBBodyResponse<[OBBudget]>>) in
    switch result {
    case .success(let result):
// Set the parsed budgets into your datasource
        self?.dataSource = result.data
    case .failure(let error):
        print("Error: \(error.localizedDescription)")
    }
}
```

The cursor param is optional. If a cursor is specified, the list starts with the item that has that ID.

Output:

```swift
class OBBudget: Codable {
    public var id: Int?
    public var userId: Int?
    public var categoryId: Int?
    public var name: String
    public var amount: Double
    public var warningPercentage: Float?
    public var spent: Double?
    public var leftToSpend: Double?
    public var status: String?
    public var dateCreated: Int?
    public var lastUpdated: Int?
}
```

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

### Get Budget

Given a valid budget ID, fetches the information of a budget.

```swift
var budgetsService = OpenBankingPFMAPI.budgetsClient()
// In order to prevent bad requests
budgetsService.clearQueryItems()

let budgetId = 123456

// Remote call
budgetsService.get(withID: budgetId) { (result: Result<OBBudget>) in
    switch result {
    case .success(let result):
// Set the parsed budget into your datasource
        self?.dataSource = [result]
    case .failure(let error):
        print("Error: \(error.localizedDescription)")
    }
}
```

Output:

```swift
class OBBudget: Codable {
    public var id: Int?
    public var userId: Int?
    public var categoryId: Int?
    public var name: String
    public var amount: Double
    public var warningPercentage: Float?
    public var spent: Double?
    public var leftToSpend: Double?
    public var status: String?
    public var dateCreated: Int?
    public var lastUpdated: Int?
}
```

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

### Create Budget

Creates a budget. A previously created user and a category is required. There can be only one budget per category.

```swift
var budgetsService = OpenBankingPFMAPI.budgetsClient()
// In order to prevent bad requests
budgetsService.clearQueryItems()

let newBudget = OBBudget(userId: 1859616, //The ID of the user that owns the budget. It's required.
         categoryId: 62, //The category ID of the budget. It's required.
         name: "Donaciones", //The name of the budget. It's required.
         amount: 100) //The amount of the budget. It's required.

// Remote call
budgetsService.create(budget) { (result: Result<OBBudget>) in
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
class OBBudget: Codable {
    public var id: Int?
    public var userId: Int?
    public var categoryId: Int?
    public var name: String
    public var amount: Double
    public var warningPercentage: Float?
    public var spent: Double?
    public var leftToSpend: Double?
    public var status: String?
    public var dateCreated: Int?
    public var lastUpdated: Int?
}
```

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

### Update Budget

Update a budget. You can pass an object with the properties to update ( `amount`: _number_, `name`: _string_, `warningPercentage`: _number_ ).

```swift
var budgetsService = OpenBankingPFMAPI.budgetsClient()
// In order to prevent bad requests
budgetsService.clearQueryItems()

// Using some existing budget
budget.amount = 500

// Remote call
categoryService.edit(budget) { (result: Result<OBBudget>) in
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
class OBBudget: Codable {
    public var id: Int?
    public var userId: Int?
    public var categoryId: Int?
    public var name: String
    public var amount: Double
    public var warningPercentage: Float?
    public var spent: Double?
    public var leftToSpend: Double?
    public var status: String?
    public var dateCreated: Int?
    public var lastUpdated: Int?
}
```

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

### Delete Budget

Deletes a budget and all its information.

```swift
var budgetsService = OpenBankingPFMAPI.budgetsClient()
// In order to prevent bad requests
budgetsService.clearQueryItems()

let budgetId = 123456

// Remote call
budgetsService.delete(withID: budgetId) { (result:Result<Bool>) in
    switch result {
    case .success(let result):
        print(result)
    case .failure(let error):
        print("Error: \(error.localizedDescription)")
    }
}
```

If the budget was removed, the response will be returned as true.

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

# Bank Aggregation Client

This is the same client as [Banks Client](#banks-client), next contains the available actions related to bank aggregation process.

How to use
----------

### Create Bank Consent

Given a bank ID and a user ID, create consent and retrieve the url for ask it.

```swift
var bankService = OpenBankingPFMAPI.banksClient()
// In order to prevent bad requests
bankService.clearQueryItems()

let userId = 487969842 //The ID of the user to request.
let time = 3 //Term of expiration of consent expressed in months: 3, 6 or 12.
let bankId = "c55a1362-93fd-49eb-86ae-d82205b861c5" //The ID of the bank to request

banksService = banksService.userId(userId)
banksService = banksService.time(time)

// Remote call
banksService.createConsent(withBankId: bankId) { (result: Result<OBCreateConsentResponse>) in
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
class OBBank: Codable {
    public var name: String
    public var bankId: String
    public var financialEntityId: Int
    public var imagePath: String?
    public var isBankAggregation: Bool
}
```

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 401

Invalid authorization.

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

### Get Bank aggregation status

Action to obtain bank aggregation status. The status can be __'CONSENT\_REQUESTED' , 'CONSENT\_AUTHORISED', 'CONSENT\_REJECTED' , 'CONSENT\_DELETED' , 'AGGREGATION\_STARTED' , 'AGGREGATION\_COMPLETED' , 'PROCESS\_FAILED'__

```swift
var bankService = OpenBankingPFMAPI.banksClient()
// In order to prevent bad requests
bankService.clearQueryItems()

let bankId = "c55a1362-93fd-49eb-86ae-d82205b861c5" //The ID of the bank to request
let userId = 487969842 //The ID of the user to request.

banksService = banksService.userId(userId)

// Remote call
banksService.getAggregationStatus(withBankId: bankId) { (result:Result<OBBankAggregationStatusResponse>) in
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
class OBBankAggregationStatusResponse: Codable {
    public var bankId: String
    public var userId: Int
    public var status: String
}
```

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 401

Invalid authorization.

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

### Get Bank Resources

Consult the resources granted to the user for the given bank and start in the background the aggregation of the resources that the user is authorised to user.

```swift
var bankService = OpenBankingPFMAPI.banksClient()
// In order to prevent bad requests
bankService.clearQueryItems()

let bankId = "c55a1362-93fd-49eb-86ae-d82205b861c5" //The ID of the bank to request
let userId = 487969842 //The ID of the user to request.

banksService = banksService.userId(userId)

// Remote call
banksService.getResources(withBankId: bankId) { (result:Result<OBResourcesResponse>) in
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
class OBResourcesResponse: Codable {
    public var bankId: String
    public var userId: Int
    public var resources: [String]
}
```

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 401

Invalid authorization.

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

### Synchronize

Start of the resources that the user is authorised for.

```swift
var bankService = OpenBankingPFMAPI.banksClient()
// In order to prevent bad requests
bankService.clearQueryItems()

let bankId = "c55a1362-93fd-49eb-86ae-d82205b861c5" //The ID of the bank to request
let userId = 487969842 //The ID of the user to request.

banksService = banksService.userId(userId)

// Remote call
banksService.synchronize(withBankId: bankId) { (result: Result<Bool>) in
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
true
```

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 401

Invalid authorization.

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

# Consents Client

This client helps in Open Banking Consents endpoints.

How to use
----------

### Get Consent

Given a valid consent ID, fetches the information of a consent.

```swift
var consentsService = OpenBankingPFMAPI.consentsClient()
// In order to prevent bad requests
consentsService.clearQueryItems()

let consentId = "some_consent_id"
let type = "ACTIVATED" // Values: 'ACTIVATED' | 'PENDING' | 'EXPIRED' | 'CANCELLED' 
let status = "RECEIVED" // Values: 'RECEIVED' | 'TRANSMITTED'

// Optional parameter
if let type = type {
    consentsService = consentsService.type(type)
}

// Optional parameter
if let status = status {
    consentsService = consentsService.status(status)
}

// Remote call
consentsService.get(withID: consentId) { (result: Result<OBConsent>) in
    switch result {
    case .success(let result):
// Set the parsed consent into your datasource
        self?.dataSource = [result]
    case .failure(let error):
        print("Error: \(error.localizedDescription)")
    }
}
```

Output:

```swift
class OBConsent: Codable {
    public var consentId: String?
    public var expirationDate: Int?
    public var bank: OBBankConsent
    public var status: String
    public var originBankName: String?
    public var customerIdentification: String?
    public var cpf: String?
    public var purpose: String?
    public var deadline: Int?
    public var isSynchronized: Bool?
}
```

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 401

Invalid authorization.

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

### Get Consent List

Fetches a list of consents per user.

```swift
var consentsService = OpenBankingPFMAPI.consentsClient()
// In order to prevent bad requests
consentsService.clearQueryItems()

let userId = 123456
consentsService = consentsService.userId(userId)

// Remote call
consentsService.getList { (result: Result<OBConsentsResponse<[OBConsent]>>) in
    switch result {
    case .success(let result):
// Set the parsed consents into your datasource
        self?.dataSource = result.consents
    case .failure(let error):
        print("Error: \(error.localizedDescription)")
    }
}
```

Output:

```swift
class OBConsent: Codable {
    public var consentId: String?
    public var expirationDate: Int?
    public var bank: OBBankConsent
    public var status: String
    public var originBankName: String?
    public var customerIdentification: String?
    public var cpf: String?
    public var purpose: String?
    public var deadline: Int?
    public var isSynchronized: Bool?
}
```

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 401

Invalid authorization.

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

### Cancel Consent

A consent is canceled given the identifier.

```swift
var consentsService = OpenBankingPFMAPI.consentsClient()
// In order to prevent bad requests
consentsService.clearQueryItems()

let consentId = "some_consent_id"

// Remote call
consentsService.delete(withID: consentId) { (result: Result<Bool>) in
    switch result {
    case .success(let result):
        print(result)
    case .failure(let error):
        print("Error: \(error.localizedDescription)")
    }
}
```

If the consent was cancelled, the response will be returned as true.

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

# Credits Client

This client helps in managing users' credit accounts.

How to use
----------

### List Credits

Fetches a list of credit accounts per user.

```swift
var creditsService = OpenBankingPFMAPI.creditsClient()
// In order to prevent bad requests
creditsService.clearQueryItems()

let userId = 123456
creditsService = creditsService.userId(userId)

// Optional parameter
if let cursor = cursor {
// The ID of the account where the list starts.
    creditsService = creditsService.cursor(cursor)
}

// Remote call
creditsService.getList { (result: Result<OBCreditResponse<[OBCredit]>>) in
    switch result {
    case .success(let result):
// Set the parsed credits into your datasource
        self?.dataSource = result.data
    case .failure(let error):
        print("Error: \(error.localizedDescription)")
    }
}
```

The ID of the user that owns the accounts is required.

Output:

```swift
class OBCreditResponse<T: Codable>: Codable {
    var data: [OBCredit]?
    var nextCursor: Int?
    var totalBalance: OBCreditTotalBalance?
}

class OBCreditTotalBalance: Codable {
    var availableAmount: Double?
    var limitAmount: Double?
    var userAmount: Double?
}

class OBCredit: Codable {
    var id: Int
    var providerId: String?
    var financialEntityId: Int
    var nature: String?
    var name: String?
    var number: String?
    var chargeable: Bool
    var dateCreated: Int
    var lastUpdated: Int
    var lineName: String?
    var availableAmount: Double
    var limitAmount: Double
    var usedAmount: Double
    var isBankAggregation: Bool
}
```

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

# Insights Client

Insights and datapoints about user financial information.

How to use
----------

### Resume

Given a valid user ID, fetches a resume of the financial information of a user. It contains expenses, incomes and balances.

```swift
var insightService = OpenBankingPFMAPI.insightsClient()
// In order to prevent bad requests
insightService.clearQueryItems()

let userId = 123456
let accountIds: String? = "12, 83"
let dateFrom: Int? = 1587567125458
let dateTo: Int? = 1587567125458 

// Optional parameter
if let accountIds = accountIds {
// The ID of the account to fetch the resume. If it is not present, the API will use all the accounts of the user.
    insightService = insightService.accountIds(accountIds)
}
// Optional parameter
if let dateFrom = dateFrom {
// The date where the resume starts, in UNIX format.
    insightService = insightService.dateFrom(dateFrom)
}
// Optional parameter
if let dateTo = dateTo {
// The date where the resume ends, in UNIX format.
    insightService = insightService.dateTo(dateTo)
}

// Remote call
insightService.userId(userId).getResume { (result: Result<OBSummaryResponse<OBResumeBalance>>) in
    switch result {
    case .success(let result):
// Set the parsed resume into your datasource
        self?.dataSource = result
    case .failure(let error):
        print("Error: \(error.localizedDescription)")
    }
}
```

Output:

```swift
class OBSummaryResponse<T: Codable>: Codable {
    let incomes: [OBIncome]?
    let expenses: [OBExpense]?
    let balances: [OBBalance]?
}

struct OBIncome: Codable {
    let date: Int
    let categories: [OBResumeByCategory]
    let amount: Double
}

struct OBExpense: Codable {
    let date: Int
    let categories: [OBResumeByCategory]
    let amount: Double
}

struct OBBalance: Codable {
    let date: Int
    let incomes: Double
    let expenses: Double
}

struct OBResumeByCategory: Codable {
    let categoryId: Int
    let amount: Double
    let subcategories: [OBResumeBySubcategory]
}

struct OBResumeBySubcategory: Codable {
    let categoryId: Int
    let amount: Double
    let transactionsByDate: [OBTransactionsByDate]
}

struct OBTransactionsByDate: Codable {
    let date: Int
    let transactions: [OBTransaction]
}

struct OBTransaction: Codable {
    public var id: Int?
    public var accountId: Int?
    public var date: Int
    public var charge: Bool
    public var transactionDescription: String
    public var amount: Double
    public var categoryId: Int?
    public var dateCreated: Int?
    public var lastUpdated: Int?
    public var isBankAggregation: Bool?
}
```

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>

### Analysis

Given a valid user ID, fetches an analysis of the financial information of a user. It contains expenses, incomes and balances. It receives an object as parameter, accountId is required.

```swift
var insightService = OpenBankingPFMAPI.insightsClient()
// In order to prevent bad requests
insightService.clearQueryItems()

let userId = 123456

// Remote call
insightService.userId(userId).getAnalysis { (result: Result<OBAnalysisResponse<OBAnalysis>>) in
    switch result {
    case .success(let result):
// Set the parsed analysis into your datasource
        self.dataSource = result.data
    case .failure(let error):
        print("Error: \(error.localizedDescription)")
    }
}
```

Output:

```swift
struct OBAnalysis: Codable {
    let data: [OBAnalysisByDate]
}

struct OBAnalysisByDate: Codable {
    let date: Int
    let categories: [Category]
}

struct Category: Codable {
    let categoryID: Int
    let amount: Double
    let subcategories: [Subcategory]
}

struct Subcategory: Codable {
    let categoryID: Int
    let amount, average: Double
    let quantity: Int
    let transactions: [Transaction]
}

struct Transaction: Codable {
    let transactionDescription: String
    let average: Double
    let quantity: Int
    let amount: Double
}
```

<details>
  <summary><h4>Possible Errors</h4></summary>

### Error 400

Something in your request was wrong.

```json
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

## Error 404

The requested param was not found.

```json
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

## Error 500

Something in your request was wrong.

```json
{
    "message": "string"
}
```

</details>