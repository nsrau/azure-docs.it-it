---
title: Proteggere la comunicazione del client OPC e PLC OPC con OPC dell'insieme di credenziali - Azure | Microsoft Docs
description: Proteggere la comunicazione del client OPC e OPC PLC firmando i certificati con autorità di certificazione OPC dell'insieme di credenziali.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 30eedd982fa0536ce45506c159de6d04132e9a14
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59493998"
---
# <a name="secure-the-communication-of-opc-client-and-opc-plc"></a>Proteggere la comunicazione del client OPC e OPC PLC

OPC dell'insieme di credenziali è un microservizio che è possibile configurare, registrare e gestire il ciclo di vita certificato server OPC UA e nelle applicazioni client nel cloud. Questo articolo illustra come proteggere la comunicazione del client OPC e OPC PLC firmando i certificati con autorità di certificazione OPC dell'insieme di credenziali.

Nel programma di installazione seguente, il client OPC testa la connettività al PLC OPC. Per impostazione predefinita, la connettività non è possibile perché entrambi i componenti non vengono effettuato il provisioning con i certificati a destra. Se un componente di OPC UA non è stata ancora fornito con un certificato, potrebbe generare un certificato autofirmato all'avvio. Tuttavia, il certificato può essere firmato da un'autorità di certificazione (CA) e installato nel componente OPC UA. Dopo questa operazione viene eseguita per client OPC e PLC OPC, la connettività è abilitata. Il flusso di lavoro riportato di seguito viene descritto il processo. Alcune informazioni generali sulla sicurezza OPC UA sono reperibile nel [questo documento](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf) white paper. Le informazioni complete sono reperibile nella specifica OPC UA.

Banco di prova: L'ambiente è configurato per il test.

Script OPC dell'insieme di credenziali:
- Proteggere la comunicazione del client OPC e OPC PLC firmando i certificati con autorità di certificazione OPC dell'insieme di credenziali.

> [!NOTE]
> Per altre informazioni, vedere GitHub [repository](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds).

## <a name="generate-a-self-signed-certificate-on-startup"></a>Generare un certificato autofirmato all'avvio

**Operazioni preliminari**

- Assicurarsi che le variabili di ambiente `$env:_PLC_OPT` e `$env:_CLIENT_OPT` sono definiti, ad esempio, `$env:_PLC_OPT=""` in di PowerShell.

- Impostare la variabile di ambiente `$env:_OPCVAULTID` a una stringa che è possibile trovare anche in questo caso i dati nell'insieme di credenziali di OPC. Sono consentiti solo caratteri alfanumerici. Per questo esempio, "123456" è stato usato come valore per questa variabile.

- Assicurarsi che non sono presenti volumi docker `opcclient` o `opcplc`. Rivolgersi `docker volume ls` e quindi rimuoverle con `docker volume rm <volumename>`. Potrebbe essere necessario rimuovere anche i contenitori con `docker rm <containerid>` se i volumi vengono comunque usati da un contenitore.

**Guida introduttiva**

Eseguire il comando PowerShell seguente nella radice del repository:

```
docker-compose -f connecttest.yml up
```

**Verifica**

Verificare nel log che non sono presenti certificati installati al primo avvio. Qui l'output del log di OPC PLC (Mostra simile a client OPC):...
```
opcplc-123456 | [20:51:32 INF] Trusted issuer store contains 0 certs
opcplc-123456 | [20:51:32 INF] Trusted issuer store has 0 CRLs.
opcplc-123456 | [20:51:32 INF] Trusted peer store contains 0 certs
opcplc-123456 | [20:51:32 INF] Trusted peer store has 0 CRLs.
opcplc-123456 | [20:51:32 INF] Rejected certificate store contains 0 certs
```
Se è possibile visualizzare i certificati segnalati, seguire la procedura di preparazione precedente ed eliminare i volumi docker.

Verificare che la connessione di PLC OPC non riuscita. Si dovrebbe essere il seguente output nell'output del log del client OPC:

```
opcclient-123456 | [20:51:35 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [20:51:36 ERR] Session creation to endpoint 'opc.tcp://opcplc-123456:50000/' failed 1 time(s). Please verify if server is up and OpcClient configuration is correct.
opcclient-123456 | Opc.Ua.ServiceResultException: Certificate is not trusted.
```
Il motivo dell'errore è che il certificato non attendibile. Ciò significa che `opc-client` prova a connettersi `opc-plc` e ha ricevuto una risposta, che indica che `opc-plc` non considera attendibile `opc-client`, un risultato della `opc-plc` non è possibile convalidare il certificato che `opc-client` ha fornito. Si tratta di un certificato autofirmato senza ulteriore configurazione di certificato in `opc-plc`, e pertanto la connessione non è riuscita.

## <a name="sign-and-install-certificates-in-opc-ua-components"></a>Accedere e installare i certificati nei componenti OPC UA

**Operazioni preliminari**
1. Esaminare l'output del log del passaggio 1 e per il recupero "CreateSigningRequest informazioni" per il PLC OPC e il client OPC. In questo caso l'output viene visualizzato solo per PLC OPC:

    ```
    opcplc-123456 | [20:51:32 INF] ----------------------- CreateSigningRequest information ------------------
    opcplc-123456 | [20:51:32 INF] ApplicationUri: urn:OpcPlc:opcplc-123456
    opcplc-123456 | [20:51:32 INF] ApplicationName: OpcPlc
    opcplc-123456 | [20:51:32 INF] ApplicationType: Server
    opcplc-123456 | [20:51:32 INF] ProductUri: https://github.com/azure-samples/iot-edge-opc-plc
    opcplc-123456 | [20:51:32 INF] DiscoveryUrl[0]: opc.tcp://opcplc-123456:50000
    opcplc-123456 | [20:51:32 INF] ServerCapabilities: DA
    opcplc-123456 | [20:51:32 INF] CSR (base64 encoded):
    opcplc-123456 | MIICmzCCAYMCAQAwETEPMA0GA1UEAwwGT3BjUGxjMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAwTvlbinAPWPxR9Lw1ndGsrLGy8GiqVOxyGaDpPUcMchX0k0/ncg28h7xrB2H1PThdiZxUJuUwsNM74HrVgt
    ofmXhA4dLM1cTxZHyJVFjl2L3vK5M58NNf6UNdKcB0x3LyuoT6mAIMXmCioqymFCk1TMzIMzbAe7JVAdUaSRBP1vuqQ1rV/cfNAe35dKQW4aPYgl7pR5f1hqprcDu/oca67X8L4kTl4oN0/bCYTk+Ibcd9cG462oAN+bSwbHn8a2jNky8rGsofA
    o9DOT+0ALPhk6CApCYIP2yxoI/kT188eqUUxzAFF9nyU79AyCkpGPuY8DGMyf56pDofgtGpfY3wQIDAQABoEUwQwYJKoZIhvcNAQkOMTYwNDAyBgNVHREEKzAphhh1cm46T3BjUGxjOm9wY3BsYy0xMjM0NTaCDW9wY3BsYy0xMjM0NTYwDQYJK
    oZIhvcNAQELBQADggEBAAsZLoOLzS2VhDcQRu0QhRbG7CGAxX19l7fDCG2WjU7lTFnCvYVTWTYyaY61ljmrWc7IbCaQdMJM8GRnAnvAzUh/PBDxkOX7NqI2+8F1yQOHgs/AfKuppOd6DIP8EzFAHnc0H85jay6zFdmIDWoWwpy0ACqOVooOTKST
    7uty0mT87bj8Cdy1yf4mvBNQx+nsuTbKgxWCBxGYAyg9dIL2uKL0aeB/ROW5Gkelz5sCEzQ1fFDokUA4oC5QiATQBN3cY7EmvRbPgdToY7CpRN3iiO7J+7bC7BP9YKfuE34E8xOFpskHPHAPf3r002/L0S67HyuVSXLUj1+Jc0LeAAF9Bw0=
    opcplc-123456 | [20:51:32 INF] ---------------------------------------------------------------------------
    ```
    
1. Andare alla [sito Web di archivio OPC](https://opcvault.azurewebsites.net/).

1. Selezionare `Register New`.

1. Immettere le informazioni di OPC PLC dall'output del log `CreateSigningRequest information` area nei campi di input nel `Register New OPC UA Application` pagina, selezionare `Server` come ApplicationType.

1. Selezionare `Register`.

1. Nella pagina successiva `Request New Certificate for OPC UA Application` seleziona `Request new Certificate with Signing Request`.

1. Nella pagina successiva `Generate a new Certificate with a Signing Request` incollo il `CSR (base64 encoded)` stringa nell'output di log nel `CreateRequest` campo di input. Assicurarsi di che copiare la stringa completa.

1. Selezionare `Generate New Certificate`.

1. Ora procedano a `View Certificate Request Details`. In questa pagina, è possibile scaricare tutte le informazioni necessarie per eseguire il provisioning gli archivi di certificati di `opc-plc`.

1. In questa pagina:  
    - Selezionare `Certificate` nelle `Download as Base64` e copiare la stringa di testo presentata nel `EncodedBase64` campo e li archivierà per uso successivo. Si fa riferimento a esso come `<applicationcertbase64-string>` in un secondo momento. Selezionare `Back`.

    - Selezionare `Issuer` nelle `Download as Base64` e copiare la stringa di testo presentata nel `EncodedBase64` campo e li archivierà per uso successivo. Si fa riferimento a esso come `<addissuercertbase64-string>` in un secondo momento. Selezionare `Back`.

    - Selezionare `Crl` nelle `Download as Base64` e copiare la stringa di testo presentata nel `EncodedBase64` campo e li archivierà per uso successivo. Si fa riferimento a esso come `<updatecrlbase64-string>` in un secondo momento. Selezionare `Back`.

1. Ora impostare nella finestra di PowerShell una variabile denominata `$env:_PLC_OPT`:

    ```
    `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  
    ```
    > [!NOTE] 
    > Sostituire le stringhe passate come stringhe Base64 valori opzione che recupero dal sito Web.

Ripetere il processo completato a partire da `Register New` (passaggio 3 precedente) per il client OPC. Sono disponibili solo le differenze seguenti di che è necessario essere a conoscenza:

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

**Verifica** verificare che i due componenti sono ora firmato certificati dell'applicazione. Controllare l'output del log per l'output seguente:

```
opcplc-123456 | [20:54:38 INF] Starting to add certificate(s) to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Certificate 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' and thumbprint 'BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83' was added to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Starting to add certificate(s) to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Certificate 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' and thumbprint 'BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83' was added to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Starting to update the current CRL.
opcplc-123456 | [20:54:38 INF] Remove the current CRL from the trusted peer store.
opcplc-123456 | [20:54:38 INF] The new CRL issued by 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' was added to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Remove the current CRL from the trusted issuer store.
opcplc-123456 | [20:54:38 INF] The new CRL issued by 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' was added to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Start updating the current application certificate.
opcplc-123456 | [20:54:38 INF] The current application certificate has SubjectName 'CN=OpcPlc' and thumbprint '8FD43F66479398BDA3AAF5B193199A6657632B49'.
opcplc-123456 | [20:54:39 INF] Remove the existing application certificate with thumbprint '8FD43F66479398BDA3AAF5B193199A6657632B49'.
opcplc-123456 | [20:54:39 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586' was added to the application certificate store.
opcplc-123456 | [20:54:39 INF] Activating the new application certificate with thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586'.
opcplc-123456 | [20:54:39 INF] Application certificate with thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586' found in the application certificate store.
opcplc-123456 | [20:54:39 INF] Application certificate is for ApplicationUri 'urn:OpcPlc:opcplc-123456', ApplicationName 'OpcPlc' and Subject is 'OpcPlc'
 ```
Il certificato dell'applicazione è presente e firmato da un'autorità di certificazione.

Verificare che ora sono presenti certificati installati nel registro. Di seguito è riportato l'output del log di OPC PLC e client OPC dispone di un output simile.
```
opcplc-123456 | [20:54:39 INF] Trusted issuer store contains 1 certs
opcplc-123456 | [20:54:39 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [20:54:39 INF] Trusted issuer store has 1 CRLs.
opcplc-123456 | [20:54:39 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [20:54:39 INF] Trusted peer store contains 1 certs
opcplc-123456 | [20:54:39 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [20:54:39 INF] Trusted peer store has 1 CRLs.
opcplc-123456 | [20:54:39 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [20:54:39 INF] Rejected certificate store contains 0 certs
```
L'autorità emittente del certificato dell'applicazione è l'autorità di certificazione `CN=Azure IoT OPC Vault CA, O=Microsoft Corp.` e il PLC OPC attendibilità anche tutti i certificati firmati da questa autorità di certificazione.


Verificare che sia stata creata correttamente la connessione al PLC OPC e il client OPC legge i dati da PLC OPC. Si dovrebbe essere il seguente output nell'output del log del client OPC:
```
opcclient-123456 | [20:54:42 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [20:54:42 INF] Session successfully created with Id ns=3;i=1085867946.
opcclient-123456 | [20:54:42 INF] The session to endpoint 'opc.tcp://opcplc-123456:50000/' has 4 entries in its namespace array:
opcclient-123456 | [20:54:42 INF] Namespace index 0: http://opcfoundation.org/UA/
opcclient-123456 | [20:54:42 INF] Namespace index 1: urn:OpcPlc:opcplc-123456
opcclient-123456 | [20:54:42 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
opcclient-123456 | [20:54:42 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
opcclient-123456 | [20:54:42 INF] The server on endpoint 'opc.tcp://opcplc-123456:50000/' supports a minimal sampling interval of 0 ms.
opcclient-123456 | [20:54:42 INF] Execute 'OpcClient.OpcTestAction' action on node 'i=2258' on endpoint 'opc.tcp://opcplc-123456:50000/' with security.
opcclient-123456 | [20:54:42 INF] Action (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258') completed successfully
opcclient-123456 | [20:54:42 INF] Value (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258'): 10/20/2018 20:54:42
```
Se viene visualizzato questo output, quindi il OPC PLC è ora trusting OPC client e viceversa in altre parole, in quanto entrambi hanno ora i certificati firmati da un'autorità di certificazione e sia trust quali where di certificati firmati da questa autorità di certificazione.

> [!NOTE] 
> Anche se sono stati presentati i passaggi di due verifica prima solo per PLC OPC, dovranno essere verificato anche per client OPC.


## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come distribuire OPC dell'insieme di credenziali di un progetto esistente, ecco il passaggio successivo consigliato:

> [!div class="nextstepaction"]
> [Distribuire OPC dei dispositivi gemelli in un progetto esistente](howto-opc-twin-deploy-existing.md)