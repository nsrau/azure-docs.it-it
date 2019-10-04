---
title: Guida alla pubblicazione dell'offerta di applicazione gestita delle applicazioni di Azure
description: Questo articolo descrive i requisiti per pubblicare un'applicazione gestita nel Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: qianw211
manager: evansma
ms.service: marketplace
ms.topic: article
ms.date: 06/14/2018
ms.author: v-qiwe
ms.openlocfilehash: 29546b0969751a43959a55860fc22e9f3c3e225b
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154951"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Applicazioni Azure: Guida alla pubblicazione dell'offerta di applicazione gestita

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
|Distribuiti alla sottoscrizione di Azure del cliente | Le app gestite devono essere distribuite nella sottoscrizione del cliente e possono essere gestite da terze parti. | 
|Fatturazione e misurazione    |  Verrà eseguito il provisioning di risorse nella sottoscrizione di Azure del cliente. Le transazioni delle macchine virtuali con pagamento in base al consumo (PAYGO) verranno effettuate con il cliente tramite Microsoft e fatturate tramite la sottoscrizione di Azure del cliente (PAYGO). <br> Nel caso di bring-your-own-license, mentre Microsoft addebiterà i costi di infrastruttura sostenuti per la sottoscrizione del cliente, si verrà transact tariffe al cliente di licenza direttamente.        |
|Disco rigido virtuale (VHD) compatibile con Azure    |   Le macchine virtuali devono essere compilate in Windows o Linux.<ul> <ul> <li>Per altre informazioni sulla creazione di un disco rigido virtuale Linux, vedere [Distribuzioni di Linux approvate in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Per altre informazioni sulla creazione di un disco rigido virtuale Windows, [vedere Creare un disco rigido virtuale compatibile con Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |

>[!NOTE]
> Le app gestite devono essere distribuibili tramite il Marketplace. Se la comunicazione con il cliente rappresenta un problema, contattare i clienti interessati dopo aver attivato la condivisione dei clienti potenziali.  

>[!Note]
>Cloud Solution Provider (CSP) partner canale acconsentire esplicitamente a questo punto è disponibile.  Vedi [Cloud Solution Provider](./cloud-solution-providers.md) per altre informazioni sul marketing dell'offerta tramite Microsoft CSP partner canali.

## <a name="next-steps"></a>Passaggi successivi
Se non è già stato fatto, 

- [Registrarsi](https://azuremarketplace.microsoft.com/sell) in Marketplace.

Se la registrazione è già stata effettuata e si sta creando una nuova offerta o lavorando su una esistente,

- [Accedere al portale Cloud Partner](https://cloudpartner.azure.com) per creare o completare l'offerta.
