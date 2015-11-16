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

###Cap�tulo 4: Clientes

1. ClientsController
2. AdminClientRequest
3. Rotas de admin.clients
4. Views de clients
5. CRUD Clients
6. ClientService
  - Recebe os repositorios de Cliente e de Usu�rio como par�metros em seu construtor
  - create e update, utilizado para criar um usu�rio automaticamente com uma senha padr�o antes de executar o create e o update do repository
  
###Cap�tulo 5: Pedidos

1. OrdersTableSeeder
  - Colocar na ModelFactory Order e OrderItem
2. OrdersController
3. Criar views admin.orders.index e edit
4. Rela��o de Order com Client
  - OrderBelongsToClient
5. Seed para deliveryman
6. Fun��o getDeliveryman no UserRepository para criar select de deliveryman

###Cap�tulo 6: Checkout

1. php artisan make:repository Cupom
2. php artisan make:migration create_cupoms_table --create=cupoms
  - aten��o: adicionar foreign key cupom_id na tabela orders e fun��o down
  ```php
  Schema::table('orders',function(Blueprint $table) {
		$table->dropForeign('orders_cupom_id_foreign');
		$table->dropColumn('cupom_id');
	});
  ```
3. Criar Seeder, Factory e adicionar fillables
4. CupomsController
5. Rotas e views para admin.cupoms
6. Refatorar _form e fazer AdminCupomRequest
7. CheckoutController com depencias de OrderRepository, UserRepository e ProductRepository
8. Cria��o novas rotas e views a partir de 'customer' (no lugar de admin)
9. Javascript para adicionar novos produtos na tela de pedidos 
10. Cria��o do Service para as orders. Conceitos importantes: 
  - \DB::beginTransaction()
  - try/catch 
  - \DB::commit (dentro do try)
  - DB::rollback() (dentro to catch)	
  - OrderService com dependencias de OrderRepository, CupomRepository e ProductRepository
11. Rotas e fun��es para customer.index e customer.create
12. Permiss�es de usu�rios (altera��o no middleware checkrole)

###Cap�tulo 7: OAuth 2

1. Instalando package https://github.com/lucadegasperi/oauth2-server-laravel/wiki
2. Corrigir CSRF (middleware -> kernel.php)
  - Configurar dentro do VerifyCsrfToken para ignorar as rotas da API
  ```php
   protected $except = [
        'oauth/access_token',
		'api/*'
    ];
  ```
3. Authorization Server
  - https://github.com/lucadegasperi/oauth2-server-laravel/wiki/Choosing-a-Grant
  - Escolhido: https://github.com/lucadegasperi/oauth2-server-laravel/wiki/Implementing-an-Authorization-Server-with-the-Password-Grant ```php
  'password' => [
		'class' => '\League\OAuth2\Server\Grant\PasswordGrant',
		'callback' => '\CodeDelivery\OAuth2\PasswordVerifier@verify',
		'access_token_ttl' => 3600
	]
  ```
  - Criar client se for testar requisi��o do token
4. Refresh token
  - https://github.com/lucadegasperi/oauth2-server-laravel/wiki/Implementing-an-Authorization-Server-with-the-Refresh-Token-Grant
5. Criando rotas para api
  ```php
  Route::group(['prefix' => 'api', 'middleware' => 'oauth' , 'as' => 'api.'], function() 
  {
     //
  });
  ```