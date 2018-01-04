---
title: Risolvere i problemi di distribuzione della macchina virtuale Windows in Azure | Microsoft Docs
description: Risolvere i problemi di distribuzione della macchina virtuale Windows in Azure con il modello di distribuzione Resource Manager.
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 6a1697061d20d26b4263c02487180fee81e87947
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Risolvere i problemi di distribuzione della macchina virtuale Windows in Azure

Per risolvere i problemi relativi alla distribuzione della macchina virtuale (VM) in Azure, esaminare i [problemi principali](#top-issues) per errori e risoluzioni comuni.

Per ricevere assistenza in qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.

## <a name="top-issues"></a>Problemi principali
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

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

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>Come si usa e si distribuisce un'immagine client Windows in Azure?

Se si ha di una sottoscrizione appropriata di Visual Studio (in precedenza MSDN) è possibile usare Windows 7, Windows 8 o Windows 10 in Azure per scenari di sviluppo/test. In questo [articolo](client-images.md) sono descritti i requisiti di idoneità per l'esecuzione di client Windows in Azure e l'uso delle immagini della raccolta di Azure.

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>Come distribuire una macchina virtuale usando il vantaggio Hybrid Use (HUB, Hybrid Use Benefit)?

Esistono due modi per distribuire le macchine virtuali di Windows con il vantaggio Azure Hybrid Use.

Per una sottoscrizione con un contratto Enterprise Agreement:

•   Distribuire le VM da immagini specifiche di Marketplace preconfigurate con il vantaggio Azure Hybrid Use.

Per il contratto Enterprise Agreement:

•   Caricare una VM personalizzata e distribuirla usando un modello di Resource Manager o Azure PowerShell.

Per altre informazioni, vedere le seguenti risorse:

 - [Panoramica del vantaggio Azure Hybrid Use ](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [Domande frequenti scaricabili](http://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [Vantaggio Azure Hybrid Use per Windows Server e Windows Client](hybrid-use-benefit-licensing.md).

 - [Come usare il vantaggio Hybrid Use in Azure](https://blogs.msdn.microsoft.com/azureedu/2016/04/13/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Come attivare il credito mensile per Visual Studio Enterprise (BizSpark)

Per attivare il credito mensile, vedere questo [articolo](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>Come aggiungere Sviluppo/test Enterprise al contratto Enterprise Agreement (EA) per ottenere l'accesso a immagini client Windows?

La possibilità di creare sottoscrizioni basate sull'offerta Sviluppo/test Enterprise è riservata ai proprietari di account che hanno ricevuto l'autorizzazione corrispondente da un amministratore dell'organizzazione. Il proprietario dell'account crea sottoscrizioni tramite il portale degli account di Azure e quindi deve aggiungere i sottoscrittori di Visual Studio attivi come coamministratori. In tal modo il proprietario dell'account può gestire e usare le risorse necessarie per sviluppo e test. Per altre informazioni, vedere [Sviluppo/test Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>I driver della VM Windows Serie N non sono disponibili

I driver per le macchine virtuali basate su Windows si trovano [qui](n-series-driver-setup.md).

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Nella VM Serie N non è disponibile un'istanza GPU

Per usufruire delle funzionalità GPU delle VM serie N di Azure che eseguono Windows Server 2016 o Windows Server 2012 R2, è necessario installare i driver della scheda grafica NVIDIA in ciascuna VM dopo la distribuzione. Le informazioni di configurazione dei driver sono disponibili anche per le [VM Windows](n-series-driver-setup.md) e le [VM Linux](../linux/n-series-driver-setup.md).

## <a name="are-client-images-supported-for-n-series"></a>Le immagini client sono supportate per la Serie N?

Attualmente Azure supporta la Serie N solo sulle VM che eseguono i sistemi operativi Windows Server e Linux.

## <a name="is-n-series-vms-available-in-my-region"></a>Le VM Serie N sono disponibili nella mia area?

È possibile controllare la disponibilità nella tabella [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services) e verificare i prezzi [qui](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>Quali immagini client è possibile usare e distribuire in Azure e come ottenerle?

A condizione di disporre di una sottoscrizione appropriata di Visual Studio (in precedenza MSDN), è possibile usare Windows 7, Windows 8 o Windows 10 in Azure per scenari di sviluppo/test. 

- Le immagini di Windows 10 sono disponibili nella raccolta di Azure in [Offerte idonee](client-images.md#eligible-offers). 
- I sottoscrittori di Visual Studio per qualsiasi tipo di offerta possono anche [preparare e creare](prepare-for-upload-vhd-image.md) un'immagine a 64 bit di Windows 7, Windows 8 o Windows 10 e quindi [caricarla in Azure](upload-generalized-managed.md). L'utilizzo rimane limitato alle attività di sviluppo e test da parte dei sottoscrittori di Visual Studio attivi.

In questo [articolo](client-images.md) sono descritti i requisiti di idoneità per l'esecuzione di client Windows in Azure e l'uso delle immagini della raccolta di Azure.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Durante il ridimensionamento della VM non è possibile visualizzare la famiglia Dimensioni macchina virtuale desiderata.

Quando una VM è in esecuzione, viene distribuita su un server fisico. I server fisici nelle aree di Azure sono raggruppati in cluster di hardware fisico comune. Il ridimensionamento di una VM che ne richiede lo spostamento in un cluster hardware diverso varia a seconda del modello usato per la distribuzione della VM.

- Nel caso delle VM distribuite con il modello di distribuzione classica è necessario rimuovere e ripetere la distribuzione del servizio cloud per impostare le VM su dimensioni appartenenti a un'altra famiglia di dimensioni.

- Nel caso delle VM distribuite con il modello di distribuzione di Gestione risorse, prima di modificare le dimensioni di una VM è necessario arrestare tutte le VM del set di disponibilità.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Le dimensioni della VM elencate non sono supportate durante la distribuzione nel set di disponibilità.

Scegliere dimensioni supportate nel cluster del set di disponibilità. Quando si crea un set di disponibilità è consigliabile scegliere per la VM le dimensioni massime che si prevede di usare e impostare la VM risultante come prima distribuzione nel set di disponibilità.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>È possibile aggiungere una VM classica esistente a un set di disponibilità?

Sì. È possibile aggiungere una VM classica esistente a un set di disponibilità nuovo o esistente. Per altre informazioni, vedere [Aggiungere una macchina virtuale esistente a un set di disponibilità](classic/configure-availability.md#addmachine).


## <a name="next-steps"></a>Passaggi successivi
Per ricevere assistenza in qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/).

In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.
