---
title: Creare un account di rendering remoto di AzureCreate an Azure Remote Rendering account
description: Descrive i passaggi per creare un account per il rendering remoto di AzureDescribes the steps to create an account for Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: b9b72fb9e80c588eb3e6642d0228bffa50b35c6e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681298"
---
# <a name="create-an-azure-remote-rendering-account"></a>Creare un account di rendering remoto di AzureCreate an Azure Remote Rendering account

Questo capitolo illustra i passaggi per creare un account per il servizio di rendering remoto di **Azure.This** chapter guides you through the steps to create an account for the Azure Remote Rendering service. Un account valido è obbligatorio per il completamento di una qualsiasi delle guide introduttive o esercitazioni.

## <a name="create-an-account"></a>Creare un account

I passaggi seguenti sono necessari per creare un account per il servizio di rendering remoto di Azure:The following steps are needed to create an account for the Azure Remote Rendering service:

1. Vai alla [pagina Anteprima realtà mista](https://aka.ms/MixedRealityPrivatePreview)
1. Fare clic sul pulsante 'Crea una risorsa'
1. Nel campo di ricerca ("Cerca nel marketplace", digita "Rendering remoto" e premi "Invio".
1. Nell'elenco dei risultati, fare clic sul riquadro "Rendering remoto"
1. Nella schermata successiva, fare clic sul pulsante "Crea". Viene aperto un modulo per creare un nuovo account di rendering remoto:
    1. Impostare 'Nome risorsa' sul nome dell'account
    1. Aggiorna 'Sottoscrizione' se necessario
    1. Impostare 'Gruppo di risorse' su un gruppo di risorse di propria sceltaSet 'Resource group' to a resource group of your choice
1. Una volta creato l'account, accedi ad esso e:
    1. Nella scheda *Panoramica,* prendere nota dell'"ID account"
    1. Nella scheda Impostazioni > Chiavi di *accesso,* prendere nota della 'Chiave primaria' - questa è la chiave dell'account segreto dell'account

### <a name="retrieve-the-account-information"></a>Recuperare le informazioni sull'account

Gli esempi e le esercitazioni richiedono di fornire l'ID account e una chiave. Ad esempio nel file arrconfig.json usato per gli script di esempio di PowerShell:For instance in the **arrconfig.json** file that is used for the PowerShell sample scripts:

```json
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure portal>",
        "arrAccountKey": "<fill in the account key from the Azure portal>",
        "region": "<select from available regions>"
    },
```

Vedere [l'elenco delle regioni disponibili](../reference/regions.md) per la compilazione dell'opzione *regione.*

I valori **`arrAccountId`** **`arrAccountKey`** per e possono essere trovati nel portale come descritto nei passaggi seguenti:

* Passare al portale di [AzureGo](https://www.portal.azure.com) to the Azure portal
* Trova il tuo **"Account di rendering remoto"** - dovrebbe essere nell'elenco **"Risorse recenti".** Puoi anche cercarlo nella barra di ricerca in alto. In tal caso, assicurarsi che la sottoscrizione che si vuole usare sia selezionata nel filtro di sottoscrizione predefinito (icona del filtro accanto alla barra di ricerca):

![Filtro della sottoscrizione](./media/azure-subscription-filter.png)

Cliccando sul tuo account si porta a questa schermata, che mostra **l'ID account** subito:

![ID account di AzureAzure account ID](./media/azure-account-id.png)

Per il tasto, seleziona Tasti di **scelta** nel pannello a sinistra. La pagina successiva mostra una chiave primaria e una secondaria:The next page shows a primary and a secondary key:

![Chiavi di accesso di AzureAzure access keys](./media/azure-account-primary-key.png)

Il valore **`arrAccountKey`** per può essere una chiave primaria o secondaria.

## <a name="link-storage-accounts"></a>Collegare gli account di archiviazione

Questo paragrafo spiega come collegare gli account di archiviazione all'account di rendering remoto. Quando un account di archiviazione è collegato, non è necessario generare un URI di accesso condiviso ogni volta che si vuole interagire con i dati nell'account, ad esempio durante il caricamento di un modello. È invece possibile usare direttamente i nomi degli account di archiviazione come descritto nella sezione Caricamento di [un modello.](../concepts/models.md#loading-models)

I passaggi descritti in questo paragrafo devono essere eseguiti per ogni account di archiviazione che deve usare questo metodo di accesso alternativo. Se non sono ancora stati creati account di archiviazione, è possibile eseguire il passaggio rispettivo della conversione di un modello per la [guida introduttiva](../quickstarts/convert-model.md#storage-account-creation)al rendering.

A questo punto si presuppone che si disponga di un account di archiviazione. Passare all'account di archiviazione nel portale e passare alla scheda Controllo di accesso (IAM) per l'account di archiviazione:Navigate to the storage account in the portal and go to the **Access Control (IAM)** tab for that storage account:

![Account di archiviazione IAM](./media/azure-storage-account.png)

 Assicurarsi di disporre delle autorizzazioni di proprietario per questo account di archiviazione per assicurarsi di poter aggiungere assegnazioni di ruolo. Se non si dispone dell'accesso, l'opzione **Aggiungi assegnazione ruolo** verrà disabilitata.

 È necessario aggiungere tre ruoli distinti come descritto nei passaggi successivi. Se non si forniscono tutti e tre i livelli di accesso, si verificano problemi di autorizzazione durante il tentativo di accedere all'account di archiviazione.

 Fare clic sul pulsante **Aggiungi** nel riquadro "Aggiungi un'assegnazione di ruolo" per aggiungere il primo ruolo:

![Account di archiviazione IAM](./media/azure-add-role-assignment.png)

* Il primo ruolo da assegnare è **Proprietario** come mostrato nella schermata precedente. 
* Selezionare **Account di rendering remoto** dall'elenco a discesa Assegna accesso**a.**
* Selezionare l'abbonamento e l'account di rendering remoto negli ultimi elenchi a discesa.

Ripetere l'aggiunta di nuovi ruoli altre due volte per le rispettive selezioni dall'elenco a discesa **Ruolo:**
* **Collaboratore account di archiviazione**
* **Collaboratore ai dati del BLOB di archiviazione**

Gli altri elenchi a discesa vengono selezionati come nel primo passaggio.

Se sono stati aggiunti tutti e tre i ruoli, l'account di rendering remoto di Azure ha accesso all'account di archiviazione usando le identità del servizio gestito a cui sono state assegnate dal sistema.

## <a name="next-steps"></a>Passaggi successivi

* [autenticazione](authentication.md)
* [Uso delle API front-end di Azure per l'autenticazioneUsing the Azure Frontend APIs for authentication](frontend-apis.md)
* [Script di PowerShell di esempioExample PowerShell scripts](../samples/powershell-example-scripts.md)
