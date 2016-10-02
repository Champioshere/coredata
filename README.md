# coredata

import UIKit
import CoreData
class Model: NSObject {
    
// let managedObjectContext = GlobalShared.sharedInstance.appDel!.managedObjectContext
    
    let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext

   
    func create(createWithRecipeName recipeName: String, recipeDecp: String, imgData: String, recipeType: String)
    {
        
        let recipesList = NSEntityDescription.insertNewObjectForEntityForName("Recipes", inManagedObjectContext: self.managedObjectContext) as! Recipes
        
        recipesList.setValue(recipeName, forKey: "recipeName")
        recipesList.setValue(recipeDecp, forKey: "recipeDesp")
        recipesList.setValue(imgData, forKey: "imgData")
        recipesList.setValue(recipeType, forKey: "recipeType")
        
        
        do {
            try recipesList.managedObjectContext?.save()
            
        } catch let error as NSError  {
            print("Could not save \(error), \(error.userInfo)")
        }
        
        
    }
    
    
    
   
 //MARK: - Create Ingredients Master
    func createIngredientsMasterTable()
    {
        
       
        
        let dictOnion = ["id":"1","name":"Onion","type":"0"]
        let dictGarlic = ["id":"2","name":"Garlic","type":"0"]
        let dictMushroom = ["id":"3","name":"Mushroom","type":"0"]
        
        let dictChicken = ["id":"4","name":"Chicken","type":"1"]
        let dictBeef = ["id":"5","name":"Beef","type":"1"]
        let dictPork = ["id":"6","name":"Pork","type":"1"]
        let dictEgg = ["id":"7","name":"Egg","type":"1"]
        let dictMutton = ["id":"8","name":"Mutton","type":"1"]
        let dictLamp = ["id":"9","name":"Lamb","type":"1"]
        let dictFish = ["id":"10","name":"Fish","type":"1"]
        let dictPrawns = ["id":"11","name":"Prawns","type":"1"]
        let dictCrab = ["id":"12","name":"Crab","type":"1"]
        
        var arrayMain = [dictOnion ,dictGarlic, dictMushroom, dictChicken, dictBeef, dictPork, dictEgg, dictMutton, dictLamp,dictFish,dictPrawns,dictCrab]
        
        for i in 0 ..< arrayMain.count
        {
            let recipesList = NSEntityDescription.insertNewObjectForEntityForName("RecipeIngredients", inManagedObjectContext: self.managedObjectContext) as! RecipeIngredients
        
            var  dict = arrayMain[i]
            recipesList.setValue(dict["id"], forKey: "id")
            recipesList.setValue(dict["name"], forKey: "name")
            recipesList.setValue(dict["type"], forKey: "type")
            
            do {
                try recipesList.managedObjectContext?.save()
                
            } catch let error as NSError  {
                print("Could not save \(error), \(error.userInfo)")
            }
        
        }
        
        
    }
    
//MARK: - Insert Recipe
    
    func insertRecipe(RecipeData : RecipeObj)
    {
        ////CHECK IF RECIPE ALREADY EXISTS/////////////////////
        var arrRecipeNames : Array<Any>!
        var arrRecipes : [String] = []
        //GlobalShared.sharedInstance.appDel!.objModel!.loadAllRecipe()
        arrRecipeNames = loadAllRecipe()
        for i in 0  ..< arrRecipeNames.count
        {
            let obj = arrRecipeNames[i] as! Recipes
            arrRecipes.append(obj.recipeName!)
        }
        if (!arrRecipes.contains(RecipeData.strRecipeName!))
        {
        /////////////////////////////////////////////////////////
        
        
    //////////////////////////////////////INSERT RECIPE//////////////////////////////////////
         let recipesList = NSEntityDescription.insertNewObjectForEntityForName("Recipes", inManagedObjectContext: self.managedObjectContext) as! Recipes
        
       recipesList.setValue(RecipeData.strRecipeName, forKey: "recipeName")
        recipesList.setValue(RecipeData.strRecipeDesp, forKey: "recipeDesp")
        recipesList.setValue(RecipeData.strImgData, forKey: "imgData")
        recipesList.setValue(RecipeData.strRecipeType, forKey: "recipeType")
        recipesList.setValue(RecipeData.strBreakFast, forKey: "breakFast")
        recipesList.setValue(RecipeData.strLunch, forKey: "lunch")
        recipesList.setValue(RecipeData.strDinner, forKey: "dinner")
        recipesList.setValue(RecipeData.strSnacks, forKey: "snacks")
        recipesList.setValue(RecipeData.strNotificationDate, forKey: "notificationDate")
        recipesList.setValue(RecipeData.strRepeatitionDays, forKey: "repeatitionDays")
         recipesList.setValue(RecipeData.strOptional, forKey: "optional")
            
      

        
      
        do {
            try managedObjectContext.save()
            
        } catch let error as NSError  {
            print("Could not save \(error), \(error.userInfo)")
        }
        
        //Now get the recipe Id and insert recipe iD with Ingredient id to ingredients Added table
        
        
        let string = recipesList.objectID.URIRepresentation().absoluteString
       print("lAST ID")
        let path : NSString!
        path = string
        print(path.lastPathComponent)
        
      // let strID = path.lastPathComponentpath
          recipesList.setValue(path.lastPathComponent, forKey: "recipeid")
        do {
            try managedObjectContext.save()
            
        } catch let error as NSError  {
            print("Could not save \(error), \(error.userInfo)")
        }
        
       
        
        let recipeId = path.lastPathComponent
        //var arrayIngredients = ["1","2"];
        for i in 0  ..< RecipeData.arrIngredintsNames.count
        {
        
          let ingredientsAdded = NSEntityDescription.insertNewObjectForEntityForName("RecipeIngredientsAdded", inManagedObjectContext: self.managedObjectContext) as! RecipeIngredientsAdded
            
            ingredientsAdded.setValue(RecipeData.arrIngredintsID[i], forKey: "ingredientId")
            ingredientsAdded.setValue(RecipeData.arrIngredintsNames[i], forKey: "ingredientName")
            ingredientsAdded.setValue(recipeId, forKey: "recipeId")
            
            do {
                try ingredientsAdded.managedObjectContext?.save()
                
            } catch let error as NSError  {
                print("Could not save \(error), \(error.userInfo)")
            }

           
        
        }
        //////////////////////////////////////////////////////////////////////////////////
        
    }
        else
       {
              print("Already exists \(RecipeData.strRecipeName)")
       }
    
   }
    
    
    
    
    //MARK: - Update Recipe
    
    func updateRecipe(RecipeData : RecipeObj)
    {
        
        let predicate = NSPredicate(format: "recipeid == %@", RecipeData.strRecipeId!)
        
        let fetchRequest = NSFetchRequest(entityName: "Recipes")
        fetchRequest.predicate = predicate
        
        do {
            let fetchedEntities = try self.managedObjectContext.executeFetchRequest(fetchRequest) as! [Recipes]
            
            fetchedEntities.first?.recipeName = RecipeData.strRecipeName
            fetchedEntities.first?.recipeType = RecipeData.strRecipeType
            fetchedEntities.first?.breakFast = RecipeData.strBreakFast
            fetchedEntities.first?.lunch = RecipeData.strLunch
            fetchedEntities.first?.dinner = RecipeData.strDinner
            fetchedEntities.first?.snacks = RecipeData.strSnacks
            fetchedEntities.first?.repeatitionDays = RecipeData.strRepeatitionDays
            fetchedEntities.first?.optional = RecipeData.strOptional
          //  fetchedEntities.first?.notificationDate = RecipeData.strNotificationDate
            if(RecipeData.strRecipeDesp != nil){
                fetchedEntities.first?.recipeDesp = RecipeData.strRecipeDesp
            }
             if(RecipeData.strImgData != nil){
            fetchedEntities.first?.imgData = RecipeData.strImgData
            }
          
            
            // ... Update additional properties with new values
        } catch {
            // Do something in response to error condition
        }
        
        do {
            try self.managedObjectContext.save()
        } catch {
            // Do something in response to error condition
        }
        
        /////////DELETE OLD ENTRIES //////////////////////
        
     
           
            
            let predicateDelete = NSPredicate(format:"recipeId == %@", RecipeData.strRecipeId!)
            let fetchRequestDelete = NSFetchRequest(entityName: "RecipeIngredientsAdded")
            fetchRequestDelete.predicate = predicateDelete
        
        
            do {
                let  result = try self.managedObjectContext.executeFetchRequest(fetchRequestDelete)
                if(result.count > 0)
                {
                    for i in 0 ..< result.count
                    {
                        self.managedObjectContext.deleteObject(result[i] as! NSManagedObject)
                        print("DELETING")
                    }
                    
                }
            } catch {
                // Do something in response to error condition
            }
            
            do {
                try self.managedObjectContext.save()
            } catch {
                // Do something in response to error condition
            }

            
       
        
       
        
       ///////////////////////////////////////////////////
        
        
        let recipeId = RecipeData.strRecipeId!
        //var arrayIngredients = ["1","2"];
        for i in 0  ..< RecipeData.arrIngredintsNames.count
        {
            
            let ingredientsAdded = NSEntityDescription.insertNewObjectForEntityForName("RecipeIngredientsAdded", inManagedObjectContext: self.managedObjectContext) as! RecipeIngredientsAdded
            
            ingredientsAdded.setValue(RecipeData.arrIngredintsID[i], forKey: "ingredientId")
            ingredientsAdded.setValue(RecipeData.arrIngredintsNames[i], forKey: "ingredientName")
            ingredientsAdded.setValue(recipeId, forKey: "recipeId")
            
            do {
                try ingredientsAdded.managedObjectContext?.save()
                
            } catch let error as NSError  {
                print("Could not save \(error), \(error.userInfo)")
            }
            
            
            
        }
        
        
    }
    
       //MARK: - Delete Recipe
    
    func deleteRecipe(RecipeName:String){
    
        let predicate = NSPredicate(format: "recipeName == %@", RecipeName)
        
        let fetchRequest = NSFetchRequest(entityName: "Recipes")
        fetchRequest.predicate = predicate
        
        do {
            let fetchedEntities = try self.managedObjectContext.executeFetchRequest(fetchRequest) as! [Recipes]
            if let entityToDelete = fetchedEntities.first {
                self.managedObjectContext.deleteObject(entityToDelete)
            }
        } catch {
            // Do something in response to error condition
        }
        
        do {
            try self.managedObjectContext.save()
        } catch {
            // Do something in response to error condition
        }
        
    }


    
    
    
    //MARK: - Delete Days
    
    func deleteDays(){
        //Clear Entire Table Before inserting!!
        
        
        if #available(iOS 9.0, *) {
            // iOS 9.0 & above
            
            ////iOS 9 & above
            let fetchRequest = NSFetchRequest(entityName: "ManageDays")
            let deleteRequest = NSBatchDeleteRequest(fetchRequest: fetchRequest)
            
            do {
                try self.managedObjectContext.executeRequest(deleteRequest)
            } catch let error as NSError {
                
                print("Could not Delete \(error), \(error.userInfo)")
            }
            
            
            ////////////////////////////////////////

          
        } else {
            // Fallback on earlier versions
            
            let fetchRequest = NSFetchRequest()
            let entityDescription = NSEntityDescription.entityForName("ManageDays", inManagedObjectContext: self.managedObjectContext)
            fetchRequest.entity = entityDescription
            
            do {
                let  result = try self.managedObjectContext.executeFetchRequest(fetchRequest)
                if(result.count > 0)
                {
                    for i in 0 ..< result.count
                    {
                        self.managedObjectContext.deleteObject(result[i] as! NSManagedObject)
                        print("DELETING")
                    }
                    
                }
                
            }
            catch let error as NSError  {
                print("Could not save \(error), \(error.userInfo)")
            }
            


        }
    }
    
    
    //MARK: - Insert Days
    func insertDays(insertDaysWithIngredients strDay: String, andArrayIngredientsID: [String], andIngredientsNames: [String], andrecipeCat: Int, andValueArr: [String] )
    {
        print("INSERT DAYS!!")
        print(strDay)
        print(andArrayIngredientsID)
        print(andIngredientsNames)
        print(andrecipeCat)
        
        ///////////////////////////CHECK IF DAY EXISTS IN TABLE///////////////////////////////////
        let fetchRequest = NSFetchRequest()
        let entityDescription = NSEntityDescription.entityForName("ManageDays", inManagedObjectContext: self.managedObjectContext)
        fetchRequest.entity = entityDescription
        fetchRequest.predicate = NSPredicate(format: "day == %@", strDay)
       // var yourArray: Array <Any>
       // yourArray = []
        do {
            let  result = try self.managedObjectContext.executeFetchRequest(fetchRequest)
            print("Manage days \(strDay) result count : \(result.count)")
                    if(result.count > 0)
                    {
                        for i in 0 ..< result.count
                        {
                             self.managedObjectContext.deleteObject(result[i] as! NSManagedObject)
                            print("DELETING")
                        }
                        
                        
            
                    }
            
            
        }
        
        catch let error as NSError  {
            print("Could not save \(error), \(error.userInfo)")
        }

        
            
            
           
        //////////////////////////////////////////////////////////////////////////////////////////
        
        
        /////////////////////////////////////////INSERT DAYS//////////////////////////////////////
        for i in 0  ..< andIngredientsNames.count
        {
            
            let daysAdded = NSEntityDescription.insertNewObjectForEntityForName("ManageDays", inManagedObjectContext: self.managedObjectContext) as! ManageDays            
            
            daysAdded.setValue(strDay, forKey: "day")
            daysAdded.setValue(andIngredientsNames[i], forKey: "ingredientName")
            daysAdded.setValue(andArrayIngredientsID[i], forKey: "ingredientId")
            daysAdded.setValue(String(andrecipeCat) , forKey: "type")
            daysAdded.setValue(andValueArr[i] , forKey: "yes")
            
            do {
                try daysAdded.managedObjectContext?.save()
                
            } catch let error as NSError  {
                print("Could not save \(error), \(error.userInfo)")
            }
            
            
            
        }
        //////////////////////////////////////////////////////////////////////////////////////////


    }
    
    
    
    //MARK: - Delete Days
    func deleteDays(deleteDaysWithIngredients strDay: String)
    {
        print("DELETE DAYS!!")
      
        ///////////////////////////CHECK IF DAY EXISTS IN TABLE///////////////////////////////////
        let fetchRequest = NSFetchRequest()
        let entityDescription = NSEntityDescription.entityForName("ManageDays", inManagedObjectContext: self.managedObjectContext)
        fetchRequest.entity = entityDescription
        fetchRequest.predicate = NSPredicate(format: "day == %@", strDay)
        // var yourArray: Array <Any>
        // yourArray = []
        do {
            let  result = try self.managedObjectContext.executeFetchRequest(fetchRequest)
            print("Manage days \(strDay) result count : \(result.count)")
            if(result.count > 0)
            {
                for i in 0 ..< result.count
                {
                    self.managedObjectContext.deleteObject(result[i] as! NSManagedObject)
                    print("DELETING")
                }
                
                
                
            }
            
            
        }
            
        catch let error as NSError  {
            print("Could not save \(error), \(error.userInfo)")
        }
        
        
        
        
        
        //////////////////////////////////////////////////////////////////////////////////////////
        
        
//        /////////////////////////////////////////INSERT DAYS//////////////////////////////////////
//        for i in 0  ..< andIngredientsNames.count
//        {
//            
//            let daysAdded = NSEntityDescription.insertNewObjectForEntityForName("ManageDays", inManagedObjectContext: self.managedObjectContext) as! ManageDays
//            
//            daysAdded.setValue(strDay, forKey: "day")
//            daysAdded.setValue(andIngredientsNames[i], forKey: "ingredientName")
//            daysAdded.setValue(andArrayIngredientsID[i], forKey: "ingredientId")
//            daysAdded.setValue(String(andrecipeCat) , forKey: "type")
//            daysAdded.setValue(andValueArr[i] , forKey: "yes")
//            
//            do {
//                try daysAdded.managedObjectContext?.save()
//                
//            } catch let error as NSError  {
//                print("Could not save \(error), \(error.userInfo)")
//            }
//            
//            
//            
//        }
        //////////////////////////////////////////////////////////////////////////////////////////
        
        
        
        
    }

    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    //MARK: - Update Festival Days
    func updateFestivalDays(andArrayFestivalIDs: [String], andValues: [String]){
        
        
        for i in 0 ..< andArrayFestivalIDs.count
        {
            let predicate = NSPredicate(format: "id == %@", andArrayFestivalIDs[i])
            
            let fetchRequest = NSFetchRequest(entityName: "Festivals")
            fetchRequest.predicate = predicate
            
            do {
                let fetchedEntities = try self.managedObjectContext.executeFetchRequest(fetchRequest) as! [Festivals]
               // fetchedEntities.first?.FirstPropertyToUpdate = NewValue
               // fetchedEntities.first?.SecondPropertyToUpdate = NewValue
                fetchedEntities.first?.seleted = andValues[i]
                // ... Update additional properties with new values
            } catch {
                // Do something in response to error condition
            }
            
            do {
                try self.managedObjectContext.save()
            } catch {
                // Do something in response to error condition
            }
     
        }
    }
    //MARK: - Delete Festivals Days
    
    func deleteFestivalsDays(){
        //Clear Entire Table Before inserting!!
        
        
        if #available(iOS 9.0, *) {
            // iOS 9.0 & above
            
            ////iOS 9 & above
            let fetchRequest = NSFetchRequest(entityName: "Festivals")
            let deleteRequest = NSBatchDeleteRequest(fetchRequest: fetchRequest)
            
            do {
                try self.managedObjectContext.executeRequest(deleteRequest)
            } catch let error as NSError {
                
                print("Could not Delete \(error), \(error.userInfo)")
            }
            
            
            ////////////////////////////////////////
            
            
        } else {
            // Fallback on earlier versions
            
            let fetchRequest = NSFetchRequest()
            let entityDescription = NSEntityDescription.entityForName("Festivals", inManagedObjectContext: self.managedObjectContext)
            fetchRequest.entity = entityDescription
            
            do {
                let  result = try self.managedObjectContext.executeFetchRequest(fetchRequest)
                if(result.count > 0)
                {
                    for i in 0 ..< result.count
                    {
                        self.managedObjectContext.deleteObject(result[i] as! NSManagedObject)
                        print("DELETING")
                    }
                    
                }
                
            }
            catch let error as NSError  {
                print("Could not save \(error), \(error.userInfo)")
            }
            
            
            
        }
    }
    
    
    
    
    
    
    

     //MARK: - Insert Repeatation Days & Same Lunch Dinner Value
    func insertRepeatDaysAndSameLunchDinner(strRepeatDays: String, strLunchDinner: String, strCount: String)
    {
        if(strCount == "0")
        {
        
        let repeatDaysList = NSEntityDescription.insertNewObjectForEntityForName("RepeatationDays", inManagedObjectContext: self.managedObjectContext) as! RepeatationDays
        
        repeatDaysList.setValue(strRepeatDays, forKey: "value")
        repeatDaysList.setValue(strLunchDinner, forKey: "sameLunchDinner")
        do {
            try managedObjectContext.save()
            
        } catch let error as NSError  {
            print("Could not save \(error), \(error.userInfo)")
        }
        }
     else
        {
            
          //  let predicate = NSPredicate(format: "id == %@", andArrayFestivalIDs[i])
            
            let fetchRequest = NSFetchRequest(entityName: "RepeatationDays")
           // fetchRequest.predicate = predicate
            
            do {
                let fetchedEntities = try self.managedObjectContext.executeFetchRequest(fetchRequest) as! [RepeatationDays]
                // fetchedEntities.first?.FirstPropertyToUpdate = NewValue
                // fetchedEntities.first?.SecondPropertyToUpdate = NewValue
                fetchedEntities.first?.value = strRepeatDays
                fetchedEntities.first?.sameLunchDinner = strLunchDinner
                
                // ... Update additional properties with new values
            } catch {
                // Do something in response to error condition
            }
            
            do {
                try self.managedObjectContext.save()
            } catch {
                // Do something in response to error condition
            }

            
        }
        
    }
    
    
    
    
    
    
    //MARK: - Insert Repeatation Days 
    func insertRepeatDays(strRepeatDays: String)
    {
        
            let repeatDaysList = NSEntityDescription.insertNewObjectForEntityForName("RepeatationDays", inManagedObjectContext: self.managedObjectContext) as! RepeatationDays
            
            repeatDaysList.setValue(strRepeatDays, forKey: "value")
        
        repeatDaysList.setValue("0", forKey: "sameLunchDinner")
        repeatDaysList.setValue("08:00", forKey: "strTime")
        repeatDaysList.setValue("0", forKey: "strTodayTomm")
        repeatDaysList.setValue("08:00 AM", forKey: "strTimeTwelveFormat")
        
            do {
                try managedObjectContext.save()
                
            } catch let error as NSError  {
                print("Could not save \(error), \(error.userInfo)")
            }
        }
    
    


    
    //MARK: - Update Repeatation Days
 /*   func UpdateRepeatDays(strRepeatDays: String, strTime: String, strPriorNotifications: String)
    {
        
            //  let predicate = NSPredicate(format: "id == %@", andArrayFestivalIDs[i])
            
            let fetchRequest = NSFetchRequest(entityName: "RepeatationDays")
            // fetchRequest.predicate = predicate
            
            do {
                let fetchedEntities = try self.managedObjectContext.executeFetchRequest(fetchRequest) as! [RepeatationDays]
                // fetchedEntities.first?.FirstPropertyToUpdate = NewValue
                // fetchedEntities.first?.SecondPropertyToUpdate = NewValue
                fetchedEntities.first?.value = strRepeatDays
                fetchedEntities.first?.strTime = strTime
                fetchedEntities.first?.strTodayTomm = strPriorNotifications
                
                
                // ... Update additional properties with new values
            } catch {
                // Do something in response to error condition
            }
            do {
                try self.managedObjectContext.save()
            } catch {
                // Do something in response to error condition
            }
             }
*/
    
    
    //MARK: - Update Repeatation Days
    func UpdateRepeatDays(strRepeatDays: String, strTime: String, strPriorNotifications: String, strTimeTwelve: String)
    {
        
        //  let predicate = NSPredicate(format: "id == %@", andArrayFestivalIDs[i])
        
        let fetchRequest = NSFetchRequest(entityName: "RepeatationDays")
        // fetchRequest.predicate = predicate
        
        do {
            let fetchedEntities = try self.managedObjectContext.executeFetchRequest(fetchRequest) as! [RepeatationDays]
            // fetchedEntities.first?.FirstPropertyToUpdate = NewValue
            // fetchedEntities.first?.SecondPropertyToUpdate = NewValue
            fetchedEntities.first?.value = strRepeatDays
            fetchedEntities.first?.strTime = strTime
            fetchedEntities.first?.strTodayTomm = strPriorNotifications
            fetchedEntities.first?.strTimeTwelveFormat = strTimeTwelve
            
            
            // ... Update additional properties with new values
        } catch {
            // Do something in response to error condition
        }
        do {
            try self.managedObjectContext.save()
        } catch {
            // Do something in response to error condition
        }
    }
    

   
    func loadRepeatDaysSameLunchValue()-> Array<String>
        //    func loadIngredients()
    {
        
        
    var yourArray: Array <String>
         yourArray = []
        
        let fetchRequest = NSFetchRequest(entityName: "RepeatationDays")
        
        do {
            let fetchedEntities = try self.managedObjectContext.executeFetchRequest(fetchRequest) as! [RepeatationDays]
            // fetchedEntities.first?.FirstPropertyToUpdate = NewValue
            // fetchedEntities.first?.SecondPropertyToUpdate = NewValue
            if(fetchedEntities.count > 0)
            {
                var strLunchDinner = String()
                if (fetchedEntities.first?.sameLunchDinner) == nil{
                    strLunchDinner = "0"
                }
                else
                {
                     strLunchDinner = (fetchedEntities.first?.sameLunchDinner)! as String
                }
           
              yourArray.append((fetchedEntities.first?.value)!)
              yourArray.append(strLunchDinner)
                
                var strTimeDb = String()
                if (fetchedEntities.first?.strTime) == nil{
                    strTimeDb = "08:00"
                }
                else
                {
                    strTimeDb = (fetchedEntities.first?.strTime)! as String
                }

              yourArray.append(strTimeDb)
                
                
                var strTodayTommDb = String()
                if (fetchedEntities.first?.strTodayTomm) == nil{
                    strTodayTommDb = "0"
                }
                else
                {
                    strTodayTommDb = (fetchedEntities.first?.strTodayTomm)! as String
                }

                
              yourArray.append(strTodayTommDb)
                
                var strTimeDbTwelve = String()
                if (fetchedEntities.first?.strTimeTwelveFormat) == nil{
                    strTimeDb = "08:00 AM"
                }
                else
                {
                    strTimeDbTwelve = (fetchedEntities.first?.strTimeTwelveFormat)! as String
                }
                yourArray.append(strTimeDbTwelve)

            }
            
            // ... Update additional properties with new values
        } catch {
            // Do something in response to error condition
        }
        
        do {
            try self.managedObjectContext.save()
        } catch {
            // Do something in response to error condition
        }
        
        return yourArray
    }

    //MARK: - Load Ingredients List
    
    func loadIngredients(strVegNonVeg:String)->Array<Any>
     //    func loadIngredients()
    {
             
        
             // Initialize Fetch Request
        let fetchRequest = NSFetchRequest()
        
        // Create Entity Description
        let entityDescription = NSEntityDescription.entityForName("RecipeIngredients", inManagedObjectContext: self.managedObjectContext)
        
        // Configure Fetch Request
        fetchRequest.entity = entityDescription
        fetchRequest.predicate = NSPredicate(format: "type == %@", strVegNonVeg)
     //   var yourArray = <AnyObject>()
        var yourArray: Array <Any>
        yourArray = []
        do {
         let  result = try self.managedObjectContext.executeFetchRequest(fetchRequest)
            
            for i in 0  ..< result.count
            {
                 yourArray.append(result[i])
            }
            
         print(result)
       
            
            
        } catch {
            let fetchError = error as NSError
            print(fetchError)
        }
        
      
      return yourArray
        
    }

   //MARK: - Load Ingredients Count
     func loadIngredientsCount()->Int
     {
        
        var count: Int!
        // Initialize Fetch Request
        let fetchRequest = NSFetchRequest()
        // Create Entity Description
        let entityDescription = NSEntityDescription.entityForName("RecipeIngredients", inManagedObjectContext: self.managedObjectContext)
        // Configure Fetch Request
        fetchRequest.entity = entityDescription
        
        do {
            let  result = try self.managedObjectContext.executeFetchRequest(fetchRequest)
            count = result.count
            print(result)            
            
        } catch {
            let fetchError = error as NSError
            print(fetchError)
        }

        return count
    }
     //MARK: - load Recipe by RecipeName
    
    func loadAllRecipe()->Array<Any>
        //    func loadIngredients()
    {
        // Initialize Fetch Request
        let fetchRequest = NSFetchRequest()
        
        // Create Entity Description
        let entityDescription = NSEntityDescription.entityForName("Recipes", inManagedObjectContext: self.managedObjectContext)
        
        // Configure Fetch Request
        fetchRequest.entity = entityDescription
        //   var yourArray = <AnyObject>()
        var yourArray: Array <Any>
        yourArray = []
        do {
            let  result = try self.managedObjectContext.executeFetchRequest(fetchRequest)
            
            for i in 0  ..< result.count
            {
                yourArray.append(result[i])
            }
           // print(result)
        } catch {
            let fetchError = error as NSError
            print(fetchError)
        }
     return yourArray
        
    }
   
    //MARK: - load Recipe by Category
    
    func loadCategory(strCategory:String)->Array<Any>
        //    func loadIngredients()
    {
        
        
        // Initialize Fetch Request
        let fetchRequest = NSFetchRequest()
        
        // Create Entity Description
        let entityDescription = NSEntityDescription.entityForName("Recipes", inManagedObjectContext: self.managedObjectContext)
        
        // Configure Fetch Request
        fetchRequest.entity = entityDescription
        if (strCategory == "breakFast"){
            fetchRequest.predicate = NSPredicate(format: "breakFast == 1")
        }
       else  if (strCategory == "lunch"){
            fetchRequest.predicate = NSPredicate(format: "lunch == 1")
        }
    
        else  if (strCategory == "dinner"){
            fetchRequest.predicate = NSPredicate(format: "dinner == 1")
        }
        else  if (strCategory == "snacks"){
            fetchRequest.predicate = NSPredicate(format: "snacks == 1")
        }
        
        let sortDescriptor = NSSortDescriptor(key:"recipeName", ascending: true)
        let sortDescriptors = [sortDescriptor]
        fetchRequest.sortDescriptors = sortDescriptors
        
        //   var yourArray = <AnyObject>()
        var yourArray: Array <Any>
        yourArray = []
        do {
            let  result = try self.managedObjectContext.executeFetchRequest(fetchRequest)
            
            for i in 0  ..< result.count
            //{
               // yourArray.append(result[i])
     
                
            //}
            
            {
                /// NOW FETCH THE INGREDIENTS OF RECIPES
                
                // Initialize Fetch Request
                let fetchRequest_ingredients = NSFetchRequest()
                
                // Create Entity Description
                
                
                
                let obj = result[i] as! Recipes as Recipes!
                
                let entityDescription_ingredients = NSEntityDescription.entityForName("RecipeIngredientsAdded", inManagedObjectContext: self.managedObjectContext)
                
                // Configure Fetch Request
                fetchRequest_ingredients.entity = entityDescription_ingredients
                fetchRequest_ingredients.predicate = NSPredicate(format:"recipeId == %@",obj.recipeid!)
                //fetchRequest_Recipes.predicate = NSPredicate(format:"recipeType == %@",strType)
                var arrRecipesIngredientsID: Array <String>
                arrRecipesIngredientsID = []
                var arrRecipesIngredientsNames: Array <String>
                arrRecipesIngredientsNames = []
                
                do {
                    let  result = try self.managedObjectContext.executeFetchRequest(fetchRequest_ingredients)
                    for i in 0  ..< result.count
                    {
                        let objResult = result[i] as! RecipeIngredientsAdded as RecipeIngredientsAdded!
                        arrRecipesIngredientsID.append(objResult.ingredientId!)
                        arrRecipesIngredientsNames.append(objResult.ingredientName!)
                        
                        
                        
                    }
                    
                    
                    let objRecipes = RecipeObj()
                    objRecipes.strRecipeId = obj.recipeid
                    objRecipes.strBreakFast = obj.breakFast
                    objRecipes.strLunch = obj.lunch
                    objRecipes.strDinner = obj.dinner
                    objRecipes.strSnacks = obj.snacks
                    objRecipes.strRecipeName = obj.recipeName
                    objRecipes.strRecipeDesp = obj.recipeDesp
                    objRecipes.strRecipeType = obj.recipeType
                    objRecipes.strNotificationDate = obj.notificationDate
                    objRecipes.arrIngredintsID = arrRecipesIngredientsID
                    objRecipes.arrIngredintsNames = arrRecipesIngredientsNames
                    objRecipes.strImgData = obj.imgData
                    objRecipes.strRepeatitionDays = obj.repeatitionDays
                    objRecipes.strOptional = obj.optional
                    
                    yourArray.append(objRecipes)
                    
                    
                } catch {
                    let fetchError = error as NSError
                    print(fetchError)
                }
                
                
            }
            
            
            
            print(result)
            
            
            
        } catch {
            let fetchError = error as NSError
            print(fetchError)
        }
        
        
        return yourArray
        
    }
    
    
    
    
    
    //MARK: - Update Festivals Master
    func updateFestivalsTable(arrayMain:[[String: String]])
    {
        
        
        print("Array Festivals \(arrayMain)")
        
        
        //  var arrayMain = [dictPongal ,dictMahaShivaratri, dictRamaNavami,dictGaneshChaturthi,dictDussehra,dictDiwali]
        print(arrayMain)
        
        for i in 0 ..< arrayMain.count
        {
            //                let festivalsList = NSEntityDescription.insertNewObjectForEntityForName("Festivals", inManagedObjectContext: self.managedObjectContext) as! Festivals
            //
            //                var  dict = arrayMain[i]
            //                festivalsList.setValue(dict["id"], forKey: "id")
            //                festivalsList.setValue(dict["festivalName"], forKey: "festivalName")
            //                festivalsList.setValue(dict["date"], forKey: "date")
            //                festivalsList.setValue(dict["seleted"], forKey: "seleted")
            //
            //                do {
            //                    try festivalsList.managedObjectContext?.save()
            //
            //
            //                } catch let error as NSError  {
            //                    print("Could not save \(error), \(error.userInfo)")
            //                }
            //
            
            var dictMain = arrayMain[i]
            let predicate = NSPredicate(format: "festivalName == %@",dictMain["festivalName"]! )
            let fetchRequest = NSFetchRequest(entityName: "Festivals")
            fetchRequest.predicate = predicate
            
            do {
                let fetchedEntities = try self.managedObjectContext.executeFetchRequest(fetchRequest) as! [Festivals]
                
                fetchedEntities.first?.date = dictMain["date"]!
                
            } catch
            {
                // Do something in response to error condition
            }
            
            do {
                try self.managedObjectContext.save()
            } catch {
                // Do something in response to error condition
            }
            
        }
        
        
        
    }
   
    
 

    //MARK: - Create Festivals Master
    func createFestivalsTable()
    {
        
        let dictPongal = ["id":"1","festivalName":"Pongal","date":"2016-01-15","seleted":"0"]
        let dictMahaShivaratri = ["id":"2","festivalName":"Maha Shivaratri","date":"2016-03-07","seleted":"0"]
        let dictRamaNavami = ["id":"3","festivalName":"Rama Navami","date":"2016-04-15","seleted":"0"]
        let dictGaneshChaturthi = ["id":"4","festivalName":"Ganesh Chaturthi","date":"2016-09-05","seleted":"0"]
        let dictDussehra = ["id":"5","festivalName":"Dussehra","date":"2016-10-11","seleted":"0"]
        let dictDiwali = ["id":"6","festivalName":"Diwali","date":"2016-10-30","seleted":"0"]
        var arrayMain = [dictPongal ,dictMahaShivaratri, dictRamaNavami,dictGaneshChaturthi,dictDussehra,dictDiwali]
        
        for i in 0 ..< arrayMain.count
        {
            let festivalsList = NSEntityDescription.insertNewObjectForEntityForName("Festivals", inManagedObjectContext: self.managedObjectContext) as! Festivals
            
            var  dict = arrayMain[i]
            festivalsList.setValue(dict["id"], forKey: "id")
            festivalsList.setValue(dict["festivalName"], forKey: "festivalName")
            festivalsList.setValue(dict["date"], forKey: "date")
            festivalsList.setValue(dict["seleted"], forKey: "seleted")
            
            do {
                try festivalsList.managedObjectContext?.save()
                
            } catch let error as NSError  {
                print("Could not save \(error), \(error.userInfo)")
            }
            
        }
        
        
    }

    //MARK: - Load Festivals List
    
    func loadFestivals()->Array<Any>
        //    func loadIngredients()
    {
        
        
        // Initialize Fetch Request
        let fetchRequest = NSFetchRequest()
        
        // Create Entity Description
        let entityDescription = NSEntityDescription.entityForName("Festivals", inManagedObjectContext: self.managedObjectContext)
        
        // Configure Fetch Request
        fetchRequest.entity = entityDescription
       // fetchRequest.predicate = NSPredicate(format: "type == %@", strVegNonVeg)
        //   var yourArray = <AnyObject>()
        var yourArray: Array <Any>
        yourArray = []
        do {
            let  result = try self.managedObjectContext.executeFetchRequest(fetchRequest)
            
            for i in 0  ..< result.count
            {
                yourArray.append(result[i])
            }
            
            print("festival")
            
            print(result)
            
            
            
            
        } catch {
            let fetchError = error as NSError
            print(fetchError)
        }
        
        
        return yourArray
        
    }
    
    //MARK: - Load Festivals Count
    func loadFestivalsCount()->Int
    {
        
        var count: Int!
        // Initialize Fetch Request
        let fetchRequest = NSFetchRequest()
        // Create Entity Description
        let entityDescription = NSEntityDescription.entityForName("Festivals", inManagedObjectContext: self.managedObjectContext)
        // Configure Fetch Request
        fetchRequest.entity = entityDescription
        
        do {
            let  result = try self.managedObjectContext.executeFetchRequest(fetchRequest)
            count = result.count
            print(result)
            
        } catch {
            let fetchError = error as NSError
            print(fetchError)
        }
        
        
        return count
        
        
    }

    
    //MARK: - Load Repeatation days Count
    func loadRepeatationDaysCount()->Int
    {
        
        var count: Int!
        // Initialize Fetch Request
        let fetchRequest = NSFetchRequest()
        // Create Entity Description
        let entityDescription = NSEntityDescription.entityForName("RepeatationDays", inManagedObjectContext: self.managedObjectContext)
        // Configure Fetch Request
        fetchRequest.entity = entityDescription
        
        do {
            let  result = try self.managedObjectContext.executeFetchRequest(fetchRequest)
            count = result.count
            print(result)
            
        } catch {
            let fetchError = error as NSError
            print(fetchError)
        }
        
        
        return count
        
        
    }

    
    //MARK: - Fetch Recipes Based on Day
    func fetchRecipesBasedOnDays(strDay:String) -> Array<Any>
        
    {
        
        print("DAY : \(strDay)")
        // Initialize Fetch Request
        let fetchRequest = NSFetchRequest()
        
        // Create Entity Description
        let entityDescription = NSEntityDescription.entityForName("ManageDays", inManagedObjectContext: self.managedObjectContext)
        
        // Configure Fetch Request
        fetchRequest.entity = entityDescription
        fetchRequest.predicate = NSPredicate(format:"day == %@",strDay)
        var arrIngredientId: Array <String>
        arrIngredientId = []
        var strType : String!
        do {
            let  result = try self.managedObjectContext.executeFetchRequest(fetchRequest)
           
            for i in 0  ..< result.count
            {
                let obj = result[i] as! ManageDays as ManageDays!
                if(obj.yes == "1")
                {
                arrIngredientId.append(obj.ingredientId!)
                }
                else{
                     strType = obj.type
                }
                
            }
          
            if(result.count > 0){
                let   obj = result[0] as! ManageDays as ManageDays!
                strType = obj.type
                print("TYPE")
                print(strType)
                print("INGREDIENTS FOR THE DAY : DONOT ALLOW")
                print(arrIngredientId)
            }
            else
            {
                //NO DAY SET IN MANAGE DAYS!!
                var arr: Array<Any>!
                arr = []
                return arr
            }
            
            
            
        } catch {
            let fetchError = error as NSError
            print(fetchError)
        }
        
        
        
        //NOW GET RECIPES THAT ARE VEG OR NON VEG!!
        
        
        // Initialize Fetch Request
        let fetchRequest_Recipes = NSFetchRequest()
        
        // Create Entity Description
        let entityDescription_Recipes = NSEntityDescription.entityForName("Recipes", inManagedObjectContext: self.managedObjectContext)
        
        // Configure Fetch Request
        fetchRequest_Recipes.entity = entityDescription_Recipes
         fetchRequest_Recipes.predicate = NSPredicate(format:"recipeType == %@",strType)
        //fetchRequest_Recipes.predicate = NSPredicate(format:"recipeType == %@",strType)
        var arrRecipes: Array <Any>!
        arrRecipes = []
       
        do {
            let  result = try self.managedObjectContext.executeFetchRequest(fetchRequest_Recipes)
           
            for i in 0  ..< result.count
            {                
                /// NOW FETCH THE INGREDIENTS OF RECIPES
                
                // Initialize Fetch Request
                let fetchRequest_ingredients = NSFetchRequest()
                
                // Create Entity Description
                
                
               
                    let obj = result[i] as! Recipes as Recipes!
                    
                    let entityDescription_ingredients = NSEntityDescription.entityForName("RecipeIngredientsAdded", inManagedObjectContext: self.managedObjectContext)
                    
                    // Configure Fetch Request
                    fetchRequest_ingredients.entity = entityDescription_ingredients
                    fetchRequest_ingredients.predicate = NSPredicate(format:"recipeId == %@",obj.recipeid!)
                    //fetchRequest_Recipes.predicate = NSPredicate(format:"recipeType == %@",strType)
                    var arrRecipesIngredientsID: Array <String>
                    arrRecipesIngredientsID = []
                    var arrRecipesIngredientsNames: Array <String>
                    arrRecipesIngredientsNames = []
                
                    do {
                        let  result = try self.managedObjectContext.executeFetchRequest(fetchRequest_ingredients)
                        for i in 0  ..< result.count
                        {
                            let objResult = result[i] as! RecipeIngredientsAdded as RecipeIngredientsAdded!
                             arrRecipesIngredientsID.append(objResult.ingredientId!)
                             arrRecipesIngredientsNames.append(objResult.ingredientName!)
                            

                        
                        }
                        
                        
                        let objRecipes = RecipeObj()
                        objRecipes.strRecipeId = obj.recipeid
                        objRecipes.strBreakFast = obj.breakFast
                        objRecipes.strLunch = obj.lunch
                        objRecipes.strDinner = obj.dinner
                        objRecipes.strSnacks = obj.snacks
                        objRecipes.strRecipeName = obj.recipeName
                        objRecipes.strRecipeDesp = obj.recipeDesp
                        objRecipes.strRecipeType = obj.recipeType
                        objRecipes.strNotificationDate = obj.notificationDate
                        objRecipes.arrIngredintsID = arrRecipesIngredientsID
                        objRecipes.arrIngredintsNames = arrRecipesIngredientsNames
                        objRecipes.strRepeatitionDays = obj.repeatitionDays
                        objRecipes.strOptional = obj.optional

                         arrRecipes.append(objRecipes)
                        
                        
                    } catch {
                        let fetchError = error as NSError
                        print(fetchError)
                    }
                
                
            }
            
           
            print("RECIPES")
            print(arrRecipes)
          
            
            
        } catch {
            let fetchError = error as NSError
            print(fetchError)
        }
        
        
        //NOW CHECK IF THE INGREDIENTS EXISTS IN RECIPES & FETCH THOSE RECIPES
        
   
        
        var arrMain: Array <Any>
        arrMain = []
        
        for i in 0  ..< arrRecipes.count
        {
            let obj = arrRecipes[i] as! RecipeObj
            
            print("Recipe Name : \(obj.strRecipeName)")
            print("Recipe Optional : \(obj.strOptional)")
            print("Recipe Ingredients Names: \(obj.arrIngredintsNames)")
            print("Recipe Ingredients IDs : \(obj.arrIngredintsID)")
            
            /////////////ADD Optional Recipes////////////
            
            if(obj.strOptional == "1")
            {
                print("Add this recipe as the Ingredients are optional")
                arrMain.append(obj)
            }
            else
            /////////////////////////////////////////////
            {
            
                    if(obj.arrIngredintsID.count == 0 && arrIngredientId.count > 0)
                        {
                            print("Add this recipe as there are no Ingredients added")
                            arrMain.append(obj)
                
                        }
                        else
                        {
            
            
                                var ingredientExists = 0
            
                                for j in 0  ..< obj.arrIngredintsID.count
                                {
                                    print("recipe ingredient \(obj.arrIngredintsID[j])")
                                    let strValue = obj.arrIngredintsID[j]
                                    print("arrIngredientId \(arrIngredientId) == recipe ingredient \(strValue)")
                                    if(arrIngredientId.contains(strValue))
                                        {
                  
                                            print("DONOT add this recipe as it contains ingredient")
                                            ingredientExists = 1
                                        }
               
                                }
            
                                if(ingredientExists != 1)
                                {
                                    print("ADD THE RECIPE AS INGREDIENTS DONT MATCH")
                                    arrMain.append(obj)
                                }
                        }
                
            }
        }
        
        print("ARRAY TO BE RETUNED")
        print(arrMain)
        return arrMain
    }
    
    
    
    
    
    
    
    
    
    
    //MARK: - Fetch Recipes Based on Festival
    func fetchRecipesBasedFestival(strDay:String) -> Array<Any>
        
    {
        
        
        // Initialize Fetch Request
        let fetchRequest = NSFetchRequest()
        
        // Create Entity Description
        let entityDescription = NSEntityDescription.entityForName("ManageDays", inManagedObjectContext: self.managedObjectContext)
        
        // Configure Fetch Request
        fetchRequest.entity = entityDescription
        fetchRequest.predicate = NSPredicate(format:"day == %@",strDay)
        var arrIngredientId: Array <String>
        arrIngredientId = []
        var strType : String!
        do {
            let  result = try self.managedObjectContext.executeFetchRequest(fetchRequest)
            
            for i in 0  ..< result.count
            {
                let obj = result[i] as! ManageDays as ManageDays!
               // arrIngredientId.append(obj.ingredientId!)
                if(obj.yes == "1")
                {
                    arrIngredientId.append(obj.ingredientId!)
                }
                else{
                    strType = obj.type
                }

                
            }
            
            if(result.count > 0){
                let   obj = result[0] as! ManageDays as ManageDays!
                strType = obj.type
                print("TYPE")
                print(strType)
                print("INGREDIENTS FOR THE DAY")
                print(arrIngredientId)
            }
            else
            {
                //NO DAY SET IN MANAGE DAYS!!
                var arr: Array<Any>!
                arr = []
                return arr
            }
            
            
            
        } catch {
            let fetchError = error as NSError
            print(fetchError)
        }
        
        
        
        //NOW GET RECIPES THAT ARE VEG OR NON VEG!!
        
        
        // Initialize Fetch Request
        let fetchRequest_Recipes = NSFetchRequest()
        
        // Create Entity Description
        let entityDescription_Recipes = NSEntityDescription.entityForName("Recipes", inManagedObjectContext: self.managedObjectContext)
        
        // Configure Fetch Request
        fetchRequest_Recipes.entity = entityDescription_Recipes
        fetchRequest_Recipes.predicate = NSPredicate(format:"recipeType == 0")
        //fetchRequest_Recipes.predicate = NSPredicate(format:"recipeType == %@",strType)
        var arrRecipes: Array <Any>!
        arrRecipes = []
        
        do {
            let  result = try self.managedObjectContext.executeFetchRequest(fetchRequest_Recipes)
            
            for i in 0  ..< result.count
            {
                /// NOW FETCH THE INGREDIENTS OF RECIPES
                
                // Initialize Fetch Request
                let fetchRequest_ingredients = NSFetchRequest()
                
                // Create Entity Description
                
                
                
                let obj = result[i] as! Recipes as Recipes!
                
                let entityDescription_ingredients = NSEntityDescription.entityForName("RecipeIngredientsAdded", inManagedObjectContext: self.managedObjectContext)
                
                // Configure Fetch Request
                fetchRequest_ingredients.entity = entityDescription_ingredients
                fetchRequest_ingredients.predicate = NSPredicate(format:"recipeId == %@",obj.recipeid!)
                //fetchRequest_Recipes.predicate = NSPredicate(format:"recipeType == %@",strType)
                var arrRecipesIngredientsID: Array <String>
                arrRecipesIngredientsID = []
                var arrRecipesIngredientsNames: Array <String>
                arrRecipesIngredientsNames = []
                
                do {
                    let  result = try self.managedObjectContext.executeFetchRequest(fetchRequest_ingredients)
                    for i in 0  ..< result.count
                    {
                        let objResult = result[i] as! RecipeIngredientsAdded as RecipeIngredientsAdded!
                        arrRecipesIngredientsID.append(objResult.ingredientId!)
                        arrRecipesIngredientsNames.append(objResult.ingredientName!)
                        
                        
                        
                    }
                    
                    
                    let objRecipes = RecipeObj()
                    objRecipes.strRecipeId = obj.recipeid
                    objRecipes.strBreakFast = obj.breakFast
                    objRecipes.strLunch = obj.lunch
                    objRecipes.strDinner = obj.dinner
                    objRecipes.strSnacks = obj.snacks
                    objRecipes.strRecipeName = obj.recipeName
                    objRecipes.strRecipeDesp = obj.recipeDesp
                    objRecipes.strRecipeType = obj.recipeType
                    objRecipes.strNotificationDate = obj.notificationDate
                    objRecipes.arrIngredintsID = arrRecipesIngredientsID
                    objRecipes.arrIngredintsNames = arrRecipesIngredientsNames
                    objRecipes.strRepeatitionDays = obj.repeatitionDays
                    objRecipes.strOptional = obj.optional
                    
                    arrRecipes.append(objRecipes)
                    
                    
                } catch {
                    let fetchError = error as NSError
                    print(fetchError)
                }
                
                
            }
            
            
            print("RECIPES")
            print(arrRecipes)
            
            
            
        } catch {
            let fetchError = error as NSError
            print(fetchError)
        }
        
        
        //NOW CHECK IF THE INGREDIENTS EXISTS IN RECIPES & FETCH THOSE RECIPES
        
        
        
        var arrMain: Array <Any>
        arrMain = []
      /*  for var i = 0 ; i < arrRecipes.count ;i++
        {
            let obj = arrRecipes[i] as! RecipeObj
            
            
            for var j = 0 ; j < obj.arrIngredintsID.count; j++
            {
                print(obj.arrIngredintsID[j])
                let strValue = obj.arrIngredintsID[j]
                if(arrIngredientId.contains(strValue))
                {
                    arrMain.append(obj)
                }
            }
            
        }
     */
        
        
        
        
        for i in 0  ..< arrRecipes.count
        {
            let obj = arrRecipes[i] as! RecipeObj
            
            print("Recipe Name : \(obj.strRecipeName)")
            print("Recipe Optional : \(obj.strOptional)")
            print("Recipe Ingredients Names: \(obj.arrIngredintsNames)")
            print("Recipe Ingredients IDs : \(obj.arrIngredintsID)")
            
            /////////////ADD Optional Recipes////////////
            
            if(obj.strOptional == "1")
            {
                print("Add this recipe as the Ingredients are optional")
                arrMain.append(obj)
            }
            else
                /////////////////////////////////////////////
            {
            
            if(obj.arrIngredintsID.count == 0 && arrIngredientId.count > 0)
            {
                print("Add this recipe as there are no Ingredients added")
                arrMain.append(obj)
                
            }
            else
            {
                var ingredientExists = 0
                for j in 0  ..< obj.arrIngredintsID.count
                {
                    print(obj.arrIngredintsID[j])
                    let strValue = obj.arrIngredintsID[j]
                    print("arrIngredientId \(arrIngredientId) == recipe ingredient \(strValue)")
                    if(arrIngredientId.contains(strValue))
                    {
                        
                        print("DONOT add this recipe as it contains ingredient")
                        ingredientExists = 1
                    }
                    
                }
                
                if(ingredientExists != 1)
                {
                    print("ADD THE RECIPE AS INGREDIENTS DONT MATCH")
                    arrMain.append(obj)
                }
            }
            }
            
        }
        
        
        print("ARRAY TO BE RETUNED")
        print(arrMain)
        return arrMain
    }
    
    
    //MARK: - Fetch ALL Recipes : NO CRITERIA
    func fetchAllRecipes() -> Array<Any>
        
    {
        
        
        // Initialize Fetch Request
        let fetchRequest_Recipes = NSFetchRequest()
        
        // Create Entity Description
        let entityDescription_Recipes = NSEntityDescription.entityForName("Recipes", inManagedObjectContext: self.managedObjectContext)
        
        // Configure Fetch Request
        fetchRequest_Recipes.entity = entityDescription_Recipes
       // fetchRequest_Recipes.predicate = NSPredicate(format:"recipeType == %@",strType)
        //fetchRequest_Recipes.predicate = NSPredicate(format:"recipeType == %@",strType)
        var arrRecipes: Array <Any>!
        arrRecipes = []
        
        do {
            let  result = try self.managedObjectContext.executeFetchRequest(fetchRequest_Recipes)
            
            for i in 0  ..< result.count
            {
                /// NOW FETCH THE INGREDIENTS OF RECIPES
                
                // Initialize Fetch Request
                let fetchRequest_ingredients = NSFetchRequest()
                
                // Create Entity Description
                
                
                
                let obj = result[i] as! Recipes as Recipes!
                
                let entityDescription_ingredients = NSEntityDescription.entityForName("RecipeIngredientsAdded", inManagedObjectContext: self.managedObjectContext)
                
                // Configure Fetch Request
                fetchRequest_ingredients.entity = entityDescription_ingredients
                fetchRequest_ingredients.predicate = NSPredicate(format:"recipeId == %@",obj.recipeid!)
                //fetchRequest_Recipes.predicate = NSPredicate(format:"recipeType == %@",strType)
                var arrRecipesIngredientsID: Array <String>
                arrRecipesIngredientsID = []
                var arrRecipesIngredientsNames: Array <String>
                arrRecipesIngredientsNames = []
                
                do {
                    let  result = try self.managedObjectContext.executeFetchRequest(fetchRequest_ingredients)
                    for i in 0  ..< result.count
                    {
                        let objResult = result[i] as! RecipeIngredientsAdded as RecipeIngredientsAdded!
                        arrRecipesIngredientsID.append(objResult.ingredientId!)
                        arrRecipesIngredientsNames.append(objResult.ingredientName!)
                        
                        
                        
                    }
                    
                    
                    let objRecipes = RecipeObj()
                    objRecipes.strRecipeId = obj.recipeid
                    objRecipes.strBreakFast = obj.breakFast
                    objRecipes.strLunch = obj.lunch
                    objRecipes.strDinner = obj.dinner
                    objRecipes.strSnacks = obj.snacks
                    objRecipes.strRecipeName = obj.recipeName
                    objRecipes.strRecipeDesp = obj.recipeDesp
                    objRecipes.strRecipeType = obj.recipeType
                    objRecipes.strNotificationDate = obj.notificationDate
                    objRecipes.arrIngredintsID = arrRecipesIngredientsID
                    objRecipes.arrIngredintsNames = arrRecipesIngredientsNames
                    objRecipes.strRepeatitionDays = obj.repeatitionDays
                    
                    arrRecipes.append(objRecipes)
                    
                    
                } catch {
                    let fetchError = error as NSError
                    print(fetchError)
                }
                
                
            }
            
            
            print("RECIPES")
            print(arrRecipes)
            
            
            
        } catch {
            let fetchError = error as NSError
            print(fetchError)
        }
        
        

        
        print("ARRAY TO BE RETUNED")
        print(arrRecipes)
        return arrRecipes
    }
    
    
    //MARK: - Update Recipe As Done : Set Date
    func updateRecipeDate(strId:String ,strDate: String)
    {
        
      
        let predicate = NSPredicate(format: "recipeid == %@", strId)
        
        let fetchRequest = NSFetchRequest(entityName: "Recipes")
        fetchRequest.predicate = predicate
        
        do {
            let fetchedEntities = try self.managedObjectContext.executeFetchRequest(fetchRequest) as! [Recipes]
            // fetchedEntities.first?.FirstPropertyToUpdate = NewValue
            // fetchedEntities.first?.SecondPropertyToUpdate = NewValue
            fetchedEntities.first?.notificationDate = strDate
            // ... Update additional properties with new values
        } catch {
            // Do something in response to error condition
        }
        
        do {
            try self.managedObjectContext.save()
        } catch {
            // Do something in response to error condition
        }


        
        
        
    }
    
    
    
    
    //MARK: - current Recipe As Done : Set Date
    func currentRecipeDate()-> String
    {
        var currentRecipeDate : String?
 
        //  let predicate = NSPredicate(format: "id == %@", andArrayFestivalIDs[i])
        
        let fetchRequest = NSFetchRequest(entityName: "RepeatationDays")
        // fetchRequest.predicate = predicate
        
        do {
            let fetchedEntities = try self.managedObjectContext.executeFetchRequest(fetchRequest) as! [RepeatationDays]
           currentRecipeDate = (fetchedEntities.first?.value)!
            
            // fetchedEntities.first?.SecondPropertyToUpdate = NewValue

            // ... Update additional properties with new values

        } catch {
            // Do something in response to error condition
        }
        
        return currentRecipeDate!


    }
 
    
    
    
    
    //MARK: - Fetch Recipe Obj Based on recipe id
    func fetchRecipeObj(strID : String) -> RecipeObj
        
    {
        
        
        // Initialize Fetch Request
        let fetchRequest_Recipes = NSFetchRequest()
        
        // Create Entity Description
        let entityDescription_Recipes = NSEntityDescription.entityForName("Recipes", inManagedObjectContext: self.managedObjectContext)
        
        // Configure Fetch Request
        fetchRequest_Recipes.entity = entityDescription_Recipes
        fetchRequest_Recipes.predicate = NSPredicate(format:"recipeid == %@",strID)
        //fetchRequest_Recipes.predicate = NSPredicate(format:"recipeType == %@",strType)
        
        let objRecipes = RecipeObj()
        
        do {
            let  result = try self.managedObjectContext.executeFetchRequest(fetchRequest_Recipes)
            
            
            
            if(result.count > 0)
            {
                let fetchRequest_ingredients = NSFetchRequest()
                
                // Create Entity Description
                
                
                
                let  obj = result[0] as! Recipes
                
                let entityDescription_ingredients = NSEntityDescription.entityForName("RecipeIngredientsAdded", inManagedObjectContext: self.managedObjectContext)
                
                // Configure Fetch Request
                fetchRequest_ingredients.entity = entityDescription_ingredients
                fetchRequest_ingredients.predicate = NSPredicate(format:"recipeId == %@",strID)
                //fetchRequest_Recipes.predicate = NSPredicate(format:"recipeType == %@",strType)
                var arrRecipesIngredientsID: Array <String>
                arrRecipesIngredientsID = []
                var arrRecipesIngredientsNames: Array <String>
                arrRecipesIngredientsNames = []
                
                do {
                    let  result = try self.managedObjectContext.executeFetchRequest(fetchRequest_ingredients)
                    for i in 0  ..< result.count
                    {
                        let objResult = result[i] as! RecipeIngredientsAdded as RecipeIngredientsAdded!
                        arrRecipesIngredientsID.append(objResult.ingredientId!)
                        arrRecipesIngredientsNames.append(objResult.ingredientName!)
                        
                        
                        
                    }
                    
                    objRecipes.strRecipeId = obj.recipeid
                    objRecipes.strBreakFast = obj.breakFast
                    objRecipes.strLunch = obj.lunch
                    objRecipes.strDinner = obj.dinner
                    objRecipes.strSnacks = obj.snacks
                    objRecipes.strRecipeName = obj.recipeName
                    objRecipes.strRecipeDesp = obj.recipeDesp
                    objRecipes.strRecipeType = obj.recipeType
                    objRecipes.strNotificationDate = obj.notificationDate
                    objRecipes.arrIngredintsID = arrRecipesIngredientsID
                    objRecipes.arrIngredintsNames = arrRecipesIngredientsNames
                    objRecipes.strImgData = obj.imgData
                    objRecipes.strRepeatitionDays = obj.repeatitionDays
                    objRecipes.strOptional = obj.optional
                    
                  //  arrRecipes.append(objRecipes)
                    
                    
                } catch {
                    let fetchError = error as NSError
                    print(fetchError)
                }
                
                
           // }
            }
            
            
            
            
            
        } catch {
            let fetchError = error as NSError
            print(fetchError)
        }
        
        
        
        
        print("ARRAY TO BE RETUNED")
        print(objRecipes)
        return objRecipes
    }
    


//MARK: - Insert IgnoreDays Days
    func insertIgnoreDays(strDays:String)
    {
        
        //////////////////////////////////////INSERT RECIPE//////////////////////////////////////
        let recipesList = NSEntityDescription.insertNewObjectForEntityForName("IgnoreDays", inManagedObjectContext: self.managedObjectContext) as! IgnoreDays
        recipesList.setValue(strDays, forKey: "day")       
        
        
        
        do {
            try managedObjectContext.save()
            
        } catch let error as NSError  {
            print("Could not save \(error), \(error.userInfo)")
        }
    }
    
    
    
    
    //MARK: - Load IgnoreDays Count
    func loadIgnoreDaysCount()->Int
    {
        
        var count: Int!
        // Initialize Fetch Request
        let fetchRequest = NSFetchRequest()
        // Create Entity Description
        let entityDescription = NSEntityDescription.entityForName("IgnoreDays", inManagedObjectContext: self.managedObjectContext)
        // Configure Fetch Request
        fetchRequest.entity = entityDescription
        
        do {
            let  result = try self.managedObjectContext.executeFetchRequest(fetchRequest)
            count = result.count
            print(result)
            
        } catch {
            let fetchError = error as NSError
            print(fetchError)
        }
        
        return count
    }

    
    
    
    //MARK: - Delete Days
    
    func deleteIgnoreDays(){
        //Clear Entire Table Before inserting!!
        
        
        if #available(iOS 9.0, *) {
            // iOS 9.0 & above
            
            ////iOS 9 & above
            let fetchRequest = NSFetchRequest(entityName: "IgnoreDays")
            let deleteRequest = NSBatchDeleteRequest(fetchRequest: fetchRequest)
            
            do {
                try self.managedObjectContext.executeRequest(deleteRequest)
            } catch let error as NSError {
                
                print("Could not Delete \(error), \(error.userInfo)")
            }
            
            
            ////////////////////////////////////////
            
            
        } else {
            // Fallback on earlier versions
            
            let fetchRequest = NSFetchRequest()
            let entityDescription = NSEntityDescription.entityForName("IgnoreDays", inManagedObjectContext: self.managedObjectContext)
            fetchRequest.entity = entityDescription
            
            do {
                let  result = try self.managedObjectContext.executeFetchRequest(fetchRequest)
                if(result.count > 0)
                {
                    for i in 0 ..< result.count
                    {
                        self.managedObjectContext.deleteObject(result[i] as! NSManagedObject)
                        print("DELETING")
                    }
                    
                }
                
            }
            catch let error as NSError  {
                print("Could not save \(error), \(error.userInfo)")
            }
            
            
            
        }
    }

    
    
    //MARK: - Load Ignore Days
    
    func loadIgnoreDays()->[String]
        //    func loadIngredients()
    {
        
        
        // Initialize Fetch Request
        let fetchRequest = NSFetchRequest()
        
        // Create Entity Description
        let entityDescription = NSEntityDescription.entityForName("IgnoreDays", inManagedObjectContext: self.managedObjectContext)
        
        // Configure Fetch Request
        fetchRequest.entity = entityDescription
        // fetchRequest.predicate = NSPredicate(format: "type == %@", strVegNonVeg)
        //   var yourArray = <AnyObject>()
     //   var yourArray: Array <Any>
      //  yourArray = []
          var yourArray : [String] = []
        do {
            let  result = try self.managedObjectContext.executeFetchRequest(fetchRequest)
            
            for i in 0  ..< result.count
            { let obj = result[i] as! IgnoreDays
                yourArray.append(obj.day!)
            }
            
            print("Ignoredays")
            
            print(result)
            
            
            
            
        } catch {
            let fetchError = error as NSError
            print(fetchError)
        }
        
        
        return yourArray
        
    }
    
    
    
    
    
    
    //MARK: - Insert IgnoreNotification Category Types
     
        func ignoreNotification()
        {
            
            //////////////////////////////////////IgnoreNotification//////////////////////////////////////
            let ignoreNotificationCat = NSEntityDescription.insertNewObjectForEntityForName("IgnoreNotification", inManagedObjectContext: self.managedObjectContext) as! IgnoreNotification
            
            ignoreNotificationCat.setValue("1", forKey: "breakfast")
            ignoreNotificationCat.setValue("1", forKey: "lunch")
            ignoreNotificationCat.setValue("1", forKey: "dinner")
            ignoreNotificationCat.setValue("1", forKey: "snacks")
            
            do {
                try managedObjectContext.save()
                
            } catch let error as NSError  {
                print("Could not save \(error), \(error.userInfo)")
            }
        }

    //MARK: - Update Ignore Notification Category Types
    func UpdateIgnoreNotification(strIgnoreCat:[String])
    {
        let fetchRequest = NSFetchRequest(entityName: "IgnoreNotification")
        
        do {
            let fetchedEntities = try self.managedObjectContext.executeFetchRequest(fetchRequest) as! [IgnoreNotification]
            
            fetchedEntities.first?.breakfast = strIgnoreCat[0]
            fetchedEntities.first?.lunch = strIgnoreCat[1]
            fetchedEntities.first?.dinner = strIgnoreCat[2]
            fetchedEntities.first?.snacks = strIgnoreCat[3]
            
            
            // ... Update additional properties with new values
        } catch {
            // Do something in response to error condition
        }
        do {
            try self.managedObjectContext.save()
        } catch {
            // Do something in response to error condition
        }
    }
    
    
     //MARK: - Load Ignore Notification Category Types
    
    
    func loadIgnoreDaysCategoryValue()-> Array<String>
        //    func loadIngredients()
    {
        
        
        var yourArray: Array <String>
        yourArray = []
        
        let fetchRequest = NSFetchRequest(entityName: "IgnoreNotification")
        
        do {
            let fetchedEntities = try self.managedObjectContext.executeFetchRequest(fetchRequest) as! [IgnoreNotification]
            // fetchedEntities.first?.FirstPropertyToUpdate = NewValue
            // fetchedEntities.first?.SecondPropertyToUpdate = NewValue
            if(fetchedEntities.count > 0)
            {
                
                yourArray.append((fetchedEntities.first?.breakfast)!)
                yourArray.append((fetchedEntities.first?.lunch)!)
                yourArray.append((fetchedEntities.first?.dinner)!)
                yourArray.append((fetchedEntities.first?.snacks)!)
                
            }
            
            // ... Update additional properties with new values
        } catch {
            // Do something in response to error condition
        }
        
        do {
            try self.managedObjectContext.save()
        } catch {
            // Do something in response to error condition
        }
        
        return yourArray
    }

}
