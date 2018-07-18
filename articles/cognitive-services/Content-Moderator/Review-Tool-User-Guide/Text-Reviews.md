---
title: Rivedere il testo in Azure Content Moderator | Microsoft Docs
description: Informazioni su come rivedere il testo in Content Moderator per visualizzarne il punteggio e i tag rilevati. Usare le informazioni per determinare se il contenuto è appropriato.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/03/2017
ms.author: sajagtap
ms.openlocfilehash: cb8774395b7374677e8de3b80630a2d4abf490f9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372916"
---
# <a name="review-text"></a>Verificare il testo

È possibile usare Azure Content Moderator per rivedere il testo tramite i punteggi e i tag rilevati. Usare le informazioni per determinare se il contenuto è appropriato. 

## <a name="select-or-enter-the-text-to-review"></a>Selezionare o immettere il testo da rivedere

In Content Moderator selezionare la scheda **Prova**. Selezionare quindi l'opzione **Testo** per passare alla schermata iniziale di moderazione testo. Immettere un testo qualsiasi o inviare il testo di esempio predefinito per la moderazione testo automatica. È possibile immettere un massimo di 1.024 caratteri.

## <a name="get-ready-to-review-results"></a>Prepararsi a esaminare i risultati

Lo strumento di revisione prima di tutto chiama l'API Moderazione testo, quindi crea revisioni del testo tramite i tag rilevati. Lo strumento di revisione abbina i punteggi risultanti per il team.

## <a name="review-text-results"></a>Rivedere i risultati del testo

I risultati, visualizzati in dettaglio nelle finestre, includono i tag rilevati e i termini restituiti dall'API Moderazione testo. Per attivare o disattivare lo stato di selezione di un tag, selezionare il tag. È anche possibile usare qualsiasi tag personalizzato eventualmente creato.

![Rivedere i risultati del testo](images/3-review-text-2.png)
