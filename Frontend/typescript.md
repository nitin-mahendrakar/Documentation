
[ Datatypes ]
    number 
    string 
    boolean 
    object 
    string[]
    unknown 
    any 


type name = string | number | boolean  
let yourname : name = string | number | boolean

type unkown_type = unknown 
let na : unknown_type = "string"

@ts-ignore // ye wali line typescript ignore kardega 
let naa : any = unknown_type; // error 

type details = {
    name : string, 
    sirname? : string. // ye wala property do nahi do chelega 
}
const ob : details = { name : "guru", sirname : "mahendrakar"}
                     {name : "guru" , sirname :  1} error 

interface {
    property : value 
    property : value f
}
            
[ Function Declarations ]
    
    // ye function impletement nahi karoge to error dega 
    function add ( a : number, b : number) : number ;
    
    //error gone 
    funcdtion add (a : number , b : number ) : number {
        return a + b; 
    }
    
[ Function Overloading ]

    function search ( number1 : string, number2 : string) : any;
    function search ( number1 : number, number2 : number) : any;

    // no errors 
    function search (number1 : string | number , number2 : string | number) : any {
        return ""
    }

    // error occured
    function search (number1 : string, number2 : string ) : any {
        return ""
    }

[ Passing Function in Arguments ]

    // 1st method 
    function ( fn : (number)=> number ) : void ;

    //2nd method 
    type function = (number : number ) => number
    function (fn : function) : void 


[ Clasess ]

    class Track {
        title : string 
        artist : string 

        constructor (){
            // error define title and string 
        }
    }

    ** Inheritance 
        interface Objects {
            protected key : type  // error dont do this interfaces are already forcefully public 
            key : type // no error 
        }

        class One Impletement Objects{
            static readonly property : type
            // only restricted (access & modify ) only in class 
            // outside class (access & modify) gives error
            protected propery : type 

            // you can (access & modify) everywhere
            public property : type 

            // sirf apne class me access and modify kar sakte hai. 
            // outside access and modify error 
            private property : type 
        }

        ** Abstract 

            class method {
                abstract property :  type;
                abstract _method(parameter : type) : number;
            }

            class method1 extends method {
                // error not impleted _method function.

                // now its fine
                _method(paramter : type) : number {
                    return 1; 
                }
            }

[ Generics ]

    [[ In Functions Generics ]]
    function GENERIC<mytype>(arg : mytype) : mytype  (){
        return arg;
    }

    GENERIC<number>(1);
    GENERIC<string>("gag)

    [[ In Classes in Generics ]]
    

    interface {
        name : string
    }
    class method<type extends interface>{

    }

    method<string>() // error not satifies with interface need object 
    method<{name : value}>() // ok 


[ extends ]

    type value = one | two | three | four 
    interface<T extends "one" | "two" ? T : never >

    interface<value> 
        - value me one hai  = one
        - value me two hai = two 
        - value me three hai = (never) "one" | "two"  isliye never
        - value me four hai = never 
        -finally = one | two (because never ignores)

    interface<one | two> // ok 
    interface<three> // error

    never ignore hojate hai. 



[ Utitily Types ]

    interface User {
        name : string ,
        sirname : string ,
        dateofbirth? : string
    }

    partial 
        partial<User>
        - internally does this
            interface User {
                name? : string
                sirname? : string 
            }

    Required
        required<User>
        - force karta hai likhne optional property ko required me 
        - internally does this 
            interface User {
                name : string,
                sirname : string,
                dateofbirth? : string {converted} dateofbirth : string 
            }

    Record 
        type keys = "one" | "two";
        type values = "three" | "four";

        // error two is missing
        const ob : Record<keys,values> = {
            one : "three",
        }

        // its ok 
        const ob : Record<keys,values> = {
            one : "three",
            two : "four"
        }

    Pick // works with objects
        Pick<User,"name">
        - internally does this 
            interface {
                name : string,
                <!-- sirname : string  --> dropped 
            }
    Omit  // works with objects
        Omit<User,"name">
        - internally does this 
            interface {
                <!-- name : string --> dropped
                sirname : string
            }

    exclude 
        type names = "guru" | "Nitin";
        // ok 
        type name : Exclude<names,"guru"> = "Nitin" 
        //error
        type name : Exclude<names,"guru"> = "guru" 

    nonNullable

        type types = string  | number | undefined | null;

        nonNullable<types> = string | number 


[ Unions ]

    type nums = 1 | 2 | 3
    type nums1 = 4,5,6 

    nums2 : nums | nums1 = 1 | 2 | 3 | 4 | 5 | 6


[ Intersection ]

    type nums = 1 | 2 | 3
    type nums1 = 1 | 5 | 6 
    type nums2 = 4 | 5 | 6

    nums3 : nums & nums1 =  1
    nums4 : nums & nums2 = never;
