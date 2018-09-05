---
title: Guida alla pubblicazione dell'offerta di applicazione gestita delle applicazioni di Azure
description: Questo articolo descrive i requisiti per pubblicare un'applicazione gestita nel Marketplace
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
ms.openlocfilehash: c8ead3dc34faefce0f113dee2074960fddfa11a1
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2018
ms.locfileid: "43144592"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Applicazioni di Azure: Guida alla pubblicazione dell'offerta di applicazione gestita

Un'applicazione gestita è uno dei modi principali per pubblicare una soluzione nel Marketplace. Usare questa guida per comprendere i requisiti per questa offerta. 

Sono offerte di transazioni che vengono distribuite e fatturate attraverso il Marketplace. L'utente viene invitato all'azione tramite "Scarica adesso".

Usare l'app di Azure: tipo di offerta di app gestita quando sono necessarie le condizioni seguenti:
- Si distribuisce una soluzione basata su sottoscrizione per i clienti che usano una macchina virtuale o un'intera soluzione basata su IaaS.
- L'utente o il cliente richiedono che la soluzione venga gestita da un partner.

>[!NOTE]
>Ad esempio, un partner potrebbe essere un integratore di sistema o provider di servizi gestiti (MSP).  

## <a name="managed-application-offer"></a>Offerta applicazione gestita

|Requisiti |Dettagli  |
|---------|---------|
|Distribuita a una sottoscrizione di Azure del cliente | Le app gestite devono essere distribuite nella sottoscrizione del cliente e possono essere gestite da terzi | 
|Fatturazione e misurazione    |  Verrà eseguito il provisioning di risorse nella sottoscrizione di Azure del cliente. Le transazioni delle macchine virtuali con pagamento in base al consumo (PAYGO) verranno effettuate con il cliente tramite Microsoft e fatturate tramite la sottoscrizione di Azure del cliente (PAYGO) 
Nel caso di Bring Your Own License, mentre Microsoft addebiterà i costi di infrastruttura sostenuti alla sottoscrizione del cliente, le transazioni relative alle tariffe delle licenze software verranno effettuate direttamente con il cliente        |
|Disco rigido virtuale (VHD) compatibile con Azure    |   Le macchine virtuali devono essere compilate in Windows o Linux.<ul> <li>Per altre informazioni sulla creazione di un disco rigido virtuale Linux, visitare la sezione Creare un VHD compatibile con Azure (basato su Linux) all'indirizzo [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Per altre informazioni sulla creazione di un disco rigido virtuale Windows, visitare la sezione Creare un VHD compatibile con Azure (basato su Windows) all'indirizzo [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul>      |

>[!NOTE]
> Le app gestite devono essere distribuibili tramite il Marketplace. Se la comunicazione con il cliente rappresenta un problema, contattare i clienti interessati dopo aver attivato la condivisione dei clienti potenziali.  


## <a name="next-steps"></a>Passaggi successivi
Se non è già stato fatto, 

- [Registrarsi](https://azuremarketplace.microsoft.com/sell) in Marketplace

Se la registrazione è già stata effettuata e si sta creando una nuova offerta o lavorando su una esistente,

- [Accedere al portale Cloud Partner](https://cloudpartner.azure.com) per creare e completare l'offerta
