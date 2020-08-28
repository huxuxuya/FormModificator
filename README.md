[![Release](https://img.shields.io/github/tag/huxuxuya/FormModificator.svg?label=Last%20release&a)](https://github.com/huxuxuya/FormModificator/releases)

# Модуль изменения управляемых форм

При доработке типовых конфигураций, для уменьшения издержек на сопровождение конфигурации при ее обновлении, доработка форм должна производиться с максимальным приоритетом программной доработки.

Целью модуля является упрощенное добавление реквизитов формы, добавление их на форму (в том числе и таблиц), так же добавление групп, команд формы и кнопок к ним.

### Установка

Установка производится через сравнение/объединения конфигурации с файлом поставки, с установкой на поддержку.

#### Предварительные требования

Модуль распространяется поставкой. 
Зависимостей не имеет.

### Обновление

Обновление производится через обновление с сохранением конфигурации поставщика
Для проверки корректности совместимистои версий необходимо открыть обработку тестирования обновляемой версии(которая входит в поставку) на версии, на которую будет обновлен модуль.

## Начало работы

### Предварительная подготовка

Необходимо добавить вызов процедуры, содержащей код программного добавления в самое начало следующих процедур:
 * ПриСозданииНаСервере()
 * ПриЧтенииНаСервер() - если форма имеет возможность открытия существующего объекта
  
 <details>
  <summary>Пример кода подготовки</summary>
	
 ``` bsl
&НаСервере
Процедура ПриЧтенииНаСервере(ТекущийОбъект)
	ПрефиксПодготовитьФорму();
	//Текст процедуры	
КонецПроцедуры 	

 &НаСервере
Процедура ПриСозданииНаСервере(Отказ, СтандартнаяОбработка)
	ПрефиксПодготовитьФорму();
	//Текст процедуры	
КонецПроцедуры 

&НаСервере	
Процедура ПрефиксПодготовитьФорму()
	Если РедакторФорм.ФормаПодготовлена(ЭтаФорма) Тогда
		Возврат;
	КонецЕсли;
	//Код изменения формы
КонецПроцедуры 
```
</details>

**Данная архитектура необходима:**
* Для возможности отображения данных, которые находятся не в самом объекте. 
* Для корректной настройке формы, которая должна происходить при чтении объекта, до вызовая создания на сервере.

Так же данный подход позволяет использовать типовые процедуры, например **УправлениеФормой()**.

### Использование

#### Базовый пример использования
<details>
 <summary>Вариант заполнение свойств элемента после инициализации контекста.</summary>
	
``` bsl
КонтекстФормы = РедакторФорм.НовыйКонтекстЭлемента(ЭтотОбъект);	
КонтекстФормы.Свойства.Вставить("Вид", ВидГруппыФормы.ОбычнаяГруппа);
КонтекстФормы.Свойства.Вставить("Группировка", ГруппировкаПодчиненныхЭлементовФормы.ГоризонтальнаяЕслиВозможно);
КонтекстФормы.Свойства.Вставить("ОтображатьЗаголовок", Ложь);
ЭлементГруппаШапка = РедакторФорм.НоваяГруппаФормы(КонтекстФормы, "ГруппаШапка"); 
```
</details>

<details>
 <summary>Вариант определение свойств до создания контекста.</summary>
	
``` bsl
Свойства = Новый Структура("Вид, ОтображатьЗаголовок", ВидГруппыФормы.ОбычнаяГруппа, Ложь);
КонтекстФормы = РедакторФорм.НовыйКонтекстЭлемента(ЭтотОбъект, , , Свойства);	
ЭлементГруппаШапка = РедакторФорм.НоваяГруппаФормы(КонтекстФормы, "ГруппаШапка"); 
```
</details>

### Программный интерфейс

* Добавление полей
	<details>
	 <summary>НовоеПолеФормы(КонтекстЭлемента, ИмяПоля)</summary>

	``` bsl
	КонтекстПоля = РедакторФорм.НовыйКонтекстЭлемента(ЭтотОбъект, ГруппаЛево);	
	КонтекстПоля.Свойства.Вставить("ПутьКДанным", "Статус");
	РедакторФорм.НовоеПолеФормы(КонтекстПоля, "Статус");
	```
	</details>
	<details>
	 <summary>НовоеПолеРеквизитаФормы(КонтекстЭлемента, ИмяРеквизита)</summary>

	``` bsl
	КонтекстЭлемента = РедакторФорм.НовыйКонтекстЭлемента(ЭтотОбъект, ГруппаПраво);
	РедакторФорм.НовоеПолеРеквизитаФормы(КонтекстЭлемента, "Состояние");
	```
	</details>	
* Добавление групп 
	<details>
	 <summary>НоваяГруппаФормы(КонтекстЭлемента, ИмяГруппы)</summary>

	``` bsl
	КонтекстСтраницы = РедакторФорм.НовыйКонтекстЭлемента(ЭтотОбъект);
	КонтекстСтраницы.Свойства.Вставить("Вид", ВидГруппыФормы.Страницы);
	ГруппаСтраницы = РедакторФорм.НоваяГруппаФормы(КонтекстСтраницы, "ГруппаСтраницы");

	КонтекстСтраницы.Свойства.Вид = ВидГруппыФормы.Страница;	
	КонтекстСтраницы.Родитель = ГруппаСтраницы;
	КонтекстСтраницы.Свойства.Вставить("Заголовок", "Товары");
	СтраницаТовары = РедакторФорм.НоваяГруппаФормы(КонтекстСтраницы, "СтраницаТовары");	
	
	КонтекстСтраницы.Свойства.Вставить("Заголовок", "Услуги");
	СтраницаУслуги = РедакторФорм.НоваяГруппаФормы(КонтекстСтраницы, "СтраницаУслуги");
	```
	</details>	
	<details>
	 <summary>НоваяГруппаКолонкиЛевоПраво(КонтекстЭлемента, ИмяОсновнойГруппы)</summary>

	``` bsl
	КонтекстГруппы = РедакторФорм.НовыйКонтекстЭлемента(ЭтотОбъект, Элементы.ГруппаОсновная);
	РедакторФорм.НоваяГруппаКолонкиЛевоПраво(КонтекстГруппы, "Шапка");
	ШапкаЛево = Элементы.ШапкаЛево;
	ШапкаПраво = Элементы.ШапкаПраво;
	```
	</details>	
	<details>
	 <summary>НоваяГруппаОбычная(КонтекстЭлемента, ИмяЭлемента)</summary>

	``` bsl
	КонтекстГруппы = РедакторФорм.НовыйКонтекстЭлемента(ЭтотОбъект, ГруппаСтраницаТовары);
	ГруппаИтогов = РедакторФорм.ДобыавитьГруппуОбычную(КонтекстГруппы, "ГруппаИтогов");
	```
	</details>	 
* Добавление таблиц
	<details>
	 <summary>НоваяТаблицаФормы(КонтекстЭлемента, ИмяПоля, ПутьКДанным, СтруктураКолонок)</summary>

	``` bsl
	КонтекстГруппТаблицы = РедакторФорм.НовыйКонтекстЭлемента(ЭтотОбъект, ГруппаИтоговойТаблицы);
	
	СтруктураКолонок = Новый Структура;
	СтруктураКолонок.Вставить("ТаблицаКолонка1", "Колонка1");
	СтруктураКолонок.Вставить("ТаблицаКолонка2", "Колонка2");
	СтруктураКолонок.Вставить("ТаблицаКолонка3", "Колонка3");
	ЭлементТаблицаПроверряемыеМетоды = РедакторФорм.НоваяТаблицаФормы(КонтекстГруппТаблицы, "ИмяТаблицы", "Объект.ТаблицаОбъекта", СтруктураКолонок);	
	```
	</details>
	<details>
	 <summary>НовоеПолеТабличнойЧастиформы(КонтекстЭлемента, ПутьТабличнойЧасти, ИмяКолонкиТабличнойЧасти)</summary>

	``` bsl
	КонтекстФормы = РедакторФорм.НовыйКонтекстЭлемента(ЭтотОбъект);
	РедакторФорм.НовоеПолеТабличнойЧастиформы(КонтекстФормы, "Объект.ИмяТаблицы", "ИмяКолонки");
	```
	</details>	
* Добавление команд
	<details>
	 <summary>НоваяКнопкаФормы(КонтекстЭлемента, ИмяКнопки, ВидКнопки, ИмяКоманды)</summary>

	``` bsl
	КонтекстГруппыКнопок = РедакторФорм.НовыйКонтекстЭлемента(ЭтотОбъект, ГруппаКнопок);
	РедакторФорм.НоваяКнопкаФормы(КонтекстГруппыКнопок, "ИмяКоманды", ВидКнопкиФормы.ОбычнаяКнопка, "Подключаемый_КомандаИмяКнопки");
	```
	</details>	 
	<details>
	 <summary>НоваяКомандаИГиперссылкаФормы(КонтекстЭлемента, ИмяКоманды, ПроцедураОбработки, ЗаголовокГиперссылки, ГруппаВывода)</summary>

	``` bsl
	КонтекстКоманды = РедакторФорм.НовыйКонтекстЭлемента(ЭтотОбъект, Элементы.Группа);
	РедакторФорм.НоваяКомандаИГиперссылкаФормы(КонтекстКоманды, "ИмяКоманды", "Подключаемый_КомандаИмяКоманды", "Очистить все");
	```
	</details>	 
	<details>
	 <summary>НоваяКомандаИКнопкаКоманднойПанели(КонтекстЭлемента, ИмяКоманды, ЗаголовокКнопки)</summary>

	``` bsl
	КонтекстКоманды = РедакторФорм.НовыйКонтекстЭлемента(ЭтотОбъект, Элементы.Группа);
	КонтекстФормы.Родитель = ЭлементТаблицаПроверряемыеМетоды.КоманднаяПанель;
	РедакторФорм.НоваяКомандаИКнопкаКоманднойПанели(КонтекстФормы, "КомандаКнопки3", "Обновить");
	```
	</details>
	</details>	 
	<details>
	 <summary>НоваяКомандаИКнопкаФормы(КонтекстЭлемента, ИмяКоманды, ЗаголовокКнопки)</summary>

	``` bsl
	КонтекстКнопки = РедакторФорм.НовыйКонтекстЭлемента(ЭтотОбъект, Элементы.ГруппаИтоги);
	РедакторФорм.НоваяКомандаИКнопкаФормы(КонтекстКнопки, "ИмяКоманды", "Заголовок");
	```
	</details>
	<details>
	 <summary>НоваяКомандаФормы(КонтекстЭлемента, ИмяКоманды, ИмяДействия, Заголовок, Подсказка)</summary>

	``` bsl
	КонтекстКоманда = РедакторФорм.НовыйКонтекстЭлемента(ЭтотОбъект, Элементы.Группа);	
	РедакторФорм.НоваяКомандаФормы(КонтекстКоманда, "Обновить", "Подключаемый_Обновить", "Обновить информаци");
	```
	</details>	 
* Добавление реквизитов объекта
	<details>
	 <summary>ДобавитьРеквизитОбъектаНаФорму(КонтекстЭлемента, ИмяРеквизита)</summary>

	``` bsl
	КонтекстГруппыБулево = РедакторФорм.НовыйКонтекстЭлемента(ЭтотОбъект, Элементы.Группа);
	РедакторФорм.ДобавитьРеквизитОбъектаНаФорму(КонтекстГруппыБулево, "НовыйРеквизит");
	```
	</details>	
	<details>
	 <summary>НовоеПолеФормыРеквизитОбъекта(КонтекстЭлемента, ИмяРеквизита)</summary>

	``` bsl
	КонтекстГруппы = РедакторФорм.НовыйКонтекстЭлемента(ЭтотОбъект, Элементы.ГуппаЛево);
	РедакторФорм.НовоеПолеФормыРеквизитОбъекта(КонтекстГруппы, "ЭтоНалог");
	```
	</details>	
	<details>
	 <summary>НовоеПолеШапкиФормыРеквизитОбъекта(КонтекстЭлемента, ИмяРеквизита</summary>

	``` bsl
	КонтекстГруппы = РедакторФорм.НовыйКонтекстЭлемента(ЭтотОбъект, Элементы.ГруппаШапкаЛево);
	РедакторФорм.НовоеПолеШапкиФормыРеквизитОбъекта(КонтекстГруппы, "Подразделение");
	```
	</details>	
	<details>
	 <summary>НовоеМногострочноеПолеРеквизитОбъекта(КонтекстЭлемента, ИмяРеквизита, Высота )</summary>

	``` bsl
	КонтекстЭлемента = РедакторФорм.НовыйКонтекстЭлемента(ЭтотОбъект, Элементы.ГруппаПодвал);
	РедакторФорм.НовоеМногострочноеПолеРеквизитОбъекта(КонтекстЭлемента, "Комментарий");	
	```
	</details>	
	<details>
	 <summary>НовоеПолеФлажокФормыРеквизитОбъекта(КонтекстЭлемента, ИмяРеквизита)</summary>

	``` bsl
	КонтекстГруппы = РедакторФорм.НовыйКонтекстЭлемента(ЭтотОбъект, Элементы.ГруппаДополнительно);
	РедакторФорм.НовоеПолеФлажокФормыРеквизитОбъекта(КонтекстГруппы, "ЭтоИнтеграционныйОбъект");
	```
	</details>	
	<details>
	 <summary>НоваяГруппаКнопок(КонтекстЭлемента)</summary>

	``` bsl
	КонтекстПанели = РедакторФорм.НовыйКонтекстЭлемента(ЭтотОбъект, ЭлементТаблицаПроверяемыеМетоды.КоманднаяПанель);
	РедакторФорм.НоваяГруппаКнопок(КонтекстПанели);
	```
	</details>	

## Запуск тестов

Тесты запускаются через vanessa runner: файл run_vanessa.bat.

## Authors

See the list of [contributors](https://github.com/huxuxuya/FormModificator/contributors) who participated in this project.
