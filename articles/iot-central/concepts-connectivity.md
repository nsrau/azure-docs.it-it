---
title: Connettività dei dispositivi in Azure IoT Central | Microsoft Docs
description: Questo articolo presenta i concetti fondamentali relativi alla connettività dei dispositivi in Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: ae57fc5366e1ed99febcd9a9d08e7f95f3bbf196
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55487354"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Connettività dei dispositivi in Azure IoT Central

Questo articolo presenta i concetti fondamentali relativi alla connettività dei dispositivi in Microsoft Azure IoT Central.

Azure IoT Central usa il [servizio Device Provisioning in hub IoT di Azure](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) grazie al quale è in grado di supportare l'onboarding e la connessione di dispositivi su larga scala.

-   I clienti possono ora generare le credenziali dei dispositivi e configurarli offline senza la necessità di registrarli prima in IoT Central
-   IoT Central supporta la connessione dei dispositivi con la connettività basata su certificato X509 consigliata nel settore pur continuando a supportare e migliorare la connettività con firme di accesso condiviso
-   I clienti di IoT Central possono ora usare gli ID dei propri dispositivi per registrarli in IoT Central, per una facile integrazione con i sistemi back office esistenti
-   Esiste un modo coerente per connettere i dispositivi a IoT Central 

>[!NOTE]
>IoT Central usa il servizio Device Provisioning di Azure IoT nel livello sottostante per la registrazione e la connessione di tutti i dispositivi. [Altre informazioni sul servizio Device Provisioning](https://docs.microsoft.com/azure/iot-dps/about-iot-dps).

In base al proprio caso d'uso, seguire le istruzioni per connettere i dispositivi a IoT Central
1. [Connettere rapidamente un dispositivo (usando firme di accesso condiviso)](#connect-a-single-device)
1. [Connettere dispositivi su larga scala usando firme di accesso condiviso](#connect-devices-at-scale-using-shared-access-signatures)
1. [Connettere dispositivi su larga scala usando certificati X509](#connect-devices-using-x509-certificates) **consigliato per carichi di lavoro di produzione**
1. [Connettere i dispositivi senza prima registrarli](#connect-without-first-registering-devices) 


>[!NOTE]
>L'endpoint globale per la connessione e il provisioning dei dispositivi è **global.azure-devices-provisioning.net**.

## <a name="connect-a-single-device"></a>Connettere un dispositivo
La connessione di un singolo dispositivo a IoT Central usando firme di accesso condiviso è facile e richiede solo pochi passaggi 
1. Aggiungere un **dispositivo reale** da Device Explorer, fare clic su **+Nuovo > Reale** per aggiungere un dispositivo reale.
    * Immettere l'ID dispositivo **<span style="color:Red">(in lettere minuscole)</span>** o usare l'ID dispositivo suggerito.
    * Immettere il nome del dispositivo o usare il nome suggerito   
    ![Aggiungere un dispositivo](media/concepts-connectivity/add-device.png)
1. Per ottenere i dettagli della connessione, ad esempio **ID ambito, ID dispositivo e chiave primaria** per il dispositivo aggiunto, fare clic su **Connetti** nella pagina del dispositivo.
    * **[ID ambito](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope)** dipende dall'app IoT Central e viene generato dal servizio Device Provisioning. Viene usato per garantire un ID dispositivo univoco all'interno dell'app.
    * **ID dispositivo** è l'ID dispositivo univoco per ogni app. Il dispositivo deve inviare l'ID dispositivo nella chiamata di registrazione.   
    * **Chiave primaria** è un token di firma di accesso condiviso e viene generata da IoT Central per il dispositivo specifico. 
    ![Dettagli di connessione](media/concepts-connectivity/device-connect.PNG)
1. Usare questi dettagli di connessione, **identità del dispositivo, nome del dispositivo e chiave primaria del dispositivo**, nel codice del dispositivo per il provisioning e la connessione del dispositivo e per iniziare a vedere istantaneamente il flusso di dati. Se si usa il dispositivo MxChip, seguire [queste istruzioni dettagliate](howto-connect-devkit.md#add-a-real-device) iniziando dalla sezione **Preparare il dispositivo DevKit**.   

    Di seguito sono riportati i riferimenti per altri linguaggi che è possibile usare.

    *   **Linguaggio C:** se si usa C, seguire [questo client di dispositivo di esempio C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md) per connettere un dispositivo di esempio. Nell'esempio usare le impostazioni seguenti.   

         ```c
         hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;

         ## Enter the Device Id and Symmetric keys 
         prov_dev_set_symmetric_key_info("<Device Id>", "<Enter Primary Symmetric key here>");
        ```

    *   **Node.js:**  se si vuole usare Node.js [seguire queste istruzioni dettagliate](tutorial-add-device.md#prepare-the-client-code) iniziando dalla sezione **Preparare il codice client**.



## <a name="connect-devices-at-scale-using-shared-access-signatures"></a>Connettere dispositivi su larga scala usando firme di accesso condiviso

Per connettere a IoT Central dispositivi su larga scala usando firme di accesso condiviso sono necessari due passaggi 
1. **Registrare i dispositivi** importandoli in IoT Central tramite un file CSV ed esportarli con i dettagli di connessione da usare per connetterli
1. **Configurazione del dispositivo** Il dispositivo viene programmato con i dettagli di connessione (**ID ambito, ID dispositivo e chiave primaria**) in modo che all'accensione esegua automaticamente la chiamata al servizio di provisioning per ottenere le informazioni di connessione o l'assegnazione dell'app IoT Central.

>[!NOTE]
>È disponibile anche un'opzione avanzata che consente di connettere i dispositivi senza doverli prima registrare in IoT Central. [Altre informazioni](https://docs.microsoft.com/azure/iot-dps/about-iot-dps).

**Registrare i dispositivi**

Per connettere un numero elevato di dispositivi all'applicazione, Azure IoT Central consente l'importazione in blocco dei dispositivi tramite un file CSV. 

Requisiti del file CSV: Il file CSV deve avere le colonne (e intestazioni) seguenti
1.  IOTC_DeviceID **<span style="color:Red">(lettere minuscole)</span>**
1.  IOTC_DeviceName (facoltativo)



Importare i dispositivi per registrarli nell'applicazione
1.  Selezionare **Explorer** nel menu di spostamento a sinistra.
1.  Nel riquadro di sinistra scegliere il modello di dispositivo per il quale si vuole creare in blocco i dispositivi. 
1.  Fare clic su **Importa**, selezionare il file CSV contenente l'elenco di ID dispositivo da importare.
Il file CSV deve avere le colonne (e intestazioni) seguenti
    *   IOTC_DeviceID **<span style="color:Red">(lettere minuscole)</span>**
    *   IOTC_DeviceName (facoltativo)
1.  Al termine dell'importazione viene visualizzato un messaggio di operazione riuscita nella griglia di dispositivi.

Esportare i dispositivi per ottenere i dettagli di connessione. L'operazione di esportazione crea un file CSV con ID del dispositivo, nome del dispositivo e chiave del dispositivo. Usare questi dettagli per connettere il dispositivo a IoT Central.
Per esportare in blocco i dispositivi dall'applicazione:
1.  Selezionare **Explorer** nel menu di spostamento a sinistra.
1.  Selezionare i dispositivi che si desidera esportare, quindi scegliere sull'azione **Esportare**.
1.  Dopo aver completato l'esportazione, viene visualizzato un messaggio di operazione completata insieme a un collegamento per scaricare il file generato.
1.  Fare clic sul messaggio di operazione completata per scaricare il file in una cartella locale sul disco.
1.  Il file CSV esportato conterrà le seguenti colonne contenenti informazioni: **Id dispositivo, nome del dispositivo, chiave del dispositivo primaria/secondaria e identificazioni personali del certificato primarie/secondarie**
    *   IOTC_DEVICEID
    *   IOTC_DEVICENAME
    *   IOTC_SASKEY_PRIMARY
    *   IOTC_SASKEY_SECONDARY
    *   IOTC_X509THUMBPRINT_PRIMARY 
    *   IOTC_X509THUMBPRINT_SECONDARY


**Configurazione del dispositivo**

Usare questi dettagli di connessione, **identità del dispositivo (IOTC_DEVICEID), chiave primaria del dispositivo (IOTC_SASKEY_PRIMARY) e ID ambito**, nel codice del dispositivo per il provisioning e la connessione del dispositivo. Se non è ancora stato fatto, ottenere l'**ID ambito** dall'app IoT Central: **Amministrazione > Connessione dispositivo > ID ambito**.
Se per connettersi si usa il dispositivo **MxChip**, seguire [queste istruzioni dettagliate](howto-connect-devkit.md#add-a-real-device) iniziando dalla sezione **Preparare il dispositivo DevKit**.   

Di seguito sono riportati i riferimenti per altri linguaggi che è possibile usare.

   *   **Linguaggio C:** se si usa C, seguire [questo client di dispositivo di esempio C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md) per connettere un dispositivo di esempio. Nell'esempio usare le impostazioni seguenti.   
         ```c
         hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;

         ## Enter the Device Id and Symmetric keys 
         prov_dev_set_symmetric_key_info("<Device Id>", "<Enter Primary Symmetric key here>");
        ```
    * **Node.js:**  se si vuole usare Node.js [seguire queste istruzioni dettagliate](tutorial-add-device.md#prepare-the-client-code) iniziando dalla sezione **Preparare il codice client**.


## <a name="connect-devices-using-x509-certificates"></a>Connettere dispositivi usando certificati X509

L'uso di certificati X.509 come meccanismo di attestazione è un ottimo modo per aumentare la **produzione** e semplificare il provisioning dei dispositivi. I certificati X.509 sono in genere disposti in una catena di certificati in cui ogni certificato è firmato tramite la chiave privata del successivo certificato superiore e così via, terminando in un certificato radice autofirmato. Questo comportamento stabilisce una catena delegata di certificati dal certificato radice generato da un'autorità di certificazione radice attendibile via via attraverso ogni autorità di certificazione intermedia e fino al certificato "foglia" dell'entità finale installato in un dispositivo. Per altre informazioni, vedere [Autenticazione dei dispositivi con i certificati della CA X.509](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

Per connettere dispositivi a IoT Central usando i certificati X509 sono necessari tre passaggi 
1. **Configurare le impostazioni di connessione** nell'app IoT Central aggiungendo/verificando il certificato radice/intermedio X509 usato per generare i certificati del dispositivo.  Per configurare le impostazioni di connessione per i certificati X509 sono necessari due passaggi.  

    *   **Aggiungere il certificato radice o intermedio X509** che viene usato per generare i certificati foglia per il dispositivo. Passare ad Amministrazione > Connessione dispositivo > Certificati. 
    
        ![Impostazioni di connessione](media/concepts-connectivity/connection-settings.PNG)
    *   **Verifica del certificato:** la verifica della proprietà del certificato garantisce che l'autore del caricamento del certificato sia in possesso della chiave privata del certificato. Per verificare il certificato
        *  Generare il codice di verifica facendo clic sul pulsante accanto al campo Codice di verifica. 
        *  Creare un certificato di verifica X.509 con il codice di verifica e salvare il certificato come file con estensione cer. 
        *  Caricare il certificato di verifica firmato e fare clic su Verifica.

        ![Impostazioni di connessione](media/concepts-connectivity/verify-cert.png)
    *   **Certificato secondario:** Durante il ciclo di vita di una soluzione IoT, è necessario rinnovare i certificati. Due dei principali motivi del rinnovo sono costituiti da una possibile violazione della sicurezza e dalle scadenze dei certificati. I certificati secondari vengono usati per ridurre i tempi di inattività per i dispositivi che tentano il provisioning mentre viene aggiornato il certificato primario.

    **SOLO A SCOPO DI TEST** 
    
    Di seguito sono riportati alcuni strumenti di utilità da riga di comando che è possibile usare per generare certificati della CA e certificati del dispositivo.

    * Se si usa MxChip, provare questo [strumento da riga di comando](https://aka.ms/iotcentral-docs-dicetool) per generare certificati della CA, aggiungerli all'app IoT Central e verificarli. 

    *   Usare questo [strumento da riga di comando](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) per
        * Creare la catena di certificati (seguire il passaggio 2 nella documentazione di GitHub). 
         Salvare i certificati come file con estensione cer e caricarli in IoT Central (primario).   
        * Ottenere il codice di verifica dall'app IoT Central, generare il certificato (seguire il passaggio 3 nella documentazione di GitHub) e caricarlo per la verifica. 
        * Creare certificati foglia con l'ID dispositivo come parametro per lo strumento (seguire il passaggio 4). Salvare il certificato e usarlo nel dispositivo.     

1. **Registrare i dispositivi** importandoli in IoT Central tramite un file CSV.

1. **Configurazione del dispositivo**: generare i certificati foglia usando il certificato radice caricato. Assicurarsi di usare l'**ID dispositivo** come CNAME nei certificati foglia e che sia in **lettere minuscole**. Questo [strumento da riga di comando](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) consente di generare certificati foglia/dispositivo **SOLO A SCOPO DI TEST**.

    Programmare il dispositivo con le informazioni del servizio di provisioning per ottenere all'accensione i dettagli di connessione e l'assegnazione dell'app IoT Central.    

    **Altre informazioni di riferimento** 
    *   Implementazione di esempio per [RaspberryPi](https://aka.ms/iotcentral-docs-Raspi-releases).  

    *   [Client di dispositivo di esempio in C](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md).

>[!NOTE]
>Quando vengono generati i certificati foglia per i dispositivi, usare l'**ID dispositivo** come cname.

>[!NOTE]
>L'**ID dispositivo** deve essere in lettere minuscole 
 
## <a name="connect-without-first-registering-devices"></a>Connettere i dispositivi senza prima registrarli
Uno degli scenari chiave abilitati da IoT Central è la produzione di dispositivi su larga scala da parte degli OEM, la generazione delle credenziali e la configurazione dei dispositivi nell'ambiente di produzione senza la necessità di registrarli prima in IoT Central. Dopo l'accensione e la connessione del dispositivo a IoT Central, l'operatore ne approva la connessione all'app IoT Central.

Di seguito è riportato il flusso per la connessione dei dispositivi con questa funzionalità

![Impostazioni di connessione](media/concepts-connectivity/device-connection-flow.PNG)


Seguire i passaggi in base alla scelta dello schema di autenticazione dei dispositivi (X509/firma di accesso condiviso)

1. **Impostazioni di connessione** 
    * **Certificati X509:** [aggiungere e verificare il certificato radice/intermedio](#connect-devices-using-x509-certificates) e usarlo per generare i certificati del dispositivo nel passaggio successivo.
    * **Firma di accesso condiviso:** copiare la chiave primaria, ovvero la chiave di firma di accesso condiviso di gruppo per questa applicazione IoT Central, e usarla per generare le chiavi di firma di accesso condiviso del dispositivo nel passaggio successivo. 
![Firma di accesso condiviso per le impostazioni di connessione](media/concepts-connectivity/connection-settings-sas.png)

1. **Generare le credenziali del dispositivo** 
    *   **Certificati X.509:** generare i certificati foglia per i dispositivi usando il certificato radice/intermedio aggiunto all'app. Assicurarsi di usare l'**ID dispositivo** come cname nei certificati foglia in **<span style="color:Red">lettere minuscole</span>**. Questo [strumento da riga di comando](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) consente di generare certificati foglia/dispositivo a scopo di test.
    *   **Firma di accesso condiviso:** le chiavi di firma di accesso condiviso del dispositivo possono essere generate usando questo [strumento da riga di comando](https://www.npmjs.com/package/dps-keygen). Usare la chiave di firma di accesso condiviso primaria (chiave di firma di accesso condiviso di gruppo) del passaggio precedente. Verificare che l'ID dispositivo **<span style="color:Red">sia in lettere minuscole</span>**.

        Usare le istruzioni seguenti per generare la chiave di firma di accesso condiviso del dispositivo           

        ```
        npm i -g dps-keygen
        ```
    
        **Utilizzo**
                        
        ```
        dps-keygen <Primary_Key(GroupSAS)> <device_id>
        ```

1. **Configurazione del dispositivo** 
    
     Eseguire il flashing del dispositivo con **ID ambito, ID dispositivo, chiave certificato/firma di accesso condiviso del dispositivo** e quindi accenderlo per connetterlo all'app IoT Central.

1. **Connettere il dispositivo a IoT Central:** dopo l'accensione, i dispositivi si connettono al servizio Device Provisioning/IoT Central per la registrazione.

1. **Associare il dispositivo a un modello:** il dispositivo connesso verrà visualizzato tra i dispositivi **Non associati** in **Device Explorer**. Lo stato di provisioning del dispositivo è **Registrato**. **Associare** il dispositivo al modello appropriato e approvarne la connessione all'app IoT Central. Il dispositivo ottiene i dettagli di connessione per l'app IoT Central, si connette e inizia a inviare i dati. Il provisioning del dispositivo è stato completato e lo *Stato di provisioning* cambia in **Provisioning eseguito**.

## <a name="device-provisioning-status"></a>Stato di provisioning del dispositivo
La connessione di un dispositivo reale ad Azure IoT Central prevede una serie di passaggi 
1. **Registrato**: il dispositivo viene prima **Registrato**, ovvero viene creato in IoT Central e ottiene un ID.
Il dispositivo è registrato quando  
    *   Un nuovo dispositivo reale viene aggiunto in **Explorer**
    *   Un set di dispositivi viene aggiunto tramite l'opzione **Importa** in **Explorer**
    *   Non è stato registrato, ma si connette con credenziali valide ed è visibile tra i dispositivi **Non associati**. 

    In tutti questi casi lo *Stato di Provisioning* è **Registrato**

1. **Provisioning eseguito**: il passaggio successivo consiste nella connessione del dispositivo con credenziali valide. IoT Central completa il passaggio di provisioning creando il dispositivo nell'hub IoT. Restituisce quindi la stringa di connessione al dispositivo per consentire la connessione e l'invio di dati. Lo *Stato di provisioning* del dispositivo ora cambia da **Registrato** in **Provisioning eseguito**.

1.  **Bloccato**: l'operatore può bloccare un dispositivo. Dopo che è stato bloccato, un dispositivo non può inviare dati a IoT Central e dovrà essere reimpostato. Per i dispositivi bloccati, lo *Stato di provisioning* è **Bloccato**. L'operatore può anche sbloccare il dispositivo. Dopo che il dispositivo è stato sbloccato, lo *Stato di provisioning* torna allo *Stato di provisioning* precedente (Registrato o Provisioning eseguito). 

## <a name="getting-device-connection-string"></a>Ottenere la stringa di connessione del dispositivo
È possibile ottenere la stringa di connessione del dispositivo all'hub IoT di Azure usando la procedura seguente 
1. Ottenere i dettagli di connessione, ad esempio **ID ambito, ID dispositivo, chiave primaria del dispositivo** dalla pagina del dispositivo (passare alla pagina del dispositivo > fare clic su Connetti) 

    ![Dettagli della connessione](media/concepts-connectivity/device-connect.PNG)

1. Ottenere la stringa di connessione del dispositivo usando lo strumento da riga di comando seguente.
    Usare le istruzioni riportate di seguito per ottenere la stringa di connessione del dispositivo  

    ```cmd/sh
    npm i -g dps-keygen
    ```
    **Utilizzo**

    Per creare una stringa di connessione, il file binario è disponibile nella cartella bin/
    ```cmd/sh
    dps_cstr <scope_id> <device_id> <Primary Key(for device)>
    ```
    Altre informazioni sullo [strumento dps-keygen sono disponibili qui](https://www.npmjs.com/package/dps-keygen).

## <a name="sdk-support"></a>Supporto SDK

Gli SDK di dispositivo di Azure rappresentano il modo più semplice per implementare il codice nei dispositivi che si connettono all'applicazione Azure IoT Central. Attualmente sono disponibili gli SDK seguenti:

- [Azure IoT SDK per C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK per Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT SDK per Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT SDK per Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK per .NET](https://github.com/azure/azure-iot-sdk-csharp)

Ogni dispositivo si connette usando una stringa di connessione univoca che identifica il dispositivo stesso. Un dispositivo può connettersi solo all'hub IoT in cui è registrato. Quando si crea un dispositivo reale nell'applicazione Azure IoT Central, l'applicazione genera una stringa di connessione da usare.

## <a name="sdk-features-and-iot-hub-connectivity"></a>Funzioni dell'SDK e connettività dell'hub IoT

Tutte le comunicazioni dei dispositivi con l'hub IoT usano le opzioni di connettività dell'hub IoT seguenti:

- [Messaggistica da dispositivo a cloud](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)
- [Dispositivi gemelli](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)

La tabella seguente riepiloga le corrispondenze tra le funzionalità di dispositivo di Azure IoT Central e le funzionalità dell'hub IoT:

| Azure IoT Central | Hub IoT Azure |
| ----------- | ------- |
| Misura: Telemetria | Messaggistica da dispositivo a cloud |
| Proprietà dei dispositivi | Proprietà segnalate del dispositivo gemello |
| Impostazioni | Proprietà desiderate e segnalate nel dispositivo gemello |

Per altre informazioni sull'uso degli SDK di dispositivo, vedere uno degli articoli seguenti con esempi di codice:

- [Connettere un'applicazione client Node.js generica all'applicazione Azure IoT Central](howto-connect-nodejs.md)
- [Connettere un dispositivo Raspberry Pi all'applicazione Azure IoT Central](howto-connect-raspberry-pi-python.md)
- [Connettere un dispositivo DevDiv kit all'applicazione Azure IoT Central](howto-connect-devkit.md).


## <a name="protocols"></a>Protocolli

Gli SDK di dispositivo supportano i protocolli di rete seguenti per la connessione a un hub IoT:

- MQTT
- AMQP
- HTTPS

Per informazioni sulle differenze fra questi protocolli e indicazioni su come sceglierne uno, vedere [Scegliere un protocollo di comunicazione](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols).

Se il dispositivo non può usare i protocolli supportati, è possibile usare Azure IoT Edge per la conversione del protocollo. IoT Edge supporta altri scenari di intelligence di perimetro per scaricare l'elaborazione sul perimetro dall'applicazione Azure IoT Central.

## <a name="security"></a>Security

Tutti i dati scambiati tra i dispositivi e Azure IoT Central vengono crittografati. L'hub IoT autentica ogni richiesta da un dispositivo che si connette a qualsiasi endpoint dell'hub IoT a contatto con i dispositivi. Per evitare lo scambio di credenziali nella rete, un dispositivo usa i token firmati per l'autenticazione. Per altre informazioni, vedere [Controllare l'accesso all'hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security).

> [!NOTE]
> Attualmente, i dispositivi che si connettono ad Azure IoT Central devono usare i token di firma di accesso condiviso. I certificati X.509 non sono supportati per i dispositivi che si connettono ad Azure IoT Central.

## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce la connettività dei dispositivi in Azure IoT Central, ecco i passaggi successivi suggeriti:

- [Preparare e connettere un dispositivo DevKit](howto-connect-devkit.md)
- [Preparare e connettere un dispositivo Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Connettere un'applicazione client Node.js generica all'applicazione Azure IoT Central](howto-connect-nodejs.md)
