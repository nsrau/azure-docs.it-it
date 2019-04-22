---
title: Secure OPC UA client e un'applicazione server OPC UA con OPC dell'insieme di credenziali - Azure | Microsoft Docs
description: Proteggere client OPC UA e OPC UA server applicazione con una nuova coppia di chiavi e un certificato usando OPC dell'insieme di credenziali.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 5ba2dba02585598b3797dd1b490976ebe34b489e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59495295"
---
# <a name="secure-opc-ua-client-and-opc-ua-server-application"></a>Secure OPC UA OPC UA server applicazioni e del client 
OPC dell'insieme di credenziali è un microservizio che è possibile configurare, registrare e gestire ciclo di vita del certificato server OPC UA e nelle applicazioni client nel cloud. Questo articolo illustra come proteggere un client OPC UA e un OPC UA applicazione server con una nuova coppia di chiavi e un certificato usando OPC dell'insieme di credenziali.

Nel programma di installazione seguente, il client OPC sta testando la connettività di PLC OPC. Per impostazione predefinita, la connettività non è possibile perché entrambi i componenti non ancora sottoposti a provisioning con i certificati a destra. In questo flusso di lavoro, Microsoft non usa i certificati autofirmati i componenti OPC UA e firmarli tramite OPC dell'insieme di credenziali. Vedere la precedente [banco di prova](howto-opc-vault-deploy-existing-client-plc-communication.md). Al contrario, questo banco di prova. effettua il provisioning di componenti con un nuovo certificato, oltre che con una nuova chiave privata che vengono generati dall'insieme di credenziali di OPC. Alcune informazioni generali sulla sicurezza OPC UA sono reperibile in questo [white paper](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf). Le informazioni complete sono reperibile nella specifica OPC UA.

Banco di prova: L'ambiente è configurato per il test.

Script OPC dell'insieme di credenziali:
- Proteggere le applicazioni server con una nuova coppia di chiavi e un certificato usando OPC dell'insieme di credenziali client OPC UA e OPC UA.

> [!NOTE]
> Per altre informazioni, vedere GitHub [repository](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds).

## <a name="generate-a-new-certificate-and-private-key"></a>Generare un nuovo certificato e chiave privata 
**Operazioni preliminari**
- Assicurarsi che le variabili di ambiente `$env:_PLC_OPT` e `$env:_CLIENT_OPT` sono definiti. Ad esempio, `$env:_PLC_OPT=""` in di PowerShell
- Impostare la variabile di ambiente `$env:_OPCVAULTID` a una stringa che è possibile trovare anche in questo caso i dati nell'insieme di credenziali di OPC. È consigliabile impostarlo su un numero di 6 cifre. Per questo esempio, "123456" è stato usato come valore della variabile.
- Verificare che nessun volume docker `opcclient` o `opcplc`. Rivolgersi `docker volume ls` e quindi rimuoverle con `docker volume rm <volumename>`. Potrebbe essere necessario rimuovere anche i contenitori con `docker rm <containerid>` se i volumi vengono comunque usati da un contenitore.

**Guida introduttiva**
1. Andare alla [sito Web di archivio OPC](https://opcvault.azurewebsites.net/)

1. Selezionare `Register New`.

1. Immettere le informazioni di OPC PLC come visualizzato nell'output del log del banco di prova precedente `CreateSigningRequest information` area nei campi di input nel `Register New OPC UA Application` pagina, selezionare `Server` come ApplicationType.

1. Selezionare `Register`.

1. Nella pagina successiva, `Request New Certificate for OPC UA Application` selezionare `Request new KeyPair and Certificate`

1. Nella pagina successiva `Generate a new Certificate with a Signing Request` incollo il `CSR (base64 encoded)` stringa nell'output di log nel `CreateRequest` campo di input. Assicurarsi di che copiare la stringa completa.

1. Nella pagina successiva, `Request New Certificate for OPC UA Application` selezionare `Request new Certificate with Signing Request`

1. Nella pagina successiva `Generate a new KeyPair and for an OPC UA Application` immettere `CN=OpcPlc` come SubjectName, `opcplc-<_OPCVAULTID>` (Sostituisci `<_OPCVAULTID>` con i propri) come nome di dominio, selezionare `PEM` come PrivateKeyFormat e immettere una password (si fa riferimento in un secondo momento come `<certpassword-string>`)

1. Selezionare `Generate New KeyPair`.

1. Ora procedano a `View Certificate Request Details`. In questa pagina, è possibile scaricare tutte le informazioni necessarie per eseguire il provisioning gli archivi di certificati di `opc-plc`.

1. In questa pagina:  
    - Selezionare `Certificate` nelle `Download as Base64` e copiare la stringa di testo presentata nel `EncodedBase64` campo e li archivierà per uso successivo. Si fa riferimento a esso come `<applicationcertbase64-string>` in un secondo momento. Selezionare `Back`.
    - Selezionare `PrivateKey` nelle `Download as Base64` e copiare la stringa di testo presentata nel `EncodedBase64` campo e li archivierà per uso successivo. Si fa riferimento a esso come `<privatekeybase64-string>` in un secondo momento. Selezionare `Back`.
    - Selezionare `Issuer` nelle `Download as Base64` e copiare la stringa di testo presentata nel `EncodedBase64` campo e li archivierà per uso successivo. Si fa riferimento a esso come `<addissuercertbase64-string>` in un secondo momento. Selezionare `Back`.
    - Selezionare `Crl` nelle `Download as Base64` e copiare la stringa di testo presentata nel `EncodedBase64` campo e li archivierà per uso successivo. Si fa riferimento a esso come `<updatecrlbase64-string>` in un secondo momento. Selezionare `Back`.

1. Ora impostare nella finestra di PowerShell una variabile denominata `$env:_PLC_OPT`:

   `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --privatekeybase64 <privatekeybase64-string> --certpassword <certpassword-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  

    Sostituire le stringhe passate come stringhe Base64 valori opzione che recupero dal sito Web.  

1. Ripetere il processo completato a partire da `Register New` per il client OPC. Sono disponibili solo le differenze seguenti di che è necessario essere a conoscenza:
    - Usare l'output del log di `opcclient`.
    - Selezionare `Client` come ApplicationType durante la registrazione.
    - Usare `$env:_CLIENT_OPT` come nome della variabile di PowerShell.

    > [!NOTE] 
    > Durante l'utilizzo di questo scenario, si potrebbe avere riconosciuto che il `<addissuercertbase64-string>` e `<updatecrlbase64-string>` i valori sono identici per `opcplc` e `opcclient`. Questo vale per il caso d'uso e consente di risparmiare tempo durante i passaggi di questa operazione.

**Guida introduttiva**

Eseguire il comando PowerShell seguente nella radice del repository:

```
docker-compose -f connecttest.yml up
```

**Verifica**

Verificare che i due componenti non hanno subito un certificato dell'applicazione esistente. Controllare l'output del log. Di seguito è riportato l'output di OPC PLC e client OPC include un output di log simili.

```
opcplc-123456 | [13:40:08 INF] There is no existing application certificate.
```
Se è presente un certificato dell'applicazione, rimuovere i volumi docker, come illustrato nei passaggi di preparazione.

Verificare nel log che il certificato della CA OPC dell'insieme di credenziali è stato installato nell'archivio certificati dell'autorità di certificazione come illustrato nell'archivio certificati attendibili peer. Di seguito è riportato l'output del log di OPC PLC e Client OPC include un output di log simili. 

```
opcplc-123456 | [13:40:09 INF] Trusted issuer store contains 1 certs
opcplc-123456 | [13:40:09 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [13:40:09 INF] Trusted issuer store has 1 CRLs.
opcplc-123456 | [13:40:09 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [13:40:09 INF] Trusted peer store contains 1 certs
opcplc-123456 | [13:40:09 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [13:40:09 INF] Trusted peer store has 1 CRLs.
opcplc-123456 | [13:40:09 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [13:40:09 INF] Rejected certificate store contains 0 certs
```
Il PLC OPC ora considerare attendibili tutti i client OPC UA con i certificati firmati da OPC dell'insieme di credenziali.

Verificare nel log che il formato della chiave privato viene riconosciuto come PEM e che sia installato il nuovo certificato dell'applicazione. Di seguito è riportato l'output del log di OPC PLC e client OPC include un output di log simili. 

```
opcplc-123456 | [13:40:09 INF] The private key for the new certificate was passed in using PEM format.
opcplc-123456 | [13:40:09 INF] Remove the existing application certificate.
opcplc-123456 | [13:40:09 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C' was added to the application certificate store.
opcplc-123456 | [13:40:09 INF] Activating the new application certificate with thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C'.
```

Il certificato dell'applicazione e la chiave privata sono ora installati nell'archivio certificati dell'applicazione e utilizzati dall'applicazione OPC UA.

Verificare che è possibile stabilire correttamente la connessione tra il client OPC e PLC OPC e il client OPC è stato possibile leggere dati da PLC OPC. Si dovrebbe essere il seguente output nell'output del log del client OPC:
```
opcclient-123456 | [13:40:12 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [13:40:12 INF] Session successfully created with Id ns=3;i=941910499.
opcclient-123456 | [13:40:12 INF] The session to endpoint 'opc.tcp://opcplc-123456:50000/' has 4 entries in its namespace array:
opcclient-123456 | [13:40:12 INF] Namespace index 0: http://opcfoundation.org/UA/
opcclient-123456 | [13:40:12 INF] Namespace index 1: urn:OpcPlc:opcplc-123456
opcclient-123456 | [13:40:12 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
opcclient-123456 | [13:40:12 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
opcclient-123456 | [13:40:12 INF] The server on endpoint 'opc.tcp://opcplc-123456:50000/' supports a minimal sampling interval of 0 ms.
opcclient-123456 | [13:40:12 INF] Execute 'OpcClient.OpcTestAction' action on node 'i=2258' on endpoint 'opc.tcp://opcplc-123456:50000/' with security.
opcclient-123456 | [13:40:12 INF] Action (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258') completed successfully
opcclient-123456 | [13:40:12 INF] Value (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258'): 10/21/2018 13:40:12
```
Se viene visualizzato questo output, quindi il PLC OPC viene ora concessione dell'attendibilità OPC client e vice versa quanto entrambi hanno ora i certificati firmati da un'autorità di certificazione ed entrambe attendibili i certificati che sono stati firmati da questa autorità di certificazione.

### <a name="a-testbed-for-opc-publisher"></a>Un banco di prova per server di pubblicazione OPC ###

**Guida introduttiva**

Eseguire il comando PowerShell seguente nella radice del repository:
```
docker-compose -f testbed.yml up
```

**Verifica**
- Verificare che i dati vengono inviati a hub IOT è stato configurato impostando `_HUB_CS` usando [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) oppure [iothub-explorer](https://github.com/Azure/iothub-explorer).
- Client di prova OPC dovrà utilizzare chiamate a metodi diretti IoTHub e chiamate al metodo OPC per configurare server di pubblicazione OPC per pubblicare/annullare la pubblicazione di nodi dal server di prova OPC.
- Osservare l'output per i messaggi di errore.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come distribuire OPC dell'insieme di credenziali di un progetto esistente, ecco il passaggio successivo consigliato:

> [!div class="nextstepaction"]
> [Distribuire OPC dei dispositivi gemelli in un progetto esistente](howto-opc-twin-deploy-existing.md)