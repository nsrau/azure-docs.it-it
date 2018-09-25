---
title: Creare un endpoint personalizzato per il riconoscimento vocale con il Servizio di riconoscimento vocale personalizzato in Azure | Microsoft Docs
description: Informazioni su come creare un endpoint personalizzato per il riconoscimento vocale con il Servizio di riconoscimento vocale personalizzato in Servizi cognitivi.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: ed93afa8e10fdfbb0d45f4500b4a648716e25e00
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46952223"
---
# <a name="create-a-custom-speech-to-text-endpoint"></a>Creare un endpoint personalizzato per il riconoscimento vocale
Dopo aver creato modelli acustici o linguistici personalizzati, è possibile distribuirli in un endpoint personalizzato di riconoscimento vocale. 

## <a name="create-an-endpoint"></a>Creare un endpoint
Per creare un nuovo endpoint personalizzato, selezionare **Distribuzioni** nel menu **Riconoscimento vocale personalizzato** all'inizio della pagina. Viene visualizzata la pagina **Distribuzioni**, che contiene una tabella degli endpoint personalizzati correnti. Se non sono stati ancora creati endpoint, la tabella è vuota. Le impostazioni locali correnti sono indicate nel titolo della tabella. 

Per creare una distribuzione per una lingua diversa, selezionare **Change Locale** (Cambia impostazioni locali). Per altre informazioni sulle lingue supportate, vedere [Impostazioni locali supportate nel Servizio di riconoscimento vocale personalizzato](cognitive-services-custom-speech-change-locale.md).

Per creare un nuovo endpoint, fare clic su **Crea nuovo**. Nel riquadro **Crea distribuzione** immettere le informazioni nelle caselle **Nome** e **Descrizione** della distribuzione personalizzata.

Nella casella combinata **Sottoscrizione** selezionare la sottoscrizione che si vuole usare. Nel caso di una sottoscrizione S2, è possibile selezionare le unità di scala e la registrazione del contenuto. Per altre informazioni sulle unità di scala e la registrazione, vedere [Contatori e quote del Servizio di riconoscimento vocale personalizzato](../cognitive-services-custom-speech-meters.md).

La tabella seguente mostra il mapping delle unità di scala alle richieste simultanee disponibili:

| Unità di scala | Numero di richieste simultanee |
| ------ | ----- |
| 0 | 1 |
| 1 | 5 |
| 2 | 10 |
| 3 | 15 |
| n | 5 * n |

Si può anche specificare se la registrazione del contenuto deve essere attivata o disattivata. In altre parole, si specifica se il traffico dell'endpoint debba essere archiviato ad uso interno di Microsoft. Se questa impostazione non viene selezionata, il traffico non viene archiviato. L'omissione dei risultati della registrazione del contenuto implica un costo aggiuntivo. Per informazioni dettagliate, vedere la [pagina di informazioni sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/).

> [!NOTE]
> In questa pagina la registrazione del contenuto è identificata dalla voce "Nessuna traccia".
>


Microsoft fornisce inoltre una stima approssimativa dei costi, in modo che l'utente sia consapevole dell'impatto dei costi delle unità di scala e della registrazione del contenuto. Va sottolineato che la stima è approssimativa e potrebbe differire dai costi effettivi.

> [!NOTE]
> Queste impostazioni non sono disponibili con le sottoscrizioni di livello gratuito.
>

Nell'elenco **Modello acustico** selezionare il modello acustico desiderato e nell'elenco **Modello linguistico** selezionare il modello linguistico. Le scelte dei modelli acustici e linguistici includono sempre i modelli base Microsoft. La selezione del modello base limita le combinazioni. Non è possibile combinare modelli colloquiali di base con modelli di ricerca e dettatura di base.

![Pagina Crea distribuzione](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-create2.png)

> [!NOTE]
> Accettare le condizioni d'uso e le informazioni sui prezzi selezionando la casella di controllo.
>

Dopo aver selezionato il modello acustico e quello linguistico, scegliere **Crea**. Si tornerà alla pagina **Distribuzioni**. Ora la tabella include una voce che corrisponde al nuovo endpoint. Lo stato dell'endpoint ne riflette lo stato corrente al momento della creazione. La creazione di un'istanza del nuovo endpoint con i modelli personalizzati può richiedere fino a 30 minuti. Quando lo stato della distribuzione cambia in *Completata*, l'endpoint è pronto per l'uso.

![Pagina Distribuzioni](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-ready.png)

Quando la distribuzione è pronta, il relativo nome diventa un collegamento. Se si seleziona il collegamento viene visualizzata la pagina **Informazioni distribuzione**, che contiene gli URL dell'endpoint personalizzato da usare con una richiesta HTTP o con la libreria client per il riconoscimento vocale dei Servizi cognitivi Microsoft, che usa Web Socket.

![Pagina Informazioni distribuzione](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-info2.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre esercitazioni, vedere:
* [Usare un endpoint personalizzato per il riconoscimento vocale](cognitive-services-custom-speech-use-endpoint.md)
* [Creare un modello acustico personalizzato](cognitive-services-custom-speech-create-acoustic-model.md)
* [Creare un modello linguistico personalizzato](cognitive-services-custom-speech-create-language-model.md)
