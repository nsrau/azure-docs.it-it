---
title: Testare la definizione dell'interfaccia utente per le Applicazioni gestite di Azure | Microsoft Docs
description: Descrizione di come testare l'esperienza utente per la creazione di applicazione gestita di Azure tramite il portale.
services: managed-applications
documentationcenter: na
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/10/2018
ms.author: tomfitz
ms.openlocfilehash: 2c313538e297c5781b48fcfe9d0d5390f94c97f5
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043549"
---
# <a name="test-azure-portal-interface-for-your-managed-application"></a>Testare l'interfaccia del portale di Azure per l'applicazione gestita
Dopo la [Creazione del file createuidefinition.json](create-uidefinition-overview.md) per l'applicazione gestita di Azure, è necessario testare l'esperienza dell'utente. Per semplificare il test, usare uno script che consente di caricare il file nel portale. Non è necessario distribuire l'applicazione gestita.

## <a name="prerequisites"></a>Prerequisiti

* Un file **createUiDefinition.json**. Se non si dispone di questo file, copiare il [file di esempio](https://github.com/Azure/azure-quickstart-templates/blob/master/test/template-validation-tests/sample-template/createUIDefinition.json) e salvarlo in locale.

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="download-test-script"></a>Scaricare gli script di test

Per testare l'interfaccia nel portale, copiare uno dei seguenti script nel computer locale:

* [Script di PowerShell sideload](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Script dell'interfaccia della riga di comando di Azure sideload](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

## <a name="run-script"></a>Eseguire lo script

Per visualizzare il file di interfaccia nel portale, eseguire lo script scaricato. Lo script crea un account di archiviazione nella sottoscrizione di Azure e carica il file createuidefinition.json nell'account di archiviazione. Quindi, viene aperto il portale e caricato il file dall'account di archiviazione.

Specificare un percorso per l'account di archiviazione e specificare la cartella che contiene il file createuidefinition.json. La prima volta che viene eseguito lo script o se l'account di archiviazione è stato eliminato, è sufficiente fornire il percorso dell'account di archiviazione.

Per PowerShell, usare:

```powershell
.\SideLoad-CreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory <path-to-folder-with-createuidefinition>
```

Per l'interfaccia della riga di comando di Azure usare:

```azurecli
./sideload-createuidef.sh \
  -l southcentralus \
  -a <path-to-folder-with-createuidefinition>
```

## <a name="test-your-interface"></a>Testare l'interfaccia

Lo script apre una nuova scheda nel browser. Viene visualizzato il portale con l'interfaccia per la creazione dell'applicazione gestita.

![Visualizzare il portale](./media/test-createuidefinition/view-portal.png)

Prima di compilare i campi, aprire gli Strumenti di sviluppo Web nel browser. La **Console** consente di visualizzare i messaggi importanti relativi all'interfaccia.

![Selezionare la console](./media/test-createuidefinition/select-console.png)

Se la definizione dell'interfaccia presenta un errore, la console ne mostra la descrizione.

![Mostrare l'errore](./media/test-createuidefinition/show-error.png)

Specificare i valori per il campo. Al termine, vengono visualizzati i valori trasmessi al modello.

![Mostrare i valori](./media/test-createuidefinition/show-json.png)

## <a name="test-your-solution-files"></a>Testare il file della soluzione

Ora che si è verificato che l'interfaccia del portale funziona come previsto, è possibile verificare che il file createUiDefinition sia correttamente integrato con il file maintemplate.json. È possibile eseguire uno script di convalida dei test per testare il contenuto dei file di soluzione, incluso il file createUiDefinition. Lo script convalida la sintassi JSON, verifica la presenza di espressioni regex nei campi di testo e garantisce che i valori di output dell'interfaccia del portale corrispondono ai parametri del modello. Per informazioni sull'esecuzione di questo script, vedere [Eseguire i controlli di convalida statica per i modelli](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver verificato l'interfaccia del portale, leggere altre informazioni su come rendere disponibili le [Applicazioni gestite di Azure nel marketplace](publish-marketplace-app.md).
