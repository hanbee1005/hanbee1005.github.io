---
layout: post
title:  "[Node] Nodejs + express에 Swagger 적용하기"
date:   2021-10-26 15:15:00 +0900
categories: nodejs express swagger
---
#### swagger 관련 라이브러리 추가
```shell
npm i swagger-ui-express swagger-jsdoc
```
#### swagger 설정 파일 작성 (app/config/swagger.js)
```js
const swaggerUi = require('swagger-ui-express')
const swaggerJsdoc = require('swagger-jsdoc')

const options = {
    swaggerDefinition: {
        openapi: '3.0.1',
        info: {
            title: '프로젝트_타이틀',
            version: '프로젝트_버전',
            description: '프로젝트_설명',
            contact: {
                name: '관리자명',
                url: '관리자_페이지',
                email: '관리자_이메일'
            },
        },
    },
    apis: ['api_및_schema_파일_위치', ...]
}

const specs = swaggerJsdoc(options)

module.exports = {
    swaggerUi,
    specs
}
```
#### swagger 설정 파일 추가 (server.js)
```js
...

// swagger
const { swaggerUi, specs } = require('./app/config/swagger')
app.use('/api/api-docs', swaggerUi.serve, swaggerUi.setup(specs))

...
```
#### schema 작성 (yaml 파일 사용)
```yaml
components:
  schemas:
    스키마명1:
      properties:
        필드명1:
          type: string
          description: 설명
          example: 예시
        필드명2:
          $ref: '#/components/schemas/스키마명2'
    스키마명2:
      properties:
        필드명1:
          type: string
          description: 설명
          example: 예시
        필드명2:
          type: string
          description: 설명
          example: 예시
```
#### api 작성
- /api/books 로 put 메소드를 통해 들어오는 요청에 대한 예시   

```js
/**
 * @swagger
 * /api/books:
 *    put:
 *      tags:
 *      - Book
 *      description: 도서 소유자 수정
 *      produces:
 *      - application/json
 *      parameters:
 *      - name: id
 *        in: path
 *        description: 도서 id
 *        example: '1'
 *        required: true
 *        schema:
 *          type: string
 *      requestBody:
 *       content:
 *         'application/json':
 *           schema:
 *             $ref: '#/components/schemas/book_owner_update_req'
 *      responses:
 *       200:
 *        description: 책 정보
 *        content:
 *          application/json:
 *            schema:
 *              $ref: '#/components/schemas/book_owner_update_res'
 */
router.put('/:id', books.update)
```

#### 참고
- https://github.com/hanbee1005/bcheck-api
- https://velog.io/@yongh8445/Node-Express-Swagger-%EC%97%B0%EB%8F%99