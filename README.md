# Mongo-Aggregate-Framework-All-codes
## Aggregation framework is the most powerfull tool that mongodb offers to reduce execution time and execute queries faster.
# Match
#### Example-1
	db.getCollection('persons').aggregate([
	{$match:{age:{$gte:25}}}
	])

#### Example-2
	db.getCollection('persons').aggregate([
	{$match:{age:{$gte:25},gender:"female"}}
	])
####  Outputs all enteries with age greater than 25 and gender female.	
# 
# Group
#### Groups documents on certain field names.
#### Example-1
	db.getCollection('persons').aggregate([
	{$group:{_id:{age:"$age",gender:"female"}}}
	])
	
####  Outputs all enteries by grouping on age field and gender field.	

#
# Count
#### # Example-1:
###### To Count All Docs.
*db.getCollection('persons').aggregate([
{$count:"allDocumentsCount"}
])*
#### # Example-2:
###### #### To match docs based on age and return there count
	*db.getCollection('persons').aggregate([
  	{$match:{age:{$gte:25}}},
  	{$count:"AgeCount"} 
	])*
# 
# Sort
#### # Example-1:
###### To Sort All Docs  if we pass -1 sort goes descending for 1 it goes ascending
	*db.getCollection('persons').aggregate([
	{$sort:{age:-1}}
	]*
#### Example-2:
###### First we sort by descending order of age and then by color in ascending.
	*db.getCollection('persons').aggregate([
	{$sort:{age:-1,eyeColor:1}}
	])*
#### Example-3:
##### Group And Sort
###### First we group docs by age field and then sord in ascending order.
	*db.getCollection('persons').aggregate([
	 {$group:{_id:"$age"}},
	{$sort:{_id:1}}
	])*
#### Example-4:
##### Match,Group And Sort
###### First we Match the docs on certain query and then then return the result to group. group then groups them by fieldname  and then sorts respectively.
	*db.getCollection('persons').aggregate([
	 {$match:{eyeColor:{$ne:"blue"}}},
	 {$group:{_id:{eyeColor:"$eyeColor",favoriteFruit:"$favoriteFruit"}}},
	{$sort:{"_id.eyeColor":1,"_id.favoriteFruit":-1}}
	]*)
#  
# Project
##### Get Certain fields with Project.
###### If we pass 1 for a certain field only that filed is included in the returned Document or if we want to exclude certain fields we can pass 0 for that fields.
#### Example-1
	*db.getCollection('persons').aggregate([
	{$project:{name:1,}}
	])*
#### Example-2 
###### Exclude Certain fields
	*db.getCollection('persons').aggregate([
	{$project:{name:0,age:0}}
	])*
#### Example-3 
###### Restructure documents accordng to your needs from main documents through Project.
	*db.getCollection('persons').aggregate([
	{$project:{
	    name:1,
		info:{
	    location:"$company.location.country",
	      favoriteFruit:"$favoriteFruit",
	    age:"$age"    
		}
	    }}
	])*	
#  
# Unwind
###### Used For displaying an array as a single item.

#### Example-1
###### Usage of unwind with project Let tags:[1,2,3,4].

	*db.getCollection('persons').aggregate([
	// stage1
	{$unwind:"$tags"},
	{$project:{name:1,tags:1}}

	])
	*
###### Explanation:Here the array pased is splitted in single items and returned in single single documents by project rest of the fields being common for each element of the array.
###### Output would be ->
	/* 1 */
	{
	    "_id" : same,
	    "name" : "same",
	    "tags" : "1"
	}

	/* 2 */
	{
	    "_id" : same,
	    "name" : "same",
	    "tags" : "2"
	}

	/* 3 */
	{
	    "_id" :same,
	    "name" : "same",
	    "tags" : "3"
	}

	/* 4 */
	{
	    "_id" : same,
	    "name" : "same",
	    "tags" : "4"
	}
	*
# 
# Accumulators
#### $sum,$avg,$max,$min
#### Example 1 Sum
###### It will give all the count for people with same age. 
#####for example
{
    "_id" : 30,
    "count" : 38.0
}
###### There are 38 people wuth age 30 in  the collection as we have grouped on age field
	*db.getCollection('persons').aggregate([
	{
	    $group:{
		_id:"$age",
		count:{$sum:1}
		}
	    }
	])*
##### Example 2 Sum with unwind and group tags:[1,2,3,4,5]

	*db.getCollection('persons').aggregate([
	{$unwind:"$tags"},
	{$group:{
	_id:"$tags",
	count:{$sum:1}
	}}
	])*
###### Output It will return the count of occureneces of every element of the tags array from the entire collection
# $avg
#### $avg Here we will count avg of certain fields.
###### Example 1
	db.getCollection('persons').aggregate([
	{
	$group:{
	_id:"$location",
	avgAge:{$avg:"$age"
	}
	}
	])
###### It will return the average age of persons with same location .
#
# Unary Operators
#### $type 
###### Let array=[1,2,3,4],object ={{"x":"y"}}
###### Explanation->Takes in the actual document and returns the type of it.
	*
	db.getCollection('persons').aggregate([
	{$project:{
	    name:1,
	    ageType:{$type:"$age"},
		arrayType:{$type:"$array"},
		objectType:{$type:"$object"}
	    }}

	 ])
	*
##### Output
	*{
	    "_id" : ObjectId("5f167bdb84e443c125dfee18"),
	    "name" : "Something",
	    "ageType" : "int",
		"arrayType":"array",
		"objectType":"object"
	}*

#
# $out Operator.
###### Used to write documents of one collection to another
###### Example->
	*db.getCollection('persons').aggregate([
	{$group:{_id:{age:"$age"}}},
	{$out:"newCollection"}

])*
###### Groups All age Categories from one collection and writes to another collection names newCollection.
#

# $elemmatch
###### Matches And returns elements from the array that match a certain field in an array or Array of objects.
	{ _id: 1, results: [ { product: "abc", score: 10 }, { product: "xyz", score: 5 } ] }
	{ _id: 2, results: [ { product: "abc", score: 8 }, { product: "xyz", score: 7 } ] }
	
#### { <field>: { $elemMatch: { <query1>, <query2>, ... } } }
####  Example 	
	db.products.find(
	   { results: { $elemMatch: { product: "xyz", score: { $gte: 8 } } } }
	)	
	
#

# INDEXING

# createIndex()
##### Creates index on required fields
## Syntax
    db.COLLECTION_NAME.createIndex( <key:1/-1>, <options>(optional) )
 key is the name of the field on which you want to create index and 1 is for ascending order.
 
#### Example
     db.studentdata.createIndex({student_name: 1})
#### Example
     db.mycol.createIndex({"title":1,"description":-1})
#### Example with Options
     db.studentdata.createIndex({student_name: 1}, {unique: true})
     
##### Other options values: background(create index in background), name(specify name for index)


# getIndex()
##### Find all the indexes created on a collection
## Syntax
    db.COLLECTION_NAME.getIndexes()
    
# Drop indexes   
##### Drop particular index or all indexes from collection
## Syntax
	db.collection_name.dropIndex({index_name: 1})
#### Example
	db.studentdata.dropIndex({student_name: 1})
### Dropping all the indexes:
	db.collection_name.dropIndexes()
	
# Index Types
### =>Single Field
### =>Compound Index
### =>Multikey Index
### =>Text Indexes
### =>Hashed Indexes

# Single Field
##### sort order of the index key does not matter because MongoDB can traverse the index in either direction.

### Example
	db.studentdata.createIndex( { score: 1 } )
#### For embedded fields:
	db.studentdata.createIndex( { "location.state": 1 } )
	
# Compound Index
##### Compound indexes can support queries that match on multiple fields
## Syntax
	db.collection_name.createIndex( { <field1>: <type>, <field2>: <type2>, ... } )
	
### Example
	db.products.createIndex( { "item": 1, "stock": 1 } )
##### The order of the fields listed in a compound index is important.

# MultiKey Index
##### To index a field that holds an array value, MongoDB creates an index key for each element in the array.
## Create Multikey Index
## Syntax
	db.coll.createIndex( { <field>: < 1 or -1 > } )
##### MongoDB automatically creates a multikey index if any indexed field is an array; you do not need to explicitly specify the multikey type.
##### You cannot create a compound multikey index if more than one to-be-indexed field of a document is an array. For example, consider a collection that contains the following document:
	{ _id: 1, a: [ 1, 2 ], b: [ 1, 2 ], category: "AB - both arrays" }
##### You cannot create a compound multikey index { a: 1, b: 1 } on the collection since both the a and b fields are arrays.

# Text Indexing
#####  Support text search queries on string content.(fields with string value or array of strings)
## Syntax
	db.COLLECTION_NAME.createIndex( { comments: "text" } )
### Example
	db.student_data.createIndex(
  	 {
    	 subject: "text",
    	 comments: "text"
   	 }
	 )
	 
# Hashed Indexes
##### Hashed indexes maintain entries with hashes of the values of the indexed field.
## Syntax
	db.collection.createIndex( { _id: "hashed" } )



