# [REST API의 이상향, HATEOAS](https://wallees.wordpress.com/2018/04/19/rest-api-hateoas/)

- REST는 간단하지 않다.
- Martin Fowloer가 2010년 제안한 Richardson 성숙도 모델에 의하면, 총 4단계로 REST API를 구분하고 있음
    - Level 0 : The Swamp of POX(Plain Old XML)
    - Level 1 : Resource
    - Level 2 : HTTP Verbs
    - Level 3 : Hypermedia Controls

Level 3에 해당하는 response는 아래와 같은 식.

```
{
    "name": "Alice",
    "links": [
        {
            "rel": "self",
            "href": "http://localhost:8080/customer/1"
        }
    ]
}
```
