---
title: Domande comuni sulla macchina virtuale in Azure Marketplace
description: Domande comuni rilevate durante la creazione di una macchina virtuale in Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 88ec58ca9bcfa5c64036d3b65e77f3248e26357d
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124952"
---
# <a name="common-questions-about-vm-in-azure-marketplace"></a>Domande comuni sulla macchina virtuale in Azure Marketplace

Queste domande frequenti riguardano i problemi comuni che possono verificarsi durante la creazione di un'offerta di macchina virtuale (VM) in Azure Marketplace.

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Ricerca per categorie: configurare una rete VPN da usare con le macchine virtuali

Se si usa il modello di distribuzione Azure Resource Manager, sono disponibili tre opzioni:

- [Creare un gateway VPN basato su route usando il portale di Azure](../vpn-gateway/tutorial-create-gateway-portal.md)
- [Creare un gateway VPN basato su route usando Azure PowerShell](../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
- [Creare un gateway VPN basato su route usando l'interfaccia della riga di comando](../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Quali sono i criteri di supporto Microsoft per l'esecuzione di software server Microsoft in macchine virtuali basate su Azure?

Maggiori dettagli sono disponibili in [Supporto software di server Microsoft per macchine virtuali di Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>In una macchina virtuale, com'è possibile gestire l'estensione di script personalizzati nell'attività di avvio?

Per informazioni dettagliate su come usare l'estensione di script personalizzato usando il modulo Azure PowerShell e i modelli di Azure Resource Manager, nonché i passaggi di risoluzione dei problemi in Windows, vedere [Estensione di script personalizzati per Windows](../virtual-machines/extensions/custom-script-windows.md).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Le applicazioni o i servizi a 32 bit sono supportati in Azure Marketplace?

No. I sistemi operativi supportati e i servizi standard per le macchine virtuali di Azure sono tutti a 64 bit. Sebbene la maggior parte dei sistemi operativi a 64 bit supporti le versioni a 32 bit delle applicazioni per la compatibilità con le versioni precedenti, l'uso di applicazioni a 32 bit come parte della soluzione di macchina virtuale non è supportato ed è altamente sconsigliato. Ricreare l'applicazione come un progetto a 64 bit.

Per altre informazioni, vedere questi articoli:

- [Running 32-bit applications](/windows/desktop/WinProg64/running-32-bit-applications) (Applicazioni a 32 bit in esecuzione)
- [Support for 32-bit operating systems in Azure virtual machines](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines) (Supporto per sistemi operativi a 32 bit nelle macchine virtuali di Azure)
- [Microsoft server software support for Microsoft Azure virtual machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) (Supporto software di server Microsoft per macchine virtuali di Microsoft Azure)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Errore: Il disco rigido virtuale è già registrato con l'archivio immagini come risorsa

Ogni volta che si tenta di creare un'immagine dai dischi rigidi virtuali, in Azure PowerShell viene visualizzato l'errore "Il disco rigido virtuale è già registrato con l'archivio immagini come risorsa". Non è stato creato alcun tipo di risorsa in precedenza, né è stata trovata alcuna immagine con questo nome in Azure. Come posso risolvere il problema?

Questo problema si verifica in genere se l'utente ha creato una macchina virtuale da un disco rigido virtuale con un blocco. Accertarsi che da questo disco rigido virtuale non sia allocata alcuna macchina virtuale, quindi ripetere l'operazione. Se il problema persiste, aprire un ticket di supporto. Vedere [Supporto per il Centro per i partner](support.md).

## <a name="next-steps"></a>Passaggi successivi

- [Risoluzione dei problemi di certificazione delle VM](azure-vm-create-certification-faq.md)