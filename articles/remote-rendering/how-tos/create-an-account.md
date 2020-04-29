---
title: Creare un account di Rendering remoto di Azure
description: Descrive i passaggi per creare un account per il rendering remoto di Azure
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: b9b72fb9e80c588eb3e6642d0228bffa50b35c6e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681298"
---
# <a name="create-an-azure-remote-rendering-account"></a>Creare un account di Rendering remoto di Azure

In questo capitolo vengono illustrati i passaggi per creare un account per il servizio di **rendering remoto di Azure** . Un account valido è obbligatorio per il completamento di tutte le guide introduttive o le esercitazioni.

## <a name="create-an-account"></a>Creare un account

Per creare un account per il servizio di rendering remoto di Azure, sono necessari i passaggi seguenti:

1. Vai alla [pagina di anteprima della realtà mista](https://aka.ms/MixedRealityPrivatePreview)
1. Fare clic sul pulsante ' crea una risorsa '
1. Nel campo di ricerca ("Cerca nel Marketplace") digitare "Remote rendering" e premere INVIO.
1. Nell'elenco dei risultati fare clic sul riquadro "Remote rendering".
1. Nella schermata successiva fare clic sul pulsante "Crea". Viene aperto un modulo per creare un nuovo account di rendering remoto:
    1. Impostare ' Resource Name ' sul nome dell'account
    1. Aggiornare ' Subscription ' se necessario
    1. Impostare ' gruppo di risorse ' su un gruppo di risorse di propria scelta
1. Una volta creato l'account, passare a esso e:
    1. Nella scheda *Panoramica* prendere nota dell'ID account
    1. Nella scheda *impostazioni > chiavi di accesso* prendere nota della "chiave primaria". si tratta della chiave dell'account Secret dell'account

### <a name="retrieve-the-account-information"></a>Recuperare le informazioni sull'account

Per gli esempi e le esercitazioni è necessario fornire l'ID e una chiave dell'account. Ad esempio, nel file **arrconfig. JSON** usato per gli script di esempio di PowerShell:

```json
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure portal>",
        "arrAccountKey": "<fill in the account key from the Azure portal>",
        "region": "<select from available regions>"
    },
```

Vedere l' [elenco delle aree disponibili](../reference/regions.md) per compilare l'opzione *Region* .

I valori per **`arrAccountId`** e **`arrAccountKey`** sono reperibili nel portale, come descritto nei passaggi seguenti:

* Accedere al [portale di Azure](https://www.portal.azure.com)
* Trovare l' **"account per il rendering remoto"** , che dovrebbe trovarsi nell'elenco **"risorse recenti"** . È anche possibile cercarlo nella barra di ricerca nella parte superiore. In tal caso, assicurarsi che la sottoscrizione che si vuole usare sia selezionata nel filtro di sottoscrizione predefinito (icona del filtro accanto alla barra di ricerca):

![Filtro della sottoscrizione](./media/azure-subscription-filter.png)

Facendo clic sull'account viene visualizzata questa schermata che mostra l'ID dell' **account** immediatamente:

![ID account Azure](./media/azure-account-id.png)

Per la chiave, selezionare **chiavi di accesso** nel pannello a sinistra. La pagina successiva mostra una chiave primaria e una chiave secondaria:

![Chiavi di accesso di Azure](./media/azure-account-primary-key.png)

Il valore di **`arrAccountKey`** può essere una chiave primaria o secondaria.

## <a name="link-storage-accounts"></a>Collegare gli account di archiviazione

Questo paragrafo spiega come collegare gli account di archiviazione all'account di rendering remoto. Quando si collega un account di archiviazione, non è necessario generare un URI di firma di accesso condiviso ogni volta che si vuole interagire con i dati dell'account, ad esempio durante il caricamento di un modello. È invece possibile usare direttamente i nomi degli account di archiviazione, come descritto nella [sezione Caricamento di un modello](../concepts/models.md#loading-models).

Per ogni account di archiviazione che deve usare questo metodo di accesso alternativo, è necessario eseguire la procedura descritta in questo paragrafo. Se gli account di archiviazione non sono ancora stati creati, è possibile esaminare il passaggio corrispondente nella [Guida introduttiva per la conversione di un modello per il rendering](../quickstarts/convert-model.md#storage-account-creation).

A questo punto si presuppone che si disponga di un account di archiviazione. Passare all'account di archiviazione nel portale e passare alla scheda **controllo di accesso (IAM)** per l'account di archiviazione:

![IAM dell'account di archiviazione](./media/azure-storage-account.png)

 Assicurarsi di disporre delle autorizzazioni di proprietario per l'account di archiviazione per assicurarsi che sia possibile aggiungere assegnazioni di ruolo. Se non si ha accesso, l'opzione **Aggiungi un'assegnazione di ruolo** verrà disabilitata.

 È necessario aggiungere tre ruoli distinti, come descritto nei passaggi successivi. Se non si forniscono tutti e tre i livelli di accesso, si verificano problemi di autorizzazione durante il tentativo di accesso all'account di archiviazione.

 Fare clic sul pulsante **Aggiungi** nel riquadro "Aggiungi assegnazione ruolo" per aggiungere il primo ruolo:

![IAM dell'account di archiviazione](./media/azure-add-role-assignment.png)

* Il primo ruolo da assegnare è **proprietario** , come illustrato nella schermata precedente. 
* Selezionare **account per il rendering remoto** dall'elenco a discesa ***assegna accesso a** .
* Selezionare la sottoscrizione e l'account per il rendering remoto negli ultimi elenchi a discesa.

Ripetere l'aggiunta di nuovi ruoli altre due volte per le rispettive selezioni dall'elenco a discesa **Role** :
* **Collaboratore account di archiviazione**
* **Collaboratore ai dati del BLOB di archiviazione**

Gli altri elenchi a discesa sono selezionati come nel primo passaggio.

Se sono stati aggiunti tutti e tre i ruoli, l'account di rendering remoto di Azure ha accesso all'account di archiviazione usando le identità del servizio gestito assegnate dal sistema.

## <a name="next-steps"></a>Passaggi successivi

* [autenticazione](authentication.md)
* [Uso delle API front-end di Azure per l'autenticazione](frontend-apis.md)
* [Script di Azure PowerShell di esempio](../samples/powershell-example-scripts.md)
