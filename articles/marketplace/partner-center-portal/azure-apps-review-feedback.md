---
title: Rivedere il feedback per offerte di app di Azure - Marketplace commerciale Microsoft
description: Procedura di gestione del feedback per l'offerta di app di Azure dal team di revisione Microsoft Azure Marketplace. È possibile accedere ai feedback in Azure DevOps con le credenziali del Centro per i partner.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 351a9742e9d839a8e217fdea35cad922acc3339f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095673"
---
# <a name="handling-review-feedback-for-azure-application-offers"></a>Gestione dei feedback di revisione per offerte app di Azure

Questo articolo illustra come accedere ai feedback del team di revisione di Microsoft Azure Marketplace in [Azure DevOps](https://azure.microsoft.com/services/devops/). Se vengono rilevati problemi critici nell'offerta di applicazione Azure durante il passaggio di **revisione Microsoft** , è possibile accedere a questo sistema per visualizzare informazioni dettagliate su questi problemi (feedback della revisione). Dopo avere risolto tutti i problemi, è necessario inviare di nuovo l'offerta per continuare a pubblicarla in Azure Marketplace. Il diagramma seguente illustra come questo processo di commenti e suggerimenti è correlato a quello di pubblicazione.

![Rivedere il processo di feedback](./media/review-feedback-process.png)

I problemi di revisione vengono in genere chiamati richieste pull. Ogni richiesta pull è collegata a un elemento di Azure DevOps, che contiene i dettagli del problema. Nell'immagine seguente viene visualizzato un esempio dell'esperienza del Centro per i partner se vengono rilevati problemi durante le revisioni. 

![Stato di pubblicazione](./media/publishing-status.png)

La richiesta pull che contiene dettagli specifici sull'invio verrà indicata nel collegamento "Visualizza report di certificazione". In caso di situazioni complesse, i team di revisione e supporto possono inviare un messaggio di posta elettronica.

## <a name="azure-devops-access"></a>Accesso ad Azure DevOps

Tutti gli utenti con accesso al ruolo "sviluppatore" nel Centro per i partner avranno accesso per visualizzare gli elementi della richiesta pull a cui si fa riferimento nel feedback di revisione.

## <a name="reviewing-the-pull-request"></a>Revisione della richiesta pull

Usare la procedura seguente per esaminare i problemi documentati nella richiesta pull.

1. Nelle sezioni **Verifica Microsoft** del modulo Passaggi per la pubblicazione, fare clic su un collegamento a una richiesta pull per avviare il browser e passare alla pagina (home page) **Panoramica** per la richiesta pull. L'immagine seguente illustra una home page di esempio relativa a un problema critico per l'offerta di app di esempio Contoso. Questa pagina contiene utili informazioni di riepilogo relative ai problemi riscontrati nella revisione dell'app Azure.

    [![Home page della richiesta pull](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Fare clic sull'immagine per ingrandirla.*

1. (Facoltativo) Sul lato destro della finestra, nella sezione **Criteri** , selezionare il messaggio relativo al problema (in questo esempio: **Policy Validation failed** (Errore nella convalida dei criteri)) per esaminare i dettagli specifici del problema, inclusi i file di log associati. Gli errori vengono in genere visualizzati nella parte inferiore dei file di log.

1. Nel menu sul lato sinistro della home page selezionare **Files** (File) per visualizzare i file dell'elenco che includono gli asset tecnici per l'offerta. I revisori Microsoft dovrebbero aver aggiunto commenti che descrivono i problemi critici individuati. Nell'esempio seguente sono stati individuati due problemi.

    [![Screenshot che evidenzia i file e i due problemi individuati.](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Fare clic sull'immagine per ingrandirla.*

1. Selezionare ogni nodo di commento nell'albero a sinistra per passare al commento nel contesto del codice circostante. Correggere il codice sorgente nel progetto del team per risolvere il problema descritto nel commento.

>[!Note]
>Non è possibile modificare gli asset tecnici dell'offerta nell'ambiente Azure DevOps del team di revisione. Per gli editori, questo ambiente è di sola lettura per il codice sorgente presente. Tuttavia, è possibile lasciare risposte ai commenti per il team di revisione Microsoft.

   Nell'esempio seguente l'editore ha rivisto e corretto il primo problema e ha risposto al commento relativo.

   ![Prima correzione e risposta al commento](./media/first-comment-reply.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver corretto i problemi critici documentati nelle richieste di pull della revisione, è necessario [ripubblicare l'offerta di app Azure](./create-new-azure-apps-offer.md#publish).
