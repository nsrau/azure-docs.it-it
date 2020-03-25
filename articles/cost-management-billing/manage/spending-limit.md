---
title: Limite di spesa di Azure
description: Questo articolo descrive il funzionamento del limite di spesa di Azure e spiega come rimuoverlo.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 6feac116c92f8dac7bd1dde2084f9b25f5e559f8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238058"
---
# <a name="azure-spending-limit"></a>Limite di spesa di Azure

Il limite di spesa in Azure impedisce spese superiori all'importo del credito. Per tutti i nuovi clienti che eseguono l'iscrizione per un account Azure gratuito o per tipi di sottoscrizioni che includono crediti per più mesi, il limite di spesa è attivato per impostazione predefinita. Il limite di spesa corrisponde all'importo del credito e non può essere modificato. Ad esempio, se è stata effettuata l'iscrizione per un account Azure gratuito, il limite di spesa è pari a $ 200 e non è possibile aumentarlo a $ 500. È tuttavia possibile rimuovere il limite di spesa. Di conseguenza, o non è presente alcun limite oppure è presente un limite pari all'importo del credito. Questo preclude la maggior parte delle tipologie di spesa. Il limite di spesa non è disponibile per le sottoscrizioni con piani di impegno e piani che prevedono prezzi con pagamento in base al consumo. Vedere l'[elenco completo dei tipi di sottoscrizioni di Azure e la disponibilità del limite di spesa](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Raggiungimento di un limite di spesa

Quando l'utilizzo effettuato genera addebiti che esauriscono il limite di spesa, i servizi distribuiti vengono disabilitati per i giorni del periodo di fatturazione rimanenti.

Se, ad esempio, si spende tutto il credito incluso nell'account Azure gratuito, le risorse di Azure distribuite vengono rimosse dalla produzione e le macchine virtuali di Azure vengono arrestate e deallocate. I dati negli account di archiviazione sono disponibili in sola lettura.

Se il tipo di sottoscrizione include crediti per più mesi, la sottoscrizione verrà riabilitata automaticamente all'inizio del periodo di fatturazione successivo. È possibile quindi ridistribuire le risorse di Azure e avere accesso completo agli account e ai database di archiviazione.

Al raggiungimento del limite di spesa, Azure invia notifiche di posta elettronica. Accedere al [Portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) per visualizzare le notifiche relative alle sottoscrizioni che hanno raggiunto il limite di spesa.

Se si usa un account Azure gratuito e si raggiunge il limite di spesa, è possibile eseguire l'aggiornamento a un piano con [pagamento in base al consumo](upgrade-azure-subscription.md) per rimuovere il limite di spesa e riabilitare automaticamente la sottoscrizione.

## <a name="remove-the-spending-limit-in-azure-portal"></a>Rimuovere il limite di spesa nel portale di Azure

È possibile rimuovere il limite di spesa in qualsiasi momento, purché alla sottoscrizione di Azure sia associato un metodo di pagamento valido. Per i tipi di sottoscrizione con credito su più mesi, come Visual Studio Enterprise e Visual Studio Professional, è anche possibile scegliere di rimuovere il limite di spesa per un periodo illimitato o solo per il periodo di fatturazione corrente. Se si sceglie solo il periodo di fatturazione corrente, il limite di spesa viene abilitato automaticamente all'inizio del periodo di fatturazione successivo.

Se si ha un account Azure gratuito, vedere [Aggiornare la sottoscrizione di Azure](upgrade-azure-subscription.md) per rimuovere il limite di spesa. In caso contrario, seguire questa procedura per rimuovere il limite di spesa:

<a id="remove"></a>

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore account.
1. Cercare **Gestione dei costi e fatturazione**.

    ![Screenshot che mostra la ricerca di Gestione dei costi e fatturazione ](./media/spending-limit/search-bar.png)

1. Nell'elenco **Sottoscrizioni personali** selezionare la propria sottoscrizione. Ad esempio, *Visual Studio Enterprise*.

   ![Screenshot che mostra la griglia delle sottoscrizioni personali in visualizzazione panoramica](./media/spending-limit/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Se non vengono visualizzate alcune sottoscrizioni di Visual Studio, è possibile che la directory della sottoscrizione sia stata modificata in un determinato momento. Per queste sottoscrizioni, è necessario passare alla directory originale (la directory in cui è stata effettuata l'iscrizione iniziale). Quindi, ripetere il passaggio 2.

1. Nella visualizzazione panoramica della sottoscrizione fare clic sul banner arancione per rimuovere il limite di spesa.

    ![Screenshot che mostra il banner per la rimozione del limite di spesa](./media/spending-limit/msdn-remove-spending-limit-banner-x.png)

1. Scegliere se si vuole rimuovere il limite di spesa per un periodo illimitato o solo per il periodo di fatturazione corrente.

      ![Screenshot che mostra il pannello per la rimozione del limite di spesa](./media/spending-limit/remove-spending-limit-blade-x.png)

      | Opzione | Effetto |
      | --- | --- |
      | Rimuovi il limite di spesa per un periodo illimitato | Il limite di spesa non viene riattivato automaticamente all'inizio del periodo di fatturazione successivo. È però possibile riattivarlo manualmente in qualsiasi momento. |
      | Rimuovi il limite di spesa per il periodo di fatturazione corrente | Il limite di spesa viene riattivato automaticamente all'inizio del periodo di fatturazione successivo. |


1. Fare clic su **Selezionare un metodo di pagamento** per scegliere un metodo di pagamento per la sottoscrizione. Questo diventerà il metodo di pagamento attivo per la sottoscrizione.

1. Fare clic su **Fine**.


## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Perché può essere necessario rimuovere il limite di spesa

Il limite di spesa può impedire all'utente di distribuire o usare determinati servizi Microsoft e di terze parti. Ecco le situazioni in cui è consigliabile rimuovere il limite di spesa per la sottoscrizione.

-  Se si prevede di distribuire immagini di terze parti come Oracle o servizi come Azure DevOps Services. Questa situazione comporta il superamento quasi immediato del limite di spesa e la disabilitazione della sottoscrizione.
- Se si usano servizi che non devono essere interrotti. Quando si raggiunge il limite di spesa, le risorse di Azure distribuite vengono rimosse dall'ambiente di produzione e le macchine virtuali di Azure vengono arrestate e deallocate. Per evitare che i servizi usati vengano interrotti, è necessario rimuovere il limite di spesa.
- Se si usano servizi e risorse con impostazioni come indirizzi IP virtuali che non si vuole perdere. Queste impostazioni vanno perse quando si raggiunge il limite di spesa e le risorse e i servizi vengono deallocati.

## <a name="turn-on-the-spending-limit-after-removing"></a>Attivare il limite di spesa dopo la rimozione

Questa funzionalità è disponibile solo quando il limite di spesa è stato rimosso per un periodo illimitato per i tipi di sottoscrizioni che includono crediti su più mesi. È possibile usare questa funzionalità per attivare automaticamente il limite di spesa all'inizio del periodo di fatturazione successivo.


1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore account.
1. Cercare **Gestione dei costi e fatturazione**.

    ![Screenshot che mostra la ricerca di Gestione dei costi e fatturazione ](./media/spending-limit/search-bar.png)

1. Nell'elenco **Sottoscrizioni personali** selezionare la propria sottoscrizione. Ad esempio, *Visual Studio Enterprise*.

   ![Screenshot che mostra la griglia delle sottoscrizioni personali in visualizzazione panoramica](./media/spending-limit/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Se non vengono visualizzate alcune sottoscrizioni di Visual Studio, è possibile che la directory della sottoscrizione sia stata modificata in un determinato momento. Per queste sottoscrizioni, è necessario passare alla directory originale (la directory in cui è stata effettuata l'iscrizione iniziale). Quindi, ripetere il passaggio 2.

1. Nella pagina di panoramica della sottoscrizione fare clic sul banner nella parte superiore della pagina per riattivare il limite di spesa.

## <a name="custom-spending-limit"></a>Limite di spesa personalizzato

Non sono disponili limiti di spesa personalizzati.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>Un limite di spesa non impedisce qualsiasi addebito

[Alcuni servizi esterni pubblicati in Azure Marketplace](../understand/understand-azure-marketplace-charges.md) non possono essere usati con i crediti per la sottoscrizione e possono essere addebitati separatamente anche quando è impostato il limite di spesa. Questo succede, ad esempio, per le licenze di Visual Studio, Azure Active Directory Premium, i piani di supporto e la maggior parte dei servizi personalizzati di terze parti. Quando si esegue il provisioning di un nuovo servizio esterno, viene visualizzato un avviso per informare che i servizi vengono fatturati separatamente:

![Avviso di acquisto di Marketplace](./media/spending-limit/marketplace-warning01.png)

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi
- Eseguire l'aggiornamento a un piano che prevede prezzi [con pagamento in base al consumo](upgrade-azure-subscription.md).
