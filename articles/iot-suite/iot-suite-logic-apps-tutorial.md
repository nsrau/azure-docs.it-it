<properties
  pageTitle="Azure IoT Suite e App per la logica | Microsoft Azure"
  description="Esercitazione su come associare App per la logica a Azure IoT Suite per un processo aziendale."
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="08/16/2016"
  ms.author="araguila"/>
  
# Esercitazione: Connettere l'app per la logica alla soluzione preconfigurata per il monitoraggio remoto Azure IoT Suite

La soluzione preconfigurata per il monitoraggio remoto [Microsoft Azure IoT Suite][lnk-internetofthings] consente di avviare rapidamente un set di funzionalità end-to-end che esemplifica una soluzione IoT. Questa esercitazione illustra come aggiungere l'app per la logica alla soluzione preconfigurata per il monitoraggio remoto Microsoft Azure IoT Suite. Questi passaggi descrivono come usare la soluzione IoT in modo avanzato collegandola a un processo aziendale.

_Per una descrizione dettagliata dell'esecuzione del provisioning di una soluzione preconfigurata per il monitoraggio remoto, vedere [Esercitazione: Introduzione alle soluzioni preconfigurate][lnk-getstarted]._

Prima di iniziare questa esercitazione, è necessario:

- Eseguire il provisioning della soluzione preconfigurata di monitoraggio remoto nella sottoscrizione di Azure.

- Creare un account SendGrid che consenta di inviare un messaggio di posta elettronica che attivi il processo aziendale. È possibile richiedere un account di valutazione gratuito accedendo al sito Web [SendGrid](https://sendgrid.com/) e facendo clic su **Try for Free** (Prova gratuitamente). Dopo aver eseguito la registrazione per ottenere un account di valutazione gratuito, è necessario creare in SendGrid una [chiave API](https://sendgrid.com/docs/User_Guide/Settings/api_keys.html) che concede le autorizzazioni per l'invio di messaggi di posta elettronica. La chiave API sarà necessaria più avanti nell'esercitazione.

Supponendo di avere già eseguito il provisioning della soluzione preconfigurata per il monitoraggio remoto, passare al gruppo di risorse per la soluzione nel [portale di Azure][lnk-azureportal]. Il nome del gruppo di risorse coincide con quello assegnato alla soluzione per il monitoraggio remoto al momento del provisioning. Nel gruppo di risorse è possibile visualizzare tutte le risorse di Azure con provisioning per la soluzione, ad eccezione dell'applicazione Azure Active Directory disponibile nel portale di Azure classico. La schermata seguente mostra un pannello **Gruppo di risorse** di esempio per una soluzione preconfigurata per il monitoraggio remoto:

![](media/iot-suite-logic-apps-tutorial/resourcegroup.png)

Per iniziare, impostare l'app per la logica da usare con la soluzione preconfigurata.

## Impostare l'app per la logica

1. Nel portale di Azure fare clic su __Aggiungi__ nella parte superiore del pannello Gruppo di risorse.

2. Cercare __App per la logica__, selezionarla e quindi fare clic su **Crea**.

3. Compilare il campo __Nome__ e immettere le stesse informazioni relative ai campi **Sottoscrizione**e **Gruppo di risorse** e usate al momento del provisioning della soluzione per il monitoraggio remoto. Fare clic su __Create__.

    ![](media/iot-suite-logic-apps-tutorial/createlogicapp.png)

4. Dopo il completamento della distribuzione, l'app per la logica viene elencata come risorsa nel gruppo di risorse.

5. Fare clic su App per la logica per passare al pannello App per la logica, selezionare il modello **Blank Logic App** (App per la logica vuota) per aprire **Progettazione app per la logica**.

    ![](media/iot-suite-logic-apps-tutorial/logicappsdesigner.png)

6. Selezionare __Richiesta__. Questa azione specifica che una richiesta HTTP in ingresso con uno specifico payload in formato JSON agisce come trigger.

7. Incollare quanto segue nello schema JSON del corpo della richiesta:

    ```
    {
      "$schema": "http://json-schema.org/draft-04/schema#",
      "id": "/",
      "properties": {
        "DeviceId": {
          "id": "DeviceId",
          "type": "string"
        },
        "measuredValue": {
          "id": "measuredValue",
          "type": "integer"
        },
        "measurementName": {
          "id": "measurementName",
          "type": "string"
        }
      },
      "required": [
        "DeviceId",
        "measurementName",
        "measuredValue"
      ],
      "type": "object"
    }
    ```
    
    Nota: è possibile copiare l'URL per il post HTTP dopo aver salvato l'app per la logica. È tuttavia necessario aggiungere prima un'azione.

8. Fare clic su __+ Nuovo passaggio__ sotto il trigger manuale. Fare quindi clic su **Aggiungi un'azione**.

    ![](media/iot-suite-logic-apps-tutorial/logicappcode.png)

9. Cercare **SendGrid - Send email** (SendGrid - Invia messaggio di posta elettronica) e fare clic.

    ![](media/iot-suite-logic-apps-tutorial/logicappaction.png)

10. Immettere un nome per la connessione, ad esempio **SendGridConnection**, immettere la **chiave API SendGrid** creata durante l'impostazione dell'account SendGrid e fare clic su **Crea**.

    ![](media/iot-suite-logic-apps-tutorial/sendgridconnection.png)

11. Aggiungere i propri indirizzi di posta elettronica nei campi **From** (Da) e **To** (A). Aggiungere **Remote monitoring alert [DeviceId]** (Avviso di monitoraggio remoto [IDdispositivo]) al campo **Subject** (Oggetto). Nel campo **Email Body** (Corpo messaggio di posta elettronica) aggiungere **Device [DeviceId] has reported [measurementName] with value [measuredValue]** (Il dispositivo [IDdispositivo] ha riportato [nomeMisurazione] con valore [valoreMisurato]). È possibile aggiungere **[DeviceId]** ([IDdispositivo]), **[measurementName]** (nomeMisurazione) e **[measuredValue]** ([valoreMisurato]) facendo clic nella sezione **You can insert data from previous steps** (È possibile aggiungere dati dei passaggi precedenti).

    ![](media/iot-suite-logic-apps-tutorial/sendgridaction.png)

12. Fare clic su __Save__ (Salva) nel menu in alto.

13. Fare clic sul trigger **Request** (Richiesta) e copiare il valore __Http Post to this URL__ (HTTP Post a questo URL). Questo URL sarà necessario più avanti nell'esercitazione.

> [AZURE.NOTE] App per la logica consente di eseguire [diversi tipi di azione][lnk-logic-apps-actions], incluse azioni in Office 365.

## Configurare il processo Web EventProcessor

In questa sezione si esegue la connessione della soluzione preconfigurata all'App per la logica creata. Per completare questa attività, aggiungere l'URL per attivare l'App per la logica all'azione che viene generata quando il valore del sensore del dispositivo supera la soglia.

1. Usare il client git per clonare la versione più recente del [repository GitHub azure-iot-remote-monitoring][lnk-rmgithub]. Ad esempio:

    ```
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```

2. In Visual Studio, aprire __RemoteMonitoring.sln__ dalla copia locale del repository.

3. Aprire il file __ActionRepository.cs__ nella cartella **Infrastructure\\Repository**.

4. Aggiornare il dizionario **actionIds** con il valore di __Http Post to this URL__ (HTTP Post a questo URL) di cui si è preso nota nell'app per la logica, come segue:

    ```
    private Dictionary<string,string> actionIds = new Dictionary<string, string>()
    {
        { "Send Message", "<Http Post to this UR>" },
        { "Raise Alarm", "<Http Post to this UR> }
    };
    ```

5. Salvare le modifiche nella soluzione e uscire da Visual Studio.

## Eseguire la distribuzione dalla riga di comando

In questa sezione verrà distribuita la versione aggiornata della soluzione per il monitoraggio remoto per sostituire quella attualmente in esecuzione in Azure.

1. Per istruzioni sull'impostazione dell'ambiente di sviluppo, fare clic [qui][lnk-devsetup].

2.  Per istruzioni sulla distribuzione in locale, fare clic [qui][lnk-localdeploy].

3.  Per istruzioni su come eseguire la distribuzione sul cloud e aggiornare la distribuzione su cloud esistente, fare clic [qui][lnk-clouddeploy]. Come nome della distribuzione usare quello della distribuzione originale. Se, ad esempio, la distribuzione originale era denominata **demologicapp**, usare il comando seguente:

    ``
    build.cmd cloud release demologicapp
    ``
    
    Quando si esegue lo script di compilazione, assicurarsi di usare gli stessi account, sottoscrizione e area di Azure nonché la stessa istanza di Active Directory usati per il provisioning della soluzione.

## App per la logica in azione

La soluzione preconfigurata per il monitoraggio remoto dispone di due regole specificate per impostazione predefinita al provisioning di una soluzione. Entrambe le regole riguardano il dispositivo **SampleDevice001**:

* Temperature > 38.00
* Humidity > 48.00

La regola relativa alla temperatura attiva l'azione **Raise Alarm**, mentre quella relativa all'umidità attiva l'azione **SendMessage**. Supponendo che sia stato usato lo stesso URL per entrambe le azioni della classe **ActionRepository**, l'app per la logica si attiva per entrambe le regole. Entrambe le regole usano SendGrid per inviare un messaggio di posta elettronica all'indirizzo **A** con i dettagli dell'avviso.

> [AZURE.NOTE] L'app per la logica continua ad attivare le regole ogni volta che i valori soglia vengono superati. Per evitare l'invio di messaggi di posta elettronica non necessari, è possibile disabilitare le regole nel portale della soluzione o disabilitare l'app per la logica nel [portale di Azure][lnk-azureportal].

Oltre a ricevere messaggi di posta elettronica, è anche possibile vedere quando l'app per la logica è in esecuzione nel portale:

![](media/iot-suite-logic-apps-tutorial/logicapprun.png)

## Passaggi successivi

Dopo aver usato un'app per la logica per connettere la soluzione preconfigurata a un processo aziendale, è possibile leggere le informazioni sulle opzioni per la personalizzazione delle soluzioni preconfigurate:

- [Usare la telemetria dinamica con la soluzione preconfigurata per il monitoraggio remoto][lnk-dynamic]
- [Metadati di informazioni sul dispositivo nella soluzione preconfigurata per il monitoraggio remoto][lnk-devinfo]

[lnk-dynamic]: iot-suite-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[lnk-internetofthings]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-getstarted]: iot-suite-getstarted-preconfigured-solutions.md
[lnk-azureportal]: https://portal.azure.com
[lnk-logic-apps-actions]: ../connectors/apis-list.md
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devsetup]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/dev-setup.md
[lnk-localdeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/local-deployment.md
[lnk-clouddeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md

<!---HONumber=AcomDC_0817_2016-->