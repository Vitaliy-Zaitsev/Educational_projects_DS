# Прогнозирование оттока клиента Банка
## Machine Learning, Classification
## Задачи проекта
На основании исторических данных о поведении клиентов и расторжении договоров с банком нужно спрогнозировать, уйдёт клиент из банка в ближайшее время или нет.

## Описание проекта
Был проведен предварительный анализ данных, удалены данные не несущие полезной для задачи информации. Обработаны категориальные признаки. Проведена стандартизация численных признаков.\
Проведен выбор модели МО, подбор гиперпараметров, подбор методов борьбы с дисбалансом классов.\
Посмотреть проект: [Прогнозирование оттока клиента Банка](https://github.com/Vitaliy-Zaitsev/Educational_projects_DS/blob/main/Educational_project_3_ML_Classification/Прогнозирование%20оттока%20клиента%20Банка.ipynb)
### 1 метод:
использование гиперпараметра class_weight='balanced'
### 2 метод:
Увеличиваем количество объекты наименьшего класса обучающей выборки. Так как я изначально разделил датасет только на обучающую и тестовую выборки, то для оценки моделей я использую кросс-валидацию.\
         Для того, чтобы избежать утечки данных было написано две функции: увеличивающая положительные объекты обучающей выборки в заданное количество раз:
         
 ```python
         def upsample(features, target, repeat):
              features_zeros = features[target == 0]
              features_ones = features[target == 1]
              target_zeros = target[target == 0]
              target_ones = target[target == 1]
              features_upsampled = pd.concat([features_zeros] + [features_ones] * repeat)
              target_upsampled = pd.concat([target_zeros] + [target_ones] * repeat)
              features_upsampled, target_upsampled = shuffle(
              features_upsampled, target_upsampled, random_state=12345)
              return features_upsampled, target_upsampled
   ```
         
        
 и функция, которая разбивает данные на обучающий и \
 тренировочный фолды, а затем на каждом фолде апсемплит наименьший класс в цикле на значения от 1 до 4, обучает модель и вычисляет метрики на каждои итерации цикла. Возвращает словарь с метриками:
```python 
    def score_model(model, params):
    
         cv = KFold(n_splits=5, shuffle=False)
         result = {}
    
         for repeat in np.arange(1, 5):
              f1_list = []
              auc_roc_list =[]
              for train_fold_index, val_fold_index in cv.split(features_train, target_train):
                  features_train_fold = features_train.iloc[train_fold_index]
                  target_train_fold = target_train.iloc[train_fold_index]
                  features_val_fold = features_train.iloc[val_fold_index]
                  target_val_fold = target_train.iloc[val_fold_index]
            
                  features_train_fold_upsample, target_train_fold_upsample = upsample(features_train_fold,
                                                                                target_train_fold,
                                                                                repeat)
                  model_obj = model(**params).fit(features_train_fold_upsample, target_train_fold_upsample)
        
                  f1 = f1_score(target_val_fold, model_obj.predict(features_val_fold))
                  f1_list.append(f1)
        
                  auc_roc = roc_auc_score(target_val_fold,  model_obj.predict_proba(features_val_fold)[:, 1])
                  auc_roc_list.append(auc_roc)
            
                  result.update({repeat:{'f1':f1_list, 'auc_roc': auc_roc_list}})
        
             return result
 ```
 Это решение позволило нам увеличить обучающую выборку не перед делением на фолды, что привело бы к утечке, а непосредственно после каждого деления.\
         Этот подход можно реализовать проще, использовав make_pipeline и SMOTE напимер из библиотеки imblearn. 


## Использованные библиотеки и инструменты
Python, Pandas, Scikit-learn, NumPy, GridSearchCV

## Итоги проекта
В проекте было исследовано 3 модели по двум метрикам: F1-мера и AUC-ROC.


![image](https://github.com/Vitaliy-Zaitsev/Educational_project_3_ML_Classification/assets/120369294/448f0278-5569-4388-9054-7d2934a5bf3f)


Метод взвешивания классов на модели случайный лес дал максимальные метрики.

#### [Список всех учебных проектов](https://github.com/Vitaliy-Zaitsev/Educational_projects_DS/blob/main/README.md)
