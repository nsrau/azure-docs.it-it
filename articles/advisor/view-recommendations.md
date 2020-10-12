---
title: Visualizza Azure Advisor raccomandazioni più importanti
description: Visualizzare e filtrare Azure Advisor consigli per ridurre il rumore.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 43bdaba7d774bba8857a6eb3ef296d7ab8e1c264
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90986862"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>Visualizza Azure Advisor raccomandazioni più importanti

Azure Advisor fornisce consigli utili per ottimizzare le distribuzioni di Azure. All'interno di Advisor, è possibile accedere a alcune funzionalità che consentono di restringere le raccomandazioni solo a quelle che interessano.

## <a name="configure-subscriptions-and-resource-groups"></a>Configurare le sottoscrizioni e i gruppi di risorse

Advisor consente di selezionare le sottoscrizioni e i gruppi di risorse che interessano l'utente e l'organizzazione. Vengono visualizzate solo le raccomandazioni per le sottoscrizioni e i gruppi di risorse selezionati. Per impostazione predefinita, vengono selezionati tutti. Le impostazioni di configurazione si applicano alla sottoscrizione o al gruppo di risorse, quindi le stesse impostazioni si applicano a tutti gli utenti che hanno accesso alla sottoscrizione o al gruppo di risorse. È possibile modificare le impostazioni di configurazione nel portale di Azure o a livello di codice.

Per apportare modifiche all'portale di Azure:

1. Aprire [Azure Advisor](https://aka.ms/azureadvisordashboard) nel portale di Azure.

1. Selezionare **configurazione** dal menu.

   ![Menu di configurazione di Advisor](./media/view-recommendations/configuration.png)

1. Selezionare la casella nella colonna **Includi** per le sottoscrizioni o i gruppi di risorse per ricevere le raccomandazioni di Advisor. Se la casella è disabilitata, è possibile che non si disponga dell'autorizzazione necessaria per apportare una modifica alla configurazione della sottoscrizione o del gruppo di risorse. Altre informazioni sulle [autorizzazioni sono disponibili in Azure Advisor](permissions.md).

1. Fare clic su **applica** nella parte inferiore dopo avere apportato una modifica.

## <a name="filtering-your-view-in-the-azure-portal"></a>Filtrare la visualizzazione nella portale di Azure

Le impostazioni di configurazione rimangono attive finché non vengono modificate. Se si desidera limitare la visualizzazione delle indicazioni per una singola visualizzazione, è possibile utilizzare gli elenchi a discesa disponibili nella parte superiore del pannello Advisor. Dai pannelli Panoramica, disponibilità elevata, sicurezza, prestazioni, costi e tutti i pannelli di raccomandazione, è possibile selezionare le sottoscrizioni, i tipi di risorse e lo stato di raccomandazione che si desidera visualizzare.

   :::image type="content" source="./media/view-recommendations/filtering.png" alt-text="Screenshot del Azure Advisor che mostra le opzioni di filtro.":::

## <a name="dismissing-and-postponing-recommendations"></a>Suggerimenti per l'immissione e il rinvio

Azure Advisor consente di ignorare o posticipare le raccomandazioni su una singola risorsa. Se si ignora una raccomandazione, non viene più visualizzata, a meno che non venga attivata manualmente. Tuttavia, se si rinvia una raccomandazione, è possibile specificare un periodo di tempo dopo il quale la raccomandazione viene nuovamente attivata automaticamente. Il rinvio può essere eseguito nel portale di Azure o a livello di codice.

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>Posticipare un'unica raccomandazione nel portale di Azure 

1. Aprire [Azure Advisor](https://aka.ms/azureadvisordashboard) nel portale di Azure.
1. Selezionare una categoria di raccomandazioni per visualizzare le raccomandazioni
1. Selezionare una raccomandazione nell'elenco di raccomandazioni
1. Selezionare posticipa o Ignora per l'indicazione che si vuole posticipare o ignorare

     :::image type="content" source="./media/view-recommendations/postpone-dismiss.png" alt-text="Screenshot del Azure Advisor che mostra le opzioni di filtro.":::

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>Posticipare o ignorare più raccomandazioni nell'portale di Azure

1. Aprire [Azure Advisor](https://aka.ms/azureadvisordashboard) nel portale di Azure.
1. Selezionare una categoria di raccomandazioni per visualizzare le raccomandazioni.
1. Selezionare una raccomandazione nell'elenco di raccomandazioni.
1. Selezionare la casella di controllo a sinistra della riga per tutte le risorse che si desidera posticipare o ignorare la raccomandazione.
1. Selezionare **posticipa** o **Ignora** nella parte superiore sinistra della tabella.

     :::image type="content" source="./media/view-recommendations/postpone-dismiss-multiple.png" alt-text="Screenshot del Azure Advisor che mostra le opzioni di filtro.":::

> [!NOTE]
> È necessario disporre dell'autorizzazione Collaboratore o proprietario per ignorare o posticipare un suggerimento. Altre informazioni sulle autorizzazioni sono disponibili in Azure Advisor.

> [!NOTE]
> Se le caselle di selezione sono disabilitate, è possibile che i suggerimenti vengano ancora caricati. Attendere il caricamento di tutte le raccomandazioni prima di provare a posticipare o ignorare.

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>Riattivare una raccomandazione posticipata o dispersa

È possibile attivare una raccomandazione che è stata posticipata o rilasciata. Questa azione può essere eseguita nel portale di Azure o a livello di codice. Nel portale di Azure:

1. Aprire [Azure Advisor](https://aka.ms/azureadvisordashboard) nel portale di Azure.

1. Modificare il filtro nel pannello panoramica su **posticipato**. Advisor Visualizza quindi le raccomandazioni rimandate o non rilasciate.

    :::image type="content" source="./media/view-recommendations/activate-postponed.png" alt-text="Screenshot del Azure Advisor che mostra le opzioni di filtro.":::

1. Selezionare una categoria per visualizzare le raccomandazioni **rimandate** e non **rilasciate** .

1. Selezionare una raccomandazione nell'elenco di raccomandazioni. In questo modo vengono aperte le raccomandazioni con la scheda **posticipata &** annullata già selezionata per visualizzare le risorse per le quali la raccomandazione è stata posticipata o non è stata rilasciata.

1. Fare clic su **attiva** alla fine della riga. Una volta fatto clic, la raccomandazione è attiva per la risorsa e quindi è stata rimossa da questa tabella. La raccomandazione è ora visibile nella scheda **attiva** .
 
     :::image type="content" source="./media/view-recommendations/activate-postponed-2.png" alt-text="Screenshot del Azure Advisor che mostra le opzioni di filtro.":::

## <a name="next-steps"></a>Passaggi successivi

Questo articolo illustra come è possibile visualizzare le raccomandazioni più importanti in Azure Advisor. Per altre informazioni su Advisor, vedere: 

- [Cos'è Azure Advisor?](advisor-overview.md)
- [Introduzione con Advisor](advisor-get-started.md)
- [Autorizzazioni in Azure Advisor](permissions.md)



