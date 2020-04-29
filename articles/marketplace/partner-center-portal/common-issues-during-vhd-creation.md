---
title: Domande frequenti sui problemi comuni che si verificano durante la creazione di dischi rigidi virtuali
description: Domande frequenti sui problemi comuni durante la creazione di un disco rigido virtuale (VHD).
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
ms.date: 04/09/2020
ms.openlocfilehash: a47bece4df7263dbc29ec19510c3ea422bb3f4cf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81266266"
---
# <a name="common-issues-during-vhd-creation"></a>Problemi comuni durante la creazione del disco rigido virtuale

> [!NOTE]
> Stiamo comunicando la gestione delle offerte della macchina virtuale di Azure dal portale Cloud Partner al centro per i partner. Fino a quando non viene eseguita la migrazione delle offerte, continuare a seguire le istruzioni riportate in [problemi comuni durante la creazione del disco rigido virtuale (FAQ)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues) in Portale cloud partner per gestire le offerte.

Queste domande frequenti riguardano problemi comuni che possono verificarsi durante la creazione di un disco rigido virtuale (VHD) per l'offerta di macchina virtuale di Azure.

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>Ricerca per categorie creare una macchina virtuale dalla portale di Azure usando un disco rigido virtuale in archiviazione Premium?

Azure Marketplace non supporta attualmente la creazione di offerte di macchine virtuali da immagini nell'archiviazione gestita o da archiviazione Premium di Azure. Per informazioni dettagliate, vedere [Panoramica di Managed Disks di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).

## <a name="can-i-use-generation-2-vms-for-offers"></a>È possibile usare le macchine virtuali di seconda generazione per le offerte?

No, sono supportati solo i dischi rigidi virtuali di generazione 1. Tuttavia, stiamo lavorando al team di Microsoft Azure Platform per esaminare il supporto per le macchine virtuali di seconda generazione. Per informazioni dettagliate, vedere la pagina relativa alla [creazione di una macchina virtuale di prima o seconda generazione in Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="how-do-i-change-the-name-of-the-host"></a>Come è possibile modificare il nome dell'host?

L'operazione non risulta possibile. Dopo la creazione di una macchina virtuale, gli utenti, inclusi i proprietari, non potranno aggiornare il nome host.

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Ricerca per categorie reimpostare il servizio Desktop remoto o la relativa password di accesso?

Questi articoli illustrano come eseguire le reimpostazioni di Servizi Desktop remoto per le macchine virtuali basate su Windows e Linux:

* [Come reimpostare il servizio Desktop remoto o la relativa password di accesso in una macchina virtuale Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
* [Come reimpostare la password o la chiave SSH di una VM Linux, correggere la configurazione SSH e verificare la coerenza dei dischi che usano l'estensione VMAccess](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)

## <a name="how-do-i-generate-new-ssh-certificates"></a>Ricerca per categorie generare nuovi certificati SSH?

La generazione di certificati è illustrata nella [certificazione delle immagini di VM di Azure](https://aks.ms/CertifyVMimage).

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Ricerca per categorie configurare una rete privata virtuale (VPN) per lavorare con le VM?

Se si usa il modello di distribuzione Azure Resource Manager, sono disponibili tre opzioni:

* [Creare un gateway VPN basato su route usando il portale di Azure](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
* [Creare un gateway VPN basato su Route usando Azure PowerShell](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
* [Creare un gateway VPN basato su route usando l'interfaccia della riga di comando](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Quali sono i criteri di supporto Microsoft per l'esecuzione di software server Microsoft in macchine virtuali basate su Azure?

Per informazioni dettagliate, vedere il [supporto del software server Microsoft per Microsoft Azure macchine virtuali](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Alle macchine virtuali sono associati identificatori univoci?

Sì, se ospitate in Azure. Azure assegna un identificatore univoco, denominato [ID univoco della macchina virtuale di Azure](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/), a ogni nuova risorsa di macchina virtuale creata. Per informazioni dettagliate, vedere ID univoco della macchina virtuale di Azure. È anche possibile ottenere questo identificatore tramite l' [API List](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>In una macchina virtuale, come si gestisce l'estensione di script personalizzati nell'attività di avvio?

Per informazioni dettagliate sull'uso dell'estensione script personalizzata usando il modulo Azure PowerShell, i modelli Azure Resource Manager e i passaggi per la risoluzione dei problemi nei sistemi Windows, vedere [estensione script personalizzato per Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>I servizi o le applicazioni a 32 bit sono supportati in Azure Marketplace?

Generalmente, no. I sistemi operativi supportati e i servizi standard per le macchine virtuali di Azure sono tutti a 64 bit. Sebbene la maggior parte dei sistemi operativi a 64 bit supporti le versioni a 32 bit delle applicazioni per la compatibilità con le versioni precedenti, l'uso di applicazioni a 32 bit come parte della soluzione VM non è supportato e molto sconsigliato. Ricreare l'applicazione come progetto a 64 bit.

Per altre informazioni, vedere questi articoli:

* [Running 32-bit applications](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications) (Applicazioni a 32 bit in esecuzione)
* [Supporto per sistemi operativi a 32 bit nelle macchine virtuali di Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [Microsoft server software support for Microsoft Azure virtual machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) (Supporto software di server Microsoft per macchine virtuali di Microsoft Azure)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Errore: il disco rigido virtuale è già registrato con l'archivio immagini come risorsa

Ogni volta che si tenta di creare un'immagine dai dischi rigidi virtuali, viene ricevuto l'errore "il VHD è già registrato con l'archivio immagini come risorsa" in Azure PowerShell. Non ho creato alcuna immagine prima né ho trovato un'immagine con questo nome in Azure. Come posso risolvere il problema?

Questo problema viene in genere visualizzato se è stata creata una macchina virtuale da un disco rigido virtuale con un blocco. Verificare che non sia stata allocata alcuna macchina virtuale da questo disco rigido virtuale, quindi ripetere l'operazione. Se il problema persiste, aprire un ticket di supporto. Vedere [supporto per il centro per i partner](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support).
