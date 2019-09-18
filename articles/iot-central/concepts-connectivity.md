---
title: Connettività dei dispositivi in Azure IoT Central | Microsoft Docs
description: Questo articolo presenta i concetti fondamentali relativi alla connettività dei dispositivi in Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 08/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: bb7711eea927212042ed2299bae74130867c1692
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067649"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Connettività dei dispositivi in Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Questo articolo presenta i concetti fondamentali relativi alla connettività dei dispositivi in Microsoft Azure IoT Central.

Azure IoT Central USA il [servizio Device provisioning in hub Azure (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) per gestire tutte le registrazioni e la connessione dei dispositivi.

L'uso di DPS Abilita:

- IoT Central per supportare il caricamento e la connessione di dispositivi su larga scala.
- Per generare le credenziali del dispositivo e configurare i dispositivi offline senza registrare i dispositivi tramite IoT Central interfaccia utente.
- Dispositivi per la connessione tramite firme di accesso condiviso (SAS).
- Dispositivi per la connessione utilizzando certificati X. 509 standard del settore.
- È possibile usare ID dispositivo personalizzati per registrare i dispositivi in IoT Central. L'uso di ID dispositivo personalizzati semplifica l'integrazione con i sistemi back-Office esistenti.
- Un unico modo coerente per connettere i dispositivi ai IoT Central.

Questo articolo descrive i quattro casi d'uso seguenti:

- [Connetti rapidamente un singolo dispositivo con SAS](#connect-a-single-device)
- [Connetti i dispositivi su larga scala con SAS](#connect-devices-at-scale-using-sas)
- [Connettere i dispositivi su larga scala usando i certificati X. 509](#connect-devices-using-x509-certificates) questo è l'approccio consigliato per gli ambienti di produzione.
- [Connettere i dispositivi senza prima registrarli](#connect-without-registering-devices)

## <a name="connect-a-single-device"></a>Connettere un dispositivo

Questo approccio è utile quando si sperimentano IoT Central o i dispositivi di testing. È possibile usare le informazioni di connessione del dispositivo dell'applicazione IoT Central per connettere un dispositivo all'applicazione IoT Central usando il servizio Device provisioning (DPS). È possibile trovare il codice client del dispositivo DPS di esempio per le lingue seguenti:

- [C\#](./howto-connect-raspberry-pi-csharp.md)
- [Node.js](./howto-connect-nodejs.md)

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

In un ambiente di produzione, l'uso di certificati X. 509 è il meccanismo di autenticazione dei dispositivi consigliato per IoT Central. Per altre informazioni, vedere [Autenticazione dei dispositivi con i certificati della CA X.509](../iot-hub/iot-hub-x509ca-overview.md).

I passaggi seguenti descrivono come connettere i dispositivi a IoT Central usando i certificati X. 509:

1. Nell'applicazione IoT Central _aggiungere e verificare il certificato X. 509 intermedio o radice_ che si sta usando per generare i certificati del dispositivo:

    - Passare ad **amministrazione > connessione del dispositivo > certificati (X. 509)** e aggiungere la radice X. 509 o il certificato intermedio che si sta usando per generare i certificati del dispositivo foglia.

      ![Impostazioni di connessione](media/concepts-connectivity/connection-settings.png)

      Se si verifica una violazione della sicurezza o se il certificato primario è impostato in modo da scadere, usare il certificato secondario per ridurre i tempi di inattività. È possibile continuare a eseguire il provisioning dei dispositivi utilizzando il certificato secondario durante l'aggiornamento del certificato primario.

    - La verifica della proprietà del certificato garantisce che l'Uploader del certificato disponga della chiave privata del certificato. Per verificare il certificato:
        - Selezionare il pulsante accanto a **codice di verifica** per generare un codice.
        - Creare un certificato di verifica X. 509 con il codice di verifica generato nel passaggio precedente. Salvare il certificato come file con estensione cer.
        - Caricare il certificato di verifica firmato e selezionare **Verifica**.

          ![Impostazioni di connessione](media/concepts-connectivity/verify-cert.png)

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

![Impostazioni di connessione](media/concepts-connectivity/device-connection-flow1.png)

I passaggi seguenti descrivono questo processo in modo più dettagliato. La procedura varia leggermente a seconda che si usino certificati SAS o X. 509 per l'autenticazione del dispositivo:

1. Configurare le impostazioni di connessione:

    - **Certificati X. 509:** [Aggiungere e verificare il certificato radice/intermedio](#connect-devices-using-x509-certificates) e usarlo per generare i certificati del dispositivo nel passaggio seguente.
    - **Firma di accesso condiviso:** Copiare la chiave primaria. Si tratta della chiave di firma di accesso condiviso del gruppo per l'applicazione IoT Central. Usare la chiave per generare le chiavi di firma di accesso condiviso del dispositivo nel passaggio seguente.
    ![Firma di accesso condiviso per le impostazioni di connessione](media/concepts-connectivity/connection-settings-sas.png)

1. Genera le credenziali del dispositivo
    - **Certificati X. 509:** Generare i certificati foglia per i dispositivi usando il certificato radice o intermedio aggiunto all'applicazione IoT Central. Assicurarsi di usare l' **ID dispositivo** in minuscolo come CNAME nei certificati foglia. Solo a scopo di test, usare questo [strumento da riga di comando](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) per generare i certificati del dispositivo.
    - **Firma di accesso condiviso:** Usare questo [strumento da riga di comando](https://www.npmjs.com/package/dps-keygen) per generare chiavi SAS del dispositivo. Usare la **chiave primaria** del gruppo nel passaggio precedente. L'ID dispositivo deve essere in minuscolo.

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

1. Il dispositivo connesso viene inizialmente visualizzato come **dispositivo non associato** nella pagina **Device Explorer** . Lo stato di provisioning del dispositivo è **Registrato**. **Associare** il dispositivo al modello di dispositivo appropriato e approvare il dispositivo per la connessione all'applicazione IoT Central. Il dispositivo può quindi recuperare una stringa di connessione dall'hub Internet e iniziare a inviare i dati. Il provisioning dei dispositivi è ora completo ed è stato effettuato il **provisioning**dello stato del provisioning.

## <a name="provisioning-status"></a>Stato provisioning

Quando un dispositivo reale si connette all'applicazione IoT Central, lo stato del provisioning viene modificato come segue:

1. Lo stato di provisioning del dispositivo è stato **registrato**per la prima volta. Questo stato indica che il dispositivo viene creato in IoT Central e ha un ID dispositivo. Un dispositivo viene registrato nei casi seguenti:
    - Viene aggiunto un nuovo dispositivo reale nella pagina **Device Explorer** .
    - Viene aggiunto un set di dispositivi usando **Importa** nella pagina **Device Explorer** .
    - Un dispositivo non è stato registrato manualmente nella pagina **Device Explorer** , ma connesso con credenziali valide ed è visibile come dispositivo non **associato** nella pagina **Device Explorer** .

1. Lo stato del provisioning del dispositivo viene modificato in **provisioning** quando il dispositivo che si connette all'applicazione IoT Central con credenziali valide completa il passaggio di provisioning. In questo passaggio il dispositivo recupera una stringa di connessione dall'hub Internet. Il dispositivo può ora connettersi all'hub Internet e iniziare a inviare i dati.

1. Un operatore può bloccare un dispositivo. Quando un dispositivo è bloccato, non può inviare dati all'applicazione IoT Central. I dispositivi bloccati hanno uno stato di provisioning **bloccato**. Un operatore deve reimpostare il dispositivo prima di poter riprendere l'invio dei dati. Quando un operatore sblocca un dispositivo, lo stato del provisioning ritorna al valore precedente, **registrato** o sottoposto a **provisioning**.

## <a name="sdk-support"></a>Supporto SDK

Gli SDK per dispositivi di Azure offrono il modo più semplice per implementare il codice del dispositivo. Attualmente sono disponibili gli SDK seguenti:

- [Azure IoT SDK per C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK per Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT SDK per Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT SDK per Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK per .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>Funzioni dell'SDK e connettività dell'hub IoT

Tutte le comunicazioni dei dispositivi con l'hub IoT usano le opzioni di connettività dell'hub IoT seguenti:

- [Messaggistica da dispositivo a cloud](../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Dispositivi gemelli](../iot-hub/iot-hub-devguide-device-twins.md)

La tabella seguente riepiloga le corrispondenze tra le funzionalità di dispositivo di Azure IoT Central e le funzionalità dell'hub IoT:

| Azure IoT Central | Hub IoT di Azure |
| ----------- | ------- |
| Misura: Telemetria | Messaggistica da dispositivo a cloud |
| Proprietà dei dispositivi | Proprietà segnalate del dispositivo gemello |
| Impostazioni | Proprietà desiderate e segnalate nel dispositivo gemello |

Per altre informazioni sull'uso degli SDK di dispositivo, vedere uno degli articoli seguenti con esempi di codice:

- [Connettere un'applicazione client Node.js generica all'applicazione Azure IoT Central](howto-connect-nodejs.md)
- [Connettere un dispositivo Raspberry Pi all'applicazione Azure IoT Central](howto-connect-raspberry-pi-python.md)
- [Connettere un dispositivo DevDiv kit all'applicazione Azure IoT Central](howto-connect-devkit.md).

### <a name="protocols"></a>Protocolli

Gli SDK di dispositivo supportano i protocolli di rete seguenti per la connessione a un hub IoT:

- MQTT
- AMQP
- HTTPS

Per informazioni sulle differenze fra questi protocolli e indicazioni su come sceglierne uno, vedere [Scegliere un protocollo di comunicazione](../iot-hub/iot-hub-devguide-protocols.md).

Se il dispositivo non può usare i protocolli supportati, è possibile usare Azure IoT Edge per la conversione del protocollo. IoT Edge supporta altri scenari di intelligence di perimetro per scaricare l'elaborazione sul perimetro dall'applicazione Azure IoT Central.

## <a name="security"></a>Security

Tutti i dati scambiati tra i dispositivi e Azure IoT Central vengono crittografati. L'hub IoT autentica ogni richiesta da un dispositivo che si connette a qualsiasi endpoint dell'hub IoT a contatto con i dispositivi. Per evitare lo scambio di credenziali nella rete, un dispositivo usa i token firmati per l'autenticazione. Per altre informazioni, vedere [Controllare l'accesso all'hub IoT](../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Passaggi successivi

Ora che sono state apprese informazioni sulla connettività dei dispositivi in Azure IoT Central, ecco i passaggi successivi suggeriti:

- [Preparare e connettere un dispositivo DevKit](howto-connect-devkit.md)
- [Preparare e connettere un dispositivo Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Connettere un'applicazione client Node.js generica all'applicazione Azure IoT Central](howto-connect-nodejs.md)
- [C SDK: SDK client del dispositivo di provisioning](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
