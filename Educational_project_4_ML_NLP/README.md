# Обучение модели классификации комментариев
## Machine Learning, NLP, Classification
**Задачи проекта:**  Определение токсичности комментариев.

## Описание проекта
Был проведен предварительный анализ данных, удалены данные не несущие полезной для задачи информации. Исследован дисбаланс классов целевого признака.\
Использована предобученная модель с [домена](https://huggingface.co/martin-ha/toxic-comment-model) для получения предсказаний.\
Использована предобученная модель Conversational BERT, English с домена DeepPavlov для векторизации, на полученных векторах обучены модели классического ML.\
Использованы методы, основанные на TF-IDF и CountVectorizer


## Использованные библиотеки и инструменты
Python, Pandas, Scikit-learn, NumPy, Nltk, re, PyTorch, Transformers, CatBoost, Gensim, imbalanced-learn, tqdm, GridSearchCV
## Итоги проекта
Лучший результат показала LGBMClassifier c CountVectorizer, f1 = 0.778.

![image](https://github.com/Vitaliy-Zaitsev/Educational_project_4_ML_NLP/assets/120369294/0b3040a7-feaf-4bec-a3f7-a28f8d73b5b3)
#### [Список всех учебных проектов](https://github.com/Vitaliy-Zaitsev/Educational_projects)