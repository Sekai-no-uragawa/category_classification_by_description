# _Предсказание категорий на основе названий и параметров товаров._

## _Описание_
В маркетплейс каждый день поступает множество новых товаров и каждый из них необходимо отнести в определенную категорию в нашем дереве категорий. На это тратится много сил и времени, поэтому мы хотим научиться предсказывать категорию на основе названий и параметров товаров.

## _Формат входных данных_
Данные доступны по ссылке: https://drive.google.com/drive/folders/11IWb5CN8ejGWJrnyoY2Gwzt9IN-UazD_?usp=sharing
- *categories_tree.csv* - файл с деревом категорий на маркетплейсе. У каждой категории есть id, заголовок и parent_id, по которому можно восстановить полный путь категории.
- *train.parquet* - файл с товарами на маркетплейсе. 

## _Метрики_
Важен путь к листовой категории. Для неверно предсказанных листовых категорий мы хотим добавлять скор, если были угаданы предшествующие родительские категории на разных уровнях.

Для честной оценки многоуровневой классификации используется видоизмененная метрика F1, взвешенная на размер выборки класса

### _Общий процесс выполнения_
Для предобработки использовал токенизацию из nltk.tokenize, регулярки, стоп слова и лемматизацию с помощью pymorphy2.
Для генерации эмбедингов использовалась Word2Vec из gensim.models
Модель, применяемая в работе - tensorflow.keras со слоями:
- Embedding (веса заданы матрицей, где вектор в i-й строке является вектором i-го слова в словаре модели word2vec)
- Dropout слой
- 2 слоя LSTM
- Выходной Dense с 1231 выходами (по кол-ву классов) с активацией softmax

Обучение сети на GPU, val_accuracy на последней эпохе: 0.8279
Взвешенный F1: 0.867309
hF1: 0.93033

Модель, на которой выполнялось итоговое предсказание, лежит в папке models с именем "lstm_with_w2v.hdf5"
