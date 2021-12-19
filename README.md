Larael8 Inertial Js Single Page Application

Laravel8 Vue Js Single Page Application For Beginners

Step 1: Install Laravel 8

Now, we need to install laravel 8 app using composer command. Run below command to install

composer global require laravel/installer

// and then run

laravel new inertia —jet

Step 2: Create Model

Here, we need to create database migration for articles table and also we will create model for articles table.
php artisan make:model Article -m

database\migrations

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateArticlesTable extends Migration
{
/**

     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('articles', function (Blueprint $table) {
            $table->id();
            $table->string('title');
            $table->timestamps();
        });
    }
    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('articles');
    }

}

Now run php artisan migrate command to create article table.

App/Models/Article.php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Article extends Model
{
use HasFactory;

    protected $fillable = [
      'title'
    ];

}

Step 3: Create Route

In third step, we will create routes for crud app. so create route here.

routes/web.php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\ArticleController;

Route::get(‘/‘, function () { return view(‘welcome’); });

Route::middleware([‘auth:sanctum’, ‘verified’])->get(‘/dashboard’, function () {
return Inertia\Inertia::render(‘Dashboard’);
})->name(‘dashboard’);

Route::get(‘/article’, [ArticleController::class, ‘index’]);
Route::post(‘/article’, [ArticleController::class, ‘store’]);
Route::patch(‘/article/edit/{id}’, [ArticleController::class, ‘update’]);
Route::delete(‘/article/delete/{id}’, [ArticleController::class, ‘delete’]);

Step 4: Create Controller

In this step, we will create article controller file and add following code on it.

app/Http/Controllers/ArticleController.php

namespace App\Http\Controllers;

use Inertia\Inertia;
use App\Models\Article;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Validator;

class ArticleController extends Controller
{
public function index(Article $article)
{
return Inertia::render(
‘Article’,
[
‘data’ => $article->latest()->get()
]
);
}

    public function store(Request $request)
    {
        Validator::make($request->all(), [
            'title' => ['required'],
        ])->validate();
        Article::create($request->all());
        return redirect()->back()
                    ->with('message', 'Article Created Successfully.');
    }
    public function update(Request $request)
    {
        Validator::make($request->all(), [
            'title' => ['required'],
        ])->validate();
        if ($request->has('id')) {
            Article::find($request->input('id'))->update($request->all());
            return redirect()->back()
                    ->with('message', 'Post Updated Successfully.');
        }
    }
    public function delete(Request $request)
    {
        $request->has('id') ? 
                Article::find($request->input('id'))->delete() :
                redirect()->back()
                    ->with('errors', 'Somethings goes wrong.');
        return redirect()->back()
                    ->with('message', 'Article deleted successfully.');
    }

}

Step 5: Share Inertia Var Globally

Here, i will create ‘message’ and ‘errors’ variable for success message and validation error so. we need to share this variables on appservices provider so that we can call it from our view component.

app/Providers/AppServiceProvider.php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use Illuminate\Support\Facades\Session;
use Inertia\Inertia;

class AppServiceProvider extends ServiceProvider
{
/**

     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
    }
    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        Inertia::share([
            'errors' => function () {
                return Session::get('errors')
                    ? Session::get('errors')->getBag('default')->getMessages()
                    : (object) [];
            },
        ]);
        Inertia::share('flash', function () {
            return [
                'message' => Session::get('message'),
            ];
        });
    }

}

Step 6: Create View Template

Here, we need to create article page. View file where we will write code to list of article and create and update model code.

resources/js/Pages/Article.vue

Now we need to compile our js code. run below command. so let’s run it as bellow:

npm install
//then
npm run watch

Now just visit below link to see crud operation using inertia js with Laravel

LINK
http://127.0.0.1:8000/article
or
localhost:8000/article

---------------- X ----------------------

 
 










