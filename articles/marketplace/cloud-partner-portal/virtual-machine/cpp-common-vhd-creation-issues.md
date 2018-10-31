---
title: Domande frequenti sui problemi comuni durante la creazione di dischi rigidi virtuali per Azure Marketplace | Microsoft Docs
description: Domande frequenti sulla creazione di dischi rigidi virtuali e sui problemi associati.
services: Azure Marketplace
documentationcenter: ''
author: HannibalSII
manager: Patrick.Butler
editor: ''
ms.assetid: e39563d8-8646-4cb7-b078-8b10ac35b494
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 10/02/2018
ms.author: hascipio; v-divte; v-miclar
ms.openlocfilehash: 535a947f7a4b9c750d585ce854a14be80c4a135c
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639156"
---
# <a name="common-issues-during-vhd-creation-faq"></a>Domande frequenti sui problemi comuni che si verificano durante la creazione di dischi rigidi virtuali

Le domande frequenti seguenti sono relative a problemi comuni che si sono verificati durante la creazione di dischi rigidi virtuali e di macchine virtuali per le offerte di macchine virtuali. 

## <a name="how-do-you-create-a-vm-from-the-azure-portal-using-the-vhd-that-is-uploaded-to-premium-storage"></a>Come viene creata una macchina virtuale nel portale di Azure usando il disco rigido virtuale caricato in Archiviazione Premium?

Azure Marketplace non supporta attualmente la creazione di offerte di macchine virtuali da immagini che risiedono nell'archiviazione gestita o da Archiviazione Premium di Azure.  Per altre informazioni su tali opzioni di archiviazione, vedere [Panoramica di Azure Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) e [Archiviazione Premium a prestazioni elevate e dischi gestiti per le macchine virtuali](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage).


## <a name="can-you-use-generation-2-vms-for-offers"></a>È possibile usare macchine virtuali di generazione 2 per le offerte?

No, sono supportate solo dischi rigidi virtuali di generazione 1.  Attualmente collaboriamo comunque con il team della piattaforma Microsoft Azure per provare a supportare macchine virtuali di generazione 2.  Per altre informazioni sulle differenze, vedere [Should I create a generation 1 or 2 virtual machine in Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) (È necessario creare una macchina virtuale di generazione 1 o 2 in Hyper-V?)


## <a name="how-do-you-change-the-name-of-the-host"></a>Come si cambia il nome dell'host?

Non è possibile.  Dopo la creazione della macchina virtuale, gli utenti (inclusi i proprietari) non possono aggiornare il nome dell'host.


## <a name="how-do-you-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Come si reimpostare il servizio Desktop remoto o la relativa password di accesso?

Gli articoli seguenti illustrano come reimpostare Servizi Desktop remoto per macchine virtuali basate su Windows e su Linux:   

- [Come reimpostare il servizio Desktop remoto o la relativa password di accesso in una macchina virtuale Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
- [Come reimpostare la password o la chiave SSH di una VM Linux, correggere la configurazione SSH e verificare la coerenza dei dischi che usano l'estensione VMAccess](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)


## <a name="how-do-you-generate-new-ssh-certificates"></a>Come è possibile generare nuovi certificati SSH?

La generazione di certificati è illustrata nell'articolo [Ottenere l'URI di firma di accesso condiviso per l'immagine di macchina virtuale](./cpp-get-sas-uri.md) nella sezione successiva [Creare risorse tecniche per un'offerta di macchina virtuale](./cpp-create-technical-assets.md).


## <a name="how-do-you-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Come si configura una rete VPN da usare con le macchine virtuali?

Se si usa il modello di distribuzione Azure Resource Manager, sono disponibili tre opzioni comuni di configurazione di una connessione VPN:
- [Creare un gateway VPN basato su route usando il portale di Azure](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
- [Creare un gateway VPN basato su route usando PowerShell](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
- [Creare un gateway VPN basato su route usando l'interfaccia della riga di comando](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)


## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Quali sono i criteri di supporto Microsoft per l'esecuzione di software server Microsoft in macchine virtuali basate su Azure?

Tali criteri di supporto sono descritte in dettaglio nell'articolo [Microsoft server software support for Microsoft Azure virtual machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) (Supporto di software server Microsoft per le macchine virtuali Microsoft Azure).


## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Alle macchine virtuali sono associati identificatori univoci?

Sì, se ospitate in Azure.  Azure assegna un identificatore univoco denominato ID univoco di macchina virtuale di Azure a ogni nuova risorsa di macchina virtuale creata.  Per altre informazioni, leggere il post del blog [Azure Virtual Machine Unique ID](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/) (ID univoco di macchina virtuale di Azure).  È anche possibile ottenere questo identificatore a livello di codice tramite le [API elenco](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).


## <a name="in-a-vm-how-do-you-manage-the-custom-script-extension-in-the-startup-task"></a>In una macchina virtuale com'è possibile gestire l'estensione di script personalizzato nell'attività di avvio?

L'articolo seguente descrive in dettaglio come usare l'estensione di script personalizzato usando il modulo Azure PowerShell e i modelli di Azure Resource Manager, nonché le operazioni di risoluzione dei problemi in Windows: [Estensione di script personalizzati per Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)


## <a name="are-32-bit-applications-or-services-supported-in-the-azure-marketplace"></a>Le applicazioni o i servizi a 32 bit sono supportati in Azure Marketplace?

Generalmente, no.  I sistemi operativi supportati e i servizi standard per le macchine virtuali di Azure sono tutti a 64 bit.  Dal punto di vista tecnico, tuttavia, la maggior parte dei sistemi operativi a 64 bit supporta l'esecuzione di versioni a 32 bit delle applicazioni per la compatibilità con le versioni precedenti.  L'uso delle applicazioni a 32 bit come parte della soluzione di macchina virtuale, tuttavia, non è supportato e pertanto è *vivamente sconsigliato*.  Ricompilare quindi l'applicazione come un progetto a 64 bit.

Per altre informazioni, vedere gli articoli seguenti:
- [Running 32-bit applications](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications) (Applicazioni a 32 bit in esecuzione)
- [Support for 32-bit operating systems in Azure virtual machines](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines) (Supporto per sistemi operativi a 32 bit nelle macchine virtuali di Azure)
- [Microsoft server software support for Microsoft Azure virtual machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) (Supporto software di server Microsoft per macchine virtuali di Microsoft Azure)


## <a name="every-time-i-try-to-create-an-image-from-my-vhds-i-get-the-error-vhd-is-already-registered-with-image-repository-as-the-resource-in-powershell-i-did-not-create-any-image-before-nor-did-i-find-any-image-with-this-name-in-azure-how-do-i-resolve-this-issue"></a>Ogni volta che si tenta di creare un'immagine dai dischi rigidi virtuali, in PowerShell viene visualizzato l'errore `.VHD is already registered with image repository as the resource`, anche se non è stato creato alcun tipo di risorsa in precedenza, né è stata trovata un'immagine con questo nome in Azure. Come si risolve questo problema?

Questo problema si verifica in genere se l'utente esegue il provisioning di una macchina virtuale da un disco rigido virtuale con un blocco.  Verificare che da questo disco rigido virtuale non sia presente alcuna macchina virtuale, quindi ripetere l'operazione.  Se il problema persiste, aprire un ticket di supporto, come illustrato in [Supporto per il portale Cloud Partner](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-support-for-cloud-partner-portal). 

