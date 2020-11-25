---
title: Connettività dei dispositivi in Azure IoT Central | Microsoft Docs
description: Questo articolo presenta i concetti fondamentali relativi alla connettività dei dispositivi in Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 10/22/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.custom:
- amqp
- mqtt
- device-developer
ms.openlocfilehash: 1a050daa3a4b3ae9be5ef40961c40adaa90dc72b
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96121820"
---
# <a name="get-connected-to-azure-iot-central"></a>Connettersi ad Azure IoT Central

*Le informazioni di questo articolo sono destinate a operatori e sviluppatori di dispositivi.*

Questo articolo descrive come i dispositivi si connettono a un'applicazione IoT Central di Azure. Prima che un dispositivo possa scambiare dati con IoT Central, deve:

- *Eseguire l'autenticazione*. L'autenticazione con l'applicazione IoT Central utilizza un _token di firma di accesso condiviso (SAS)_ o un _certificato X. 509_. Negli ambienti di produzione è consigliabile usare i certificati X.509.
- Eseguire la *registrazione*. I dispositivi devono essere registrati con l'applicazione IoT Central. È possibile visualizzare i dispositivi registrati nella pagina **dispositivi** dell'applicazione.
- *Associa a un modello di dispositivo*. In un'applicazione IoT Central, i modelli di dispositivo definiscono l'interfaccia utente usata dagli operatori per visualizzare e gestire i dispositivi connessi.

IoT Central supporta i due scenari di registrazione dei dispositivi seguenti:

- *Registrazione automatica*. Il dispositivo viene registrato automaticamente alla prima connessione. Questo scenario consente agli OEM di produrre in massa dispositivi che possono connettersi senza prima essere registrati. Un OEM genera le credenziali del dispositivo appropriate e configura i dispositivi nello stabilimento. Facoltativamente, è possibile richiedere a un operatore di approvare il dispositivo prima di iniziare a inviare i dati. Questo scenario richiede la configurazione di una _registrazione del gruppo_ X. 509 o SAS nell'applicazione.
- *Registrazione manuale*. Gli operatori registrano singoli dispositivi nella pagina **dispositivi** oppure [importano un file CSV](howto-manage-devices.md#import-devices) per registrare in blocco i dispositivi. In questo scenario è possibile usare la _registrazione del gruppo_ x. 509 o SAS o la registrazione di un _singolo_ certificato x. 509 o SAS.

I dispositivi che si connettono a IoT Central devono seguire le *convenzioni plug and Play*. Una di queste convenzioni è che un dispositivo deve inviare l' _ID modello_ del modello di dispositivo che implementa quando si connette. L'ID modello consente all'applicazione IoT Central di associare il dispositivo al modello di dispositivo corretto.

IoT Central usa il [servizio Device Provisioning in hub IoT di Azure](../../iot-dps/about-iot-dps.md) per gestire il processo di connessione. Un dispositivo si connette prima a un endpoint DPS per recuperare le informazioni necessarie per la connessione all'applicazione. Internamente, l'applicazione IoT Central usa un hub IoT per gestire la connettività dei dispositivi. Con il servizio Device Provisioning:

- IoT Central supporta l'onboarding e la connessione dei dispositivi su larga scala.
- È possibile generare credenziali dei dispositivi e configurarli offline senza registrarli tramite l'interfaccia utente di IoT Central.
- È possibile usare ID dispositivo personalizzati per registrare i dispositivi in IoT Central. L'uso di ID dispositivo personalizzati semplifica l'integrazione con i sistemi back-office esistenti.
- È disponibile un singolo modo coerente per connettere i dispositivi a IoT Central.

Questo articolo descrive i passaggi di connessione del dispositivo seguenti:

- [Registrazione del gruppo X. 509](#x509-group-enrollment)
- [Registrazione del gruppo SAS](#sas-group-enrollment)
- [Registrazione individuale](#individual-enrollment)
- [Registrazione del dispositivo](#device-registration)
- [Associare un dispositivo a un modello di dispositivo](#associate-a-device-with-a-device-template)

## <a name="x509-group-enrollment"></a>Registrazione del gruppo X. 509

In un ambiente di produzione, l'uso di certificati X.509 è il meccanismo di autenticazione dei dispositivi consigliato per IoT Central. Per altre informazioni, vedere [Autenticazione dei dispositivi con i certificati della CA X.509](../../iot-hub/iot-hub-x509ca-overview.md).

Per connettere un dispositivo con un certificato X. 509 all'applicazione:

1. Creare un *gruppo di registrazioni* che usi il tipo di attestazione **certificati (X. 509)** .
1. Aggiungere e verificare un certificato X. 509 intermedio o radice nel gruppo di registrazioni.
1. Generare un certificato foglia dal certificato radice o intermedio nel gruppo di registrazione. Inviare il certificato foglia dal dispositivo quando si connette all'applicazione.

Per altre informazioni, vedere [come connettere i dispositivi con certificati X. 509](how-to-connect-devices-x509.md)

### <a name="for-testing-purposes-only"></a>Solo a scopo di test

Solo per i test, è possibile usare le utilità seguenti per generare il certificato radice, intermedio e del dispositivo:

- [Strumenti per Azure IoT SDK Device Provisioning Device](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md): una raccolta di strumenti Node.js che è possibile usare per generare e verificare certificati X.509 e chiavi.
- Se si usa un dispositivo DevKit, questo [strumento da riga di comando](https://aka.ms/iotcentral-docs-dicetool) genera un certificato della CA che è possibile aggiungere all'applicazione IoT Central per verificare i certificati.
- [Gestire i certificati della CA di test per esempi ed esercitazioni](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md): una raccolta di script di PowerShell e Bash per:
  - Creare una catena di certificati.
  - Salvare i certificati come file con estensione cer da caricare nell'applicazione IoT Central.
  - Usare il codice di verifica dell'applicazione IoT Central per generare il certificato di verifica.
  - Creare certificati foglia per i dispositivi usando gli ID dispositivo come parametro per lo strumento.

## <a name="sas-group-enrollment"></a>Registrazione del gruppo SAS

Per connettere un dispositivo con la chiave SAS del dispositivo all'applicazione:

1. Creare un *gruppo di registrazioni* che usi il tipo di attestazione della **firma di accesso condiviso (SAS)** .
1. Copiare la chiave primaria o secondaria del gruppo dal gruppo di registrazione.
1. Usare l'interfaccia della riga di comando di Azure per generare una chiave del dispositivo dalla chiave di gruppo:

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. Usare la chiave del dispositivo generata quando il dispositivo si connette all'applicazione IoT Central.

## <a name="individual-enrollment"></a>Registrazione individuale

I clienti che connettono i dispositivi che hanno le proprie credenziali di autenticazione, usano le registrazioni individuali. Una registrazione singola è una voce per un singolo dispositivo a cui è consentita la connessione. Le registrazioni singole possono usare sia certificati foglia X.509 che token di firma di accesso condiviso (da un TPM fisico o virtuale) come meccanismo di attestazione. Un ID dispositivo può contenere lettere, numeri e il carattere `-`. Per altre informazioni, vedere [Registrazione singola DPS](../../iot-dps/concepts-service.md#individual-enrollment).

> [!NOTE]
> Quando si crea una registrazione singola per un dispositivo, essa ha la precedenza sulle opzioni di registrazione di gruppo predefinite nell'applicazione IoT Central.

### <a name="create-individual-enrollments"></a>Creare registrazioni singole

IoT Central supporta i meccanismi di attestazione seguenti per le registrazioni individuali:

- **Attestazione con chiave simmetrica:** L'attestazione con chiave simmetrica costituisce un approccio semplice per autenticare un dispositivo con l'istanza DPS. Per creare una registrazione singola che usa chiavi simmetriche, aprire la pagina **connessione** del dispositivo per il dispositivo, selezionare **registrazione singola** come metodo di connessione e **firma di accesso condiviso (SAS)** come meccanismo. Immettere chiavi primarie e secondarie con codifica Base64 e salvare le modifiche. Usare **Ambito ID**, **ID dispositivo** e la chiave primaria o secondaria per connettere il dispositivo.

    > [!TIP]
    > Per il test, è possibile usare **OpenSSL** per generare chiavi con codifica Base64: `openssl rand -base64 64`

- **Certificati X.509:** Per creare una registrazione singola con certificati X.509, aprire la pagina **Connessione dispositivo**, selezionare **Registrazione singola** come metodo di connessione e **Certificati X.509** come meccanismo. I certificati del dispositivo usati con una voce di registrazione singola hanno un requisito per cui l'autorità di certificazione e l'oggetto CN devono essere impostati sull'ID dispositivo.

    > [!TIP]
    > Per il test, è possibile usare [Strumenti per Azure IoT SDK Device Provisioning Device per Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) per generare un certificato autofirmato: `node create_test_cert.js device "mytestdevice"`

- **Attestazione TPM (Trusted Platform Module):** Un [TPM](../../iot-dps/concepts-tpm-attestation.md) è un tipo di modulo di protezione hardware. L'uso di un TPM è uno dei modi più sicuri per connettere un dispositivo. Questo articolo presuppone che l'utente usi un TPM discreto, firmware o integrato. I TPM emulati dal software sono ideali per la creazione di prototipi o i test, ma non forniscono lo stesso livello di sicurezza di un TPM discreto, firmware o integrato. Non usare TPM software nell'ambiente di produzione. Per creare una registrazione singola che usa un TPM, aprire la pagina **Connessione dispositivo**, selezionare **Registrazione singola** come metodo di connessione e **TPM** come meccanismo. Immettere la chiave di verifica dell'autenticità del TPM e salvare le informazioni di connessione del dispositivo.

## <a name="device-registration"></a>Registrazione del dispositivo

Prima che un dispositivo possa connettersi a un'applicazione IoT Central, deve essere registrato nell'applicazione:

- I dispositivi possono registrarsi automaticamente quando si connettono per la prima volta. Per usare questa opzione, è necessario usare la registrazione del [gruppo X. 509](#x509-group-enrollment) o la [registrazione del gruppo SAS](#sas-group-enrollment).
- Un operatore può importare un file CSV per registrare in blocco un elenco di dispositivi nell'applicazione.
- Un operatore può registrare manualmente un singolo dispositivo nella pagina **dispositivi** dell'applicazione.

IoT Central consente agli OEM di produrre in massa i dispositivi che possono registrarsi automaticamente. Un OEM genera le credenziali del dispositivo appropriate e configura i dispositivi nello stabilimento. Quando un cliente accende un dispositivo per la prima volta, questo si connette a DPS, che quindi connette automaticamente il dispositivo all'applicazione IoT Central corretta. Facoltativamente, è possibile richiedere a un operatore di approvare il dispositivo prima di iniziare a inviare i dati all'applicazione.

> [!TIP]
> Nella pagina **amministrazione > connessione del dispositivo** l'opzione **auto approva** controlla se un operatore deve approvare manualmente il dispositivo prima di poter avviare l'invio dei dati.

### <a name="automatically-register-devices-that-use-x509-certificates"></a>Registrare automaticamente i dispositivi che usano certificati X. 509

1. Generare i certificati foglia per i dispositivi usando il certificato radice o intermedio aggiunto al gruppo di [registrazione X. 509](#x509-group-enrollment). Usare gli ID dispositivo come `CNAME` nei certificati foglia. Un ID dispositivo può contenere lettere, numeri e il carattere `-`.

1. Come OEM, lampeggiare ogni dispositivo con un ID dispositivo, un certificato foglia X. 509 generato e il valore dell' **ambito dell'ID** applicazione. Il codice del dispositivo deve anche inviare l'ID del modello del dispositivo che implementa.

1. Quando si passa a un dispositivo, si connette prima di tutto a DPS per recuperare le informazioni di connessione IoT Central.

1. Il dispositivo usa le informazioni di DPS per connettersi e registrarsi con l'applicazione IoT Central.

L'applicazione IoT Central USA l'ID modello inviato dal dispositivo per [associare il dispositivo registrato a un modello di dispositivo](#associate-a-device-with-a-device-template).

### <a name="automatically-register-devices-that-use-sas-tokens"></a>Registrare automaticamente i dispositivi che usano i token SAS

1. Copiare la chiave primaria del gruppo dal gruppo di registrazione **SAS-Internet-i-dispositivi** :

    :::image type="content" source="media/concepts-get-connected/group-primary-key.png" alt-text="Raggruppare la chiave primaria dal gruppo di registrazione SAS-Internet del dispositivo":::

1. Usare il `az iot central device compute-device-key` comando per generare le chiavi di firma di accesso condiviso del dispositivo. Usare la chiave primaria del gruppo del passaggio precedente. L'ID del dispositivo può contenere lettere, numeri e il `-` carattere:

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. Come OEM, lampeggiare ogni dispositivo con l'ID del dispositivo, la chiave di firma di accesso condiviso del dispositivo generata e il valore dell' **ambito dell'ID** applicazione. Il codice del dispositivo deve anche inviare l'ID del modello del dispositivo che implementa.

1. Quando si accende un dispositivo, questo si connette prima di tutto a DPS per recuperare le informazioni di registrazione IoT Central.

1. Il dispositivo usa le informazioni di DPS per connettersi e registrarsi con l'applicazione IoT Central.

L'applicazione IoT Central USA l'ID modello inviato dal dispositivo per [associare il dispositivo registrato a un modello di dispositivo](#associate-a-device-with-a-device-template).

### <a name="bulk-register-devices-in-advance"></a>Registrare in blocco i dispositivi in anticipo

Per registrare un numero elevato di dispositivi con l'applicazione IoT Central, usare un file CSV per [importare gli ID dispositivo e i nomi di dispositivo](howto-manage-devices.md#import-devices).

Se i dispositivi usano i token di firma di accesso condiviso per l'autenticazione, [esportare un file CSV dall'applicazione IoT Central](howto-manage-devices.md#export-devices). Il file CSV esportato include gli ID dispositivo e le chiavi di firma di accesso condiviso.

Se i dispositivi usano certificati X. 509 per l'autenticazione, generare i certificati foglia X. 509 per i dispositivi usando il certificato radice o intermedio in caricato nel gruppo di registrazione X. 509. Usare gli ID del dispositivo importati come `CNAME` valore nei certificati foglia.

I dispositivi devono usare il valore di **ambito ID** per l'applicazione e inviare un ID modello quando si connettono.

> [!TIP]
> È possibile trovare il valore di **ambito ID** in **Amministrazione > connessione del dispositivo**.

### <a name="register-a-single-device-in-advance"></a>Registrare un singolo dispositivo in anticipo

Questo approccio è utile quando si usano IoT Central o dispositivi di testing. Selezionare **+ nuovo** nella pagina **dispositivi** per registrare un singolo dispositivo. È possibile usare le chiavi di firma di accesso condiviso del dispositivo per connettere il dispositivo all'applicazione IoT Central. Copiare la _chiave di firma di accesso condiviso del dispositivo_ dalle informazioni di connessione per un dispositivo registrato:

![Chiavi di firma di accesso condiviso per un singolo dispositivo](./media/concepts-get-connected/single-device-sas.png)

## <a name="associate-a-device-with-a-device-template"></a>Associare un dispositivo a un modello di dispositivo

IoT Central associa automaticamente un dispositivo a un modello di dispositivo quando si connette il dispositivo. Un dispositivo invia un ID modello quando si connette. IoT Central USA l'ID modello per identificare il modello di dispositivo per il modello di dispositivo specifico. Il processo di individuazione funziona nel modo seguente:

1. Se il modello di dispositivo è già pubblicato nell'applicazione IoT Central, il dispositivo viene associato al modello di dispositivo.
1. Se il modello di dispositivo non è già pubblicato nell'applicazione IoT Central, IoT Central Cerca il modello di dispositivo nel repository del modello pubblico. Se IoT Central trova il modello, lo usa per generare un modello di dispositivo di base.
1. Se IoT Central non trova il modello nel repository del modello pubblico, il dispositivo viene contrassegnato come non **associato**. Un operatore può creare un modello di dispositivo per il dispositivo e quindi eseguire la migrazione del dispositivo non associato al nuovo modello di dispositivo.

## <a name="device-status-values"></a>Valori di stato del dispositivo

Quando un dispositivo reale si connette all'applicazione IoT Central, lo stato del dispositivo viene modificato come segue:

1. Lo stato del dispositivo prima è **Registrato**. Questo stato indica che il dispositivo viene creato in IoT Central e ha un ID dispositivo. Un dispositivo è registrato quando:
    - Un nuovo dispositivo reale viene aggiunto nella pagina **Dispositivi**.
    - Un set di dispositivi viene aggiunto tramite l'opzione **Importa** nella pagina **Dispositivi**.

1. Lo stato del dispositivo passa a **Provisioning** quando il dispositivo che si connette all'applicazione IoT Central con credenziali valide completa il passaggio di provisioning. In questo passaggio, il dispositivo usa DPS per recuperare automaticamente una stringa di connessione dall'hub IoT Central usato dall'applicazione IoT Central. Il dispositivo può ora connettersi a IoT Central e iniziare a inviare dati.

1. Un operatore può bloccare un dispositivo. Quando un dispositivo è bloccato, non può inviare dati all'applicazione IoT Central. I dispositivi bloccati hanno lo stato **Bloccato**. Un operatore deve reimpostare il dispositivo prima di poter riprendere l'invio di dati. Quando un operatore sblocca un dispositivo, lo stato torna al valore precedente: **Registrato** o **Provisioning**.

1. Se lo stato del dispositivo è **In attesa di approvazione**, significa che l'opzione **Approvazione automatica** è disabilitata. Un operatore deve approvare esplicitamente un dispositivo prima di iniziare a inviare dati. I dispositivi non registrati manualmente nella pagina **Dispositivi**, ma connessi con credenziali valide, avranno lo stato del dispositivo **In attesa di approvazione**. Gli operatori possono approvare questi dispositivi dalla pagina **Dispositivi** usando il pulsante **Approva**.

1. Se lo stato del dispositivo è **Non associato**, significa che il dispositivo che si connette a IoT Central non ha un modello di dispositivo associato. Questa situazione si verifica in genere negli scenari seguenti:

    - Viene aggiunto un set di dispositivi usando **Importa** nella pagina **Dispositivi** senza specificare il modello di dispositivo.
    - Un dispositivo è stato registrato manualmente nella pagina **Dispositivi** senza specificare il modello di dispositivo. Il dispositivo è quindi connesso con credenziali valide.  

    L'operatore può associare un dispositivo a un modello di dispositivo dalla pagina **Dispositivi** usando il pulsante **Esegui migrazione**.

## <a name="best-practices"></a>Procedure consigliate

Non salvare in modo permanente o memorizzare nella cache la stringa di connessione del dispositivo che DPS restituisce quando si connette il dispositivo per la prima volta. Per riconnettere un dispositivo, esaminare il flusso di registrazione del dispositivo standard per ottenere la stringa di connessione del dispositivo corretta. Se il dispositivo memorizza nella cache la stringa di connessione, il software del dispositivo rischia di avere una stringa di connessione obsoleta. Se IoT Central aggiorna l'hub delle cose di Azure sottostante usato, un dispositivo con una stringa di connessione non aggiornata non può connettersi.

## <a name="sdk-support"></a>Supporto SDK

Gli Azure IoT SDK offrono il modo più semplice per implementare il codice del dispositivo. Attualmente sono disponibili gli SDK seguenti:

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

### <a name="protocols"></a>Protocolli

Gli SDK di dispositivo supportano i protocolli di rete seguenti per la connessione a un hub IoT:

- MQTT
- AMQP
- HTTPS

Per informazioni sulle differenze fra questi protocolli e indicazioni su come sceglierne uno, vedere [Scegliere un protocollo di comunicazione](../../iot-hub/iot-hub-devguide-protocols.md).

Se il dispositivo non può usare uno dei protocolli supportati, usare Azure IoT Edge per eseguire la conversione del protocollo. IoT Edge supporta altri scenari di intelligence on-the-Edge per l'offload dell'elaborazione dall'applicazione Azure IoT Central.

## <a name="security"></a>Sicurezza

Tutti i dati scambiati tra i dispositivi e Azure IoT Central vengono crittografati. L'hub IoT autentica ogni richiesta da un dispositivo che si connette a qualsiasi endpoint dell'hub IoT a contatto con i dispositivi. Per evitare lo scambio di credenziali nella rete, un dispositivo usa i token firmati per l'autenticazione. Per altre informazioni, vedere [Controllare l'accesso all'hub IoT](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Passaggi successivi

Se si è uno sviluppatore di dispositivi, alcuni passaggi successivi suggeriti sono:

- Esaminare alcuni esempi di codice che illustrano come usare i token SAS in [esercitazione: creare e connettere un'applicazione client all'applicazione IoT Central di Azure](tutorial-connect-device.md)
- Informazioni su come [connettere i dispositivi con certificati X. 509 usando Node.js SDK per dispositivi per IOT Central applicazione](how-to-connect-devices-x509.md)
- Informazioni su come [monitorare la connettività dei dispositivi con l'interfaccia della riga di comando di Azure](./howto-monitor-devices-azure-cli.md)
- Informazioni su come [definire un nuovo tipo di dispositivo IoT nell'applicazione Azure IoT Central](./howto-set-up-template.md)
- Informazioni sui [dispositivi Azure IoT Edge e su Azure IoT Central](./concepts-iot-edge.md)
