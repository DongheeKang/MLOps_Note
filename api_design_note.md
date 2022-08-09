# API Design Notes

> RESTful API Services high level design notes.

<br/><a name="contents"></a>
## Contents

* [Flask API](#flask-api)
* [WSGI Servers](#wsgi-servers)
* [FastAPI](#fastapi)
* [SQLAlchemy](#SQLAlchemy)
* [Swagger](#swagger)



<br/><a name="flask-api"></a>
## Flask API

### Why flask API?
  * https://flask.palletsprojects.com/en/2.2.x/
  * alternative: Django

### Code-first approach

  * [Blueprint, Flask-RESTPlus, and Swagger](http://michal.karzynski.pl/blog/2016/06/19/building-beautiful-restful-apis-using-flask-swagger-ui-flask-restplus/) |
    [source](https://github.com/postrational/rest_api_demo/tree/master/rest_api_demo)
  * [Designing well-structed REST APIs with Flask-RestPlus](https://medium.com/ki-labs-engineering/designing-well-structured-rest-apis-with-flask-restplus-part-1-7e96f2da8850)
  * [Flask REST API with Swagger UI](https://towardsdatascience.com/working-with-apis-using-flask-flask-restplus-and-swagger-ui-7cf447deda7f)
  * [Flask and Flask-RESTPlus](https://nikgrozev.com/2018/10/12/python-api-with-flask-and-flask-restplus/)
  * Requirements:
    - flask
    - flask-restplus
    - fastapi (`pip install fastapi[all]`)
      * fastapi==0.26.0
      * aiofiles-0.4.0
      * aniso8601-3.0.2
      * dnspython-1.16.0
      * email-validator-1.0.4
      * graphene-2.1.3
      * graphql-core-2.1
      * graphql-relay-0.4.5
      * h11-0.8.1
      * httptools-0.0.13
      * promise-2.2.1
      * pydantic==0.26 (`pip install fastapi`)
      * python-multipart-0.0.5
      * rx-1.6.1 ujson-1.35
      * starlette==0.12.0 (`pip install fastapi`)
      * uvicorn-0.7.1
      * uvloop-0.12.2
      * websockets-7.0
    - flasgger (optional)
      * aniso8601==6.0.0
      * flasgger==0.9.2 (15M)
      * Flask-RESTful==0.3.7 (`pip install flask-restful`)
      * mistune==0.8.4 (with flasgger)

  * Example 1:

    ```python
    from flask import Flask
    from flask_restplus import Api, Resource

    app = Flask(__name__)
    api = Api(app = app)

    ns = api.namespace('main', description='Main APIs')

    @ns.route('/hello')               # Optional using namespace
    @api.route('/hello')              # Create a URL route to this resource
    class HelloWorld(Resource):       # Create a RESTful resource
        def get(self):                # Create GET endpoint
            return {'hello': 'world'}

    if __name__ == '__main__':
      app.run(debug=True)  
    ```


<br/><a name="wsgi-servers"></a>
## WSGI Server/Gateway/Application

  * [A Detailed Study of WSGI](https://www.cabotsolutions.com/2017/11/a-detailed-study-of-wsgi-web-server-gateway-interface-of-python)
  * An Introduction to Python WSGI Servers
    - [Part 1](https://www.appdynamics.com/blog/engineering/an-introduction-to-python-wsgi-servers-part-1/), [Part 2](https://www.appdynamics.com/blog/engineering/a-performance-analysis-of-python-wsgi-servers-part-2/)
  * [Choosing a Fast Python API Framework](https://fgimian.github.io/blog/2018/05/17/choosing-a-fast-python-api-framework/)
  * [Python WSGI Server Benchmark](https://github.com/kubeup/python-wsgi-benchmark)
  * [WSGI Servers](https://www.fullstackpython.com/wsgi-servers.html)
  * [uWSGI](http://flask.pocoo.org/docs/1.0/deploying/uwsgi/)

Reference:
  * Maximizing Python Performance with Nginx
    - [Part 1](https://www.nginx.com/blog/maximizing-python-performance-with-nginx-parti-web-serving-and-caching/), [Part 2](https://www.nginx.com/blog/maximizing-python-performance-with-nginx-part-ii-load-balancing-and-monitoring/)
  * [Full Stack Python](https://www.fullstackpython.com/table-of-contents.html)


<br/><a name="fastapi"></a>
## FastAPI

  * [FastAPI](https://fastapi.tiangolo.com/)
  * [uvicorn](https://www.uvicorn.org/)
  * [Gunicorn Documentation](https://buildmedia.readthedocs.org/media/pdf/gunicorn-docs/stable/gunicorn-docs.pdf)

* Example 1:
  
    ```python
    import uvicorn
    from fastapi import FastAPI

    app = FastAPI()

    @app.get("/")
    def read_root():
        return {"message": "Hello World"}

    @app.get("/items/{item_id}")
    def read_item(item_id: int, q: str = None):
        return {"item_id": item_id, "q": q}

    # intuitive way to define asynchronous code
    @app.get("/async/")
    async def async_read_root():
        return {"Hello": "World"}

    @app.get("/async/items/{item_id}")
    async def async_read_item(item_id: int, q: str = None):
        return {"item_id": item_id, "q": q}

    if __name__ == '__main__':
        uvicorn.run(app, host="0.0.0.0", port=8000, worker=10)
    ```
   
Reference:
  * [Full stack FastAPI PorstgresSQL base project](https://github.com/tiangolo/full-stack-fastapi-postgresql)
    - Docker integration
    - Starlette build asynchronize web services 
    - Pydantic type hints at runtime
    - SQLAlchemy models
    - CORS(Cross Origin Resource Sharing)
    - authentication with OAuth2 JWT tokens
    - Pytest for backend testing
    - PGAdmin for PostgreSQL 

<br/><a name="SQLAlchemy"></a>
## SQLAlchemy

* Example 1: creating DB session from SQLAlchemy
  
    ```python
    from sqlalchemy import Column, Integer, String
    import sqlalchemy import create_engine
    from sqlalchemy.orm import sessionmaker
    from sqlalchemy.ext.declarative import declarative_base

    Base = declarative_base()

    #engine = create_engine('sqlite:///sales.db', echo = True)
    engine = create_engine('postgresql://{username}:{password}@{host}:{port}/{db_name}'.format(
        username='postgres', password='superpassword', host='127.0.01',port='5432',db_name='database',
    ))

    Session = sessionmaker(autocommit=False, autoflush=False, bind=engine)
    db_session = Session()     

    class Customers(Base):
        __tablename__ = 'customers'
        id = Column(Integer, primary_key =  True)
        name = Column(String)
        address = Column(String)
        email = Column(String)

        '''    
        SELECT customers.id AS customers_id, 
        customers.name AS customers_name, 
        customers.address AS customers_address, 
        customers.email AS customers_email
        FROM customers
        '''
      
    result = db_session.query(Customers).all()

    if __nemae__ == '__main__':
        
        for row in result:
            print ("Name: ",row.name, "Address:",row.address, "Email:",row.email)

    ```

**Note**: 
    - please take care about the scoped_session with async mode (from sqlalchemy.orm import scoped_session)


<br/><a name="swagger"></a>
## Swagger

  The API specifications is designed in [swagger-editor](https://editor.swagger.io/).
  On a dev box with docker installed, `Makefile` provides a script to run a swagger-editor locally (in docker container):

  ```
  make swagger-editor  # this will open swagger-editor on http://localhost:8881
  # or with specific port
  SWAGGER_PORT=9980 make swagger-editor
  ```
  **Note**:
  - The swagger-editor is a distributed web app with embedded "Petstore" spec
  - Open or drag-and-drop [swagger.yaml](../apidoc/v1)
