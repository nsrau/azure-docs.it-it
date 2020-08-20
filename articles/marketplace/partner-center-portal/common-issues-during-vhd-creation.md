---
title: Domande frequenti sui problemi comuni che si verificano durante la creazione di dischi rigidi virtuali
description: Domande frequenti sui problemi comuni durante la creazione di un disco rigido virtuale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/09/2020
ms.openlocfilehash: f4d30cdb931d6523eba3aac003caeee38a8c024d
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653529"
---
# <a name="common-issues-during-vhd-creation"></a>Problemi comuni che si verificano durante la creazione di dischi rigidi virtuali

Queste domande frequenti riguardano problemi comuni che possono verificarsi durante la creazione di un disco rigido virtuale per l'offerta di macchine virtuali di Azure pertinente.

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>Ricerca per categorie: creare una macchina virtuale dal portale di Azure usando un disco rigido virtuale in archiviazione Premium

Azure Marketplace non supporta attualmente la creazione di offerte di macchine virtuali da immagini nell'archiviazione gestita o da Archiviazione Premium di Azure. Per informazioni dettagliate, vedere [Panoramica di Azure Managed Disks](../../virtual-machines/managed-disks-overview.md).

## <a name="can-i-use-generation-2-vms-for-offers"></a>È possibile usare macchine virtuali di generazione 2 per le offerte?

No, sono supportati solo dischi rigidi virtuali di generazione 1. Attualmente collaboriamo comunque con il team della piattaforma Microsoft Azure per provare a supportare macchine virtuali di generazione 2. Per informazioni dettagliate, vedere [Creare una macchina virtuale di generazione 1 o 2 in Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="how-do-i-change-the-name-of-the-host"></a>Come è possibile modificare il nome dell'host?

Non è possibile. Dopo la creazione di una macchina virtuale, gli utenti (inclusi i proprietari) non possono aggiornare il nome dell'host.

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Ricerca per categorie: reimpostare il servizio Desktop remoto o la relativa password di accesso

I presenti articoli illustrano come reimpostare Servizi Desktop remoto per macchine virtuali basate su Windows e su Linux:

* [Come reimpostare il servizio Desktop remoto o la relativa password di accesso in una macchina virtuale Windows](/azure/virtual-machines/troubleshooting/reset-rdp)
* [Come reimpostare la password o la chiave SSH di una VM Linux, correggere la configurazione SSH e verificare la coerenza dei dischi che usano l'estensione VMAccess](/azure/virtual-machines/extensions/vmaccess)

## <a name="how-do-i-generate-new-ssh-certificates"></a>Ricerca per categorie: generare nuovi certificati SSH

La generazione di certificati è illustrata nella [certificazione delle immagini di macchine virtuali di Azure](https://aks.ms/CertifyVMimage).

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Ricerca per categorie: configurare una rete VPN da usare con le macchine virtuali

Se si usa il modello di distribuzione Azure Resource Manager, sono disponibili tre opzioni:

* [Creare un gateway VPN basato su route usando il portale di Azure](../../vpn-gateway/create-routebased-vpn-gateway-portal.md)
* [Creare un gateway VPN basato su route usando Azure PowerShell](../../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
* [Creare un gateway VPN basato su route usando l'interfaccia della riga di comando](../../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Quali sono i criteri di supporto Microsoft per l'esecuzione di software server Microsoft in macchine virtuali basate su Azure?

Maggiori dettagli sono disponibili in [Supporto software di server Microsoft per macchine virtuali di Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Alle macchine virtuali sono associati identificatori univoci?

Sì, se ospitate in Azure. Azure assegna un identificatore univoco denominato [ID univoco di macchina virtuale di Azure](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/) a ogni nuova risorsa di macchina virtuale creata. Per informazioni dettagliate, vedere ID univoco di macchina virtuale di Azure. È possibile ottenere questo identificatore anche tramite l'[API List](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>In una macchina virtuale, com'è possibile gestire l'estensione di script personalizzati nell'attività di avvio?

Per informazioni dettagliate su come usare l'estensione di script personalizzato usando il modulo Azure PowerShell e i modelli di Azure Resource Manager, nonché i passaggi di risoluzione dei problemi in Windows, vedere [Estensione di script personalizzati per Windows](/azure/virtual-machines/extensions/custom-script-windows).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Le applicazioni o i servizi a 32 bit sono supportati in Azure Marketplace?

Generalmente, no. I sistemi operativi supportati e i servizi standard per le macchine virtuali di Azure sono tutti a 64 bit. Sebbene la maggior parte dei sistemi operativi a 64 bit supporti le versioni a 32 bit delle applicazioni per la compatibilità con le versioni precedenti, l'uso di applicazioni a 32 bit come parte della soluzione di macchina virtuale non è supportato ed è altamente sconsigliato. Ricreare l'applicazione come un progetto a 64 bit.

Per altre informazioni, vedere questi articoli:

* [Running 32-bit applications](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications) (Applicazioni a 32 bit in esecuzione)
* [Support for 32-bit operating systems in Azure virtual machines](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines) (Supporto per sistemi operativi a 32 bit nelle macchine virtuali di Azure)
* [Microsoft server software support for Microsoft Azure virtual machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) (Supporto software di server Microsoft per macchine virtuali di Microsoft Azure)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Errore: Il disco rigido virtuale è già registrato con l'archivio immagini come risorsa

Ogni volta che si tenta di creare un'immagine dai dischi rigidi virtuali, in Azure PowerShell viene visualizzato l'errore "Il disco rigido virtuale è già registrato con l'archivio immagini come risorsa". Non è stato creato alcun tipo di risorsa in precedenza, né è stata trovata alcuna immagine con questo nome in Azure. Come posso risolvere il problema?

Questo problema si verifica in genere se l'utente ha creato una macchina virtuale da un disco rigido virtuale con un blocco. Accertarsi che da questo disco rigido virtuale non sia allocata alcuna macchina virtuale, quindi ripetere l'operazione. Se il problema persiste, aprire un ticket di supporto. Vedere [Supporto per il Centro per i partner](support.md).
