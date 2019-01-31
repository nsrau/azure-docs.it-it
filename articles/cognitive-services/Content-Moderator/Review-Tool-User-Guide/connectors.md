---
title: Connettersi ad altri servizi durante la moderazione di contenuti - Content Moderator
titlesuffix: Azure Cognitive Services
description: Informazioni su come accedere ad altre API per i flussi di lavoro di Content Moderator usando i connettori.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 5e56579a856f7b6259acddcbe34f2e5361505cb5
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217612"
---
# <a name="connect-to-other-cognitive-services"></a>Connettersi ad altri servizi cognitivi

Oltre all'API Content Moderator, i flussi di lavoro di Azure Content Moderator possono usare altre API. È possibile accedere ad altre API usando un connettore in Content Moderator. Il connettore fornisce un collegamento ad altre API.

Content Moderator include questi connettori predefiniti:

* API Emozioni
* API Viso
* Servizio cloud PhotoDNA

![Connettori disponibili di Content Moderator](images/connectors-1.png)

## <a name="verify-your-credentials"></a>Verificare le credenziali 

Prima di definire un flusso di lavoro, verificare di avere delle credenziali valide per l'API del connettore che si vuole usare:

1.  Nella scheda **Settings** (Impostazioni) del dashboard dello strumento di revisione selezionare  > **Connectors** (Connettori).

  ![In Content Moderator vengono selezionati i connettori](images/connectors-2.png)

2.  Selezionare il simbolo di **modifica** accanto al connettore per cui si vogliono verificare le credenziali.

  ![In Content Moderator viene selezionato il simbolo di modifica](images/connectors-3.png)

3.  Viene visualizzata la chiave di sottoscrizione. Al termine, se si apportano modifiche, fare clic su **Save** (Salva).

  ![Pagina della modifica dei connettori di Content Moderator](images/connectors-4-1.png)
 
## <a name="add-a-connector"></a>Aggiungere un connettore

1.  Per poter aggiungere un connettore è necessaria una chiave di sottoscrizione. Nel dashboard dello strumento di revisione selezionare **Settings** (Impostazioni)  > **Credentials** (Credenziali). Selezionare e copiare il valore nella casella **Ocp-Admin-Subscription-Key**.

2.  Selezionare **Connectors** (Connettori). Selezionare uno dei connettori disponibili che vengono visualizzati nel dashboard dello strumento di revisione. Selezionare **Connect** (Connetti). 

  ![Pagina dell'aggiunta di un connettore di Content Moderator](images/connectors-5.png)

3.  Nella casella **Ocp-Admin-Subscription-Key** incollare la chiave copiata. Selezionare quindi **Salva**.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come usare i connettori per [definire dei flussi di lavoro personalizzati](workflows.md).
