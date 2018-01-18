---
title: Concetti relativi alla sicurezza nel servizio Azure Device Provisioning in hub IoT | Microsoft Docs
description: Descrive i concetti relativi al provisioning della sicurezza specifici dei dispositivi con il servizio Device Provisioning e l'hub IoT
services: iot-dps
keywords: 
author: nberdy
ms.author: nberdy
ms.date: 09/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: ab2bfff571af659552eef8117de041ca6367ce56
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>Concetti relativi alla sicurezza del servizio Device Provisioning in hub IoT 

Il servizio Device Provisioning in hub IoT è un servizio helper per l'hub IoT che consente di configurare il provisioning completamente automatico dei dispositivi in un hub IoT specifico. Con il servizio Device Provisioning è possibile effettuare il provisioning di milioni di dispositivi in modo sicuro e scalabile. Questo articolo fornisce una panoramica dei concetti relativi alla *sicurezza* in relazione al servizio Device Provisioning. L'articolo è di particolare interesse per tutte le persone coinvolte nella preparazione di un dispositivo per la distribuzione.

## <a name="attestation-mechanism"></a>Meccanismo di attestazione

Il meccanismo di attestazione è il metodo usato per verificare l'identità di un dispositivo. Tale meccanismo è importante anche per l'elenco di registrazione, che indica al servizio di provisioning il metodo di attestazione da usare con un determinato dispositivo.

> [!NOTE]
> L'hub IoT usa lo "schema di autenticazione" per un concetto simile in tale servizio.

Il servizio Device Provisioning supporta due tipi di attestazione:
* **Certificati X.509** basati sul flusso di autenticazione del certificato X.509 standard.
* **Token di firma di accesso condiviso** basati su una verifica nonce tramite lo standard TPM per le chiavi. Non è necessario un modulo TPM fisico nel dispositivo, ma il servizio prevede di eseguire l'attestazione usando la chiave di verifica dell'autenticità in base alle [specifiche TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).

## <a name="hardware-security-module"></a>Modulo di protezione hardware

Il modulo di protezione hardware viene usato per l'archiviazione sicura, basata su hardware dei segreti dei dispositivi ed è il tipo di archiviazione dei segreti più sicuro. Nel modulo di protezione hardware è possibile archiviare sia i certificati X.509 che i token di firma di accesso condiviso. I moduli di protezione hardware possono essere usati con entrambi i meccanismi di attestazione supportati dal provisioning.

> [!TIP]
> È consigliabile usare un modulo di protezione hardware con i dispositivi per archiviare in modo sicuro i segreti nei dispositivi.

I segreti dei dispositivi possono essere archiviati anche nel software (memoria), ma si tratta di un tipo di archiviazione meno sicuro rispetto a un modulo di protezione hardware.

## <a name="trusted-platform-module-tpm"></a>TPM (Trusted Platform Module)

Il concetto di TPM può fare riferimento a uno standard per archiviare in modo sicuro le chiavi usate per autenticare la piattaforma oppure all'interfaccia di I/O usata per interagire con i moduli che implementano lo standard. Il TPM può essere un componente hardware distinto o integrato e può essere basato su firmware o su software. Vedere altre informazioni su [TPM e attestazione TPM](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation). Il servizio Device Provisioning supporta solo il TPM 2.0.

### <a name="endorsement-key"></a>Chiave di verifica dell'autenticità

La chiave di verifica dell'autenticità è una chiave asimmetrica contenuta all'interno del TPM che è stata generata o inserita internamente in fase di produzione ed è univoca per ogni TPM. La chiave di verifica dell'autenticità non può essere modificata o rimossa. La parte privata della chiave di verifica dell'autenticità non viene mai rilasciata al di fuori del TPM, mentre la parte pubblica viene usata per riconoscere un TPM originale. Vedere altre informazioni sulla [chiave di verifica dell'autenticità](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

### <a name="storage-root-key"></a>Chiave radice di archiviazione

La chiave radice di archiviazione viene archiviata nel TPM e usata per proteggere le chiavi TPM create dalle applicazioni, in modo che queste chiavi non possano essere usate senza il TPM. La chiave radice di archiviazione viene generata quando si acquisisce la proprietà del TPM. Quando si cancella il TPM in modo che un altro utente possa acquisirne la proprietà, viene generata una nuova chiave radice di archiviazione. Vedere altre informazioni sulla [chiave radice di archiviazione](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="x509-certificates"></a>Certificati X.509

L'uso di certificati X.509 come meccanismo di attestazione è un ottimo approccio per ridimensionare la produzione e semplificare il provisioning di dispositivi. I certificati X.509 sono in genere disposti in una catena di certificati in cui ogni certificato è firmato tramite la chiave privata del successivo certificato superiore e così via, terminando in un certificato radice autofirmato. Questo comportamento stabilisce una catena delegata di certificati dal certificato radice generato da un'autorità di certificazione radice attendibile via via attraverso ogni autorità di certificazione intermedia e fino al certificato dell'entità finale installato in un dispositivo. Per altre informazioni, vedere [Autenticazione dei dispositivi con i certificati della CA X.509](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

Spesso la catena di certificati rappresenta una gerarchia logica o fisica associata ai dispositivi. Ad esempio, un produttore può rilasciare un certificato della CA radice autofirmato, usare il certificato per generare un certificato della CA intermedia univoco per ogni fabbrica, usare il certificato di ogni fabbrica per generare un certificato della CA intermedia univoco per ogni linea di produzione nello stabilimento e infine usare il certificato della linea di produzione per generare un certificato univoco del dispositivo (entità finale) per ogni dispositivo prodotto nella linea di produzione. Per altre informazioni, vedere [Comprensione concettuale dei certificati x.509 nel settore IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-concept). 

### <a name="root-certificate"></a>Certificato radice

Un certificato radice è un certificato X.509 autofirmato che rappresenta un'autorità di certificazione (CA). Si tratta della parte finale, o trust anchor, della catena di certificati. I certificati radice possono essere rilasciati automaticamente da un'organizzazione o acquistati da un'autorità di certificazione radice. Per altre informazioni, vedere [Ottenere certificati della CA X.509](https://docs.microsoft.com/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates). Il certificato radice viene anche chiamato certificato della CA radice.

### <a name="intermediate-certificate"></a>Certificato intermedio

Un certificato intermedio è un certificato X.509 che è stato firmato dal certificato radice (o da un altro certificato intermedio con il certificato radice nella catena). L'ultimo certificato intermedio in una catena viene usato per firmare il certificato foglia. Un certificato intermedio viene anche chiamato certificato della CA intermedia.

### <a name="leaf-certificate"></a>Certificato foglia

Il certificato foglia, o certificato dell'entità finale, identifica il titolare del certificato. Include il certificato radice nella relativa catena di certificati, nonché zero o più certificati intermedi. Il certificato foglia non viene usato per firmare altri certificati. Identifica in modo univoco il dispositivo per il servizio di provisioning e a volte viene chiamato anche certificato del dispositivo. Durante l'autenticazione, il dispositivo usa la chiave privata associata a questo certificato per rispondere a una richiesta di prova di possesso dal servizio. Per altre informazioni, vedere [Autenticazione dei dispositivi firmati con i certificati della CA X.509](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Controllo dell'accesso dei dispositivi al servizio di provisioning con certificati X.509

Il servizio di provisioning espone due tipi di voce di registrazione che è possibile usare per controllare l'accesso per i dispositivi che usano il meccanismo di attestazione X.509:  

- Le voci di [registrazione singola](./concepts-service.md#individual-enrollment) vengono configurate con il certificato del dispositivo associato a un dispositivo specifico. Queste voci controllano la registrazione per dispositivi specifici.
- Le voci dei [gruppi di registrazioni](./concepts-service.md#enrollment-group) sono associate a un certificato della CA intermedia o radice specifico. Queste voci controllano la registrazione per tutti i dispositivi che hanno un certificato intermedio o radice nella rispettiva catena di certificati. 

Quando un dispositivo si connette al servizio di provisioning, il servizio assegna la priorità alle voci di registrazione più specifiche rispetto a quelle meno specifiche. Di conseguenza, se esiste una registrazione singola per il dispositivo, il servizio di provisioning applica questa voce. Se non esiste alcuna registrazione singola per il dispositivo ed esiste invece un gruppo di registrazioni per il primo certificato intermedio nella catena di certificati del dispositivo, il servizio applica questa voce e così via verso l'alto nella catena fino alla radice. Il servizio applica la prima voce applicabile trovata, in modo che:

- Se la prima voce di registrazione trovata è abilitata, il servizio effettua il provisioning del dispositivo.
- Se la prima voce di registrazione trovata è disabilitata, il servizio non effettua il provisioning del dispositivo.  
- Se non vengono trovate voci di registrazione per alcuno dei certificati nella catena di certificati del dispositivo, il servizio non effettua il provisioning del dispositivo. 

Questo meccanismo e la struttura gerarchica delle catene di certificati offrono una potente flessibilità per controllare l'accesso per singoli dispositivi, nonché per gruppi di dispositivi. Ad esempio, si suppongano cinque dispositivi con le catene di certificati seguenti: 

- *Dispositivo 1*: certificato radice -> certificato A -> certificato 1 del dispositivo
- *Dispositivo 2*: certificato radice -> certificato A -> certificato 2 del dispositivo
- *Dispositivo 3*: certificato radice -> certificato A -> certificato 3 del dispositivo
- *Dispositivo 4*: certificato radice -> certificato B -> certificato 4 del dispositivo
- *Dispositivo 5*: certificato radice -> certificato B -> certificato 5 del dispositivo

Inizialmente, è possibile creare un'unica voce di un gruppo di registrazioni abilitata per il certificato radice per abilitare l'accesso per tutti i dispositivi. Se il certificato B viene successivamente compromesso, è possibile creare una voce di un gruppo di registrazioni disabilitata per il certificato B in modo da impedire la registrazione del *dispositivo 4* e del *dispositivo 5*. Se successivamente anche il *dispositivo 3* viene compromesso, è possibile creare una voce di registrazione singola disabilitata per il certificato corrispondente. In questo modo, viene revocato l'accesso per il *dispositivo 3*, continuando a consentire la registrazione del *dispositivo 1* e del *dispositivo 2*.