---
title: Guida alla pubblicazione dell'offerta di applicazione gestita delle applicazioni di Azure
description: Questo articolo descrive i requisiti per pubblicare un'applicazione gestita nel Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 56281ff8faba6b4b950334e2b0018d48c8e7aeb3
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687571"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Applicazioni di Azure: Guida alla pubblicazione dell'offerta di applicazione gestita

Un'applicazione gestita è uno dei modi principali per pubblicare una soluzione nel Marketplace. Usare questa guida per comprendere i requisiti per questa offerta. 

Si tratta di offerte di transazione distribuite e fatturate tramite Marketplace. L'utente viene invitato all'azione tramite "Scarica adesso".

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
|Disco rigido virtuale (VHD) compatibile con Azure    |   Le macchine virtuali devono essere compilate in Windows o Linux.<ul> <ul> <li>Per altre informazioni sulla creazione di un disco rigido virtuale Linux, vedere [Distribuzioni di Linux approvate in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Per altre informazioni sulla creazione di un disco rigido virtuale di Windows, vedere [Creare un'offerta](./partner-center-portal/create-new-azure-apps-offer.md)di applicazione Azure.For more information about creating a Windows VHD, see create an Azure application offer.</li> </ul> |

>[!NOTE]
> Le app gestite devono essere distribuibili tramite il Marketplace. Se la comunicazione con il cliente rappresenta un problema, contattare i clienti interessati dopo aver attivato la condivisione dei clienti potenziali.  

>[!Note]
>L'opt-in del canale per i partner Cloud Solution Provider (CSP) è ora disponibile.  Per ulteriori informazioni sul marketing dell'offerta tramite i canali partner Microsoft CSP, consulta [Cloud Solution Provider.](./cloud-solution-providers.md)

## <a name="next-steps"></a>Passaggi successivi

Se non è già stato fatto, 

- [Scopri](https://azuremarketplace.microsoft.com/sell) di più sul mercato.

Per registrarti al Centro per i partner, inizia a creare una nuova offerta o a lavorarla su una esistente:

- [Accedi al Centro per i partner](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) per creare o completare la tua offerta.
- Per altre informazioni, vedere [Creare un'offerta di applicazione Azure.See create an Azure application offer](./partner-center-portal/create-new-azure-apps-offer.md) for more information.
