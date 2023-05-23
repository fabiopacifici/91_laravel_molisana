# laravel primi passi layout completo

```bash

composer create-project laravel/laravel:^9.2 project_name
cd project_name
composer require pacificdev/laravel_9_presets
// esegui I comandi suggeriti dal pacchetto
```

### Creazione app.blade.php

```html
<!-- resources/views/layouts/app.blade.php -->
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">

<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">

    <title>@yield('page-title', 'Laravel Molisana')</title>
    <!-- Fonts -->
    <link href="https://fonts.bunny.net/css2?family=Nunito:wght@400;600;700&display=swap" rel="stylesheet">

    <!-- Styles -->
    @vite('resources/js/app.js')
</head>

<body class="">
    <!--  includiamo l'header del sito web con il menu tramite un partial -->
    @include('partials.header')

    <main>
        @yield('content')
    </main>

    <!-- includiamo il footer del sito web tramite un partial -->
    @include('partials.footer')

</body>

</html>
```

Ora passiamo alla creazione dei due parziali (header e footer) in `resources/views/partials/`

```html
<!-- resources/views/partials/header.blade.php -->
<header>
    logo
</header>
```

creiamo il parziale per il footer

```html
<!-- resources/views/partials/footer.blade.php -->
<footer>
    copyright
</footer>

```

Per far riferimento agli assets nel template blade possiamo usare `{{Vite::asset()}}`
Proviamo ad include usando questo metodo il logo dentro all'header

```html
<header>
    <img src="{{ Vite::asset('resources/img/logo.png') }}" alt="">
</header>
```

Per far riferimento agli assets dal css, usiamo semplicemente i percorsi relativi e vite fará il resto.
Questi assets vengono poi versionati quando faremo la build con il comando `npm run build`.

```scss
// app.scss
body {
    background-image: url('../img/page_bg.jpg');
    background-size: cover;
}
```

## Layout la Molisana

Iniziamo con l'aggiungere un menu all'header e complatare il footer per poi passare all'aggiunta delle rotte.

```html
<!-- resources/partials/header.blade.php -->
<header class="bg-white">
    <div id="logo" class="text-center">
        <img src="{{ Vite::asset('resources/img/logo.png') }}" alt="">
    </div>
    <div id="main-menu">

        <!-- // facciamo vedere un esempio di come possiamo riciclare lo stesso layout
        // creando 2/3 paginette diverse random -->
        <nav class="navbar-nav container navbar-light">
            <ul class="list-unstyled d-flex justify-content-center gap-2 text-uppercase">
                <li class="nav-item">
                    <a class="nav-link" href="{{ url('/') }}">
                        <strong>Home</strong>
                    </a>
                </li>
                <!-- 👉 Attenzione, se non si é creata prima la rotta, otterremo un errore route not found -->
                <li class="nav-item">
                    <a class="nav-link" href="{{ route('products') }}">
                        Prodotti
                    </a>
                </li>
                <!-- 👉 Attenzione, se non si é creata prima la rotta, otterremo un errore route not found -->
                <li class="nav-item">
                    <a class="nav-link" href="{{ route('blog') }}">
                        News
                    </a>
                </li>
            </ul>
        </nav>
    </div>
</header>
```

Aggiungiamo del boilerplate e l'immagine al footer

```html
<!-- footer.blade.php -->
<footer>
    <div class="container">
        footer content
    </div>
    <img src="{{ Vite::asset('resources/img/footer_bg.jpg') }}" alt="">
</footer>

```

### Creazione Rotte

```php

Route::get('/products', function () {
    // Prima di aggiungere questa variabile, bisogna aver creato i dati nel file /config/db.php
    $products = config('db.pasta');
    //dd($products);
    return view('products', compact('products'));
})->name('products');


Route::get('/blog', function () {
    // Aggiungiamo una lista di posts nel file db.php e poi usiamo i dati per far vedere agli studenti tutto il giro da capo.
    //$posts = config('db.posts');
    return view('blog');
})->name('blog');
```

### Creazione e Aggiunta dei dati al file config/db.php

```php
//config/db.php

return [
    'pasta' => [
        [
            "src" => "https://www.lamolisana.it/wp-content/uploads/2021/09/4-spaghetto-quadrato-bucato.jpg",
            "src-h" => "https://www.lamolisana.it/wp-content/uploads/2021/09/4-spaghetto-quadrato-bucato.png",
            "src-p" => "https://www.lamolisana.it/wp-content/uploads/2021/09/spaghetto-quadrato-bucato.jpg",
            "titolo" => "N.4 Spaghetto Quadrato Bucato",
            "tipo" => "lunga",
            "cottura" => "6 min",
            "peso" => "500g",
            "descrizione" => "È la vera rivelazione della gamma! Lo Spaghetto Quadrato Bucato n.4 fa pensare subito ad una pasta molto succulenta che ha lo stesso potenziale dello Spaghetto Quadrato (N.1 Spaghetto Quadrato). La sua consistenza soda si sprigiona in bocca con un’esplosione di emozioni, grazie agli spessori corposi, al colore elegantemente ambrato, alla texture delicatamente ruvida, cangiante e piacevolissima al tatto che trattiene il condimento sulla superficie. <br> Da provare per rivoluzionare le sorti del primo piatto sia a casa che al ristorante.Dedicato a chi in cucina ama sperimentare nuove forme del gusto, ma vuole stupire affidandosi ad una pasta che garantisce ottime performance in cottura, lo Spaghetto Quadrato Bucato n .4 è il formato perfetto che racchiude tutte caratteristiche uniche di Pasta La Molisana. <br> Da provare per quelli che… il Bucatino già mi piace, lo Spaghetto Quadrato Bucato n .4 sarà il paradiso della pasta!"
        ],
        /* .... */
    ],

    // Se c'é tempo
    'posts' => [
        /* add posts here */
         [
            'title' => 'learn php',
            'body' => 'Lorem, ipsum dolor sit amet consectetur adipisicing elit. Laboriosam, quos.',
            'img' => 'https://picsum.photos/400/200'
        ],
    ]

];

```

### Creazione Products view

In questa fase creiamo la view per la rotta `/products` e stampiamo a schermo una card per ciascun prodotto.

```html
@extends('layouts.app')
@section('page-title', 'Tutti i prodotti - La Molisana')
@section('content')
<div class="prodotti">
    <div class="container">
        <h2 class="my-4">Tutti i prodotti</h2>
        <div class="row row-cols-1 row-cols-sm-2 row-cols-md-3 row-cols-xl-4 g-4">
            @foreach ($products as $indice => $formato)
            <div class="col">
                <div class="card border-0">
                    <img class="img-fluid card-img-top" src="{{ $formato['src'] }}" alt="{{ $formato['titolo'] }}">
                    <div class="card-body">
                        <h4 class="card-title">
                            {{ $formato['titolo'] }}
                        </h4>
                    </div>
                </div>
            </div>
            @endforeach
        </div>
    </div>
</div>
@endsection

```

## Rotte finali

```php
Route::get('/', function () {
    return view('welcome');
});


Route::get('/products', function () {
    $products = config('db.pasta');
    //dd($products);
    return view('products', compact('products'));
})->name('products');


Route::get('/blog', function () {
    // Aggiungiamo una lista di posts nel file db.php
    $posts = config('db.posts');
    return view('blog', compact('posts'));
})->name('blog');

```

## Layout finale

app.blade.php file

```html
<!-- layouts/app.blade.php -->

<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">

<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">

    <title>@yield('page-title', 'Laravel Molisana')</title>
    <!-- Fonts -->
    <link href="https://fonts.bunny.net/css2?family=Nunito:wght@400;600;700&display=swap" rel="stylesheet">


</head>

<body class="">
    <!--  includiamo l'header del sito web con il menu tramite un partial -->
    @include('partials.header')

    <main>
        @yield('content')
    </main>

    <!-- includiamo il footer del sito web tramite un partial -->
    @include('partials.footer')
    <!-- Assets JS/CSS -->
    @vite('resources/js/app.js')
</body>

</html>

```

header.blade.php file

```html
<!-- /resources/views/partials/header.blade.php -->
<header class="bg-white">
    <div id="logo" class="text-center">
        <img src="{{ Vite::asset('resources/img/logo.png') }}" alt="">
    </div>
    <div id="main-menu">

        <!-- // facciamo vedere un esempio di come possiamo riciclare lo stesso layout
        // creando 2/3 paginette diverse random -->
        <nav class="navbar-nav container navbar-light">
            <ul class="list-unstyled d-flex justify-content-center gap-2 text-uppercase">
                <li class="nav-item">
                    <a class="nav-link" href="{{ url('/') }}">
                        <strong>Home</strong>
                    </a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="{{ route('products') }}">
                        Prodotti
                    </a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="{{ route('blog') }}">
                        News
                    </a>
                </li>
            </ul>
        </nav>
    </div>
</header>

```

Footer completo

```html
<!-- /resources/views/partials/footer.blade.php -->

<footer class="bg-white mt-5 pt-5">
    <div class="container">
        <div class="row">
            <div class="col">
                <div id="logo" class="">
                    <img src="{{ Vite::asset('resources/img/logo.png') }}" alt="">
                </div>
                <p class="lead">Lorem ipsum dolor sit amet.</p>
                <!-- /.list-unstyled -->
            </div>
            <div class="col">
                <h4 class="text-uppercase">Quick links</h4>
                <ul class="list-unstyled">
                    <li><a href="#">Lorem.</a></li>
                    <li><a href="#">Quas.</a></li>
                    <li><a href="#">Volupt</a></li>
                    <li><a href="#">Esse.</a></li>
                </ul>
            </div>
            <div class="col">
                <h4 class="text-uppercase">Privacy Policy</h4>
                <ul class="list-unstyled">
                    <li><a href="#">Lorem.</a></li>
                    <li><a href="#">Quas.</a></li>
                    <li><a href="#">Volupt</a></li>
                    <li><a href="#">Esse.</a></li>
                </ul>
            </div>
        </div>

    </div>
    <img src="{{ Vite::asset('resources/img/footer_bg.jpg') }}" alt="">
</footer>

```

Welcome page

```html

@extends('layouts.app')

@section('content')


<div class="container">

    <h1 class="my-5">Welcome Page</h1>
    <div class="row row-cols-1 row-cols-md-3 g-4">
        <div class="col">
            <div class="card">
                <div class="card-body">
                    <p class="card-text">Lorem ipsum dolor sit amet consectetur, adipisicing elit. Saepe, delectus ad esse illum omnis earum eligendi sint a minus quasi, inventore nulla autem. Maxime voluptatem eligendi veniam voluptates. Soluta, sunt!</p>
                </div>
            </div>
        </div>
        <div class="col">
            <div class="card">
                <div class="card-body">
                    <p class="card-text">Lorem ipsum dolor sit amet consectetur, adipisicing elit. Saepe, delectus ad esse illum omnis earum eligendi sint a minus quasi, inventore nulla autem. Maxime voluptatem eligendi veniam voluptates. Soluta, sunt!</p>
                </div>
            </div>
        </div>
        <div class="col">
            <div class="card">
                <div class="card-body">
                    <p class="card-text">Lorem ipsum dolor sit amet consectetur, adipisicing elit. Saepe, delectus ad esse illum omnis earum eligendi sint a minus quasi, inventore nulla autem. Maxime voluptatem eligendi veniam voluptates. Soluta, sunt!</p>
                </div>
            </div>
        </div>
        <div class="col">
            <div class="card">
                <div class="card-body">
                    <p class="card-text">Lorem ipsum dolor sit amet consectetur, adipisicing elit. Saepe, delectus ad esse illum omnis earum eligendi sint a minus quasi, inventore nulla autem. Maxime voluptatem eligendi veniam voluptates. Soluta, sunt!</p>
                </div>
            </div>
        </div>
        <div class="col">
            <div class="card">
                <div class="card-body">
                    <p class="card-text">Lorem ipsum dolor sit amet consectetur, adipisicing elit. Saepe, delectus ad esse illum omnis earum eligendi sint a minus quasi, inventore nulla autem. Maxime voluptatem eligendi veniam voluptates. Soluta, sunt!</p>
                </div>
            </div>
        </div>
        <div class="col">
            <div class="card">
                <div class="card-body">
                    <p class="card-text">Lorem ipsum dolor sit amet consectetur, adipisicing elit. Saepe, delectus ad esse illum omnis earum eligendi sint a minus quasi, inventore nulla autem. Maxime voluptatem eligendi veniam voluptates. Soluta, sunt!</p>
                </div>
            </div>
        </div>
    </div>
</div>
@endsection


```

Products page

```html

@extends('layouts.app')
@section('page-title', 'Tutti i prodotti - La Molisana')
@section('content')
<div class="prodotti">
    <div class="container">
        <h2 class="my-4">Tutti i prodotti</h2>
        <div class="row row-cols-1 row-cols-sm-2 row-cols-md-3 row-cols-xl-4 g-4">
            @foreach ($products as $product)
            <div class="col">
                <div class="card border-0">
                    <img class="img-fluid card-img-top" src="{{ $product['src'] }}" alt="{{ $product['titolo'] }}">
                    <div class="card-body">
                        <h4 class="card-title">
                            {{ $product['titolo'] }}
                        </h4>
                    </div>
                </div>
            </div>

            @endforeach

        </div>

    </div>
</div>
@endsection
```

Blog view

```html

@extends('layouts.app')

@section('content')


<div class="container">

    <h2 class="my-4">Tutti gli articoli</h2>

    <div class="row row-cols-1 row-cols-sm-2 row-cols-md-3 row-cols-xl-4 g-4">

        @foreach($posts as $post)

        <div class="col">
            <div class="card border-0">
                <img class="card-img-top" src="{{$post['img']}}" alt="">
                <div class="card-body">
                    <h3 class="card-title">
                        {{$post['title']}}
                    </h3>
                    <p class="card-text">
                        {{$post['body']}}
                    </p>
                </div>

            </div>
        </div>

        @endforeach
    </div>

</div>
@endsection

```
