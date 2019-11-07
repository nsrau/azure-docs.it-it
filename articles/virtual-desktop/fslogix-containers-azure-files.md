---
title: File di contenitori del profilo FSLogix di desktop virtuali Windows-Azure
description: Questo articolo descrive i contenitori di profili FSLogix all'interno di un desktop virtuale di Windows e di file di Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: helohr
ms.openlocfilehash: cf4274931d83b51afb4a8ada7d1c9040ccce3c14
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606844"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>Contenitori di profili FSLogix e file di Azure

Il servizio desktop virtuale di Windows consiglia i contenitori del profilo FSLogix come soluzione di profilo utente. FSLogix è progettato per eseguire il roaming dei profili in ambienti di elaborazione remota, ad esempio desktop virtuale di Windows. Archivia un profilo utente completo in un singolo contenitore. Al momento dell'accesso, questo contenitore viene collegato dinamicamente all'ambiente di elaborazione usando un disco rigido virtuale (VHD) e un disco rigido virtuale Hyper-V supportati in modo nativo (VHDX). Il profilo utente è immediatamente disponibile e viene visualizzato nel sistema esattamente come un profilo utente nativo. Questo articolo descrive il modo in cui i contenitori di profili FSLogix usati con File di Azure funzione nel desktop virtuale di Windows.

>[!NOTE]
>Se si sta cercando materiale di confronto sulle diverse opzioni di archiviazione del contenitore del profilo FSLogix in Azure, vedere [Opzioni di archiviazione per i contenitori di profili FSLogix](store-fslogix-profile.md).

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
| **Dischi del profilo utente (UPD)** | Sì | Sì | Sì | Sì | Sì | No | Sì | Win 7 + | Sì | |
| **Profilo utente mobile (RUP), modalità manutenzione** | No | Sì | Sì | Sì | Sì| No | Sì | Win 7 + | No | |
| **Enterprise State Roaming (ESR)** | Sì | No | Sì | No | Vedere le note | Sì | No | Windows 10 | No | Funzioni nello SKU del server ma nessuna interfaccia utente di supporto |
| **Virtualizzazione dell'esperienza utente (UE-V)** | Sì | Sì | Sì | No | Sì | No | Sì | Win 7 + | No |  |
| **File Cloud OneDrive** | No | No | No | Sì | Vedere le note | Vedere le note  | Vedere le note | Win 10 RS3 | No | Non testato nello SKU del server. L'archiviazione back-end in Azure dipende dal client di sincronizzazione. Per l'archiviazione back-end locale è necessario un client di sincronizzazione. |

#### <a name="performance"></a>Prestazioni

UPD richiede [spazi di archiviazione diretta (S2D)](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment) per soddisfare i requisiti di prestazioni. UPD usa il protocollo SMB (Server Message Block). Il profilo viene copiato nella macchina virtuale in cui viene registrato l'utente. UPD con S2D è la soluzione consigliata per desktop virtuale di Windows.  

#### <a name="cost"></a>Costi

Sebbene i cluster S2D raggiungano le prestazioni necessarie, il costo è costoso per i clienti aziendali, ma è particolarmente costoso per i clienti di piccole e medie imprese (SMB). Per questa soluzione, le aziende pagano i dischi di archiviazione, insieme al costo delle macchine virtuali che usano i dischi per una condivisione.

#### <a name="administrative-overhead"></a>Overhead amministrativo

I cluster S2D richiedono un sistema operativo con patch, aggiornamento e manutenzione in uno stato sicuro. Questi processi e la complessità della configurazione del ripristino di emergenza di S2D rendono S2D fattibile solo per le aziende con un personale IT dedicato.

## <a name="fslogix-profile-containers"></a>Contenitori del profilo FSLogix

Il 19 novembre 2018 Microsoft ha [acquisito FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/). FSLogix risolve molti problemi relativi ai contenitori di profili. La chiave tra di essi è:

- **Prestazioni:** I [contenitori del profilo FSLogix](https://fslogix.com/products/profile-containers) sono prestazioni elevate e risoluzione dei problemi di prestazioni con modalità di scambio memorizzata nella cache storicamente bloccata.
- **OneDrive:** Senza i contenitori del profilo FSLogix, OneDrive for business non è supportato in ambienti RDSH o VDI non persistenti. [Le procedure consigliate per OneDrive for business e FSLogix](https://fslogix.com/products/technical-faqs/284-onedrive-for-business-and-fslogix-best-practices) descrivono il modo in cui interagiscono. Per altre informazioni, vedere [usare il client di sincronizzazione su desktop virtuali](https://docs.microsoft.com/deployoffice/rds-onedrive-business-vdi).
- **Cartelle aggiuntive:** FSLogix offre la possibilità di estendere i profili utente per includere cartelle aggiuntive.

Dall'acquisizione, Microsoft ha iniziato a sostituire le soluzioni del profilo utente esistenti, ad esempio UPD, con i contenitori del profilo FSLogix.

## <a name="azure-files-integration-with-azure-active-directory-domain-service"></a>Integrazione di File di Azure con Azure Active Directory servizio del dominio

Le prestazioni e le funzionalità dei contenitori del profilo FSLogix sfruttano il cloud. Il 7 agosto 2019, i file di Microsoft Azure annunciano la disponibilità a livello generale dell' [autenticazione file di Azure con Azure Active Directory Domain Service (ad DS)](https://docs.microsoft.com/en-us/azure/storage/files/storage-files-active-directory-overview). Affrontando il sovraccarico amministrativo e costi, File di Azure con l'autenticazione di Azure AD DS è una soluzione Premium per i profili utente nel servizio desktop virtuale di Windows.

## <a name="best-practices-for-windows-virtual-desktop"></a>Procedure consigliate per desktop virtuale Windows

Desktop virtuale di Windows offre il controllo completo sulle dimensioni, il tipo e il numero di macchine virtuali utilizzate dai clienti. Per ulteriori informazioni, vedere informazioni sul [desktop virtuale di Windows](overview.md).

Per assicurarsi che l'ambiente desktop virtuale di Windows segua le procedure consigliate:

- File di Azure account di archiviazione deve trovarsi nella stessa area delle VM host sessione.
- File di Azure le autorizzazioni devono corrispondere alle autorizzazioni descritte nei [contenitori dei profili dei requisiti](https://docs.microsoft.com/fslogix/overview#requirements).
- Ogni pool di host deve essere compilato con la stessa macchina virtuale di tipo e dimensioni basata sulla stessa immagine master.
- Ogni macchina virtuale del pool host deve trovarsi nello stesso gruppo di risorse per agevolare la gestione, la scalabilità e l'aggiornamento.
- Per ottenere prestazioni ottimali, la soluzione di archiviazione e il contenitore del profilo FSLogix devono trovarsi nello stesso percorso data center.
- L'account di archiviazione contenente l'immagine master deve trovarsi nella stessa area e nella stessa sottoscrizione in cui è in corso il provisioning delle macchine virtuali.

## <a name="next-steps"></a>Passaggi successivi

Utilizzare le seguenti guide per configurare un ambiente desktop virtuale di Windows.

- Per iniziare a creare la soluzione di virtualizzazione desktop, vedere [creare un tenant in un desktop virtuale di Windows](tenant-setup-azure-active-directory.md).
- Per creare un pool di host nel tenant del desktop virtuale Windows, vedere [creare un pool di host con Azure Marketplace](create-host-pools-azure-marketplace.md).
- Per configurare condivisioni file completamente gestite nel cloud, vedere [configurare file di Azure condivisione](/azure/storage/files/storage-files-active-directory-enable).
- Per configurare i contenitori di profili FSLogix, vedere [creare un contenitore di profili per un pool host usando una condivisione file](create-host-pools-user-profile.md).
- Per assegnare gli utenti a un pool di host, vedere [gestire i gruppi di app per desktop virtuale di Windows](manage-app-groups.md).
- Per accedere alle risorse del desktop virtuale di Windows da un Web browser, vedere [connettersi al desktop virtuale di Windows](connect-web.md).
