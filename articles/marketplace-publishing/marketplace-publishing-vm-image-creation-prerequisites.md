---
title: Prerequisiti tecnici per la creazione di un&quot;immagine di macchina virtuale per Azure Marketplace | Documentazione Microsoft
description: Informazioni sui requisiti per la creazione e la distribuzione di un&quot;immagine di macchina virtuale in Azure Marketplace per l&quot;acquisto da parte di altri utenti.
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 63c16966-0304-4b17-a715-368a0a5ccb2c
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: hascipio; v-divte
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: af3e2ad623d8d7bfafe676411f9ae3fbee78aab8
ms.lasthandoff: 03/31/2017


---
# <a name="technical-prerequisites-for-creating-a-virtual-machine-image-for-the-azure-marketplace"></a>Prerequisiti tecnici per la creazione di un'immagine di macchina virtuale per Azure Marketplace
Leggere attentamente le informazioni sul processo prima di iniziare e comprendere dove e perché viene eseguito ogni passaggio. Per quanto possibile, è necessario preparare le informazioni sulla società e altri dati, scaricare gli strumenti necessari e/o creare i componenti tecnici prima di iniziare il processo di creazione dell'offerta. Questi elementi dovrebbero risultare chiari dalla lettura di questo articolo.  

## <a name="download-needed-tools--applications"></a>Scaricare gli strumenti e le applicazioni necessari
È necessario disporre dei seguenti elementi prima di iniziare il processo:

* A seconda del sistema operativo di destinazione, installare i [cmdlet di Azure PowerShell](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/WindowsAzurePowershellGet.3f.3f.3fnew.appids) o lo [strumento di interfaccia della riga di comando di Linux](https://go.microsoft.com/fwlink/?LinkId=253472&clcid=0x409) dalla pagina dei [download di Azure](https://azure.microsoft.com/downloads/).
* Installare lo strumento di esplorazione dell'archiviazione di Azure da CodePlex.
* Scaricare e installare lo strumento di test della certificazione per Azure Certified:
  * [http://go.microsoft.com/fwlink/?LinkID=526913](http://go.microsoft.com/fwlink/?LinkID=526913). È necessario un computer basato su Windows per eseguire lo strumento di certificazione. Se non è disponibile un computer basato su Windows, è possibile eseguire lo strumento usando una macchina virtuale basata su Windows in Azure.

## <a name="platforms-supported"></a>Piattaforme supportate
È possibile sviluppare macchine virtuali basate su Azure in Windows o Linux. Per alcuni elementi del processo di pubblicazione, ad esempio la creazione di un disco rigido virtuale (VHD) compatibile con Azure, vengono usati strumenti e passaggi diversi a seconda del sistema operativo in uso:  

* Se si usa Linux, vedere la sezione relativa alla creazione di un disco rigido virtuale (basato su Linux) compatibile con Azure della [Guida alla pubblicazione di un'immagine di macchina virtuale](marketplace-publishing-vm-image-creation.md).
* Se si usa Windows, vedere la sezione relativa alla creazione di un disco rigido virtuale (basato su Windows) compatibile con Azure della [Guida alla pubblicazione di un'immagine di macchina virtuale](marketplace-publishing-vm-image-creation.md).

> [!NOTE]
> È necessario l'accesso a un computer basato su Windows per:
> 
> * Eseguire lo strumento di convalida della certificazione.
> * Creare l'URL di firma di accesso condiviso del disco rigido virtuale per l'invio della certificazione del disco rigido virtuale.
> 
> 

## <a name="develop-your-vhd"></a>Sviluppare il disco rigido virtuale
È possibile sviluppare i dischi rigidi virtuali di Azure nel cloud o in locale:

* Lo sviluppo basato sul cloud implica che tutte le operazioni di sviluppo vengono eseguite in modalità remota su un disco rigido virtuale residente in Azure.
* Lo sviluppo in locale richiede il download di un disco rigido virtuale e il relativo sviluppo tramite l'infrastruttura locale. Sebbene questa operazione sia possibile, non è consigliabile. Si noti che lo sviluppo per Windows o SQL in locale richiede la disponibilità in locale dei codici di licenza appropriati. Non è possibile includere o installare SQL Server dopo aver creato una VM. È inoltre necessario basare l’offerta su un'immagine SQL approvata dal portale di Azure. Se si decide di eseguire lo sviluppo in locale, è necessario effettuare alcuni passaggi in modo diverso rispetto allo sviluppo nel cloud. È possibile trovare le informazioni appropriate in [Creare un'immagine di VM in locale](marketplace-publishing-vm-image-creation-on-premise.md).

[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md

