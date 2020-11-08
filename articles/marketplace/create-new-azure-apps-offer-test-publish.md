---
title: Come testare e pubblicare un'offerta di applicazione Azure
description: Usa il centro per i partner per inviare l'offerta di applicazione Azure alla versione di anteprima, visualizzare un'anteprima dell'offerta, testare e quindi pubblicarla nel Marketplace commerciale Microsoft.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: b54e965188be51ec54110bb85d8cda8f01256836
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370228"
---
# <a name="how-to-test-and-publish-an-azure-application-offer"></a>Come testare e pubblicare un'offerta di applicazione Azure

Questo articolo illustra come usare il centro per i partner per inviare l'offerta applicazione Azure per la pubblicazione, visualizzare l'anteprima dell'offerta, testarla e quindi pubblicarla nel Marketplace commerciale. È necessario avere già creato un'offerta che si desidera pubblicare.

## <a name="submit-your-offer-for-publishing"></a>Invia l'offerta per la pubblicazione

1. Accedere al dashboard del Marketplace commerciale nel centro per i [partner](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).
1. Nella pagina **Panoramica** selezionare l'offerta che si desidera pubblicare.
1. Nell'angolo in alto a destra del portale selezionare **revisione e pubblicazione**.
1. Verificare che nella colonna **stato** per ogni pagina sia stato **completato**. I tre possibili stati sono i seguenti:
    - **Non avviato** : la pagina è incompleta.
    - **Incompleto** : nella pagina mancano le informazioni necessarie o sono presenti errori che devono essere corretti. È necessario tornare alla pagina e aggiornarla.
    - **Completato** : la pagina è stata completata. Sono stati forniti tutti i dati necessari e non sono presenti errori.
1. Se una delle pagine presenta uno stato diverso da **completo** , selezionare il nome della pagina, correggere il problema, salvare la pagina, quindi selezionare **Verifica e pubblica** di nuovo per tornare a questa pagina.
1. Al termine di tutte le pagine, nella casella **Note per la certificazione** , fornire istruzioni di test al team di certificazione per assicurarsi che l'app venga testata correttamente. Fornire eventuali note supplementari utili per comprendere l'app.
1. Per avviare il processo di pubblicazione per l'offerta, selezionare **pubblica**. Viene visualizzata la pagina **Panoramica dell'offerta** che mostra lo **stato di pubblicazione** dell'offerta.

Lo stato di pubblicazione dell'offerta cambierà durante il processo di pubblicazione. Per informazioni dettagliate su questo processo, vedere la pagina relativa alla [procedura di convalida e pubblicazione](review-publish-offer.md#validation-and-publishing-steps).

## <a name="preview-and-test-your-offer"></a>Anteprima e test dell'offerta

Quando l'offerta è pronta per la disconnessione, ti invieremo un messaggio di posta elettronica per richiedere la verifica e l'approvazione dell'anteprima dell'offerta. È anche possibile aggiornare la pagina **Panoramica dell'offerta** nel browser per verificare se l'offerta ha raggiunto la fase di approvazione dell'editore. In caso contrario, il pulsante **Vai in diretta** e il collegamento anteprima saranno disponibili. Se si sceglie di vendere l'offerta tramite Microsoft, tutti gli utenti che sono stati aggiunti ai destinatari dell'anteprima possono testare l'acquisizione e la distribuzione dell'offerta per garantire che soddisfi i requisiti durante questa fase.

Lo screenshot seguente mostra la pagina di **Panoramica dell'offerta** per un'offerta, con due collegamenti di anteprima nel pulsante **Go Live** . I passaggi di convalida visualizzati in questa pagina variano a seconda delle selezioni effettuate durante la creazione dell'offerta.

[![Viene illustrata la pagina di panoramica dell'offerta per un'offerta nel centro per i partner. Vengono visualizzati il pulsante Go Live e i collegamenti di anteprima.](media/create-new-azure-app-offer/azure-app-publish-status.png)](media/create-new-azure-app-offer/azure-app-publish-status.png#lightbox)

Per visualizzare in anteprima l'offerta, seguire questa procedura.

1. Nella pagina **Panoramica dell'offerta** selezionare un collegamento anteprima nel pulsante **Vai in diretta** . 

1. Per convalidare il flusso di acquisto e configurazione end-to-end, acquista l'offerta mentre è in anteprima. Per prima cosa, inviare una notifica a Microsoft con un [ticket di supporto](https://aka.ms/marketplacesupport) per assicurarsi che non venga elaborato alcun addebito.

1. Se l'applicazione Azure supporta la [fatturazione a consumo usando il servizio di misurazione del Marketplace commerciale](./partner-center-portal/azure-app-metered-billing.md), rivedere e seguire le procedure consigliate di test descritte in dettaglio nelle API per la [fatturazione a consumo del Marketplace](./partner-center-portal/marketplace-metering-service-apis.md#development-and-testing-best-practices).

1. Se è necessario apportare modifiche dopo la visualizzazione in anteprima e il test dell'offerta, è possibile modificare e inviare di nuovo per pubblicare una nuova anteprima. Per ulteriori informazioni, vedere [aggiornare un'offerta esistente nel Marketplace commerciale](./partner-center-portal/update-existing-offer.md).

## <a name="publish-your-offer-live"></a>Pubblica l'offerta in tempo reale

Dopo aver completato tutti i test nell'anteprima, selezionare **Go Live** per pubblicare l'offerta in diretta nel Marketplace commerciale.

   > [!TIP]
   > Se l'offerta è già presente nel Marketplace commerciale, gli eventuali aggiornamenti non verranno attivati fino a quando non si seleziona **Go Live**.

Ora che si è scelto di rendere disponibile l'offerta nel Marketplace commerciale, viene eseguita una serie di controlli di convalida finali per assicurarsi che l'offerta Live sia configurata esattamente come la versione di anteprima dell'offerta. Per informazioni dettagliate su questi controlli di convalida, vedere [fase di pubblicazione](review-publish-offer.md#publish-phase).

Al termine di questi controlli di convalida, l'offerta sarà disponibile nel Marketplace.

### <a name="errors-and-review-feedback"></a>Errori e feedback della revisione

Il passaggio di **convalida manuale** del processo di pubblicazione rappresenta un'ampia revisione dell'offerta e le risorse tecniche associate (in particolare il modello di Azure Resource Manager) vengono in genere presentate come collegamenti richiesta pull (PR). Per una spiegazione su come visualizzare e rispondere a queste richieste pull, vedere [Gestione del feedback di revisione](partner-center-portal/azure-apps-review-feedback.md).

Se si verificano errori in uno o più passaggi di pubblicazione, correggerli prima di ripubblicare l'offerta.

## <a name="next-step"></a>Passaggio successivo

- [Accedere ai report analitici per il Marketplace commerciale nel centro per i partner](partner-center-portal/analytics.md)
- Scopri [come commercializzare il tuo applicazione Azure offerta](create-new-azure-apps-offer-marketing.md) attraverso la co-selling con Microsoft e la rivendita tramite i programmi CSP.
