# Обучение модели классификации комментариев
## Machine Learning, NLP, Classification
**Задачи проекта:**  Определение токсичности комментариев.

## Описание проекта
Был проведен предварительный анализ данных, удалены данные не несущие полезной для задачи информации. Исследован дисбаланс классов целевого признака.\
Использована предобученная модель с [домена](https://huggingface.co/martin-ha/toxic-comment-model) для получения предсказаний.\
Использована предобученная модель Conversational BERT, English с домена DeepPavlov для векторизации, на полученных векторах обучены модели классического ML.\
Использованы методы, основанные на TF-IDF и CountVectorizer\
Посмотреть проект: [Обучение модели классификации комментариев](https://github.com/Vitaliy-Zaitsev/Educational_projects_DS/blob/main/Educational_project_4_ML_NLP/Проект%20МО%20для%20текстов%20(final%20version).ipynb)


## Использованные библиотеки и инструменты
Python, Pandas, Scikit-learn, NumPy, Nltk, re, PyTorch, Transformers, CatBoost, Gensim, imbalanced-learn, tqdm, GridSearchCV
## Итоги проекта
В выборке почти 160000 строк. Из них негативные только 10%.

**Обучение моделей с BERT**

Использовал предобученную модель с домена https://huggingface.co/martin-ha/toxic-comment-model \
Не создавал эмбердинги, а сразу получал предсказания модели. Очень быстрый результат, но метрика ниже заданного порога.\
f1 = 0.727

Использовал предобученную модель Conversational BERT, English с домена DeepPavlov для векторизации, на полученных векторах обучены модели классического ML. Для экономии времени было взято 200 объектов сохранив исходный дисбаланс класов целевого признака.  Лучший результат у LogisticRegression, f1 = 0.626. Но все же он намного ниже требуемого. Возможно с увеличением количества объектов увеличится и качество модели.

**Обучение моделей с TF-IDF и CountVectorizer**

Для предподготовки текста использовал библиотеку nltk.\
Используя GridSearchCV произвел перебор гиперпараметров и методов векторизации для двух моделей: LogisticRegression и LGBMClassifier

![image](https://github.com/Vitaliy-Zaitsev/Educational_projects_DS/assets/120369294/9811fdc1-ee53-471a-9970-e78af569ecb7)

Лучший результат показала LGBMClassifier c CountVectorizer, f1 = 0.778.

*Итоговая таблица содержит исследованные методы векторизации и значение метрики f1 на кросс-валидации для моделей LogisticRegression и LGBMClassifier*

![image](https://github.com/Vitaliy-Zaitsev/Educational_projects_DS/assets/120369294/a283a064-f254-4137-a824-a4f96cbd6433)

На тестовой выборке лучшая модель показала f1 = 0.787

#### [Список всех учебных проектов](https://github.com/Vitaliy-Zaitsev/Educational_projects_DS/blob/main/README.md)
