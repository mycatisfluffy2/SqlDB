# SqlDB
Form with spl db using flask and python

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Welcome to pyEstate</title>
    
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet"
        integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous">
    <script src="https://cdn.jsdelivr.net/npm/tinymce@5.10.2/tinymce.min.js" referrerpolicy="origin"></script>
    <!-- <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-YvpcrYf0tY3lHB60NNkmXc5s9fDVZLESaAA55NDzOxhy9GkcIdslK1eN7N6jIeHz" crossorigin="anonymous"></script> -->
    <script>
        tinymce.init({
            selector: '.editor',
            menubar: false,
            plugins: 'advlist autolink lists link charmap print preview anchor textcolor',
            toolbar: 'undo redo | formatselect | bold italic backcolor | alignleft aligncenter alignright alignjustify | bullist numlist outdent indent | removeformat'
        });
    </script>
    <link rel="stylesheet" href="{{ url_for('static', filename='styles.css') }}">
<style>
    /* @media only screen and (max-width: 600px) {
    table{
        background-color: red;
    }
  } */
</style>
</head>
<body>
    <div class="main-contianer center">
        <div class="col">
            <div class="row">
                <div class="logo-box col-2"></div>
                <div class="col-5 buttons">
                    <div class="btn btn-primary j-button">One</div>
                    <div class="btn btn-primary j-button">Two</div>
                    <div class="btn btn-primary j-button">Three</div>
                    <div class="btn btn-primary j-button">Four</div>
                </div>
            </div>
            <div>
                <form action="/" method="post" class="flex-nowrap">
                    <p>Name</p>
                    <input name="name" required type="text">
                    <p>City</p>
                    <input name="city" required type="text">
                    <p>Country</p>
                    <input name="country" required type="text">
                    <button type="submit">Submit</button>
                </form>
                <table class="table table-striped">
                    <thead>
                        <tr>
                            <th scope="col">Name</th>
                            <th scope="col">City</th>
                            <th scope="col">Country</th>
                            <th>
                                <div class="btn btn-primary">Send</div>
                            </th>
                        </tr>
                    </thead>
                    <tbody>
                        {%for participant in data%}
                        <tr>
                            <td>{{participant[0]}}</td>
                            <td>{{participant[1]}}</td>
                            <td>{{participant[2]}}</td>
                        </tr>
                        {%endfor%}
                    </tbody>
                    
                </table>
            </div>
        </div>
    </div>
</body>

</html>

```
```css
body {
    font-family: Arial, sans-serif;
}

.logo-box {
    width: 200px;
    height: 100px;
    background-color: #ff0000; /* Red color */
    margin-bottom: auto;
}
.buttons{
    display: flex;
    justify-content: space-between;
}
.j-button{
    margin-top: 4rem;
    width: 8rem;
    height: fit-content;
}
.table {
    overflow-x:auto;
}
```
```python
from flask import Flask, render_template, request, redirect
import sqlite3

app = Flask(__name__)

@app.route('/',methods=['GET','POST'])
def home():
    connect = sqlite3.connect('database.db')
    connect.execute('''CREATE TABLE IF NOT EXISTS PARTICIPANTS (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                name TEXT NOT NULL,
                city TEXT NOT NULL,
                country TEXT NOT NULL
    );''')
    connect.close()

    if request.method == "POST":
        name = request.form.get('name')
        city = request.form.get('city')
        country = request.form.get('country')

        # Validate that all fields are provided
        if name and city and country:
            with sqlite3.connect("database.db") as users:
                cursor = users.cursor()
                cursor.execute("INSERT INTO PARTICIPANTS (name, city, country) VALUES (?, ?, ?)", (name, city, country))
                users.commit()
            return redirect('/')
        else:
            error = "All fields are required!"
            return render_template("index.html", error=error)
    else:
        connect = sqlite3.connect('database.db')
        cursor = connect.cursor()
        cursor.execute('SELECT * FROM PARTICIPANTS')
        data = cursor.fetchall()
        return render_template("index.html", data=data)

if __name__ == '__main__':
    app.run(debug=True)
```

