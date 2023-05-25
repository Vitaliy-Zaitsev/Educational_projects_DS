# Прогнозирование количества заказов такси на следующий час
## Machine Learning, Time Series, Regression
**Задачи проекта:**  На основании исторических данных о заказах такси в аэропортах нужно разработать систему предсказания объема заказа.

## Описание проекта
Был проведен предварительный анализ данных, данные приведины к нужному формату.\
Был проведен анализ тренда и сезонности.\
Написана функция для создания новых признаков (отстающие значения и скользящая средняя) и разбивки полученного датасета на обучающую и тренировочную выборки.

```python
    def make_features(data, max_lag, rolling_mean_size):
        data_f = data.copy()
        data_f['dayofweek'] = data_f.index.dayofweek
        data_f['hour'] = data_f.index.hour
        
    
        for lag in range(1, max_lag + 1):
            data_f['lag_{}'.format(lag)] = data_f['num_orders'].shift(lag)
        data_f['rolling_mean'] = data_f['num_orders'].shift().rolling(rolling_mean_size).mean()
    
        train, test = train_test_split(data_f, shuffle=False, test_size=0.1)
        train = train.dropna()
    
        X_train = train.drop('num_orders', axis=1)
        y_train = train.num_orders
        X_test = test.drop('num_orders', axis=1)
        y_test = test.num_orders
    
        return X_train, y_train, X_test, y_test
```        
Обучено несколько моделей классического ML, для каждой модели индивидуально подбирались входные значения для функции создания новых признаков и гиперпараметры.\
В результате каждая модель обучалась на том наборе данных, на котором она выдавала максимальные значения метрик. 

```python    
    #создаем словарь, содержащий исследуемые модели, их параметры и интервалы гиперпараметров для перебора в GridSearchCV
    
    model_param = [{'model_name': LinearRegression(), 'param_grid':{}},                                     
               {'model_name': lgb.LGBMRegressor(metric='root_mean_squared_error', random_state=12345),      
                'param_grid': {'n_estimators': range(50, 150, 50)}},
               {'model_name': CatBoostRegressor(loss_function="RMSE", verbose=False),
                'param_grid': {'n_estimators': range(50, 150, 50)}}
              ]

    #перебираем в первом цикле все указаные в словаре модели
    for i in range(len(model_param)):  
        
        print('Подбираем гиперпараметры и обучаем', model_param[i]['model_name'])
        
        #определяем стартовый максимальный порог для метрики
        max_RMSE = 48 
        
        #первый вложенный цикл перебирает значения параметра max_lag передающегося
        #в функция make_features и определяющего колмчество признаков lag features
        for max_lag in range(1, 200, 10):                                   
            #второй вложенный цикл перебирает значения параметра rolling_mean_size,
            #передающегося в функция make_features и определяющего значение для скользящего среднего
            for rolling_mean_size in range(24, 120, 24): 
            
                #получаем train и test вызовом функции make_features.
                #параметрами для функции задаем значения max_lag и rolling_mean_size, актуальные для данной итерации циклов
                X_train, y_train, X_test, y_test = make_features(data, max_lag, rolling_mean_size) 
                                                                                                   
                # т.к. наши данные представлены в виде непрерывного временного ряда, параметр cv для GridSearchCV
                # зададим с помощью функции TimeSeriesSplit(), количество фолдов укажем равное 5
                cv=TimeSeriesSplit(n_splits=5) 
                                               
                #создаем объект GridSearchCV, модель и гиперпараметры для перебора берем из словаря model_param
                ln_reg = GridSearchCV(model_param[i]['model_name'],       
                                      model_param[i]['param_grid'],
                                      n_jobs=-1,
                                      verbose=200,
                                      cv=cv,
                                      scoring='neg_root_mean_squared_error' 
                                     )
                ln_reg.fit(X_train, y_train)
            
                #Лучшие значения метрики, max_lag, rolling_mean_size и гиперпараметры модели сохраняем в словарь
                if -ln_reg.best_score_ <= max_RMSE:                                     
                    model_param[i]['best_params'] = ln_reg.best_params_
                    model_param[i]['best_score'] = -ln_reg.best_score_
                    model_param[i]['max_lag'] = max_lag
                    model_param[i]['rolling_mean_size'] = rolling_mean_size
                    model_param[i]['model'] = ln_reg
                    max_RMSE = -ln_reg.best_score_
                    
```

Обученные модели, оптимальные гиперпараметры к ним, оптимальные значения количества lag features и скользящего среднего для каждой модели сохранены в итоговую таблицу.\
Проведено тестирование лучшей модели.\
Построены графики сравнения значений целевого признака и предсказаний в срезе двух недель и 48 часов.\
Посмотреть проект: [Прогнозирование количества заказов такси на следующий час](https://github.com/Vitaliy-Zaitsev/Educational_projects_DS/blob/main/Educational_project_5_ML_TimeSeries/Проект%20Временные%20ряды(финальная%20версия).ipynb)

## Использованные библиотеки и инструменты
Python, Pandas, Scikit-learn, NumPy, Nltk, re, PyTorch, Transformers, CatBoost, Gensim, imbalanced-learn, tqdm, GridSearchCV
## Итоги проекта
Лучшая модель LinearRegressor(), max_lag = 171, rolling_mean_size = 48, RMSE на тесте 34,7


![image](https://github.com/Vitaliy-Zaitsev/Educational_project_5_ML_TimeSeries/assets/120369294/5960d535-61c3-4ef6-bbea-c98589d723d3)


#### [Список всех учебных проектов](https://github.com/Vitaliy-Zaitsev/Educational_projects_DS/blob/main/README.md)
