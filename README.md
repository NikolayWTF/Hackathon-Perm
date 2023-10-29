#  Hackathon-Perm
## Весь код можно посмотреть в Хакатон_Пермь_стоп_кодинг.ipynb
##  Постановка задачи
На основе набора последовательных кадров с
мультиспектральной камеры подсчитать
количество уникальных объектов каждой
категории ТБО: стекло, дерево, метал, пластик.
В результате работы программы должны быть
получены 4 числа - количество уникальных
объектов каждой из категорий ТБО по каждому
семплу (семпл - набор фреймов относящихся к
одному видео).

##  Метрика
![image](https://github.com/NikolayWTF/Hackathon-Perm/assets/55809234/6cc76cc3-d8e6-4c2a-a98a-d9e006caf6ab)
## Вычисление M1
Мы обучали модель на 2 видео, а последнее использовали в качестве теста, чтобы была возможность трезво оценивать результаты.

-  Разметили датасет
  ![image](https://github.com/NikolayWTF/Hackathon-Perm/assets/55809234/f79c5db1-16f5-4919-af95-2bbef3080edd)

-  Обучили несколько классификаторов для 4 классов.
-  -  YOLOv8n 150 эпох
     ###  **M1 = 1.16**
     ![image](https://github.com/NikolayWTF/Hackathon-Perm/assets/55809234/350afed7-212a-436d-8916-562ae2a8d144)
-  -  YOLOv8s 180 эпох
     ### **M1 = 1.13**
   ![image](https://github.com/NikolayWTF/Hackathon-Perm/assets/55809234/7e7852df-159e-4b3f-acbf-86fc5d4dfe57)
-  -  YOLOv8m 100 эпох
     ###  **M1 = 0.95**
   ![image](https://github.com/NikolayWTF/Hackathon-Perm/assets/55809234/d76abb52-54bb-4455-b5ee-2725640b52ce)
-  -  4 отдельных классификатора
     ###  **M1 = 1.23**
   ![image](https://github.com/NikolayWTF/Hackathon-Perm/assets/55809234/45c598f0-5be0-453c-8fbf-2c95e7064355)
   ![image](https://github.com/NikolayWTF/Hackathon-Perm/assets/55809234/5fbb0734-25bb-4c0e-a39a-cc1801ac7cb6)
   ![image](https://github.com/NikolayWTF/Hackathon-Perm/assets/55809234/f6bc0a53-071c-4366-ab15-35e254c3b503)
   ![image](https://github.com/NikolayWTF/Hackathon-Perm/assets/55809234/cdbc6ced-05dd-406c-88fa-6f9812183fcd)




## Вычисление М2
1.  Ширина фотографии = 640 пикселей.
2.   Мы провели несколько вертикальных черт на уровнях (100, 200, 300, 400, 500, 600).
3.   ``Количество дерева на видео = SUM( max(Q_i - Q_(i-1), 0) )``, где ``Q_i`` - количество дерева попадающего на вертикальную черту на i фрейме.
4.  Алогично для остальных ТБО.
5.  Вычисляем количество ТБО для каждой черты, после чего берём среднее арифметическое.
6.  | Ресурс |  Вычисленное | Истинное |
    | ------ | ------------ | -------- |
    | Дерево |      128     | 67       |
    | Стекло | 126          | 59       |
    | Пластик| 185          | 57       |
    | Металл |61            | 61       |
## Вычисление дублирующего коэффициента
Можно заметить, что для металла истинное и вычисленное значения - совпадают. В то время как для других ТБО - значительно отличаются. Дело в том, что некоторые объекты не распознаются на всех фреймах. При проходе через черту может возникнуть ситуация, что на 1 из фреймов объект не был распознан. В таком случае его засчитают повторно. Как видно из датасета металл - самый маленький по площади объект, поэтому он подвержен дублированию меньше, чем другие ТБО.

``k_w = true_wood / calculated_wood`` ``k_w`` - дублирующий коэффициент для дерева. Это среднее количество раз, сколько дерево задублируется при переходе, через линию.

При домножении вычисленного значения ТБО на соответствующие дублирующие коэфициенты, метрика М2 значительно улучшается.

**P.S. Для разных моделей, дублирующий коэффициент разный**
##  Визуализация
Сделали небольшой интерфейс для просмотра количества ТБО на каждом фрейме.
![image](https://github.com/NikolayWTF/Hackathon-Perm/assets/55809234/db0ae31f-108a-4151-8865-105775b2e9c4)
![image](https://github.com/NikolayWTF/Hackathon-Perm/assets/55809234/917c5867-2918-4745-94bc-b58575947cd3)




 

