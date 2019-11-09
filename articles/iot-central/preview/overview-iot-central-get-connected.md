---
title: Connettività dei dispositivi in Azure IoT Central | Microsoft Docs
description: Questo articolo presenta i concetti fondamentali relativi alla connettività dei dispositivi in Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: ef0e4c9070733266349a37e863c48901eae90c16
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73894591"
---
# <a name="get-connected-to-azure-iot-central-preview-features"></a>Connessione ad Azure IoT Central (funzionalità di anteprima)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Questo articolo presenta i concetti fondamentali relativi alla connettività dei dispositivi in Microsoft Azure IoT Central.

Azure IoT Central USA il [servizio Device provisioning in hub Azure (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) per gestire tutte le registrazioni e la connessione dei dispositivi.

L'uso di DPS Abilita:

- IoT Central per supportare il caricamento e la connessione di dispositivi su larga scala.
- Per generare le credenziali del dispositivo e configurare i dispositivi offline senza registrare i dispositivi tramite IoT Central interfaccia utente.
- Dispositivi per la connessione tramite firme di accesso condiviso (SAS).
- Dispositivi per la connessione utilizzando certificati X. 509 standard del settore.
- È possibile usare ID dispositivo personalizzati per registrare i dispositivi in IoT Central. L'uso di ID dispositivo personalizzati semplifica l'integrazione con i sistemi back-Office esistenti.
- Un unico modo coerente per connettere i dispositivi ai IoT Central.

Questo articolo descrive i casi d'uso seguenti:

- [Connetti rapidamente un singolo dispositivo con SAS](#connect-a-single-device)
- [Connetti i dispositivi su larga scala con SAS](#connect-devices-at-scale-using-sas)
- [Connettere i dispositivi su larga scala usando i certificati X. 509](#connect-devices-using-x509-certificates) questo è l'approccio consigliato per gli ambienti di produzione.
- [Connettere i dispositivi senza prima registrarli](#connect-without-registering-devices)
- [Connettere i dispositivi usando le funzionalità Plug and Play](#connect-devices-with-iot-plug-and-play)

## <a name="connect-a-single-device"></a>Connettere un dispositivo

Questo approccio è utile quando si sperimentano IoT Central o i dispositivi di testing. È possibile usare le informazioni di connessione del dispositivo dell'applicazione IoT Central per connettere un dispositivo all'applicazione IoT Central usando il servizio Device provisioning (DPS). È possibile trovare il codice client del dispositivo DPS di esempio per le lingue seguenti:

- [C\#](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device)
- [Node.js](https://github.com/Azure-Samples/azure-iot-samples-node/tree/master/provisioning/Samples/device)

## <a name="connect-devices-at-scale-using-sas"></a>Connetti i dispositivi su larga scala con SAS

Per connettere i dispositivi a IoT Central su larga scala con la firma di accesso condiviso, è necessario registrare e quindi configurare i dispositivi:

### <a name="register-devices-in-bulk"></a>Registrare i dispositivi in blocco

Per registrare un numero elevato di dispositivi con l'applicazione IoT Central, usare un file CSV per [importare gli ID dispositivo e i nomi dei dispositivi](howto-manage-devices.md#import-devices).

Per recuperare le informazioni di connessione per i dispositivi importati, [esportare un file CSV dall'applicazione IoT Central](howto-manage-devices.md#export-devices).

> [!NOTE]
> Per informazioni su come connettere i dispositivi senza prima registrarli in IoT Central, vedere [connettersi senza prima registrare i dispositivi](#connect-without-registering-devices).

### <a name="set-up-your-devices"></a>Configurare i dispositivi

Usare le informazioni di connessione del file di esportazione nel codice del dispositivo per consentire ai dispositivi di connettersi e inviare dati all'applicazione IoT Central. Per ulteriori informazioni sulla connessione dei dispositivi, vedere [passaggi successivi](#next-steps).

## <a name="connect-devices-using-x509-certificates"></a>Connettere i dispositivi con certificati X. 509

In un ambiente di produzione, l'uso di certificati X. 509 è il meccanismo di autenticazione dei dispositivi consigliato per IoT Central. Per altre informazioni, vedere [Autenticazione dei dispositivi con i certificati della CA X.509](../../iot-hub/iot-hub-x509ca-overview.md).

I passaggi seguenti descrivono come connettere i dispositivi a IoT Central usando i certificati X. 509:

1. Nell'applicazione IoT Central _aggiungere e verificare il certificato X. 509 intermedio o radice_ che si sta usando per generare i certificati del dispositivo:

    - Passare ad **amministrazione > connessione del dispositivo > certificati (X. 509)** e aggiungere la radice X. 509 o il certificato intermedio che si sta usando per generare i certificati del dispositivo foglia.

      ![Impostazioni di connessione](media/overview-iot-central-get-connected/connection-settings.png)

      Se si verifica una violazione della sicurezza o se il certificato primario è impostato in modo da scadere, usare il certificato secondario per ridurre i tempi di inattività. È possibile continuare a eseguire il provisioning dei dispositivi utilizzando il certificato secondario durante l'aggiornamento del certificato primario.

    - La verifica della proprietà del certificato garantisce che l'Uploader del certificato disponga della chiave privata del certificato. Per verificare il certificato:
        - Selezionare il pulsante accanto a **codice di verifica** per generare un codice.
        - Creare un certificato di verifica X. 509 con il codice di verifica generato nel passaggio precedente. Salvare il certificato come file con estensione cer.
        - Caricare il certificato di verifica firmato e selezionare **Verifica**.

          ![Impostazioni di connessione](media/overview-iot-central-get-connected/verify-cert.png)

1. Usare un file CSV per _importare e registrare i dispositivi_ nell'applicazione IoT Central.

1. _Configurare i dispositivi._ generare i certificati foglia usando il certificato radice caricato. Usare l' **ID dispositivo** come valore CNAME nei certificati foglia. L'ID dispositivo deve essere in lettere minuscole. Quindi programmare i dispositivi con le informazioni sul servizio di provisioning. Quando un dispositivo viene attivato per primo, recupera le informazioni di connessione per l'applicazione IoT Central da DPS.

### <a name="further-reference"></a>Ulteriori riferimenti

- Implementazione di esempio per [RaspberryPi](https://aka.ms/iotcentral-docs-Raspi-releases).

- [Client di dispositivo di esempio in C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md).

### <a name="for-testing-purposes-only"></a>Solo a scopo di test

Solo per i test, è possibile usare queste utilità per generare certificati della CA e certificati del dispositivo.

- Se si usa un dispositivo DevKit, questo [strumento da riga di comando](https://aka.ms/iotcentral-docs-dicetool) genera un certificato della CA che è possibile aggiungere all'applicazione IoT Central per verificare i certificati.

- Utilizzare questo [strumento da riga di comando](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) per:
  - Creare una catena di certificati. Seguire il passaggio 2 nell'articolo di GitHub.
  - Salvare i certificati come file con estensione cer da caricare nell'applicazione IoT Central.
  - Usare il codice di verifica dell'applicazione IoT Central per generare il certificato di verifica. Seguire il passaggio 3 nell'articolo di GitHub.
  - Creare certificati foglia per i dispositivi usando gli ID del dispositivo come parametro per lo strumento. Seguire il passaggio 4 nell'articolo di GitHub.

## <a name="connect-without-registering-devices"></a>Connettersi senza registrare i dispositivi

Uno scenario chiave IoT Central consente agli OEM di produrre in massa i dispositivi in grado di connettersi a un'applicazione IoT Central senza prima essere registrati. Un produttore deve generare credenziali appropriate e configurare i dispositivi nella Factory. Quando un dispositivo viene attivato per la prima volta, si connette automaticamente a un'applicazione IoT Central. Un operatore IoT Central deve approvare il dispositivo prima di poter inviare i dati.

Il diagramma seguente illustra questo flusso:

![Impostazioni di connessione](media/overview-iot-central-get-connected/device-connection-flow1.png)

I passaggi seguenti descrivono questo processo in modo più dettagliato. La procedura varia leggermente a seconda che si usino certificati SAS o X. 509 per l'autenticazione del dispositivo:

1. Configurare le impostazioni di connessione:

    - **Certificati X. 509:** [aggiungere e verificare il certificato radice/intermedio](#connect-devices-using-x509-certificates) e usarlo per generare i certificati del dispositivo nel passaggio seguente.
    - Firma di accesso condiviso **:** Copiare la chiave primaria. Si tratta della chiave di firma di accesso condiviso del gruppo per l'applicazione IoT Central. Usare la chiave per generare le chiavi di firma di accesso condiviso del dispositivo nel passaggio seguente.
    ![Firma di accesso condiviso per le impostazioni di connessione](media/overview-iot-central-get-connected/connection-settings-sas.png)

1. Genera le credenziali del dispositivo
    - **Certificati X. 509:** Generare i certificati foglia per i dispositivi usando il certificato radice o intermedio aggiunto all'applicazione IoT Central. Assicurarsi di usare l' **ID dispositivo** in minuscolo come CNAME nei certificati foglia. Solo a scopo di test, usare questo [strumento da riga di comando](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) per generare i certificati del dispositivo.
    - Firma di accesso condiviso **:** Usare questo [strumento da riga di comando](https://www.npmjs.com/package/dps-keygen) per generare chiavi SAS del dispositivo. Usare la **chiave primaria** del gruppo nel passaggio precedente. L'ID dispositivo deve essere in minuscolo.

      Per installare l'[utilità generatore di chiavi](https://github.com/Azure/dps-keygen), eseguire il comando seguente:

      ```cmd/sh
      npm i -g dps-keygen
      ```

      Per generare una chiave del dispositivo dalla chiave primaria SAS del gruppo, eseguire il comando seguente:

      ```cmd/sh
      dps-keygen -mk:<Primary_Key(GroupSAS)> -di:<device_id>
      ```

1. Per configurare i dispositivi, eseguire il flashing di ogni dispositivo con l' **ID ambito**, l' **ID dispositivo**e il **certificato del dispositivo X. 509** o la **chiave SAS**.

1. Quindi accendere il dispositivo per connettersi all'applicazione IoT Central. Quando si passa a un dispositivo, si connette prima di tutto a DPS per recuperare le informazioni di registrazione IoT Central.

1. Il dispositivo connesso viene inizialmente visualizzato come non **associato** nella pagina **dispositivi** . Lo stato di provisioning del dispositivo è **Registrato**. **Eseguire la migrazione** del dispositivo al modello di dispositivo appropriato e approvare il dispositivo per la connessione all'applicazione IoT Central. Il dispositivo può quindi recuperare una stringa di connessione dall'hub Internet e iniziare a inviare i dati. Il provisioning dei dispositivi è ora completo ed è stato effettuato il **provisioning**dello stato del provisioning.

## <a name="individual-enrollment-based-device-connectivity"></a>Connettività del dispositivo basata sulla registrazione singola

Per i clienti che connettono i dispositivi con credenziali di autenticazione per ogni singola registrazione del dispositivo è l'opzione. Una registrazione singola è una voce per un singolo dispositivo che può connettersi. Le registrazioni individuali possono usare certificati foglia X.509 o token di firma di accesso condiviso (in un TPM fisico o virtuale) come meccanismo di attestazione. L'ID del dispositivo (noto anche come ID di registrazione) in una registrazione singola è alfanumerico, minuscolo e può contenere trattini. Altre informazioni sulle registrazioni individuali sono disponibili [qui](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment).

> [!NOTE]
> Quando si crea una registrazione singola per un dispositivo, ha la precedenza sulle attestazioni basate sulla registrazione di gruppo predefinite (SAS, X509) nell'app.

### <a name="creating-individual-enrollments"></a>Creazione di registrazioni singole
IoT Central supporta i meccanismi di attestazione seguenti

1. **Attestazione chiave simmetrica:** L'attestazione della chiave simmetrica è un approccio semplice per l'autenticazione di un dispositivo con un'istanza del servizio Device provisioning. Per creare una registrazione singola con chiavi simmetriche; Aprire la finestra di dialogo Connetti, selezionare registrazione singola e meccanismo "SAS" e immettere le chiavi primarie e secondarie. Le chiavi SAS devono avere codifica Base64. Ecco il [collegamento](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/SymmetricKeySample) agli esempi di codice per semplificare la scrittura del codice del dispositivo per il provisioning dei dispositivi con chiavi simmetriche e registrazioni singole.
1. **Certificati X. 509:** I certificati X. 509 come titolo suggerisce sono un meccanismo di attestazione basato su certificati, un ottimo modo per ridimensionare la produzione. Per creare una registrazione singola con chiavi simmetriche selezionare registrazione e meccanismo singoli "X. 509" e caricare i certificati primari e secondari e salvare per creare la registrazione. Ecco il [collegamento](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/X509Sample) agli esempi di codice per semplificare la scrittura del codice del dispositivo per il provisioning dei dispositivi con X509. I certificati del dispositivo usati con una voce di registrazione [singola](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment) hanno il requisito che il nome del soggetto deve essere impostato sull'ID del dispositivo (noto anche come ID di registrazione) della voce di registrazione singola.
1. **Attestazione TPM:** TPM sta per Trusted Platform Module ed è un tipo di modulo di protezione hardware (HSM) ed è uno dei modi più sicuri per connettere i dispositivi.  In questo articolo si presuppone che l’utente utilizzi un firmware discreto o integrato TPM. I TPM emulatori di software sono ideali per la creazione di prototipi o test, ma non forniscono lo stesso livello di sicurezza di un firmware discreto o integrato TPM. Non è consigliabile utilizzare software TPM nell'ambiente di produzione. Per creare una registrazione singola con chiavi simmetriche, selezionare registrazione singola e meccanismo "TPM" e immettere le chiavi di verifica dell'autenticità per creare la registrazione. Per ulteriori informazioni sui tipi di TPMs, è possibile ottenere altre informazioni sull'attestazione TPM [qui](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation). Ecco il [collegamento](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/TpmSample) agli esempi di codice per semplificare la scrittura del codice del dispositivo per il provisioning dei dispositivi tramite TPM. Per creare un'attestazione basata su TPM, digitare la chiave di verifica dell'autenticità e salvare.

## <a name="connect-devices-with-iot-plug-and-play"></a>Connetti i dispositivi con Plug and Play

Una delle funzionalità principali di Plug and Play con IoT Central è la possibilità di associare automaticamente i modelli di dispositivo alla connessione del dispositivo. Insieme alle credenziali del dispositivo, i dispositivi possono ora inviare il **CapabilityModelId** come parte della chiamata di registrazione del dispositivo e IOT Central individua e associa il modello di dispositivo. Il processo di individuazione segue l'ordine seguente:

1. Associa al modello di dispositivo se è già pubblicato nell'applicazione IoT Central.
1. Recupera dal repository pubblico dei modelli di funzionalità pubblicate e certificate.

Di seguito è riportato il formato del payload aggiuntivo inviato dal dispositivo durante la chiamata di registrazione DPS

```javascript
'__iot:interfaces': {
              CapabilityModelId: <this is the URN for the capability model>
          }
```

> [!NOTE]
> Si noti che l'opzione di approvazione automatica deve essere abilitata per la connessione automatica dei dispositivi, per individuare il modello e iniziare a inviare dati.

## <a name="device-status"></a>Stato dei dispositivi

Quando un dispositivo reale si connette all'applicazione IoT Central, lo stato del dispositivo viene modificato come segue:

1. Lo stato del dispositivo è stato **registrato**per la prima volta. Questo stato indica che il dispositivo viene creato in IoT Central e ha un ID dispositivo. Un dispositivo viene registrato nei casi seguenti:
    - Viene aggiunto un nuovo dispositivo reale nella pagina **dispositivi** .
    - Viene aggiunto un set di dispositivi usando **Importa** nella pagina **dispositivi** .

1. Lo stato del dispositivo cambia in **provisioning** eseguito quando il dispositivo che si connette all'applicazione IoT Central con credenziali valide completa il passaggio di provisioning. In questo passaggio il dispositivo recupera una stringa di connessione dall'hub Internet. Il dispositivo può ora connettersi all'hub Internet e iniziare a inviare i dati.

1. Un operatore può bloccare un dispositivo. Quando un dispositivo è bloccato, non può inviare dati all'applicazione IoT Central. I dispositivi bloccati hanno lo stato **bloccato**. Un operatore deve reimpostare il dispositivo prima di poter riprendere l'invio dei dati. Quando un operatore sblocca un dispositivo, lo stato torna al valore precedente, **registrato** o sottoposto a **provisioning**.

1. Lo stato del dispositivo è **in attesa di approvazione** , il che significa che l'opzione **auto approva** è disabilitata e richiede che tutti i dispositivi che si connettono a IOT Central essere approvati esplicitamente da un operatore. I dispositivi non registrati manualmente nella pagina **dispositivi** , ma connessi con credenziali valide avranno lo stato del dispositivo **in attesa di approvazione**. Gli operatori possono approvare questi dispositivi dalla pagina **dispositivi** usando il pulsante **approva** .

1. Lo stato del dispositivo non è **associato** , il che significa che i dispositivi che si connettono a IOT Central non dispongono di un modello di dispositivo associato. Questa situazione si verifica in genere negli scenari seguenti:
    - Viene aggiunto un set di dispositivi usando **Importa** nella pagina **dispositivi** senza specificare il modello di dispositivo
    - I dispositivi non sono registrati manualmente nella pagina **dispositivi** connessa con credenziali valide, ma senza specificare l'ID modello durante la registrazione.  
L'operatore può associare un dispositivo a un modello dalla pagina **dispositivi** usando il pulsante **migra** .

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
| Misura: dati di telemetria | Messaggistica da dispositivo a cloud |
| Proprietà dei dispositivi | Proprietà segnalate del dispositivo gemello |
| Impostazioni | Proprietà desiderate e segnalate nel dispositivo gemello |

Per altre informazioni sull'uso degli SDK per dispositivi, vedere [connettere un dispositivo DevDiv Kit all'applicazione IoT Central di Azure per un](howto-connect-devkit.md) esempio di codice.

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

Ora che sono state apprese informazioni sulla connettività dei dispositivi in Azure IoT Central, ecco i passaggi successivi suggeriti:

- [Preparare e connettere un dispositivo DevKit](howto-connect-devkit.md)
- [C SDK: provisioning di Device client SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
