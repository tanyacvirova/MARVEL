# MARVEL
social network analysis for MARVEL heroes graph

## Описание задачи и её актуальности
В своей работе я буду анализировать и визуализировать связи между персонажами фильмов и комиксов Marvel. Вершинами графов будут персонажи фильмов, например Tony Stark / Iron Man или Bruce Banner / The Hulk. Ребрами — встречи в фильмах, ребра будут иметь вес: сколько раз пара персонажей встретилась в фильмах киновселенной.

На эту тему опубликовано уже множество работ. Приведу несколько примеров. В этой работе в трех частях ([часть 1](https://tbgraph.wordpress.com/2017/06/10/neo4j-marvel-social-graph/), [часть 2](https://tbgraph.wordpress.com/2017/11/23/neo4j-marvel-social-graph-algorithms-centralities/), [часть 3](https://tbgraph.wordpress.com/2017/11/17/neo4j-marvel-social-graph-algorithms-community-detection/)) анализируют связи, центральности и сообщества. В [другой работе](https://blog.dataiku.com/2015/05/19/marvel-social-graph-analysis) исследуют только сообщества, но применяют другую методологию для отбора узлов графа для визуализации.

План работы:
1. Собрать киновселенную Marvel со [специальной страницы Кинопоиска](https://www.kinopoisk.ru/lists/editorial/marvel_universe/?quick_filters=films&tab=all), используя подстраницу /cast для каждого фильма, на которой перечислены основные персонажи.
2. Сравнить сообщества киновселенной со знаменитым датасетом по комиксам Marvel (в примерах выше для этого использовали Louvain method). Оценить разницу в кластерах — это позволит ответить на вопрос, насколько фильмы отличаются от комиксов.

Чем будет отличаться от уже опубликованных работ:
1. Все датасеты в работах, перечисленных выше, старше 2015 года, с того года у Marvel вышло 8 фильмов (из 19), понятно, что там они не учитывались
2. Буду исследовать связи между персонажами не в комиксах, а в фильмах. Смогу сравнить полученный результат с уже созданными графами по комиксам.


## Сбор данных
Для создания графа персонажей киновселенной Marvel я буду использовать Кинопоиск, а именно подстраницу /cast для каждого фильма, где перечислены все актеры и роли, которые они играли в этом фильме. Меня будут интересовать только роли.

Для создания списка фильмов Marvel я использовала [подборку Кинопоиска](https://www.kinopoisk.ru/lists/editorial/marvel_universe/?quick_filters=films&tab=all) и [страницу на Википедии](https://ru.wikipedia.org/wiki/%D0%9A%D0%B8%D0%BD%D0%B5%D0%BC%D0%B0%D1%82%D0%BE%D0%B3%D1%80%D0%B0%D1%84%D0%B8%D1%87%D0%B5%D1%81%D0%BA%D0%B0%D1%8F_%D0%B2%D1%81%D0%B5%D0%BB%D0%B5%D0%BD%D0%BD%D0%B0%D1%8F_Marvel). Все фильмы собрала в словарь, где ключи — это названия фильмов, значения — список, состоящий из двух элементов: ссылки на подстраницу /cast и число актеров, задействованных в фильме. Это число нам пригодится при очистке данных.

Ссылка на код: https://colab.research.google.com/drive/1IqOsUdNclg-2QUO1ceIF919qkMbwmhb5?usp=sharing


## Описательная статистика, визуализация и исследование данных
В этом разделе я буду использовать файл с ролями, который подготовила на прошлом шаге, но уже вручную очищенный от второстепенных героев. Задача этого раздела: убрать дубликаты, разночтения и лишние знаки. Подготовить словарь для визуализации сети в NetworkX.

Для создания финального файла героев для графа я воспользовалась функцией Counter, чтобы посчитать совместную встречаемость героев в фильмах и использовать в дальнейшем этот показатель как вес ребра.

В этой части работы я рассчитала коэффициенты центральности и построила соответствующие графики распределения. 

Ссылка на код: https://colab.research.google.com/drive/1yNwfiucLjr9duGnn_OYJChc5IomY6RQd?usp=sharing 


## Сравнение сообществ в комиксах и фильмах Marvel
Эта часть работы посвящена сравнению двух графов: графа персонажей фильмов и графа персонажей комиксов. 

Для этого: 
1. Сравнила структуры графов, чтобы посмотреть, насколько они похожи. 
2. Извлекла из графов только сильные связи (для фильмов: герои встречались больше, чем в двух фильмах, для комиксов: герои встречались больше, чем в 150 комиксах вместе). Снова сравнила структуры графов и проверила, выполняется ли для них степенной закон. 
3. Построила кластеры для полных графов с помощью алгоритмов Agglomerative clusters, DBSCAN clusters и Node2Vec. 

Ссылка на код: https://colab.research.google.com/drive/1q2Mj_d70BKB-nRDtUXP27CBovMoLnoUb?usp=sharing 


## Выводы, заключение
1. Граф комиксов более плотный и связный, чем граф фильмов. Это может говорить о том, что в комиксах больше отсылок к другим героям вселенной, чем в фильмах. Большинство фильмов посвящены одному или нескольким героям. Фильмов, показывающих связь между всеми героями вселенной, единицы.

2. Структуры подграфов с сильными связями похожи на структуры полных графов. Подграф фильмов состоит по сути из двух кластеров (это видно на графике с визуализацией degree), а подграф комиксов в двух местах распадается на несвязанные с остальным графом пары.

3. График Adjacency Matrix для фильмов показывает, что есть группа центральных персонажей, которые сильно связаны между собой, и слабо связанных узлов на периферии. На графике для комиксов похожая ситуация, но связи внутри центрального клубка не такие сильные, как у персонажей фильмов, а между периферийными узлами связей практически нет.

4. Для графа фильмов лучше всего сработал алгоритм DBSCAN clusters с параметрами eps=2.0, min_samples=15. В нем четко выделены Мстители (зеленая группа в центре), второстепенные персонажи в фильмах про Мстителей (голубые узлы вокруг центрального зеленого кластера) и второстепенные персонажи отдельных фильмов (разноцветные группы на периферии графа). 

5. Для графа комиксов оба алгоритма сработали плохо. При большом количестве графов, узлы разных цветов перемешиваются друг с другом. Единственный график по комиксам, для которого мне удалось найти объяснение — это граф DBSCAN clusters с параметрами eps=0.9, min_samples=9, в котором получилось 5 кластеров: герои историй про Человека-паука (оранжевый), герои историй про Тора (фиолетовый), герои историй про Людей Х (коричневый), а также центральный кластер (зеленый) c героями историй про Железного человека.
