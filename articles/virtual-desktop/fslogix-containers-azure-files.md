---
title: Contenitori e File di Azure del profilo FSLogix nel desktop virtuale di Windows-Azure
description: Questo articolo descrive i contenitori di profili FSLogix all'interno di un desktop virtuale di Windows e di file di Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: helohr
ms.openlocfilehash: c01e138c8afcdd59fcb0c87f189d98bec10e16d7
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828134"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>Contenitori di profili FSLogix e file di Azure

Il servizio di anteprima desktop virtuale di Windows consiglia i contenitori di profili FSLogix come soluzione di profilo utente. FSLogix è progettato per eseguire il roaming dei profili in ambienti di elaborazione remota, ad esempio desktop virtuale di Windows. Archivia un profilo utente completo in un singolo contenitore. Al momento dell'accesso, questo contenitore viene collegato dinamicamente all'ambiente di elaborazione usando un disco rigido virtuale (VHD) e un disco rigido virtuale Hyper-V supportati in modo nativo (VHDX). Il profilo utente è immediatamente disponibile e viene visualizzato nel sistema esattamente come un profilo utente nativo.

In questo articolo verranno descritti i contenitori di profili FSLogix usati con File di Azure. Le informazioni sono contenute nel contesto di desktop virtuale di Windows, [annunciato il 3/21](https://www.microsoft.com/microsoft-365/blog/2019/03/21/windows-virtual-desktop-public-preview/).

## <a name="user-profiles"></a>Profili utente

Un profilo utente contiene elementi dati relativi a un singolo utente, incluse le informazioni di configurazione come le impostazioni del desktop, le connessioni di rete permanenti e le impostazioni dell'applicazione. Per impostazione predefinita, in Windows viene creato un profilo utente locale strettamente integrato con il sistema operativo.

Un profilo utente remoto fornisce una partizione tra i dati utente e il sistema operativo. Consente di sostituire o modificare il sistema operativo senza influire sui dati dell'utente. In host sessione Desktop remoto (RDSH) e Virtual Desktop infrastructures (VDI), il sistema operativo può essere sostituito per i motivi seguenti:

- Aggiornamento del sistema operativo
- Sostituzione di una macchina virtuale (VM) esistente
- Un utente che fa parte di un ambiente RDSH o VDI in pool (non persistente)

I prodotti Microsoft operano con diverse tecnologie per i profili utente remoti, incluse le tecnologie seguenti:
- Profili utente mobili (RUP)
- Dischi del profilo utente (UPD)
- ESR (Enterprise state roaming)

UPD e RUP sono le tecnologie più diffuse per i profili utente in ambienti host sessione Desktop remoto (RDSH) e dischi rigidi virtuali (VHD).

### <a name="challenges-with-previous-user-profile-technologies"></a>Problemi con le tecnologie dei profili utente precedenti

Le soluzioni Microsoft esistenti e legacy per i profili utente sono state affrontate con varie esigenze. Nessuna soluzione precedente ha gestito tutte le esigenze del profilo utente disponibili con un ambiente RDSH o VDI. Ad esempio, UPD non è in grado di gestire file OST di grandi dimensioni e RUP non mantiene le impostazioni moderne.

#### <a name="functionality"></a>Funzionalità

La tabella seguente illustra i vantaggi e le limitazioni delle tecnologie dei profili utente precedenti.

| Tecnologia | Impostazioni moderne | Impostazioni Win32 | Impostazioni del sistema operativo | Dati utente | Supportato nello SKU del server | Archiviazione back-end in Azure | Archiviazione back-end locale | Supporto della versione | Ora di accesso successiva |Note|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **Dischi del profilo utente (UPD)** | Sì | Sì | Sì | Sì | Sì | No | Yes | Win 7 + | Sì | |
| **Profilo utente mobile (RUP), modalità manutenzione** | No | Yes | Sì | Sì | Sì| No | Yes | Win 7 + | No | |
| **Enterprise State Roaming (ESR)** | Yes | No | Sì | No | Vedere le note | Yes | No | Windows 10 | No | Funzioni nello SKU del server ma nessuna interfaccia utente di supporto |
| **Virtualizzazione dell'esperienza utente (UE-V)** | Yes | Sì | Sì | No | Sì | No | Sì | Win 7 + | No |  |
| **File Cloud OneDrive** | No | No | No | Sì | Vedere le note | Vedere le note  | Vedere le note | Win 10 RS3 | No | Non testato nello SKU del server. L'archiviazione back-end in Azure dipende dal client di sincronizzazione. Per l'archiviazione back-end locale è necessario un client di sincronizzazione. |

#### <a name="performance"></a>Prestazioni

UPD richiede [spazi di archiviazione diretta (S2D)](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment) per soddisfare i requisiti di prestazioni. UPD usa il protocollo SMB (Server Message Block). Il profilo viene copiato nella macchina virtuale in cui viene registrato l'utente. UPD con S2D è la soluzione consigliata dal team RDS per desktop virtuale Windows durante l'anteprima del servizio.  

#### <a name="cost"></a>Costi

Sebbene i cluster S2D raggiungano le prestazioni necessarie, il costo è costoso per i clienti aziendali, ma è particolarmente costoso per i clienti di piccole e medie imprese (SMB). Per questa soluzione, le aziende pagano i dischi di archiviazione, insieme al costo delle macchine virtuali che usano i dischi per una condivisione.

#### <a name="administrative-overhead"></a>Overhead amministrativo

I cluster S2D richiedono un sistema operativo con patch, aggiornamento e manutenzione in uno stato sicuro. Questi processi e la complessità della configurazione del ripristino di emergenza di S2D rendono S2D fattibile solo per le aziende con un personale IT dedicato.

## <a name="fslogix-profile-containers"></a>Contenitori del profilo FSLogix

Il 19 novembre 2018 Microsoft ha [acquisito FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/). FSLogix risolve molti problemi relativi ai contenitori di profili. La chiave tra di essi è:

- **Prestazioni:** I [contenitori del profilo FSLogix](https://fslogix.com/products/profile-containers) sono prestazioni elevate e risoluzione dei problemi di prestazioni con modalità di scambio memorizzata nella cache storicamente bloccata.
- **OneDrive** Senza i contenitori del profilo FSLogix, OneDrive for business non è supportato in ambienti RDSH o VDI non persistenti. [Le procedure consigliate per OneDrive for business e FSLogix](https://fslogix.com/products/technical-faqs/284-onedrive-for-business-and-fslogix-best-practices) descrivono il modo in cui interagiscono. Per altre informazioni, vedere [usare il client di sincronizzazione su desktop virtuali](https://docs.microsoft.com/deployoffice/rds-onedrive-business-vdi).
- **Cartelle aggiuntive:** FSLogix offre la possibilità di estendere i profili utente per includere cartelle aggiuntive.

Dall'acquisizione, Microsoft ha iniziato a sostituire le soluzioni del profilo utente esistenti, ad esempio UPD, con i contenitori del profilo FSLogix.

## <a name="azure-files-integration-with-azure-active-directory"></a>Integrazione di File di Azure con Azure Active Directory

Le prestazioni e le funzionalità dei contenitori del profilo FSLogix sfruttano il cloud. Il 24 settembre 2018, i file di Microsoft Azure hanno annunciato un'anteprima pubblica di [file di Azure che supportano l'autenticazione Azure Active Directory](https://azure.microsoft.com/blog/azure-active-directory-integration-for-smb-access-now-in-public-preview/). Affrontando il sovraccarico amministrativo e costi, File di Azure con l'autenticazione Azure Active Directory è una soluzione Premium per i profili utente nel nuovo servizio desktop virtuale di Windows.

## <a name="best-practices-for-windows-virtual-desktop"></a>Procedure consigliate per desktop virtuale Windows

Desktop virtuale di Windows offre il controllo completo sulle dimensioni, il tipo e il numero di macchine virtuali utilizzate dai clienti. Per ulteriori informazioni, vedere [che cos'è l'anteprima di desktop virtuale Windows?](https://docs.microsoft.com/azure/virtual-desktop/overview).

Per assicurarsi che l'ambiente desktop virtuale di Windows segua le procedure consigliate:

- File di Azure account di archiviazione deve trovarsi nella stessa area delle VM host sessione.
- File di Azure le autorizzazioni devono corrispondere alle autorizzazioni descritte nei [contenitori dei profili dei requisiti](https://docs.fslogix.com/display/20170529/Requirements+-+Profile+Containers).
- Ogni pool di host deve essere compilato con la stessa macchina virtuale di tipo e dimensioni basata sulla stessa immagine master.
- Ogni macchina virtuale del pool host deve trovarsi nello stesso gruppo di risorse per agevolare la gestione, la scalabilità e l'aggiornamento.
- Per ottenere prestazioni ottimali, la soluzione di archiviazione e il contenitore del profilo FSLogix devono trovarsi nello stesso percorso data center.
- L'account di archiviazione contenente l'immagine master deve trovarsi nella stessa area e nella stessa sottoscrizione in cui è in corso il provisioning delle macchine virtuali.

## <a name="next-steps"></a>Passaggi successivi

Utilizzare le istruzioni seguenti per configurare un ambiente desktop virtuale di Windows.

- Per iniziare a creare la soluzione di virtualizzazione desktop, vedere [creare un tenant in un desktop virtuale di Windows](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory).
- Per creare un pool di host nel tenant del desktop virtuale Windows, vedere [creare un pool di host con Azure Marketplace](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace).
- Per configurare condivisioni file completamente gestite nel cloud, vedere [configurare file di Azure condivisione](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-enable).
- Per configurare i contenitori di profili FSLogix, vedere [configurare una condivisione del profilo utente per un pool host](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-user-profile).
- Per assegnare gli utenti a un pool di host, vedere [gestire i gruppi di app per desktop virtuale di Windows](https://docs.microsoft.com/azure/virtual-desktop/manage-app-groups).
- Per accedere alle risorse del desktop virtuale di Windows da un Web browser, vedere [connettersi al desktop virtuale di Windows](https://docs.microsoft.com/azure/virtual-desktop/connect-web).
