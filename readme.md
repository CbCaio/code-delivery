## Sistema de delivery com Laravel 5.1 + Ionic

Roteiro de atividades realizadas no curso Laravel 5.1 + Ionic da CodeEducation. 
Criado para fixar meu aprendizado e servir como refer�ncias futuras.

###Cap�tulo 1: Criando a base do sistema

1. Gerar APP_KEY caso n�o tiver sido gerada na instala��o do laravel
  ```
    php artisan key:generate
  ```
2. Definir vari�veis no .env
3. Mudar nome da aplica��o 
  ```
    php artisan app:name [namespace]
  ```
4. Criar database
5. Em rela��o aos Models:
  - Criada pasta app\Models 
  - User transferi para nova paste e namespace corrigido (no config\auth.php tamb�m)
  - Category, Product, Client, Order, OrderItem models criados
6. Em rela��o �s Migrations criadas:
  - create_categories_table
  - create_products_table
  - create_clients_table
  - create_orders_table
  - create_order_items_table
7. Em rela��o �s Factories criadas:
  - Category
  - Product
  - Client
8. Em rela��o aos Seeds:
  - UserTableSeeder (Client sendo criado junto)
  - CategoryTableSeeder
  - ProductTableSeeder (n�o utilizado, ver CategoryTableSeeder)
9. Relacionamentos:
  - Category hasMany Product
  - Product belongsTo Category
  - User hasOne Client
  - Client hasOne User
  - Order hasMany OrderItem
  - Order belongsTo User
  - Order hasMany Product
  - OrderItem belongsTo Product
  - OrderItem belongsTo Order
  
###Cap�tulo 2: Repositories (padr�o de projeto)

1. https://github.com/andersao/l5-repository
  - php artisan vendor:publish
  - Arrumar repository.php rootNamespace e models
  - Recriar todos os models j� criados no Cap1 utilizando o make:repository
2. Criar Provider
  - Criar RepositoryServiceProvider (php artisan make:provider)
  - Fazer bind de todos os repositorios criados
  ```php
  /* dentro de register() */
  $this->app->bind(
  	'CodeDelivery\Repositories\CategoryRepository',
  	'CodeDelivery\Repositories\CategoryRepositoryEloquent'
  );
  ```
3. Adicionar repository criado a lista de providers (app.php)

###Cap�tulo 3: Sistema Administrativo

1. Instalar packages
  - https://github.com/bestmomo/scafold
    1. Adicionar ao composer.json
    ```
      "minimum-stability": "dev",
    ```
	2. composer require bestmomo/scafold
	3. Adicionar o Bestmomo\Scafold\ScafoldServiceProvider::class a lista de service provider
	4. php artisan vendor:publish
  - https://github.com/illuminate/html
	1. composer require illuminate/html
	2. Adicionar o Illuminate\Html\HtmlServiceProvider::class a lista de service provider
	3. Adicionar os Facades 
	```php
	  /* dentro de 'providers' em app.php */
      'Html'      => Illuminate\Html\HtmlFacade::class,
      'Form'      => Illuminate\Html\FormFacade::class,
	```
2. Controllers
  - CategoryController
  - ProductsController
3. Views
  - admin.categories
  - admin.products
4. Pagina��o
5. Rotas nomeadas 
6. Custom Requests
  - AdminCategoryRequest
  - AdminProductRequest
7. Refatorando Forms (_form)
8. Agrupando rotas
9. Middleware CheckRole (admin criado nas seeds)