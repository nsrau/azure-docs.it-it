---
title: Guida alla creazione di un servizio dati per il Marketplace | Documentazione Microsoft
description: Istruzioni dettagliate su come creare, certificare e distribuire un servizio dati per l&quot;acquisto in Azure Marketplace.
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 96194198-6991-43b4-918e-ee337e250339
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e0057a5e8ab3d2fa3c9f201ec88246db7a53cc4a


---
# <a name="data-service-publishing-guide-for-the-azure-marketplace"></a>Guida alla pubblicazione del servizio dati per Azure Marketplace
> [!IMPORTANT]
> **In questo momento non stiamo più caricando nuovi editori di servizi dati. I nuovi servizi dati non saranno approvati per l'elencazione.** Se si dispone di un'applicazione aziendale SaaS che si vuole pubblicare in AppSource, è possibile trovare altre informazioni [qui](https://appsource.microsoft.com/partners). Se si dispone di un'applicazione IaaS o di un servizio per gli sviluppatori che si vuole pubblicare in Azure Marketplace, è possibile trovare altre informazioni [qui](https://azure.microsoft.com/marketplace/programs/certified/).
> 
> 

Dopo aver completato il passaggio 1, [Creazione e registrazione dell'account](marketplace-publishing-accounts-creation-registration.md), sono state fornite indicazioni sui [requisiti generali non tecnici](marketplace-publishing-pre-requisites.md) e [tecnici](marketplace-publishing-data-service-creation-prerequisites.md) delle offerte di servizi dati in Azure Marketplace. Verranno ora illustrati i passaggi per la creazione di un'offerta di un servizio dati sul [portale di pubblicazione][link-pubportal] relativo ad Azure Marketplace.

## <a name="1----login-to-the-publishing-portal"></a>1.    Accedere al portale di pubblicazione.
Passare alla pagina [https://publish.windowsazure.com](https://publish.windowsazure.com.)

**Per il primo accesso al portale di pubblicazione, usare lo stesso account con cui è stato registrato il profilo venditore della società nel Centro per sviluppatori.**   (Successivamente è possibile aggiungere qualsiasi dipendente della società come coamministratore nel portale di pubblicazione).

Fare clic sul riquadro **Pubblica servizi dati** se si tratta del primo accesso al portale di pubblicazione.

## <a name="2----choose-data-services-in-the-navigation-menu-on-the-left-side"></a>2.    Selezionare **Servizi dati** nel menu di navigazione a sinistra.
  ![disegno](media/marketplace-publishing-data-service-creation/pubportal-main-nav.png)

## <a name="3----create-a-new-data-service"></a>3.    Creare un nuovo servizio dati
Immettere il titolo per l'offerta del nuovo servizio, fare clic su "+" a destra.

  ![disegno](media/marketplace-publishing-data-service-creation/step-3.png)

## <a name="4----review-the-sub-menu-under-the-newly-created-data-service-in-the-navigation-menu"></a>4.    Esaminare il sottomenu relativo al servizio dati appena creato nel menu di navigazione.
Fare clic sulla scheda **Procedura dettagliata** ed esaminare tutti i passaggi necessari per pubblicare correttamente il servizio dati in Azure Marketplace.

> [!TIP]
> È sempre possibile fare clic sui collegamenti nella pagina "Procedura dettagliata" o utilizzare schede nel sottomenu dell'offerta del servizio dati sul lato sinistro.
> 
> 

## <a name="5----create-a-new-plan"></a>5.    Creare un nuovo piano.
### <a name="offers-plans-transactions"></a>Offerte, piani, transazioni.
Ciascuna offerta può contenere più piani, tuttavia è necessario disporre di almeno un (1) piano. La sottoscrizione da parte degli utenti riguarda uno dei piani dell'offerta. Ciascun piano definisce il modo in cui gli utenti finali potranno utilizzare il servizio.

Al momento Azure Marketplace supporta solo il modello basato su transazioni di sottoscrizione mensile per i servizi dati, ad esempio l'utente finale pagherà un canone mensile in base al prezzo del piano specifico sottoscritto e potrà usufruire del numero di transazioni previsto dal piano su base mensile.

Di solito, ciascuna transazione viene definita come il numero di record restituiti dal servizio dati in base alla query inviata al servizio. Il valore predefinito è 100. Il numero di transazioni restituite per ciascuna query corrisponde al numero di record diviso per 100 e arrotondato per eccesso al numero intero più vicino.

Il monitoraggio (misurazione) del numero di transazioni utilizzate da ciascuna query è responsabilità del livello di servizio di Azure Marketplace.

> [!IMPORTANT]
> Gli utenti finali che hanno raggiunto il limite delle transazioni durante il mese non potranno continuare a usare il servizio fino alla fine del relativo ciclo di sottoscrizione mensile.
> 
> Facoltativamente, il piano o uno dei piani possono comprendere un numero illimitato di transazioni.
> 
> 

### <a name="create-a-plan"></a>Creare un piano.
1. Fare clic su **"+"** accanto a "Aggiungi un nuovo piano".
2. Selezionare una delle opzioni: uso **Senza limiti** o **Limitato** per questo piano.  Se si seleziona Limitato, indicare il numero di transazioni mensili consentito dal piano.
   
    ![disegno](media/marketplace-publishing-data-service-creation/step-5.1.png)  
   
    Il portale di pubblicazione suggerirà anche un "identificatore del piano", utilizzato per comunicare agli utenti finali il nome del piano nell'interfaccia utente e utilizzato dal servizio del Marketplace per identificarlo. Facoltativamente, è possibile modificare tale identificatore.
   
   > [!NOTE]
   > L'identificatore del piano deve essere univoco all'interno dell'ambito di ciascuna offerta. Come molti altri identificatori utilizzati nel portale di pubblicazione, l'identificatore del piano verrà bloccato dopo la prima pubblicazione prodotta e non sarà più modificabile.
   > 
   > 
3. Fare clic per confermare la scelta.
4. Verranno quindi poste alcune domande aggiuntive circa il piano appena creato.
   
    ![disegno](media/marketplace-publishing-data-service-creation/step-5.2.png)

| Domanda | Significato |
| --- | --- |
| **Il piano è gratuito e disponibile tutto il mondo?** |È possibile creare un piano completamente gratuito. Se si tratta dell'unico piano di questa offerta, verrà pubblicata un'offerta gratuita in Marketplace. Se si tratta solo di uno di alcuni piani, sarà possibile offrire agli utenti finali per ulteriori informazioni sul servizio con un numero relativamente ridotto di transazioni mensili.  Se la risposta è "Sì", non verranno poste ulteriori domande. |

> [!NOTE]
> Gli utenti finali possono effettuare l'aggiornamento ai piani a pagamento.
> 
> 

| Domanda | Significato |
| --- | --- |
| **È disponibile una versione di prova gratuita?** |È possibile selezionare "Nessuna versione di prova" o fornire un'opzione per utilizzare il piano per "Un mese". Gli autori preferiscono questa opzione per offrire agli utenti finali la possibilità di scoprire i vantaggi dell'offerta mediante un mese di utilizzo gratuito. |

> [!IMPORTANT]
> Gli utenti finali potranno acquistare una versione di prova gratuita solo se hanno impostato uno strumento per i pagamenti, ad esempio una carta di credito, un contratto aziendale ecc.
> 
> Dopo un mese di prova gratuita, Azure Marketplace addebiterà ai clienti il prezzo a partire dalla data della sottoscrizione, a meno che il cliente non abbia richiesto l'annullamento della sottoscrizione. Gli utenti finali non riceveranno alcuna particolare notifica.
> 
> 

| Domanda | Significato |
| --- | --- |
| **Il piano richiede un codice di promozione per l'acquisto?** |Gli autori hanno la possibilità di limitare l'accesso ai propri piani di servizio fornendo un codice speciale, noto come "codice di promozione", a determinati clienti. Solo gli utenti finali che dispongono di tale codice di promozione potranno sottoscrivere il piano. Se si seleziona "No", si accetta che tutti gli utenti dall'area in cui l'offerta è disponibile (vedere [Guida ai contenuti marketing nel Marketplace](marketplace-publishing-push-to-staging.md) per ulteriori dettagli) potranno sottoscrivere il piano. Non verranno poste ulteriori domande. |
| **Nascondere anche il piano a tutti gli utenti che non dispongono di un codice di promozione valido?** |Se la risposta alla domanda precedente è "Sì", l'autore può anche rimuovere completamente il piano dalla visualizzazione nell'interfaccia utente del Marketplace. Ciò significa che i clienti non visualizzeranno il piano nella pagina dei dettagli dell'offerta. Gli utenti finali che riceveranno un codice di promozione per l'acquisto potranno effettuare la sottoscrizione tramite tale codice. |

## <a name="6----create-your-marketplace-marketing-content"></a>6.    Creare contenuti di marketing di Marketplace
Per dettagli su come fornire le informazioni necessarie nelle schede **Marketing, Prezzi, Supporto e Categorie** , visitare la [Guida ai contenuti marketing nel Marketplace](marketplace-publishing-push-to-staging.md) che è comune a tutti gli elementi pubblicati in Azure Marketplace.  

## <a name="7----connect-your-offer-to-your-service-sql-azure-based-or-web-service-based"></a>7.    Collegare l'offerta al servizio (basato su SQL Azure o su servizio Web).
Fare clic sul sottomenu **Servizi dati** .

Nella metà superiore della pagina verrà richiesto di indicare lo **spazio dei nomi**dell'offerta.  

  ![disegno](media/marketplace-publishing-data-service-creation/step-7.png)

La domanda seguente definirà in che modo l'autore esporrà l'offerta appena creata in Azure Marketplace. (Per ulteriori informazioni, vedere la [Guida ai prerequisiti tecnici dei servizi dati](marketplace-publishing-data-service-creation-prerequisites.md)).

  ![disegno](media/marketplace-publishing-data-service-creation/step-7.2.png)

**Pubblicazione del servizio basato su database**

Fare clic su **Database**. Verrà visualizzata la pagina seguente:

  ![disegno](media/marketplace-publishing-data-service-creation/step-7.3.png)

Per creare un mapping CSDL per il set di dati basato su database di SQL Azure:

  ![disegno](media/marketplace-publishing-data-service-creation/step-7.4.png)

E quindi per ciascuna tabella

  ![disegno](media/marketplace-publishing-data-service-creation/step-7.5.png)

  ![disegno](media/marketplace-publishing-data-service-creation/step-7.6.png)

Se il servizio Web

  ![disegno](media/marketplace-publishing-data-service-creation/step-7.7.png)

> [!IMPORTANT]
> Leggere [Mapping di un servizio Web esistente in OData tramite CSDL](marketplace-publishing-data-service-creation-odata-mapping.md) per istruzioni dettagliate ed esempi sulla creazione di un servizio Web CSDL.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Una volta creata l'offerta del servizio dati, verificare di aver completato le istruzioni della [guida ai contenuti di marketing nel Marketplace](marketplace-publishing-push-to-staging.md) prima di proseguire all'articolo [Testing your Data Service in Staging](marketplace-publishing-data-service-test-in-staging.md) (Test del servizio dati in staging).

## <a name="see-also"></a>Vedere anche
* [Guida introduttiva: Come pubblicare un'offerta in Azure Marketplace](marketplace-publishing-getting-started.md)
* Per apprendere il processo di mapping generale e lo scopo di OData , leggere questo articolo [Mapping di OData del servizio dati](marketplace-publishing-data-service-creation-odata-mapping.md) per esaminare le definizioni, le strutture e le istruzioni.
* Per ricevere formazione e informazioni sui nodi specifici e i relativi parametri, leggere l'articolo relativo ai [nodi di mapping di OData del servizio dati](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) per definizioni, spiegazioni, esempi e casi di utilizzo contestuali.
* Per esaminare gli esempi, leggere questo articolo [Esempi di mapping di OData del servizio dati](marketplace-publishing-data-service-creation-odata-mapping-examples.md) per consultare il codice di esempio e apprendere la sintassi del codice e il contesto.

[link-pubportal]:https://publish.windowsazure.com



<!--HONumber=Nov16_HO3-->


