---
title: Risolvere i problemi di distribuzione della macchina virtuale Linux in Azure | Microsoft Docs
description: Risolvere i problemi di distribuzione della macchina virtuale Linux in Azure con il modello di distribuzione Resource Manager.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/22/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: 66f04537c7846b5511a032007e09b0d4e8ae1615
ms.contentlocale: it-it
ms.lasthandoff: 07/26/2017

---
# <a name="troubleshoot-deploying-linux-virtual-machine-issues-in-azure"></a>Risolvere i problemi di distribuzione della macchina virtuale Linux in Azure

Per risolvere i problemi relativi alla distribuzione della macchina virtuale (VM) in Azure, esaminare i [problemi principali](#top-issues) per errori e risoluzioni comuni.

Per ricevere assistenza in qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.

## <a name="top-issues"></a>Problemi principali
[!INCLUDE [virtual-machines-linux-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>Il cluster non supporta le dimensioni della VM richieste
<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Ripetere la richiesta usando una VM di dimensioni inferiori.
- Se le dimensioni della VM richieste non possono essere modificate:
    - Arrestare tutte le VM nel set di disponibilità. Fare clic su **Gruppi di risorse** > il proprio gruppo di risorse > **Risorse** > il proprio set di disponibilità > **Macchine virtuali** > la propria macchina virtuale > **Arresta**.
    - Dopo l'arresto di tutte le VM creare la VM con le dimensioni desiderate.
    - Avviare prima la nuova VM, quindi selezionare ogni VM arrestata e fare clic su Avvia.


## <a name="the-cluster-does-not-have-free-resources"></a>Il cluster non ha risorse disponibili
<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Ripetere la richiesta più tardi.
- Se la nuova VM può far parte di un set di disponibilità diverso
    - Creare una VM in un altro set di disponibilità nella stessa area.
    - Aggiungere la nuova VM alla stessa rete virtuale.

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Come attivare il credito mensile per Visual Studio Enterprise (BizSpark)

Per attivare il credito mensile, vedere questo [articolo](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="why-can-i-not-install-the-gpu-driver-for-an-ubuntu-nv-vm"></a>Perché non è possibile installare il driver GPU per una VM Ubuntu NV?

Attualmente, il supporto per GPU Linux è disponibile solo sulle macchine virtuali NC di Azure che eseguono Ubuntu Server 16.04 LTS. Per altre informazioni, vedere [Configurare i driver GPU per le VM serie N che eseguono Linux](n-series-driver-setup.md).

## <a name="my-drivers-are-missing-for-my-linux-n-series-vm"></a>I driver della VM serie N di Linux non sono disponibili

I driver per le macchine virtuali basate su Linux si trovano [qui](n-series-driver-setup.md). 

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Non è possibile trovare un'istanza GPU nella VM serie N

Per usufruire delle funzionalità GPU delle VM serie N di Azure che eseguono Windows Server 2016 o Windows Server 2012 R2, è necessario installare i driver della scheda grafica NVIDIA in ciascuna VM dopo la distribuzione. Le informazioni di configurazione dei driver sono disponibili anche per le [VM Windows](../windows/n-series-driver-setup.md) e le [VM Linux](n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>Le VM serie N sono disponibili nell'area di appartenenza?

È possibile controllare la disponibilità nella tabella [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services) e verificare i prezzi [qui](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Durante il ridimensionamento della VM non è possibile visualizzare la famiglia Dimensioni macchina virtuale desiderata.

Quando una VM è in esecuzione, viene distribuita su un server fisico. I server fisici nelle aree di Azure sono raggruppati in cluster di hardware fisico comune. Il ridimensionamento di una VM che ne richiede lo spostamento in un cluster hardware diverso varia a seconda del modello usato per la distribuzione della VM.

- Nel caso delle VM distribuite con il modello di distribuzione classica è necessario rimuovere e ripetere la distribuzione del servizio cloud per impostare le VM su dimensioni appartenenti a un'altra famiglia di dimensioni.

- Nel caso delle VM distribuite con il modello di distribuzione di Gestione risorse, prima di modificare le dimensioni di una VM è necessario arrestare tutte le VM del set di disponibilità.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Le dimensioni della VM elencate non sono supportate durante la distribuzione nel set di disponibilità.

Scegliere dimensioni supportate nel cluster del set di disponibilità. Quando si crea un set di disponibilità, è consigliabile scegliere per la VM le dimensioni massime che si prevede di usare e di usare la VM risultante come prima distribuzione nel set di disponibilità.

## <a name="what-linux-distributionsversions-are-supported-on-azure"></a>Quali distribuzioni/versioni di Linux sono supportate in Azure?

È possibile trovare l'elenco per Linux in [Distribuzioni approvate per Azure](endorsed-distros.md).

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>È possibile aggiungere una VM classica esistente a un set di disponibilità?

Sì. È possibile aggiungere una VM classica esistente a un set di disponibilità nuovo o esistente. Per altre informazioni, vedere [Aggiungere una macchina virtuale esistente a un set di disponibilità](../windows/classic/configure-availability.md#addmachine).


## <a name="next-steps"></a>Passaggi successivi
Per ricevere assistenza in qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/).

In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.

