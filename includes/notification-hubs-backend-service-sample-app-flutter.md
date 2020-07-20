---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 2f8cd5e554980225850780cc474106c3dc69288a
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156341"
---
### <a name="create-the-flutter-solution"></a>Creare la soluzione Flutter

1. Aprire una nuova istanza di **Visual Studio Code**.

1. Aprire il **Riquadro comandi** (**MAIUSC** + **COMMAND** + **P**).

1. Selezionare il comando **Flutter: Nuovo progetto** e premere **INVIO**.

1. Immettere *push_demo* per **Nome progetto** quindi selezionare una posizione per **Percorso progetto**.

1. Quando richiesto, scegliere **Ottieni i pacchetti**.

1. Premere **CTRL** + **clic** sulla cartella **kotlin** (in **app** > **src** > **main**), quindi scegliere **Visualizza in Finder**. Rinominare quindi le cartelle figlio (nella cartella **kotlin**) rispettivamente in ```com```, ```<your_organization>``` e ```pushdemo```.

    > [!NOTE]
    > Quando si usa il modello di **Visual Studio Code**, per impostazione predefinita queste cartelle sono denominate **com**, **example**, **<nome_progetto>** . Supponendo che venga usato **mobcat** per l'**organizzazione**, la struttura delle cartelle dovrebbe essere simile alla seguente:
    >
    > - kotlin
    >     - com
    >         - mobcat
    >             - pushdemo

1. Tornare in **Visual Studio Code** e aggiornare il valore di **applicationId** in **android** > **app** > **build.gradle** specificando `com.<your_organization>.pushdemo`.

    > [!NOTE]
    > È consigliabile usare il nome della propria organizzazione per il segnaposto *<your_organization>* . Se ad esempio si usa **mobcat** come nome dell'organizzazione, il valore di **nome pacchetto** sarà *com.mobcat.pushdemo*.

1. Aggiornare l'attributo **package** nei file **AndroidManifest.xml**, rispettivamente in **src** > **debug**, **src** > **main** e **src** > **profile**. Verificare che i valori corrispondano al valore di **applicationId** usato nel passaggio precedente.

    ```xml
    <manifest
        xmlns:android="http://schemas.android.com/apk/res/android"
        package="com.<your_organization>.pushdemo>">
        ...
    </manifest>
    ```

1. Aggiornare l'attributo ```android:label``` nel file  **AndroidManifest.xml** in **src** > **main** specificando *PushDemo*. Aggiungere quindi l'attributo ```android:allowBackup```, direttamente in ```android:label```, impostando il relativo valore su **false**.

    ```xml
    <application
        android:name="io.flutter.app.FlutterApplication"
        android:label="PushDemo"
        android:allowBackup="false"
        android:icon="@mipmap/ic_launcher">
        ...
    </application>
    ```

1. Aprire il file **build.gradle** a livello di app (**android** > **app** > **build.gradle**), quindi aggiornare *compileSdkVersion* (nella sezione **android**) per l'uso di API **29**. Aggiornare quindi i valori di *minSdkVersion* e *targetSdkVersion* (nella sezione **defaultConfig**), specificando rispettivamente **26** e **29**.

    > [!NOTE]
    > Ai fini di questa esercitazione sono supportati solo i dispositivi che eseguono il **livello API 26 e successivo**, tuttavia è possibile estendere il supporto ai dispositivi che eseguono versioni precedenti.

1. Premere **CTRL** + **clic** sulla cartella **ios**, quindi scegliere **Apri in Xcode**.

1. In **Xcode**fare clic su **Runner** (**xcodeproj** nella parte superiore, non la cartella). Selezionare quindi la destinazione **Runner** e la scheda **Generale**. Con l'opzione **Tutto** selezionata per la configurazione di compilazione, aggiornare **Identificatore del bundle** specificando `com.<your_organization>.PushDemo`.

    > [!NOTE]
    > È consigliabile usare il nome della propria organizzazione per il segnaposto *<your_organization>* . Se ad esempio si usa **mobcat** come nome dell'organizzazione, il valore di **Identificatore del bundle** sarà *com.mobcat.PushDemo*.

1. Fare clic su **info.plist** quindi aggiornare il valore di **Nome del bundle** specificando **PushDemo**

1. Chiudere **Xcode** e tornare in **Visual Studio Code**.

1. Di nuovo in **Visual Studio Code** aprire **pubspec.yaml**, quindi aggiungere i **pacchetti Dart** [http](https://pub.dev/packages/http) e [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage) come dipendenze. Quindi, salvare il file e fare clic su **Ottieni i pacchetti** quando richiesto.

    ```yaml
    dependencies:
      flutter:
        sdk: flutter

      http: ^0.12.1
      flutter_secure_storage: ^3.3.3
    ```

1. In **Terminale**passare alla cartella **ios** (per il progetto Flutter). Eseguire quindi il comando [**pod install**](https://guides.cocoapods.org/using/getting-started.html#installation) per installare i nuovi pod, necessari per il pacchetto [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage).

1. Premere **CTRL** + **clic** sulla cartella **lib**, quindi scegliere **Nuovo file** dal menu usando *main_page.dart* come nome file. Aggiungere quindi il codice seguente.

    ```kotlin
    import 'package:flutter/material.dart';

    class MainPage extends StatefulWidget {
      @override
      _MainPageState createState() => _MainPageState();
    }

    class _MainPageState extends State<MainPage> {
      @override
      Widget build(BuildContext context) {
        return Scaffold(
          body: SafeArea(
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.stretch,
              children: <Widget>[],
            )
          )
        );
      }
    }
    ```

1. In **main.dart** sostituire il codice basato su modello con quanto segue.

    ```dart
    import 'package:flutter/material.dart';
    import 'package:push_demo/main_page.dart';

    final navigatorKey = GlobalKey<NavigatorState>();

    void main() => runApp(MaterialApp(home: MainPage(), navigatorKey: navigatorKey));
    ```

1. In **Terminale** compilare ed eseguire l'app basata su modello in ogni piattaforma di destinazione per verificare se viene eseguita nei dispositivi. Assicurarsi che i dispositivi supportati siano connessi.

    ```bash
    flutter run
    ```

### <a name="implement-the-cross-platform-components"></a>Implementare i componenti multipiattaforma

1. Premere **CTRL** + **clic** sulla cartella **lib**, quindi scegliere **Nuova cartella** dal menu usando *models* per **Nome cartella**.

1. Premere **CTRL** + **clic** sulla cartella **models**, quindi scegliere **Nuovo file** dal menu usando *device_installation.dart* come nome file. Aggiungere quindi il codice seguente.

    ```dart
    class DeviceInstallation {
        final String deviceId;
        final String platform;
        final String token;
        final List<String> tags;

        DeviceInstallation(this.deviceId, this.platform, this.token, this.tags);

        DeviceInstallation.fromJson(Map<String, dynamic> json)
          : deviceId = json['installationId'],
            platform = json['platform'],
            token = json['pushChannel'],
            tags = json['tags'];

        Map<String, dynamic> toJson() =>
        {
          'installationId': deviceId,
          'platform': platform,
          'pushChannel': token,
          'tags': tags,
        };
    }
    ```

1. Aggiungere alla cartella **models** un nuovo file denominato *push_demo_action.dart* definendo l'enumerazione di azioni supportate in questo esempio.

    ```dart
    enum PushDemoAction {
      actionA,
      actionB,
    }
    ```

1. Aggiungere al progetto una nuova cartella denominata **services**, quindi aggiungere a tale cartella un nuovo file denominato *device_installation_service.dart* con l'implementazione seguente.

    ```dart
    import 'package:flutter/services.dart';

    class DeviceInstallationService {
      static const deviceInstallation = const MethodChannel('com.<your_organization>.pushdemo/deviceinstallation');
      static const String getDeviceIdChannelMethod = "getDeviceId";
      static const String getDeviceTokenChannelMethod = "getDeviceToken";
      static const String getDevicePlatformChannelMethod = "getDevicePlatform";

      Future<String> getDeviceId() {
        return deviceInstallation.invokeMethod(getDeviceIdChannelMethod);
      }

      Future<String> getDeviceToken() {
        return deviceInstallation.invokeMethod(getDeviceTokenChannelMethod);
      }

      Future<String> getDevicePlatform() {
        return deviceInstallation.invokeMethod(getDevicePlatformChannelMethod);
      }
    }
    ```

    > [!NOTE]
    > È consigliabile usare il nome della propria organizzazione per il segnaposto *<your_organization>* . Se ad esempio si usa **mobcat** come nome dell'organizzazione, il valore di [MethodChannel](https://api.flutter.dev/flutter/services/MethodChannel-class.html) sarà *com.mobcat.pushdemo/deviceinstallation*.
    >
    > Questa classe incapsula l'uso della piattaforma nativa sottostante per acquisire i dettagli di installazione necessari del dispositivo. [MethodChannel](https://api.flutter.dev/flutter/services/MethodChannel-class.html) semplifica la comunicazione asincrona bidirezionale con le piattaforme native sottostanti. La controparte specifica della piattaforma per questo canale verrà creata in nei passaggi successivi.

1. Aggiungere a tale cartella un altro file denominato *notification_action_service.dart* con l'implementazione seguente.

    ```dart
    import 'package:flutter/services.dart';
    import 'dart:async';
    import 'package:push_demo/models/push_demo_action.dart';

    class NotificationActionService {
      static const notificationAction =
          const MethodChannel('com.<your_organization>.pushdemo/notificationaction');
      static const String triggerActionChannelMethod = "triggerAction";
      static const String getLaunchActionChannelMethod = "getLaunchAction";

      final actionMappings = {
        'action_a' : PushDemoAction.actionA,
        'action_b' : PushDemoAction.actionB
      };

      final actionTriggeredController = StreamController.broadcast();

      NotificationActionService() {
        notificationAction
            .setMethodCallHandler(handleNotificationActionCall);
      }

      Stream get actionTriggered => actionTriggeredController.stream;

      Future<void> triggerAction({action: String}) async {

        if (!actionMappings.containsKey(action)) {
          return;
        }

        actionTriggeredController.add(actionMappings[action]);
      }

      Future<void> checkLaunchAction() async {
        final launchAction = await notificationAction.invokeMethod(getLaunchActionChannelMethod) as String;

        if (launchAction != null) {
          triggerAction(action: launchAction);
        }
      }

      Future<void> handleNotificationActionCall(MethodCall call) async {
        switch (call.method) {
          case triggerActionChannelMethod:
            return triggerAction(action: call.arguments as String);
          default:
            throw MissingPluginException();
            break;
        }
      }
    }
    ```

    > [!NOTE]
    > Questo semplice meccanismo consente di centralizzare la gestione delle azioni di notifica in modalità multipiattaforma usando un'enumerazione fortemente tipizzata. Tramite il servizio la piattaforma nativa sottostante può attivare un'azione, se ne è specificata una nel payload di notifica. Il codice comune, inoltre, può verificare retrospettivamente se è stata specificata un'azione durante l'avvio dell'applicazione quando Flutter è pronto per elaborarla, ad esempio quando l'app viene avviata toccando una notifica del centro notifiche.

1. Aggiungere alla cartella **services** un nuovo file denominato *notification_registration_service.dart* con l'implementazione seguente.

    ```dart
    import 'dart:convert';
    import 'package:flutter/services.dart';
    import 'package:http/http.dart' as http;
    import 'package:push_demo/services/device_installation_service.dart';
    import 'package:push_demo/models/device_installation.dart';
    import 'package:flutter_secure_storage/flutter_secure_storage.dart';

    class NotificationRegistrationService {
      static const notificationRegistration =
          const MethodChannel('com.<your_organization>.pushdemo/notificationregistration');

      static const String refreshRegistrationChannelMethod = "refreshRegistration";
      static const String installationsEndpoint = "api/notifications/installations";
      static const String cachedDeviceTokenKey = "cached_device_token";
      static const String cachedTagsKey = "cached_tags";

      final deviceInstallationService = DeviceInstallationService();
      final secureStorage = FlutterSecureStorage();

      String baseApiUrl;
      String apikey;

      NotificationRegistrationService(this.baseApiUrl, this.apikey) {
        notificationRegistration
            .setMethodCallHandler(handleNotificationRegistrationCall);
      }

      String get installationsUrl => "$baseApiUrl$installationsEndpoint";

      Future<void> deregisterDevice() async {
        final cachedToken = await secureStorage.read(key: cachedDeviceTokenKey);
        final serializedTags = await secureStorage.read(key: cachedTagsKey);

        if (cachedToken == null || serializedTags == null) {
          return;
        }

        var deviceId = await deviceInstallationService.getDeviceId();

        if (deviceId.isEmpty) {
          throw "Unable to resolve an ID for the device.";
        }

        var response = await http
            .delete("$installationsUrl/$deviceId", headers: {"apikey": apikey});

        if (response.statusCode != 200) {
          throw "Deregister request failed: ${response.reasonPhrase}";
        }

        await secureStorage.delete(key: cachedDeviceTokenKey);
        await secureStorage.delete(key: cachedTagsKey);
      }

      Future<void> registerDevice(List<String> tags) async {
        try {
          final deviceId = await deviceInstallationService.getDeviceId();
          final platform = await deviceInstallationService.getDevicePlatform();
          final token = await deviceInstallationService.getDeviceToken();

          final deviceInstallation =
              DeviceInstallation(deviceId, platform, token, tags);

          final response = await http.put(installationsUrl,
              body: jsonEncode(deviceInstallation),
              headers: {"apikey": apikey, "Content-Type": "application/json"});

          if (response.statusCode != 200) {
            throw "Register request failed: ${response.reasonPhrase}";
          }

          final serializedTags = jsonEncode(tags);

          await secureStorage.write(key: cachedDeviceTokenKey, value: token);
          await secureStorage.write(key: cachedTagsKey, value: serializedTags);
        } on PlatformException catch (e) {
          throw e.message;
        } catch (e) {
          throw "Unable to register device: $e";
        }
      }

      Future<void> refreshRegistration() async {
        final currentToken = await deviceInstallationService.getDeviceToken();
        final cachedToken = await secureStorage.read(key: cachedDeviceTokenKey);
        final serializedTags = await secureStorage.read(key: cachedTagsKey);

        if (currentToken == null ||
            cachedToken == null ||
            serializedTags == null ||
            currentToken == cachedToken) {
          return;
        }

        final tags = jsonDecode(serializedTags);

        return registerDevice(tags);
      }

      Future<void> handleNotificationRegistrationCall(MethodCall call) async {
        switch (call.method) {
          case refreshRegistrationChannelMethod:
            return refreshRegistration();
          default:
            throw MissingPluginException();
            break;
        }
      }
    }
    ```

    > [!NOTE]
    > Questa classe incapsula l'uso di **DeviceInstallationService** e le richieste al servizio back-end per eseguire le azioni necessarie di registrazione, nonché di annullamento e aggiornamento della registrazione. L'argomento **apiKey** è obbligatorio solo se si è scelto di completare la sezione [Autenticare i client usando una chiave API](#authenticate-clients-using-an-api-key-optional).

1. Aggiungere alla cartella **lib** un nuovo file denominato *config.dart* con l'implementazione seguente.

    ```dart
    class Config {
      static String apiKey = "API_KEY";
      static String backendServiceEndpoint = "BACKEND_SERVICE_ENDPOINT";
    }
    ```

    > [!NOTE]
    > Si tratta di un semplice modo per definire i segreti dell'app. Sostituire i valori segnaposto con i propri. Si dovrebbe aver preso nota di questi valori durante la compilazione del servizio back-end. L'**URL dell'app per le API** dovrà essere `https://<api_app_name>.azurewebsites.net/`. Il membro **apiKey** è necessario solo se si è scelto di completare la sezione [Autenticare i client usando una chiave API](#authenticate-clients-using-an-api-key-optional).
    >
    > Assicurarsi di aggiungerlo al file gitignore per evitare di eseguire il commit di questi segreti nel controllo del codice sorgente.

### <a name="implement-the-cross-platform-ui"></a>Implementare l'interfaccia utente multipiattaforma

1. In **main_page.dart**sostituire la funzione **build** con il codice seguente.

    ```dart
    @override
    Widget build(BuildContext context) {
    return Scaffold(
        body: Padding(
          padding: const EdgeInsets.symmetric(horizontal: 20.0, vertical: 40.0),
          child: Column(
            mainAxisAlignment: MainAxisAlignment.end,
            crossAxisAlignment: CrossAxisAlignment.stretch,
            children: <Widget>[
              FlatButton(
                child: Text("Register"),
                onPressed: registerButtonClicked,
              ),
              FlatButton(
                child: Text("Deregister"),
                onPressed: deregisterButtonClicked,
              ),
            ],
          ),
        ),
      );
    }
    ```

1. Aggiungere le istruzioni import necessarie all'inizio del file **main_page.dart**.

    ```dart
    import 'package:push_demo/services/notification_registration_service.dart';
    import 'config.dart';
    ```

1. Aggiungere un campo alla classe **_MainPageState** per archiviare un riferimento a **NotificationRegistrationService**.

    ```dart
    final notificationRegistrationService = NotificationRegistrationService(Config.backendServiceEndpoint, Config.apiKey);
    ```

1. Nella classe **_MainPageState** implementare i gestori per gli eventi **onPressed** dei pulsanti **Register** e **Deregister**. Chiamare i metodi **Register**/**Deregister** corrispondenti, quindi mostrare un avviso per indicare il risultato.

    ```dart
    void registerButtonClicked() async {
        try {
          await notificationRegistrationService.registerDevice(List<String>());
          await showAlert(message: "Device registered");
        }
        catch (e) {
          await showAlert(message: e);
        }
      }

      void deregisterButtonClicked() async {
        try {
          await notificationRegistrationService.deregisterDevice();
          await showAlert(message: "Device deregistered");
        }
        catch (e) {
          await showAlert(message: e);
        }
      }

      Future<void> showAlert({ message: String }) async {
        return showDialog<void>(
          context: context,
          barrierDismissible: false,
          builder: (BuildContext context) {
            return AlertDialog(
              title: Text('PushDemo'),
              content: SingleChildScrollView(
                child: ListBody(
                  children: <Widget>[
                    Text(message),
                  ],
                ),
              ),
              actions: <Widget>[
                FlatButton(
                  child: Text('OK'),
                  onPressed: () {
                    Navigator.of(context).pop();
                  },
                ),
              ],
            );
          },
        );
      }
    ```

1. In **main.dart** verificare che le istruzioni import seguenti siano presenti all'inizio del file.

    ```dart
    import 'package:flutter/material.dart';
    import 'package:push_demo/models/push_demo_action.dart';
    import 'package:push_demo/services/notification_action_service.dart';
    import 'package:push_demo/main_page.dart';
    ```

1. Dichiarare una variabile per archiviare il riferimento a un'istanza di **NotificationActionService** e inizializzarla.

    ```dart
    final notificationActionService = NotificationActionService();
    ```

1. Aggiungere le funzioni per gestire la visualizzazione di un avviso quando viene attivata un'azione.

    ```dart
    void notificationActionTriggered(PushDemoAction action) {
      showActionAlert(message: "${action.toString().split(".")[1]} action received");
    }

    Future<void> showActionAlert({ message: String }) async {
      return showDialog<void>(
        context: navigatorKey.currentState.overlay.context,
        barrierDismissible: false,
        builder: (BuildContext context) {
          return AlertDialog(
            title: Text('PushDemo'),
            content: SingleChildScrollView(
              child: ListBody(
                children: <Widget>[
                  Text(message),
                ],
              ),
            ),
            actions: <Widget>[
              FlatButton(
                child: Text('OK'),
                onPressed: () {
                  Navigator.of(context).pop();
                },
              ),
            ],
          );
        },
      );
    }
    ```

1. Aggiornare la funzione **main** per osservare il flusso **NotificationActionService** **actionTriggered** e verificare la presenza di eventuali azioni acquisite durante l'avvio dell'app.

    ```dart
    void main() async {
      runApp(MaterialApp(home: MainPage(), navigatorKey: navigatorKey,));
      notificationActionService.actionTriggered.listen((event) { notificationActionTriggered(event as PushDemoAction); });
      await notificationActionService.checkLaunchAction();
    }
    ```

    > [!NOTE]
    > Serve semplicemente a dimostrare la ricezione e la propagazione delle azioni di notifica push. In genere queste azioni vengono gestite in modo automatico, ad esempio passando a una visualizzazione specifica o aggiornando alcuni dati anziché visualizzare un avviso in questo caso.
