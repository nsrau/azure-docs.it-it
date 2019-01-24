---
title: Gestire le credenziali in Azure Content Moderator - Content Moderator
titlesuffix: Azure Cognitive Services
description: Gestire le credenziali di Content Moderator da usare con le API.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 3d183870938f44cd71f1dab4105eec390ba12d94
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2019
ms.locfileid: "54266146"
---
# <a name="manage-content-moderator-service-credentials"></a>Gestire le credenziali del servizio Content Moderator

Le credenziali di Content Moderator vengono create nelle posizioni seguenti:

- [Il portale di Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)
- [Lo strumento di revisione di Content Moderator](https://contentmoderator.cognitive.microsoft.com/)

Questo articolo illustra dove trovarle e come interagiscono tra loro.

## <a name="the-azure-portal"></a>Portale di Azure

Nel dashboard del portale di Azure selezionare l'account di Content Moderator. In **Gestione risorse** selezionare **Chiavi**. Per copiare la chiave, selezionare l'icona a destra della chiave.

![Chiavi di Content Moderator nel portale di Azure](images/credentials-azure-portal-keys.PNG)

### <a name="use-the-azure-account-with-the-review-tool-and-review-api"></a>Usare l'account di Azure con lo strumento e l'API di revisione
Per usare la chiave di Azure con le API di revisione, copiare l'ID risorsa elencato nella schermata **Proprietà** mostrata nello screenshot seguente e immetterlo nella schermata delle credenziali dello strumento di revisione nel campo **Whitelisted Resource Id(s)** (ID risorsa consentiti) come illustrato nella sezione **ID risorsa** seguente. 

> [!NOTE]
> L'area della sottoscrizione di Content Moderator deve corrispondere all'area del team di revisione perché Content Moderator possa riconoscere il team e accedere ai dati del team. Ad esempio, nelle immagini in questa pagina, l'area **West US** **(4)** contiene la sottoscrizione di Azure di Content Moderator e il team di revisione.
>
> Una volta inseriti la chiave e l'ID risorsa della sottoscrizione di Azure nello strumento di revisione, la chiave **Ocp-Apim-Subscription-Key** per la versione di valutazione gratuita mostrata nella schermata Credenziali non viene più utilizzata, ma è comunque disponibile.
> La chiave della versione di valutazione gratuita prevede un limite di 5.000 operazioni al mese e 1 richiesta al secondo (RPS).

![ID risorsa di Content Moderator nel portale di Azure](images/credentials-azure-portal-resourceid.PNG)

### <a name="use-the-azure-account-with-the-workflows-in-the-review-tool"></a>Usare l'account di Azure con i flussi di lavoro nello strumento di revisione

Per usare la chiave di Azure per i flussi di lavoro disponibili in Content Moderator, immetterla nel campo **Ocp-Apim-Subscription-Key** della sezione **Impostazioni del flusso di lavoro** come mostrato nella sezione **Flussi di lavoro** seguente. Selezionare **'+'** per salvare l'ID risorsa.

![Credenziali del flusso di lavoro di Content Moderator nello strumento di revisione](images/credentials-workflow.PNG)

## <a name="the-review-tool"></a>Strumento di revisione

Nella scheda **Impostazioni** del dashboard dello strumento di revisione selezionare **Credenziali**.

![Credenziali di Content Moderator nello strumento di revisione](images/credentials-trial-resource-workflow.PNG)

La sezione seguente esamina l'immagine precedente in modo più dettagliato:

### <a name="api"></a>API

Nella prima parte sono elencati i valori di **endpoint dell'API di revisione**, **ID del team** e **Ocp-Apim-Subscription-Key (chiave della versione di valutazione gratuita di Content Moderator)** generati nell'ambito della creazione del team di revisione. Usarli per chiamare tutte le API di Content Moderator, inclusa l'API di revisione.

Si noti inoltre l'identificatore di area per l'endpoint API. Ad esempio **westus** è l'area in "https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0"

![Chiave di Content Moderator nello strumento di revisione](images/credentials-trialkey.PNG)

### <a name="resource-id"></a>ID risorsa

Questo set di campi è descritto nella sezione precedente, [Usare l'account di Azure con lo strumento e l'API di revisione](credentials.md#use-the-azure-account-with-the-review-tool-and-review-api). Questo campo è in genere vuoto a meno che non si aggiunga l'Id di risorsa di Azure a questo campo come spiegato nella sezione precedente.

### <a name="workflows"></a>Flussi di lavoro

Questo set di campi è descritto nella sezione precedente, [Usare l'account di Azure con i flussi di lavoro nello strumento di revisione](credentials.md#use-the-azure-account-with-the-workflows-in-the-review-tool). Per impostazione predefinita, lo strumento di revisione utilizza la propria chiave di prova generata automaticamente per l'esecuzione di flussi di lavoro e questo è ciò che viene visualizzato per iniziare. Gli altri due campi consentono di usare elenchi di termini e immagini rispettivamente nelle operazioni di filtro del testo e valutazione di immagini.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come usare le credenziali di Content Moderator nei [flussi di lavoro](workflows.md).
