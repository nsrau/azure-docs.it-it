---
title: Guida alla pubblicazione dell'offerta di applicazione gestita delle applicazioni di Azure
description: Questo articolo descrive i requisiti per pubblicare un'applicazione gestita nel Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/14/2018
ms.author: dsindona
ms.openlocfilehash: 38323ecfee69460b16542a3e004513f6a7389c41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288411"
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
|Distribuito nella sottoscrizione di Azure di un clienteDeployed to a customer's Azure subscription | Le app gestite devono essere distribuite nella sottoscrizione del cliente e possono essere gestite da terze parti. | 
|Fatturazione e misurazione    |  Verrà eseguito il provisioning delle risorse nella sottoscrizione di Azure del cliente. Le macchine virtuali PAYGO (Pay-as-you-go) verranno effettuate transazioni con il cliente tramite Microsoft, fatturate tramite la sottoscrizione di Azure del cliente (PAYGO). <br> Nel caso di licenza bring-your-own, mentre Microsoft addebiterà i costi dell'infrastruttura sostenuti nell'abbonamento del cliente, si correranno le spese di licenza del software al cliente direttamente.        |
|Disco rigido virtuale (VHD) compatibile con Azure    |   Le macchine virtuali devono essere compilate in Windows o Linux.<ul> <ul> <li>Per altre informazioni sulla creazione di un disco rigido virtuale Linux, vedere [Distribuzioni di Linux approvate in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Per altre informazioni sulla creazione di un disco rigido virtuale Windows, [vedere Creare un disco rigido virtuale compatibile con Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |

>[!NOTE]
> Le app gestite devono essere distribuibili tramite il Marketplace. Se la comunicazione con il cliente rappresenta un problema, contattare i clienti interessati dopo aver attivato la condivisione dei clienti potenziali.  

>[!Note]
>L'opt-in del canale per i partner Cloud Solution Provider (CSP) è ora disponibile.  Per ulteriori informazioni sul marketing dell'offerta tramite i canali partner Microsoft CSP, consulta [Cloud Solution Provider.](./cloud-solution-providers.md)

## <a name="next-steps"></a>Passaggi successivi
Se non è già stato fatto, 

- [Registrati](https://azuremarketplace.microsoft.com/sell) nel marketplace.

Se la registrazione è già stata effettuata e si sta creando una nuova offerta o lavorando su una esistente,

- [Accedi al portale Cloud Partner](https://cloudpartner.azure.com) per creare o completare la tua offerta.
