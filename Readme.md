# Построение модели для прогнозированя оттока клиентов банка

## Описание задачи 

Из «Бета-Банка» стали уходить клиенты. Каждый месяц. Немного, но заметно. Банковские маркетологи посчитали: сохранять текущих клиентов дешевле, чем привлекать новых.

Необходимо спрогнозировать, уйдёт клиент из банка в ближайшее время или нет. Нам предоставлены исторические данные о поведении клиентов и расторжении договоров с банком. 

Источник данных: [https://www.kaggle.com/barelydedicated/bank-customer-churn-modeling](https://www.kaggle.com/barelydedicated/bank-customer-churn-modeling)

## Используемые модели и достигнутые результаты

Построено несколько моделей с предельно большим значением *F1*-меры:
- Модель решающего дерева;
- Модель случайного леса;
- Модель логистической регрессии.

Перед обучением моделей, данные были предобратоны: удалены пропуски, обработаны явные и неявные дубликаты, проведен EDA анализ данных, преобразованы категориальные признаки с помощью метода One-Hot-Encoding, проведено масщтабирование данных, разбивка на выборки, так же применены методы для балансировки классов (метод `'weight=balanced'`, простой `'upsampling'`, `'Smote'`.

Дополнительно измерена метрика *AUC-ROC* и проведено её сравнение *F1*-мерой.

Результаты моделей на валидационной выборке приведены в сводной таблице.Результаты отсортированы по убыванию `'F1 меры'`.

![%D0%A1%D0%B2%D0%BE%D0%B4%D0%BD%D0%B0%D1%8F%20%D1%82%D0%B0%D0%B1%D0%BB%D0%B8%D1%86%D0%B0.jpg](attachment:%D0%A1%D0%B2%D0%BE%D0%B4%D0%BD%D0%B0%D1%8F%20%D1%82%D0%B0%D0%B1%D0%BB%D0%B8%D1%86%D0%B0.jpg)

Лучший результат показала модель случайного леса с простым апсемплингом. `'F1 мера'` составила **0.645**, `'ROC-AUC'` - **0.864** и `'Accuracy'` - **0.828**.

На тестовой выборке модель решающего дерева показала чуть худшие результаты:
- **F1** мера снизилась **до 0.599**;
- **Accuracy** снизилась **до 0.806**, что может говорить о переобученности модели.
- **ROC_AUC** снизилась до **0.851**.

В целом модель **ошибается в 19,4%** процентах случаев.

Модель так же проверена на адекватность. Важными признаками модель приняла **возраст** и **количество продуктов**, что согласуются с выводами, полученными на этапе *EDA*. Так же не менее важным признаком является **баланс клиента**, что не так очевидно при первичном анализе данных.  

## Обший вывод:

1. Так как метрика `точности` (**Accuracy**) зависима от баланса классов, значения метрик **F1-меры**, которая отражает сбалансированность точности и полноты предсказаний модели (метрик `recall` и `precision`), и **ROC-AUC**, характеризующей насколько модель правильно и полно определяет целевой класс (`1`) среди всех истиноположительных объектов при наименьшей ошибке I-го рода (ложноположительном прогнозе среди лояльных клиентов), являются более весомыми и значимыми в случае несбалансированности классов. 
2. Балансировка классов с помощью **апсемплинга** (простого и с использованием **Smote**) дала прирост в качестве прогнозирования моделей по метрике **F1** и позволила достичь хороших показателей на **моделях случайного леса** и **решающего дерева**. Подбор **порога** классификатора так же позволил увеличить показатели двух метрик из трех (**F1 меру** и **ROC-AUC**).
3. **Логистическая регрессия** показала *наихудший результат* во всех случаях. Возможно целевой признак имеет более сложную и нелинейную связь с остальными признаками.
4. После апсемплинга видно что графики "растягиваются" и оптимальные пороги смещаются вправо.
5. Наилучшими признаками для прогнозирвоания оттока можно считать `['Age']`, `['Num of products']`, `['Gender']`, `['Geography']`, `['CreditScore']`, что так же хорошо видно на графиках на этапе *EDA*.
6. Параметр **random_state**, который задает псевдослучайность в каждом случае так же влияет на значения метрик (в пределах определенной погрешности), что в свою очередь может сказаться на итогах выбора лучшей модели. Например при подборе нужного параметра **random_state** так же можно было бы достичь значения метрики **F1** в **0.59** модели решающего дерева на тестовой выборке.

Для достижения ещё более лучших показателей предсказания оттока рекомендуется:
- выяснить причину пропусков по колонке `['Tenure']` и устранить;
- подобрать нелинейную модель, которая сможет улучшить качество и предсказания целевого признака на несбалансированных данных. 