---
title: Testare la definizione dell'interfaccia utente per le Applicazioni gestite di Azure | Microsoft Docs
description: Descrizione di come testare l'esperienza utente per la creazione di applicazione gestita di Azure tramite il portale.
author: tfitzmac
ms.service: managed-applications
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 54eb2df06df56c33e1a3cd74e7a4a93c07aab682
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575671"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Testare l'interfaccia del portale per le applicazioni gestite di Azure

Dopo aver [creato il file createUiDefinition. JSON](create-uidefinition-overview.md) per l'applicazione gestita, è necessario testare l'esperienza utente. Per semplificare i test, usare un ambiente sandbox che carica il file nel portale. Non è necessario distribuire l'applicazione gestita. Il sandbox presenta l'interfaccia utente nell'esperienza del portale a schermo intero corrente. In alternativa, è possibile usare uno script per testare l'interfaccia. Entrambi gli approcci sono illustrati in questo argomento. Il sandbox è il modo consigliato per visualizzare l'anteprima dell'interfaccia.

## <a name="prerequisites"></a>Prerequisiti

* Un file **createUiDefinition.json**. Se non si dispone di questo file, copiare il [file di esempio](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json).

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="use-sandbox"></a>USA sandbox

1. Aprire la [sandbox creare la definizione dell'interfaccia utente](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade).

   ![Mostra sandbox](./media/test-createuidefinition/show-sandbox.png)

1. Sostituire la definizione vuota con il contenuto del file createUiDefinition. JSON. Selezionare **Anteprima**.

   ![Seleziona anteprima](./media/test-createuidefinition/select-preview.png)

1. Viene visualizzato il modulo creato. È possibile eseguire l'esperienza utente e inserire i valori.

   ![Mostra modulo](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Risoluzione dei problemi

Se il modulo non viene visualizzato dopo aver selezionato **Anteprima**, è possibile che si verifichi un errore di sintassi. Cercare l'indicatore rosso sulla barra di scorrimento a destra e passarvi.

![Mostra errore di sintassi](./media/test-createuidefinition/show-syntax-error.png)

Se il modulo non viene visualizzato e viene visualizzata un'icona di un cloud con una lacrima, il form presenta un errore, ad esempio una proprietà mancante. Aprire il Strumenti di sviluppo Web nel browser. La **Console** consente di visualizzare i messaggi importanti relativi all'interfaccia.

![Mostra l'errore](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Usa script di test

Per testare l'interfaccia nel portale, copiare uno dei seguenti script nel computer locale:

* [Script di caricamento laterale PowerShell-AZ Module](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-AzCreateUIDefinition.ps1)
* [Script di caricamento laterale di PowerShell-modulo di Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Script dell'interfaccia della riga di comando di Azure sideload](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

Per visualizzare il file di interfaccia nel portale, eseguire lo script scaricato. Lo script crea un account di archiviazione nella sottoscrizione di Azure e carica il file createuidefinition.json nell'account di archiviazione. L'account di archiviazione viene creato la prima volta che si esegue lo script o se è stato eliminato. Se l'account di archiviazione esiste già nella sottoscrizione di Azure, lo script lo riutilizza. Lo script apre il portale e carica il file dall'account di archiviazione.

Specificare un percorso per l'account di archiviazione e specificare la cartella che contiene il file createuidefinition.json.

Per PowerShell, usare:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Per l'interfaccia della riga di comando di Azure usare:

```azurecli
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Se il file createUiDefinition.json si trova nella stessa cartella dello script, se è già stato creato l'account di archiviazione, non è necessario specificare tali parametri.

Per PowerShell, usare:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1
```

Per l'interfaccia della riga di comando di Azure usare:

```azurecli
./sideload-createuidef.sh
```

Lo script apre una nuova scheda nel browser. Viene visualizzato il portale con l'interfaccia per la creazione dell'applicazione gestita.

Specificare i valori per il campo. Al termine, verranno visualizzati i valori passati al modello che si trovano nella console degli strumenti di sviluppo del browser.

![Mostra valori](./media/test-createuidefinition/show-json.png)

È possibile usare questi valori come il file dei parametri per eseguire il test del modello di distribuzione.

Se il portale si blocca nella schermata di riepilogo, potrebbe essersi verificato un bug nella sezione output. Ad esempio, è stato fatto riferimento a un controllo che non esiste. Se un parametro nell'output è vuoto, è possibile che il parametro faccia riferimento a una proprietà che non esiste. Ad esempio, il riferimento al controllo è valido ma il riferimento alla proprietà non è valido.

## <a name="test-your-solution-files"></a>Testare il file della soluzione

Ora che si è verificato che l'interfaccia del portale funziona come previsto, è possibile verificare che il file createUiDefinition sia correttamente integrato con il file maintemplate.json. È possibile eseguire uno script di convalida dei test per testare il contenuto dei file di soluzione, incluso il file createUiDefinition. Lo script convalida la sintassi JSON, verifica la presenza di espressioni regex nei campi di testo e garantisce che i valori di output dell'interfaccia del portale corrispondono ai parametri del modello. Per informazioni sull'esecuzione di questo script, vedere [Eseguire i controlli di convalida statica per i modelli](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver verificato l'interfaccia del portale, leggere altre informazioni su come rendere disponibili le [Applicazioni gestite di Azure nel marketplace](publish-marketplace-app.md).
