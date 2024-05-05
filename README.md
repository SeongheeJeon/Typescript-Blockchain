### nomadcoders lecture - typescript for beginners(blockchain)


# 1.3 Who should take this course

- JS개발자가 생산성 높이고 버그 줄이고 싶을 때
- C# 이나 Java 개발자가 JS 는 사용하기 싫을 때 TS로 타입 안정성

# 1.4 Software requirements

- TS도 VS Code도 MS가 만들었음. 그래서 VS Code로 작업하길 추천
- Node.js도 필요

# 1.5 Why not JavaScript

- **타입안정성**
    
    → 런타임 에러 감소, 생산성 향상됨
    
- JS는 자유롭고 개발자를 최대한 이해하고자 함.
    
    ```jsx
    [1,2,3,4] + false
    => '1,2,3,4false'
    ```
    
    → **이상한 문법**인데 JS는 이걸 허용함 
    
    ```jsx
    function divide(a, b){
    	return a/b;
    }
    divide("abvseg")
    => NaN
    ```
    
    → **잘못된 값이 전달**되었는데 에러가 발생하지 않고, 실행이 가능함. 
    
    ```jsx
    const hee = {name: "hee"}
    hee.hello();
    ```
    
    → **hee 객체에 hello라는 함수가 없음**. BUT JS는 개발 과정에서 오류를 알려주지 않고, 코드가 customer에게 전달되어 실행된 후, 브라우저에서 에러가 발생함. 
    
    → 더 나은 방법은, 코드가 customer에게 전달되기 전에, 실행되기 전에 “hee 객체에 hello라는 함수가 없다”라고 알려주는 것. (Rust, Go에서는 이 경우 실행조차 안됨)


# 2.0 How Typescript Works

- strongly types programming language
- TS도 결국 JS로 컴파일됨. 근데 TS가 어떻게 JS와는 달리 개발자를 보호해줄까?
    - JS로 컴파일되기 전에 코드를 검사함 (유저가 코드를 실행하는 런타임에 진행되는게 아니라)

# 2.1 Implicit Types vs Explicit Types

- 보통 Implicit으로, TS가 타입을 추론하도록  함.
    
    ```jsx
    let a = "hello";
    a = "bye";
    a = 1; // ERROR
    
    let b : boolean = "hello" // ERROR 
    let c : number[] = [] 
    ```
    

# 2.2 Types of TS (part 1)

- optional type, Alias
    
    ```jsx
    type Player = {     // Alias TYPE
    	name: string,
    	age?: number,     // optional 
    }
    
    const hee : Player = {
    	name: "hee"
    }
    
    const min : Player = {
    	name: "hee",
    	age: 24
    }
    ```
    
- 함수 return type
    
    ```tsx
    type Player = {
    	name: string,
    	age?: number, // optional 
    }
    
    function playerMaker(name: string){    // return type 선언X
    	return {name}
    }
    
    const hee = playerMaker("hee")
    hee.age = 4        // ERROR(Player 객체가 아니라, name만 갖는 객체라서)
    
    function playerMaker2(name: string) : Player{    // return type 선언 
    	return {name}
    }
    
    const min = playerMaker2("min")
    hee.age = 4 // GOOD
    ```
    

# 2.3 Types of TS (part 2)

- readonly
    
    ```tsx
    type Player = {
    	readonly name: string,
    }
    
    const a : readonly number[] = [1,2,3,4]
    a.push(3) // ERROR
    ```
    
- Tuple
    - 최소한의 길이를 갖는, 특정 위치에 특정 타입이 존재해야하는 배열
    
    ```tsx
    const player: [string, number, boolean] = ["hee", 24, true]
    ```
    

- any
    
    ```tsx
    let arr = [] // array of any
    
    const a : any[] = [1,2,3]
    const b : any = true
    a + b // NOT ERROR , NOT GOOD 
    ```
    
    → TS의 보호장치로부터 빠져나오고 싶을 때(바보같은 짓을 하게 허용해달라고 할 때) 사용.
    
    → any 사용을 제한하기 위한 설정 가능 
    

# 2.4 Types of TS (part 3) - JS에는 없는 types

- **unknown** : 타입확인 작업을 강제시킴
    
    ```tsx
    let a: unknown
    let b = a + 1 // ERROR
    
    if(typeof a === 'number'){
    	let b = a + 1;
    }
    
    if(typeof a === 'string'){
    	let b = a.toUpperCase();
    }
    ```
    
    → unknown 사용하지 않고 let a; 라고 선언하면, never 타입에 toUpperCase사용할 수 없다고함.
    

- **void** : 함수 자체는 반환되지만, 반환값은 없을 때 사용(ex : console.log를 실행하는 함수)
- **never** : 함수가 절대로 반환되지지 않을 때 사용(ex : 런타임 에러 throw 하는 함수)
    
    ```tsx
    function hello(): never{
    	throw new Error("xxx")
    	return "hello" // ERROR 
    }
    ```
    
    - 존재할 수 없는 값의 경우?!
    
    ```tsx
    function hello(name: string){
    	if(typeof name === "string"){
            name // type: string
        } else { 
            name // type: never
        }
    }
    ```
    
    → 인자를 제대로 전달 받았다면 else 문은 실행되지 않아야함.

# 3.0 Call Signatures

- 함수만의 type 만들기. Alias type 처럼
    
    ```tsx
    type Add1 = (a: number, b:number) => number     // 방식 1
    type Add2 = {                                   // 방식 2
        (a: number, b: number): number
    }
    
    const add:Add1 = (a,b) => a+b
    ```
    
    → 타입 선언과 함수 구현을 구분함 
    
    → React에서 prop으로 함수를 전달할 때, 이러한 과정이 필요함 
    

# 3.1 Overloading

- 함수가 여러개의 Call Signature를 가질 때 발생함.
    
    ```tsx
    // 인자의 갯수가 같을 경우
    
    type Config = {
    	path: string,
    	state: object
    }
    
    type Push = {
    	(path: string): void
    	(config: Config): void
    }
    
    const push:Push = (config) => {
    	if(typeof config === "string") console.log(config)
    	else console.log(config.path)
    }
    ```
    
    ```tsx
    // 인자 갯수가 다를 경우
    
    type Add = {
        (a: number, b:number) : number,
        (a: number, b:number, c:number) : number,
    }
    
    // const add:Add = (a,b,c) => {             // (1)
    const add:Add = (a,b,c?:number) => {        // (2)
        if(c) return a+b+c
        else return a+b
    }
    
    add(1,3);
    add(1,3,5);
    ```
    
    → 인자가 2개일수도 있고 3개일수도 있음. 즉, c는 optional
    
    → 근데 (1)처럼 작성하면, 인자가 2개 전달되었을 경우 에러가 발생함. 그래서 c는 optional로 작성해주고, c가 있을 경우와 없을 경우 구분해서 코드를 작성해야함.
    

# 3.2 Polymorphism

- poly(many) + morphos(structure) : 많은(다양한) 형태
- generic을 사용해서 다양한 형태의 call signature를 만들어줌
- generic : type의 placeholder 같은 역할
    - call signature에서 인자의 확실한 타입을 모를 때 generic 사용할 수 있음
    - TS가 알아낸 타입으로 인자의 타입을 대체해줌
    
    ```tsx
    type SuperPrint = {
    	<TypePlaceholder>(arr: TypePlaceholder[]):void // <> 안에 아무 문자나 가능 
    }
    type PrintFirst = {
    	<T>(arr: T[]):T 
    }
    
    const superPrint: SuperPrint = (arr) => {
    	arr.forEach(i => console.log(i))
    }
    const printFirst: PrintFirst = (arr) => {
    	return arr[0]
    }
    
    superPrint([1,2,3,4])
    superPrint([true, false, true])
    superPrint(["a", "b", "c"])
    superPrint([1,2,true,false,"hello"])
    ```
    
    → 함수 호출부에서 전달인자의 타입에 맞춰서 call signature을 자동생성해줌. 즉, 함수가 다양한 형태를 가질 수 있음.
    
    - 이 경우는 TS의 보호장치에서 벗어나는거라고 보면 되나?
        
        generic이 아니라 any를 사용하면, 아예 타입을 무시해버리기 때문에 벗어나는거라고 할 수 있지만, generic은 TS가 자동으로 call signature를 생성해주는 개념이기 때문에 TS의 보호장치 내에서 동작할 수 있는 듯.
        
    - 다양한 call signature가 필요할 때에만 generic을 사용하고, 그게 아닐 경우엔 타입을 명시해주는게 나은건가?
- React.js에서는 다른 방식으로 generic 을 사용할 것임.

# 3.4 Conclusion

- generic을 사용해서 call signature를 작성하는 일은 드물것임.
    
    → 보통의 경우 다른 라이브러리나 패키지를 사용하게되는데, 그것들이 이미 내부적으로 generic을 활용하고 있기 때문. 그래서 라이브러리 등을 만들 때 generic이 유용할 것. 
    

- generic 사용 예
    
    ```tsx
    type Player<E> = {
    	name: string,
    	extreInfo: E
    }
    
    const hee:Player<{favFood: string}> = {
    	name: "hee",
    	extraInfo: {
    		favFood: "kimchi"
    	}
    }
    ```

# 4.0 Classes

```tsx
class Player {
    constructor(
        private firstName:string,
        private lastName:string,
        public nickname:string
    ){}
}

const hee = new Player("hee","jeon","good");
hee.firstName // ERROR
hee.nickname // OK
```

→ private, public 키워드는 JS에서 사용되지 않지만 ,TS에서 보호를 위해 사용함. 

### abstract class

- 다른 class가 상속받을 수 있는 class. 직접 새로운 인스턴스를 만들수는 없음.
    
    ```tsx
    abstract class User{
        constructor(
            private firstName:string,
            private lastName:string,
            public nickname:string
        ){}
    }
    
    class Player extends User{}
    
    const hee = new Player("hee","jeon","good"); // OK
    const min = new User("hee","jeon","good"); // ERROR
    ```
    

### abstract method

- abstract class를 상속받는 모든 class가 구현해야하는 method
- abstract class 안에 abstract method를 만들려면 추상 클래스 안에는 call signature만 작성해야함. 코드 구현은 상속받는 class에서.
    
    ```tsx
    abstract class User{
        constructor(
            protected firstName:string,
            protected lastName:string,
            protected nickname:string
        ){}
        abstract getNickname():void
    }
    
    class Player extends User{
        getNickname(){
            console.log(this.nickname) // protected라서 접근 가능 
        }
    }
    
    const hee = new Player("hee","jeon","good");
    hee.getNickname()
    ```
    
    → **protected** : 자식 클래스에서도 접근 가능. (private : 외부 어느 곳에서도 접근 불가)
    

# 4.1 Recap

- code
    
    ```tsx
    type Words = {
        [key:string]: string      // key와 value의 타입이 모두 string인 객체
    }
    
    class Dict {
        private words:Words
        constructor(){           // Dict 인스턴스 생성 시점에 words를 전달받지 않음.
            this.words = {}      // 생성 시에는 빈 객체를 words에 할당함
        }
        add(word: Word){         // class를 type처럼 사용 가능
            if(this.words[word.term] === undefined){
                this.words[word.term] = word.def
            }
        }
        def(term: string){
            return this.words[term]
        }
    
    }
    
    class Word {
        constructor(
            public term: string,
            public def: string
        ){}
    }
    
    const kimchi = new Word("kimchi", "한국의음식")
    const ramen = new Word("ramen", "맛있는음식")
    const dict = new Dict()
    dict.add(kimchi)
    dict.add(ramen)
    
    console.log(dict)
    console.log(dict.def("kimchi"))
    ```
    

# 4.2 Interfaces

- type의 활용
    
    ```tsx
    type Nickname = string
    type Team = "red" | "blue" | "yellow"
    type Health = 1 | 5 | 10
    
    type Player = {
        nickname: Nickname,
        team: Team,
        health: Health
    }
    ```
    

### Interface

- TS 에서 object의 모양을 알려줌
    
    ```tsx
    interface Player {           // type Player = {    이렇게 작성하는것과 동일한 기능  
        nickname: string,
        team: "red" | "blue" | "yellow",
        health:  1 | 5 | 10
    }
    ```
    
- interface & 상속 (class와 동일하게 extends 키워드 사용)
    
    ```tsx
    interface User {
        name: string
    }
    
    interface Player extends User {}
    
    const hee: Player = {
        name: "hee"
    }
    ```
    
- type & 상속
    
    ```tsx
    type User = {
        name: string
    }
    
    type Player = User & {}
    
    const hee: Player = {
        name: "hee"
    }
    ```
    
- interface의 합체능력 : property 축적 가능
    
    ```tsx
    interface User {
        name: string
    }
    interface User {
        age: number
    }
    interface User {
        isStudent: boolean
    }
    
    const hee: User = {
        name: "hee",
        age: 15,
        isStudent: false
    }
    ```
    

- type 과는 다른 interface의 특징
    - 객체지향 프로그래밍의 개념을 활용해 디자인되었음

# 4.3 Interfaces (part 2)

- abstract class와 interface의 공통점
    - 클래스의 구조를 미리 정의함, 상속 가능
    - 자식class에게 어떻게 구현해야할지는 알려주지 않지만 무엇을 구현해야할지 알려줌
- abstract class와 interface의 차이
    
    ```tsx
    (abstract class)
    
    abstract class User {
        constructor(
            protected firstName: string,
            protected lastName: string,
        ){}
        abstract sayHi(name:string): string
        abstract fullName(): string
    }
    
    class Player extends User {
        sayHi(name: string){
            return `Hello ${name}. My name is ${this.firstName} ${this.lastName}`
        }
        fullName(){
            return `${this.firstName} ${this.lastName}`
        }
    }
    ```
    
    → abstract class는 클래스의 구조를 정의하는 TS의 기능임. TS에서 abstract class를 기반으로 타입검토를 하고, 문제 없이 JS로 변환될 경우 abstract class는 JS에서 불필요함. 하지만 JS에 일반 class로 변환되어 존재하기 때문에 코드가 불필요하게 길다고 볼 수 있음.
    
    → abstract class에서 constructor 사용 가능 
    
    ```tsx
    (interface)
    
    interface User {
        firstName: string,
        lastName: string,
        sayHi(name:string): string
        fullName(): string
    }
    
    class Player implements User {
        constructor(
            public firstName: string,
            public lastName: string,
            private age: number
        ){}
        sayHi(name: string){
            return `Hello ${name}. My name is ${this.firstName} ${this.lastName}`
        }
        fullName(){
            return `${this.firstName} ${this.lastName}`
        }
    }
    ```
    
    → 컴파일하면 JS로 변환되지 않고 사라짐!!! 파일 사이즈 줄일 수 있음. 가벼워짐.
    
    → JS에서도 사용되는 extends 대신, implements 사용 (JS로 변환되지 않고 사라짐)
    
    → interface를 상속받을 때, 상속받은 property는 public만 가능. interface에서 private 등을 정의할 수 없기 때문.
    

# 4.5 Polymorphism

### generic(polymorphism) + class + interface

```tsx
interface SStorage<T>{
    [key: string]: T
}

class LocalStorage<T> {
    private storage: SStorage<T> = {}
    set(key: string, value: T){
        this.storage[key] = value
    }
    remove(key: string){
       delete this.storage[key]
    }
    get(key: string):T{
        return this.storage[key]
    }
    clear(){
        this.storage = {}
    }
}

const stringStorage = new LocalStorage<string>()
const booleanStorage = new LocalStorage<boolean>()

stringStorage.set("name", "hee")
booleanStorage.set("isTrue", true)
```

# 5.1 Targets

1. 프로젝트 폴더 생성
2. 폴더를 vscode에서 열고 node 프로젝트 초기화 `npm init -y`, TS 설치 `npm i -D typescript`
3.  tsconfig.json 파일 생성 
    1. tsconfig.json : 프로젝트에서 TS 사용할것을 vscode가 인지함
        
        ```json
        {
          "include": ["src"],
          "compilerOptions": {
            "outDir": "build"
          }
        }
        ```
        
        → include : js로 컴파일 하고자하는 폴더들의 배열 
        
        → outDir : js 파일이 생성될 폴더 
        
4. src > index.ts 파일 생성, 아래 코드 작성
    
    ```tsx
    const hello = () => "hi";
    ```
    
5. package.json에서 “main” 항목 지우고, script에 build 명령어 작성, `npm run build`
    
    ```tsx
    "scripts": {
      "build": "tsc"
    },
    ```
    
6. build 폴더에 index.js 파일 생성됨. 호환성 좋은 js로 변환된 상태. 
    
    ```tsx
    var hello = function () { return "hi"; };
    ```
    
    → 어떤 버전의 js로 변환할지 설정 가능 (tsconfig.json > compilerOptions > target) 
    
    ```tsx
    "compilerOptions": {
      "outDir": "build",
      "target": "ES3"
    }
    ```
    
    → ES3는 화살표함수랑 const 키워드 없음. ES6로 바꿔서 build 해보기, 그리고 ES6로 하길 추천함 (대부분의 nodejs와 브라우저가 ES6를 지원하기 때문)
    
- practice
    
    ```tsx
    class Block {
      constructor(private data: string) {}
      static hello() {
        return "hi";
      }
    }
    ```
    
    → `npm run build` : class 가 존재하지 않는 옛날 버전의 js로 코드 변환해줌. 
    

# 5.2 Lib Configuration

- 변환된 js 코드가 실행될 환경을 설정해줌
    
    ```tsx
    "compilerOptions": {
      "lib": ["ES6","DOM"]
    }
    ```
    
    → ES6를 지원하는 환경에서, 그리고 브라우저에서 실행될 것이라는걸 알려주면 ts는 관련된 API를 알고있기 때문에 관련 자동완성 기능을 제공함 (ex : document, window, localStorage 등)
    

# 5.3 Declaration Files

- TS가 어떻게 window, localStorage 등의 API를 알고있는가?
    - “TS는 내장된 JS API를 위한 기본적인 타입정의를 갖고 있다” : 누군가가 시간을 들여서 TS에게 window 오브젝트의 모양과 argument, return type 등에 대해 설명해주었다.
    - 보통 프레임워크나 라이브러리 등을 사용하는데, 그것들은 ts가 아닌 js로 만들어져있음. 그것들을 ts 프로젝트에서 사용하려면 ts가 이해할 수 있어야 하기 때문에 타입 정의를 해둠.

- 타입 정의 예시
    1. src > myPackages.js 생성 (node의 모듈이라고 생각하기)
        
        ```jsx
        export function init(config) {
          return true;
        }
        
        export function exit(code) {
          return code + 1;
        }
        ```
        
    
    1. src > index.ts에서 myPackages를 node모듈인 것처럼 사용
        
        ```jsx
        import {init} from "myPackages";
        ```
        
        → 이렇게만 하면 정의파일(js코드의 모양을 ts에 설명해주는 파일)을 찾을 수 없다고 나옴. 
        
        → filename.d.ts 라는 정의파일을 만들어줘야함 
        
        → 만약 위와같은 TS 오류가 뜨지 않는다면 strict 모드가 아니라서임. tsconfig.json > compilerOptions 에 strict : true 설정해주면됨 
        

    1. myPackages.ts에 모듈 선언 
        
        ```jsx
        interface Config {
          url: string;
        }
        declare module "myPackages" {
          function init(config: Config): boolean;
        }
        ```
    

# 5.4 JSDoc

- 이전 섹션에서는 js로 작성된 node 모듈을 ts에서 사용하는 경우의 타입 정의이고, 이번엔 프로젝트 내에 js 파일을 사용할 경우의 타입정의.
- 우선 tsconfig.json > compilerOptions > `allowJs: true`
- js 파일을 그대로 둔 채 ts의 보호를 받고 싶다면, js 파일 최상단에 `// @ts-check` 작성하고 JSDoc(함수 위에 코멘트 적어주는 것) 사용.
    
    → 코멘트일뿐이라 브라우저나 서버의 프로덕션 환경에서 에러날 걱정 안해도됨.
    
    → 코멘트일뿐인데 ts 에서 타입체크를 해준다는게 엄청난것 
    
    ```jsx
    // @ts-check
    /**
     * Initialize the project
     * @param {object} config
     * @param {boolean} config.debug
     * @param {string} config.url
     * @returns {boolean}
     */
    export function init(config) {
      return true;
    }
    
    /**
     * Exit the project
     * @param {number} code
     * @returns {number}
     */
    export function exit(code) {
      return code + 1;
    }
    
    ```
    

# 5.5 Blocks

- **ts-node** : build 없이 ts 실행할 수 있음. 프로덕션에서 사용하는건 아니고, 개발 모드에서만 사용. 빌드 없이 빠르게 새로고침 할 수 있음
    1. `npm i -D ts-node` 
    2. package.json > scripts > `"dev": "ts-node src/index"`
- **nodemon** : 자동으로 커맨드 재실행해줌. 서버 재실행할 필요 없음.
    1. `npm i nodemon`
    2. package.json > scripts > `"dev": "nodemon --exec ts-node src/index.ts”`
    
- 블록체인
    - 여러개의 블록이 사슬처럼 묶인 것.
    - 블록 안에는 데이터가 있음. 블록체인으로 보호하고자하는 데이터
    - 블록은 다른 블록에 묶여있음. 연결고리로. 그 연결고리는 해쉬값임.
    
- crypto 모듈 사용
    - tsconfig.json > compilerOption > `"esModuleInterop": true`
    - index.ts > `import crypto from "crypto"`
    - 타입정의를 찾을 수 업다는 에러 뜲.
        
        → 다음 섹션에서 해결
        
        - tsconfig.json
            
            ```json
            {
              "include": ["src"],
              "compilerOptions": {
                "outDir": "build",
                "target": "ES6",
                "lib": ["ES6"],
                "strict": true,
                "esModuleInterop": true,
                "module": "CommonJS"
              }
            }
            ```
            

# 5.6 Definitely Typed

- ts로 만들어지지 않은 패키지를 다운받았는데 타입정의가 없을 경우
    - [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped) 사용 : 타입정의 파일들
    - `npm i -D @types/node` : node를 위한 타입정의를 모두 설치
    - 만약 express 를 사용하고 싶으면 express를 먼저 설치 후 `npm i -D @types/express`
    - 최근엔 .ds.ts 파일을 포함해서 패키지를 배포하는 경우가 많음.

```jsx
import crypto from "crypto";

interface BlockShape {
  hash: string;
  prevHash: string;
  height: number;
  data: string;
}

class Block implements BlockShape {
  public hash: string;
  constructor(
    public prevHash: string,
    public height: number,
    public data: string
  ) {
    this.hash = Block.calculateHash(prevHash, height, data);
  }
  static calculateHash(prevHash: string, height: number, data: string) {
    const toHash = `${prevHash}${height}${data}`;
    return crypto.createHash("sha256").update(toHash).digest("hex");
  }
}

```

# 5.7 Chain

```tsx
import crypto from "crypto";

interface BlockShape {
  hash: string;
  prevHash: string;
  height: number;
  data: string;
}

class Block implements BlockShape {
  public hash: string;
  constructor(
    public prevHash: string,
    public height: number,
    public data: string
  ) {
    this.hash = Block.calculateHash(prevHash, height, data);
  }
  static calculateHash(prevHash: string, height: number, data: string) {
    const toHash = `${prevHash}${height}${data}`;
    return crypto.createHash("sha256").update(toHash).digest("hex");
  }
}

class Blockchain {
  private Blocks: Block[];
  constructor() {
    this.Blocks = [];
  }
  private getPrevHash() {
    if (this.Blocks.length === 0) return "";
    else return this.Blocks[this.Blocks.length - 1].hash;
  }
  public addBlock(data: string) {
    const newBlock = new Block(
      this.getPrevHash(),
      this.Blocks.length + 1,
      data
    );
    this.Blocks.push(newBlock);
  }
  public getBlocks() {
    return [...this.Blocks];
  }
}

const blockchain = new Blockchain();

blockchain.addBlock("First one");
blockchain.addBlock("Second one");
blockchain.addBlock("Third one");

blockchain.getBlocks().push(new Block("xxxx", 111, "HACKEDDDDD!!!"));

console.log(blockchain.getBlocks());

```
