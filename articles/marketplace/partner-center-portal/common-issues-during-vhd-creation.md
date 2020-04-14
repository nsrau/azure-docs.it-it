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
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266266"
---
# <a name="common-issues-during-vhd-creation"></a>Problemi comuni durante la creazione del disco rigido virtualeCommon issues during VHD creation

> [!NOTE]
> Stiamo spostando la gestione delle offerte di vm di Azure dal portale Cloud Partner al Centro per i partner. Fino alla migrazione delle offerte, continua a seguire le istruzioni in Problemi comuni durante la creazione del disco rigido [virtuale (FAQ)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues) nel portale Cloud Partner per gestire le offerte.

Queste domande frequenti riguardano i problemi comuni che possono verificarsi durante la creazione di un disco rigido virtuale (VHD) per l'offerta di macchine virtuali di Azure.These frequently asked questions (FAQ) cover common issues you might encounter when creating a virtual hard disk (VHD) for your Azure Virtual Machine offer.

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>Come si crea una macchina virtuale dal portale di Azure usando un disco rigido virtuale nell'archiviazione Premium?

Azure Marketplace does not currently support creating VM offers from images on managed storage or from Azure Premium Storage. Per informazioni dettagliate, vedere [Panoramica dei dischi gestiti](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)di Azure .For details, see Azure Managed Disks Overview .

## <a name="can-i-use-generation-2-vms-for-offers"></a>È possibile usare le macchine virtuali di generazione 2 per le offerte?

No, sono supportati solo i dischi rigidi virtuali di generazione 1. Tuttavia, stiamo attualmente lavorando con il team della piattaforma Microsoft Azure per analizzare il supporto per le macchine virtuali di generazione 2.However, we're working with the Microsoft Azure Platform Team to investigate support for Generation 2 VMs. Per informazioni dettagliate, vedere Creare una macchina virtuale di [generazione 1 o 2 in Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="how-do-i-change-the-name-of-the-host"></a>Come è possibile modificare il nome dell'host?

L'operazione non risulta possibile. Dopo aver creato una macchina virtuale, gli utenti (inclusi i proprietari) non possono aggiornare il nome host.

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Come si reimposta il servizio Desktop remoto o la relativa password di accesso?

Questi articoli illustrano come eseguire reimpostazioni di Servizi Desktop remoto per le macchine virtuali basate su Windows e Linux:These articles explain how to perform RDS resets for Windows- and Linux-based VMs:

* [Come reimpostare il servizio Desktop remoto o la relativa password di accesso in una macchina virtuale Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
* [Come reimpostare la password o la chiave SSH di una VM Linux, correggere la configurazione SSH e verificare la coerenza dei dischi che usano l'estensione VMAccess](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)

## <a name="how-do-i-generate-new-ssh-certificates"></a>Come si generano nuovi certificati SSH?

La generazione dei certificati è illustrata in [Certificazione immagine VM](https://aks.ms/CertifyVMimage)di Azure.Generation of certificates is explained in Azure VM image certification .

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Come si configura una rete privata virtuale (VPN) per l'utilizzo con le macchine virtuali?

Se si usa il modello di distribuzione di Azure Resource Manager, sono disponibili tre opzioni:If you are using the Azure Resource Manager deployment model, you have three options:

* [Creare un gateway VPN basato su route usando il portale di Azure](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
* [Creare un gateway VPN basato sulla route usando Azure PowerShellCreate a route-based VPN gateway using Azure PowerShell](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
* [Creare un gateway VPN basato su route usando l'interfaccia della riga di comando](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Quali sono i criteri di supporto Microsoft per l'esecuzione di software server Microsoft in macchine virtuali basate su Azure?

Per informazioni dettagliate, vedere [Supporto software server Microsoft per macchine virtuali di Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Alle macchine virtuali sono associati identificatori univoci?

Sì, se ospitate in Azure. Azure assegna un identificatore univoco, denominato ID univoco macchina virtuale di [Azure,](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/)a ogni nuova risorsa VM creata. Per informazioni dettagliate, vedere ID univoco macchina virtuale di Azure.For details, see Azure Virtual Machine Unique ID. È inoltre possibile ottenere questo identificatore tramite [l'API List](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>In una macchina virtuale, come si gestisce l'estensione di script personalizzata nell'attività di avvio?

Per informazioni dettagliate sull'uso dell'estensione script personalizzata tramite il modulo Azure PowerShell, i modelli di Azure Resource Manager e la procedura di risoluzione dei problemi nei sistemi Windows, vedere [Estensione script personalizzata per Windows.](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Le applicazioni o i servizi a 32 bit sono supportati in Azure Marketplace?

Generalmente, no. I sistemi operativi supportati e i servizi standard per le macchine virtuali di Azure sono tutti a 64 bit. Sebbene la maggior parte dei sistemi operativi a 64 bit supporti le versioni a 32 bit delle applicazioni per garantire la compatibilità con le versioni precedenti, l'utilizzo di applicazioni a 32 bit come parte della soluzione VM non è supportato e altamente sconsigliato. Ricreare l'applicazione come progetto a 64 bit.

Per altre informazioni, vedere questi articoli:

* [Running 32-bit applications](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications) (Applicazioni a 32 bit in esecuzione)
* [Supporto per sistemi operativi a 32 bit nelle macchine virtuali di Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [Microsoft server software support for Microsoft Azure virtual machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) (Supporto software di server Microsoft per macchine virtuali di Microsoft Azure)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Errore: il disco rigido virtuale è già registrato con l'archivio immagini come risorsa

Ogni volta che si tenta di creare un'immagine dai dischi rigidi virtuali, viene visualizzato l'errore "VHD è già registrato con l'archivio immagini come risorsa" in Azure PowerShell.Every time To try to create an image from my VHDs, I get the error "VHD is already registered with image repository as the resource" in Azure PowerShell. Non ho creato alcuna immagine prima né ho trovato alcuna immagine con questo nome in Azure.You didn't create any image before nor i find any image with this name in Azure. Come posso risolvere il problema?

Questo problema si verifica in genere se è stata creata una macchina virtuale da un disco rigido virtuale con un blocco su di esso. Verificare che non sia presente alcuna macchina virtuale allocata da questo disco rigido virtuale e quindi ritentare l'operazione. Se il problema persiste, aprire un ticket di supporto. Consultate [Supporto per il Centro per i partner.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support)
