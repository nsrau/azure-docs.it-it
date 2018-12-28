---
title: Creare un endpoint personalizzato per il riconoscimento vocale con il Servizio di riconoscimento vocale in Azure | Microsoft Docs
description: Informazioni su come creare un endpoint personalizzato per il riconoscimento vocale con il Servizio di riconoscimento vocale in Servizi cognitivi.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 03/12/2018
ms.author: panosper
ms.openlocfilehash: d06dbc399a3610982c99a548373482fc5ec21f4d
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52852870"
---
# <a name="create-a-custom-speech-to-text-endpoint"></a>Creare un endpoint personalizzato per il riconoscimento vocale

Dopo aver creato modelli acustici o linguistici personalizzati, è possibile distribuirli in un endpoint personalizzato di riconoscimento vocale. 

## <a name="create-an-endpoint"></a>Creare un endpoint
Per creare un nuovo endpoint personalizzato, selezionare **Endpoint** nel menu **Riconoscimento vocale personalizzato** all'inizio della pagina. Viene visualizzata la pagina **Endpoint**, che contiene una tabella degli endpoint personalizzati correnti. Se non sono stati ancora creati endpoint, la tabella è vuota. Le impostazioni locali correnti sono indicate nel titolo della tabella. 

Per creare una distribuzione per una lingua diversa, selezionare **Change Locale** (Cambia impostazioni locali). Per altre informazioni sui linguaggi supportati.

Per creare un nuovo endpoint, fare clic su **Crea nuovo**. Nel riquadro **Crea endpoint** immettere le informazioni nelle caselle **Nome** e **Descrizione** della distribuzione personalizzata.

Nella casella combinata **Sottoscrizione** selezionare la sottoscrizione che si vuole usare. Se si tratta di una sottoscrizione S0 (versione a pagamento), è possibile selezionare richieste simultanee.

Si può anche specificare se la registrazione del contenuto deve essere attivata o disattivata. In altre parole, si specifica se il traffico dell'endpoint debba essere archiviato. Se questa impostazione non viene selezionata, il traffico non viene archiviato. Per tutto il contenuto registrato è possibile trovare collegamenti per il download nella vista Endpoint -> Dettagli

Nell'elenco **Modello acustico** selezionare il modello acustico desiderato e nell'elenco **Modello linguistico** selezionare il modello linguistico. Le scelte dei modelli acustici e linguistici includono sempre i modelli base Microsoft. La selezione del modello base limita le combinazioni. Non è possibile combinare modelli colloquiali di base con modelli di ricerca e dettatura di base.

> [!NOTE]
> Accettare le condizioni d'uso e le informazioni sui prezzi selezionando la casella di controllo.
>

Dopo aver selezionato il modello acustico e quello linguistico, scegliere **Crea**. Si tornerà alla pagina **Distribuzioni**. Ora la tabella include una voce che corrisponde al nuovo endpoint. Lo stato dell'endpoint ne riflette lo stato corrente al momento della creazione. La creazione di un'istanza del nuovo endpoint con i modelli personalizzati può richiedere fino a 30 minuti. Quando lo stato della distribuzione cambia in *Completata*, l'endpoint è pronto per l'uso.

Quando la distribuzione è pronta, il nome dell'endpoint diventa un collegamento. Se si seleziona il collegamento viene visualizzata la pagina delle **informazioni sull'endpoint** che contiene gli URL dell'endpoint personalizzato da usare con una richiesta HTTP o con la libreria client per il riconoscimento vocale dei Servizi cognitivi Microsoft, che usa Web Socket.

## <a name="next-steps"></a>Passaggi successivi

Per altre esercitazioni, vedere:
- [Ottenere una sottoscrizione di prova al servizio Voce](https://azure.microsoft.com/try/cognitive-services/)
- [Creare un modello acustico personalizzato](how-to-customize-acoustic-models.md)
- [Creare un modello linguistico personalizzato](how-to-customize-language-model.md)
