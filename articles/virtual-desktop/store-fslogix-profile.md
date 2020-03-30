---
title: Contenitore di profili Storage FSLogix Windows Virtual Desktop - Azure
description: Opzioni per l'archiviazione del profilo FSLogix di Windows Virtual Desktop in Archiviazione di Azure.Options for storing your Windows Virtual Desktop FSLogix profile on Azure Storage.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 71ba24784dee7771acbe19bf0261c7dc02478b24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127527"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Opzioni di archiviazione per i contenitori di profili FSLogix in Windows Virtual Desktop

Azure offre più soluzioni di archiviazione che è possibile usare per archiviare il contenitore di profili FSLogix.Azure offers multiple storage solutions that you can use to store your FSLogix profile container. Questo articolo confronta le soluzioni di archiviazione offerte da Azure per i contenitori dei profili utente FSLogix di Windows Desktop virtuale.

Windows Virtual Desktop offre i contenitori dei profili FSLogix come soluzione di profilo utente consigliata. FSLogix è progettato per eseguire il roaming dei profili in ambienti di elaborazione remota, ad esempio Windows Virtual Desktop. All'accesso, questo contenitore è collegato dinamicamente all'ambiente di elaborazione utilizzando un disco rigido virtuale (VHD) supportato in modo nativo e un disco rigido virtuale Hyper-V (VHDX). Il profilo utente è immediatamente disponibile e viene visualizzato nel sistema esattamente come un profilo utente nativo.

Le tabelle seguenti confrontano le soluzioni di archiviazione offerte da Archiviazione di Azure per i profili del contenitore di profili FSLogix di Windows Desktop virtuale.

## <a name="azure-platform-details"></a>Dettagli della piattaforma AzureAzure platform details

|Funzionalità|File di Azure|Azure NetApp Files|Spazi di archiviazione diretta|
|--------|-----------|------------------|---------------------|
|Caso d'uso|Scopo generico|Ultra prestazioni o migrazione da NetApp locale|Multipiattaforma|
|Servizio piattaforma|Sì, soluzione nativa di AzureYes, Azure-native solution|Sì, soluzione nativa di AzureYes, Azure-native solution|No, autogestito|
|Disponibilità a livello di area|Tutte le aree|[Selezionare le regioni](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|Tutte le aree|
|Ridondanza|Ridondante localmente ridondante/zona/geo-ridondante|Ridondante a livello locale|Ridondante localmente ridondante/zona/geo-ridondante|
|Livelli e prestazioni|Standard<br>Premium<br>Fino a 100k IOPS per azione con 5 GBps per condivisione con una latenza di circa 3 ms|Standard<br>Premium<br>Ultra<br>Fino a 320k (16K) Dioal con 4,5 GBps per volume a una latenza di circa 1 ms|HDD standard: fino a 500 limiti di IOPS per disco<br>SSD standard: fino a 4 k limiti di IOPS per disco<br>SSD Premium: fino a 20 k limiti di IOPS per disco<br>Consigliamo i dischi Premium per Storage Spaces Direct|
|Capacity|100 TiB per azione|100 TiB per volume, fino a 12,5 PiB per abbonamento|Massimo 32 TiB per disco|
|Infrastruttura richiesta|Dimensione minima della quota 1 GiB|Pool di capacità minima 4 TiB, dimensione min volume 100 GiB|Due macchine virtuali in Azure IaaS (sezione Cloud di controllo cloud) o almeno tre macchine virtuali senza e costi per i dischi|
|Protocolli|SMB 2.1/3. e REST|NFSv3, NFSv4.1 (anteprima), SMB 3.x/2.x|NFSv3, NFSv4.1, SMB 3.1|

## <a name="azure-management-details"></a>Dettagli di gestione di AzureAzure management details

|Funzionalità|File di Azure|Azure NetApp Files|Spazi di archiviazione diretta|
|--------|-----------|------------------|---------------------|
|Accesso|Cloud, locale e ibrido (sincronizzazione file di Azure)Cloud, on-premises and hybrid (Azure file sync)|Cloud, on-premises (via ExpressRoute)|Cloud in locale|
|Backup|Integrazione snapshot di backup di AzureAzure backup snapshot integration|Snapshot dei file NetApp di AzureAzure NetApp Files snapshots|Integrazione snapshot di backup di AzureAzure backup snapshot integration|
|Sicurezza e conformità|[Tutti i certificati supportati da AzureAll Azure supported certificates](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|ISO completato|[Tutti i certificati supportati da AzureAll Azure supported certificates](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Integrazione di Azure Active Directory|[Active Directory nativo e Servizi di dominio Azure Active Directory](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview)|[Servizi di dominio Azure Active Directory e Active Directory nativo](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|Solo supporto nativo di Active Directory o Servizi di dominio Azure Active Directory|

Dopo aver scelto il metodo di archiviazione, consulta i prezzi di [Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/) per informazioni sui nostri piani tariffari.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui contenitori di profili FSLogix, sui dischi dei profili utente e su altre tecnologie dei profili utente, vedere la tabella in Contenitori di [profili FSLogix e file di Azure](fslogix-containers-azure-files.md).

Se si è pronti per creare i propri contenitori di profili FSLogix, iniziare con una di queste esercitazioni:If you're ready to create your own FSLogix profile containers, get started with one of these tutorials:

- [Introduzione ai contenitori di profili FSLogix in File di Azure in Windows Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-FSLogix-profile-containers-on-Azure-Files/ba-p/746477)
- [Creare un contenitore di profili FSLogix per un pool host usando i file NetApp di AzureCreate an FSLogix profile container for a host pool using Azure NetApp files](create-fslogix-profile-container.md)
- Le istruzioni in Distribuire un file server di scalabilità diretta degli spazi di archiviazione a [due nodi per l'archiviazione UPD in Azure](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) si applicano anche quando si usa un contenitore di profili FSLogix anziché un disco del profilo utente

È anche possibile iniziare dall'inizio e configurare la propria soluzione Windows Virtual Desktop in [Creare un tenant in Windows Virtual Desktop](tenant-setup-azure-active-directory.md).
