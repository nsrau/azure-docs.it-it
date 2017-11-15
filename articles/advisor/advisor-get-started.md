---
title: Introduzione ad Azure Advisor | Microsoft Docs
description: Introduzione ad Azure Advisor.
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: makohli
ms.openlocfilehash: dc89cd29e1e8038f0ff317ff6acee332218ebce7
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2017
---
# <a name="get-started-with-azure-advisor"></a>Introduzione ad Azure Advisor

Informazioni su come accedere ad Advisor tramite il portale di Azure, ricevere e implementare i consigli.

## <a name="get-advisor-recommendations"></a>Ricevere consigli da Advisor

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel riquadro sinistro fare clic su **Advisor**.  Se Advisor non è disponibile nel riquadro sinistro, fare clic su **Altri servizi**.  Nel riquadro del menu dei servizi, in **Monitoraggio e gestione** fare clic su **Advisor**.
 Verrà visualizzato il dashboard di Advisor.

   ![Accedere ad Azure Advisor tramite il Portale di Azure](./media/advisor-get-started/advisor-portal-menu.png) 

4. Nel dashboard di Advisor viene visualizzato un riepilogo dei consigli per tutte le sottoscrizioni selezionate.  È possibile scegliere le sottoscrizioni per le quali si vuole ottenere i consigli usando come filtro il menu a discesa delle sottoscrizioni.

5. Per ricevere consigli per una categoria specifica, fare clic su una delle schede seguenti: **Disponibilità elevata**, **Sicurezza**, **Prestazioni** o **Costo**.
 
> [!NOTE]
> Per usare Azure Advisor con una sottoscrizione, è necessario che un *proprietario* della sottoscrizione avvii il dashboard di Advisor.  Questa azione registra la sottoscrizione con Advisor.  Da questo momento in poi qualsiasi *proprietario*, *collaboratore* o *lettore* della sottoscrizione può accedere ai consigli di Advisor per la sottoscrizione.  

  ![Dashboard di Azure Advisor](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-advisor-recommendation-details-and-implement-a-solution"></a>Ottenere i dettagli dei consigli di Advisor e implementare una soluzione

È possibile selezionare un consiglio in Advisor per visualizzare dettagli aggiuntivi, ad esempio le azioni consigliate e le risorse che sono interessate dalle azioni, e per implementare la soluzione del consiglio.  

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi aprire [Advisor](https://aka.ms/azureadvisordashboard).

2. Selezionare una categoria di consigli per visualizzare l'elenco di suggerimenti disponibili in tale categoria oppure selezionare la scheda **Tutto** per visualizzare tutti i consigli.

3. Fare clic su un consiglio che si intende esaminare in dettaglio.

4. Esaminare le informazioni sul consiglio e sulle risorse a cui si applica.

5. Fare clic su **Azione consigliata** per implementare il consiglio.

## <a name="filter-advisor-recommendations"></a>Filtrare i consigli di Advisor

È possibile filtrare i consigli alla ricerca di quello più consono alle proprie esigenze.  Il filtro può essere applicato in base alla sottoscrizione, al tipo di risorsa o allo stato di consiglio.  

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi aprire [Advisor](https://aka.ms/azureadvisordashboard).

2.  Usare i menu a discesa nel dashboard di Advisor per filtrare in base alla sottoscrizione, al tipo di risorsa o allo stato di consiglio.

    ![Criteri dei filtri di ricerca di Advisor](./media/advisor-get-started/advisor-filters.png)

## <a name="snooze-or-dismiss-advisor-recommendations"></a>Posporre o ignorare i consigli di Advisor

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi aprire [Advisor](https://aka.ms/azureadvisordashboard).

2. Passare al consiglio che si intende posporre o eliminare.

3. Fare clic sul consiglio.

4. Fare clic su **Posponi**. 

5. È possibile specificare il periodo di tempo in base al quale posporre il consiglio oppure selezionare **Mai** per ignorare il consiglio.

## <a name="exclude-subscriptions-or-resource-groups-from-advisor"></a>Escludere sottoscrizioni o gruppi di risorse da Advisor

È possibile che si disponga di gruppi di risorse, come le risorse di test, e di sottoscrizioni per i quali non si vuole ricevere i consigli di Advisor.  Si può configurare Advisor in modo che generi consigli solo per sottoscrizioni e gruppi di risorse specifici.

> [!NOTE]
> Per includere o escludere una sottoscrizione o un gruppo di risorse da Advisor, è necessario essere un proprietario della sottoscrizione.  Se non si dispone delle autorizzazioni necessarie per una sottoscrizione o un gruppo di risorse, l'opzione per includere o escludere la sottoscrizione o il gruppo di risorse è disabilitata nell'interfaccia utente.

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi aprire [Advisor](https://aka.ms/azureadvisordashboard).

2. Fare clic su **Configura** nella barra delle azioni.

3. Deselezionare tutte le sottoscrizioni o i gruppi di risorse per i quali non si intende ricevere i consigli di Advisor.

    ![Esempio di configurazione di risorse in Advisor](./media/advisor-get-started/advisor-configure-resources.png)

4. Fare clic sul pulsante **Apply** (Applica).

## <a name="configure-the-average-cpu-utilization-rule-for-the-low-usage-virtual-machine-recommendation"></a>Configurare la regola di utilizzo medio della CPU per il consiglio sulle macchine virtuali a utilizzo ridotto

Advisor monitora l'utilizzo delle macchine virtuali per 14 giorni, in modo da identificare le macchine virtuali il cui utilizzo è ridotto. Le macchine virtuali con un utilizzo della CPU pari al 5% o inferiore e un utilizzo di rete pari a 7 MB o inferiore per quattro o più giorni sono considerate macchine virtuali a utilizzo ridotto.

Per un'individuazione più mirata delle macchine virtuali a utilizzo ridotto, è possibile perfezionare la regola di utilizzo medio della CPU in base alla sottoscrizione.  La regola di utilizzo medio della CPU può essere impostata su 5%, 10%, 15% o 20%.

> [!NOTE]
> Per modificare questa regola per identificare le macchine virtuali a utilizzo ridotto, è necessario essere un *proprietario* della sottoscrizione.  Se non si dispone delle autorizzazioni necessarie per una sottoscrizione o un gruppo di risorse, l'opzione per includere o escludere la sottoscrizione o il gruppo di risorse è disabilitata nell'interfaccia utente. 

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi aprire [Advisor](https://aka.ms/azureadvisordashboard).

2. Fare clic su **Configura** nella barra delle azioni.

3. Fare clic sulla scheda **Regole**.

4. Selezionare le sottoscrizioni per cui vuole modificare la regola di utilizzo medio della CPU e quindi fare clic su **Modifica**.

5. Selezionare il valore di utilizzo medio della CPU desiderato e fare clic su **Applica**.

6. Fare clic su **Aggiorna raccomandazioni** per aggiornare i consigli esistenti all'uso della nuova regola di utilizzo medio della CPU. 

   ![Esempio di configurazione di una regola di consiglio in Advisor](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-your-advisor-recommendations"></a>Scaricare i consigli di Advisor

Advisor consente di scaricare un riepilogo dei consigli  come file con estensione PDF o con estensione CSV.  Il riepilogo scaricato può essere facilmente condiviso con i colleghi o usato per effettuare analisi personalizzate in base ai dati dei consigli.

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi aprire [Advisor](https://aka.ms/azureadvisordashboard).

2. Nella barra delle azioni fare clic su **Scarica come CSV** o su **Scarica come PDF**.

L'opzione di download rispetta tutti i filtri che sono stati applicati al dashboard di Advisor.  Se si seleziona l'opzione di download mentre si visualizza una categoria di consigli specifica o un consiglio specifico, il riepilogo scaricato includerà solo le informazioni relative alla categoria o al consiglio specifico. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Advisor, vedere:
* [Introduction to Azure Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
* [Advisor High Availability recommendations](advisor-high-availability-recommendations.md) (Consigli di Advisor sulla disponibilità elevata)
* [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza)
-  [Advisor Performance recommendations](advisor-performance-recommendations.md) (Consigli di Advisor sulle prestazioni)
* [Advisor Cost recommendations](advisor-performance-recommendations.md) (Consigli di Advisor sui costi)
