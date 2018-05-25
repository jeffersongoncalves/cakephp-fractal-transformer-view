# FractalTransformerView plugin for CakePHP

This plugin is a thin wrapper for `JsonView` that allows using [Fractal transformers][fractal-transformer] for your API output. What is [Fractal][fractal]?

> Fractal provides a presentation and transformation layer for complex data output, the like found in RESTful APIs, and works really well with JSON. Think of this as a view layer for your JSON/YAML/etc.
> When building an API it is common for people to just grab stuff from the database and pass it to json_encode(). This might be passable for “trivial” APIs but if they are in use by the public, or used by mobile applications then this will quickly lead to inconsistent output.


## Requirements

- CakePHP 3.6+

## Installation

You can install this plugin into your CakePHP application using [Composer][composer].

```bash
composer require jeffersonsimaogoncalves/cakephp-fractal-transformer-view
```


## Usage
To enable the plugin set `FractalTransformerView.FractalTransformer` class name for viewBuilder. Then you just do what you would normally do in your [data views](http://book.cakephp.org/3.0/en/views/json-and-xml-views.html) - specify which view vars you want to get serialized by setting `_serialize`  var. E.g.:

```php
namespace App\Controller;

class ArticlesController extends AppController
{
    public function initialize()
    {
        parent::initialize();
        $this->loadComponent('RequestHandler');
        
        $this->viewBuilder()->className('FractalTransformerView.FractalTransformer');
    }

    public function index()
    {
        // Set the view vars that have to be serialized.
        $this->set('articles', $this->paginate());
        // Specify which view vars JsonView should serialize.
        $this->set('_serialize', ['articles']);
    }
}
```

The view will look for transformer class starting with entity name. E.g.:

```php
namespace App\Model\Transformer;

use App\Model\Entity\Article;
use League\Fractal\TransformerAbstract;

class ArticleTransformer extends TransformerAbstract
{
    /**
     * Creates a response item for each instance
     *
     * @param Article $article post entity
     * @return array transformed post
     */
    public function transform(Article $article)
    {
        return [
            'title' => $article->get('title')
        ];
    }
}
```

If transformer class not found the variable is serialized the normal way.

Custom transformer class name can be set by defining `_transformer` view var:

```php
$this->set('_transform', ['articles' => '\App\Model\Transformer\CustomArticleTransformer']);
```

You can also define if you don't want to use transformer for certain variables:

```php
$this->set('_transform', ['articles' => false]);
```

## Bugs & Feedback

https://github.com/jeffersonsimaogoncalves/cakephp-fractal-transformer-view/issues

## Credits

This work is based on the [code by Andrej Griniuk](https://github.com/andrej-griniuk/cakephp-fractal-transformer-view).

[cakephp]:http://cakephp.org
[composer]:http://getcomposer.org
[fractal]:http://fractal.thephpleague.com/
[fractal-transformer]:http://fractal.thephpleague.com/transformers/
