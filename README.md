# VS Code .env test

The Python extension in VS Code has a default setting that loads your `.env` file in the workspace folder 🤯 I had no idea. Read more about it [here](https://code.visualstudio.com/docs/python/environments#_environment-variable-definitions-file).

This project is to test that with a Django project.

---

## Setup
1. Create virtual environment with `python3 -m venv venv`
2. Activate the virtual environment with `. venv/bin/activate`
3. Install requirements with `pip install -r requirements.txt`
4. Create a `.env` with `cp .env.example .env`

## Try it out

Run any of the following runserver commands in both your terminal and VS Code, separately. Look at the standard output for each of them.

|server|command|
|---|---|
|Django dev server|`python manage.py runserver --noreload`|
|Gunicorn|`gunicorn --reload --bind 0.0.0.0:8000 coffee.wsgi:application`|


### Terminal stdout
```
Django settings loading... ⏳
🎨 None
💚💚 None likes her coffee None
--- Before environ reads and sets env --- 📭
--- After environ reads and sets env --- 📬
🎨2️⃣  magenta
💚💚2️⃣ sangeeta likes her coffee black
```

Kill the server and head over to VS Code.

### VS Code stdout
On the sidebar, click `Run and Debug` and select one of the configurations.
```
Django settings loading... ⏳
🎨 magenta
💚💚 sangeeta likes her coffee black
--- Before environ reads and sets env --- 📭
--- After environ reads and sets env --- 📬
🎨2️⃣  magenta
💚💚2️⃣ sangeeta likes her coffee black
```
Notice how the `magenta`, `sangeeta`, and `black` values are printed out at the top, which means our `.env` was loaded already.

## Notes & TIL
1. This project uses the[ `django-environ` package](https://django-environ.readthedocs.io/en/latest/) to load values from your `.env` and map it to Django settings with correct types. On this [line](coffee/settings.py#L31), it actually reads from the `.env` and sets those values into the `os.environ`...basically doing what the VS Code Python extension does by default. The reason I used it was to replicate some work projects and rule this out as not being the reason for loading `.env` by default.

2. One thing I learned was Django's reason for loading settings twice when running the dev server. As you might have noticed, there's an extra flag in the Django runserver command that disables this. Running `python manage.py runserver` will start the dev server in reload mode which causes Django to load the app on separate threads ([ref](https://stackoverflow.com/questions/11149730/django-settings-py-seems-to-load-multiple-times)). You can bypass this by running with the `--noreload` flag as shown above.

    To see the separate threads, you can run `ps -M` ([ref](https://ss64.com/osx/ps.html)) which will show all threads per process and you should see 2 when you run the dev server with reload enabled.