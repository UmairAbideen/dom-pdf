## 📄 Laravel 10 PDF Generation with DomPDF Example

This project demonstrates how to generate PDFs in Laravel 10 using the Barryvdh/DomPDF package. It allows you to create downloadable PDF documents from data stored in your database, such as books, reports, invoices, or any data-based document.

🛠️ Tech Stack

| Tool                         | Purpose                                |
|------------------------------|----------------------------------------|
| Laravel 10                   | PHP Framework                          |
| barryvdh/laravel-dompdf      | Package for generating PDF files       |
| Blade View                   | View to render the PDF content         |

## 📝 Step-by-Step Guide

1. Install DomPDF Package

   
To install the Barryvdh/DomPDF package in your Laravel 10 project, run the following command:
``` bash
composer require barryvdh/laravel-dompdf
```
This will install the package and add it to your composer.json file.


2. Publish the Configuration (Optional)

   
You can publish the package's configuration file (optional, but useful if you want to customize PDF settings):
``` bash
php artisan vendor:publish --provider="Barryvdh\DomPDF\ServiceProvider"
```
This will create a config/dompdf.php file, where you can customize DomPDF settings such as paper size, font, and margins.
 


3. Create Book Model & Migration

   
Generate the Book model and migration file:
``` bash
php artisan make:model Book -m
Define the necessary fields in the migration file for the Book model. For example: 

public function up()
{
    Schema::create('books', function (Blueprint $table) {
        $table->id();
        $table->string('name');
        $table->string('author');
        $table->date('release_date');
        $table->string('genre');
        $table->integer('pages');
        $table->string('publisher');
        $table->timestamps();
    });
}
```

4. Run Migration to Create Books Table

   
After creating the migration, run the following command to create the books table in your database:
``` bash
php artisan migrate
```

5. Create Controller for Handling PDF Generation


Generate a new controller for the Book model:
``` bash
php artisan make:controller BookController
In the BookController, add a method to handle the PDF generation:

use App\Models\Book;
use Barryvdh\DomPDF\Facade as PDF;

public function generatePDF()
{
    $books = Book::all(); // Fetch books from the database

    // Load the view and pass the data to the PDF
    $pdf = PDF::loadView('books.pdf', compact('books'));

    // Download the PDF file
    return $pdf->stream('books_list.pdf');
}
``` 

6. Create Blade View for PDF Layout


Create a Blade view resources/views/books/pdf.blade.php for rendering the books data into a table format:

``` bash
<!DOCTYPE html>
<html>
<head>
    <title>Books List</title>
    <style>
        table {
            width: 100%;
            border-collapse: collapse;
        }
        table, th, td {
            border: 1px solid black;
        }
        th, td {
            padding: 8px;
            text-align: left;
        }
    </style>
</head>
<body>
    <h1>Books List</h1>
    <table>
        <thead>
            <tr>
                <th>Name</th>
                <th>Author</th>
                <th>Release Date</th>
                <th>Genre</th>
                <th>Pages</th>
                <th>Publisher</th>
            </tr>
        </thead>
        <tbody>
            @foreach ($books as $book)
            <tr>
                <td>{{ $book->name }}</td>
                <td>{{ $book->author }}</td>
                <td>{{ \Carbon\Carbon::parse($book->release_date)->format('d M, Y') }}</td>
                <td>{{ $book->genre }}</td>
                <td>{{ $book->pages }}</td>
                <td>{{ $book->publisher }}</td>
            </tr>
            @endforeach
        </tbody>
    </table>
</body>
</html>
```

7. Set Up Route for PDF Generation


Define a route in routes/web.php to trigger the PDF generation:

``` bash
Route::get('/generate-pdf', [BookController::class, 'generatePDF'])->name('generate.pdf');
``` 

8. Test PDF Generation


Now, you can test the PDF generation by visiting the route in your browser:
http://your-app.test/generate-pdf
This will generate a PDF file containing the list of books.
