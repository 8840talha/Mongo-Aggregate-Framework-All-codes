# Mongo-Aggregate-Framework-All-codes
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
