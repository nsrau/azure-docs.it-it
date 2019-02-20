---
title: Gestione del feedback della revisione dell'applicazione Azure - Azure Marketplace | Microsoft Docs
description: Viene illustrato come usare Azure DevOps per gestire il feedback della revisione per le offerte di applicazioni Azure per Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: pbutlerm
ms.openlocfilehash: 98ab9b25c59af194a1a81c600c373b2180272e10
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56106955"
---
# <a name="handling-review-feedback"></a>Gestione del feedback della revisione

Questo articolo illustra come accedere all'ambiente Azure DevOps usato dal team di revisione di Microsoft Azure Marketplace.  Se vengono rilevati problemi critici nell'offerta di applicazione Azure durante il passaggio di **revisione Microsoft**, è possibile accedere a questo sistema per visualizzare informazioni dettagliate su questi problemi (feedback della revisione).  Dopo aver risolto tutti i problemi, è necessario inviare di nuovo l'offerta per continuare a pubblicarla in Azure Marketplace.  Il diagramma seguente illustra come questo processo di commenti e suggerimenti è correlato a quello di pubblicazione.

![Procedura di pubblicazione con i commenti e suggerimenti di VSTS](./media/pub-flow-vsts-access.png)

I problemi di revisione vengono in genere chiamati richieste pull.  Ogni richiesta pull è collegata a un elemento [Azure DevOps](https://azure.microsoft.com/services/devops/) (in precedenza detto Visual Studio Team Services o VSTS) online, che contiene i dettagli del problema.  L'immagine seguente visualizza un esempio di riferimento a una richiesta pull di revisione.  In caso di situazioni complesse, i team di revisione e supporto possono inviare un messaggio di posta elettronica. 

![Scheda Stato con il feedback della revisione](./media/status-tab-ms-review.png)


## <a name="vsts-access"></a>Accesso VSTS

Per visualizzare gli elementi delle richieste pull indicati nel feedback della revisione, gli editori devono avere l'autorizzazione appropriata.  In caso contrario, i nuovi editori ricevono una pagina di risposta `401 - Not Authorized` quando cercano di visualizzare le richieste pull.  Per richiedere l'accesso a questo repository Azure DevOps, seguire questa procedura:

1. Raccogliere le informazioni seguenti:
    - Il nome dell'autore e l'ID
    - Tipo di offerta (app Azure), nome dell'offerta e ID SKU
    - Collegamento della richiesta pull, ad esempio: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>` Questo URL può essere recuperato dal messaggio di notifica o dall'indirizzo della pagina di risposta 401.
    - Gli indirizzi di posta elettronica delle persone dell'organizzazione di pubblicazione a cui si vuole concedere l'accesso.  L'elenco deve includere gli indirizzi dei proprietari specificati durante la registrazione come editore nel portale Cloud Partner.
2. Creare una richiesta di assistenza.  Nella barra del titolo del portale Cloud Partner selezionare il pulsante **?**, quindi scegliere **Supporto** dal menu.  Il Web browser predefinito verrà avviato e passerà alla pagina Microsoft della nuova richiesta di assistenza.  Potrebbe essere necessario eseguire prima l'accesso.
3. In **Tipo di problema** specificare **Onboarding del Marketplace** e in **Categoria** specificare **Problema di accesso**, quindi selezionare **Avvio richiesta**.

    ![Categoria del ticket di supporto](./media/support-incident1.png)

4. Nella pagina **Passaggio 1 di 2** specificare le informazioni sul contatto e selezionare **Continua**.
5. Nella pagina **Passaggio 2 di 2** specificare un titolo dell'evento imprevisto (ad esempio, `Request VSTS access`) e fornire le informazioni raccolte nel primo passaggio (sopra).  Leggere e accettare il contratto, quindi selezionare **Invia**.

Se la creazione dell'evento imprevisto ha esito positivo, viene visualizzata una pagina di conferma.  Salvare le informazioni di conferma nella pagina per riferimento.  Il team del supporto tecnico Microsoft risponderà alla richiesta di accesso entro alcuni giorni lavorativi.


## <a name="reviewing-the-pull-request"></a>Revisione della richiesta pull 

Usare la procedura seguente per esaminare i problemi documentati nella richiesta pull.

1. Nella sezione **Microsoft review** (Revisione Microsoft) del modulo **Publishing steps** (Passaggi per la pubblicazione) fare clic su un collegamento a una richiesta pull per avviare il browser e passare alla home page **Overview** (Panoramica) per la richiesta pull.  L'immagine seguente illustra una home page di esempio relativa a un problema critico per l'offerta di app di esempio Contoso.  Questa pagina contiene utili informazioni di riepilogo relative ai problemi riscontrati nella revisione dell'app Azure.  

    [ ![Home page della richiesta pull](./media/pr-home-page-thumb.png) ](./media/pr-home-page.png)
    <br/> *Fare clic sull'immagine per ingrandirla.*
    
2. (Facoltativo) Sul lato destro della finestra, nella sezione **Policies** (Criteri), fare clic sul messaggio relativo al problema (in questo esempio: **Policy Validation failed** (Errore nella convalida dei criteri)) per esaminare i dettagli specifici del problema, inclusi i file di log associati.  Gli errori vengono in genere visualizzati nella parte inferiore dei file di log.

3. Nel menu sul lato sinistro della home page selezionare **Files** (File) per visualizzare i file dell'elenco che includono gli asset tecnici per l'offerta.  I revisori Microsoft dovrebbero aver aggiunto commenti che descrivono i problemi critici individuati.  Nell'esempio seguente sono stati individuati due problemi. 

    [ ![Home page della richiesta pull](./media/pr-files-page-thumb.png) ](./media/pr-files-page.png)
    <br/> *Fare clic sull'immagine per ingrandirla.*

4. Fare clic su ogni nodo di commento nell'albero a sinistra per passare al commento nel contesto del codice circostante.  Correggere il codice sorgente nel progetto del team per risolvere il problema descritto nel commento.

> [!Note]
> Non è possibile modificare gli asset tecnici dell'offerta nell'ambiente Azure DevOps del team di revisione.  Per gli editori, questo ambiente è di sola lettura per il codice sorgente presente.  Tuttavia, è possibile lasciare risposte ai commenti per il team di revisione Microsoft.

   Nell'esempio seguente l'editore ha rivisto e corretto il primo problema e ha risposto al commento relativo.

   ![Prima correzione e risposta al commento](./media/first-comment-reply.png)


## <a name="next-steps"></a>Passaggi successivi

Dopo aver corretto i problemi critici documentati nelle richieste di pull della revisione, è necessario [ripubblicare l'offerta di app Azure](./cpp-publish-offer.md).
