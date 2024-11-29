# Домашнее задание к занятию «Система сборки Maven»
## Цели задания
1. Научиться создавать maven-проекты.
2. Научиться писать юнит-тесты с помощью JUnit 5.
3. Научиться использовать maven-плагины
------
## Задание 1 

Нашей целью будет переделать проект с приложением про бонус с покупки на Maven и его протестировать.

**Шаг 1.** Создайте проект на базе Maven.

**Шаг 2.** Добавьте в проект JUnit Jupiter & Surefire Plugin

**Шаг 3.** Создайте сервисный класс со следующим исходным кодом:
```java
public class BonusService {
  public long calculate(long amount, boolean registered) {
    int percent = registered ? 3 : 1;
    long bonus = amount * percent / 100;
    long limit = 500;
    if (bonus > limit) {
      bonus = limit;
    }
    return bonus;
  }
}
```
**Шаг 4.** Создайте тестовый класс со следующим исходным кодом:
```java
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.Assertions;

public class BonusServiceTest {

  @Test
  void shouldCalculateForRegisteredAndUnderLimit() {
    BonusService service = new BonusService();

    // подготавливаем данные:
    long amount = 1000;
    boolean registered = true;
    long expected = 30;

    // вызываем целевой метод:
    long actual = service.calculate(amount, registered);

    // производим проверку (сравниваем ожидаемый и фактический):
    Assertions.assertEquals(expected, actual);
  }

  @Test
  void shouldCalculateForRegisteredAndOverLimit() {
    BonusService service = new BonusService();

    // подготавливаем данные:
    long amount = 1_000_000;
    boolean registered = true;
    long expected = 500;

    // вызываем целевой метод:
    long actual = service.calculate(amount, registered);

    // производим проверку (сравниваем ожидаемый и фактический):
    Assertions.assertEquals(expected, actual);
  }
}
```
**Шаг 5.** Запустите тесты через `mvn clean test`, убедитесь, что они запускаются и проходят.

**Шаг 6.** Проведите поверхностный тест-дизайн сервисного класса, допишите как минимум два недостающих и прямо напрашивающихся теста.

**Шаг 7.** Убедитесь, что тесты запускаются и проходят.

Итого: отправьте на проверку ссылку на репозиторий GitHub с вашим проектом.
------
## Задание 2 
:warning: Эта задача выполняется на основе первой задачи в том же репозитории.

Одна из частых ошибок начинающих программировать на Java — нарушение правил форматирования кода, в первую очередь в именованиях.
Давайте подключим плагин, который бы автоматически проверял наличие в вашем коде неправильно названных локальных переменных и обрушал бы сборку, если находил такие.

Создадим подобный дефект. Перейдите в класс `BonusService` и переименуйте переменную в нём с `percent` на `Percent`. Убедитесь, что код компилится, тесты проходят.

Добавьте в `pom.xml` в плагины следующую настройку:
``` xml             
               <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-checkstyle-plugin</artifactId>
                <version>3.2.1</version>
                <configuration>
                    <checkstyleRules>
                        <module name="Checker">
                            <module name="TreeWalker">
                                <module name="LocalVariableName"/>
                            </module>
                        </module>
                    </checkstyleRules>
                </configuration>
                <executions>
                    <execution>
                        <id>validate</id>
                        <phase>validate</phase>
                        <goals>
                            <goal>check</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
```

Это подключит `maven-checkstyle-plugin` к вашему проекту и сконфигурирует проверку на правило `LocalVariableName`, которое
проверяет правильность именований локальных переменных, а саму проверку (`check` в `executions`) будет запускать на ранней файзе
`validate` ещё до тестов.

Запустите `mvn clean test`. Сборка должна упасть, так как плагин должен найти эту ошибку именования. Откройте полные логи сборки,
кликнув на верхний пункт в левом нижнем окне (по умолчанию будет выделен самый нижний, как на скриншоте ниже),
прокручивая окно логов сборки сверху вниз, найдите первое упоминания слова `ERROR` (с англ. «ошибка»), убедитесь, что в этой строке идёт речь
про неправильное именование переменной `Percent`:

<img width="255" alt="image" src="https://user-images.githubusercontent.com/53707586/212552797-162c2265-66b0-4206-b6a3-1d3f0de55568.png">

Сделайте коммит с комментарием `Rename percent to Percent` и запушьте изменения на GitHub.Создайте баг-репорт на основе GitHub Issues.

В шагах воспроизведения укажите запуск `mvn clean test`, в ожидаемом результате успешное прохождение сборки, в 
фактическом — падение из-за неправильного именования переменной `Percent`.
Добавьте в баг-репорт ещё один раздел для логов сборки. Синтаксис красивого его оформления будет следующим. Обратите внимание на пробелы и пустые строки, это важно, чтобы всё сработало:

<img width="341" alt="image" src="https://user-images.githubusercontent.com/53707586/212553230-ad39ce10-9eb8-4fa1-a60d-2530a5137d62.png">
После создания баг-репорта переключитесь на IDEA, исправьте дефект (переименуйте обратно на `percent`), убедитесь, что сборка
проходит успешно, сделайте коммит с сообщением `Fix #1` и пуш, где `1` — это номер баг-репорта, который будет закрыт 
автоматически при пуше этого коммита в основную ветку.
В качестве решения второго задания пришлите ссылку на этот фикс-коммит на GitHub.

------
<details closed>
  <summary> Прежде чем ознакомиться с моим решением задачи, пожалуйста, подумайте над ней самостоятельно.</summary>
  
  ## Когда ты запустил тест и он не работает.
  
![xnidfnaqjhduzgrg619n8uvtau4](https://github.com/user-attachments/assets/81ae4206-77e5-4012-bb6e-5061463ae639)

