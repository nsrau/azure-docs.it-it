---
title: Visualizza i consigli di Azure Advisor importanti per te
description: Visualizzare e filtrare i consigli di Azure Advisor per ridurre il rumore.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 10d7b16864f8e449dc51e870c5ff9f20d8c0dc87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422365"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>Visualizza i consigli di Azure Advisor importanti per te

Azure Advisor fornisce consigli per ottimizzare le distribuzioni di Azure.Azure Advisor provides recommendations to help you optimize your Azure deployments. All'interno di Advisor, hai accesso ad alcune funzioni che ti aiutano a restringere i tuoi consigli solo a quelli che ti interessano.

## <a name="configure-subscriptions-and-resource-groups"></a>Configurare sottoscrizioni e gruppi di risorse

Advisor offre la possibilità di selezionare le sottoscrizioni e i gruppi di risorse importanti per l'utente e l'organizzazione. Vengono visualizzati solo i consigli per le sottoscrizioni e i gruppi di risorse selezionati. Per impostazione predefinita, sono selezionati tutti. Le impostazioni di configurazione si applicano alla sottoscrizione o al gruppo di risorse, pertanto le stesse impostazioni si applicano a tutti gli utenti che hanno accesso a tale sottoscrizione o gruppo di risorse. Le impostazioni di configurazione possono essere modificate nel portale di Azure o a livello di codice.

Per apportare modifiche nel portale di Azure:To make changes in the Azure portal:

1. Aprire Azure Advisor nel portale di Azure.Open [Azure Advisor](https://aka.ms/azureadvisordashboard) in the Azure portal.

1. Selezionare **Configurazione** dal menu.

   ![Menu di configurazione di Advisor](./media/view-recommendations/configuration.png)

1. Selezionare la casella nella colonna **Includi** per consentire a tutte le sottoscrizioni o ai gruppi di risorse di ricevere i suggerimenti di Advisor. Se la casella è disabilitata, è possibile che non si disponga dell'autorizzazione per apportare una modifica alla configurazione per la sottoscrizione o il gruppo di risorse. Altre informazioni sulle autorizzazioni in Azure Advisor .Learn more about [permissions in Azure Advisor](permissions.md).

1. Fare clic su **Applica** nella parte inferiore dopo aver apportato una modifica.

## <a name="filtering-your-view-in-the-azure-portal"></a>Filtraggio della visualizzazione nel portale di AzureFiltering your view in the Azure portal

Le impostazioni di configurazione rimangono attive fino a quando non vengono modificate. Se si desidera limitare la visualizzazione dei suggerimenti per una singola visualizzazione, è possibile utilizzare i menu a discesa disponibili nella parte superiore del pannello Advisor. Dai pannelli Panoramica, Disponibilità elevata, Sicurezza, Prestazioni, Costo e Tutti i consigli, è possibile selezionare le sottoscrizioni, i tipi di risorse e lo stato dei suggerimenti che si desidera visualizzare.

   ![Menu filtro Advisor](./media/view-recommendations/filtering.png)

## <a name="dismissing-and-postponing-recommendations"></a>Abbandonare e rinviare le raccomandazioni

Azure Advisor consente di ignorare o posticipare i suggerimenti per una singola risorsa. Se si ignora un suggerimento, non viene visualizzato di nuovo a meno che non venga attivato manualmente. Tuttavia, il rinvio di un suggerimento consente di specificare una durata dopo la quale la raccomandazione viene nuovamente attivata automaticamente. Il rinvio può essere eseguito nel portale di Azure o a livello di codice.

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>Posticipare una singola raccomandazione nel portale di AzurePostpone a single recommendation in the Azure portal 

1. Aprire Azure Advisor nel portale di Azure.Open [Azure Advisor](https://aka.ms/azureadvisordashboard) in the Azure portal.
1. Selezionare una categoria di raccomandazione per visualizzare i suggerimenti
1. Selezionare una raccomandazione dall'elenco dei suggerimenti
1. Selezionare Posticipa o Ignora per il suggerimento che si desidera posticipare o ignorare

     ![Menu filtro Advisor](./media/view-recommendations/postpone-dismiss.png)

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>Posticipare o ignorare più raccomandazioni nel portale di AzurePostio o ignorano più raccomandazioni nel portale di AzurePostpone or dismiss a multiple recommendations in the Azure portal

1. Aprire Azure Advisor nel portale di Azure.Open [Azure Advisor](https://aka.ms/azureadvisordashboard) in the Azure portal.
1. Selezionare una categoria di raccomandazione per visualizzare i suggerimenti.
1. Selezionare un suggerimento dall'elenco di raccomandazioni.
1. Selezionare la casella di controllo a sinistra della riga per tutte le risorse che si desidera posticipare o ignorare il suggerimento.
1. Selezionare **Posticipa** o **Ignora** nella parte superiore sinistra della tabella.

     ![Menu filtro Advisor](./media/view-recommendations/postpone-dismiss-multiple.png)

> [!NOTE]
> È necessaria l'autorizzazione del collaboratore o del proprietario per ignorare o posticipare un suggerimento. Altre informazioni sulle autorizzazioni in Azure Advisor.Learn more about permissions in Azure Advisor.

> [!NOTE]
> Se le caselle di selezione sono disabilitate, è possibile che le raccomandazioni siano ancora in fase di caricamento. Si prega di attendere che tutte le raccomandazioni da caricare prima di tentare di rinviare o chiudere.

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>Riattivare una raccomandazione posticipata o ignorata

È possibile attivare un suggerimento che è stato posticipato o chiuso. Questa azione può essere eseguita nel portale di Azure o a livello di codice. Nel portale di Azure:

1. Aprire Azure Advisor nel portale di Azure.Open [Azure Advisor](https://aka.ms/azureadvisordashboard) in the Azure portal.

1. Modificare il filtro nel pannello Panoramica in **Posticipato**. Advisor mostra quindi le raccomandazioni rinviate o ignorate.

    ![Menu filtro Advisor](./media/view-recommendations/activate-postponed.png)

1. Selezionare una categoria per visualizzare i suggerimenti **posticipati** e **ignorati.**

1. Selezionare un suggerimento dall'elenco di raccomandazioni. In questo modo verranno visualizzati suggerimenti con la scheda **Posticipato & Ignorato** già selezionata per mostrare le risorse per le quali la raccomandazione è stata posticipata o chiusa.

1. Fare clic su **Attiva** alla fine della riga. Dopo aver fatto clic, la raccomandazione è attiva per la risorsa e quindi rimossa da questa tabella. La raccomandazione è ora visibile nella scheda **Attivo.**
 
     ![Menu filtro Advisor](./media/view-recommendations/activate-postponed-2.png)

## <a name="next-steps"></a>Passaggi successivi

Questo articolo illustra come visualizzare i suggerimenti importanti in Azure Advisor.This article explains how you can view recommendations that you for you in Azure Advisor. Per altre informazioni su Advisor, vedere: 

- [Informazioni su Azure Advisor](advisor-overview.md)
- [Introduzione a Advisor](advisor-get-started.md)
- [Autorizzazioni in Azure AdvisorPermissions in Azure Advisor](permissions.md)



