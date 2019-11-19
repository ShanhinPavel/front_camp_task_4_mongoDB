**4**
**4.1**
**Query**:
db.airlines.aggregate([{$group:{_id:"$class",total:{$sum:1}}},{$project:{class:"$_id",total:"$total",_id:0}}])

**Result**:
{ "class" : "F", "total" : 140343 }
{ "class" : "G", "total" : 17499 }
{ "class" : "L", "total" : 23123 }
{ "class" : "P", "total" : 5683 }

**4.2**
**Query**:
db.airlines.aggregate([{$match:{ destCountry:{$ne:"United States"} }},{$group:{_id:"$destCity", avgPassengers:{$avg:"$passengers"}}},{$sort:{avgPassengers:-1}},{$limit:3},{$project:{city:"$_id", avgPassengers:"$avgPassengers",_id:0}}])

**Result**:
{city:”Abu Dhabi, United Arab Emirates”, avgPassengers: 8052.3809}
{city: “Dubai, United Arab Emirates”, avgPassengers :7176,59}
{city:”Guangzhou, China”, avgPassengers : 7103,33}

**4.3**
**Query:**
db.airlines.aggregate( [{$match: { destCountry:"Latvia" }}, {$group: { _id: "$destCountry", carriers: { $push: "$carrier" } }}])

**Result:**
{ "\_id" : "Latvia",
"carriers" : [ "JetClub AG", "Blue Jet SP Z o o", "Uzbekistan Airways", "Uzbekistan Airways", "Uzbekistan Airways", "Uzbekistan Airways", "Uzbekistan Airways", "Uzbekistan Airways","Uzbekistan Airways" ] }

**4.4**
**Query:**
db.airlines.aggregate( [{$match: {  originCountry:"United States",  destCountry: {$in:["Spain","Greece","Italy"]}, }}, {$group: {  _id: "$carrier", total: { $sum: "$passengers" } }}, {$sort: {  total: -1 }}, {$limit: 10}, {\$skip: 3}])

**Result:**
{ "\_id" : "Compagnia Aerea Italiana", "total" : 280256 }
{ "\_id" : "United Air Lines Inc.", "total" : 229936 }
{ "\_id" : "Emirates", "total" : 100903 }
{ "\_id" : "Air Europa", "total" : 94968 }
{ "\_id" : "Meridiana S.p.A", "total" : 20308 }
{ "\_id" : "Norwegian Air Shuttle ASA", "total" : 13344 }
{ "\_id" : "VistaJet Limited", "total" : 183 }

**4.5**
**Query**:
db.airlines.aggregate( [{$match: { originCountry: 'United States' }}, {$group: { _id: { originState: "$originState", originCity: "$originCity" }, passengers: { $sum: "$passengers" } }}, {$sort: { passengers: -1 }},{limit: 5} {$sort: { "_id.originState": 1 }}, {$project: { totalPassengers:"$passengers",location:{state:"$_id.originState", city:"$_id.originCity"}, _id:0 }}])

**Result:**
{ "totalPassengers" : 760120, "location" : { "city" : "Birmingham, AL", "state" : "Alabama" } }
{ "totalPassengers" : 1472404, "location" : { "city" : "Anchorage, AK", "state" : "Alaska" } }
{ "totalPassengers" : 13152753, "location" : { "city" : "Phoenix, AZ", "state" : "Arizona" } }
{ "totalPassengers" : 571452, "location" : { "city" : "Little Rock, AR", "state" : "Arkansas" } }
{ "totalPassengers" : 23701556, "location" : { "city" : "Los Angeles, CA", "state" : "California" } }

**5.**
**Query:**
db.enron.aggregate([{$project: {  from:"$headers.From", to: { "$setUnion": [ "$headers.To", [] ] } }}, {$unwind: {  path: "$to", }}, {$group: {  _id: {from: "$from", to: "$to"},  total: {  $sum: 1 } }}, {$sort: {  total: -1 }}, {$limit: 1}])

**Result:**
{ "\_id" : { "from" : "susan.mara@enron.com", "to" : "jeff.dasovich@enron.com" }, "total" : 750 }
