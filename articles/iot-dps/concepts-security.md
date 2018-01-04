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
ms.openlocfilehash: f004e4763106c25d94f585f644560cf3a72d3f1b
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/23/2017
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

La chiave di verifica dell'autenticità è una chiave asimmetrica contenuta all'interno del TPM generato internamente o inserito in fase di produzione ed è univoco per ogni TPM. La chiave di verifica dell'autenticità non può essere modificata o rimossa. La parte privata della chiave di verifica dell'autenticità non viene mai rilasciata al di fuori del TPM, mentre la parte pubblica viene usata per riconoscere un TPM originale. Vedere altre informazioni sulla [chiave di verifica dell'autenticità](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

### <a name="storage-root-key"></a>Chiave radice di archiviazione

La chiave radice di archiviazione viene archiviata nel TPM e usata per proteggere le chiavi TPM create dalle applicazioni, in modo che queste chiavi non possano essere usate senza il TPM. La chiave radice di archiviazione viene generata quando si acquisisce la proprietà del TPM. Quando si cancella il TPM in modo che un altro utente possa acquisirne la proprietà, viene generata una nuova chiave radice di archiviazione. Vedere altre informazioni sulla [chiave radice di archiviazione](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="x509-certificates"></a>Certificati x. 509

Uso di certificati x. 509 come un meccanismo di attestazione è un modo eccellente per scala di produzione e semplificare il provisioning di dispositivi. Certificati x. 509 in genere vengono disposte in una catena di certificati attendibili in cui ogni certificato nella catena è firmato dalla chiave privata del certificato superiore successivo e così via, che termina con un certificato radice autofirmato. In tal modo si stabilisce una catena di trust dal certificato radice generato da un'autorità di certificazione radice attendibile (CA) verso il basso attraverso ogni autorità di certificazione intermedia per il certificato finale installato in un dispositivo delegata. Per ulteriori informazioni, vedere [l'autenticazione del dispositivo mediante certificati x. 509](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-x509ca-overview). 

La catena di certificati rappresenta spesso alcuni gerarchia logico o fisico associata ai dispositivi. Ad esempio, un produttore può emettere un certificato della CA radice autofirmato, usare tale certificato per generare un certificato della CA intermedio univoco per ogni factory, utilizzare il certificato di ogni factory per generare un certificato della CA intermedio univoco per ogni produzione riga dell'impianto e infine utilizzare il certificato di produzione per generare un certificato univoco del dispositivo (entità di fine) per ogni dispositivo prodotte nella riga. Per ulteriori informazioni, vedere [concetti dei certificati di autorità di certificazione x. 509 nel settore IoT](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-x509ca-concept). 

### <a name="root-certificate"></a>Certificato radice

Un certificato radice è un certificato x. 509 autofirmato che rappresenta un'autorità di certificazione (CA). Si tratta di corsa o ancoraggio di trust, della catena di certificati. I certificati radice possono essere autonomo emesso da un'organizzazione o acquistati da un'autorità di certificazione radice. Per ulteriori informazioni, vedere [x. 509 ottenere certificati](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates). Il certificato radice può essere indicato anche come un certificato CA radice.

### <a name="intermediate-certificate"></a>Certificato intermedio

Un certificato intermedio è un certificato x. 509 che è stato firmato dal certificato radice (o da un altro certificato intermedio con il certificato radice nella catena). L'ultimo certificato intermedio in una catena viene utilizzato per firmare il certificato foglia. Un certificato intermedia può essere indicato anche come un certificato della CA intermedia.

### <a name="leaf-certificate"></a>Certificato foglia

Il certificato foglia o certificato finale, identifica il titolare del certificato. Contiene il certificato nella catena di certificati radice e zero o più certificati intermedi. Il certificato foglia non viene usato per firmare altri certificati. In modo univoco identifica il dispositivo al servizio di provisioning e talvolta come il certificato del dispositivo. Durante l'autenticazione, il dispositivo usa la chiave privata associata al certificato per rispondere a un modello di prova della richiesta che possiede dal servizio. Per ulteriori informazioni, vedere [autenticazione per i dispositivi firmati con i certificati di autorità di certificazione x. 509](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Controllo dell'accesso dei dispositivi per il servizio di provisioning con certificati x. 509

Il servizio di provisioning espone due tipi di voce di registrazione che è possibile utilizzare per controllare l'accesso per i dispositivi che usano il meccanismo di x. 509 attestazione:  

- [Registrazione di singoli](./concepts-service.md#individual-enrollment) voci sono configurate con il certificato del dispositivo associato a un dispositivo specifico. Queste voci di controllo la registrazione per dispositivi specifici.
- [Gruppo di registrazione](./concepts-service.md#enrollment-group) voci sono associate un intermedio specifico o un certificato CA radice. Queste voci di controllo la registrazione per tutti i dispositivi che hanno intermedia o certificato la catena di certificati radice. 

Quando un dispositivo si connette al servizio di provisioning, il servizio assegna la priorità più specifiche voci di registrazione sulle voci di registrazione meno specifiche. Se esiste una singola registrazione per il dispositivo, vale a dire, il servizio di provisioning si applica tale voce. Se non è presente alcun singola registrazione per il dispositivo ed esiste un gruppo di registrazione per il primo certificato intermedio nella catena di certificati del dispositivo, il servizio si applica tale voce, e così via fino alla catena alla radice. Il servizio si applica la prima voce applicabile trovato, in modo che:

- Se la prima voce di registrazione trovata è abilitata, il servizio esegue il provisioning del dispositivo.
- Se la prima voce di registrazione trovata è disabilitata, il servizio non eseguire il provisioning del dispositivo.  
- Se viene trovata alcuna voce di registrazione per uno dei certificati nella catena di certificati del dispositivo, il servizio non eseguire il provisioning del dispositivo. 

Questo meccanismo e la struttura gerarchica di catene di certificati offre flessibilità potente di come è possibile controllare l'accesso per i singoli dispositivi anche come gruppi di dispositivi. Si supponga, ad esempio, cinque dispositivi con le catene di certificati seguenti: 

- *1 dispositivo*: il certificato radice -> certificati A -> certificato del dispositivo 1
- *Dispositivo 2*: il certificato radice -> certificati A -> certificato del dispositivo 2
- *Dispositivo 3*: il certificato radice -> certificati A -> certificato del dispositivo 3
- *Dispositivo 4*: il certificato radice -> B -> certificato del dispositivo 4 certificato
- *Dispositivo 5*: il certificato radice -> B -> certificato del dispositivo 5 certificato

Inizialmente, è possibile creare una voce di registrazione singolo gruppo abilitato per il certificato radice consentire l'accesso per tutti e cinque i dispositivi. Se viene compromessa certificato B in un secondo momento, è possibile creare la voce di un gruppo di registrazione disabilitata per il certificato B impedire *dispositivo 4* e *dispositivo 5* dalla registrazione. Se ancora successive *dispositivo 3* diventa compromessa, è possibile creare una voce di registrazione di singoli disabilitato per il proprio certificato. Revoca l'accesso *dispositivo 3*, ma consente ancora *1 dispositivo* e *dispositivo 2* di registrazione.