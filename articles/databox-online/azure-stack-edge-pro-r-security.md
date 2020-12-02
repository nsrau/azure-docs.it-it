---
title: Sicurezza di Azure Stack Edge Pro R | Microsoft Docs
description: Vengono descritte le funzionalità di sicurezza e privacy che proteggono i dispositivi, i servizi e i dati di Azure Stack Edge Pro R e di Azure Stack Edge in locale e nel cloud.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: f7d81d14ca561e6d4d897994088b2fc01b2c7701
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467801"
---
# <a name="security-and-data-protection-for-azure-stack-edge-pro-r-and-azure-stack-edge-mini-r"></a>Sicurezza e protezione dei dati per Azure Stack Edge Pro R e Azure Stack Edge Mini R

[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]

La sicurezza è un problema fondamentale quando si adotta una nuova tecnologia, soprattutto se la tecnologia viene usata con dati riservati o proprietari. Azure Stack Edge Pro R e Azure Stack Edge Mini R aiutano a garantire che solo le entità autorizzate possano visualizzare, modificare o eliminare i dati.

Questo articolo descrive le funzionalità di sicurezza r e Azure Stack Edge di Azure Stack Edge Pro che consentono di proteggere ogni componente della soluzione e i dati archiviati.

La soluzione è costituita da quattro componenti principali che interagiscono tra loro:

- **Servizio di Azure stack Edge, ospitato nel cloud pubblico di Azure o in Azure per enti** pubblici. La risorsa di gestione usata per creare l'ordine dei dispositivi, configurare il dispositivo e quindi tenere traccia dell'ordine fino al completamento.
- **Dispositivo robusto Azure stack Edge**. Il dispositivo fisico robusto fornito all'utente per poter importare i dati locali nel cloud pubblico di Azure o in Azure per enti pubblici. Il dispositivo potrebbe essere Azure Stack Edge Pro R o Azure Stack Edge Mini R.
- **Client/host connessi al dispositivo**. I client nell'infrastruttura che si connettono al dispositivo e contengono dati che devono essere protetti.
- **Archiviazione cloud**. Posizione della piattaforma cloud di Azure in cui vengono archiviati i dati. Questo percorso è in genere l'account di archiviazione collegato alla risorsa Azure Stack Edge creata.

## <a name="service-protection"></a>Protezione del servizio

Il servizio Azure Stack Edge è un servizio di gestione ospitato in Azure. Il servizio viene usato per configurare e gestire il dispositivo.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-service-protection.md)]

## <a name="device-protection"></a>Protezione dei dispositivi

Il dispositivo robusto è un dispositivo locale che consente di trasformare i dati mediante l'elaborazione locale e quindi inviarli ad Azure. Il dispositivo:

- Richiede una chiave di attivazione per accedere al servizio Azure Stack Edge.
- È sempre protetto da una password del dispositivo.
- È un dispositivo bloccato. Il Baseboard Management Controller del dispositivo (BMC) e il BIOS sono protetti da password. Il BMC è protetto da accesso utente limitato.
- Dispone di avvio protetto abilitato che garantisce che il dispositivo venga avviato solo utilizzando il software attendibile fornito da Microsoft.
- Esegue il controllo delle applicazioni di Windows Defender (WDAC). WDAC consente di eseguire solo applicazioni attendibili definite nei criteri di integrità del codice.
- Dispone di un Trusted Platform Module (TPM) che esegue funzioni correlate alla sicurezza basate su hardware. In particolare, il TPM gestisce e protegge i segreti e i dati che devono essere salvati in modo permanente nel dispositivo.
- Nel dispositivo vengono aperte solo le porte necessarie e tutte le altre porte sono bloccate. Per ulteriori informazioni, vedere l'elenco dei [requisiti di porta per il dispositivo](azure-stack-edge-pro-r-system-requirements.md) .
- Tutti gli accessi all'hardware del dispositivo e al software vengono registrati. 
    - Per il software del dispositivo, vengono raccolti i log predefiniti del firewall per il traffico in ingresso e in uscita dal dispositivo. Questi log sono aggregati nel pacchetto per il supporto.
    - Per l'hardware del dispositivo, tutti gli eventi dello chassis del dispositivo, ad esempio l'apertura e la chiusura dello chassis del dispositivo, vengono registrati nel dispositivo.

    Per altre informazioni sui log specifici che contengono gli eventi di intrusione hardware e software e su come ottenere i log, vedere raccogliere i registri di [sicurezza avanzati](azure-stack-edge-gpu-troubleshoot.md).


### <a name="protect-the-device-via-activation-key"></a>Proteggere il dispositivo tramite la chiave di attivazione

È consentito l'aggiunta del servizio Azure Stack Edge creato nella sottoscrizione di Azure solo a un dispositivo Azure Stack Edge Pro R o Azure Stack Edge autorizzato. Per autorizzare un dispositivo, è necessario usare una chiave di attivazione per attivare il dispositivo con il servizio Azure Stack Edge.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-activation-key.md)]

Per altre informazioni, vedere [ottenere una chiave di attivazione](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Proteggi il dispositivo tramite password

Le password assicurano che solo gli utenti autorizzati possano accedere ai dati. I dispositivi R Azure Stack Edge Pro vengono avviati in uno stato bloccato.

È possibile scegliere:

- Connettersi all'interfaccia utente Web locale del dispositivo tramite un browser e quindi specificare una password per accedere al dispositivo.
- Connettersi in remoto all'interfaccia di PowerShell del dispositivo tramite HTTP. La gestione remota è attivata per impostazione predefinita. La gestione remota è configurata anche per l'uso di just enough Administration (JEA) per limitare le operazioni che gli utenti possono eseguire. È quindi possibile specificare la password del dispositivo per accedere al dispositivo. Per altre informazioni, vedere [connettersi in remoto al dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md).
- L'utente perimetrale locale sul dispositivo ha accesso limitato al dispositivo per la configurazione iniziale e la risoluzione dei problemi. È possibile accedere ai carichi di lavoro di calcolo in esecuzione nel dispositivo, al trasferimento dei dati e all'archiviazione dal portale di Azure pubblico o per enti pubblici per la risorsa nel cloud.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-password-best-practices.md)]
- Usare l'interfaccia utente Web locale per [modificare la password](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#change-device-password). Se si modifica la password, assicurarsi di notificare a tutti gli utenti di accesso remoto in modo che non si verifichino problemi durante l'accesso.

### <a name="establish-trust-with-the-device-via-certificates"></a>Stabilire una relazione di trust con il dispositivo tramite certificati

Azure Stack dispositivo robusto Edge consente di importare i propri certificati e di installarli per l'uso per tutti gli endpoint pubblici. Per altre informazioni, vedere [caricare il certificato](azure-stack-edge-j-series-manage-certificates.md#upload-certificates). Per un elenco di tutti i certificati che possono essere installati nel dispositivo, vedere gestire i [certificati nel dispositivo](azure-stack-edge-j-series-manage-certificates.md).

- Quando si configura il calcolo nel dispositivo, vengono creati un dispositivo e un dispositivo IoT Edge. A questi dispositivi vengono assegnate automaticamente delle chiavi di accesso simmetriche. Per una protezione ottimale, queste chiavi vengono ruotate regolarmente tramite il servizio dell'hub IoT.

## <a name="protect-your-data"></a>Proteggere i dati

In questa sezione vengono descritte le funzionalità di sicurezza di che proteggono i dati archiviati e in transito.

### <a name="protect-data-at-rest"></a>Proteggere i dati inattivi

Tutti i dati inattivi sul dispositivo sono con crittografia doppia, l'accesso ai dati viene controllato e dopo la disattivazione del dispositivo i dati vengono cancellati in modo sicuro dai dischi dati.

#### <a name="double-encryption-of-data"></a>Doppia crittografia dei dati

I dati nei dischi sono protetti da due livelli di crittografia:

- Il primo livello di crittografia è la crittografia BitLocker XTS-AES a 256 bit sui volumi di dati.
- Il secondo livello è costituito dai dischi rigidi con crittografia incorporata.
- Il volume del sistema operativo ha BitLocker come singolo livello di crittografia.

> [!NOTE]
> Il disco del sistema operativo ha la crittografia del software BitLocker XTS-AES-256 a livello singolo.

Quando il dispositivo viene attivato, viene richiesto di salvare un file di chiave che contiene le chiavi di ripristino che consentono di ripristinare i dati nel dispositivo se il dispositivo non viene avviato. Il file contiene due chiavi:

- Una chiave recupera la configurazione del dispositivo nei volumi del sistema operativo.
<!-- - Second key is to unlock the BitLocker on the data disks. -->
- Il secondo tasto sblocca la crittografia hardware nei dischi dati.

> [!IMPORTANT]
> Salvare il file di chiave in una posizione sicura all'esterno del dispositivo stesso. Se il dispositivo non viene avviato e non si dispone della chiave, potrebbe causare la perdita di dati.

- Determinati scenari di ripristino richiederanno il file di chiave salvato. 
<!--- If a node isn't booting up, you will need to perform a node replacement. You will have the option to swap the data disks from the failed node to the new node. For a 4-node device, you won't need a key file. For a 1-node device, you will be prompted to provide a key file.-->

#### <a name="restricted-access-to-data"></a>Accesso limitato ai dati

L'accesso ai dati archiviati nelle condivisioni e negli account di archiviazione è limitato.

- I client SMB che accedono ai dati di condivisione necessitano di credenziali utente associate alla condivisione. Queste credenziali vengono definite al momento della creazione della condivisione.
- Ai client NFS che accedono a una condivisione è necessario aggiungere l'indirizzo IP in modo esplicito al momento della creazione della condivisione.
- Gli account di archiviazione perimetrali creati sul dispositivo sono locali e sono protetti dalla crittografia sui dischi dati. Gli account di archiviazione di Azure a cui sono mappati questi account di archiviazione perimetrale sono protetti dalla sottoscrizione e dalle chiavi di accesso alle archiviazione a 2 512 bit associate all'account di archiviazione perimetrale (queste chiavi sono diverse da quelle associate agli account di archiviazione di Azure). Per altre informazioni, vedere [proteggere i dati negli account di archiviazione](#protect-data-in-storage-accounts).
- BitLocker XTS-AES 256 bit Encryption viene usato per proteggere i dati locali.

#### <a name="secure-data-erasure"></a>Cancellazione dati protetta

Quando il dispositivo viene sottoposto a un ripristino rigido, viene eseguita una cancellazione sicura sul dispositivo. La cancellazione sicura esegue la cancellazione dei dati sui dischi usando il NIST SP 800-88r1 Purge.

### <a name="protect-data-in-flight"></a>Proteggere i dati in corso

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-data-flight.md)]

### <a name="protect-data-in-storage-accounts"></a>Proteggere i dati negli account di archiviazione

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-protect-data-storage-accounts.md)]

- Ruotare e [sincronizzare regolarmente le chiavi dell'account di archiviazione](azure-stack-edge-j-series-manage-storage-accounts.md) per proteggere l'account di archiviazione da utenti non autorizzati.

## <a name="manage-personal-information"></a>Gestisci informazioni personali

Il servizio Azure Stack Edge raccoglie informazioni personali negli scenari seguenti:

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-manage-personal-data.md)]

Per visualizzare l'elenco degli utenti che possono accedere o eliminare una condivisione, seguire i passaggi descritti in [gestire le condivisioni sul Azure stack Edge](azure-stack-edge-j-series-manage-shares.md).

Per ulteriori informazioni, consultare l'informativa sulla privacy Microsoft nel [Centro protezione](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Passaggi successivi

[Distribuire il dispositivo R di Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md)
