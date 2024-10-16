
# celertix-framework
   This project have two parts I) Webserver, ii) ORM and it's in under development cycle.
 The main purpose of the project for creating the REST - API in rappid way with the support of built-in Asyncronous ORM functionality for PostgreSql. 

## Web Server
This is built on sanic web framework for creating the fastest async REST - API with sanic blueprint 

## ORM
This is built on asyncpg world's fastest asynchronous postgresql database interface library. provides CURD operations

## Installation

Use the package manager [pip](https://pip.pypa.io/en/stable/) to install required packages.

```bash
#Clone the project
git clone https://github.com/BALAVIGNESHDOSTRIX/celertix.git
cd celertix

pip3 install -r requirements.txt
			(or)
pip3 install asyncio
pip3 install asyncpg
pip3 install sanic
pip3 install Sanic-Auth
```

# Setup
```
# DB Configuration
# Create Database manually through console or pgadmin
CREATE DATABASE db_name OWNER user;

# DB config on celertix
# Go to celertix ---> configs ---> celertix_dbconfig.py

DB_CONFIG = {
        'dbname' : 'gres', #Database Name
        'dbpass' : 'odoo@123', #Postgres user password or DB password
        'dbport' : 5432, # Postgresql running port (Default 5432)
        'dbuser' : 'odoo', #Database user
        'dbhost' : '127.0.0.1' #Database Host
}


```

## Run
```
# Server will start by run the following command inside the project
python3 celertix-bin.py
```

## Usage
Project dir should be following for example to create the res_users table with their routes.

```
addons               #root dir
  |
  |__resusers        #res_users table dir
       |____ __init__.py
       |
       |____ models  #resusers models dir
       |      |___ __init__.py
       |      |___ res_partner.py #reseusers table file
       |
       |____ routes
              |___ __init__.py
              |___ res_partner_router.py #resusers table route

Please create the each models inside the addons and should be follow the above order of directory. If you want use other names please go to configure in config ----> celertix_module_config.py

```
# ORM API Methods
## ORM-Supported Fields
```
# Fields
Integer, Char, Datetime, Date, Float

# Examples
name = Char(string="Name")
age = Integer(string="Age")
department = Char(string="Department")
create_dt = Datetime(string="CreateDt")
write_dt = Date(string="WriteDt")
salary = Float(string="Salary")
```
## create method
```
vals = {'name': 'balavignesh', 'age': 24, 'department': 'IT', 'blood_group': 'A+'}
userobj = await clxuniv.env['res.user'].create(vals=vals)

# Access the Attribute with values

>> userobj._id
1
>> userobj.blood_group
A+
>> userobj.age
24
>> userobj.department 
IT
``` 

## browse method
```
search_ids = [1,8,10]
userobj = await clxuniv.env['res.user'].browse(search_ids)

userobj # contains list of objects

#Accessing the each objects in the list
for obj in userobj:
    obj._id
    obj.age


```

## search method
```
name = 'bala'
age = 20

# SELECT age, blood_group, department, name, id FROM res_user WHERE  name = 'bala' AND age = 90;
userobj_l = await clxuniv.env['res.user'].search([('name', '=',name), '&', ('age', '=', age)])

# Access each record with objects
for user in userobj_l:
    user.age

# Operators
{
    '=' : '=',
    '&' : 'AND',
    '!=': '!=',
    '|' : 'OR',
    'in': 'IN',
    'not in': 'NOT IN'
}

```

## update (write) method
```
name = 'bala'
age = 20

userobj = await clxuniv.env['res.user'].search([('name', '=',name), '&', ('age', '=', age)])
     for user in userobj:
          await user.write({'name': 'vignesh', 'age': 20})
```

## delete (unlink) method
```
name = 'bala'
age = 20

userobj = await clxuniv.env['res.user'].search([('name', '=',name), '&', ('age', '=', age)])
     for user in userobj:
          await user.unlink()
```

# celertix framework Usage
Below Example for creating the res_user table


## Create Model Example


```python
from celertix.models.celertix_orm import Model
from celertix.models.celertix_fields import *


class ResUers(Model):
    _name = 'res.user'
    _id = 0
    _migrate = True

    name = Char(string="Name")
    age = Integer(string="Age")
    department = Char(string="Department")
    blood_group = Char(string="BloodGroup")
    create_dt = Datetime(string="CreateDt")
    write_dt = Date(string="WriteDt")
    salary = Float(string="Salary")

Model Variable Details
_name = 'res.user' #This is table name should be declare with '.' it will create the table with name res_user in postgres 

_id = 0 #This is should be declare for record id

_migrate = True #This is for field and table migration signal if you declare False the new fields of the model not create in the database if you declare True means declared fields are created in the database and also initally creating table you should use Ture then only table wil create in the database.
```

## Create Route Example

```
from celertrix.tools.celertrix_global import clx, Clxauth, ClxBlr,ClxRsp,ClassRegistry


resusersaction = ClxBlr('res_users', url_prefix='/users')

@resusersaction.route('/user/create', methods=['POST'])
async def action_create_user(request):
     vals = {'name': request.form.get('name'), 'age': int(request.form.get('age'))}
     userobj = await clxuniv.env['res.user'].create(vals=vals)
     print(userobj._id, userobj.name, userobj.age)
     return ClxRsp.json({'msg': 'Success Token','res_code': 2000, 'result': 'good'})

```

## Search Route Example
```
# http://localhost:8000/users/user/search?age=20&name=bala

@resusersaction.route('/user/search', methods=['GET'])
async def action_search_user(request):
     name = request.args.get('name')
     age = request.args.get('age')
     userobj_l = await clxuniv.env['res.user'].search([('name', '=',name), '&', ('age', '=', age)])
     for user in userobj_l:
          print(user._id)
     return ClxRsp.json({'msg': 'Success Token','res_code': 2000, 'result': 'good'})
```

## Write Route Example
```
@resusersaction.route('/user/update', methods=['POST'])
async def action_search_user(request):
     name = request.args.get('name')
     age = request.args.get('age')
     userobj = await clxuniv.env['res.user'].search([('name', '=',name), '&', ('age', '=', age)])
     for user in userobj:
          await user.write({'name': 'vignesh', 'age': 20})
     return ClxRsp.json({'msg': 'Success Token','res_code': 2000, 'result': 'good'})
```

## Delete Route Example
```

@resusersaction.route('/user/delete', methods=['POST'])
async def action_search_user(request):
     name = request.args.get('name')
     age = request.args.get('age')
     userobj = await clxuniv.env['res.user'].search([('name', '=',name), '&', ('age', '=', age)])
     for user in userobj:
          await user.unlink()
     return ClxRsp.json({'msg': 'Success Token','res_code': 2000, 'result': 'good'})
```

## Register the user route
```
ClxRegistry(resusersaction)
```



## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

Please make sure to update tests as appropriate.

## License
[MIT](https://choosealicense.com/licenses/mit/)
