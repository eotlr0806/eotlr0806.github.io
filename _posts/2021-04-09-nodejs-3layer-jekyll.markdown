---
layout: post
title:  How to organize best structure in node.js?
date:   2021-04-09 19:00:00 +0900
description: 
img: img-post-nodejs-3layer.jpg
fig-caption: 
tags: [Node.js, Javascript, Architecture]
---

​     

오늘은 Node.js 의 프로젝트 구조에 대해 한번 적어보려고 한다.

Node 를 사용하다 보면 너무 편리한점이 많다. 개발 속도도 빠르고 NPM이라는 무시무시한 장점도 있고~ 그리고 많은 장점 중 굉장히 융통성있는? 언어라는 점이 있는것 같다. **개발자의 생각에 따라 마음대로 설계를 할 수 있다는 장점**, 이런 표현이 맞는지 모르겠다...
뭐 아무튼 이러한 **장점이 단점으로 되는건 한 순간**이다.

express 를 사용하면, 보다 손쉽게 서버를 구축할 수 있지만, 이 안에는 정해진 설계 규칙이 존재하지 않는다.

많은 미들웨어가 지원을 하고 있기에, **"이러한 기능이 가능해"** 일 뿐이지, **"이렇게 써야만 해"** 라고 구조를 정해주지 않는다.

그렇기에 개발자의 생각대로 설계를 할 수 있지만, 잘못된 설계를 할 경우 운영을 하면 할 수록 끔찍한 일들이 일어나는걸 우리는 알고있다... 하나 바꾸는데 이것 저것 요것 다 바껴야되고, 신입이 들어와 소스코드를 이해하는데만 몇 주가 소요되는...

그래서 오늘은 Node.js 의 대표적인 **아키텍처인 3-Layer 구조**에 알아보려고 한다.

그 전에 설계가 왜 중요한지부터 짚고 넘어가보자.

___

​      

​    



# Why architecture is important

하나의 가정을 해보자

```html
우리는 승용차 대여 플랫폼을 운영중인데, 지금까지는 승용차에 대한 대여만 서비스해 왔다.
하지만 규모가 점점 커짐에 따라, 트럭 대여 서비스도 운영을 하려고 한다.

기존의 기능에는 승용차에 대한 CRUD, 차량 예약 CRUD 가 있다고 가정한다.
그런데, 트럭 대여 서비스가 추가됨에 따라 승용차에 대한 CRUD, 차량 예약 CRUD 에 대한 API 가 변경이되어야 한다.

아마 많은 사람들이 이런 생각을 할 것 이다. 
"누가 설계를 그렇게 해~~" 
하지만! 자신있는가!! 당신은 이렇게 설계를 안 할 자신이 있는가!!

처음 서비스를 운영할때부터 "우리는 무조건 승용차만 대여하지, 다른 차량은 대여 안해줄거야" 라고 시작을 했다면?

테이블을 설계할 때 부터 car 라는 테이블에는 무조건 승용차만 들어가기에, car type 을 확인할 수 있는 컬럼이 없다면? 트럭이 추가되었을 때, 승용차와 트럭은 무엇으로 구분할 것인가? 트럭 테이블을 만들것인가? 나중에는 또 다른 타입의 차량이 추가된다면 또 테이블을 만들것인가?
차량 테이블의 type 하나가 추가되는 것만으로 승용차 CRUD 시에 where type="승용차" 라는 조건이 추가되어야 한다.

차량 예약 또한 비슷한 케이스가 생길 것이다.
```

애당초 처음부터 "다른 타입의 차량도 대여할 수 있지 않을까?" 라는 생각으로 확장성을 가지고 설계를 했다면 트럭 대여 서비스가 운영된다 하더라도, 기존의 기능에는 수정이 필요없을 것이다. 그렇다면 개발 시간도 단축될 것이고, 시간은 곧 돈이니 개발 비용도 단축이 될 것 이다.

​    

여기서 내가 말하고 싶은 것은,

### **설계가 잘 된 프로그램은, 새로운 기능이 추가되더라도 기존 기능에 대한 수정 없이 개발이 가능하다.**

**즉, 새로운 기능 추가에 대해 시간과 비용을 절약할 수 있다.** 개발자의 시간을 절약할 수 있는 것은 그 시간 동안 개발자가 다른 서비스를 개발 할 수 있다는 의미로, 생각보다 큰 장점이라고 생각한다.

​    

또 다른 이야기를 해보자.

뭐 이런 저런 케이스를 다 잡아 개발을 해 나갈 수 있긴 하다. 그렇게 서비스를 운영한지 긴 시간이 흘렀다.

```
새로운 신입이 들어왔고, 신입에게 "이번주는 소스좀 분석해봐" 라고 일을 주었다.
과연 그 신입은 일주일만에 서비스의 흐름을 알 수 있을까?

잘못된 설계의 무서운 점은 시간이 지날 수록 쓸 수 없을 정도로 코드가 더러워진다는 것이다.
"여기에는 승용차에 대한 DTO가 있어, 하지만 트럭에 대한 DTO는 저기 있어, 그리고 승용차도 트럭도 아닌 차량도 있을거잖아 그런 ETC에 있으니까 알아둬. 그리고 테이블 구조는 이렇게 되어 있어"
"이 테이블에서 tmp컬럼이 뭐냐고? 그건 그때 그때 임의 값으로 사용할수도 있으니까 넣어 둔거야. 필요한 값을 넣어 쓰면 돼"

아마 신입은 들어와서 쉽게 이해하지 못 할 것이다..
```

설계가 잘못된다면, 새로운 기능이 추가 될 때에도 문제가 되지만 개발한 사람이 나가고 새로운 사람이 들어왔을 때 그 사람이 소스를 이해하는데만도 엄청난 시간이 든다.

​    

### 설계가 잘 된 프로그램은, 누가 보더라도 가독성이 좋아야 하며, 구조를 쉽게 이해할 수 있어야 된다.

내가 나가더라도, 누군가 당장 개발을 대체 할 수 있을 정도로 말이다.

   

이 외에도 작성한 코드에 대한 재활용성도 중요하며, 중복되는 코드도 없어야 한다. 또한 개발만큼 시간을 써야되는 것 중 하나가 테스트인데 테스트가 용이하기도 해야 한다. 이 내용에 대해서는 리펙토링 이라는 책을 보면 잘 나와 있는 것 같다. 이건 따로 포스트를 올리도록 하겠다.

**사실 기능 개발이야 일정 수준만 되면 누구나 만들어 낼 수 있다. 하지만 어떻게 설계하느냐가 그 개발자의 실력을 보여주는 지표가 아닐까 생각한다.**

___

​    

​     

# Node.js 3-Layer Architecture

Node.js 아키텍처 중 대표적인 아키텍처인듯 하다.

3-Layer의 의미는 프로그램의 **구조를 3개의 층**으로 나누기 때문이다.

구조는 아래와 같다.

<div class="mermaid">
graph LR
A[Controller Layer] --> B[Service Layer] --> C[Data Access Layer]
B --> B
</div>

글보다는 그림이 더 이해하기 쉬울것 같아 mermaid.js 를 사용해봤는데 그닥~~ㅋㅋㅋ

무튼 3-Layer 는 Controller / Service / Data access 이렇게 3개의 계층으로 나눠진다.
각 계층이 나눠져 있는 이유는 **계층별로 주어진 역할**이 있으며, 각 계층이 가진 역할에 충실해야 한다. 또한 **계층은 연결된 계층으로만 엑세스 할 수 있고 계층을 뛰어 넘어 엑세스 할 수 없다.**

​    



### Controller

Controller 계층은 쉽게 말해 **서버로 요청을 제일 먼저 받아 데이터를 정리** 해주는 계층이라고 생각하면 될 것 같다.

해당 계층에서는 서버로 요청온 Request 데이터를 정리하여 Service Layer로 넘겨주기도 하며, 처리된 값을 Response에 담아 다시 Client 로 보내주기도 한다. 

해당 계층에서는 우리가 흔히 알고 있는 비즈니스 로직이 수행되어서는 안되며, HTTP의 Request, Response 객체를 Service Layer로 직접 넘겨주어서도 안된다. 해당 객체들에 대한 처리는 Controller 계층에서 처리가 되고, Service Layer에서는 순수하게 요청에 대한 처리를 필요로 하는 데이터만 전달되어야 한다.

express를 사용할 경우, Router에서 해당 역할을 해줄 것이다.
어떤 기능에 대한 Router 에서는 서비스 로직이 포함되어 있고, 어떤 기능에 대한 Router는 Service 계층으로 넘기고, 어떤 기능에 대한 Router에서는 직접 Data Access 작업을 수행한다면 위에서 이야기한대로 나중에 유지보수가 굉장히 어려울 것이다.

아래 코드는 내가 특정 서비스를 개발 할때 작성한 구조이다.

```javascript
import express from 'express';
import { menuGroupService } from '../../service/service_index.js';

const menugroupRouter   = express.Router();

// GET menugroup
menugroupRouter.get('/menugroup', async (req,res,next) => {
    let menuGroupList = await menuGroupService.getMenuGroupList();
    return res.json(menuGroupList);
});

// POST menugroup
menugroupRouter.post('/menugroup', async (req,res,next) => {
    let returnCode = await menuGroupService.postMenuGroupList(req.body);
    return res.json(returnCode);    
}),

// DELETE menugroup
menugroupRouter.delete('/menugroup', async (req,res,next) => {
    let returnCode = await menuGroupService.deleteMenuGroupList(req.query.menucd);
    return res.json(returnCode);    
}),


// PUT menugroup
menugroupRouter.put('/menugroup', async (req,res,next) => {
    let returnCode = await menuGroupService.putMenuGroupList(req.body);
    return res.json(returnCode);    
})


export default menugroupRouter

```



위 코드는 Router 의 파일만 따온것으로 body-parser 같은 미들웨어나 라우팅 설정은 앱이 올라오면서 설정되므로 여기서는 순수하게 Controller 계층 역할만 하도록 수행하였다.

해당 계층에 대해 찾아보면 어떤 문서는 API Layer 또는 Route Layer 로 소개하는 글도 꽤 많이 보았다.     

​     



### Service Layer 

Service Layer는 Controller Layer 에서 받은 데이터로 **실제 데이터를 처리하는 비즈니스 로직이 돌아가는 계층** 이라고 생각하면 된다.

for문이 될 수도 있고, parsing 작업이 될 수 도 있고 뭐 그렇다. 해당 계층에서 데이터를 처리하는 과정에서 DB나 외부 서비스에 Access가 필요할 경우 Data Access 계층을 호출한다.

아래 코드는 위 라우터코드가 호출한 서비스들에 대한 서비스 계층의 파일이다.

```javascript
import { menuGroupRepo } from '../../repository/repository_index.js';

const menuGroupService = {    
    getMenuGroupList : async () => {
        try{
            return await menuGroupRepo.getMenuGroupList();
        }catch(err){
            console.log(`[menugroup_service.js] getMenuGroupList service error ${err}`);
        }

    },

    postMenuGroupList : async (params) => {
        try{
            let returnCode = await menuGroupRepo.postMenuGroupList(params.params);
            if(returnCode === 1){
                return {status : 200, title: 'Success', msg : 'Insert Success'};
            }
            return {status: 400, title: 'Fail', msg : 'Problems occurred during data processing'};
        }catch(err){
            console.log(`[menugroup_service.js] postMenuGroupList service error ${err}`);
        }
    },

    deleteMenuGroupList : async (params) => {
        try{
            let parameter = [];
            parameter.push(params)
            let returnCode = await menuGroupRepo.deleteMenuGroupList(parameter);
            if(returnCode === 1){
                return {status : 200, title: 'Success', msg : 'Insert Success'};
            }
            return {status: 400, title: 'Fail', msg : 'Problems occurred during data processing'};
        }catch(err){
            console.log(`[menugroup_service.js] deleteMenuGroupList service error ${err}`);
        }
    },

    putMenuGroupList : async (params) => {
        try{
            let returnCode = await menuGroupRepo.putMenuGroupList(params.params);
            if(returnCode === 1){
                return {status : 200, title: 'Success', msg : 'Update Success'};
            }
            return {status: 400, title: 'Fail', msg : 'Problems occurred during data processing'};
        }catch(err){
            console.log(`[menugroup_service.js] putMenuGroupList service error ${err}`);
        }
    },
}

export default menuGroupService
```

아직 return 시 예외에 대한 테스트가 진행되지 않아 200/400만 체크하며, config로 코드도 설정되어 있지 않는 미완전한 모습을 보여주지만~심지어 로그도 console로 찍고있다..ㅋㅋ 무튼 예시가 없는것 보단 나을 것 같아 적었다.
해당 Service 계층에서는 Data Access를 모두 필요로 하며, Data Access 계층을 호출하면서 해당 계층에서 필요로 하는 데이터를 가공하여 전달해준다.

또한 서비스는 하나의 기능에 충실하도록 설계하는것이 좋다.
예를 들어 getMenuGroupList 에서 Data Access 작업을 처리하기 위해 Data 파싱 작업이 필요하다면, getMenuGroupList 에서 파싱 작업을 하는게 아닌 새로운 함수를 만들어서 호출하도록 하자.

경험상 하나의 기능에 충실할 경우, 추후 에러가 발생하여도 에러를 한번에 잡을 수 있다. 
여러 기능을 try catch 로 한번에 잡을 경우 다시 그 에러가 발생하길 기다려야되는 순간이 생길 수 있기 때문이다.

​        



### Data Access Layer

**Data Access Layer의 경우 외부 서비스에 Access 하는 역할을 하는 Layer** 라고 보면 된다.

해당 서비스를 운영하는데, 외부 기관계에서 데이터를 받아야 한다면 데이터를 받아오는 로직은 Data Access Layer 에 구현될 것이다. 또한 우리가 흔히 알고있는, DB Connection 또한 Data Access Layer 에 존재한다.

```javascript
import oracledb from 'oracledb';

let menuGroupRepo = {
    getMenuGroupList : async () => {
        let result;
        let conn;
        try{
            conn = await oracledb.getConnection();    
            let sql = `SELECT
                            MENUCD,
                            MENUNM,
                            NAME_ENG,
                            MENUDEPTH,
                            MENUSEQ,
                            PARENTCD,
                            USEWIZARD,
                            VIEWCHECK
                        FROM FW_MENU_INFO
                        ORDER BY MENUSEQ ASC`;
            result = await conn.execute(sql);         
            return result.rows
        } catch(err){
            console.log(`[menugroup_repository.js] getMenuGroupList error ${err}`);
        } finally {
            if(conn){
                try{
                    await conn.close();
                }catch(err){
                    console.log(`[menugroup_repository.js] getMenuGroupList connection release error ${err}`);
                }
            }
        }

    },

    postMenuGroupList : async (params) => {
        let result;
        let conn;
        console.log(`[menugroup_repository.js] postMenuGroupList parameter ${JSON.stringify(params)}`);

        try{
            conn = await oracledb.getConnection();    
            let sql = `INSERT INTO FW_MENU_INFO(menucd, menunm, name_eng, menudepth, menuseq, parentcd, usewizard, viewcheck)
                        VALUES(:MENUCD, :MENUNM, :NAME_ENG, :MENUDEPTH, :MENUSEQ, :PARENTCD , :USEWIZARD, :VIEWCHECK)`;
            result = await conn.execute(sql,params);    
            return result.rowsAffected;
        } catch(err){
            console.log(`[menugroup_repository.js] postMenuGroupList error ${err}`);
            return 0;
        } finally {
            if(conn){
                try{
                    await conn.close();
                }catch(err){
                    console.log(`[menugroup_repository.js] postMenuGroupList connection release error ${err}`);
                }
            }
        }
    },

    deleteMenuGroupList : async (params) => {
        let result;
        let conn;
        console.log(`[menugroup_repository.js] deleteMenuGroupList parameter ${JSON.stringify(params)}`);

        try{
            conn = await oracledb.getConnection();    
            let sql = `DELETE FROM FW_MENU_INFO WHERE MENUCD = :MENUCD`;
            result = await conn.execute(sql,params);    
            return result.rowsAffected;
        } catch(err){
            console.log(`[menugroup_repository.js] postMenuGroupList error ${err}`);
            return 0;
        } finally {
            if(conn){
                try{
                    await conn.close();
                }catch(err){
                    console.log(`[menugroup_repository.js] postMenuGroupList connection release error ${err}`);
                }
            }
        }

    },

    putMenuGroupList : async (params) => {
        let result;
        let conn;
        console.log(`[menugroup_repository.js] putMenuGroupList parameter ${JSON.stringify(params)}`);

        try{
            conn = await oracledb.getConnection();    
            let sql = `UPDATE FW_MENU_INFO 
                       SET menunm    = :MENUNM, 
                           name_eng  = :NAME_ENG, 
                           menudepth = :MENUDEPTH,
                           menuseq   = :MENUSEQ,
                           parentcd  = :PARENTCD,
                           USEWIZARD = :USEWIZARD,
                           VIEWCHECK = :VIEWCHECK
                       WHERE MENUCD  = :MENUCD`;
            result = await conn.execute(sql,params);    
            return result.rowsAffected;
        } catch(err){
            console.log(`[menugroup_repository.js] putMenuGroupList error ${err}`);
            return 0;
        } finally {
            if(conn){
                try{
                    await conn.close();
                }catch(err){
                    console.log(`[menugroup_repository.js] putMenuGroupList connection release error ${err}`);
                }
            }
        }

    },


}

export default menuGroupRepo;
```

위 코드는 위에 코드들과 연관되는 Data Access Layer이다. DB Access를 이용할 경우 해당 계층에 Query가 작성될 것이다.

---

​    

이렇게 Node.js 의 3-Layer를 정리해보았다.

실제로 3-Layer를 사용해본 결과, 유지보수적인 측면에서 굉장한 효율을 피부로 느꼈다... 어떤 기능이 추가되어야 할 때 "여기 여기 여기에 추가하면 되네!" 하고 바로 머리속으로 떠올릴 수 있다. 심지어 어떤 기능이 안될 때, 어디를 보면 되는구나 라고 바로 잡을 수가 있었다. 각 계층별로 역할이 분명히 나눠져 있으며, 그 역할에 충실할 경우, 지금까지 느껴보지 못한 깔끔한 구조를 맛 볼 수 있다...!

​    



#### 참조

----

[Node.js project architecture best practices](https://blog.logrocket.com/the-perfect-architecture-flow-for-your-next-node-js-project/)

[견고한 Node.js Project](https://velog.io/@hopsprings2/%EA%B2%AC%EA%B3%A0%ED%95%9C-node.js-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EC%95%84%ED%82%A4%ED%85%8D%EC%B3%90-%EC%84%A4%EA%B3%84%ED%95%98%EA%B8%B0)

[Organize node.js api server](https://bytearcher.com/articles/node-project-structure/)

