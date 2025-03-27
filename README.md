### Лекция 4: Создание регистрации для пользователей

#### Шаг 1: Создание модели для регистрации
Мы будем использовать модель `User`, которую создали ранее, но добавим в неё правила валидации и метод для регистрации.

1. Откройте файл `models/User.php`.
2. Добавьте правила валидации в метод `rules()`: 
   ```php
   public function rules()
   {
       return [
            [['username', 'password', 'full_name', 'phone', 'email'], 'required', 'message' => 'Обязательное поле'],
            ['username', 'unique', 'message' => 'Этот логин уже занят.'],
            ['email', 'email', 'message' => 'Введите корректный email'],
            ['phone', 'match', 'pattern' => '/^\+7\(\d{3}\)-\d{3}-\d{2}-\d{2}$/', 'message' => 'Телефон должен быть в формате +7(XXX)-XXX-XX-XX.'],
            ['password', 'string', 'tooShort' => 'Пароль должен содержать минимум 6 символов', 'min' => 6],
            ['full_name', 'match', 'pattern' => '/^[а-яА-ЯёЁ\s]+$/u', 'message' => 'ФИО должно содержать только кириллицу и пробелы.'],
        ];
   }
   ```
   **ВАЖНО!** Функция rules отвечает за валидацию полей формы при нажатии кнопки `Зарегистрироваться` пользователем.
   
3. Добавьте метод `register()` для сохранения пользователя:
   ```php
   public function register()
   {
       if ($this->validate()) {
           $this->password = Yii::$app->security->generatePasswordHash($this->password); // Хэшируем пароль
           return $this->save(false); // Сохраняем пользователя без повторной валидации
       }
       return false;
   }
   ```

   
---


#### Шаг 2: Создание действия для регистрации в контроллере
Теперь изменим действие `actionCreate` в контроллере `UserController`.

1. Откройте файл `controllers/UserController.php`.
2. Измените код функции `actionCreate`:
   ```php
   public function actionCreate()
   {
       $model = new User();

        if ($model->load(Yii::$app->request->post())) {
            if ($model->register()) {
                Yii::$app->session->setFlash('success', 'Регистрация прошла успешно!');
               return $this->redirect(['site/index']); // Перенаправляем на главную страницу
            } else {
                Yii::$app->session->setFlash('error', 'Ошибка при регистрации.');
            }
        }

        return $this->render('create', [
            'model' => $model,
        ]);
   }
   ```


---

#### Шаг 3: Создание представления для регистрации
Мы уже сгенерировали файл `views/user/_form.php` ранее. Давайте его доработаем.

1. Откройте файл `views/user/_form.php`.
2. Добавьте метод label() к каждому полю, где нужен текст на русском языке:
   ```php
    <?php $form = ActiveForm::begin(); ?>

    <?= $form->field($model, 'username')->textInput(['maxlength' => true])->label('Логин') ?>

    <?= $form->field($model, 'password')->passwordInput(['maxlength' => true])->label('Пароль') ?>

    <?= $form->field($model, 'full_name')->textInput(['maxlength' => true])->label('ФИО') ?>

    <?= $form->field($model, 'phone')->textInput(['maxlength' => true])->label('Номер телефона') ?>

    <?= $form->field($model, 'email')->textInput(['maxlength' => true]) ?>

    <div class="form-group">
        <?= Html::submitButton('Регистрация', ['class' => 'btn btn-success']) ?>
    </div>

    <?php ActiveForm::end(); ?>
   ```

![image](https://github.com/user-attachments/assets/c66f0236-12a3-4bf3-8dcd-b897e0f713c1)


---


#### Шаг 4: Изменим название формы и страницы для регистрации

1. Откройте файл `views/user/create.php`.
2. Изменим код, чтобы заголовки на странице были русскими:

    ```php
      use yii\helpers\Html;
      
      /** @var yii\web\View $this */
      /** @var app\models\User $model */
      
      $this->title = 'Регистрация';
      
      ?>
      <div class="user-create">
      
          <h1><?= Html::encode($this->title) ?></h1>
      
          <?= $this->render('_form', [
              'model' => $model,
          ]) ?>
      
      </div>
    ```

![image](https://github.com/user-attachments/assets/228ffe21-d91d-48c3-9d84-562de393739a)



---

#### Шаг 5: Проверка регистрации
1. Откройте браузер и перейдите по адресу `http://cleaning-portal/web/user/create`.
2. Заполните форму регистрации и нажмите **Регистрация**.
3. Если данные корректны, вы увидите сообщение об успешной регистрации и будете перенаправлены на главную страницу.

![image](https://github.com/user-attachments/assets/fea1e815-4dbf-4d20-bf25-1eaf101f167a)

---

### Итог
Теперь у вас есть функционал регистрации пользователей:
- Модель `User` с валидацией и методом регистрации.
- Действие `actionCreate` в контроллере `UserController`.
- Представление `register.php` для отображения формы регистрации.

В следующей лекции мы реализуем авторизацию пользователей.


### [Следующая лекция](https://github.com/petrocollege-web/5.-Login)
### [Предыдущая лекция](https://github.com/petrocollege-web/3.-CRUD)
