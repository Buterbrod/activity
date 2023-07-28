# Документация
* [Build location-aware apps](https://developer.android.com/training/location)
* [> android.location](https://developer.android.com/reference/android/location/package-summary)

Написание кода:
* [Android Location API to track your current location](https://www.digitalocean.com/community/tutorials/android-location-api-tracking-gps)

# Android Location API

Android Location API можно использовать для отслеживания текущего местоположения вашего мобильного телефона и отображения в приложении.

Службы определения местоположения Android доступны, начиная с Android API 1. Google официально рекомендует использовать API службы определения местоположения **Google Play**.
Android Location Services API по-прежнему используется для разработки приложений на основе определения местоположения для устройств, которые не поддерживают сервисы Google Play.

|||
|-|-|
|bearing|Азимут - горизонтальный угол, отсчитываемый между заранее выбранным направлением (например, северным) и направлением на заданный предмет|
|||


## LocationListener (интерфейс)

Интерфейс LocationListener используется для получения уведомлений от **LocationManager** при изменении местоположения. 
Эти методы вызываются после регистрации прослушивателя в LocationManager.

```java
// вызывается, когда провайдер включен пользователем
default void onProviderEnabled(String provider)

// вызывается, когда провайдер отключен пользователем
default void onProviderDisabled(String provider)

// вызывается при изменении местоположения
default void onLocationChanged(Location location)

// вызывается, когда местоположение изменилось и местоположения доставляются партиями
// cписок местоположений не пуст и упорядочен от самого раннего местоположения
// к последнему местоположению
default void onLocationChanged(List<Location> locations)

// вызывается после завершения операции сброса и после доставки удаленных местоположений
default void onFlushComplete(int requestCode) 
```
## LocationManager (класс)
Этот класс предоставляет доступ к системным службам определения местоположения.

Эти службы позволяют приложениям получать периодические обновления о географическом местоположении устройства или получать уведомления, когда устройство приближается к заданному географическому местоположению.


!!!note 
    Для всех методов API определения местоположения требуются разрешения
    * Manifest.permission.ACCESS_COARSE_LOCATION 
    * или Manifest.permission.ACCESS_FINE_LOCATION.
  
    Если ваше приложение имеет только грубое разрешение, провайдеры все равно будут возвращать результаты определения местоположения, но точное местоположение будет обфусцировано до грубого уровня точности.

```java
// Broadcast intent action when the set of enabled location providers changes. 
String ACTION_GNSS_CAPABILITIES_CHANGED

// Intent extra included with ACTION_GNSS_CAPABILITIES_CHANGED broadcasts, containing the new GnssCapabilities.
String EXTRA_GNSS_CAPABILITIES

// Intent extra included with MODE_CHANGED_ACTION broadcasts, containing the boolean enabled state of location.
String 	EXTRA_LOCATION_ENABLED

// Intent extra included with PROVIDERS_CHANGED_ACTION broadcasts, containing the boolean enabled state of the location provider that has changed.
String EXTRA_PROVIDER_ENABLED

// Intent extra included with PROVIDERS_CHANGED_ACTION broadcasts, containing the name of the location provider that has changed.
String EXTRA_PROVIDER_NAME

// fused провайдер
String FUSED_PROVIDER

// определение местоположения с помощью спутников GNSS
// местоположение относится к основной позиции антенны GNSS внутри устройства
// скорость отклика и точность определения местоположения могут зависеть от условий сигнала GNSS
// --
// getGnssAntennaInfos() может использоваться для определения положения антенны GNSS
// относительно системы координат Android и преобразования между ними при необходимости.
// как правило, это необходимо только для приложений с высокой точностью.
// --
// дополнительно могут поступать следующие пары ключ/значение:
// * спутники - количество спутников, используемых для получения корректировки
String 	GPS_PROVIDER

// Broadcast intent action when the device location enabled state changes.
String 	MODE_CHANGED_ACTION

// Standard name of the network location provider.
String 	NETWORK_PROVIDER

// A special location provider for receiving locations without actively initiating a location fix.
String 	PASSIVE_PROVIDER

// Broadcast intent action when the set of enabled location providers changes. 
String 	PROVIDERS_CHANGED_ACTION

// -----------------------------------------------------------
// Возвращает список имен всех доступных поставщиков местоположений. Возвращаются все провайдеры, в том числе отключенные на данный момент.
public List<String> getAllProviders ()

// Асинхронно возвращает одно текущее исправление местоположения от данного поставщика на основе заданного LocationRequest.
// Может активировать датчики для вычисления нового местоположения.
// Функция обратного вызова вызовется один раз с найденным местоположением, либо с нулевым местоположением, если провайдер не смог определить местоположение.
// Может возвращать недавно определенное местоположение (порядка нескольких секунд), но никогда не возвращает более старые местоположения 
//     (например, несколько минут назад или старше). Но можно быть уверенным, что этот метод вернет наилучшее местоположение устройства в настоящий момент
// Можно использовать LocationRequest#getDurationMillis() для задания максимально допустимой продолжительности перед сбоем.
//     Система всегда будет ограничивать максимальное количество времени, в течение которого может выполняться запрос о текущем местоположении, 
//     до некоторого разумного значения (например, менее минуты), прежде чем запрос будет отклонен.
// Параметры:
//   CancellationSignal - отмены операции (без вызова функции обратного вызова)
//   
public void getCurrentLocation (String provider, 
                CancellationSignal cancellationSignal, 
                Executor executor, 
                Consumer<Location> consumer)

public void getCurrentLocation (String provider, 
                LocationRequest locationRequest,       // +
                CancellationSignal cancellationSignal, 
                Executor executor, 
                Consumer<Location> consumer)
                
// Возвращает текущий список сведений об антеннах GNSS или null, если они неизвестны или не поддерживаются.
public List<GnssAntennaInfo> getGnssAntennaInfos ()

// Возвращает поддерживаемые возможности набора микросхем GNSS
public GnssCapabilities getGnssCapabilities ()

// Возвращает название модели (включая производителя и версию аппаратного/программного обеспечения) аппаратного драйвера GNSS или null, если эта информация недоступна
// Этот API не возвращает серийный номер или идентификатор устройства
public String getGnssHardwareModelName ()

// Возвращает год выпуска аппаратного и программного обеспечения GNSS или 0, если год выпуска предшествует 2016
public int getGnssYearOfHardware ()

// Возвращает свойства провайдера или null, если свойства в настоящее время неизвестны
// Свойства провайдера могут меняться со временем, хотя это не рекомендуется и должно происходить редко.
// Наиболее распространенный переход - это когда свойства провайдера из неизвестных становятся известными, что чаще всего происходит во время загрузки.
public ProviderProperties getProviderProperties (String provider)

// Возвращает список имен доступных провайдеров
// Если enableOnly имеет значение false, функционально это то же самое, что и getAllProviders()
public List<String> getProviders (boolean enabledOnly)

// Возвращает true, если данный провайдер существует на этом устройстве, независимо от того, включен он в данный момент или нет
public boolean hasProvider (String provider)

// Возвращает текущее включенное/отключенное состояние местоположения. Чтобы прослушать изменения, см. MODE_CHANGED_ACTION.
public boolean isLocationEnabled ()

// Возвращает текущий статус (включенный/отключенный) провайдера
public boolean isProviderEnabled (String provider)

// Регистрирует прослушиватель информации об антенне GNSS, который будет получать все изменения в информации об антенне.
// Используйте getGnssAntennaInfos() для получения информации о текущей антенне
// Не все наборы микросхем GNSS поддерживают обновление информации об антенне, см. getGnssCapabilities(). Если не поддерживается, слушатель никогда не будет вызван.
public boolean registerAntennaInfoListener (Executor executor, 
                GnssAntennaInfo.Listener listener)

// Регистрирует обратный вызов измерения GNSS
public boolean registerGnssMeasurementsCallback (GnssMeasurementRequest request, 
                Executor executor, 
                GnssMeasurementsEvent.Callback callback)

public boolean registerGnssMeasurementsCallback (GnssMeasurementsEvent.Callback callback, 
                Handler handler)

// Регистрирует обратный вызов навигационного сообщения GNSS
// Навигационные сообщения GNSS будут получены только тогда, когда GPS_PROVIDER включен, а клиентское приложение находится на переднем плане.
public boolean registerGnssNavigationMessageCallback (GnssNavigationMessage.Callback callback, 
                Handler handler)

public boolean registerGnssNavigationMessageCallback (Executor executor, 
                GnssNavigationMessage.Callback callback)

// Регистрирует обратный вызов статуса GNSS
// Информация о состоянии GNSS будет получена только тогда, когда GPS_PROVIDER включен, а клиентское приложение находится на переднем плане.
public boolean registerGnssStatusCallback (GnssStatus.Callback callback, 
                Handler handler)

public boolean registerGnssStatusCallback (Executor executor, 
                GnssStatus.Callback callback)

// Удаляет прослушиватель NMEA
public void removeNmeaListener (OnNmeaMessageListener listener)

// Удаляет поставщика тестового местоположения с заданным именем или ничего не делает, если такой поставщик тестового местоположения не существует
public void removeTestProvider (String provider)

// Удаляет все обновления расположения для указанного LocationListener. Данный прослушиватель гарантированно не получит никаких вызовов, которые происходят после вызова этого метода.
// Если у данного прослушивателя есть какие-либо пакетные запросы, этот метод не будет сбрасывать неполные пакеты местоположения перед остановкой обновлений местоположения.
// Если вы хотите сбросить все ожидающие местоположения перед остановкой, вы должны сначала вызвать requestFlush(java.lang.String, android.location.LocationListener, int), 
// а затем вызвать этот метод после завершения сброса. Если этот метод вызывается до завершения сброса, вы можете не получить сброшенные местоположения.
public void removeUpdates (LocationListener listener)

// Запрашивает, чтобы данный провайдер сбрасывал все пакетные местоположения для провайдеров.
// У данного слушателя (зарегистрированного у провайдера) будет вызываться LocationListener#onFlushComplete(int) с заданным кодом результата
//    после того, как все местоположения, которые были сброшены, будут доставлены.
//    Если removeUpdates(android.location.LocationListener) вызывается до того, как будет выполнен обратный вызов flush, то обратный вызов flush никогда не будет выполнен.
public void requestFlush (String provider, 
                LocationListener listener, 
                int requestCode)

// !!!!!
// Зарегистрируйтесь для получения обновлений местоположения от данного провайдера с заданными аргументами и обратным вызовом Looper вызывающего потока.
// Если выбранный вами провайдер отключен, обновления местоположения прекратятся, и будет отправлено обновление доступности провайдера.
//    Как только провайдер снова будет включен, будет отправлено еще одно обновление доступности провайдера, и обновления местоположения возобновятся.
// Местоположение, возвращенное GPS_PROVIDER, относится к положению основной антенны GNSS внутри устройства. getGnssAntennaInfos() может использоваться 
//    для определения положения антенны GNSS относительно системы координат Android и преобразования между ними при необходимости.
//    Как правило, это необходимо только для приложений с высокой точностью.
// Когда вызываются обратные вызовы местоположения, система будет удерживать блокировку от имени вашего приложения в течение некоторого периода времени,
//    но не бесконечно. Если вашему приложению требуется длительный wakelock в обратном вызове местоположения, вы должны приобрести его самостоятельно.
// Чтобы отменить регистрацию для получения обновлений местоположения, используйте removeUpdates(android.location.LocationListener).
// Параметры:
//    minTimeMs - минимальный интервал времени между обновлениями местоположения в миллисекундах
//    minDistanceM - минимальное расстояние между обновлениями местоположения в метрах
public void requestLocationUpdates (String provider, 
                long minTimeMs, 
                float minDistanceM, 
                LocationListener listener)

public void requestLocationUpdates (String provider, 
                long minTimeMs, 
                float minDistanceM, 
                LocationListener listener, 
                Looper looper)

public void requestLocationUpdates (String provider, 
                long minTimeMs, 
                float minDistanceM, 
                Executor executor, 
                LocationListener listener)

public void requestLocationUpdates (String provider, 
                LocationRequest locationRequest, 
                Executor executor, 
                LocationListener listener)

// Отправляет дополнительные команды провайдеру местоположения
//    Может использоваться для поддержки специфичных для провайдера расширений API Location Manager.
public boolean sendExtraCommand (String provider, 
                String command, 
                Bundle extras)

// Включает или отключает данный поставщик тестов
public void setTestProviderEnabled (String provider, 
                boolean enabled)

// Устанавливает новое местоположение для данного тестового провайдера. Это местоположение будет идентифицировано как фиктивное для всех клиентов с помощью Location#isMock().
// Объект местоположения должен иметь минимальное количество полей, установленных для того, чтобы считаться допустимым, в соответствии с документацией по классу местоположения.
public void setTestProviderLocation (String provider, 
                Location location)

// Отменяет регистрацию прослушивателя информации об антенне GNSS
public void unregisterAntennaInfoListener (GnssAntennaInfo.Listener listener)

// Отменяет регистрацию обратного вызова измерения GPS
public void unregisterGnssMeasurementsCallback (GnssMeasurementsEvent.Callback callback)

// Отменяет регистрацию обратного вызова навигационного сообщения GNSS
public void unregisterGnssNavigationMessageCallback (GnssNavigationMessage.Callback callback)

// Удаляет обратный вызов статуса GNSS
public void unregisterGnssStatusCallback (GnssStatus.Callback callback)
```

## Location (класс)

Географическое положение
Местоположение состоит из широты, долготы, метки времени, точности и другой информации, такой как азимут, высота и скорость.

!!!note Поддельные местоположения
    Обратите внимание, что Android предоставляет приложениям возможность отправлять «ложные» или поддельные местоположения через LocationManager, и что эти местоположения затем могут быть получены приложениями, использующими LocationManager для получения информации о местоположении. Эти местоположения можно определить с помощью API isMock(). Имейте в виду, что у пользователя может быть веская причина для имитации своего местоположения, и поэтому приложения обычно должны отклонять фиктивные местоположения только тогда, когда для их варианта использования важно, чтобы принимались только реальные местоположения.

```java
// Преобразует координату широты/долготы в строковое представление
public static String convert (double coordinate, 
                int outputType)

public static double convert (String coordinate)

// Вычисляет приблизительное расстояние в метрах между двумя местоположениями и, 
// при необходимости, начальное и конечное направление кратчайшего пути между ними. 
// Расстояние и азимут определяются с помощью эллипсоида WGS84.
public static void distanceBetween (double startLatitude, 
                double startLongitude, 
                double endLatitude, 
                double endLongitude, 
                float[] results)

// Возвращает приблизительное расстояние в метрах между этим местоположением и заданным местоположением
// Расстояние определяется с помощью эллипсоида WGS84
public float distanceTo (Location dest)

// Возвращает true, если это местоположение помечено как фиктивное.
// Если это местоположение получено из платформы Android, это означает,
//    что местоположение было предоставлено поставщиком тестового местоположения 
//    и, следовательно, может не быть связано с фактическим местоположением устройства.
public boolean isMock ()
```





Android.location имеет два способа получения данных о местоположении:
|Тип провайдера|Описание|
|-|-|
|LocationManager.GPS_PROVIDER|определяет местоположение с помощью спутников. В зависимости от условий этому провайдеру может потребоваться некоторое время, чтобы вернуть исправление местоположения|
|LocationManager.NETWORK_PROVIDER|определяет местоположение на основе наличия ближайших вышек сотовой связи и точек доступа Wi-Fi. Это быстрее, чем GPS_PROVIDER|

## LocationManager (класс)
Класс LocationManager обеспечивает доступ к системным службам определения местоположения. 
