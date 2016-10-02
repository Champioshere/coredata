# coredata

import UIKit
import CoreData
class Model: NSObject {
        
    let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext

  
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
