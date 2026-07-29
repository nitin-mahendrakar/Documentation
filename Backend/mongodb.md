
[ Linux : vim /etc/mongod.conf ]  
    -- its yaml file for mongod 
        set dbpath : $yourpath /mongodb/data 
        set logpath : $yourpath /mongod/logs/mongod.log 

[ cls command ]
    cls --> ye screen clear nahi karta sirf screen ko top pe le jaata hai.

[ Shell Helpers ] 
    --> 3 types of Shell Helpers ( use, show , it ) ;
        [ commands ]                Into Javascript 
            use <dbs>                   db.adminCommanda"listDatabases");
            db                     db.getSiblingDB('$dbname')
            show users                  db.getUsers();
            show roles                  db.getRoles(showBuiltinRoles : true);
            show log                    db.adminCommand({getlog : <logname>});
            show logs                   db.adminCommand({getlog : *});                    
            show collections            db.getCollectionNames(); 

[ db ] 
    db.getDB = test.getDB object 

[ mongosh ] 
    
    --> use <db>
        - select database
        [ manually select database ] 
            db = getSiblingDB('local')
            local> : now points local
            db = Mongo().getDB('local'); both works;


[ .mongoshrc ] 
    --> mongosh chalo hone se pehle ye file run hoti hai aur ye javascript engine based hai.     

    linux file localtion = /etc/.mongoshrc 
    use <db>
    db = new DB() : db har baar change hota hai jab <use db> karte ho 

    samjo mujhe dropDatabase() nahi chahiye band karna safe concern.

    DB.prototype.dropDatabase = ()=>{
                    console.log("can`t delete database");
                    }; : ab hamara wala function chalega.


[ CRUD OPERATIONS ] !IMPORTANT.
    Create  == write only  (insert,insertMany,insertOne) 
    Read    == read only 
    Update  == read + write only 
    delete  == read + write only 

    [ Create ]  ( insert,insertMany,insertOne ) 

        db.collection.insert( [...document ], {
                ordered : true | false ,
                writeConcern : <document>
        }; 
        
        db.collection.insertOne(<document>, {
                writeConcern : <document>
        }; 
        
        db.collection.insertMany === db.collection.insert.
        (dono same format) 

    
        [ ordered : true | false ]
            default : true 
            _id = kisi bhi other document me match nahi honi chahiye.
            
            adb.collection.insertMany(<document>,<doc2>,<doc3> )
            
            [ ordered : true situation ]
            doc1.id == doc2.id --> same hai to doc3 write nahi hoga 
            [ ordered : false ]
            
            in database : doc1 onnly inserted        

            doc1.id === doc2.id --> same nahi hai to write mat karo.
            but doc3 ki id different hai write karo 

            in database : doc1, doc3 only inserted.


        [ Operations ]
        
            [ insertOne ]     
            db.collection.insertOne({ _id : 1,  name : guru})
            result : 
                { acknowleged : true ,insertedId : $uniqueid }

            [ error ]        
            db.collection.insertOne( { _id : 1 , name : 'nitin' }) : _id dubplicate 
            result : 
                MongoServerError: E11000 duplicate key error collection: list.user index:
                {_id : 1 } 

            
            [ insertMany ] 
            list> db.user.insertMany({name : 'guru'});
            MongoInvalidArgumentError: Argument "docs" must be an array of documents

            list> db.user.insertMany([{ name: 'guru' }])
            {       
                acknowledged: true,
                insertedIds: { '0': ObjectId('6a692dc8de8db12a4106c089') }
            }
            
            list> db.user.insertMany([{ name: 'guru' },{name : "nitin"}])
            {
                acknowledged: true,
                insertedIds: {
                    '0': ObjectId('6a692df5de8db12a4106c08a'),
                    '1': ObjectId('6a692df5de8db12a4106c08b')
                }
            }

            [ error ]


            list> db.user.insertMany([{_id : 10, name: 'guru' },{_id : 10 , name : "nitin"}])
            Uncaught: {
                MongoBulkWriteError: E11000 duplicate key error collection: list.user index: _id_ dup key: { _id: 10 }
                Result: BulkWriteResult {
                insertedCount: 1,
                matchedCount: 0,
                modifiedCount: 0,
                deletedCount: 0,
                upsertedCount: 0,
                upsertedIds: {},
                insertedIds: { '0': 10 }
                }
                Write Errors: [
                    WriteError {
                        err: {
                        index: 1,
                        code: 11000,
                        errmsg: 'E11000 duplicate key error collection: list.user index: _id_ dup key: { _id: 10 }',
                        errInfo: undefined,
                        op: { _id: 10, name: 'nitin' }
                        }
                    }
                ]
            }

    [ Read Operations ] (find, findOne)

        [ Returns ]
            find() kuch bhi match nahi huva to = ""
            findOne() kuch bhi match nahi huva to = null;

        db.collection.find(query,projection)
        db.collection.findOne(query,projection)

        projection = [ $,$elemMatch,$slice,$meta] = (topics hai )

            db.user.find({},{
                slice : 2 
            })
        
        [ Comparison Selectors ] = ($eq,$gt,$gte,$in,$lt,$lte,$ne,$nin,$size)

            for list = [ $in : <field> or [ <field,</field> ] , $nin , $size , $all  , $slice ]

            for string = $eq, $ne
            for number = $gt,$gte,$lt,$lte
"n
            [ how to use ]
            db.user.find({ <field>: { <$selector> : <value> } })
        

        [ Logical Operations ] = $and,$or,$not

        
            db.user.find( $and  : [ { <field> : {$eq  : <value>}}, { <field> : {$ne  : <value>}} ] )
            db.user.find( $or  : [ { <field> : {$eq  : <value>}}, { <field> : {$ne  : <value>}} ] )
            db.user.find( $not  : [ { <field> : {$eq  : <value>}}, { <field> : {$ne  : <value>}} ] )


        [ Element Selectors ] = $exists , $type 

            $exists : true | false 

            db.user.find({name : {$exists : true }}) : ye field hai to return karo 

        [ Operations ]

            db.user.find({},{_id : 0}) = _id ki <field> mat do.
            db.user.find().sort({name : value}).limit(5).skip(1)
        
        [ ArrayREADQueries ]

            db.user.find({ cities : {$all : ["mumbai","pune"]}}) = ye dono me konse bhi ho dedo 
            db.user.find({{cities : "mumbai" }}) = kisi bhi list me ye hai to do 
            db.user.find({ciites : {$size : 2}})  = jo list ki length 2 hai o do 
            db.user.find({})
            db.user.find({},{slice : 2}) = sirf list ke 2 values do 
            db.user.find().countDocuments({name : "guru"}) --> result <interger>

            db.user.find().toArray() = list returns.
            db.user.find() = iterator 



            $elemMatch (important)
            {
                vehicle : "toyota, 
                models : [
                    {gearbox : "auto"},
                    {gearbox : "cvc"},
                    {gearbox : "auto"}
                ]
            }
            list ke andar jo object hai. uske andar field comparison ke liye $elemMatch

            db.vehicles.find(
                {models : {$elemMatch : {gearbox : "auto"}} }
            )

            [ sirf elemMatch value dhikhao ]
            db.vehicles.find(
                {models : {$elemMatch : {gearbox : "auto"}}},
                {$models.$ : true}
            ) 

            
            [ another way to do it easy ]

        [ Projection ]

        {
                vehicle : "toyota, 
                models : [
                    {gearbox : "auto"},
                    {gearbox : "cvc"},
                    {gearbox : "auto"}
                ]
            }

        db.vehicles.find({},{$slice : 2, $elemMatch : "auto" });
            result : 
                {
                    vehicle : "toyota, 
                    models : [
                        {gearbox : "auto"},
                        {gearbox : "auto"}
                    ]
            }
            
        
        [ collation Operations ]

            [ strength ]
                1 = case insensitive and stylish text ok 
                2 = case sensitive but stylish text match nahi hoga.

            [ Case Level ] = a != A

            db.user.find().collection({
                locale : "en",
                strength : interger
            })



    [ Update ] = (updateOne,O)

        db.user.update()

        
        [ Update Operators ]

            $currentDate, $inc, $min , $max, $mul , $rename , $set , $setOnInsert , $unset 


            db.collelction.updateOne and UpdateMany(
                <query>,
                <update_value>,
                {
                    upsert : boolean,
                    writeConcern : document,
                    collation : document,
                    arrayFilters : [ filter documents.. ]
                    hint : document : string 
                }
            )

            [ Upsert ] = jab kuch bhi match na ho tab upsert chalta hai 

            collection {
                {name : "guru},
                {name : "nitin"}
            }

            db.user.updateOne({
                name : "gu",
                {$setToAdd : {pro : true }},
                {upsert : true}
            }) = upsert : true = gu match nahi huva to ek naya document banayega 

               new document added =  {name : "gu", pro : true}

            [ Operations ]
                collection {
                    {name : "guru",count : 0},
                    {name : "nitin",count : 0}
                }

                [ $set ]
                db.user.updateOne({
                    name : "guru",
                    {$set : {name: "guru1} }
                }) 
                result : 
                    collection {
                    {name : "guru1",count : 0},
                    {name : "nitin",count : 0}
                }

                [ without $set ]
                db.user.updateOne({ = $set opeartor ke bina saare fields remove hogaye 
                    name : "guru",
                    {name: "guru1 }
                }) 
                result :  = all fields removed 
                    collection {
                    {name : "guru1"},
                    {name : "nitin",count : 0}
                }

                [$unset]
                db.user.updateOne({
                    name : "guru"
                },{$unset : {name : ""}})

                result : = name removed 
                    collection {
                    {count : 0},
                    {name : "nitin",count : 0}
                }

                [ $rename ]

                    db.user.updateOne({
                        name : "guru"
                    },{$rename : {name : myname}})

                    result : = name removed 
                        collection {
                        {myname : "guru",count : 0},
                        {name : "nitin",count : 0}
                    }



        [ Remove Operations ] = (pop pull pullAll)
            [$pop]

                db.user.updateOne({
                    name : "guru"
                },{$pop : { cities : 1}}) = cities me se 1 field chala gaya 


            [ $pull ]
                db.user.updateOne({
                },{$pull : { rank : {$lte : 10 }}}) = rank < 10 niche ho rahe sab remove hogaye 


            [ $pullAll]
                db.user.updateOne({
                },{$pullAll : { cities : ["london","mumbai"] }}) = london  and mumbai remove hogaye 

        [ Add Operations ] = (push,each,postion,sort)

            db.user.updateOne({
                name : "guru",
                {$push : {cities : "london" }}
            })

            
            (mutiple insertions)
            db.user.updateOne({
                name : "guru",
                {$push : {cities : {$each : {cities : ["london","mumbai"]}} }}
            })


            db.user.updateOne({ = index 1 se append honge
                name : "guru",
                {$push : {cities : {$each : {cities : ["london","mumbai"]},$position : 1} }}
            })

            db.user.updateOne({ = sort karo and update karo  
                name : "guru",
                {$push : {cities : {$each : {rank : [],sort : -1 } }} }
            })

            db.user.updateOne({ = index 1 se append honge
                name : "guru",
                {$push : {cities : {$AddToSet : {$each : {cities : ["london","mumbai"]},$position : 1}} }}
            })



        [ Replace IN List Operations ]

        cities = ["london',"london",bhalki]
        db.user.updateOne({name : "guru",cities : "london", {$set : {cities.$ : "UKcapital"}}})

        result : cities = ["UKcapital',"london",bhalki] = first one replaced



        cities = ["london',"ok",bhalki]
        db.user.updateOne({name : "guru",cities : "london", {$set : {cities.$[capital] : "UKcapital"},{capital : "london"} }})

        result = cities = ["london',"london",london]





    [ Indexs ]


        db