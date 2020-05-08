---
title: Guida alla pubblicazione dell'offerta di applicazioni gestite di Azure-Azure Marketplace
description: Questo articolo descrive i requisiti per la pubblicazione di un'applicazione gestita in Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 5bd89e406c3067585595479dc1d8351e9ea7eea8
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856111"
---
# <a name="publishing-guide-for-azure-managed-applications"></a>Guida alla pubblicazione per le applicazioni gestite di Azure

Un'offerta di *applicazione gestita* di Azure è un modo per pubblicare un'applicazione Azure in Azure Marketplace. Le applicazioni gestite sono offerte per transazioni distribuite e fatturate tramite Azure Marketplace. La chiamata all'azione visualizzata da un utente è *ora*disponibile.

Questo articolo illustra i requisiti per il tipo di offerta di applicazione gestita.

Usare il tipo di offerta di applicazione gestita nelle condizioni seguenti:

- Si sta distribuendo una soluzione basata sulla sottoscrizione per il cliente usando una macchina virtuale (VM) o un'intera soluzione basata su infrastruttura distribuita come servizio (IaaS).
- L'utente o il cliente richiede che la soluzione sia gestita da un partner.

>[!NOTE]
>Un partner può essere ad esempio un integratore di sistemi o un provider di servizi gestiti (MSP).  

## <a name="managed-application-offer-requirements"></a>Requisiti dell'offerta di applicazione gestita

|Requisiti |Dettagli  |
|---------|---------|
|Una sottoscrizione di Azure. | Le applicazioni gestite devono essere distribuite nella sottoscrizione di un cliente, ma possono essere gestite da terze parti. |
|Fatturazione e misurazione    |  Le risorse vengono fornite nella sottoscrizione di Azure di un cliente. Le macchine virtuali che usano il modello di pagamento con pagamento in base al consumo vengono sottoposte a transazione con il cliente tramite Microsoft e fatturate tramite la sottoscrizione di Azure del cliente. <br><br> Per le macchine virtuali con licenza Bring-Your-Own, Microsoft addebita tutti i costi di infrastruttura sostenuti per la sottoscrizione del cliente, ma è possibile effettuare direttamente le spese di licenza software con il cliente.        |
|Un disco rigido virtuale (VHD) compatibile con Azure    |   Le macchine virtuali devono essere compilate in Windows o Linux.<br><br>Per altre informazioni sulla creazione di un disco rigido virtuale Linux, vedere [Distribuzioni di Linux approvate in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).<br><br>Per ulteriori informazioni sulla creazione di un disco rigido virtuale di Windows, vedere [creare un'offerta di applicazione Azure](./partner-center-portal/create-new-azure-apps-offer.md). |

---

> [!NOTE]
> Le applicazioni gestite devono essere distribuibili tramite Azure Marketplace. Se la comunicazione dei clienti rappresenta un problema, contattare i clienti interessati dopo aver abilitato la condivisione dei lead.  

> [!Note]
> È ora disponibile un canale partner del provider di soluzioni cloud (CSP). Per ulteriori informazioni sul marketing dell'offerta tramite i canali del partner Microsoft CSP, vedere [Cloud Solution Provider](./cloud-solution-providers.md).

## <a name="next-steps"></a>Passaggi successivi

Se non è già stato fatto, Scopri come [espandere il tuo business cloud con Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

Per eseguire la registrazione e iniziare a usare il centro per i partner:

- [Accedere al centro](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) per i partner per creare o completare l'offerta.
- Per altre informazioni, vedere [creare un'offerta di applicazione Azure](./partner-center-portal/create-new-azure-apps-offer.md) .
