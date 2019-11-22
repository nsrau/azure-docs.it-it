---
title: Introduzione all'account di fatturazione del Contratto Microsoft Partner - Azure CSP
description: Informazioni sull'account di fatturazione del Contratto Microsoft Partner (CSP)
author: bandersmsft
manager: amberbhargava
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2019
ms.author: banders
ms.openlocfilehash: 6bb0f3ea1b4e3bf17ed3bf26b838da8f2a694673
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72898558"
---
# <a name="get-started-with-your-microsoft-partner-agreement-billing-account"></a>Introduzione all'account di fatturazione del Contratto Microsoft Partner

L'account di fatturazione viene creato quando ci si iscrive per usare Azure. Viene usato per gestire le fatture e i pagamenti, oltre che per tenere traccia dei costi. È possibile avere accesso a più account di fatturazione. Ad esempio, l'iscrizione ad Azure potrebbe essere stata eseguita per i progetti personali. È anche possibile accedere ad Azure tramite il contratto Enterprise, il Contratto del cliente Microsoft o il Contratto Microsoft Partner dell'organizzazione. Per ognuno di questi scenari, si avrà un account di fatturazione distinto.

Le informazioni in questo articolo si applicano agli account di fatturazione per i contratti Microsoft Partner. Questi account vengono creati per i provider CSP e consentire loro di gestire la fatturazione per conto dei clienti nella nuova esperienza Commerce. La nuova esperienza è disponibile per i partner che hanno almeno un cliente che ha accettato un Contratto del cliente Microsoft con un piano di Azure. [Verificare di avere accesso a un Contratto Microsoft Partner](#check-access-to-a-microsoft-partner-agreement). Un [piano di Azure](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) offre ai clienti l'accesso ai servizi di Azure alle tariffe con pagamento in base al consumo in base al Contratto del cliente Microsoft.

## <a name="your-billing-account"></a>Account di fatturazione

L'account di fatturazione per il Contratto Microsoft Partner contiene un profilo di fatturazione per ogni valuta in cui l'azienda opera. Un profilo di fatturazione consente di gestire le fatture per le relative valute. Quando si stabiliscono relazioni con i clienti, a seconda delle rispettive valute, le sottoscrizioni di Azure e gli altri acquisti vengono fatturati nei rispettivi profili di fatturazione.

Il diagramma seguente mostra la relazione tra un account di fatturazione, i profili di fatturazione, i clienti e i rivenditori.

![Diagramma che mostra la gerarchia di fatturazione del Contratto Microsoft Partner](./media/mpa-overview/mpa-hierarchy.svg)

Gli utenti con ruolo **amministratore globale** e **agenti amministratori**  nell'organizzazione possono gestire gli account di fatturazione, i profili di fatturazione e i clienti. Per altre informazioni, vedere [Centro per i partner - Assegnare autorizzazioni e ruoli utente](https://docs.microsoft.com/partner-center/permissions-overview).

## <a name="billing-profiles"></a>Profili di fatturazione

Usare un profilo di fatturazione per gestire le fatture per una valuta. Viene generata una fattura mensile all'inizio del mese per ogni profilo di fatturazione incluso nell'account. La fattura contiene gli addebiti nella valuta del profilo di fatturazione per tutte le sottoscrizioni di Azure e gli altri acquisti del mese precedente.

È possibile visualizzare la fattura e scaricare i documenti correlati, ad esempio il file dell'utilizzo e l'elenco prezzi, nel portale di Azure. Per altre informazioni, vedere [Scaricare le fatture per un Contratto Microsoft Partner](billing-download-azure-invoice.md).

> [!IMPORTANT]
>
> Le fatture per i profili di fatturazione contengono gli addebiti per i clienti con piani di Azure, oltre ad acquisti di soluzioni SaaS, prodotti di Azure Marketplace e prenotazioni per i clienti che non hanno accettato il Contratto del cliente Microsoft e non hanno piani di Azure.

## <a name="customers"></a>Clienti

È possibile visualizzare e gestire i clienti che hanno accettato un Contratto del cliente Microsoft e hanno un piano di Azure nel portale di Azure. Per tali clienti è possibile visualizzare gli addebiti e le transazioni, nonché creare e gestire le sottoscrizioni di Azure.

### <a name="enable-policy-to-give-visibility-into-cost"></a>Abilitare i criteri per ottenere la visibilità sui costi

Applicare i criteri per controllare se gli utenti nell'organizzazione dei clienti possono visualizzare e analizzare i costi in base alle tariffe con pagamento in base al consumo per l'utilizzo di Azure. Per impostazione predefinita, il criterio è disattivato e gli utenti non possono visualizzare i costi. Una volta abilitato, gli utenti che dispongono dell'accesso per il [controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) appropriato per una sottoscrizione possono visualizzare e analizzare i costi per la sottoscrizione.

Per attivare il criterio:

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Eseguire ricerche in **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra una ricerca nel portale di Azure](./media/mpa-overview/search-cmb.png)

1. Selezionare **Clienti** sul lato sinistro e quindi selezionare un cliente dall'elenco.

   ![Screenshot che mostra la selezione del cliente](./media/mpa-overview/mpa-customers.png)

1. Selezionare **Criteri** sul lato sinistro.

   ![Screenshot che mostra i criteri](./media/mpa-overview/mpa-change-policy.png)

1. Selezionare **Sì**.

## <a name="resellers"></a>Rivenditori

I provider indiretti nel [modello a due livelli ](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview#azure-csp-direct-and-azure-csp-indirect) del programma CSP possono selezionare un rivenditore durante la creazione delle sottoscrizioni per i clienti nel portale di Azure. Dopo la creazione, possono visualizzare l'elenco delle sottoscrizioni, filtrato in base a un rivenditore e analizzare i costi di un cliente in base ai rivenditori nell'analisi dei costi di Azure.

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Verificare l'accesso a un Contratto Microsoft Partner
[!INCLUDE [billing-check-mpa](../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se si necessita assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'account di fatturazione, vedere gli articoli seguenti:

- [Creare un'altra sottoscrizione di Azure per il Contratto Microsoft Partner](billing-create-subscription.md)
- Integrare i dati di fatturazione con il proprio sistema di creazione di report usando le [API di fatturazione di Azure](https://docs.microsoft.com/rest/api/billing/)
- [Guida di avvio rapido per Gestione costi di Azure per i partner](https://go.microsoft.com/fwlink/?linkid=2106482)
