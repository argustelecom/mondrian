#1 Сборка JAR
Проект Mondrian является Maven проектом (см. pom.xml), поэтому для получения jar необходим установленный Maven.
Для сборки проекта необходимо использовать специфичный settings.xml файл.
Данный settings.xml файл можно найти в официальном репозитории: https://github.com/pentaho/maven-parent-poms/blob/master/maven-support-files/settings.xml
Для скачивания со страницы Github необходимо перейти в режим RAW и сохранить файл через пункт контекстного меню "Сохранить как".

Соответственно, чтобы выполнить сборку проекта необходимо выполнять Maven комманды с указанием этого settings.xml. Например:
mvn -s D:/settings.xml clean install

Собранный артефакт будет расположен здесь: .\mondrian\target\mondrian-<версия>.jar


#2 Деплой в artifactory:
1. Перейти в artifactory web ui: http://git:8081/artifactory
2. В меню слева выбрать пункт "Artifacts"
3. Раскрыть элемент "ext-release-local" (т.к. Mondrian - это сторонняя библиотека)
4. Выбрать каталог /pentaho/mondrian/
5. Нажать кнопку "Deploy" (c правой стороны, с иконкой стрелки)
6. В "Target Repository" выбрать "ext-release-local"
7. На вкладке "Single" добавить JAR файл собранного Mondrian
8. Убедиться, что указана верная версия, а НЕ SNAPSHOT. Например: 3.14-ARGUS
9. Нажать "Generate Default POM" чтобы Artifactory показал, какой pom он предлагает сгенерировать.

(!)Важно: artifactory генерирует файл с ошибкой, его нужно исправить!

При помощи Ctrl+A копируем содержимое и сохраняем его в отдельном файле с названием mondrian-<версия>. 
Например: mondrian-3.14-ARGUS.pom

Необходимо в этом файле блок "parent" заменить блоком "parent" из pom.xml файла проекта mondrian в нашем репозитории.
Например:
  <parent>
    <groupId>pentaho</groupId>
    <artifactId>pentaho-mondrian-parent-pom</artifactId>
    <version>3.14-SNAPSHOT</version>
  </parent>

После этого флаг "Generate Default POM" убираем и нажимаем кнопку "Deploy".

10. По аналогии с пунктами 5-7 деплоим созданный в пункте 9 "General POM".

Теперь собранный артефакт доступен в артефактори для проектов аргус.


#3 Добавить в Argus Appserver
1. Открыть в репозитории аппсервера pom файл: \workspace\grandparent\pom.xml
2. Изменить версию в property "version.mondrian".
3. Собрать проект с новой версией.
4. Убедиться, что при сборке Аргус получаем новый артефакт.
Узнать локальный репозиторий: mvn help:evaluate -Dexpression=settings.localRepository
Например, если локальный репозиторий D:\mvn\, то новый артефакт должен появиться здесь: D:\mvn\pentaho\mondrian\
5. Выполнить проверку базового функционала аппсервера, который связан с построением сводных отчётов


