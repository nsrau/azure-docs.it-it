---
title: Moderare il contenuto mediante la revisione umana con la console API - Content Moderator
titlesuffix: Azure Cognitive Services
description: Usare le operazioni di revisione dell'API di revisione per creare revisioni di immagini o testo per la moderazione umana.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 7e4c750e7470c6a76e56bd67d6a134b1b61535eb
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218391"
---
# <a name="create-reviews-from-the-api-console"></a>Creare revisioni dalla console per le API

Usare le [operazioni review](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4) dell'API di revisione per creare revisioni di immagini o testo per la moderazione umana. I moderatori umani usano lo strumento di revisione per esaminare il contenuto. Usare questa operazione in base alla logica di business post-moderazione. Usarla dopo avere analizzato il contenuto con una delle API di immagine o di testo di Content Moderator oppure altre API Servizi cognitivi. 

Dopo che un moderatore umano ha esaminato i tag assegnati automaticamente e i dati di stima e ha inviato una decisione finale relativa alla moderazione, l'API di revisione invia tutte le informazioni all'endpoint API.

## <a name="use-the-api-console"></a>Usare la console dell'API
Per eseguire il test drive dell'API usando la console online, sono necessari alcuni valori da immettere nella console:

- **teamName**: Nome del team creato quando è stato configurato l'account dello strumento di revisione. 
- **ContentId**: Questa stringa viene passata all'API e restituita tramite il callback. ContentId è utile per associare i metadati o gli identificatori interni ai risultati di un processo di moderazione.
- **Metadati**: Coppia chiave-valore personalizzata restituita all'endpoint API durante il callback. Se la chiave è un codice breve che viene definito nello strumento di revisione, viene visualizzata come tag.
- **Ocp-Apim-Subscription-Key**: Disponibile nella scheda **Impostazioni**. Per altre informazioni, vedere la [panoramica](overview.md).

Il modo più semplice per accedere a una console di test è dalla finestra **Credentials** (Credenziali).

1.  Nella finestra **Credentials** (Credenziali) selezionare [Review API reference](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4) (Informazioni di riferimento per l'API di revisione).

  Verrà aperta la pagina **Review - Create** (Revisione - Creazione).

2.  Per **Open API testing console** (Apri console di test dell'API) selezionare l'area che identifica con maggiore precisione la propria posizione.

  ![Selezione dell'area nella pagina Review - Create (Revisione - Creazione)](images/test-drive-region.png)

  Viene visualizzata la console dell'API **Review - Create** (Revisione - Creazione).
  
3.  Immettere i valori per i parametri di query obbligatori, il tipo di contenuto e la chiave di sottoscrizione. Nella casella **Request body** (Corpo della richiesta) specificare il contenuto (ad esempio, la posizione dell'immagine), i metadati e altre informazioni associate al contenuto.

  ![Parametri di query, intestazioni e casella Corpo della richiesta della console di creazione della revisione](images/test-drive-review-1.PNG)
  
4.  Selezionare **Send** (Invia). Viene creato un ID revisione. Copiare questo ID da usare nei passaggi seguenti.

  ![La casella Response content (Contenuto della risposta) della console Review - Create (Revisione - Creazione) visualizza l'ID revisione](images/test-drive-review-2.PNG)
  
5.  Selezionare **Get** (Ottieni) e quindi aprire l'API selezionando il pulsante corrispondente all'area. Nella pagina risultante immettere i valori per **teamName**, **ReviewID** e la **chiave di sottoscrizione**. Selezionare il pulsante **Send** (Invia) nella pagina. 

  ![Ottenere i risultati nella console di creazione della revisione](images/test-drive-review-3.PNG)
  
6.  Verranno visualizzati i risultati dell'analisi.

  ![Casella Response content (Contenuto della risposta) della console Review - Create (Revisione - Creazione)](images/test-drive-review-4.PNG)
  
7.  Nel dashboard di Content Moderator selezionare **Review** (Revisione) > **Image** (Immagine). Viene visualizzata l'immagine analizzata, pronta per la revisione umana.

  ![Immagine di un pallone da calcio nello strumento di revisione](images/test-drive-review-5.PNG)

## <a name="next-steps"></a>Passaggi successivi

Usare l'API REST nel codice o seguire la [guida introduttiva alle revisioni per .NET](moderation-reviews-quickstart-dotnet.md) per procedere all'integrazione con la propria applicazione.
