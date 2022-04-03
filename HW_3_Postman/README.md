HW_3 Postman
<hr>

`http://162.55.220.72:5005/login`
1. Приходящий токен необходимо передать во все остальные запросы
* Парсим response body в json
```js
var jsonData = pm.response.json();
```
* передаем токен в окружение
```js
pm.environment.set("auth_token", jsonData.token);
```

<hr>

`http://162.55.220.72:5005/user_info`
1. Статус код 200
```js
pm.test("Статус код 200", function () {
    pm.response.to.have.status(200);
});
```
2. Проверка структуры json в ответе
```js
let schema = {
"type": "object",
  "properties": {
    "person": {
      "type": "object",
      "properties": {
        "u_age": {
          "type": "integer"
        },
        "u_name": {
          "type": "array",
          "items": [
            {
              "type": "string"
            },
            {
              "type": "integer"
            },
            {
              "type": "integer"
            }
          ]
        },
        "u_salary_1_5_year": {
          "type": "integer"
        }
      },
      "required": [
        "u_age",
        "u_name",
        "u_salary_1_5_year"
      ]
    },
    "qa_salary_after_12_months": {
      "type": "number"
    },
    "qa_salary_after_6_months": {
      "type": "integer"
    },
    "start_qa_salary": {
      "type": "integer"
    }
  },
  "required": [
    "person",
    "qa_salary_after_12_months",
    "qa_salary_after_6_months",
    "start_qa_salary"
  ]
}

pm.test("Validate JSON schema", function ()  {
    pm.response.to.have.jsonSchema(schema);
  })
```

3. В ответе указаны коэффициенты умножения salary, напишите тесты по проверке правильности результата перемножения на коэффициент.
* парсим тело ответа в json, для работы тестов
```js
var jsonData = pm.response.json();
```
* Зарпалата через 6 месяцев
```js
pm.test("qa salary after 6 months = 2000", function () {
    pm.expect(jsonData.start_qa_salary*2).to.eql(jsonData.qa_salary_after_6_months)
})
```
* Зарплата через 12 месяцев
```js
pm.test("qa salary after 12 months = 2900", function () {
    pm.expect(jsonData.start_qa_salary*2.9).to.eql(jsonData.qa_salary_after_12_months)
})
```
* Зарпалат через 1.5 года
```js
pm.test("qa salary after 1.5 year = 4000", function () {
    pm.expect(jsonData.start_qa_salary*4).to.eql(jsonData.person.u_salary_1_5_year)
})
```

4. Достать значение из поля 'u_salary_1.5_year' и передать в поле salary запроса http://162.55.220.72:5005/get_test_user
```js
let salaryInt = jsonData.person.u_salary_1_5_year;
pm.environment.set("salary_1_5", jsonData.person.u_salary_1_5_year);
```

<hr>

`http://162.55.220.72:5005/new_data`

1. Статус код 200
```js
pm.test("Статус код 200", function () {
    pm.response.to.have.status(200);
});
```
2. Проверка структуры json в ответе
```js
let schema = {
	"type": "object",
	"required": [
		"age",
		"name",
		"salary"
	],
	"properties": {
		"age": {
			"$id": "#root/age", 
			"title": "Age", 
			"type": "integer",
			"examples": [
				24
			],
			"default": 0
		},
		"name": {
			"$id": "#root/name", 
			"title": "Name", 
			"type": "string",
			"default": "",
			"examples": [
				"Tima"
			],
			"pattern": "^.*$"
		},
		"salary": {
			"$id": "#root/salary", 
			"title": "Salary", 
			"type": "array",
			"default": []
		}
	}
}

pm.test("Validate JSON schema", function ()  {
    pm.response.to.have.jsonSchema(schema);
  })
```
3. В ответе указаны коэффициенты умножения salary, напишите тесты по проверке правильности результата перемножения на коэффициент.
```js
var jsonData = pm.response.json();
let reqData = request.data;
let salary_0 = jsonData.salary[0];
let salary_1 = jsonData.salary[1];
let salary_2 = jsonData.salary[2];
```
* Зарпалата - 1
```js
pm.test("salary = 1", function () {
    pm.expect(reqData.salary*1).to.eql(salary_0)
})
```
* Зарплата - 2
```js
pm.test("salary = 2", function () {
    pm.expect(reqData.salary*2).to.eql(+salary_1)
})
```
* Зарплата - 3
```js
pm.test("salary = 3", function () {
    pm.expect(reqData.salary*3).to.eql(+salary_2)
})
```
4. проверить, что 2-й элемент массива salary больше 1-го и 0-го
* alary 2 больше salary 1
```js
pm.test("salary 2 больше salary 1", function () {
    pm.expect(+salary_2).to.above(+salary_1);
})
```
* 4.2 salary 2 больше salary 0
```js
pm.test("salary 2 больше salary 0", function () {
    pm.expect(+salary_2).to.above(+salary_0)
})
```

<hr>

`http://162.55.220.72:5005/test_pet_info`

1. Статус код 200
```js
pm.test("Статус код 200", function () {
    pm.response.to.have.status(200);
});
```
2. Проверка структуры json в ответе
```js
let schema = {
	"type": "object",
	"required": [
		"age",
		"daily_food",
		"daily_sleep",
		"name"
	],
	"properties": {
		"age": {
			"$id": "#root/age", 
			"title": "Age", 
			"type": "integer",
			"examples": [
				24
			],
			"default": 0
		},
		"daily_food": {
			"$id": "#root/daily_food", 
			"title": "Daily_food", 
			"type": "number",
			"examples": [
				0.72
			],
			"default": 0.0
		},
		"daily_sleep": {
			"$id": "#root/daily_sleep", 
			"title": "Daily_sleep", 
			"type": "integer",
			"examples": [
				150
			],
			"default": 0
		},
		"name": {
			"$id": "#root/name", 
			"title": "Name", 
			"type": "string",
			"default": "",
			"examples": [
				"Tima"
			],
			"pattern": "^.*$"
		}
	}
}

pm.test("Validate JSON schema", function ()  {
    pm.response.to.have.jsonSchema(schema);
  })
```
3. В ответе указаны коэффициенты умножения weight, напишите тесты по проверке правильности результата перемножения на коэффициент.
```js
var jsonData = pm.response.json();
let reqData = request.data;
let reqWeight = reqData.weight
```

```js
pm.test("request weight*0.012 = response daily_food", function () {
    pm.expect(reqWeight*0.012).to.eql(jsonData.daily_food);
});
```
```js
pm.test("request weight*2.5 = response daily_food", function () {
    pm.expect(reqWeight*2.5).to.eql(jsonData.daily_sleep);
});
```

<hr>

`http://162.55.220.72:5005/get_test_user`
1. Статус код 200
```js
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});
```
2. Проверка структуры json в ответе
```js
let schema = {
	"title": "Root", 
	"type": "object",
	"required": [
		"age",
		"family",
		"name",
		"salary"
	],
	"properties": {
		"age": {
			"$id": "#root/age", 
			"title": "Age", 
			"type": "string",
			"default": "",
			"examples": [
				"24"
			],
			"pattern": "^.*$"
		},
		"family": {
			"$id": "#root/family", 
			"title": "Family", 
			"type": "object",
			"required": [
				"children",
				"u_salary_1_5_year"
			],
			"properties": {
				"children": {
					"$id": "#root/family/children", 
					"title": "Children", 
					"type": "array",
					"default": [],
					"items":{
						"$id": "#root/family/children/items", 
						"title": "Items", 
						"type": "array",
						"default": []
					}
				},
				"u_salary_1_5_year": {
					"$id": "#root/family/u_salary_1_5_year", 
					"title": "U_salary_1_5_year", 
					"type": "integer",
					"examples": [
						4000
					],
					"default": 0
				}
			}
		}
,
		"name": {
			"$id": "#root/name", 
			"title": "Name", 
			"type": "string",
			"default": "",
			"examples": [
				"Tima"
			],
			"pattern": "^.*$"
		},
		"salary": {
			"$id": "#root/salary", 
			"title": "Salary", 
			"type": "integer",
			"examples": [
				1000
			],
			"default": 0
		}
	}
}

pm.test("Validate JSON schema", function ()  {
    pm.response.to.have.jsonSchema(schema);
  })
```
3. Проверить что значение поля name = значению переменной name из окружения

```js
var jsonData = pm.response.json();
```
```js
pm.test("значение поля name = значению переменной name из окружения", function () {
    pm.expect(jsonData.name).to.eql(environment.name)
})
```
4. Проверить что занчение поля age в ответе соответсвует отправленному в запросе значению поля age
```js
let reqData = request.data;
```
```js
pm.test("занчение поля age в ответе соответсвует отправленному в запросе значению поля age", function () {
    pm.expect(jsonData.age).to.eql(reqData.age)
})
```
