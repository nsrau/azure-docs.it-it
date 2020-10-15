---
title: Connettività dei dispositivi in Azure IoT Central | Microsoft Docs
description: Questo articolo presenta i concetti fondamentali relativi alla connettività dei dispositivi in Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.custom:
- amqp
- mqtt
- device-developer
ms.openlocfilehash: f39efcbfe7f0094e9481049a1678dba8a045888f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91714215"
---
# <a name="get-connected-to-azure-iot-central"></a>Connettersi ad Azure IoT Central

*Le informazioni di questo articolo sono destinate a operatori e sviluppatori di dispositivi.*

Questo articolo descrive le opzioni per la connessione dei dispositivi a un'applicazione di Azure IoT Central.

In genere, è necessario registrare un dispositivo nell'applicazione perché questa possa connettersi. Tuttavia, IoT Central supporta scenari in cui [i dispositivi possono connettersi senza prima essere registrati](#connect-without-registering-devices).

IoT Central usa il [servizio Device Provisioning in hub IoT di Azure](../../iot-dps/about-iot-dps.md) per gestire il processo di connessione. Un dispositivo si connette prima a un endpoint DPS per recuperare le informazioni necessarie per la connessione all'applicazione. Internamente, l'applicazione IoT Central usa un hub IoT per gestire la connettività dei dispositivi. Con il servizio Device Provisioning:

- IoT Central supporta l'onboarding e la connessione dei dispositivi su larga scala.
- È possibile generare credenziali dei dispositivi e configurarli offline senza registrarli tramite l'interfaccia utente di IoT Central.
- È possibile usare ID dispositivo personalizzati per registrare i dispositivi in IoT Central. L'uso di ID dispositivo personalizzati semplifica l'integrazione con i sistemi back-office esistenti.
- È disponibile un singolo modo coerente per connettere i dispositivi a IoT Central.

Per proteggere la comunicazione tra un dispositivo e l'applicazione, IoT Central supporta sia le firme di accesso condiviso (SAS) che i certificati X.509. Negli ambienti di produzione è consigliabile usare i certificati X.509.

Questo articolo descrive i casi d'uso seguenti:

- [Connettere un singolo dispositivo usando la firma di accesso condiviso](#connect-a-single-device)
- [Connettere dispositivi su larga scala usando la firma di accesso condiviso](#connect-devices-at-scale-using-sas)
- [Connettere dispositivi su larga scala usando certificati X.509](#connect-devices-using-x509-certificates): l'approccio consigliato per gli ambienti di produzione
- [Connettere dispositivi senza prima registrarli](#connect-without-registering-devices)
- [Connettere dispositivi che usano le registrazioni singole DPS](#individual-enrollment-based-device-connectivity)
- [Associare automaticamente un dispositivo a un modello di dispositivo](#automatically-associate-with-a-device-template)

## <a name="connect-a-single-device"></a>Connettere un dispositivo

Questo approccio è utile quando si usano IoT Central o dispositivi di testing. È possibile usare le chiavi di firma di accesso condiviso del dispositivo dall'applicazione IoT Central per connettere un dispositivo all'applicazione IoT Central. Copiare la _chiave di firma di accesso condiviso del dispositivo_ dalle informazioni di connessione per un dispositivo registrato:

![Chiavi di firma di accesso condiviso per un singolo dispositivo](./media/concepts-get-connected/single-device-sas.png)

Per altre informazioni, vedere l'esercitazione [Creare e connettere un'applicazione client Node.js all'applicazione Azure IoT Central](./tutorial-connect-device-nodejs.md).

## <a name="connect-devices-at-scale-using-sas"></a>Connettere dispositivi su larga scala usando la firma di accesso condiviso

Per connettere dispositivi a IoT Central su larga scala usando chiavi di firma di accesso condiviso, è necessario registrare e quindi configurare i dispositivi:

### <a name="register-devices-in-bulk"></a>Registrare dispositivi in blocco

Per registrare un numero elevato di dispositivi con l'applicazione IoT Central, usare un file CSV per [importare gli ID dispositivo e i nomi di dispositivo](howto-manage-devices.md#import-devices).

Per recuperare le informazioni di connessione per i dispositivi importati, [esportare un file in formato CSV dall'applicazione IoT Central](howto-manage-devices.md#export-devices). Il file CSV esportato include gli ID dispositivo e le chiavi di firma di accesso condiviso.

### <a name="set-up-your-devices"></a>Configurare i dispositivi

Usare le informazioni di connessione del file di esportazione nel codice del dispositivo per consentire ai dispositivi di connettersi e inviare dati a IoT all'applicazione IoT Central. Per l'applicazione, è necessario anche l'**ambito ID** del DPS. È possibile trovare questo valore in **Amministrazione > Connessione dispositivo**.

> [!NOTE]
> Per informazioni su come connettere i dispositivi senza prima registrarli in IoT Central, vedere [Connettere i dispositivi senza prima registrarli](#connect-without-registering-devices).

## <a name="connect-devices-using-x509-certificates"></a>Connettere dispositivi usando certificati X.509

In un ambiente di produzione, l'uso di certificati X.509 è il meccanismo di autenticazione dei dispositivi consigliato per IoT Central. Per altre informazioni, vedere [Autenticazione dei dispositivi con i certificati della CA X.509](../../iot-hub/iot-hub-x509ca-overview.md).

Per connettere un dispositivo con un certificato X. 509 all'applicazione:

1. Creare un *gruppo di registrazioni* che usi il tipo di attestazione **certificati (X. 509)** .
2. Aggiungere e verificare un certificato X. 509 intermedio o radice nel gruppo di registrazioni.
3. Registrare e connettere i dispositivi che usano i certificati X. 509 foglia generati dal certificato radice o intermedio nel gruppo di registrazione.

### <a name="create-an-enrollment-group"></a>Creare un gruppo di registrazione

Un [gruppo di registrazioni](../../iot-dps/concepts-service.md#enrollment) è un gruppo di dispositivi che condividono lo stesso tipo di attestazione. I due tipi di attestazione supportati sono certificati X. 509 e SAS:

- In un gruppo di registrazione X. 509 tutti i dispositivi che si connettono a IoT Central usano i certificati X. 509 foglia generati dal certificato radice o intermedio nel gruppo di registrazioni.
- In un gruppo di registrazione SAS tutti i dispositivi che si connettono a IoT Central usano un token di firma di accesso condiviso generato dal token SAS nel gruppo di registrazioni.

I due gruppi di registrazioni predefiniti in ogni applicazione IoT Central sono gruppi di registrazione SAS, uno per i dispositivi Internet e uno per i dispositivi Azure IoT Edge. Per creare un gruppo di registrazione X. 509, passare alla pagina **connessione del dispositivo** e selezionare **+ Aggiungi gruppo di registrazione**:

:::image type="content" source="media/concepts-get-connected/add-enrollment-group.png" alt-text="Schermata di aggiunta di un gruppo di registrazioni X. 509":::

### <a name="add-and-verify-a-root-or-intermediate-x509-certificate"></a>Aggiungere e verificare un certificato X. 509 radice o intermedio

Per aggiungere e verificare un certificato radice o intermedio al gruppo di registrazione:

1. Passare al gruppo di registrazione X. 509 appena creato. È possibile aggiungere certificati X. 509 primari e secondari. Selezionare **+ Gestisci primario**.

1. Nella **pagina certificato primario**caricare il certificato X. 509 primario. Si tratta del certificato radice o intermedio:

    :::image type="content" source="media/concepts-get-connected/upload-primary-certificate.png" alt-text="Schermata di aggiunta di un gruppo di registrazioni X. 509":::

1. Fare clic su **genera codice di verifica** per generare un codice per lo strumento che si sta utilizzando per generare il certificato di verifica. Quindi selezionare **Verifica** per caricare il certificato di verifica.

1. Quando la verifica ha esito positivo, viene visualizzata la conferma seguente:

    :::image type="content" source="media/concepts-get-connected/verified-primary-certificate.png" alt-text="Schermata di aggiunta di un gruppo di registrazioni X. 509":::

La verifica della proprietà del certificato garantisce che l'autore del caricamento del certificato sia in possesso della chiave privata del certificato.

Se si verifica una violazione della sicurezza o se il certificato primario è impostato in modo da scadere, usare il certificato secondario per ridurre il tempo di inattività. È possibile continuare a eseguire il provisioning dei dispositivi usando il certificato secondario durante l'aggiornamento del certificato primario.

### <a name="register-and-connect-devices"></a>Registrare e connettere dispositivi

Per connettere in blocco i dispositivi con certificati X. 509, registrare prima i dispositivi nell'applicazione usando un file CSV per [importare gli ID dispositivo e i nomi dei dispositivi](howto-manage-devices.md#import-devices). Tutti gli ID dispositivo devono essere specificati in minuscolo.

Generare certificati foglia X. 509 per i dispositivi usando il certificato radice o intermedio caricato nel gruppo di registrazione X. 509. Usare l'**ID dispositivo** come valore `CNAME` nei certificati foglia. Il codice del dispositivo richiede il valore **Ambito ID** per l'applicazione, l'**ID dispositivo** e il certificato del dispositivo corrispondente.

#### <a name="sample-device-code"></a>Esempio di codice dispositivo

L'esempio seguente da [Azure IoT SDK Node.JS](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/device/samples/register_x509.js) illustra come un client dispositivo Node.js usa un certificato foglia X.509 e DPS per la registrazione con un'applicazione IoT Central:

:::code language="nodejs" source="~/azure-iot-sdk-node/provisioning/device/samples/register_x509.js":::

Per un esempio C equivalente, vedere [prov_dev_client_sample.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c) in [Azure IoT SDK C Provisioning Device Client](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md).

### <a name="for-testing-purposes-only"></a>Solo a scopo di test

Solo per i test, è possibile usare le utilità seguenti per generare il certificato radice, intermedio e del dispositivo:

- [Strumenti per Azure IoT SDK Device Provisioning Device](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md): una raccolta di strumenti Node.js che è possibile usare per generare e verificare certificati X.509 e chiavi.
- Se si usa un dispositivo DevKit, questo [strumento da riga di comando](https://aka.ms/iotcentral-docs-dicetool) genera un certificato della CA che è possibile aggiungere all'applicazione IoT Central per verificare i certificati.
- [Gestire i certificati della CA di test per esempi ed esercitazioni](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md): una raccolta di script di PowerShell e Bash per:
  - Creare una catena di certificati.
  - Salvare i certificati come file con estensione cer da caricare nell'applicazione IoT Central.
  - Usare il codice di verifica dell'applicazione IoT Central per generare il certificato di verifica.
  - Creare certificati foglia per i dispositivi usando gli ID dispositivo come parametro per lo strumento.

## <a name="connect-without-registering-devices"></a>Connettere i dispositivi senza registrarli

Tutti gli scenari descritti in precedenza richiedono la registrazione dei dispositivi nell'applicazione prima della connessione. IoT Central consente inoltre agli OEM di produrre in massa dispositivi che possono connettersi senza prima essere registrati. Un OEM genera le credenziali del dispositivo appropriate e configura i dispositivi nello stabilimento. Quando un cliente accende un dispositivo per la prima volta, questo si connette a DPS, che quindi connette automaticamente il dispositivo all'applicazione IoT Central corretta. Un operatore IoT Central deve approvare il dispositivo prima che esso inizi a inviare i dati all'applicazione.

Il flusso è leggermente diverso a seconda che i dispositivi usino token di firma di accesso condiviso o certificati X.509:

### <a name="connect-devices-that-use-sas-tokens-without-registering"></a>Connettere dispositivi che usano token di firma di accesso condiviso senza registrazione

1. Copiare la chiave primaria del gruppo dal gruppo di registrazione **SAS-Internet-i-dispositivi** :

    :::image type="content" source="media/concepts-get-connected/group-primary-key.png" alt-text="Schermata di aggiunta di un gruppo di registrazioni X. 509":::

1. Usare il `az iot central device compute-device-key` comando per generare le chiavi di firma di accesso condiviso del dispositivo. Usare la chiave primaria del gruppo del passaggio precedente. Gli ID dispositivo devono essere in minuscolo:

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. L'OEM esegue il flash di ogni dispositivo con un ID dispositivo, una chiave di firma di accesso condiviso del dispositivo generata e il valore **Ambito ID** dell'applicazione.

1. Quando si accende un dispositivo, questo si connette prima di tutto a DPS per recuperare le informazioni di registrazione IoT Central.

    Il dispositivo ha inizialmente uno stato del dispositivo **Non associato** nella pagina **Dispositivi** e non è assegnato a un modello di dispositivo. Nella pagina **Dispositivi** **migrare** il dispositivo al modello di dispositivo appropriato. Il provisioning del dispositivo è ora completo, lo stato del dispositivo ora è **Provisioning** e il dispositivo può iniziare a inviare dati.

    Nella pagina **Amministrazione > Connessione dispositivo** l'opzione **Approvazione automatica** controlla se è necessario approvare manualmente il dispositivo prima che possa iniziare a inviare i dati.

    > [!NOTE]
    > Per informazioni su come associare automaticamente un dispositivo a un modello di dispositivo, vedere [Associare automaticamente un dispositivo a un modello di dispositivo](#automatically-associate-with-a-device-template).

### <a name="connect-devices-that-use-x509-certificates-without-registering"></a>Connettere dispositivi che usano certificati X.509 senza registrazione

1. [Creare un gruppo di registrazione](#create-an-enrollment-group) e quindi [aggiungere e verificare un certificato X. 509 radice o intermedio](#add-and-verify-a-root-or-intermediate-x509-certificate) per l'applicazione IoT Central.

1. Generare i certificati foglia per i dispositivi usando il certificato radice o intermedio aggiunto all'applicazione IoT Central. Usare gli ID dispositivo in minuscolo come `CNAME` nei certificati foglia.

1. L'OEM esegue il flash di ogni dispositivo con un ID dispositivo, un certificato X.509 foglia generato e il valore **Ambito ID** dell'applicazione.

1. Quando si accende un dispositivo, questo si connette prima di tutto a DPS per recuperare le informazioni di registrazione IoT Central.

    Il dispositivo ha inizialmente uno stato del dispositivo **Non associato** nella pagina **Dispositivi** e non è assegnato a un modello di dispositivo. Nella pagina **Dispositivi** **migrare** il dispositivo al modello di dispositivo appropriato. Il provisioning del dispositivo è ora completo, lo stato del dispositivo ora è **Provisioning** e il dispositivo può iniziare a inviare dati.

    Nella pagina **Amministrazione > Connessione dispositivo** l'opzione **Approvazione automatica** controlla se è necessario approvare manualmente il dispositivo prima che possa iniziare a inviare i dati.

    > [!NOTE]
    > Per informazioni su come associare automaticamente un dispositivo a un modello di dispositivo, vedere [Associare automaticamente un dispositivo a un modello di dispositivo](#automatically-associate-with-a-device-template).

## <a name="individual-enrollment-based-device-connectivity"></a>Connettività dei dispositivi basata sulla registrazione singola

Per i clienti che connettono dispositivi che dispongono ognuno delle proprie credenziali di autenticazione, usare le registrazioni singole. Una registrazione singola è una voce per un singolo dispositivo a cui è consentita la registrazione. Le registrazioni singole possono usare sia certificati foglia X.509 che token di firma di accesso condiviso (da un TPM fisico o virtuale) come meccanismo di attestazione. L'ID dispositivo (definito anche ID di registrazione) in una registrazione singola è alfanumerico, con caratteri minuscoli e può contenere trattini. Per altre informazioni, vedere [Registrazione singola DPS](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment).

> [!NOTE]
> Quando si crea una registrazione singola per un dispositivo, essa ha la precedenza sulle opzioni di registrazione di gruppo predefinite nell'applicazione IoT Central.

### <a name="create-individual-enrollments"></a>Creare registrazioni singole

IoT Central supporta i meccanismi di attestazione seguenti per le registrazioni individuali:

- **Attestazione con chiave simmetrica:** L'attestazione con chiave simmetrica costituisce un approccio semplice per autenticare un dispositivo con l'istanza DPS. Per creare una registrazione singola che usa chiavi simmetriche, aprire la pagina **Connessione dispositivo**, selezionare **Registrazione singola** come metodo di connessione e **Firma di accesso condiviso (SAS)** come meccanismo. Immettere chiavi primarie e secondarie con codifica Base64 e salvare le modifiche. Usare **Ambito ID**, **ID dispositivo** e la chiave primaria o secondaria per connettere il dispositivo.

    > [!TIP]
    > Per il test, è possibile usare **OpenSSL** per generare chiavi con codifica Base64: `openssl rand -base64 64`

- **Certificati X.509:** Per creare una registrazione singola con certificati X.509, aprire la pagina **Connessione dispositivo**, selezionare **Registrazione singola** come metodo di connessione e **Certificati X.509** come meccanismo. I certificati del dispositivo usati con una voce di registrazione singola hanno un requisito per cui l'autorità di certificazione e l'oggetto CN devono essere impostati sull'ID dispositivo.

    > [!TIP]
    > Per il test, è possibile usare [Strumenti per Azure IoT SDK Device Provisioning Device per Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) per generare un certificato autofirmato: `node create_test_cert.js device "mytestdevice"`

- **Attestazione TPM (Trusted Platform Module):** Un [TPM](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation) è un tipo di modulo di protezione hardware. L'uso di un TPM è uno dei modi più sicuri per connettere un dispositivo. Questo articolo presuppone che l'utente usi un TPM discreto, firmware o integrato. I TPM emulati dal software sono ideali per la creazione di prototipi o i test, ma non forniscono lo stesso livello di sicurezza di un TPM discreto, firmware o integrato. Non usare TPM software nell'ambiente di produzione. Per creare una registrazione singola che usa un TPM, aprire la pagina **Connessione dispositivo**, selezionare **Registrazione singola** come metodo di connessione e **TPM** come meccanismo. Immettere la chiave di verifica dell'autenticità del TPM e salvare le informazioni di connessione del dispositivo.

## <a name="automatically-associate-with-a-device-template"></a>Associare automaticamente a un modello di dispositivo

Una delle funzionalità principali di IoT Central è la possibilità di associare automaticamente modelli di dispositivo alla connessione del dispositivo. Insieme alle credenziali del dispositivo, i dispositivi possono inviare un **CapabilityModelId** come parte della chiamata di registrazione del dispositivo. Il **CapabilityModelID** è un URN che identifica il modello di funzionalità implementato dal dispositivo. L'applicazione IoT Central può usare il **CapabilityModelID** per identificare il modello di dispositivo da usare e quindi associare automaticamente il dispositivo al modello di dispositivo. Il processo di individuazione funziona nel modo seguente:

1. Se il modello di dispositivo è già pubblicato nell'applicazione IoT Central, il dispositivo viene associato al modello di dispositivo.
1. Per i dispositivi Plug and Play IoT con pre-certificazione, se il modello di dispositivo non è già stato pubblicato nell'applicazione IoT Central, il modello di dispositivo viene recuperato dal repository pubblico.

I frammenti di codice seguenti mostrano il formato del payload aggiuntivo che il dispositivo deve inviare durante la chiamata di registrazione DPS per il funzionamento dell'associazione automatica.

Si tratta del formato per i dispositivi che usano l'SDK per dispositivi disponibile a livello generale:

```javascript
    iotcModelId: '< this is the URN for the capability model>';
```

Questo è il formato per i dispositivi che usano Public Preview SDK per dispositivi:

```javascript
'__iot:interfaces': {
    CapabilityModelId: <this is the URN for the capability model>
}
```

> [!NOTE]
> L'opzione **Approvazione automatica** in **Amministrazione > Connessione dispositivo** deve essere abilitata per la connessione automatica dei dispositivi, per individuare il modello di dispositivo e per iniziare a inviare dati.

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

Non salvare in modo permanente o memorizzare nella cache la stringa di connessione del dispositivo che DPS restituisce quando si connette il dispositivo per la prima volta. Per riconnettere un dispositivo, esaminare il flusso di registrazione del dispositivo standard per ottenere la stringa di connessione del dispositivo corretta. Se il dispositivo memorizza nella cache la stringa di connessione, il software del dispositivo rischia di avere una stringa di connessione non aggiornata se IoT Central aggiorna l'hub IoT di Azure sottostante.

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

Se il dispositivo non può usare i protocolli supportati, è possibile usare Azure IoT Edge per la conversione del protocollo. IoT Edge supporta altri scenari di intelligence di perimetro per scaricare l'elaborazione sul perimetro dall'applicazione Azure IoT Central.

## <a name="security"></a>Sicurezza

Tutti i dati scambiati tra i dispositivi e Azure IoT Central vengono crittografati. L'hub IoT autentica ogni richiesta da un dispositivo che si connette a qualsiasi endpoint dell'hub IoT a contatto con i dispositivi. Per evitare lo scambio di credenziali nella rete, un dispositivo usa i token firmati per l'autenticazione. Per altre informazioni, vedere [Controllare l'accesso all'hub IoT](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Passaggi successivi

Se si è uno sviluppatore di dispositivi, alcuni passaggi successivi suggeriti sono:

- Informazioni su come [monitorare la connettività dei dispositivi con l'interfaccia della riga di comando di Azure](./howto-monitor-devices-azure-cli.md)
- Informazioni su come [definire un nuovo tipo di dispositivo IoT nell'applicazione Azure IoT Central](./howto-set-up-template.md)
- Informazioni sui [dispositivi Azure IoT Edge e su Azure IoT Central](./concepts-iot-edge.md)
