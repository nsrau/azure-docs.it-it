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
ms.openlocfilehash: ddbb1c6fd705e658867c0d594981e87bc8cd6afe
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930489"
---
# <a name="get-connected-to-azure-iot-central"></a>Connettersi ad Azure IoT Central

*Questo articolo si applica a operatori e sviluppatori di dispositivi.*

Questo articolo descrive le opzioni per la connessione dei dispositivi a un'applicazione IoT Central di Azure.

In genere, è necessario registrare un dispositivo nell'applicazione per potersi connettere. Tuttavia, IoT Central supporta scenari in cui i [dispositivi possono connettersi senza prima essere registrati](#connect-without-registering-devices).

IoT Central USA il [servizio Device provisioning in hub Azure (DPS)](../../iot-dps/about-iot-dps.md) per gestire il processo di connessione. Un dispositivo si connette per la prima volta a un endpoint DPS per recuperare le informazioni necessarie per la connessione all'applicazione. Internamente, l'applicazione IoT Central usa un hub Internet per gestire la connettività dei dispositivi. Con il servizio Device Provisioning:

- IoT Central supporta l'onboarding e la connessione dei dispositivi su larga scala.
- È possibile generare credenziali dei dispositivi e configurarli offline senza registrarli tramite l'interfaccia utente di IoT Central.
- È possibile usare ID dispositivo personalizzati per registrare i dispositivi in IoT Central. L'uso di ID dispositivo personalizzati semplifica l'integrazione con i sistemi back-office esistenti.
- È disponibile un singolo modo coerente per connettere i dispositivi a IoT Central.

Per proteggere la comunicazione tra un dispositivo e l'applicazione, IoT Central supporta sia le firme di accesso condiviso (SAS) che i certificati X. 509. I certificati X. 509 sono consigliati negli ambienti di produzione.

Questo articolo descrive i casi d'uso seguenti:

- [Connettere un singolo dispositivo con SAS](#connect-a-single-device)
- [Connetti i dispositivi su larga scala con SAS](#connect-devices-at-scale-using-sas)
- [Connettere i dispositivi su larga scala usando i certificati X. 509](#connect-devices-using-x509-certificates) : l'approccio consigliato per gli ambienti di produzione.
- [Connettere i dispositivi senza prima registrarli](#connect-without-registering-devices)
- [Connettere i dispositivi che usano le registrazioni singole DPS](#individual-enrollment-based-device-connectivity)
- [Associare automaticamente un dispositivo a un modello di dispositivo](#automatically-associate-with-a-device-template)

## <a name="connect-a-single-device"></a>Connettere un dispositivo

Questo approccio è utile quando si sperimentano IoT Central o i dispositivi di testing. È possibile usare le chiavi di firma di accesso condiviso del dispositivo dell'applicazione IoT Central per connettere un dispositivo all'applicazione IoT Central. Copiare la _chiave SAS del dispositivo_ dalle informazioni di connessione per un dispositivo registrato:

![Chiavi SAS per un singolo dispositivo](./media/concepts-get-connected/single-device-sas.png)

Per altre informazioni, vedere l'esercitazione [creare e connettere un'applicazione client Node. js all'applicazione Azure IOT Central](./tutorial-connect-device-nodejs.md) .

## <a name="connect-devices-at-scale-using-sas"></a>Connetti i dispositivi su larga scala con SAS

Per connettere i dispositivi a IoT Central su larga scala usando le chiavi SAS, è necessario registrare e quindi configurare i dispositivi:

### <a name="register-devices-in-bulk"></a>Registrare i dispositivi in blocco

Per registrare un numero elevato di dispositivi con l'applicazione IoT Central, usare un file CSV per [importare gli ID dispositivo e i nomi dei dispositivi](howto-manage-devices.md#import-devices).

Per recuperare le informazioni di connessione per i dispositivi importati, [esportare un file CSV dall'applicazione IoT Central](howto-manage-devices.md#export-devices). Il file CSV esportato include gli ID dispositivo e le chiavi di firma di accesso condiviso.

### <a name="set-up-your-devices"></a>Configurare i dispositivi

Usare le informazioni di connessione del file di esportazione nel codice del dispositivo per consentire ai dispositivi di connettersi e inviare dati all'applicazione IoT Central. È necessario anche l' **ambito ID** DPS per l'applicazione. È possibile trovare questo valore in **amministrazione > connessione del dispositivo**.

> [!NOTE]
> Per informazioni su come connettere i dispositivi senza prima registrarli in IoT Central, vedere [connettersi senza prima registrare i dispositivi](#connect-without-registering-devices).

## <a name="connect-devices-using-x509-certificates"></a>Connettere i dispositivi con certificati X. 509

In un ambiente di produzione, l'uso di certificati X. 509 è il meccanismo di autenticazione dei dispositivi consigliato per IoT Central. Per altre informazioni, vedere [Autenticazione dei dispositivi con i certificati della CA X.509](../../iot-hub/iot-hub-x509ca-overview.md).

Prima di connettere un dispositivo con un certificato X. 509, aggiungere e verificare un certificato X. 509 intermedio o radice nell'applicazione. I dispositivi devono usare i certificati X. 509 foglia generati dal certificato radice o intermedio.

### <a name="add-and-verify-a-root-or-intermediate-certificate"></a>Aggiungere e verificare un certificato radice o intermedio

Passare ad **amministrazione > connessione del dispositivo > Gestisci certificato primario** e aggiungere la radice X. 509 o il certificato intermedio usato per generare i certificati del dispositivo.

![Impostazioni di connessione](media/concepts-get-connected/manage-x509-certificate.png)

La verifica della proprietà del certificato garantisce che la persona che carica il certificato disponga della chiave privata del certificato. Per verificare il certificato:

  1. Selezionare il pulsante accanto a **codice di verifica** per generare un codice.
  1. Creare un certificato di verifica X. 509 con il codice di verifica generato nel passaggio precedente. Salvare il certificato come file con estensione cer.
  1. Caricare il certificato di verifica firmato e selezionare **Verifica**. Il certificato è contrassegnato come **verificato** quando la verifica ha esito positivo.

Se si verifica una violazione della sicurezza o se il certificato primario è impostato in modo da scadere, usare il certificato secondario per ridurre i tempi di inattività. È possibile continuare a eseguire il provisioning dei dispositivi utilizzando il certificato secondario durante l'aggiornamento del certificato primario.

### <a name="register-and-connect-devices"></a>Registrare e connettere i dispositivi

Per connettere in blocco i dispositivi con certificati X. 509, registrare prima i dispositivi nell'applicazione, usando un file CSV per [importare gli ID dispositivo e i nomi dei dispositivi](howto-manage-devices.md#import-devices). Gli ID del dispositivo devono essere tutti in lettere minuscole.

Generare certificati foglia X. 509 per i dispositivi usando il certificato radice o intermedio caricato. Usare l' **ID del dispositivo** come `CNAME` valore nei certificati foglia. Il codice del dispositivo richiede il valore di **ambito ID** per l'applicazione, l' **ID del dispositivo**e il certificato del dispositivo corrispondente.

#### <a name="sample-device-code"></a>Codice del dispositivo di esempio

L'esempio seguente del [node. js SDK di Azure](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/device/samples/register_x509.js) è illustrato come un client del dispositivo node. js usa un certificato foglia X. 509 e un DPS per la registrazione con un'applicazione IoT Central:

:::code language="nodejs" source="~/azure-iot-sdk-node/provisioning/device/samples/register_x509.js":::

Per un esempio C equivalente, vedere [prov_dev_client_sample. C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c) nell'SDK del client del dispositivo per il [provisioning di Azure](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md). c.

### <a name="for-testing-purposes-only"></a>Solo a scopo di test

Solo per i test, è possibile usare le utilità seguenti per generare i certificati radice, intermedia e del dispositivo:

- [Strumenti per Azure Internet Device provisioning Device SDK](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md): una raccolta di strumenti node. js che è possibile usare per generare e verificare i certificati X. 509 e le chiavi.
- Se si usa un dispositivo DevKit, questo [strumento da riga di comando](https://aka.ms/iotcentral-docs-dicetool) genera un certificato della CA che è possibile aggiungere all'applicazione IoT Central per verificare i certificati.
- [Gestire i certificati della CA di test per esempi ed esercitazioni](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md): una raccolta di script di PowerShell e bash per:
  - Creare una catena di certificati.
  - Salvare i certificati come file con estensione cer da caricare nell'applicazione IoT Central.
  - Usare il codice di verifica dell'applicazione IoT Central per generare il certificato di verifica.
  - Creare certificati foglia per i dispositivi usando gli ID del dispositivo come parametro per lo strumento.

## <a name="connect-without-registering-devices"></a>Connettersi senza registrare i dispositivi

Gli scenari descritti in precedenza richiedono la registrazione dei dispositivi nell'applicazione prima della connessione. IoT Central consente inoltre agli OEM di produrre in massa i dispositivi che possono connettersi senza prima essere registrati. Un OEM genera le credenziali del dispositivo appropriate e configura i dispositivi nella Factory. Quando un cliente accende un dispositivo per la prima volta, si connette a DPS, che quindi connette automaticamente il dispositivo alla IoT Central applicazione corretta. Un operatore IoT Central deve approvare il dispositivo prima di iniziare a inviare i dati all'applicazione.

Il flusso è leggermente diverso a seconda che i dispositivi usino token SAS o certificati X. 509:

### <a name="connect-devices-that-use-sas-tokens-without-registering"></a>Connetti i dispositivi che usano token SAS senza registrazione

1. Copiare la chiave primaria del gruppo dell'applicazione IoT Central:

    ![Chiave SAS primaria del gruppo di applicazioni](media/concepts-get-connected/group-sas-keys.png)

1. Usare lo strumento [DPS-keygen](https://www.npmjs.com/package/dps-keygen) per generare le chiavi di firma di accesso condiviso del dispositivo. Usare la chiave primaria del gruppo nel passaggio precedente. Gli ID dispositivo devono essere in lettere minuscole:

    ```cmd
    dps-keygen -mk:<group primary key> -di:<device ID>
    ```

1. L'OEM lampeggia ogni dispositivo con un ID dispositivo, una chiave di firma di accesso condiviso del dispositivo generata e il valore dell' **ambito dell'ID** applicazione.

1. Quando si passa a un dispositivo, si connette prima di tutto a DPS per recuperare le informazioni di registrazione IoT Central.

    Il dispositivo ha inizialmente uno stato del dispositivo non **associato** nella pagina **dispositivi** e non è assegnato a un modello di dispositivo. Nella pagina **dispositivi** eseguire la **migrazione** del dispositivo al modello di dispositivo appropriato. Il provisioning dei dispositivi è ora completo, ora viene effettuato il **provisioning**dello stato del dispositivo e il dispositivo può iniziare a inviare dati.

    Nella pagina **amministrazione > connessione del dispositivo** , l'opzione di **approvazione automatica** controlla se è necessario approvare manualmente il dispositivo prima di poter iniziare a inviare i dati.

    > [!NOTE]
    > Per informazioni su come associare automaticamente un dispositivo a un modello di dispositivo, vedere [associare automaticamente un](#automatically-associate-with-a-device-template)dispositivo a un modello di dispositivo.

### <a name="connect-devices-that-use-x509-certificates-without-registering"></a>Connettere i dispositivi che usano certificati X. 509 senza registrazione

1. [Aggiungere e verificare un certificato X. 509 radice o intermedio](#connect-devices-using-x509-certificates) per l'applicazione IoT Central.

1. Generare i certificati foglia per i dispositivi usando il certificato radice o intermedio aggiunto all'applicazione IoT Central. Usare gli `CNAME` ID dispositivo in minuscolo come nei certificati foglia.

1. L'OEM lampeggia ogni dispositivo con un ID dispositivo, un certificato X. 509 foglia generato e il valore dell' **ambito dell'ID** applicazione.

1. Quando si passa a un dispositivo, si connette prima di tutto a DPS per recuperare le informazioni di registrazione IoT Central.

    Il dispositivo ha inizialmente uno stato del dispositivo non **associato** nella pagina **dispositivi** e non è assegnato a un modello di dispositivo. Nella pagina **dispositivi** eseguire la **migrazione** del dispositivo al modello di dispositivo appropriato. Il provisioning dei dispositivi è ora completo, ora viene effettuato il **provisioning**dello stato del dispositivo e il dispositivo può iniziare a inviare dati.

    Nella pagina **amministrazione > connessione del dispositivo** , l'opzione di **approvazione automatica** controlla se è necessario approvare manualmente il dispositivo prima di poter iniziare a inviare i dati.

    > [!NOTE]
    > Per informazioni su come associare automaticamente un dispositivo a un modello di dispositivo, vedere [associare automaticamente un](#automatically-associate-with-a-device-template)dispositivo a un modello di dispositivo.

## <a name="individual-enrollment-based-device-connectivity"></a>Connettività dei dispositivi basata sulla registrazione singola

Per i clienti che connettono i dispositivi che hanno le proprie credenziali di autenticazione, usare le registrazioni singole. Una registrazione singola è una voce per un singolo dispositivo a cui è consentita la connessione. Le registrazioni individuali possono usare certificati foglia X. 509 o token di firma di accesso condiviso (da un modulo della piattaforma Trusted virtuale o fisico) come meccanismi di attestazione. L'ID del dispositivo (noto anche come ID di registrazione) in una registrazione singola è alfanumerico, minuscolo e può contenere trattini. Per altre informazioni, vedere [registrazione singola DPS](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment).

> [!NOTE]
> Quando si crea una registrazione singola per un dispositivo, ha la precedenza sulle opzioni di registrazione di gruppo predefinite nell'applicazione IoT Central.

### <a name="create-individual-enrollments"></a>Creare registrazioni singole

IoT Central supporta i meccanismi di attestazione seguenti per le registrazioni individuali:

- **Attestazione chiave simmetrica:** L'attestazione della chiave simmetrica è un approccio semplice per l'autenticazione di un dispositivo con l'istanza DPS. Per creare una registrazione singola che usa chiavi simmetriche, aprire la pagina **connessione del dispositivo** , selezionare **registrazione singola** come metodo di connessione e firma di **accesso condiviso (SAS)** come meccanismo. Immettere chiavi primarie e secondarie con codifica Base64 e salvare le modifiche. Usare l' **ambito ID**, l' **ID dispositivo**e la chiave primaria o secondaria per connettere il dispositivo.

    > [!TIP]
    > Per il test, è possibile usare **openssl** per generare chiavi con codifica Base64:`openssl rand -base64 64`

- **Certificati X. 509:** Per creare una registrazione singola con certificati X. 509, aprire la pagina **connessione del dispositivo** , selezionare **registrazione singola** come metodo di connessione e **certificati (x. 509)** come meccanismo. I certificati del dispositivo usati con una voce di registrazione singola hanno il requisito che l'emittente e l'oggetto CN siano impostati sull'ID del dispositivo.

    > [!TIP]
    > Per il test, è possibile usare [gli strumenti per l'SDK del dispositivo di provisioning di Azure per i dispositivi per node. js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) per generare un certificato autofirmato:`node create_test_cert.js device "mytestdevice"`

- **Attestazione Trusted Platform Module (TPM):** Un [TPM](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation) è un tipo di modulo di protezione hardware. L'uso di un TPM è uno dei modi più sicuri per connettere un dispositivo. Questo articolo presuppone che si stia usando un TPM discreto, firmware o integrato. I TPMs di software emulati sono particolarmente adatti per la realizzazione di prototipi o test, ma non forniscono lo stesso livello di sicurezza di discreto, firmware o TPMs integrato. Non usare il software TPMs nell'ambiente di produzione. Per creare una registrazione singola che usa un TPM, aprire la pagina **connessione del dispositivo** , selezionare **registrazione singola** come metodo di connessione e **TPM** come meccanismo. Immettere la chiave di verifica dell'autenticità del TPM e salvare le informazioni di connessione del dispositivo.

## <a name="automatically-associate-with-a-device-template"></a>Associa automaticamente a un modello di dispositivo

Una delle principali funzionalità di IoT Central è la possibilità di associare automaticamente i modelli di dispositivo alla connessione del dispositivo. Insieme alle credenziali del dispositivo, i dispositivi possono inviare un **CapabilityModelId** come parte della chiamata di registrazione del dispositivo. **CapabilityModelID** è un URN che identifica il modello di funzionalità implementato dal dispositivo. L'applicazione IoT Central può usare **CapabilityModelID** per identificare il modello di dispositivo da usare e quindi associare automaticamente il dispositivo al modello di dispositivo. Il processo di individuazione funziona nel modo seguente:

1. Se il modello di dispositivo è già pubblicato nell'applicazione IoT Central, il dispositivo viene associato al modello di dispositivo.
1. Se il modello di dispositivo non è già stato pubblicato nell'applicazione IoT Central, il modello di dispositivo viene recuperato dal repository pubblico per i dispositivi Plug and Play con pre-certificazione.

I frammenti di codice seguenti mostrano il formato del payload aggiuntivo che il dispositivo deve inviare durante la chiamata di registrazione DPS per il funzionamento dell'associazione automatica.

Si tratta del formato per i dispositivi che usano l'SDK per dispositivi disponibile a livello generale che non supporta le Plug and Play:

```javascript
    iotcModelId: '< this is the URN for the capability model>';
```

Si tratta del formato per i dispositivi che usano l'SDK di dispositivi di anteprima pubblica che supporta l'Plug and Play Internet:

```javascript
'__iot:interfaces': {
    CapabilityModelId: <this is the URN for the capability model>
}
```

> [!NOTE]
> L'opzione di **approvazione automatica** in **Amministrazione > connessione del dispositivo** deve essere abilitata per la connessione automatica dei dispositivi, per individuare il modello di dispositivo e per iniziare a inviare dati.

## <a name="device-status-values"></a>Valori dello stato del dispositivo

Quando un dispositivo reale si connette all'applicazione IoT Central, lo stato del dispositivo viene modificato come segue:

1. Lo stato del dispositivo è stato **registrato**per la prima volta. Questo stato indica che il dispositivo viene creato in IoT Central e ha un ID dispositivo. Un dispositivo viene registrato nei casi seguenti:
    - Viene aggiunto un nuovo dispositivo reale nella pagina **dispositivi** .
    - Viene aggiunto un set di dispositivi usando **Importa** nella pagina **dispositivi** .

1. Lo stato del dispositivo cambia in **provisioning** eseguito quando il dispositivo che si connette all'applicazione IoT Central con credenziali valide completa il passaggio di provisioning. In questo passaggio, il dispositivo usa DPS per recuperare automaticamente una stringa di connessione dall'hub Internet delle cose usato dall'applicazione IoT Central. Il dispositivo può ora connettersi a IoT Central e iniziare a inviare dati.

1. Un operatore può bloccare un dispositivo. Quando un dispositivo è bloccato, non può inviare dati all'applicazione IoT Central. I dispositivi bloccati hanno lo stato **bloccato**. Un operatore deve reimpostare il dispositivo prima di poter riprendere l'invio dei dati. Quando un operatore sblocca un dispositivo, lo stato torna al valore precedente, **registrato** o sottoposto a **provisioning**.

1. Se lo stato del dispositivo è **in attesa di approvazione**, significa che l'opzione **approvazione automatica** è disabilitata. Un operatore deve approvare esplicitamente un dispositivo prima di iniziare a inviare i dati. I dispositivi non registrati manualmente nella pagina **dispositivi** , ma connessi con credenziali valide avranno lo stato del dispositivo **in attesa di approvazione**. Gli operatori possono approvare questi dispositivi dalla pagina **dispositivi** usando il pulsante **approva** .

1. Se lo stato del dispositivo non è **associato**, significa che il dispositivo che si connette a IOT Central non dispone di un modello di dispositivo associato. Questa situazione si verifica in genere negli scenari seguenti:

    - Viene aggiunto un set di dispositivi usando **Importa** nella pagina **dispositivi** senza specificare il modello di dispositivo.
    - Un dispositivo è stato registrato manualmente nella pagina **dispositivi** senza specificare il modello di dispositivo. Il dispositivo è quindi connesso con credenziali valide.  

    L'operatore può associare un dispositivo a un modello di dispositivo dalla pagina **dispositivi** usando il pulsante **migra** .

## <a name="best-practices"></a>Procedure consigliate

Non renderla permanente o memorizzare nella cache la stringa di connessione del dispositivo che DPS restituisce quando si connette il dispositivo per la prima volta. Per riconnettere un dispositivo, esaminare il flusso di registrazione del dispositivo standard per ottenere la stringa di connessione del dispositivo corretta. Se il dispositivo memorizza nella cache la stringa di connessione, il software del dispositivo rischia di avere una stringa di connessione non aggiornata se IoT Central aggiorna l'hub di Azure.

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

Per altre informazioni sull'uso degli SDK per dispositivi, vedere [connettere un dispositivo DevDiv Kit all'applicazione IoT Central di Azure per un](howto-connect-devkit.md) esempio di codice.

### <a name="protocols"></a>Protocolli

Gli SDK di dispositivo supportano i protocolli di rete seguenti per la connessione a un hub IoT:

- MQTT
- AMQP
- HTTPS

Per informazioni sulle differenze fra questi protocolli e indicazioni su come sceglierne uno, vedere [Scegliere un protocollo di comunicazione](../../iot-hub/iot-hub-devguide-protocols.md).

Se il dispositivo non può usare i protocolli supportati, è possibile usare Azure IoT Edge per la conversione del protocollo. IoT Edge supporta altri scenari di intelligence di perimetro per scaricare l'elaborazione sul perimetro dall'applicazione Azure IoT Central.

## <a name="security"></a>Sicurezza

Tutti i dati scambiati tra i dispositivi e Azure IoT Central vengono crittografati. L'hub IoT autentica ogni richiesta da un dispositivo che si connette a qualsiasi endpoint dell'hub IoT a contatto con i dispositivi. Per evitare lo scambio di credenziali nella rete, un dispositivo usa i token firmati per l'autenticazione. Per altre informazioni, vedere [controllare l'accesso all'hub](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Passaggi successivi

Se si è uno sviluppatore di dispositivi, alcuni passaggi successivi suggeriti sono:

- Informazioni su come [monitorare la connettività dei dispositivi usando l'interfaccia](./howto-monitor-devices-azure-cli.md) della riga di comando
- Informazioni su come [definire un nuovo tipo di dispositivo Internet all'interno dell'applicazione IoT Central di Azure](./howto-set-up-template.md)
- Scopri di più sui [dispositivi Azure IOT Edge e su Azure IOT Central](./concepts-iot-edge.md)
