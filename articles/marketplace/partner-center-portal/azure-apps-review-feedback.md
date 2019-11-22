---
title: Gestione del feedback di revisione per l'offerta app di Azure nel Marketplace commerciale
description: Come gestire il feedback della revisione per le app di Azure per l'inserzione o la vendita in Azure Marketplace, AppSource o tramite il programma Cloud Solution Provider (CSP) usando il portale di Marketplace commerciale nel centro per i partner Microsoft.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 7406a6e35929e3df5d342affbcb4b740efa0cab8
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74281220"
---
# <a name="handling-review-feedback"></a>Gestione del feedback della revisione

Questo articolo illustra come accedere all'ambiente Azure DevOps usato dal team di revisione di Microsoft Azure Marketplace. Se vengono rilevati problemi critici nell'offerta di applicazione Azure durante il passaggio di **revisione Microsoft**, è possibile accedere a questo sistema per visualizzare informazioni dettagliate su questi problemi (feedback della revisione). Dopo aver risolto tutti i problemi, è necessario inviare di nuovo l'offerta per continuare a pubblicarla in Azure Marketplace. Il diagramma seguente illustra come questo processo di commenti e suggerimenti è correlato a quello di pubblicazione.

![Esaminare il processo di feedback](./media/review-feedback-process.png)

I problemi di revisione vengono in genere chiamati richieste pull. Ogni richiesta pull è collegata a un elemento [Azure DevOps](https://azure.microsoft.com/services/devops/) (in precedenza detto Visual Studio Team Services o VSTS) online, che contiene i dettagli del problema. Nell'immagine seguente viene visualizzato un esempio dell'esperienza del centro per i partner se vengono rilevati problemi durante le revisioni. 

![Stato di pubblicazione](./media/publishing-status.png)

La richiesta pull che contiene dettagli specifici sull'invio verrà indicata nel collegamento "Visualizza report certificazione". In caso di situazioni complesse, i team di revisione e supporto possono inviare un messaggio di posta elettronica.

## <a name="azure-devops-access"></a>Accesso ad Azure DevOps

Tutti gli utenti con accesso al ruolo "sviluppatore" nel centro per i partner avranno accesso per visualizzare gli elementi della richiesta pull a cui si fa riferimento nel feedback di revisione.

<!---
To view the PR items referenced in review feedback, publishers must first be granted proper authorization. Otherwise, new publishers receive a 401 - Not Authorized response page when trying to view PRs. To request access to this Azure DevOps repository, perform the following steps:

1. Collect the following information:
    * Your publisher name and ID
    * Offer type (Azure app), offer name, and SKU ID
    * The pull request link, for example: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`. This URL can be retrieved from the notification message or the address of the 401 response page.
    * The email address(es) of the individuals from your publishing organization that you want access granted to. This list should include the owner address(es) you provided when registering as a publisher on the Cloud Partner Portal (or Partner Center??).
1. Create a support incident. In the title bar of the Cloud Partner Portal (??), select the **Help** button, then choose **Support** from the menu. Your default web browser should launch and navigate to the Microsoft new support incident page. (You may have to sign in first.)
1. Specify the **Problem type** as **marketplace onboarding** and **Category** as **Access problem**, then select **Start request**.

    ![Create an incident](./media/create-an-incident.png)

1. In **Step 1 of 2** page, supply your contact information and select **Continue**.
1. In **Step 2 of 2** page, specify an incident title (for example Request Azure DevOps access) and supply the information you collected in the first step (above). Read and accept the agreement, then select **Submit**.

If the incident creation was successful, a confirmation page is displayed. Save the confirmation information on this page for your reference. The Microsoft Support Team should reply to your access request within a few business days. --->

## <a name="reviewing-the-pull-request"></a>Revisione della richiesta pull

Usare la procedura seguente per esaminare i problemi documentati nella richiesta pull.

1. Nel modulo **Microsoft Review** of Publishing Steps fare clic su un collegamento PR per avviare il browser e passare alla pagina **Overview** (Home) per questa richiesta pull. Nell'immagine seguente viene illustrato un esempio del problema critico home page per l'offerta di app di esempio contoso. Questa pagina contiene utili informazioni di riepilogo relative ai problemi riscontrati nella revisione dell'app Azure.

    [![richiesta Pull home page](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Fare clic sull'immagine per ingrandirla.*

1. Opzionale Sul lato destro della finestra, nella sezione **criteri**, fare clic sul messaggio di problema (in questo esempio: convalida dei **criteri non riuscita**) per esaminare i dettagli di basso livello del problema, inclusi i file di log associati. Gli errori vengono in genere visualizzati nella parte inferiore dei file di log.
1. Nel menu sul lato sinistro della home page selezionare **Files** (File) per visualizzare i file dell'elenco che includono gli asset tecnici per l'offerta. I revisori Microsoft dovrebbero aver aggiunto commenti che descrivono i problemi critici individuati. Nell'esempio seguente sono stati individuati due problemi.

    [![richiesta Pull home page](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Fare clic sull'immagine per ingrandirla.*

1. Fare clic su ogni nodo di commento nell'albero a sinistra per passare al commento nel contesto del codice circostante. Correggere il codice sorgente nel progetto del team per risolvere il problema descritto nel commento.

>[!Note]
>Non è possibile modificare gli asset tecnici dell'offerta nell'ambiente Azure DevOps del team di revisione. Per gli editori, questo ambiente è di sola lettura per il codice sorgente presente. Tuttavia, è possibile lasciare risposte ai commenti per il team di revisione Microsoft.

   Nell'esempio seguente l'editore ha rivisto e corretto il primo problema e ha risposto al commento relativo.

   ![Prima correzione e risposta al commento](./media/first-comment-reply.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver corretto i problemi critici documentati nelle richieste di pull della revisione, è necessario [ripubblicare l'offerta di app Azure](./create-new-azure-apps-offer.md#publish).
