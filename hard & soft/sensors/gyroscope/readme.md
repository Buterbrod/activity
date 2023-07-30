
# Документация
[User locations](https://developer.android.com/training/location)

# Android
## Полномочия
Полномочия определяют:
- приложение работает на переднем/**заднем** плане
- приложение получает **точные**/примерные данные

|Полномочие|Описание|
|-|-|
|[ACCESS_BACKGROUND_LOCATION](https://developer.android.com/reference/android/Manifest.permission#ACCESS_BACKGROUND_LOCATION)|Позволяет приложению получать доступ к местоположению в **фоновом** режиме.<br>Если вы запрашиваете это разрешение, вы также должны запросить <br>- либо ACCESS_COARSE_LOCATION,<br>- либо ACCESS_FINE_LOCATION.<br>Запрос этого разрешения сам по себе не дает вам доступа к местоположению.|
|[ACCESS_COARSE_LOCATION](https://developer.android.com/reference/android/Manifest.permission#ACCESS_COARSE_LOCATION)|Позволяет приложению получить доступ к приблизительному местоположению.<br>Если эта оценка местоположения получена от службы LocationManagerService или FusedLocationProvider, то эта оценка имеет точность в пределах примерно 3 квадратных километров.<br>Альтернатива - ACCESS_FINE_LOCATION|
|[ACCESS_FINE_LOCATION](https://developer.android.com/reference/android/Manifest.permission#ACCESS_FINE_LOCATION)|Позволяет приложению получать доступ к **точному** местоположению.<br>Если оценка местоположения получена от LocationManagerService или FusedLocationProvider, эта оценка обычно находится в пределах примерно 50 метров, а иногда с точностью до нескольких метров или лучше.<br>Альтернатива - ACCESS_COARSE_LOCATION.|

Для запросов, работающих на заднем плане и точно определяющих координаты, надо включить такие привелегии:
```java
<manifest ... >
  <!-- Required only when requesting background location access on
       Android 10 (API level 29) and higher. -->
  <uses-permission android:name="android.permission.ACCESS_BACKGROUND_LOCATION" />

  <!-- Always include this permission -->
  <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />

  <!-- Include only if your app benefits from precise location access. -->
  <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
</manifest>
```
### Background Location Limits
[Background Location Limits](https://developer.android.com/about/versions/oreo/background-location-limits)

Чтобы снизить энергопотребление, Android 8.0 (уровень API 26) ограничивает частоту получения приложением информации о текущем местоположении пользователя, когда оно работает в фоновом режиме. В этих условиях приложения могут получать обновления местоположения **только несколько раз в час**.

Чтобы получать обновления местоположения чаще, можно выполнить одно из следующих действий:
- Использовать пассивный прослушиватель местоположения, который может получать более быстрые обновления местоположения, если есть приложения переднего плана, запрашивающие обновления местоположения с большей скоростью.
- Если вашему приложению требуется доступ к истории местоположений, которая содержит частые обновления, используйте пакетную версию элементов API `Fused Location Provider`, например интерфейс `FusedLocationProviderApi`. Когда ваше приложение работает в фоновом режиме, этот API получает информацию о местоположении пользователя чаще, чем непакетный API. Однако имейте в виду, что ваше приложение по-прежнему получает обновления пакетами только несколько раз в час.
