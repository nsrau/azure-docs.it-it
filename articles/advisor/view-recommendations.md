---
title: Visualizzare le indicazioni di Azure Advisor importanti
description: Visualizzare e filtrare i consigli di Azure Advisor per ridurre il rumore.
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 04/03/2019
ms.author: kasparks
ms.openlocfilehash: 9f599a63fd5f52420f1b79e769d4f7bca9683b32
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59052838"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>Visualizzare le indicazioni di Azure Advisor importanti

Azure Advisor fornisce consigli per aiutarti a ottimizzare le distribuzioni di Azure. All'interno di Advisor, è possibile utilizzare alcune funzionalità che consentono di restringere le raccomandazioni solo a quelli importanti.

## <a name="configure-subscriptions-and-resource-groups"></a>Configurare le sottoscrizioni e gruppi di risorse

Advisor offre la possibilità di selezionare le sottoscrizioni e gruppi di risorse che sono importanti per la propria organizzazione. Visualizzare solo le raccomandazioni per le sottoscrizioni e gruppi di risorse selezionato. Per impostazione predefinita, vengono selezionati tutti. Le impostazioni di configurazione si applicano al gruppo di risorse o sottoscrizione, in modo che le stesse impostazioni si applicano a tutti gli utenti con accesso a tale sottoscrizione o gruppo di risorse. Impostazioni di configurazione possono essere modificate nel portale di Azure o a livello di codice.

Per apportare modifiche nel portale di Azure:

1. Aprire [Azure Advisor](https://aka.ms/azureadvisordashboard) nel portale di Azure.

1. Selezionare **configurazione** dal menu di scelta.

   ![Dal menu configurazione Advisor](./media/view-recommendations/configuration.png)

1. Selezionare la casella nella **inclusione** colonna per tutte le sottoscrizioni o gruppi di risorse per ricevere consigli di Advisor. Se la finestra è disabilitato, non si abbia l'autorizzazione per modificare una configurazione in tale gruppo di risorse o sottoscrizione. Altre informazioni sulle [le autorizzazioni in Azure Advisor](permissions.md).

1. Fare clic su **applica** nella parte inferiore dopo aver apportato una modifica.

## <a name="filtering-your-view-in-the-azure-portal"></a>Filtrare la visualizzazione nel portale di Azure

Le impostazioni di configurazione rimangono attive finché non vengono modificate. Se si desidera limitare la visualizzazione delle raccomandazioni per una visualizzazione singola, è possibile usare il menu a discesa forniti nella parte superiore del Pannello di Advisor. Dai pannelli panoramica, la disponibilità elevata, sicurezza, prestazioni, costi e tutte le raccomandazioni, è possibile selezionare le sottoscrizioni, i tipi di risorse e allo stato di Consiglio che si desidera visualizzare.

   ![Menu filtro di Advisor](./media/view-recommendations/filtering.png)

## <a name="dismissing-and-postponing-recommendations"></a>Chiudendo e posticipare le raccomandazioni

Azure Advisor consente di ignorare o posticipare le raccomandazioni su una singola risorsa. Se si elimina una raccomandazione, non è presente anche in questo caso a meno che non si attivarlo manualmente. Tuttavia, posticipare una raccomandazione consente di specificare un periodo di tempo dopo il quale la raccomandazione viene attivata automaticamente anche in questo caso. Posposizione può essere eseguita nel portale di Azure o a livello di codice.

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>Posporre il Consiglio un singolo nel portale di Azure 

1. Aprire [Azure Advisor](https://aka.ms/azureadvisordashboard) nel portale di Azure.
1. Selezionare una categoria di raccomandazione per visualizzare le raccomandazioni
1. Selezionare una raccomandazione nell'elenco delle raccomandazioni
1. Selezionare Posponi o Dismiss per l'indicazione che si vuole posporre o ignorare

     ![Menu filtro di Advisor](./media/view-recommendations/postpone-dismiss.png)

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>Posporre o ignorare una più le raccomandazioni presenti nel portale di Azure

1. Aprire [Azure Advisor](https://aka.ms/azureadvisordashboard) nel portale di Azure.
1. Selezionare una categoria di raccomandazione per visualizzare le raccomandazioni.
1. Selezionare una raccomandazione nell'elenco delle raccomandazioni.
1. Selezionare la casella di controllo a sinistra della riga per tutte le risorse che si vuole posporre o ignorare la raccomandazione.
1. Selezionare **Posponi** oppure **Dismiss** nella parte superiore sinistra della tabella.

     ![Menu filtro di Advisor](./media/view-recommendations/postpone-dismiss-multiple.png)

> [!NOTE]
> È necessaria l'autorizzazione di proprietario o collaboratore per ignorare o posticipare una raccomandazione. Altre informazioni sulle autorizzazioni in Azure Advisor.

> [!NOTE]
> Se le caselle di selezione sono disabilitate, consigli potrebbero ancora essere caricato. Attendere che tutte le raccomandazioni caricare prima di provare a posporre o ignorare.

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>Riattivare una raccomandazione dismissed o Posposto

È possibile attivare un'indicazione che è stata posticipata o chiusa. Questa azione può essere eseguita nel portale di Azure o a livello di codice. Nel portale di Azure:

1. Aprire [Azure Advisor](https://aka.ms/azureadvisordashboard) nel portale di Azure.

1. Modificare il filtro nel Pannello di panoramica **Posposto**. Advisor consente di visualizzare quindi i consigli posposti o ignorate.

    ![Menu filtro di Advisor](./media/view-recommendations/activate-postponed.png)

1. Selezionare una categoria per visualizzare **Posposto** e **Dismissed** raccomandazioni.

1. Selezionare una raccomandazione nell'elenco delle raccomandazioni. Ciò consente di aprire le raccomandazioni con i **posticipato & chiusa** scheda già selezionata per mostrare le risorse per il quale questa raccomandazione è stata posticipata o chiusa.

1. Fare clic su **Activate** alla fine della riga. Dopo aver fatto clic, la raccomandazione è attiva per la risorsa e quindi rimosso da questa tabella. La raccomandazione è ora visibile nel **Active** scheda.
 
     ![Menu filtro di Advisor](./media/view-recommendations/activate-postponed-2.png)

## <a name="next-steps"></a>Passaggi successivi

Questo articolo illustra come visualizzare le raccomandazioni rilevante per l'utente in Azure Advisor. Per altre informazioni su Advisor, vedere: 

- [Informazioni su Azure Advisor](advisor-overview.md)
- [Introduzione ad Advisor](advisor-get-started.md)
- [Autorizzazioni in Azure Advisor](permissions.md)



