---
title: Domande frequenti sulla pubblicazione e sull&quot;uso delle app di Machine Learning in Azure Marketplace | Documentazione Microsoft
description: Domande frequenti
services: machine-learning
documentationcenter: 
author: bharaths
manager: jhubbard
editor: cgronlun
ms.assetid: 26b3a1e7-8b9a-4004-98bc-17456d4c25e8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2016
ms.author: bharaths
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f8ae758a406dfed48968531ae20a9bbd2383db92


---
# <a name="publishing-and-using-machine-learning-apps-in-the-azure-marketplace-faq"></a>Domande frequenti sulla pubblicazione e sull'uso delle app di Machine Learning in Azure Marketplace
## <a name="questions-about-consuming-from-marketplace"></a>Domande sull'utilizzo da Marketplace
**1. Perché viene generato il messaggio di errore seguente dopo l'immissione di dati di input per il servizio Web?**

**La richiesta ha avuto come risultato un timeout di back-end o un errore di back-end. Il team sta esaminando questo problema. Ci scusiamo per l'inconveniente. (500)**

È possibile che i parametri di input immessi non siano conformi al formato richiesto per il servizio Web specifico. Fare riferimento al collegamento seguente relativo alla documentazione per individuare il formato corretto per i parametri di input e per informazioni sulle limitazioni di questo servizio Web.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**2. Se si copia il collegamento all'API per il servizio Web disponibile in "Esplorare il set di dati" e lo si incolla in un'altra finestra del browser, quali credenziali è necessario usare per accedere ai risultati e come vengono visualizzati?**

È consigliabile usare l'account Marketplace come nome utente e la chiave dell'account principale come password. La chiave dell'account principale è disponibile nella pagina **Esplorare il set di dati**, sotto la descrizione del servizio Web (fare clic sul pulsante **Mostra**). È possibile che il risultato venga visualizzato nel browser o che sia disponibile per il download, a seconda del browser usato.

**3. Perché viene generato il messaggio di errore seguente dopo l'immissione di dati di input per il servizio Web nella pagina "Esplorare il set di dati"?** 

**Si è verificato un errore imprevisto durante l'elaborazione della richiesta. Riprovare più tardi.**

È possibile che uno o più parametri di input del servizio Web abbiano superato il limite di lunghezza durante l'utilizzo del servizio Web nella pagina **Esplorare il set di dati** del Marketplace. I servizi possono essere chiamati con una lunghezza di input maggiore usando i metodi HTTP POST. Per esempi, vedere l'argomento relativo alle [soluzioni di esempio sull'uso di R in Machine Learning e pubblicate in Marketplace](machine-learning-r-csharp-web-service-examples.md).

**4. Perché non riesco a visualizzare i risultati nella scheda "ESPLORA API" nello Store del portale di Azure classico?** 

Si tratta di un problema noto con il Marketplace del portale di Azure classico. Il team è impegnato nella risoluzione del problema. 

## <a name="questions-about-publishing-from-azure-machine-learning-on-marketplace"></a>Domande sulla pubblicazione da Azure Machine Learning nel Marketplace
**1. Perché i logo, le immagini e il numero di transazioni non vengono aggiornati per il servizio Web?** 

I logo e le immagini vengono memorizzati nella cache del portale di pubblicazione e potrebbero essere necessari fino a 10 giorni per l'aggiornamento del nuovo logo o della nuova immagine nel portale.

**2. Perché nella scheda "Dettagli" del servizio Web nel Marketplace viene visualizzato un errore?**

Si tratta di un problema noto del Marketplace durante la connessione ad Azure Machine Learning per ottenere informazioni dettagliate sui servizi. Il team è impegnato nella risoluzione del problema.

**3. Perché il codice R di esempio nei servizi Web di Azure Machine Learning non funziona per l'uso dei servizi Web nel Marketplace?**

I sistemi di autenticazione presentano differenze in caso di connessione diretta ai servizi Web di Azure Machine Learning rispetto alla connessione a questi servizi Web tramite il Marketplace. I servizi disponibili in Marketplace sono servizi OData e possono essere chiamati tramite metodi GET o POST. 

**4. Perché i collegamenti per il supporto delle offerte del servizio Web non vengono aggiornati correttamente per alcune offerte?**

I collegamenti per il supporto sono globali per le singole entità di pubblicazione e non sono specifici per le singole offerte. 

**5. Come si pubblica un servizio Web con modalità di input batch nel Marketplace?**

La modalità di input batch non è attualmente supportata nei servizi Web del Marketplace.

**6. A chi ci si deve rivolgere per ottenere supporto in caso di domande su come diventare un'entità di pubblicazione di dati o in caso di problemi durante la pubblicazione?**

Per altre informazioni, contattare il team di Azure Marketplace in <mailto:datamarketbd@microsoft.com>.




<!--HONumber=Nov16_HO3-->


