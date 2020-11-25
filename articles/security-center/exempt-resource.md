---
title: Esentare una risorsa dalle raccomandazioni sulla sicurezza del Centro sicurezza di Azure e il Punteggio sicuro
description: Informazioni su come esentare una risorsa dalle raccomandazioni sulla sicurezza e il Punteggio sicuro
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: b7780a0ef70a89a88070d5883cc840319a67fa3d
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122360"
---
# <a name="exempt-a-resource-from-recommendations-and-secure-score"></a>Esentare una risorsa dai consigli e dal punteggio di sicurezza

Una priorità di base di ogni team di sicurezza consiste nel provare a garantire che gli analisti possano concentrarsi sulle attività e gli eventi imprevisti che interessano l'organizzazione. Il Centro sicurezza dispone di molte funzionalità per la personalizzazione delle informazioni a cui si attribuisce più priorità e per assicurarsi che il Punteggio sicuro sia un riflesso valido delle decisioni di sicurezza dell'organizzazione. L'esenzione delle risorse è una funzionalità di questo tipo.

Quando si esamina una raccomandazione per la sicurezza nel centro sicurezza di Azure, una delle prime informazioni esaminate è l'elenco delle risorse interessate.

In alcuni casi, una risorsa viene elencata e non deve essere inclusa. È possibile che sia stata corretta da un processo non rilevato dal Centro sicurezza o che l'organizzazione abbia deciso di accettare il rischio per tale risorsa specifica. 

In questi casi, è possibile creare una regola di esenzione e assicurarsi che la risorsa non sia elencata con le risorse non integre in futuro e non influisca sul punteggio sicuro. 

La risorsa sarà elencata come non applicabile e il motivo verrà visualizzato come "esentato" con la giustificazione selezionata.

## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|Anteprima<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Prezzi:|Si tratta di una funzionalità di criteri di Azure Premium offerta per i clienti di Azure Defender senza costi aggiuntivi. Per gli altri utenti, potrebbero essere applicati addebiti in futuro.|
|Autorizzazioni e ruoli obbligatori:|**Proprietario della sottoscrizione** o **collaboratore dei criteri** per creare un'esenzione<br>Per creare una regola, è necessario disporre delle autorizzazioni per modificare i criteri in criteri di Azure.<br>Per altre informazioni, vedere [autorizzazioni RBAC di Azure in criteri di Azure](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![No](./media/icons/no-icon.png) Cloud nazionali/sovrani (US Gov, governo cinese, altri governi)|
|||


## <a name="create-an-exemption-rule"></a>Creare una regola di esenzione

1. Nell'elenco delle risorse non integre selezionare il menu con i puntini di sospensione ("...") per la risorsa che si desidera esentare.

    :::image type="content" source="./media/exempt-resource/create-exemption.png" alt-text="Opzione Crea esenzione dal menu di scelta rapida":::

    Verrà visualizzato il riquadro crea esenzione.

    :::image type="content" source="./media/exempt-resource/exemption-rule-options.png" alt-text="Crea riquadro esenzione":::

1. Immettere i criteri e selezionare i criteri per il motivo per cui la risorsa deve essere esentata:
    - **Attenuato** : questo problema non è pertinente per la risorsa perché è stato gestito da uno strumento o un processo diverso rispetto a quello suggerito
    - **Deroga** -accettazione del rischio per questa risorsa
1. Selezionare **Salva**.
1. Dopo un po' di tempo (potrebbero essere necessarie fino a 24 ore):
    - La risorsa non ha alcun effetto sul punteggio sicuro.
    - La risorsa è elencata nella scheda **non applicabile** della pagina dei dettagli della raccomandazione
    - L'elenco di informazioni nella parte superiore della pagina dei dettagli della raccomandazione elenca il numero di risorse esentate:
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="Numero di risorse esentate":::

1. Per esaminare le risorse esentate, aprire la scheda **non applicabile** .

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="Modifica di un'esenzione":::

    Il motivo di ogni esenzione è incluso nella tabella (1).

    Per modificare o eliminare un'esenzione, selezionare il menu con i puntini di sospensione ("...") come illustrato (2).


## <a name="review-all-of-the-exemption-rules-on-your-subscription"></a>Esaminare tutte le regole di esenzione per la sottoscrizione

Le regole di esenzione usano criteri di Azure per creare un'esenzione per la risorsa nell'assegnazione dei criteri.

È possibile usare i criteri di Azure per tenere traccia di tutte le esenzioni nella pagina di **esenzione** :

:::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Pagina di esenzione di criteri di Azure":::



## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come esentare una risorsa da una raccomandazione, in modo che non influisca sul punteggio sicuro. Per ulteriori informazioni sul punteggio sicuro, vedere:

- [Punteggio di sicurezza nel Centro sicurezza di Azure](secure-score-security-controls.md)