---
author: alexeystrakh
ms.author: alstrakh
ms.date: 06/11/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: c8271cc4de558ec5c97f41d6a070f71a9fc49cd2
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060434"
---
### <a name="create-the-react-native-solution"></a>Creare la soluzione React Native

1. Nel `Terminal` aggiornare gli strumenti dell'ambiente necessari per usare React Native con i comandi seguenti:

    ```bash
    # install node
    brew install node
    # or update
    brew update node
    # install watchman
    brew install watchman
    # or update
    brew upgrade watchman
    # install cocoapods
    sudo gem install cocoapods
    ```

1. Nel `Terminal` eseguire il comando seguente per disinstallare l'interfaccia della riga di comando di `React Native`, se è installata. Usare `npx` per accedere automaticamente alla versione più recente dell'interfaccia della riga di comando di React Native disponibile:

    ```bash
    npm uninstall -g react-native-cli
    ```

    > [!NOTE]
    > React native ha un'interfaccia della riga di comando incorporata. Invece di installare e gestire una versione specifica dell'interfaccia della riga di comando a livello globale, è consigliabile accedere alla versione corrente in fase di esecuzione usando `npx`, fornito con Node.js. Con `npx react-native <command>`, la versione stabile corrente dell'interfaccia della riga di comando viene scaricata ed eseguita al momento dell'esecuzione del comando.

1. Passare alla cartella dei progetti in cui si vuole creare la nuova applicazione. Usare il modello basato su Typescript specificando il parametro `--template`:

    ```bash
    # init new project with npx
    npx react-native init PushDemo --template react-native-template-typescript
    ```

1. Eseguire il server metro, che compila i bundle JavaScript e monitora gli aggiornamenti del codice per aggiornare i bundle in tempo reale:

    ```bash
    cd PushDemo
    npx react-native start
    ```

1. Eseguire l'app iOS per verificare l'installazione. Prima di eseguire il comando seguente, assicurarsi di aver avviato un simulatore iOS o di aver connesso un dispositivo iOS:

    ```bash
    npx react-native run-ios
    ```

1. Eseguire l'app Android per verificare l'installazione. Sono necessari alcuni passaggi aggiuntivi per configurare un emulatore o un dispositivo Android in modo che possa accedere al server metro di React Native. I comandi seguenti generano il bundle JavaScript iniziale per Android e lo inseriscono nella cartella assets.

    ```bash
    # create assets folder for the bundle
    mkdir android/app/scr/main/assets
    # build the bundle
    npx react-native bundle --platform android --dev true --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res
    # enable ability for sim to access the localhost
    adb reverse tcp:8081 tcp:8081
    ```

    Questo script verrà predistribuito con la versione iniziale dell'app. Dopo la distribuzione, configurare l'emulatore o il dispositivo per accedere al server metro specificando l'indirizzo IP del server. Usare il comando seguente per compilare ed eseguire l'applicazione Android:

    ```bash
    npx react-native run-android
    ```

    All'interno dell'app, premere `CMD+M` (emulatore) o scuotere il dispositivo per popolare le impostazioni modalità sviluppatore, passare a `Settings` > `Change Bundle Location` e specificare l'indirizzo IP del server metro con la porta predefinita: `<metro-server-ip-address>:8081`.

1. Nel file `App.tsx` applicare eventuali modifiche al layout di pagina e salvarlo in modo che le modifiche si riflettano automaticamente nelle app iOS e Android.

    > [!NOTE]
    > Una guida dettagliata alla configurazione dell'ambiente di sviluppo è disponibile nella [documentazione ufficiale](https://reactnative.dev/docs/environment-setup)

### <a name="install-required-packages"></a>Installare i pacchetti necessari

Per il corretto funzionamento di questo esempio sono necessari i tre pacchetti seguenti:

1. [@react-native-community/push-notification-ios](https://www.npmjs.com/package/@react-native-community/push-notification-ios) - [Projects - GitHub](https://github.com/react-native-community/push-notification-ios)

    Questo pacchetto è stato creato quando PushNotificationIOS è stato estratto da React Native. Il pacchetto implementa in modo nativo le notifiche push per iOS e fornisce l'interfaccia di React Native per accedervi. Eseguire il comando seguente per installare il pacchetto:

    ```bash
    yarn add @react-native-community/push-notification-ios
    ```

1. [react-native-push-notification](https://www.npmjs.com/package/react-native-push-notification)

    Questo pacchetto implementa le notifiche locali e remote in iOS e Android in modalità multipiattaforma. Eseguire il comando seguente per installare il pacchetto:

    ```bash
    yarn add react-native-push-notification
    ```

1. [react-native-device-info](https://www.npmjs.com/package/react-native-device-info) Il pacchetto fornisce informazioni su un dispositivo in fase di esecuzione. Usarlo per definire un identificatore di dispositivo, che viene usato per la registrazione per le notifiche push. Eseguire il comando seguente per installare il pacchetto:

    ```bash
    yarn add react-native-device-info
    ```

### <a name="implement-the-cross-platform-components"></a>Implementare i componenti multipiattaforma

1. Creare e implementare `DemoNotificationHandler`:

    ```typescript
    import PushNotification from 'react-native-push-notification';

    class DemoNotificationHandler {
      private _onRegister: any;
      private _onNotification: any;

      onNotification(notification: any) {
        console.log('NotificationHandler:', notification);

        if (typeof this._onNotification === 'function') {
          this._onNotification(notification);
        }
      }

      onRegister(token: any) {
        console.log('NotificationHandler:', token);

        if (typeof this._onRegister === 'function') {
          this._onRegister(token);
        }
      }

      attachTokenReceived(handler: any) {
        this._onRegister = handler;
      }

      attachNotificationReceived(handler: any) {
        this._onNotification = handler;
      }
    }

    const handler = new DemoNotificationHandler();

    PushNotification.configure({
      onRegister: handler.onRegister.bind(handler),
      onNotification: handler.onNotification.bind(handler),
      permissions: {
        alert: true,
        badge: true,
        sound: true,
      },
      popInitialNotification: true,
      requestPermissions: true,
    });

    export default handler;
    ```

1. Creare e implementare `DemoNotificationService`:

    ```typescript
    import PushNotification from 'react-native-push-notification';
    import DemoNotificationHandler from './DemoNotificationHandler';

    export default class DemoNotificationService {
      constructor(onTokenReceived: any, onNotificationReceived: any) {
        DemoNotificationHandler.attachTokenReceived(onTokenReceived);
        DemoNotificationHandler.attachNotificationReceived(onNotificationReceived);
        PushNotification.getApplicationIconBadgeNumber(function(number: number) {
          if(number > 0) {
            PushNotification.setApplicationIconBadgeNumber(0);
          }
        });
      }

      checkPermissions(cbk: any) {
        return PushNotification.checkPermissions(cbk);
      }

      requestPermissions() {
        return PushNotification.requestPermissions();
      }

      cancelNotifications() {
        PushNotification.cancelLocalNotifications();
      }

      cancelAll() {
        PushNotification.cancelAllLocalNotifications();
      }

      abandonPermissions() {
        PushNotification.abandonPermissions();
      }
    }
    ```

1. Creare e implementare `DemoNotificationRegistrationService`:

    ```typescript
    export default class DemoNotificationService {
        constructor(
            readonly apiUrl: string,
            readonly apiKey: string) {
        }

    async registerAsync(request: any): Promise<Response> {
            const method = 'PUT';
            const registerApiUrl = `${this.apiUrl}/notifications/installations`;
            const result = await fetch(registerApiUrl, {
                method: method,
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                    'apiKey': this.apiKey
                },
                body: JSON.stringify(request)
            });

            this.validateResponse(registerApiUrl, method, request, result);
            return result;
        }

        async deregisterAsync(deviceId: string): Promise<Response> {
            const method = 'DELETE';
            const deregisterApiUrl = `${this.apiUrl}/notifications/installations/${deviceId}`;
            const result = await fetch(deregisterApiUrl, {
                method: method,
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                    'apiKey': this.apiKey
                }
            });

            this.validateResponse(deregisterApiUrl, method, null, result);
            return result;
        }

        private validateResponse(requestUrl: string, method: string, requestPayload: any, response: Response) {
            console.log(`Request: ${method} ${requestUrl} => ${JSON.stringify(requestPayload)}\nResponse: ${response.status}`);
            if (!response || response.status != 200) {
                throw `HTTP error ${response.status}: ${response.statusText}`;
            }
        }
    }
    ```

1. Configurare l'app. Aprire `package.json` e aggiungere la definizione di script seguente:

    ```json
    "configure": "cp .app.config.tsx src/config/AppConfig.tsx"
    ```

    Eseguire quindi questo script, che copia la configurazione predefinita nella cartella `config`.

    ```bash
    yarn configure
    ```

    Il passaggio finale consiste nell'aggiornare il file di configurazione copiato nel passaggio precedente con le informazioni di accesso all'API. Specificare i parametri `apiKey` e `apiUrl`:

    ```typescript
    module.exports = {
        appName: "PushDemo",
        env: "production",
        apiUrl: "https://<azure-push-notifications-api-url>/api/",
        apiKey: "<api-auth-key>",
    };
    ```

### <a name="implement-the-cross-platform-ui"></a>Implementare l'interfaccia utente multipiattaforma

1. Definire il layout di pagina

    ```typescript
    <View style={styles.container}>
      {this.state.isBusy &&
        <ActivityIndicator></ActivityIndicator>
      }
      <View style={styles.button}>
        <Button title="Register" onPress={this.onRegisterButtonPress.bind(this)} disabled={this.state.isBusy} />
      </View>
      <View style={styles.button}>
        <Button title="Deregister" onPress={this.onDeregisterButtonPress.bind(this)} disabled={this.state.isBusy} />
      </View>
    </View>
    ```

1. Applicare gli stili

    ```css
    const styles = StyleSheet.create({
      container: {
        flex: 1,
        alignItems: "center",
        justifyContent: 'flex-end',
        margin: 50,
      },
      button: {
        margin: 5,
        width: "100%",
      }
    });
    ```

1. Inizializzare il componente della pagina

    ```typescript
      state: IState;
      notificationService: DemoNotificationService;
      notificationRegistrationService: DemoNotificationRegistrationService;
      deviceId: string;

      constructor(props: any) {
        super(props);
        this.deviceId = DeviceInfo.getUniqueId();
        this.state = {
          status: "Push notifications registration status is unknown",
          registeredOS: "",
          registeredToken: "",
          isRegistered: false,
          isBusy: false,
        };

        this.notificationService = new DemoNotificationService(
          this.onTokenReceived.bind(this),
          this.onNotificationReceived.bind(this),
        );

        this.notificationRegistrationService = new DemoNotificationRegistrationService(
          Config.apiUrl,
          Config.apiKey,
        );
      }
    ```

1. Definire i gestori di clic dei pulsanti

    ```typescript
      async onRegisterButtonPress() {
        if (!this.state.registeredToken || !this.state.registeredOS) {
          Alert.alert("The push notifications token wasn't received.");
          return;
        }

        let status: string = "Registering...";
        let isRegistered = this.state.isRegistered;
        try {
          this.setState({ isBusy: true, status });
          const pnPlatform = this.state.registeredOS == "ios" ? "apns" : "fcm";
          const pnToken = this.state.registeredToken;
          const request = {
            installationId: this.deviceId,
            platform: pnPlatform,
            pushChannel: pnToken,
            tags: []
          };
          const response = await this.notificationRegistrationService.registerAsync(request);
          status = `Registered for ${this.state.registeredOS} push notifications`;
          isRegistered = true;
        } catch (e) {
          status = `Registration failed: ${e}`;
        }
        finally {
          this.setState({ isBusy: false, status, isRegistered });
        }
      }

      async onDeregisterButtonPress() {
        if (!this.notificationService)
          return;

        let status: string = "Deregistering...";
        let isRegistered = this.state.isRegistered;
        try {
          this.setState({ isBusy: true, status });
          await this.notificationRegistrationService.deregisterAsync(this.deviceId);
          status = "Deregistered from push notifications";
          isRegistered = false;
        } catch (e) {
          status = `Deregistration failed: ${e}`;
        }
        finally {
          this.setState({ isBusy: false, status, isRegistered });
        }
      }
    ```

1. Gestire le registrazioni di token ricevute e le notifiche push

    ```typescript
      onTokenReceived(token: any) {
        console.log(`Received a notification token on ${token.os}`);
        this.setState({ registeredToken: token.token, registeredOS: token.os, status: `The push notifications token has been received.` });

        if (this.state.isRegistered && this.state.registeredToken && this.state.registeredOS) {
          this.onRegisterButtonPress();
        }
      }

      onNotificationReceived(notification: any) {
        console.log(`Received a push notification on ${this.state.registeredOS}`);
        this.setState({ status: `Received a push notification...` });

        if (notification.data.message) {
          Alert.alert(AppConfig.appName, `${notification.data.action} action received`);
        }
      }
    };
    ```