---
title: File dei contenitori di profili FSLogix di Windows Virtual Desktop - Azure
description: Questo articolo descrive i contenitori di profili FSLogix all'interno dei file di Desktop virtuale windows e Azure.This article describes FSLogix profile containers within Windows Virtual Desktop and Azure files.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1dc5d54fa24217c91e14a8f37e092888b2bb6474
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127872"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>Contenitori di profili FSLogix e file di Azure

Il servizio Desktop virtuale di Windows consiglia i contenitori dei profili FSLogix come soluzione di profilo utente. FSLogix è progettato per eseguire il roaming dei profili in ambienti di elaborazione remota, ad esempio Windows Virtual Desktop. Memorizza un profilo utente completo in un singolo contenitore. All'accesso, questo contenitore è collegato dinamicamente all'ambiente di elaborazione utilizzando disco rigido virtuale (VHD) supportato in modo nativo e disco rigido virtuale Hyper-V (VHDX). Il profilo utente è immediatamente disponibile e viene visualizzato nel sistema esattamente come un profilo utente nativo. Questo articolo descrive come fungo fsLogix usare con la funzione File di Azure in Windows Virtual Desktop.This article describes how FSLogix profile containers used with Azure Files function in Windows Virtual Desktop.

>[!NOTE]
>Per informazioni sul confronto tra le diverse opzioni di archiviazione del contenitore di profili FSLogix in Azure, vedere Opzioni di archiviazione per i [contenitori di profili FSLogix.](store-fslogix-profile.md)

## <a name="user-profiles"></a>Profili utente

Un profilo utente contiene elementi di dati su un individuo, incluse le informazioni di configurazione come le impostazioni del desktop, le connessioni di rete permanenti e le impostazioni dell'applicazione. Per impostazione predefinita, Windows crea un profilo utente locale strettamente integrato con il sistema operativo.

Un profilo utente remoto fornisce una partizione tra i dati utente e il sistema operativo. Consente di sostituire o modificare il sistema operativo senza influire sui dati utente. In Remote Desktop Session Host (RDSH) e Virtual Desktop Infrastructures (VDI), il sistema operativo può essere sostituito per i seguenti motivi:

- Un aggiornamento del sistema operativo
- Sostituzione di una macchina virtuale esistente
- Un utente che fa parte di un ambiente RDSH o VDI in pool (non persistente)

I prodotti Microsoft funzionano con diverse tecnologie per i profili utente remoti, incluse le tecnologie seguenti:
- Profili utente mobili (RUP)
- Dischi profilo utente (UPD)
- Roaming dello stato aziendale (PSIR)

UPD e RUP sono le tecnologie più utilizzate per i profili utente negli ambienti Remote Desktop Session Host (RDSH) e Disco rigido virtuale (VHD).

### <a name="challenges-with-previous-user-profile-technologies"></a>Sfide con le tecnologie precedenti del profilo utente

Le soluzioni Microsoft esistenti e legacy per i profili utente hanno suscitato varie sfide. Nessuna soluzione precedente ha gestito tutte le esigenze del profilo utente associate a un ambiente RDSH o VDI. Ad esempio, UPD non è in grado di gestire file OST di grandi dimensioni e RUP non mantiene le impostazioni moderne.

#### <a name="functionality"></a>Funzionalità

Nella tabella seguente vengono illustrati i vantaggi e le limitazioni delle tecnologie dei profili utente precedenti.

| Tecnologia | Impostazioni moderne | Impostazioni Win32 | Impostazioni del sistema operativo | Dati utente | Supportato su SKU server | Back-end storage on Azure | Archiviazione back-end in locale | Supporto versione | Tempo di accesso successivo |Note|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **Dischi profilo utente (UPD)** | Sì | Sì | Sì | Sì | Sì | No | Sì | Vinci 7 anni e più | Sì | |
| **Profilo utente mobile (RUP), modalità di manutenzione** | No | Sì | Sì | Sì | Sì| No | Sì | Vinci 7 anni e più | No | |
| **Enterprise State Roaming (ESR)** | Sì | No | Sì | No | Vedere le note | Sì | No | Windows 10 | No | Funzioni sullo SKU del server ma nessuna interfaccia utente di supporto |
| **User Experience Virtualization (UE-V)** | Sì | Sì | Sì | No | Sì | No | Sì | Vinci 7 anni e più | No |  |
| **File cloud di OneDrive** | No | No | No | Sì | Vedere le note | Vedere le note  | Vedere Note | Vinci 10 RS3 | No | Non testato sullo SKU del server. L'archiviazione back-end in Azure dipende dal client di sincronizzazione. L'archiviazione back-end locale richiede un client di sincronizzazione. |

#### <a name="performance"></a>Prestazioni

UPD richiede [Storage Spaces Direct (S2D)](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) per soddisfare i requisiti di prestazioni. UPD utilizza il protocollo SMB (Server Message Block). Copia il profilo nella macchina virtuale in cui viene registrato l'utente. UPD con S2D è la soluzione che consigliamo per Windows Virtual Desktop.  

#### <a name="cost"></a>Costi

Sebbene i cluster S2D raggiungano le prestazioni necessarie, il costo è costoso per i clienti aziendali, ma particolarmente costoso per i clienti di piccole e medie imprese (SMB). Per questa soluzione, le aziende pagano per i dischi di archiviazione, insieme al costo delle macchine virtuali che usano i dischi per una condivisione.

#### <a name="administrative-overhead"></a>Sovraccarico amministrativo

I cluster S2D richiedono un sistema operativo con patch, aggiornamento e manutenzione in uno stato sicuro. Questi processi e la complessità della configurazione del disaster recovery S2D rendono S2D fattibile solo per le aziende con personale IT dedicato.

## <a name="fslogix-profile-containers"></a>Contenitori di profili FSLogix

Il 19 novembre 2018, [Microsoft ha acquisito FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/). FSLogix affronta molte sfide legate al contenitore di profili. I principali sono:

- **Prestazioni:** I contenitori di [profili FSLogix](/fslogix/configure-profile-container-tutorial/) sono ad alte prestazioni e risolvere i problemi di prestazioni che storicamente hanno bloccato la modalità di scambio nella cache.
- **OneDrive:** Senza i contenitori di profili FSLogix, OneDrive for Business non è supportato in ambienti RDSH o VDI non persistenti. Le procedure consigliate di [OneDrive for Business e FSLogix](/fslogix/overview/) descrivono come interagiscono. Per ulteriori informazioni, vedere Utilizzare il client di [sincronizzazione sui desktop virtuali.](/deployoffice/rds-onedrive-business-vdi/)
- **Cartelle aggiuntive:** FSLogix offre la possibilità di estendere i profili utente per includere cartelle aggiuntive.

Dall'acquisizione, Microsoft ha iniziato a sostituire le soluzioni dei profili utente esistenti, come UPD, con i contenitori di profili FSLogix.

## <a name="azure-files-integration-with-azure-active-directory-domain-service"></a>Azure Files integration with Azure Active Directory Domain Service

Le prestazioni e le funzionalità dei contenitori di profili FSLogix sfruttano il cloud. Il 7 agosto 2019, File di Microsoft Azure ha annunciato la disponibilità generale dell'autenticazione dei file di Azure con Il servizio di [dominio Azure Active Directory.](../storage/files/storage-files-active-directory-overview.md) Risolvendo sia i costi che il sovraccarico amministrativo, File di Azure con l'autenticazione di Azure AD DS è una soluzione premium per i profili utente nel servizio Desktop virtuale di Windows.By Addressing both cost and administrative overhead, Azure Files with Azure AD DS Authentication is a premium solution for user profiles in the Windows Virtual Desktop service.

## <a name="best-practices-for-windows-virtual-desktop"></a>Procedure consigliate per Windows Virtual Desktop

Windows Virtual Desktop offre il controllo completo su dimensioni, tipo e numero di macchine virtuali utilizzate dai clienti. Per ulteriori informazioni, vedere [Che cos'è Desktop virtuale](overview.md)di Windows? .

Per garantire che l'ambiente Windows Virtual Desktop segue le procedure consigliate:

- L'account di archiviazione File di Azure deve trovarsi nella stessa area delle macchine virtuali host della sessione.
- Le autorizzazioni File di Azure devono corrispondere alle autorizzazioni descritte in [Requisiti - Contenitori di profili](/fslogix/fslogix-storage-config-ht).
- Ogni pool host deve essere compilato con lo stesso tipo e dimensione VM in base alla stessa immagine master.
- Ogni macchina virtuale del pool host deve trovarsi nello stesso gruppo di risorse per facilitare la gestione, la scalabilità e l'aggiornamento.
- Per ottenere prestazioni ottimali, la soluzione di archiviazione e il contenitore di profili FSLogix devono trovarsi nella stessa posizione del data center.
- L'account di archiviazione contenente l'immagine master deve trovarsi nella stessa area e sottoscrizione in cui viene eseguito il provisioning delle macchine virtuali.

## <a name="next-steps"></a>Passaggi successivi

Utilizzare le guide seguenti per configurare un ambiente Windows Virtual Desktop.

- Per iniziare a creare la soluzione di virtualizzazione desktop, vedere [Creare un tenant in Windows Virtual Desktop.](tenant-setup-azure-active-directory.md)
- Per creare un pool host all'interno del tenant di Windows Virtual Desktop, vedere [Creare un pool di host con Azure Marketplace.](create-host-pools-azure-marketplace.md)
- Per configurare condivisioni file completamente gestite nel cloud, vedere Configurare la [condivisione file](/azure/storage/files/storage-files-active-directory-enable/)di Azure .
- Per configurare i contenitori di profili FSLogix, vedere Creare un contenitore di profili [per un pool di host utilizzando una condivisione file.](create-host-pools-user-profile.md)
- Per assegnare utenti a un pool host, vedere Gestire gruppi di [app per Desktop virtuale di Windows.](manage-app-groups.md)
- Per accedere alle risorse di Windows Virtual Desktop da un Web browser, vedere [Connettersi a Windows Virtual Desktop](connect-web.md).
