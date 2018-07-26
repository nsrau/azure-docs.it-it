---
title: Guida alla pubblicazione dell'offerta di modello di soluzione delle Applicazioni di Azure
description: Questo articolo descrive i requisiti per pubblicare un modello di soluzione nel Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: 44d081a0666aa37ec0bf8eeac540b7a7f4f4f904
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/14/2018
ms.locfileid: "39057883"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Applicazioni di Azure: Guida alla pubblicazione dell'offerta di modello di soluzione

I modelli di soluzione sono uno dei modi principali per pubblicare una soluzione nel Marketplace di Azure. Usare questa guida per comprendere i requisiti per questa offerta. 

Sono offerte di transazioni che vengono distribuite e fatturate attraverso il Marketplace. L'utente viene invitato all'azione tramite "Scarica adesso".

Usare app Azure: l'offerta Modello di soluzione quando una soluzione richiede un'automazione aggiuntiva per la distribuzione e la configurazione oltre a una semplice macchina virtuale. È possibile automatizzare il provisioning di una o più macchine virtuali con le app di Azure: Modelli di soluzione. È anche possibile eseguire il provisioning di risorse di rete e di archiviazione. App di Azure: l'offerta Modello di soluzione offre i vantaggi di automazione per singole macchine virtuali e le soluzioni complete basate su IaaS.

## <a name="requirements-for-solution-templates"></a>Requisiti per i modelli di soluzioni

|Requisiti |Dettagli  |
|---------|---------|
|Fatturazione e misurazione    |  Verrà eseguito il provisioning di risorse nella sottoscrizione di Azure del cliente. Le transazioni delle macchine virtuali con pagamento in base al consumo (PAYGO) verranno effettuate con il cliente tramite Microsoft e fatturate tramite la sottoscrizione di Azure del cliente (PAYGO) 
Nel caso di Bring Your Own License, mentre Microsoft addebiterà i costi di infrastruttura sostenuti alla sottoscrizione del cliente, le transazioni relative alle tariffe delle licenze software verranno effettuate direttamente con il cliente        |
|Disco rigido virtuale (VHD) compatibile con Azure    |   Le macchine virtuali devono essere compilate in Windows o Linux.<ul> <li>Per altre informazioni sulla creazione di un disco rigido virtuale Linux, visitare la sezione Creare un VHD compatibile con Azure (basato su Linux) all'indirizzo [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Per altre informazioni sulla creazione di un disco rigido virtuale Windows, visitare la sezione Creare un VHD compatibile con Azure (basato su Windows) all'indirizzo [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul>      |



## <a name="next-steps"></a>Passaggi successivi
Se non è già stato fatto, 

- [Registrarsi](https://azuremarketplace.microsoft.com/sell) in Marketplace

Se la registrazione è già stata effettuata e si sta creando una nuova offerta o lavorando su una esistente,

- [Accedere al portale Cloud Partner](https://cloudpartner.azure.com) per creare e completare l'offerta
