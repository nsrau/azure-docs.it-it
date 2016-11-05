---
title: Pubblicare il servizio Web di Machine Learning in Azure Marketplace | Microsoft Docs
description: Informazioni su come pubblicare il servizio Web di Azure Machine Learning in Azure Marketplace.
services: machine-learning
documentationcenter: ''
author: BharathS
manager: jhubbard
editor: cgronlun

ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2016
ms.author: bharaths

---
# Pubblicare il servizio Web di Azure Machine Learning in Azure Marketplace
Azure Marketplace consente di pubblicare servizi Web di Azure Machine Learning sotto forma di servizi a pagamento o gratuiti utilizzabili da clienti esterni. Questo articolo offre una panoramica del processo e fornisce i collegamenti alle linee guida per iniziare. In questo modo sarà possibile rendere disponibili i servizi Web ad altri sviluppatori che potranno usarli nelle proprie applicazioni.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Panoramica della procedura di pubblicazione
I passaggi seguenti consentono di pubblicare un servizio Web di Azure Machine Learning in Azure Marketplace:

1. Creare e pubblicare un servizio di richiesta-risposta di Machine Learning.
2. Distribuire il servizio in produzione e ottenere le informazioni sull'endpoint OData e sulla chiave API.
3. Usare l'URL del servizio Web pubblicato per la pubblicazione in [Azure Marketplace](https://publish.windowsazure.com/workspace/) (store online per dati).
4. Una volta inviata, l'offerta viene esaminata e deve essere approvata prima che i clienti possano iniziare ad acquistarla. La procedura di pubblicazione può richiedere alcuni giorni lavorativi.

## Procedura dettagliata
### Passaggio 1: Creare e pubblicare un servizio di richiesta-risposta di Machine Learning.
 Se non è già stato fatto, vedere questa [procedura dettagliata](machine-learning-walkthrough-5-publish-web-service.md).

### Passaggio 2: Distribuire il servizio in produzione e ottenere le informazioni sull'endpoint OData e sulla chiave API.
1. Nel [portale di Azure classico](http://manage.windowsazure.com) selezionare l'opzione **MACHINE LEARNING** nella barra di spostamento a sinistra e quindi selezionare l'area di lavoro.
2. Fare clic sulla scheda **WEB SERVICES** e selezionare il servizio Web che si vuole pubblicare nel Marketplace.
   
    ![Azure Marketplace][workspace]
3. Selezionare l'endpoint che dovrà essere utilizzato nel Marketplace. Se non sono ancora stati creati endpoint aggiuntivi, è possibile selezionare l'endpoint **Default**.
4. Dopo avere fatto clic sull'endpoint, si potrà visualizzare **API KEY**. Copiare la chiave, perché queste informazioni saranno necessarie in seguito nel passaggio 3.
   
    ![Azure Marketplace][apikey]
5. Fare clic sul metodo **REQUEST/RESPONSE**. Al momento, non è supportata la pubblicazione di servizi di esecuzione batch nel Marketplace. Verrà visualizzata la pagina della Guida per l'API relativa al metodo Request/Response.
6. Copiare l'indirizzo disponibile in **OData Endpoint Address**. Queste informazioni saranno necessarie in seguito nel passaggio 3.
   
    ![Azure Marketplace][odata]

distribuire il servizio in produzione.

### Passaggio 3: Usare l'URL del servizio Web pubblicato per la pubblicazione in Azure Marketplace (store online per dati)
1. Passare ad [Azure Marketplace (store online per dati)](http://datamarket.azure.com/home)
2. Fare clic sul collegamento **Pubblica** nella parte superiore della pagina. Verrà visualizzato il [portale di pubblicazione di Microsoft Azure](https://publish.windowsazure.com)
3. Fare clic sulla sezione **editori** per registrarsi come editore.
4. Quando si crea una nuova offerta, selezionare **Servizi dati**, quindi fare clic su **Crea un nuovo servizio dati**.
   
   ![Azure Marketplace][image1]
   
   <br />
5. In **Piani** fornire le informazioni relative all'offerta, incluso un piano tariffario. Decidere se offrire un servizio gratuito o a pagamento. Nel caso di un servizio a pagamento, fornire i dati per il pagamento, come le coordinate bancarie e le informazioni fiscali.
6. In **Marketing** fornire le informazioni relative all'offerta, ad esempio il titolo e una descrizione dell'offerta.
7. In **Prezzi** è possibile impostare il prezzo dei piani per paesi specifici o consentire al sistema di applicare un prezzo automatico all'offerta.
8. Nella scheda **Servizio dati** fare clic su **Servizio Web** come **Origine dati**.
   
    ![Azure Marketplace][image2]
9. Ottenere l'URL del servizio Web e la chiave API dal portale di Azure classico, come descritto nel passaggio 2 precedente.
10. Nella finestra di dialogo di configurazione del servizio dati del Marketplace incollare l'indirizzo dell'endpoint OData nella casella **URL servizio**.
11. Per **Autenticazione** scegliere **Intestazione** come **Schema di autenticazione**.
    
    * Immettere "Autorizzazione" per **Nome intestazione**.
    * Per **Valore intestazione** immettere "Portante" (senza virgolette), fare clic sulla **BARRA SPAZIATRICE** e quindi incollare la chiave API.
    * Selezionare la casella di controllo **Questo servizio è OData**.
    * Fare clic su **Test connessione** per testare la connessione.
12. In **Categorie** assicurarsi che sia selezionata l'opzione **Machine Learning**.
13. Dopo avere completato l'immissione di tutti i metadati dell'offerta, fare clic su **Pubblica** e quindi su **Push a gestione temporanea**. A questo punto si verrà informati della presenza di eventuali problemi rimanenti da correggere.
14. Dopo avere verificato il completamento di tutti i problemi in sospeso, fare clic su **Approvazione richiesta per push in produzione**. La procedura di pubblicazione può richiedere alcuni giorni lavorativi.

[image1]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
[workspace]: ./media/machine-learning-publish-web-service-to-azure-marketplace/selectworkspace.png
[apikey]: ./media/machine-learning-publish-web-service-to-azure-marketplace/apikey.png
[odata]: ./media/machine-learning-publish-web-service-to-azure-marketplace/odata.png


<!---HONumber=AcomDC_0914_2016-->