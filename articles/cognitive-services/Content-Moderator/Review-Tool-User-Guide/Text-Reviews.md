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
ms.openlocfilehash: 60a8d03a6562c9a66c2f6781ebbc330c989b343e
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2018
ms.locfileid: "51852096"
---
# <a name="review-text"></a>Verificare il testo

È possibile usare Azure Content Moderator per rivedere il testo tramite i punteggi e i tag rilevati. Usare le informazioni per determinare se il contenuto è appropriato. 

## <a name="select-or-enter-the-text-to-review"></a>Selezionare o immettere il testo da rivedere

In Content Moderator selezionare la scheda **Prova**. Selezionare quindi l'opzione **Testo** per passare alla schermata iniziale di moderazione testo. Immettere un testo qualsiasi o inviare il testo di esempio predefinito per la moderazione testo automatica. È possibile immettere un massimo di 1.024 caratteri.

## <a name="get-ready-to-review-results"></a>Prepararsi a esaminare i risultati

Lo strumento di revisione prima di tutto chiama l'API Moderazione testo, quindi crea revisioni del testo tramite i tag rilevati. Lo strumento di revisione abbina i punteggi risultanti per il team.

## <a name="review-text-results"></a>Rivedere i risultati del testo

I risultati, visualizzati in dettaglio nelle finestre, includono i tag rilevati e i termini restituiti dall'API Moderazione testo. Per attivare o disattivare lo stato di selezione di un tag, selezionare il tag. È anche possibile usare qualsiasi tag personalizzato eventualmente creato.

![Screenshot dello strumento di revisione che mostra il testo contrassegnato in una finestra del browser Chrome](../images/reviewresults_text.png)
