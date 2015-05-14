[API DOC](http://liangzeng.github.io/cqrs/api/index.html)
---------------------------------------------------------

#### DDD-CQRS-Actor framework for javascript.

###### Schematic diagram
![](https://raw.githubusercontent.com/leogiese/cqrs/master/img.png)

###### Understand ActorListener
![](https://raw.githubusercontent.com/leogiese/cqrs/master/img2.png)

Install
=======

    npm install cqrs --save

Test
====

    npm test

Use
===

#### Step 1 . create a domain

    var Domain = require("cqrs").Domain;
    var Actor = require("cqrs").Actor;
    var domain = new Domain(options);  // create a cqrs domain

+ options for eventstore , see [eventstore - Provide implementation for storage](https://github.com/adrai/node-eventstore#provide-implementation-for-storage)


#### Step 2 . create a actor class

    // use es5 extend tool
    var extend = require("cqrs").extend;    
    var User = extend({
    
       type:'User',
       
       _when:function(event){
           switch(event.name){
               case "changeName":
                   this._data.name = event.name;
               break;
           }
       },

       changeName(name){
           this._apply("changeName",name);
       }
    });
    
    
    --- or use es6 class ---

    class User extends Actor{

        static get type(){
           return 'User';
        }

        _when(event){
            switch(event.name){
                case "changeName":
                    this._data.name = event.name;
                break;
            }
        }

        changeName(name){
            this._apply("changeName",name);
        }

    }

#### Step 3 . register actor class to domain

    domain.register(User);

#### Step 4 . listen domain's event

    domain.on("User.changeName",function(event){
        // ......
    })

#### Step 5 . actor operating

    domain.get("User",userId,function(err,actor){

    });

    // or

    domain.get("User",userId).then(function(actor){
        actor.changeName("leo giese");
    });


Advanced Usage
==============

#### Actor listen domain'event, and handle.

    export default
    class Transfer extends Actor {

        static get type() {
            return "Transfer";
        }

        transfer(fromId, toId, money) {
            ......

            this._listen("User." + fromId + ":deduct&" + this.id, "__userDeduct");
            this._listen("User." + toId + ":recharge&" + this.id, "__userRecharge");

            ......
        }

        // event handle method
        __userDeduct(event) {
            ......
        }

        __userRecharge(event) {
            ......
        }

    }


LICENSE
=======
MIT