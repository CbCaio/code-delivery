## Sistema de delivery com Laravel 5.1 + Ionic

###Cap�tulo 1: Criando a base do sistema

- Gerar APP_KEY caso n�o tiver sido gerada na instala��o do laravel
	php artisan key:generate

- Definir vari�veis no .env

- Mudar nome da aplica��o 
	php artisan app:name [namespace]

- Criar database

- Em rela��o aos Models:
	Cria��o pasta app\Models 
	Transferir User para a pasta e corrigir namespace e config\auth.php
	Category, Product, Client, Order, OrderItem models criados
	
- Em rela��o �s Migrations:
	create_categories_table
	create_products_table
	create_clients_table
	create_orders_table
	create_order_items_table
	
- Em rela��o �s Factories:
	Category, Product, Client factories criadas
	
- Em rela��o aos Seeds:
	UserTableSeeder (Client sendo criado junto), CategoryTableSeeder, ProductTableSeeder (n�o utilizado, ver CategoryTableSeeder)
	
- Relacionamentos:
	Category hasMany Product
	Product belongsTo Category
	User hasOne Client
	Client hasOne User
	Order hasMany OrderItem
	Order belongsTo User
	Order hasMany Product
	OrderItem belongsTo Product
	OrderItem belongsTo Order