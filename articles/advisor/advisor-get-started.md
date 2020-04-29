---
title: Introduzione ad Azure Advisor
description: Introduzione ad Azure Advisor.
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: 8c2699030b1a6d428ddc2a4db40a66003824cf10
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79259630"
---
# <a name="get-started-with-azure-advisor"></a>Introduzione ad Azure Advisor

Informazioni su come accedere ad Advisor tramite il portale di Azure, ricevere e implementare i consigli.

> [!NOTE]
> Azure Advisor viene eseguito automaticamente in background per trovare le risorse appena create. Fornire consigli su tali risorse può richiedere fino a 24 ore.

## <a name="get-recommendations"></a>Ottenere raccomandazioni

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel riquadro sinistro fare clic su **Advisor**.  Se Advisor non è disponibile nel riquadro sinistro, fare clic su **Tutti i servizi**.  Nel riquadro del menu dei servizi, in **Monitoraggio e gestione** fare clic su **Advisor**. Verrà visualizzato il dashboard di Advisor.

   ![Accedere ad Azure Advisor tramite il Portale di Azure](./media/advisor-get-started/advisor-portal-menu.png) 

1. Nel dashboard di Advisor viene visualizzato un riepilogo dei consigli per tutte le sottoscrizioni selezionate.  È possibile scegliere le sottoscrizioni per le quali si vuole ottenere i consigli usando come filtro il menu a discesa delle sottoscrizioni.

1. Per ricevere consigli per una categoria specifica, fare clic su una delle schede seguenti: **Disponibilità elevata**, **Sicurezza**, **Prestazioni** o **Costo**. 

   ![Dashboard di Azure Advisor](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-recommendation-details-and-implement-a-solution"></a>Ottenere i dettagli dei consigli e implementare una soluzione

È possibile selezionare un consiglio in Advisor per visualizzare dettagli aggiuntivi, ad esempio le azioni consigliate e le risorse che sono interessate dalle azioni, e per implementare la soluzione del consiglio.  

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi aprire [Advisor](https://aka.ms/azureadvisordashboard).

1. Selezionare una categoria di consigli per visualizzare l'elenco di suggerimenti disponibili in tale categoria oppure selezionare la scheda **Tutto** per visualizzare tutti i consigli.

1. Fare clic su un consiglio che si intende esaminare in dettaglio.

1. Esaminare le informazioni sul consiglio e sulle risorse a cui si applica.

1. Fare clic su **Azione consigliata** per implementare il consiglio.

## <a name="filter-recommendations"></a>Filtrare le raccomandazioni

È possibile filtrare i consigli alla ricerca di quello più consono alle proprie esigenze.  Il filtro può essere applicato in base alla sottoscrizione, al tipo di risorsa o allo stato di consiglio.  

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi aprire [Advisor](https://aka.ms/azureadvisordashboard).

1. Usare i menu a discesa nel dashboard di Advisor per filtrare in base alla sottoscrizione, al tipo di risorsa o allo stato di consiglio.

    ![Criteri dei filtri di ricerca di Advisor](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-recommendations"></a>Posporre o ignorare consigli

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi aprire [Advisor](https://aka.ms/azureadvisordashboard).

1. Passare al consiglio che si vuole posporre o ignorare.

1. Fare clic sul consiglio.

1. Fare clic su **Posponi**. 

1. Specificare fino a quando posporre il consiglio oppure selezionare **Mai** per ignorarlo.

## <a name="exclude-subscriptions-or-resource-groups"></a>Escludere sottoscrizioni o gruppi di risorse

È possibile che si disponga di gruppi di risorse, come le risorse di test, e di sottoscrizioni per i quali non si vuole ricevere i consigli di Advisor.  Si può configurare Advisor in modo che generi consigli solo per sottoscrizioni e gruppi di risorse specifici.

> [!NOTE]
> Per includere o escludere una sottoscrizione o un gruppo di risorse da Advisor, è necessario essere un proprietario della sottoscrizione.  Se non si dispone delle autorizzazioni necessarie per una sottoscrizione o un gruppo di risorse, l'opzione per includere o escludere la sottoscrizione o il gruppo di risorse è disabilitata nell'interfaccia utente.

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi aprire [Advisor](https://aka.ms/azureadvisordashboard).

1. Fare clic su **Configura** nella barra delle azioni.

1. Deselezionare tutte le sottoscrizioni o i gruppi di risorse per i quali non si intende ricevere i consigli di Advisor.

    ![Esempio di configurazione di risorse in Advisor](./media/advisor-get-started/advisor-configure-resources.png)

1. Fare clic sul pulsante **Applica**.

## <a name="configure-low-usage-vm-recommendation"></a>Configurare un consiglio sulle macchine virtuali a utilizzo ridotto

Questa procedura configura la regola di utilizzo medio della CPU per il consiglio sulle macchine virtuali a utilizzo ridotto.

Advisor monitora l'utilizzo della macchina virtuale per 7 giorni e quindi identifica le macchine virtuali con utilizzo ridotto. Le macchine virtuali sono considerate con scarso utilizzo se l'utilizzo della CPU è pari al 5% o inferiore e l'utilizzo della rete è inferiore al 2% o se il carico di lavoro corrente può essere adattato da una dimensione di macchina virtuale inferiore.

Per un'individuazione più mirata delle macchine virtuali a utilizzo ridotto, è possibile perfezionare la regola di utilizzo medio della CPU in base alla sottoscrizione.  La regola di utilizzo della CPU può essere impostata su 5%, 10%, 15% o 20%.

> [!NOTE]
> Per modificare questa regola per identificare le macchine virtuali a utilizzo ridotto, è necessario essere un *proprietario* della sottoscrizione.  Se non si dispone delle autorizzazioni necessarie per una sottoscrizione o un gruppo di risorse, l'opzione per includere o escludere la sottoscrizione o il gruppo di risorse è disabilitata nell'interfaccia utente. 

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi aprire [Advisor](https://aka.ms/azureadvisordashboard).

1. Fare clic su **Configura** nella barra delle azioni.

1. Fare clic sulla scheda **Regole**.

1. Selezionare le sottoscrizioni per cui vuole modificare la regola di utilizzo medio della CPU e quindi fare clic su **Modifica**.

1. Selezionare il valore di utilizzo medio della CPU desiderato e fare clic su **Applica**.

1. Fare clic su **Aggiorna raccomandazioni** per aggiornare i consigli esistenti all'uso della nuova regola di utilizzo medio della CPU. 

   ![Esempio di configurazione di una regola di consiglio in Advisor](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-recommendations"></a>Scaricare consigli

Advisor consente di scaricare un riepilogo dei consigli  come file con estensione PDF o con estensione CSV.  Il riepilogo scaricato può essere facilmente condiviso con i colleghi o usato per effettuare analisi personalizzate in base ai dati dei consigli.

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi aprire [Advisor](https://aka.ms/azureadvisordashboard).

1. Nella barra delle azioni fare clic su **Scarica come CSV** o su **Scarica come PDF**.

L'opzione di download rispetta tutti i filtri che sono stati applicati al dashboard di Advisor.  Se si seleziona l'opzione di download mentre si visualizza una categoria di consigli specifica o un consiglio specifico, il riepilogo scaricato includerà solo le informazioni relative alla categoria o al consiglio specifico. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Advisor, vedere:

- [Introduction to Azure Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
- [Consigli di Advisor sulla disponibilità elevata](advisor-high-availability-recommendations.md)
- [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza)
- [Consigli di Advisor sulle prestazioni](advisor-performance-recommendations.md)
- [Advisor Cost recommendations](advisor-performance-recommendations.md) (Consigli di Advisor sui costi)
