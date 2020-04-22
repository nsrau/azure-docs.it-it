---
title: Connettività dei dispositivi in Azure IoT Central | Microsoft Docs
description: Questo articolo presenta i concetti fondamentali relativi alla connettività dei dispositivi in Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f37b070c74abd8511fc597f9b159312d91281083
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759124"
---
# <a name="get-connected-to-azure-iot-central"></a>Connettersi ad Azure IoT Central

*Questo articolo si applica agli operatori e agli sviluppatori di dispositivi.*

Questo articolo descrive le opzioni per la connessione dei dispositivi a un'applicazione Azure IoT Central.This article describes the options for connecting your devices to an Azure IoT Central application.

In genere, è necessario registrare un dispositivo nell'applicazione prima che possa connettersi. Tuttavia, IoT Central supporta scenari in cui i [dispositivi possono connettersi senza prima essere registrati.](#connect-without-registering-devices)

IoT Central usa il servizio Di provisioning dei [dispositivi hub IoT](../../iot-dps/about-iot-dps.md) di Azure per gestire il processo di connessione. Un dispositivo si connette innanzitutto a un endpoint DPS per recuperare le informazioni necessarie per connettersi all'applicazione. Internamente, l'applicazione IoT Central usa un hub IoT per gestire la connettività dei dispositivi. Con il servizio Device Provisioning:

- IoT Central supporta l'onboarding e la connessione dei dispositivi su larga scala.
- È possibile generare credenziali dei dispositivi e configurarli offline senza registrarli tramite l'interfaccia utente di IoT Central.
- È possibile usare ID dispositivo personalizzati per registrare i dispositivi in IoT Central. L'uso di ID dispositivo personalizzati semplifica l'integrazione con i sistemi back-office esistenti.
- È disponibile un singolo modo coerente per connettere i dispositivi a IoT Central.

Per proteggere la comunicazione tra un dispositivo e l'applicazione, IoT Central supporta sia le firme di accesso condiviso (SAS) che i certificati X.509. I certificati X.509 sono consigliati negli ambienti di produzione.

In questo articolo vengono descritti i seguenti casi d'uso:

- [Connettere un singolo dispositivo tramite la configurazione di accesso sasConnect a single device using SAS](#connect-a-single-device)
- [Connettere dispositivi su larga scala tramite SASConnect devices at scale using SAS](#connect-devices-at-scale-using-sas)
- [Connettere i dispositivi su larga scala utilizzando i certificati X.509:](#connect-devices-using-x509-certificates) l'approccio consigliato per gli ambienti di produzione.
- [Collegare i dispositivi senza prima registrarli](#connect-without-registering-devices)
- [Connettere i dispositivi che usano le registrazioni individuali DPSConnect devices that use DPS individual enrollments](#individual-enrollment-based-device-connectivity)
- [Collegare i dispositivi utilizzando le funzionalità IoT Plug and Play (anteprima)](#connect-devices-with-iot-plug-and-play-preview)

## <a name="connect-a-single-device"></a>Connettere un dispositivo

Questo approccio è utile quando si sperimentano i dispositivi IoT Central o di test. È possibile usare le chiavi di sAS di connessione del dispositivo dall'applicazione IoT Central per connettere un dispositivo all'applicazione IoT Central.You can use the device connection SAS keys from your IoT Central application to connect a device to your IoT Central application. Copiare la _chiave di accesso sas_ del dispositivo dalle informazioni di connessione per un dispositivo registrato:Copy the device SAS key from the connection information for a registered device:

![Chiavi di accesso sAS per un singolo dispositivo](./media/concepts-get-connected/single-device-sas.png)

Per altre informazioni, vedere l'esercitazione [Creare e connettere un'applicazione client Node.js all'applicazione Azure IoT Central.To](./tutorial-connect-device.md) learn more, see the Create and connect a Node.js client application to your Azure IoT Central application tutorial.

## <a name="connect-devices-at-scale-using-sas"></a>Connettere dispositivi su larga scala tramite SASConnect devices at scale using SAS

Per connettere i dispositivi a IoT Central su larga scala usando le chiavi di accesso utenti, è necessario registrare e quindi configurare i dispositivi:To connect devices to IoT Central at scale using SAS keys, you need to register and then set up the devices:

### <a name="register-devices-in-bulk"></a>Registrare i dispositivi in blocco

Per registrare un numero elevato di dispositivi con l'applicazione IoT Central, utilizzare un file CSV per importare ID dispositivo e nomi di [dispositivo.](howto-manage-devices.md#import-devices)

Per recuperare le informazioni di connessione per i dispositivi importati, [esportare un file CSV dall'applicazione IoT Central](howto-manage-devices.md#export-devices). Il file CSV esportato include gli ID dispositivo e le chiavi di firma di accesso utenti.

### <a name="set-up-your-devices"></a>Configurare i dispositivi

Usare le informazioni di connessione del file di esportazione nel codice del dispositivo per consentire ai dispositivi di connettersi e inviare dati all'IoT all'applicazione IoT Central. È inoltre necessario **l'ambito ID** DPS per l'applicazione. Questo valore è disponibili in **Amministrazione > Connessione dispositivo**.

> [!NOTE]
> Per informazioni su come connettere i dispositivi senza prima registrarli in IoT Central, consultate [Connessione senza prima registrare i dispositivi.](#connect-without-registering-devices)

## <a name="connect-devices-using-x509-certificates"></a>Connettere i dispositivi usando i certificati X.509Connect devices using X.509 certificates

In a production environment, using X.509 certificates is the recommended device authentication mechanism for IoT Central. Per altre informazioni, vedere [Autenticazione dei dispositivi con i certificati della CA X.509](../../iot-hub/iot-hub-x509ca-overview.md).

Prima di connettere un dispositivo a un certificato X.509, aggiungere e verificare un certificato X.509 intermedio o radice nell'applicazione. I dispositivi devono utilizzare certificati X.509 foglia generati dal certificato radice o intermedio.

### <a name="add-and-verify-a-root-or-intermediate-certificate"></a>Aggiungere e verificare un certificato radice o intermedioAdd and verify a root or intermediate certificate

Passare ad **Amministrazione > Connessione dispositivo > Gestisci certificato primario** e aggiungere il certificato radice o intermedio X.509 utilizzato per generare i certificati del dispositivo.

![Impostazioni di connessione](media/concepts-get-connected/manage-x509-certificate.png)

La verifica della proprietà del certificato garantisce che la persona che carica il certificato disponga della chiave privata del certificato. Per verificare il certificato:

  1. Seleziona il pulsante accanto a **Codice di verifica** per generare un codice.
  1. Creare un certificato di verifica X.509 con il codice di verifica generato nel passaggio precedente. Salvare il certificato come file CER.
  1. Caricare il certificato di verifica firmato e selezionare **Verifica**. Il certificato viene contrassegnato come **verificato** quando la verifica ha esito positivo.

Se si verifica una violazione della sicurezza o il certificato primario è impostato per la scadenza, utilizzare il certificato secondario per ridurre i tempi di inattività. È possibile continuare a eseguire il provisioning dei dispositivi usando il certificato secondario durante l'aggiornamento del certificato primario.

### <a name="register-and-connect-devices"></a>Registrare e collegare i dispositivi

Per connettere in blocco i dispositivi utilizzando certificati X.509, registrare innanzitutto i dispositivi nell'applicazione, utilizzando un file CSV per importare gli ID e i nomi dei [dispositivi.](howto-manage-devices.md#import-devices) Gli ID del dispositivo devono essere tutti minuscoli.

Generare certificati foglia X.509 per i dispositivi utilizzando il certificato radice o intermedio caricato. Utilizzare **l'ID** `CNAME` dispositivo come valore nei certificati foglia. Il codice del dispositivo richiede il valore **dell'ambito ID** per l'applicazione, l'ID **del dispositivo**e il certificato del dispositivo corrispondente.

### <a name="for-testing-purposes-only"></a>Solo a scopo di test

Solo per i test, è possibile utilizzare le utilità seguenti per generare certificati radice, intermedi e del dispositivo:For only, you can use the following utilities to generate root, intermediate, and device certificates:

- [Strumenti per Azure IoT Device Provisioning Device SDK:](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md)una raccolta di strumenti Node.js che è possibile usare per generare e verificare chiavi e certificati X.509.Tools for the Azure IoT Device Provisioning Device SDK : a collection of Node.js tools that you can use to generate and verify X.509 certificates and keys.
- Se si usa un dispositivo DevKit, questo [strumento della riga](https://aka.ms/iotcentral-docs-dicetool) di comando genera un certificato CA che è possibile aggiungere all'applicazione IoT Central per verificare i certificati.
- Gestire i certificati CA di [test per esempi ed esercitazioni:](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)una raccolta di script PowerShell e Bash per:Manage test CA certificates for samples and tutorials : a collection of PowerShell and Bash scripts to:
  - Creare una catena di certificati.
  - Salvare i certificati come file con estensione cer da caricare nell'applicazione IoT Central.
  - Usare il codice di verifica dell'applicazione IoT Central per generare il certificato di verifica.
  - Creare certificati foglia per i dispositivi usando gli ID dispositivo come parametro per lo strumento.

### <a name="further-reference"></a>Altre informazioni di riferimento

- [Esempio di implementazione per RaspberryPi](https://aka.ms/iotcentral-docs-Raspi-releases)
- [Client del dispositivo di esempio in CSample device client in C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

## <a name="connect-without-registering-devices"></a>Connetti senza registrare i dispositivi

Gli scenari descritti in precedenza richiedono tutti di registrare i dispositivi nell'applicazione prima che si connettano. IoT Central consente inoltre agli OEM di produrre in serie dispositivi in grado di connettersi senza prima essere registrati. Un OEM genera credenziali di dispositivo adatte e configura i dispositivi in fabbrica. Quando un cliente accende un dispositivo per la prima volta, si connette a DPS, che quindi connette automaticamente il dispositivo all'applicazione IoT Central corretta. Un operatore IoT Central deve approvare il dispositivo prima di iniziare a inviare dati all'applicazione.

Il flusso è leggermente diverso a seconda che i dispositivi utilizzino token di firma di accesso condiviso o certificati X.509:The flow is slightly different depending on whether the devices use SAS tokens or X.509 certificates:

### <a name="connect-devices-that-use-sas-tokens-without-registering"></a>Connettere i dispositivi che usano i token di firma di accesso condiviso senza eseguire la registrazioneConnect devices that use SAS tokens without registering

1. Copiare la chiave primaria del gruppo dell'applicazione IoT Central:

    ![Chiave di accesso livello di accesso primario del gruppo di applicazioniApplication group primary SAS key](media/concepts-get-connected/group-sas-keys.png)

1. Usare lo strumento [dps-keygen](https://www.npmjs.com/package/dps-keygen) per generare le chiavi di accesso sAS del dispositivo. Utilizzare la chiave primaria del gruppo del passaggio precedente. Gli ID dispositivo devono essere minuscoli:

    ```cmd
    dps-keygen -mk:<group primary key> -di:<device ID>
    ```

1. L'OEM lampeggia ogni dispositivo con un ID dispositivo, una chiave di accesso sas del dispositivo generato e il valore **dell'ambito dell'ID** applicazione.

1. Quando si accende un dispositivo, questo si connette prima a DPS per recuperare le informazioni di registrazione di IoT Central.When you switch on a device, it first connects to DPS to retrieve its IoT Central registration information.

    Il dispositivo ha inizialmente uno stato del dispositivo **Non associato** nella pagina **Dispositivi** e non è assegnato a un modello di dispositivo. Nella pagina **Dispositivi** **eseguire la migrazione** del dispositivo al modello di dispositivo appropriato. Il provisioning del dispositivo è stato completato, lo stato del dispositivo è ora **sottoposto**a provisioning e il dispositivo può iniziare a inviare dati.

    Nella pagina **Amministrazione > connessione dispositivo,** l'opzione **Approva automaticamente** controlla se è necessario approvare manualmente il dispositivo prima che possa iniziare a inviare i dati.

    > [!NOTE]
    > Per informazioni su come associare automaticamente un dispositivo a un modello di dispositivo, consultate [Collegare i dispositivi con Plug and Play IoT (anteprima).](#connect-devices-with-iot-plug-and-play-preview)

### <a name="connect-devices-that-use-x509-certificates-without-registering"></a>Connettere i dispositivi che utilizzano certificati X.509 senza registrarsi

1. [Aggiungere e verificare un certificato X.509 radice o intermedio](#connect-devices-using-x509-certificates) nell'applicazione IoT Central.Add and verify a root or intermediate X.509 certificate to your IoT Central application. (#connect-devices-using-x509-certificati)

1. Generare i certificati foglia per i dispositivi usando il certificato radice o intermedio aggiunto all'applicazione IoT Central.Generate the leaf-certificates for your devices using the root or intermediate certificate you added to your IoT Central application. Utilizzare gli ID dispositivo con `CNAME` lettere minuscole come nei certificati foglia.

1. L'OEM lampeggia ogni dispositivo con un ID dispositivo, un certificato X.509 a sinistra generato e il valore **dell'ambito dell'ID** applicazione.

1. Quando si accende un dispositivo, questo si connette prima a DPS per recuperare le informazioni di registrazione di IoT Central.When you switch on a device, it first connects to DPS to retrieve its IoT Central registration information.

    Il dispositivo ha inizialmente uno stato del dispositivo **Non associato** nella pagina **Dispositivi** e non è assegnato a un modello di dispositivo. Nella pagina **Dispositivi** **eseguire la migrazione** del dispositivo al modello di dispositivo appropriato. Il provisioning del dispositivo è stato completato, lo stato del dispositivo è ora **sottoposto**a provisioning e il dispositivo può iniziare a inviare dati.

    Nella pagina **Amministrazione > connessione dispositivo,** l'opzione **Approva automaticamente** controlla se è necessario approvare manualmente il dispositivo prima che possa iniziare a inviare i dati.

    > [!NOTE]
    > Per informazioni su come associare automaticamente un dispositivo a un modello di dispositivo, consultate [Collegare i dispositivi con Plug and Play IoT (anteprima).](#connect-devices-with-iot-plug-and-play-preview)

## <a name="individual-enrollment-based-device-connectivity"></a>Connettività dei dispositivi basata sulla registrazione singola

Per i clienti che connettono dispositivi che dispongono di credenziali di autenticazione personalizzate, usare le registrazioni individuali. Una singola registrazione è una voce per un singolo dispositivo a cui è consentito connettersi. Le singole registrazioni possono usare i certificati foglia X.509 o i token di firma di accesso condiviso (da un modulo della piattaforma attendibile o virtuale) come meccanismi di attestazione. L'ID dispositivo (noto anche come ID di registrazione) in una singola registrazione è alfanumerico, minuscolo e può contenere trattini. Per ulteriori informazioni, consultate [Registrazione singola DPS.](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment)

> [!NOTE]
> Quando si crea una singola registrazione per un dispositivo, ha la precedenza sulle opzioni di registrazione di gruppo predefinite nell'applicazione IoT Central.When you create an individual enrollment for a device, it takes precedence over the default group enrollment options in your IoT Central application.

### <a name="creating-individual-enrollments"></a>Creazione di singole iscrizioni

IoT Central supporta i seguenti meccanismi di attestazione per le singole registrazioni:

- **Attestazione a chiave simmetrica:** L'attestazione a chiave simmetrica è un approccio semplice per l'autenticazione di un dispositivo con l'istanza DPS. Per creare una singola registrazione che usa chiavi simmetriche, aprire la pagina **Connessione dispositivo,** selezionare **Registrazione singola** come metodo di connessione e Firma di accesso **condiviso (SAS)** come meccanismo. Immettere le chiavi primarie e secondarie con codifica base64 e salvare le modifiche. Utilizzare **l'ambito ID**, **l'ID dispositivo**e la chiave primaria o secondaria per connettere il dispositivo.

    > [!TIP]
    > Per il test, è possibile usare OpenSSL per generare chiavi codificate in base64:For testing, you can use **OpenSSL** to generate base64 encoded keys:`openssl rand -base64 64`

- **Certificati X.509:** Per creare una singola registrazione con certificati X.509, aprire la pagina **Connessione dispositivo,** selezionare **Registrazione singola** come metodo di connessione e **Certificati (X.509)** come meccanismo. I certificati del dispositivo usati con una singola voce di registrazione hanno il requisito che l'autorità emittente e il CN soggetto siano impostati sull'ID dispositivo.

    > [!TIP]
    > Per i test, è possibile usare gli strumenti per l'SDK del dispositivo di provisioning dei dispositivi di Azure IoT per Node.js per generare un certificato autofirmato:For testing, you can use [Tools for the Azure IoT Device Provisioning Device SDK for Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) to generate a self-signed certificate:`node create_test_cert.js device "mytestdevice"`

- **Attestazione TPM (Trusted Platform Module):** Un [TPM](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation) è un tipo di modulo di sicurezza hardware. L'utilizzo di un TPM è uno dei modi più sicuri per connettere un dispositivo. In questo articolo si presuppone che si stia utilizzando un TPM discreto, firmware o integrato. I TPM emulati dal software sono adatti per la prototipazione o il test, ma non forniscono lo stesso livello di sicurezza dei TPM discreti, firmware o integrati. Non utilizzare TPM software nell'ambiente di produzione. Per creare una singola registrazione che utilizza un TPM, aprire la pagina **Connessione dispositivo,** selezionare **Registrazione singola** come metodo di connessione e **TPM** come meccanismo. Immettere la chiave di approvazione del TPM e salvare le informazioni di connessione del dispositivo.

## <a name="connect-devices-with-iot-plug-and-play-preview"></a>Collegare i dispositivi con IoT Plug and Play (anteprima)

Una delle caratteristiche principali di IoT Plug and Play (anteprima) con IoT Central è la possibilità di associare automaticamente i modelli di dispositivo alla connessione del dispositivo. Insieme alle credenziali del dispositivo, i dispositivi possono ora inviare **il CapabilityModelId** come parte della chiamata di registrazione del dispositivo. Questa funzionalità consente a IoT Central di individuare e associare il modello di dispositivo al dispositivo. Il processo di individuazione funziona come segue:The discovery process works as follows:

1. Viene associato al modello di dispositivo se è già pubblicato nell'applicazione IoT Central.Associates with the device template if it's already published in the IoT Central application.
1. Recupera dall'archivio pubblico dei modelli di funzionalità pubblicati e certificati.

Di seguito è riportato il formato del payload aggiuntivo che il dispositivo invierebbe durante la chiamata di registrazione DPS

```javascript
'__iot:interfaces': {
    CapabilityModelId: <this is the URN for the capability model>
}
```

> [!NOTE]
> Si noti che l'opzione **Approva automaticamente** in **Amministrazione > connessione dispositivo** deve essere abilitata affinché i dispositivi si connettano automaticamente, rilevino il modello di dispositivo e inizino a inviare dati.

## <a name="device-status-values"></a>Valori di stato del dispositivo

Quando un dispositivo reale si connette all'applicazione IoT Central, il relativo stato cambia come segue:

1. Lo stato del dispositivo è prima **Registrato**. Questo stato indica che il dispositivo è stato creato in IoT Central e ha un ID dispositivo. Un dispositivo viene registrato quando:
    - Nella pagina **Dispositivi** viene aggiunto un nuovo dispositivo reale.
    - Un set di dispositivi viene aggiunto utilizzando **Importa** nella pagina **Dispositivi.**

1. Lo stato del dispositivo diventa **Provisioned** quando il dispositivo connesso all'applicazione IoT Central con credenziali valide completa il passaggio di provisioning. In questo passaggio, il dispositivo usa DPS per recuperare automaticamente una stringa di connessione dall'hub IoT usato dall'applicazione IoT Central.In this step, the device uses DPS to automatically retrieve a connection string from the IoT Hub used by your IoT Central application. Il dispositivo può ora connettersi a IoT Central e iniziare a inviare dati.

1. Un operatore può bloccare un dispositivo. Quando un dispositivo è bloccato, non può inviare dati all'applicazione IoT Central.When a device is blocked, it can't send data to your IoT Central application. I dispositivi bloccati hanno lo stato **Bloccato**. Un operatore deve reimpostare il dispositivo prima di poter riprendere l'invio dei dati. Quando un operatore sblocca un dispositivo, lo stato torna al valore precedente, **Registrato** o **Provisioning .**

1. Se lo stato del dispositivo è **In attesa di approvazione**, significa che l'opzione **Auto approve (Approva automaticamente)** è disattivata. Un operatore deve approvare in modo esplicito un dispositivo prima di avviare l'invio di dati. I dispositivi non registrati manualmente nella pagina **Dispositivi,** ma connessi con credenziali valide, avranno lo stato del dispositivo **In attesa di approvazione.** Gli operatori possono approvare questi dispositivi dalla pagina **Dispositivi** utilizzando il pulsante **Approva.**

1. Se lo stato del dispositivo è **Non associato**, significa che al dispositivo che si connette a IoT Central non è associato un modello di dispositivo. Questa situazione si verifica in genere nei seguenti scenari:

    - Un set di dispositivi viene aggiunto utilizzando **Importa** nella pagina **Dispositivi** senza specificare il modello di dispositivo.
    - Un dispositivo è stato registrato manualmente nella pagina **Dispositivi** senza specificare il modello di dispositivo. Il dispositivo si è quindi connesso con credenziali valide.  

    L'operatore può associare un dispositivo a un modello di dispositivo dalla pagina **Dispositivi** utilizzando il pulsante **Esegui migrazione.**

## <a name="best-practices"></a>Procedure consigliate

Non mantenere persistente o memorizzare nella cache la stringa di connessione del dispositivo restituita da DPS quando si connette il dispositivo per la prima volta. Per riconnettere un dispositivo, passare attraverso il flusso di registrazione del dispositivo standard per ottenere la stringa di connessione del dispositivo corretta. Se il dispositivo memorizza nella cache la stringa di connessione, il software del dispositivo incontra il rischio di avere una stringa di connessione non aggiornata se IoT Central aggiorna l'hub IoT di Azure sottostante che usa.

## <a name="sdk-support"></a>Supporto SDK

Gli SDK per dispositivi di Azure offrono il modo più semplice per implementare il codice del dispositivo. Attualmente sono disponibili gli SDK seguenti:

- [Azure IoT SDK per C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK per Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT SDK per Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT SDK per Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK per .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>Funzioni dell'SDK e connettività dell'hub IoT

Tutte le comunicazioni dei dispositivi con l'hub IoT usano le opzioni di connettività dell'hub IoT seguenti:

- [Messaggistica da dispositivo a cloud](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Dispositivi gemelli](../../iot-hub/iot-hub-devguide-device-twins.md)

La tabella seguente riepiloga le corrispondenze tra le funzionalità di dispositivo di Azure IoT Central e le funzionalità dell'hub IoT:

| Azure IoT Central | Hub IoT Azure |
| ----------- | ------- |
| Telemetria | Messaggistica da dispositivo a cloud |
| Proprietà | Proprietà segnalate del dispositivo gemello |
| Proprietà (scrivibile) | Proprietà desiderate e segnalate nel dispositivo gemello |
| Comando | Metodi diretti |

Per altre informazioni sull'uso degli SDK per dispositivi, vedere [Connettere un dispositivo kit DevDiv all'applicazione Azure IoT Central](howto-connect-devkit.md) per esempio di codice.

### <a name="protocols"></a>Protocolli

Gli SDK di dispositivo supportano i protocolli di rete seguenti per la connessione a un hub IoT:

- MQTT
- AMQP
- HTTPS

Per informazioni sulle differenze fra questi protocolli e indicazioni su come sceglierne uno, vedere [Scegliere un protocollo di comunicazione](../../iot-hub/iot-hub-devguide-protocols.md).

Se il dispositivo non può usare i protocolli supportati, è possibile usare Azure IoT Edge per la conversione del protocollo. IoT Edge supporta altri scenari di intelligence di perimetro per scaricare l'elaborazione sul perimetro dall'applicazione Azure IoT Central.

## <a name="security"></a>Sicurezza

Tutti i dati scambiati tra i dispositivi e Azure IoT Central vengono crittografati. L'hub IoT autentica ogni richiesta da un dispositivo che si connette a qualsiasi endpoint dell'hub IoT a contatto con i dispositivi. Per evitare lo scambio di credenziali nella rete, un dispositivo usa i token firmati per l'autenticazione. Per altre informazioni, vedere [Controllo dell'accesso all'hub IoT](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Passaggi successivi

Se sei uno sviluppatore di dispositivi, alcuni passaggi successivi suggeriti sono:If you're a device developer, some suggested next steps are to:

- Informazioni su come [monitorare la connettività dei dispositivi usando l'interfaccia della riga di comando di AzureLearn](./howto-monitor-devices-azure-cli.md) how to Monitor device connectivity using Azure CLI
- Informazioni su come definire un nuovo tipo di [dispositivo IoT nell'applicazione Azure IoT Central](./howto-set-up-template.md)
- Informazioni sui [dispositivi Azure IoT Edge e su Azure IoT Central](./concepts-iot-edge.md)
