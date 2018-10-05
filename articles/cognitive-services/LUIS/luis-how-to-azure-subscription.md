---
title: Gestione della sottoscrizione endpoint nell'app LUIS
titleSuffix: Azure Cognitive Services
description: In questo articolo viene illustrato come creare una chiave di endpoint a consumo per consentire all'account LUIS di fornire traffico illimitato all'endpoint sulla base di un piano di pagamento.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: c7c6009d9fec14e8633a37e5384dd203f2f5fa60
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47040381"
---
# <a name="manage-azure-endpoint-subscription-keys"></a>Gestire la chiave di sottoscrizione a un endpoint di Azure

Solo in caso di test o come prototipo, usa il livello (F0) gratuito. Per i sistemi di produzione, usa un livello [a pagamento](https://aka.ms/luis-price-tier). 

> [!NOTE]
> Non usare la [chiave di creazione](luis-concept-keys.md#authoring-key) per le query di endpoint nell'ambiente di produzione.

<a name="create-luis-service"></a>
## <a name="create-luis-endpoint-key"></a>Creare la chiave di endpoint LUIS

1. Accedi a **[Microsoft Azure](https://ms.portal.azure.com/)**. 
2. Fai clic sul **+** verde, accedi al riquadro superiore sinistro e cerca "LUIS" nel marketplace, quindi fai clic su **Language Understanding** e segui la **Creazione dell'esperienza**  per generare un account di sottoscrizione a LUIS. 

    ![Ricerca di Azure](./media/luis-azure-subscription/azure-search.png) 

3. Configura la sottoscrizione con impostazioni quali nome dell'account, piani tariffari e così via. 

    ![Scelta di API di Azure](./media/luis-azure-subscription/azure-api-choice.png) 

4. Dopo aver creato il servizio LUIS, è possibile visualizzare le chiavi di accesso generate in **Gestione delle risorse -> Chiavi**.  

    ![Chiavi di Azure](./media/luis-azure-subscription/azure-keys.png)

    > [!Note] 
    > Accedi al sito Web [LUIS](luis-reference-regions.md) della tua area geografica e [aggiungi la nuova chiave endpoint LUIS](luis-how-to-manage-keys.md#assign-endpoint-key). Dovrai utilizzare il nome della sottoscrizione LUIS, ottenuto nel passaggio 3.

## <a name="change-luis-pricing-tier"></a>Modificare il piano tariffario LUIS

1.  In [Azure](https://portal.azure.com), trova la sottoscrizione LUIS. Fai clic sulla sottoscrizione LUIS.
    ![Trova la sottoscrizione LUIS](./media/luis-usage-tiers/find.png)
2.  Fai clic su **Piano tariffario** per visualizzare i piani tariffari disponibili. 
    ![Visualizza piano tariffario](./media/luis-usage-tiers/subscription.png)
3.  Scegli il piano tariffario e fai clic su **Seleziona** per salvare la modifica. 
    ![Modifica il piano tariffario LUIS](./media/luis-usage-tiers/plans.png)
4.  Una volta completata la modifica dei prezzi, una finestra popup verifica il nuovo piano tariffario. 
    ![Verifica il piano tariffario LUIS](./media/luis-usage-tiers/updated.png)
5. Ricorda di [assegnare questa chiave di endpoint](luis-how-to-manage-keys.md#assign-endpoint-key) nella pagina **Pubblica** e di usarla in tutte le query di endpoint. 

## <a name="exceed-pricing-tier-usage"></a>Superamento dell'utilizzo del piano tariffario
Ogni livello consente richieste di endpoint al proprio account LUIS a una specifica velocità. Se la velocità delle richieste è superiore a quella dell'account a consumo consentita al minuto o al mese, le richieste riportano un errore HTTP "429: ricevute troppe richieste."

Ogni livello consente richieste cumulative mensili. Se le richieste totali sono superiori rispetto alla velocità consentita, le richieste riportano un errore HTTP "403: accesso negato".  

## <a name="viewing-summary-usage"></a>Visualizzazione del riepilogo dell'utilizzo
È possibile visualizzare le informazioni sull'utilizzo di LUIS in Azure. La pagina **Panoramica** contiene informazioni di riepilogo recenti, incluse chiamate ed errori. Se effettui una richiesta LUIS di endpoint, quindi osservi la **pagina Panoramica**, attendi fino a cinque minuti perché compaia l'utilizzo.

![Visualizzazione del riepilogo dell'utilizzo](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>Personalizzazione di grafici relativi all'utilizzo
Metrica offre una visualizzazione più dettagliata dei dati.

![Metriche predefinite](./media/luis-usage-tiers/metrics-default.png)

È possibile configurare i grafici delle metriche per periodo di tempo e il tipo di metrica. 

![Metriche personalizzate](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>Avviso di superamento della soglia delle transazioni totali
Se desideri sapere quando è stata raggiunta una determinata soglia delle transazioni, ad esempio 10.000 transazioni, puoi creare un avviso. 

![Avvisi predefiniti](./media/luis-usage-tiers/alert-default.png)

Aggiungi un avviso di metrica per la metrica **Totale chiamate** riferito a un determinato periodo di tempo. Aggiungi gli indirizzi e-mail di tutti gli utenti che devono ricevere l'avviso. Aggiungi webhook per tutti i sistemi che devono ricevere l'avviso. È anche possibile eseguire un'app per la logica quando viene generato l'avviso. 

## <a name="next-steps"></a>Passaggi successivi

Impara a usare le [versioni](luis-how-to-manage-versions.md) per gestire le modifiche all'app LUIS.