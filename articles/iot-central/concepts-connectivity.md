---
title: Connettività dei dispositivi in Azure IoT Central | Microsoft Docs
description: Questo articolo presenta i concetti fondamentali relativi alla connettività dei dispositivi in Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 02/28/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 0790b7d5280b3fdf26c34c3903a6257d21bc877c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57835833"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Connettività dei dispositivi in Azure IoT Central

Questo articolo presenta i concetti fondamentali relativi alla connettività dei dispositivi in Microsoft Azure IoT Central.

Azure IoT Central Usa la [servizio di Azure IoT Hub Device Provisioning (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) per gestire tutte le registrazioni di dispositivi e connessione.

L'utilizzo di punti di distribuzione consente:

- IoT Central per il supporto di onboarding e che si connettono i dispositivi su larga scala.
- È possibile generare dispositivo credenziali e configurare i dispositivi offline senza registrare i dispositivi tramite IoT Central dell'interfaccia utente.
- I dispositivi devono connettersi usando condiviso (SAS) delle firme di accesso.
- Dispositivi devono connettersi usando certificati X.509 standard del settore.
- È possibile usare il proprio ID dispositivo per registrare i dispositivi in IoT Central. Tramite il proprio ID dispositivo semplifica l'integrazione con i sistemi back-office esistenti.
- Un modo unico e coerenza per connettere i dispositivi in IoT Central.

Questo articolo descrive i casi di utilizzo di quattro seguenti:

1. [Connettersi rapidamente un singolo dispositivo tramite firma di accesso condiviso](#connect-a-single-device)
1. Connetti i dispositivi su larga scala usando una firma di accesso condiviso
1. [Connetti i dispositivi su larga scala usando certificati X.509](#connect-devices-using-x509-certificates) questo è l'approccio consigliato per gli ambienti di produzione.
1. Connettere i dispositivi senza prima registrarli 

## <a name="connect-a-single-device"></a>Connettere un dispositivo

Questo approccio è utile quando si esegue la sperimentazione con IoT Central o si testano i dispositivi.

Per connettere un singolo dispositivo in uso di SAS IoT Central, seguire questi passaggi:

1. Per aggiungere vero e proprio dispositivo, passare al **Device Explorer**, scegliere un modello di dispositivo e selezionare **+ nuovo > reale**:
    - Immettere il proprio (lettere minuscole) **ID dispositivo** o utilizzare l'ID suggerita.
    - Immettere un **nome dispositivo** oppure usare il nome suggerito.

      ![Aggiungere un dispositivo](media/concepts-connectivity/add-device.png)

1. Per ottenere le informazioni di connessione del dispositivo, selezionare **Connect** nella pagina del dispositivo. È necessario il **ambito ID**, **ID dispositivo**, e **Primary Key** valori:
    - Ogni applicazione IoT Central è una proprietà univoca [ambito ID](../iot-dps/concepts-device.md#id-scope) che viene generato dal servizio Device Provisioning.
    - [ID dispositivo](../iot-dps/concepts-device.md#device-id) è l'ID univoco del dispositivo. Il dispositivo ID viene archiviato nella [registro delle identità](../iot-hub/iot-hub-devguide-identity-registry.md).
    - **Chiave primaria** è un token di firma di accesso condiviso, generato da IoT Central per il dispositivo.

      ![Dettagli della connessione](media/concepts-connectivity/device-connect.png)

Usare le informazioni di connessione nel codice dispositivo per abilitare il dispositivo per la connessione e inviare dati a IoT all'applicazione IoT Central. Per altre informazioni sulla connessione di dispositivi, vedere [passaggi successivi](#next-steps).

## <a name="connect-devices-at-scale-using-sas"></a>Connetti i dispositivi su larga scala usando una firma di accesso condiviso

Per connettere i dispositivi IoT Central su larga scala usando una firma di accesso condiviso, è necessario registrare e quindi configurare i dispositivi:

### <a name="register-devices-in-bulk"></a>Registrare i dispositivi in blocco

Per registrare un numero elevato di dispositivi con l'applicazione IoT Central, usare un file CSV [importare gli ID dispositivo e i nomi dei dispositivi](howto-manage-devices.md#import-devices).

Per recuperare le informazioni di connessione per i dispositivi importati [esportare un file CSV dall'applicazione IoT Central](howto-manage-devices.md#export-devices).

> [!NOTE]
> Per informazioni su come connettere i dispositivi senza doverli registrare nelle IoT Central, vedere Connect senza la registrazione dei dispositivi prima.

### <a name="set-up-your-devices"></a>Configurare i dispositivi

Usare le informazioni di connessione dal file di esportazione del codice di dispositivo per consentire ai dispositivi di connettersi e inviare dati IoT all'applicazione IoT Central. Per altre informazioni sulla connessione di dispositivi, vedere [passaggi successivi](#next-steps).

## <a name="connect-devices-using-x509-certificates"></a>Connettere i dispositivi usando certificati X.509

In un ambiente di produzione usando certificati X.509 è il meccanismo di autenticazione consigliate per i dispositivi per IoT Central. Per altre informazioni, vedere [Autenticazione dei dispositivi con i certificati della CA X.509](../iot-hub/iot-hub-x509ca-overview.md).

I passaggi seguenti viene descritto come connettere dispositivi IoT Central usando certificati X.509:

1. Nell'applicazione IoT Central _aggiungere e verificare intermedia o radice certificato X.509_ si usa per generare i certificati del dispositivo:

    - Passare a **amministrazione > connessione del dispositivo > certificati (X.509)** e aggiungere X.509 radice o un certificato intermedio si usa per generare i certificati del dispositivo foglia.

      ![Impostazioni di connessione](media/concepts-connectivity/connection-settings.png)

      Se si dispone di una violazione della sicurezza o il certificato primario è impostato su una scadenza, usare il certificato secondario per ridurre i tempi di inattività. È possibile continuare a eseguire il provisioning di dispositivi usando il certificato secondario mentre si aggiorna il certificato primario.

    - Verifica della proprietà del certificato garantisce che l'utilità di caricamento del certificato con chiave privata del certificato. Per verificare il certificato:
        - Selezionare il pulsante **il codice di verifica** per generare un codice.
        - Creare un certificato di verifica X.509 con il codice di verifica che è stato generato nel passaggio precedente. Salvare il certificato come file con estensione cer.
        - Caricare il certificato di verifica firmato e selezionare **Verify**.

          ![Impostazioni di connessione](media/concepts-connectivity/verify-cert.png)

1. Usare un file CSV _importare e registrare i dispositivi_ nell'applicazione IoT Central.

1. _Configurare i dispositivi._ generare i certificati foglia usando il certificato radice caricato. Usare la **ID dispositivo** come valore CNAME nei certificati foglia. L'ID del dispositivo deve contenere solo lettere minuscole. Programmare i dispositivi con informazioni sul servizio di provisioning. Quando un dispositivo viene attivato per il primo, recupera le informazioni di connessione per l'applicazione IoT Central dal servizio Device Provisioning.

### <a name="further-reference"></a>Ulteriore riferimento

- Implementazione di esempio per [RaspberryPi](https://aka.ms/iotcentral-docs-Raspi-releases).

- [Client di dispositivo di esempio in C](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md).

### <a name="for-testing-purposes-only"></a>Solo a scopo di test

Solo per i test, è possibile utilizzare queste utilità per generare i certificati della CA e certificati del dispositivo.

- Se si usa un dispositivo DevKit, ciò [strumento della riga di comando](https://aka.ms/iotcentral-docs-dicetool) genera un certificato della CA che è possibile aggiungere all'applicazione IoT Central per verificare i certificati.

- Usare questa [strumento della riga di comando](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) per:
  - Creare una catena di certificati. Seguire il passaggio 2 dell'articolo di GitHub.
  - Salvare i certificati come file con estensione cer da caricare in un'applicazione IoT Central.
  - Usare il codice di verifica dall'applicazione IoT Central per generare il certificato di verifica. Seguire il passaggio 3 nell'articolo di GitHub.
  - Creare certificati foglia per i tuoi dispositivi usando l'ID dispositivo come un parametro allo strumento. Seguire il passaggio 4 nell'articolo di GitHub.

## <a name="connect-without-registering-devices"></a>La connessione senza la registrazione di dispositivi

Uno scenario fondamentale consente a IoT Central è destinata agli OEM per produrre massa dispositivi che possono connettersi a un'applicazione IoT Central senza che sia necessario in fase di registrazione. Un produttore deve generare credenziali appropriate e configurare i dispositivi nell'ambiente di produzione. Quando un dispositivo viene attivata per la prima volta, si connette automaticamente a un'applicazione IoT Central. Un operatore IoT Central deve approvare il dispositivo prima che possa stat e l'invio dei dati.

Il diagramma seguente illustra questo flusso:

![Impostazioni di connessione](media/concepts-connectivity/device-connection-flow.png)

I passaggi seguenti descrivono questo processo in modo più dettagliato. I passaggi variano leggermente a seconda del fatto che si usi certificati X.509 o firma di accesso condiviso per l'autenticazione del dispositivo:

1. Configurare le impostazioni di connessione:

    - **Certificati X.509:** [Aggiungere e verificare il certificato radice/intermedie](#connect-devices-using-x509-certificates) e usarlo per generare i certificati del dispositivo nel passaggio seguente.
    - **Firma di accesso condiviso:** Copiare la chiave primaria. Questa chiave è la chiave di firma di accesso condiviso di gruppo per l'applicazione IoT Central. Usare la chiave per generare le chiavi di firma di accesso condiviso di dispositivo nel passaggio seguente.
    ![Firma di accesso condiviso per le impostazioni di connessione](media/concepts-connectivity/connection-settings-sas.png)

1. Generare le credenziali del dispositivo
    - **Certificati X.509:** Generare i certificati foglia per i tuoi dispositivi usando il certificato radice o intermedio che è stato aggiunto all'applicazione IoT Central. Assicurarsi di usare le lettere minuscole **ID dispositivo** come il record CNAME nei certificati foglia. Per a scopo di test Usa solo questo [strumento della riga di comando](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) per generare i certificati del dispositivo.
    - **Firma di accesso condiviso:** Usare questa [strumento da riga di comando](https://www.npmjs.com/package/dps-keygen) per generare le chiavi di firma di accesso condiviso di dispositivo. Usare il gruppo **Primary Key** nel passaggio precedente. L'ID del dispositivo deve essere in lettere minuscolo.

      Per installare il [utilità di generazione della chiave](https://github.com/Azure/dps-keygen), eseguire il comando seguente:

      ```cmd/sh
      npm i -g dps-keygen
      ```

      Per generare una chiave del dispositivo dalla chiave primaria di firma di accesso condiviso di gruppo, eseguire il comando seguente:

      ```cmd/sh
      dps-keygen -mk:<Primary_Key(GroupSAS)> -di:<device_id>
      ```

1. Per configurare i dispositivi, flash ogni dispositivo con il **ambito ID**, **ID dispositivo**, e **certificato dispositivo X.509** oppure **chiave di firma di accesso condiviso**.

1. Quindi accendere il dispositivo per la connessione all'applicazione IoT Central. Quando si passa in un dispositivo, si connette prima al servizio Device Provisioning per il recupero delle informazioni di registrazione IoT Central.

1. Il dispositivo connesso inizialmente visualizzato come un **dispositivi non associati** nel **Device Explorer** pagina. Lo stato di provisioning del dispositivo è **Registrato**. **Associare** il dispositivo per il modello di dispositivo appropriati e approvare il dispositivo di connettersi all'applicazione IoT Central. Il dispositivo può quindi recuperare una stringa di connessione dall'IoT Hub e iniziare a inviare dati. Il provisioning dei dispositivi è ora completato ed è ora lo stato del provisioning **Provisioned**.

## <a name="provisioning-status"></a>Stato provisioning

Quando un dispositivo reale si connette all'applicazione IoT Central, modifiche dello stato di provisioning come indicato di seguito:

1. Il dispositivo lo stato di provisioning è primo **Registered**. Questo stato indica che il dispositivo viene creato in IoT Central e ha un ID dispositivo. Un dispositivo viene registrato quando:
    - Viene aggiunto un nuovo dispositivo reale nel **Device Explorer** pagina.
    - Viene aggiunto un set di dispositivi utilizzando **importazione** nel **Device Explorer** pagina.
    - Un dispositivo non è stato registrato manualmente nel **Device Explorer** pagina, ma connessi con credenziali valide e non è visibile come un' **Unassociated** dispositivo sul **Device Explorer**pagina.

1. Device provisioning lo stato viene modificato in **Provisioned** quando il dispositivo connesso all'applicazione IoT Central con credenziali valide viene completata la fase di provisioning. In questo passaggio, il dispositivo recupera una stringa di connessione dall'IoT Hub. Il dispositivo può ora connettersi all'IoT Hub e iniziare a inviare dati.

1. Un operatore può bloccare un dispositivo. Quando un dispositivo è bloccato, è possibile inviare dati all'applicazione IoT Central. I dispositivi bloccato con stato provisioning **bloccato**. Un operatore necessario reimpostare il dispositivo prima che sia possibile riprendere l'invio dei dati. Quando un operatore Sblocca un dispositivo lo stato del provisioning restituisce il valore precedente **Registered** oppure **Provisioned**.

## <a name="get-a-connection-string"></a>Ottenere una stringa di connessione

I passaggi seguenti descrivono come ottenere una stringa di connessione per un dispositivo:

1. Selezionare **Connect** nel **Device Explorer** pagina per ottenere i dettagli della connessione: **Definire l'ambito ID**, **ID dispositivo**, e **chiave primaria dispositivo**:

    ![Dettagli della connessione](media/concepts-connectivity/device-connect.png)

1. Usare il `dps-keygen` utilità della riga di comando per generare una stringa di connessione:  Per installare il [utilità di generazione della chiave](https://github.com/Azure/dps-keygen), eseguire il comando seguente:

    ```cmd/sh
    npm i -g dps-keygen
    ```

    Per generare una stringa di connessione, eseguire il comando seguente:

    ```cmd/sh
    dps-keygen -di:<device_id> -dk:<device_key> -si:<scope_id>
    ```

## <a name="sdk-support"></a>Supporto SDK

L'offerta di Azure SDK per dispositivi il modo più semplice per è implementare il codice del dispositivo. Attualmente sono disponibili gli SDK seguenti:

- [Azure IoT SDK per C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK per Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT SDK per Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT SDK per Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK per .NET](https://github.com/azure/azure-iot-sdk-csharp)

Ogni dispositivo si connette usando una stringa di connessione univoca che identifica il dispositivo stesso. Un dispositivo può connettersi solo all'hub IoT in cui è registrato. Quando si crea un dispositivo reale nell'applicazione Azure IoT Central, l'applicazione genera le informazioni necessarie per costruire una stringa di connessione usando `dps-keygen`.

### <a name="sdk-features-and-iot-hub-connectivity"></a>Funzioni dell'SDK e connettività dell'hub IoT

Tutte le comunicazioni dei dispositivi con l'hub IoT usano le opzioni di connettività dell'hub IoT seguenti:

- [Messaggistica da dispositivo a cloud](../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Dispositivi gemelli](../iot-hub/iot-hub-devguide-device-twins.md)

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

Ora che si sono appresi connettività dei dispositivi in Azure IoT Central, ecco i passaggi successivi consigliati:

- [Preparare e connettere un dispositivo DevKit](howto-connect-devkit.md)
- [Preparare e connettere un dispositivo Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Connettere un'applicazione client Node.js generica all'applicazione Azure IoT Central](howto-connect-nodejs.md)
- [C SDK: Provisioning Device Client SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
