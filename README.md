<p align="center"><a href="https://laravel.com" target="_blank"><img src="https://raw.githubusercontent.com/laravel/art/master/logo-lockup/5%20SVG/2%20CMYK/1%20Full%20Color/laravel-logolockup-cmyk-red.svg" width="400" alt="Laravel Logo"></a></p>

# Creación de una API

## Definiciones
- #### API:
    - Una aplicacion que permite comunicar otra aplicacion a una base de datos.

- #### SOAP
    - Es un protocolo estándar que define cómo dos objetos en diferentes procesos pueden comunicarse por medio de intercambio de datos XML

- #### REST
    - Especificacion de como crear las API's

- #### Rutas:

    - ##### Controladores
        - Parte encargada de coger los datos de la base de datos y entregarlos

    - ##### Resources
        - Se utiliza para almacenar recursos que son necesarios para el funcionamiento de la API.

    - #### Request
        - Donde se validan los datos

    - ##### Middleware
        - Es una clase con un manejador cuyo contenido al ejecutarse entre la solicitud y la respuesta que validara algo

    - ##### Collection
        - Se refiere a una colección de archivos que contienen ejemplos de solicitudes y respuestas para la API.

    - ##### Modelo
        -  Es una clase que va a interactuar directamente con una tabla de la base de datos

    - ##### Migracion
        - Crear las tablas de la base de datos. Me permite modificar comodamente el ddl, tiene 2 metodos, Up y Down

    - ##### Factory
        - Creamos los datos para los campos para la tabla

    - ##### Seeders
        - Poblamos la base de datos.  
          A partir de una base centralizada llamada DatabaseSeeder, lo que hace esta clase es llenar la base de datos con información de manera automatizada.


## Práctica

Abrimos una **terminal** y en la carpeta laravel creamos un nuevo proyecto

*~/laravel*

```bash
laravel new NombreProyecto
```
- ##### Opciones de la descarga
    - Predeterminadas
    - Base de datos: **MySql**

<hr>


### Creamos migrate y factory

*~/laravel/nombre_proyecto*

```bash
php artisan make:model Nombre --api -fm  
```

### Archivos .env y docker-compose

Ruta: **/**

#### .env

```php
APP_NAME=Laravel
APP_ENV=local
APP_KEY=base64:2LsZA1WpEGaIoFovyisJ8NXwi+oFyqCmn9nRmLk/Sxg=
APP_DEBUG=true
APP_URL=http://localhost

LOG_CHANNEL=stack
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=23306
DB_DATABASE=instituto
DB_USERNAME=alumno
DB_PASSWORD=alumno
DB_PASSWORD_ROOT=root
DB_PORT_PHPMYADMIN=8080

BROADCAST_DRIVER=log
CACHE_DRIVER=file
FILESYSTEM_DISK=local
QUEUE_CONNECTION=sync
SESSION_DRIVER=file
SESSION_LIFETIME=120

MEMCACHED_HOST=127.0.0.1

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_MAILER=smtp
MAIL_HOST=mailpit
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS="hello@example.com"
MAIL_FROM_NAME="${APP_NAME}"

AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=
AWS_USE_PATH_STYLE_ENDPOINT=false

PUSHER_APP_ID=
PUSHER_APP_KEY=
PUSHER_APP_SECRET=
PUSHER_HOST=
PUSHER_PORT=443
PUSHER_SCHEME=https
PUSHER_APP_CLUSTER=mt1

VITE_APP_NAME="${APP_NAME}"
VITE_PUSHER_APP_KEY="${PUSHER_APP_KEY}"
VITE_PUSHER_HOST="${PUSHER_HOST}"
VITE_PUSHER_PORT="${PUSHER_PORT}"
VITE_PUSHER_SCHEME="${PUSHER_SCHEME}"
VITE_PUSHER_APP_CLUSTER="${PUSHER_APP_CLUSTER}"

LANG_FAKE ="es_ES"
```

<hr>

Ruta: **/**

#### docker-compose.yaml

```php
#Nombre de la version
version: "3.8"
services:
  mysql:
    # image: mysql <- Esta es otra opcion si no hacemos el build
    image: mysql

    
    volumes:
      - ./datos:/var/lib/mysql # Para no perder los datos cuando destruyamos el contenedor, se guardara en ese derectorio
    ports:
      - ${DB_PORT}:3306
    environment:
      - MYSQL_USER=${DB_USERNAME}
      - MYSQL_PASSWORD=${DB_PASSWORD}
      - MYSQL_DATABASE=${DB_DATABASE}
      - MYSQL_ROOT_PASSWORD=${DB_PASSWORD_ROOT}

    phpmyadmin:
        image: phpmyadmin
        container_name: phpmyadmin  #Si no te pone por defecto el nombre_directorio-nombre_servicio
        ports:
        - ${DB_PORT_PHPMYADMIN}:80
        depends_on:
        - mysql
        environment:
        PMA_ARBITRARY: 1 #Para permitir acceder a phpmyadmin desde otra maquina
        PMA_HOST: mysql

```

<hr>

## Docker compose

Levantamos el docker con la base de datos.

```bash
docker compose up -d  
```

Si ya tenemos un docker levantado y tenemos conflicto los paramos y eliminamos para asegurarnos de que se levanta correctamente.

```bash
docker stop $(docker ps -a -q)  
```

```bash
docker rm $(docker ps -a -q)
```

Una vez hecho esto levantamos el docker.

<br>

## Iniciamos el servidor

```bash
php artisan serve
```

<br>

## Poblamos la base de datos

Ruta: **database/factories/NombreFactory.php**

#### NombreFactory.php

```php
public function definition(): array
{
    return [
    "nombre" =>fake()->name(),
    "direccion" =>fake()->address(),
    "email" =>fake()->email()
    ];
}
```

<br><br>

Ruta: **database/seeders/DatabaseSeeder.php**

#### DatabaseSeeder.php

```php
public function run(): void
{
    Nombre::factory(20)->create();
}
```
<br><br>

Si queremos cambiar el idioma de los datos que vamos a crear tenemos que modificar el idioma.

Ruta: **config/app.php**

#### app.php

```php
'faker_locale' => 'es_ES',  
```
<br><br>

Ruta: **database/migrations/2024_02_20_092559_create_nombre_table.php**


#### 2024_02_20_092559_create_nombre_table.php

```php
Schema::create('nombre_plural', function (Blueprint $table) {
        $table->id();
        $table->string("nombre");
        $table->string("direccion");
        $table->string("email");
        $table->timestamps();
});
```

<br><br>

Ahora ejecutamos el comando para poblar la base con los requisitos que le hemos dado.

```bash
php artisan migrate --seed 
```
<br>

## Creacion resource y request

```bash 
php artisan make:request NombreFormRequest
```
```bash
php artisan make:resource NombreResource
```
```bash
php artisan make:resource NombreCollection
```

<br><br>

Ruta: **app/Http/Resources/NombreCollection.php**

#### NombreCollection.php

```php
public function toArray(Request $request): array
{
return parent::toArray($request);
}
```

```php
public function with(Request $request)
    {
    return[
        "jsonapi" => [
            "version"=>"1.0"
        ]
    ];
}
```
<br><br>

Ruta: **app/Http/Resources/NombreResoruces.php**

#### NombreResoruces.php

```php
public function toArray(Request $request): array{
    return[
        "id"=>$this->id,
        "type" => "Nombre",
        "attributes" => [
            "nombre"=>$this->nombre,
            "direccion"=>$this->direccion,
            "email"=>$this->email,
        ],
        "link"=>url('api/nombre_plural'.$this->id)
    ];
}
```

<br><br>

Ruta: **routes/api.php**

#### api.php

```php
use \App\Http\Controllers\NombreController;

Route::apiResource("nombre_plural",NombreController::class);
```

<br><br>

Ruta: **app/Http/Controllers/NombreController.php**

#### NombreController.php

```php
public function index()
{
    $nombre_plural = Nombre::all();
    return response()->json($nombre_plural);
}
```

```php
public function show(Nombre $nombre)
{
    return new NombreResource($nombre);
} 
```

<br>

## Controlar errores cuando no es posible conectarse a la base de datos

Ruta: **app/Exceptions/Handler.php**

#### Handler.php

```php
use Illuminate\Database\QueryException;

public function render($request, Throwable $exception)
{
    // Errores de base de datos)
    if ($exception instanceof QueryException) {
        return response()->json([
            'errors' => [ 
                [
                    'status' => '500',
                    'title' => 'Database Error',
                    'detail' => 'Error procesando la respuesta. Inténtelo más tarde.'
                ]
            ]
        ], 500);
    }
    // Delegar a la implementación predeterminada para otras excepciones no manejadas
    return parent::render($request, $exception);
}
```

<br><br>

Podemos comprobar en el navegador si aparecen, en este caso, los alumnos que hemos creado.

```bash
http://localhost:8000/api/alumnos/
```

<br>

Para comprobar que nos da el error de la base de datos tiramos el docker.

```bash
docker compose down
```

<br>

## Creamos el middleware

```bash 
php artisan make:middleware HandleMiddleware
```

<br><br>

Ruta: **app/Http/Middleware/HandleMiddleware.php**

#### HandleMiddleware.php

```php  
public function handle(Request $request, Closure $next): Response
{
    if ($request->header('accept') != 'application/vnd.api+json') {
        return response()->json([
            "errors"=>[
                "status"=>406,
                "title"=>"Not Accetable",
                "deatails"=>"Content File not specifed"
            ]
        ],406);
    }
    return $next($request);
}
```

<br><br>

Ruta: **app/Http/Kernel.php**

#### Kernel.php
Dentro de middelwareGroups -> api

```php
HandleMiddleware::class;
```
<br>

## Execpciones para usuarios no encontrados

Ruta: **app/Http/Controllers/NombreController.php**

#### NombreController.php

```php
public function store(NombreFormRequest $request)
{
    $datos = $request->input("data.attributes");
}
```

<br><br>

Ruta: **app/Http/Requests/NombreRequest.php**

#### NombreRequest.php

```php
public function authorize(): bool
{
    return true;
}

public function rules(): array
{
    return [
        "data.attributes.nombre"=>"required|min:5",
        "data.attributes.direccion"=>"required",
        "data.attributes.email"=>"required|email|unique:alumnos"
    ];
}
```

<br>

## Otros errores (Validación)

Ruta: **app/Exceptions/Handler.php**

#### Handler.php

```php

use Illuminate\Http\JsonResponse;
use Illuminate\Validation\ValidationException;

protected function invalidJson($request, ValidationException  $exception):JsonResponse
{
    //Sirve para devolver un error por cada uno de los no requitsitos que cumplan cada dato de mi formulario
    return response()->json([
        'errors' => collect($exception->errors())->map(function ($message, $field) use
        ($exception) {
            return [
                'status' => '422',
                'title' => 'Validation Error',
                'details' => $message[0],
                'source' => [
                    'pointer' => '/data/attributes/' . $field
                ]
            ];
        })->values()
    ], $exception->status);
}
```

<br><br>

Ruta: **app/Exceptions/Handler.php**

#### Handler.php

Render()

```php
    //Sirve para llamar al método invalidJson
    if ($exception instanceof ValidationException) {
        return $this->invalidJson($request, $exception);
    }
```

<br>

## POSTMAN

Vamos a comprobar que aparece la informacion en postman.

- Ponemos la dirección, dejamos seleccionado get

  Ejemplo de dirección: http://localhost:8000/api/alumnos/

- Creamos un nuevo **Header**
    - Nombre: Accept
    - Value: application/vnd.api+json

- Por último comprobamos que se ve toda la informacion


<br>


## Creación de articulos

Ruta: **app/http/Models/Nombre.php**

#### Nombre.php

```php
protected $fillable = ["nombre","direccion","email"];
```

<br><br>

Ruta: **app/http/Controllers/NombreController.php**

#### NombreController.php

```php
public function store(NombreFormRequest $request)
{
    $datos = $request->input("data.attributes");
    $nombre = new Nombre($datos);
    $nombre->save();

    return new NombreResource($nombre);
}
```

<br><br>

Ruta: **app/http/Request/NombreFormRequest.php**

#### NombreFormRequest.php

```php
public function rules(): array
{
    return [
    "data.attributes.nombre"=>"required|min:5",
    "data.attributes.direccion"=>"required",
    "data.attributes.email"=>"required|email|unique:alumnos,email"
    ];
}
```  

<br><br>

Comprobamos que podemos añadir nueva información en **POSTMAN**

Cambiamos a metodo **POST**

Para ello nos vamos al apartado **Body** -> **raw** y escribimos lo siguiente


```bash
{
    "data": 
    {
        "type": "Nombre_Plural",
        "attributes": 
        {
            "nombre": "Nacho",
            "direccion": "Casa",
            "email": "a@a.com"
        }
    }
}
```

<br>

## Eliminación del articulo

Ruta: **app/http/Controllers/NombreController.php**

#### NombreController.php

```php
public function destroy(int $id)
{
    $nombre = Nombre::find($id);
    if (!$nombre) {
        return response()->json([
                'errors' => [
                    [
                    'status' => '404',
                    'title' => 'Resource Not Found',
                    'detail' => 'The requested resource does not exist or could not be found.'
                    ]
                ]
        ], 404);
    }

    $nombre->delete();
    return response()->json(null,204);
    //No devuelv econtenido
    return response()->noContent();
}
```

Comprobamos que podemos eliminar información en **POSTMAN**

Cambiamos a metodo **DELETE**

Eliminaremos con la id que especifiquemos en la URL. http://127.0.0.1:8000/api/nombre/id


<br>

## Actualizar datos

Ruta: **app/http/Controllers/NombreController.php**

#### NombreController.php

```php
public function update(Request $request, int $id)
{
    $nombre = Nombre::find($id);
    
    if (!$nombre) {
        return response()->json([
            'errors' => [
                [
                'status' => '404',
                'title' => 'Resource Not Found',
                'detail' => 'The requested resource does not exist or could not be found.'
                ]
            ]
        ], 404);
    }

    $verbo = $request->method();
    //En función del verbo creo unas reglas de
    // validación u otras
    if ($verbo == "PUT") { //Valido por PUT
        $rules = [
            "data.attributes.nombre" => ["required", "min:5"],
            "data.attributes.direccion" => "required",
            "data.attributes.email" => ["required", "email", Rule::unique("nombre_plural", "email")->ignore($nombre)]
        ];

    } else { //Valido por PATCH
        if ($request->has("data.attributes.nombre"))
            $rules["data.attributes.nombre"]= ["required", "min:5"];
        if ($request->has("data.attributes.direccion"))
            $rules["data.attributes.direccion"]= ["required"];
        if ($request->has("data.attributes.email"))
            $rules["data.attributes.email"]= ["required", "email", Rule::unique("alumnos", "email")->ignore($nombre)];
    }

    $datos_validados = $request->validate($rules);

    foreach ($datos_validados['data']['attributes'] as $campo=>$valor)
        $datos[$campo] = $valor;

    $nombre->update($request->input("data.attributes"));

    return new NombreResource($nombre);
}
```

Comprobamos que podemos modificar información en **POSTMAN**

Cambiamos a metodo **PUT** (Obligatorio poner todos los datos aunque no los modifiquemos) o **PATCH** (No es necesario poner todos, solo los que vamos a modificar)

Modificaremos con la id que especifiquemos en la URL. http://127.0.0.1:8000/api/nombre/id

Para ello nos vamos al apartado **Body** -> **raw** y escribimos lo que queremos modificar

```bash
{
    "data": {
        "type": "Alumnos",
        "attributes": {
                "nombre": "Modificacion"
        }
    }
}
```
