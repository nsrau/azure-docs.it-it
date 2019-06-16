---
title: FSLogix profilo contenitori e file di Azure in Windows Desktop virtuale - Azure
description: Questo articolo descrive i contenitori di profilo FSLogix all'interno dei file di Desktop virtuale Windows e Azure.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: v-chjenk
ms.openlocfilehash: b3032aa796b3c79572bbf8b2beb85efc252ff73b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66497529"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>Contenitori di profili FSLogix e file di Azure

Il servizio anteprima di Desktop virtuale Windows consiglia contenitori profilo FSLogix come una soluzione del profilo utente. FSLogix è progettato per il roaming profili in ambienti di elaborazione remota, ad esempio, Windows Desktop virtuale. Archivia un profilo utente completo in un singolo contenitore. All'accesso, questo contenitore è collegato in modo dinamico l'ambiente di elaborazione utilizzando supportati in modalità nativa di disco rigido virtuale (VHD) e Hyper-V Virtual Hard disk (VHDX). Il profilo utente è immediatamente disponibile e viene visualizzato nel sistema esattamente come un profilo utente native.

In questo articolo descriveremo contenitori profilo FSLogix usata con file di Azure. Le informazioni sono nel contesto di Desktop virtuale Windows, che è stata [annunciati su 3/21](https://www.microsoft.com/microsoft-365/blog/2019/03/21/windows-virtual-desktop-public-preview/).

## <a name="user-profiles"></a>Profili utente

Un profilo utente contiene gli elementi di dati su un singolo utente, incluse le informazioni di configurazione, ad esempio le impostazioni del desktop, le connessioni di rete persistente e le impostazioni dell'applicazione. Per impostazione predefinita, Windows crea un profilo utente locale che è strettamente integrato con il sistema operativo.

Un profilo utente remoto fornisce una partizione tra il sistema operativo e dati utente. Consente al sistema operativo di essere sostituita o modificata senza influire sui dati utente. Host sessione Desktop remoto (RDSH) e infrastrutture VDI (Virtual Desktop), il sistema operativo può essere sostituito per i motivi seguenti:

- Un aggiornamento del sistema operativo
- Sostituzione di una macchina virtuale esistente (VM)
- Un utente da parte di un ambiente VDI oppure host sessione Desktop remoto (non permanenti) in pool

I prodotti Microsoft operano con diverse tecnologie per i profili utente remoto, tra cui queste tecnologie:
- I profili utente mobili (Reperire)
- Dischi dei profili utente (UPD)
- Stato Enterprise roaming PVR)

Disco profili utente e profili mobili sono le tecnologie più ampiamente usate per i profili utente in ambienti Host sessione Desktop remoto (RDSH) e disco rigido virtuale (VHD).

### <a name="challenges-with-previous-user-profile-technologies"></a>Problemi con le tecnologie di profilo utente precedente

Soluzioni Microsoft esistenti e legacy per i profili utente fornito con diverse sfide. Nessuna soluzione precedente gestite tutte le esigenze di profilo utente che sono dotate di un ambiente VDI oppure host sessione Desktop remoto. Ad esempio, disco profili utente non può gestire file di grandi dimensioni OST e Reperire non rende persistenti le impostazioni di moderni.

#### <a name="functionality"></a>Funzionalità

La tabella seguente illustra i vantaggi e limitazioni delle tecnologie di profilo utente precedente.

| Tecnologia | Impostazioni moderne | Impostazioni di Win32 | Impostazioni del sistema operativo | Dati utente | Supportato nella SKU server | Archiviazione back-end in Azure | Archiviazione back-end in locale | Supporto della versione | Per gli accessi successivi nel tempo |Note|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **Dischi dei profili utente (UPD)** | Yes | Sì | Sì | Sì | Sì | No | Yes | Win 7+ | Yes | |
| **Roaming del profilo utente (Reperire), la modalità di manutenzione** | No | Yes | Sì | Sì | Sì| No | Yes | Win 7+ | No | |
| **Enterprise State Roaming (ESR)** | Yes | No | Sì | No | Vedere le note | Yes | No | Windows 10 | No | Funzioni nella SKU server ma nessun supporto dell'interfaccia utente |
| **User Experience Virtualization (UE-V)** | Yes | Sì | Sì | No | Sì | No | Yes | Win 7+ | No |  |
| **File in cloud OneDrive** | No | No | No | Yes | Vedere le note | Vedere le note  | Vedere le note | Win 10 RS3 | No | Non testato nel server di SKU. Back-end archiviazione di Azure dipende dal client di sincronizzazione. È necessario un client di sincronizzazione archiviazione back-end dall'ambiente locale. |

#### <a name="performance"></a>Prestazioni

Richiede l'UPD [spazi di archiviazione è diretta (S2D)](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment) per soddisfare requisiti di prestazioni. UPD Usa il protocollo Server Message Block (SMB). Copia il profilo per la macchina virtuale in cui l'utente è connesso. UPD con S2D è la soluzione consigliato il team di servizi desktop remoto per Windows Desktop virtuale durante l'anteprima del servizio.  

#### <a name="cost"></a>Costi

Mentre i cluster di S2D ottenere le prestazioni necessarie, il costo è costoso per i clienti aziendali, ma soprattutto costose per i clienti di piccole e medie imprese (SMB). Per questa soluzione, le aziende pagano per i dischi di archiviazione premium, insieme ai costi delle macchine virtuali che usano i dischi per una condivisione.

#### <a name="administrative-overhead"></a>Sovraccarico amministrativo

I cluster di S2D richiedono un sistema operativo che viene corretto, aggiornato e mantenuto in uno stato protetto. Questi processi e la complessità della configurazione di ripristino di emergenza S2D rendono praticabili solo per le aziende con un personale IT dedicato e S2D.

## <a name="fslogix-profile-containers"></a>Contenitori di profilo FSLogix

19 novembre 2018 [Microsoft ha acquisito FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/). FSLogix risolve molti problemi di contenitore di profilo. Chiave tra di essi sono:

- **Prestazioni:** Il [contenitori profilo FSLogix](https://fslogix.com/products/profile-containers) prestazioni elevate e modalità di cache di risolvere i problemi di prestazioni che storicamente sono bloccati.
- **OneDrive:** Senza i contenitori profilo FSLogix OneDrive for Business non è supportato in ambienti host sessione Desktop remoto o VDI non persistente. [OneDrive for Business e FSLogix consigliate](https://fslogix.com/products/technical-faqs/284-onedrive-for-business-and-fslogix-best-practices) descrive come interagiscono. Per altre informazioni, vedere [usare il client di sincronizzazione in desktop virtuali](https://docs.microsoft.com/deployoffice/rds-onedrive-business-vdi).
- **Cartelle aggiuntive:** FSLogix offre la possibilità di estendere i profili utente in modo da includere altre cartelle.

Dopo l'acquisizione, Microsoft avviato la sostituzione di soluzioni di profilo utente esistente, come UPD, con i contenitori di profilo FSLogix.

## <a name="azure-files-integration-with-azure-active-directory"></a>Integrazione di Azure i file con Azure Active Directory

Funzionalità e prestazioni dei contenitori profilo FSLogix sfruttare i vantaggi del cloud. 24 settembre 2018, i file di Azure di Microsoft ha annunciato un'anteprima pubblica delle [file di Azure che supportano l'autenticazione Azure Active Directory](https://azure.microsoft.com/blog/azure-active-directory-integration-for-smb-access-now-in-public-preview/). Incentrandosi costi e il sovraccarico amministrativo, i file di Azure con l'autenticazione di Azure Active Directory è una soluzione di premium per i profili utente nel nuovo servizio Windows Desktop virtuale.

## <a name="best-practices-for-windows-virtual-desktop"></a>Procedure consigliate per Windows Desktop virtuale

Windows Desktop virtuale offre controllo completo sulla dimensione, tipo e numero di macchine virtuali che vengono usate dai clienti. Per altre informazioni, vedere [What ' s Windows Virtual Desktop Preview?](https://docs.microsoft.com/azure/virtual-desktop/overview).

Per verificare che il Desktop virtuale Windows ambiente segue le procedure consigliate:

- Account di archiviazione file Azure deve essere nella stessa area dell'host di sessione le macchine virtuali.
- Autorizzazioni di file di Azure devono corrispondere a autorizzazioni descritte in [requisiti - contenitori profilo](https://docs.fslogix.com/display/20170529/Requirements+-+Profile+Containers).
- Ogni pool di host deve essere compilato dello stesso tipo e dimensioni della macchina virtuale in base all'immagine master stessa.
- Ogni pool di host macchina virtuale deve essere nello stesso gruppo di risorse per agevolare gestione, scalabilità e l'aggiornamento.
- Per ottenere prestazioni ottimali, la soluzione di archiviazione e il FSLogix contenitore profilo deve essere gli stessi dati center location.
- L'account di archiviazione che contiene l'immagine master deve essere nella stessa area e sottoscrizione in cui le macchine virtuali sono sottoposto a provisioning.

## <a name="next-steps"></a>Passaggi successivi

Usare le istruzioni seguenti per configurare un ambiente di Desktop virtuale Windows.

- Per iniziare a compilare la soluzione di virtualizzazione desktop, vedere [creare un tenant in Windows Desktop virtuale](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory).
- Per creare un pool di host all'interno del tenant di Desktop virtuale Windows, vedere [creare un pool di host con Azure Marketplace](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace).
- Per configurare completamente gestito condivisioni file nel cloud, vedere [configurare la condivisione di file di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-enable).
- Per configurare i contenitori di profilo FSLogix, vedere [configurare una condivisione di profilo utente per un pool di host](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-user-profile).
- Per assegnare gli utenti a un pool di host, vedere [gestire i gruppi di app per Windows Desktop virtuale](https://docs.microsoft.com/azure/virtual-desktop/manage-app-groups).
- Per accedere alle risorse di Desktop virtuale Windows da un web browser, vedere [Connetti a Windows Desktop virtuale](https://docs.microsoft.com/azure/virtual-desktop/connect-web).
