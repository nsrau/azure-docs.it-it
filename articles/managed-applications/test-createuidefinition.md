---
title: Testare la definizione dell'interfaccia utente per le Applicazioni gestite di Azure | Microsoft Docs
description: Descrizione di come testare l'esperienza utente per la creazione di applicazione gestita di Azure tramite il portale.
author: tfitzmac
ms.service: managed-applications
ms.topic: conceptual
ms.date: 05/26/2019
ms.author: tomfitz
ms.openlocfilehash: 99ca319910be2cb20214172826eb40361abe72f0
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257636"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Testare l'interfaccia del portale per le applicazioni gestite di Azure

Dopo aver [creando il file createuidefinition. JSON](create-uidefinition-overview.md) per l'applicazione gestita, è necessario testare l'esperienza dell'utente. Per semplificare il test, usare un ambiente sandbox che carica il file nel portale. Non è necessario distribuire l'applicazione gestita. La sandbox viene visualizzata l'interfaccia utente nell'esperienza del portale corrente e a schermo intero. In alternativa, è possibile usare uno script di PowerShell per il test dell'interfaccia, ma usa una vista precedente del portale. Entrambi gli approcci sono illustrati in questo argomento. La sandbox è il modo consigliato per visualizzare l'anteprima dell'interfaccia.

## <a name="prerequisites"></a>Prerequisiti

* Un file **createUiDefinition.json**. Se non si dispone di questo file, copiare il [file di esempio](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json).

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="use-sandbox"></a>Usare sandbox

1. Aprire il [Sandbox di definizione dell'interfaccia utente di creare](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade).

   ![Sandbox Show](./media/test-createuidefinition/show-sandbox.png)

1. Sostituire la definizione vuota con il contenuto del file createuidefinition. JSON. Selezionare **Preview**.

   ![Selezionare l'anteprima](./media/test-createuidefinition/select-preview.png)

1. Viene visualizzato il modulo che è stato creato. È possibile esaminare l'esperienza utente e immettere i valori.

   ![Visualizzazione del form](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>risoluzione dei problemi

Se il modulo non viene visualizzato dopo aver selezionato **Preview**, potrebbe essere un errore di sintassi. Cercare l'indicatore rosso nella barra di scorrimento a destra e spostarsi su di esso.

![Mostra errore di sintassi](./media/test-createuidefinition/show-syntax-error.png)

Se non viene visualizzato il form e invece viene visualizzata un'icona di un cloud con il rilascio di disinstallazione, il form contenga un errore, ad esempio una proprietà mancante. Aprire gli strumenti di sviluppo Web nel browser. La **Console** consente di visualizzare i messaggi importanti relativi all'interfaccia.

![Mostrare l'errore](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Usare lo script di test

Per testare l'interfaccia nel portale, copiare uno dei seguenti script nel computer locale:

* [Script di PowerShell sideload](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Script dell'interfaccia della riga di comando di Azure sideload](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

Per visualizzare il file di interfaccia nel portale, eseguire lo script scaricato. Lo script crea un account di archiviazione nella sottoscrizione di Azure e carica il file createuidefinition.json nell'account di archiviazione. L'account di archiviazione viene creato la prima volta che si esegue lo script o se è stato eliminato. Se l'account di archiviazione esiste già nella sottoscrizione di Azure, lo script lo riutilizza. Lo script apre il portale e carica il file dall'account di archiviazione.

Specificare un percorso per l'account di archiviazione e specificare la cartella che contiene il file createuidefinition.json.

Per PowerShell, usare:

```powershell
.\SideLoad-CreateUIDefinition.ps1 `
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
.\SideLoad-CreateUIDefinition.ps1
```

Per l'interfaccia della riga di comando di Azure usare:

```azurecli
./sideload-createuidef.sh
```

Lo script apre una nuova scheda nel browser. Viene visualizzato il portale con l'interfaccia per la creazione dell'applicazione gestita.

![Visualizzare il portale](./media/test-createuidefinition/view-portal.png)

Specificare i valori per il campo. Al termine, vengono visualizzati i valori trasmessi al modello.

![Mostrare i valori](./media/test-createuidefinition/show-json.png)

È possibile usare questi valori come il file dei parametri per eseguire il test del modello di distribuzione.

Se il portale si blocca nella schermata di riepilogo, potrebbe esserci un bug nella sezione di output. Ad esempio, è stato fatto riferimento a un controllo che non esiste. Se un parametro di output è vuoto, il parametro potrebbe essere che fanno riferimento a una proprietà che non esiste. Ad esempio, il riferimento al controllo è valido ma il riferimento alla proprietà non è valido.

## <a name="test-your-solution-files"></a>Testare il file della soluzione

Ora che si è verificato che l'interfaccia del portale funziona come previsto, è possibile verificare che il file createUiDefinition sia correttamente integrato con il file maintemplate.json. È possibile eseguire uno script di convalida dei test per testare il contenuto dei file di soluzione, incluso il file createUiDefinition. Lo script convalida la sintassi JSON, verifica la presenza di espressioni regex nei campi di testo e garantisce che i valori di output dell'interfaccia del portale corrispondono ai parametri del modello. Per informazioni sull'esecuzione di questo script, vedere [Eseguire i controlli di convalida statica per i modelli](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver verificato l'interfaccia del portale, leggere altre informazioni su come rendere disponibili le [Applicazioni gestite di Azure nel marketplace](publish-marketplace-app.md).
