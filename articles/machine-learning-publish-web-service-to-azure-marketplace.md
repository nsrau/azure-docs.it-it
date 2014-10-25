<properties title="Publishing Azure ML Web Services to the Azure Marketplace" pageTitle="Publishing Azure ML Web Services to the Azure Marketplace | Azure" description="Publishing Azure ML Web Services to the Azure Marketplace" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Pubblicare servizi Web di Azure ML in Azure Marketplace

Contenuto del documento

-   [Introduzione][Introduzione]
-   [Panoramica della procedura di pubblicazione][Panoramica della procedura di pubblicazione]
-   [Linee guida per la pubblicazione in Azure Marketplace][Linee guida per la pubblicazione in Azure Marketplace]
-   [Opzioni specifiche di Machine Learning][Opzioni specifiche di Machine Learning]

<!--Anchors-->

## Introduzione

Azure Marketplace consente di pubblicare servizi Web di Azure Machine Learning sotto forma di servizi a pagamento o gratuiti utilizzabili da clienti esterni. In questo documento viene offerta una panoramica di questa procedura e vengono forniti i collegamenti alle linee guida per iniziare. In questo modo sarà possibile rendere disponibili i servizi Web ad altri sviluppatori che potranno usarli nelle proprie applicazioni.

## Panoramica della procedura di pubblicazione

I passaggi seguenti consentono di pubblicare un servizio Web Azure ML in Azure Marketplace:

1.  Creare e pubblicare un servizio Web di richiesta/risposta o di esecuzione batch di Azure ML.
2.  Distribuire il servizio in produzione dal portale di gestione di Azure.
3.  Usare l'URL del servizio Web pubblicato per la pubblicazione in Azure Marketplace.
4.  Panoramica della procedura di pubblicazione: <http://msdn.microsoft.com/en-us/library/azure/hh580725.aspx>
5.  Una volta inviata, l'offerta viene esaminata e deve essere approvata prima che i clienti possano iniziare ad acquistarla. La procedura di pubblicazione può richiedere alcuni giorni lavorativi. Microsoft sta cercando di ridurre il più possibile i tempi di attesa e comunicherà a breve gli eventuali aggiornamenti in merito.

## Linee guida per la pubblicazione in Azure Marketplace

1.  È necessario effettuare la registrazione come editore. Per informazioni dettagliate, vedere: <http://msdn.microsoft.com/en-us/library/azure/hh563872.aspx>
2.  È necessario fornire informazioni sull'offerta, incluso un piano tariffario. Decidere se offrire un servizio gratuito o a pagamento. Per informazioni dettagliate, vedere: <http://msdn.microsoft.com/en-us/library/azure/hh563873.aspx>
3.  Nel caso di servizi a pagamento, è necessario fornire i dati per il pagamento, come le coordinate bancarie e le informazioni fiscali. Per informazioni dettagliate, vedere: <http://msdn.microsoft.com/en-us/library/azure/hh563873.aspx>

## Opzioni specifiche di Machine Learning

1.  Quando si crea una nuova offerta, selezionare **Servizi dati**, quindi fare clic su **Crea un nuovo servizio dati**.

    ![Azure Marketplace][Azure Marketplace]

2.  Nella scheda **Servizio dati** fare clic su **Servizio Web** come origine dati.

    ![Azure Marketplace][1]

3.  In **URL servizio** specificare l'URL del servizio Web:

    -   Nel menu di sinistra di Azure ML Studio fare clic su **SERVIZI WEB**.
    -   Fare clic sul servizio Web da pubblicare in Marketplace.
    -   Nella pagina **Dashboard** fare clic su **Pagina della Guida per l'API** per il servizio di richiesta/risposta.
    -   Copiare l'indirizzo dell'endpoint OData.

4.  Per l'autenticazione scegliere **Intestazione** come **Schema di autenticazione**.

    -   Immettere "Authorization" in **Nome intestazione**.
    -   Per **Valore intestazione**:

        -   Nella pagina **Dashboard** del servizio Web in ML Studio copiare il valore di **Chiave API**.
        -   Nel campo **Valore intestazione** immettere "Bearer" (senza le virgolette), seguito da uno spazio, quindi incollare la chiave API.
    -   Selezionare che la casella di controllo **Questo servizio è OData**.

5.  Categorie:

    -   Assicurarsi che l'opzione **Machine Learning** sia selezionata.

  [Introduzione]: #introduction
  [Panoramica della procedura di pubblicazione]: #overview-of-the-publishing-process
  [Linee guida per la pubblicazione in Azure Marketplace]: #guidelines-for-publishing-to-azure-marketplace
  [Opzioni specifiche di Machine Learning]: #machine-learning-specific-options
  [Azure Marketplace]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
  [1]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
