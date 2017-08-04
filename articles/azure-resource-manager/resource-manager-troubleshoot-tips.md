---
title: Informazioni sugli errori di distribuzione di Azure | Microsoft Docs
description: Viene descritto come reperire le informazioni sugli errori di distribuzione di Azure.
services: azure-resource-manager
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: errore di distribuzione, distribuzione di azure, distribuire in azure
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: b67bb30fa259fa08e37e11afec724c8b8c3eb633
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="understand-azure-deployment-errors"></a>Informazioni sugli errori di distribuzione di Azure
In questo argomento vengono descritti gli errori di distribuzione e le modalità per reperire altre informazioni sull'errore. Per la risoluzione di comuni errori di distribuzione, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="two-types-of-errors"></a>Due tipi di errori
I possibili errori sono di due tipi:

* errori di convalida
* errori di distribuzione

L'immagine seguente mostra il registro attività di una sottoscrizione. Rappresenta due distribuzioni. In una distribuzione il modello non ha superato la convalida (**Convalida**) e la procedura non è stata eseguita. Nell'altra distribuzione il modello ha superato la convalida, ma non ha permesso di creare risorse, si noti **Write Deployments** (Scrivi distribuzioni). 

![mostra codice di errore](./media/resource-manager-troubleshoot-tips/show-activity-log.png)

Gli errori di convalida sono causati da scenari già determinati prima della distribuzione. Tra di essi figurano gli errori di sintassi presenti in un modello oppure il tentativo di distribuire risorse superiori alle quote di sottoscrizione. Gli errori di distribuzione sorgono da condizioni che si verificano durante il processo di distribuzione. Tra essi configurano i tentativi di accedere a una risorsa che viene distribuita in parallelo.

Entrambi i tipi di errore restituiscono un codice necessario per risolvere i problemi della distribuzione. Entrambi i tipi di errori vengono visualizzati nel [log attività](resource-group-audit.md). Gli errori di convalida non sono invece visualizzati nella cronologia della distribuzione, poiché quest'ultima non è mai stata avviata.

## <a name="determine-error-code"></a>Determinare il codice di errore

Per avere informazioni sull'errore è possibile esaminare il messaggio di errore e il codice di errore. L'articolo [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md) elenca le risoluzione in base al codice di errore. L'argomento illustra come usare il portale di Azure per trovare il codice di errore.

### <a name="validation-errors"></a>Errori di convalida

Durante la distribuzione nel portale, viene visualizzato un errore di convalida dopo l'invio dei valori.

![vista dell'errore di convalida nel portale](./media/resource-manager-troubleshoot-tips/validation-error.png)

Selezionare il messaggio per avere altre informazioni. Nella figura seguente viene visualizzato un errore **InvalidTemplateDeployment** e un messaggio che indica che la distribuzione è stata bloccata da un criterio.

![visualizzare le informazioni di convalida](./media/resource-manager-troubleshoot-tips/validation-details.png)

### <a name="deployment-errors"></a>Errori di distribuzione

Quando l'operazione supera la convalida, ma non esegue correttamente la distribuzione, viene visualizzato l'errore nelle notifiche. Selezionare la notifica.

![Notifica di errore](./media/resource-manager-troubleshoot-tips/notification.png)

Vengono visualizzati maggiori dettagli sulla distribuzione. Selezionare l'opzione per trovare altre informazioni sull'errore.

![distribuzione non riuscita](./media/resource-manager-troubleshoot-tips/deployment-failed.png)

Viene visualizzato il messaggio di errore e i codici di errore. Si noti che sono presenti due codici di errore. Il primo codice di errore, **DeploymentFailed** è un errore generale che non contiene i dettagli necessari per risolvere l'errore. Il secondo codice di errore, **StorageAccountNotFound**, contiene i dettagli necessari. 

![dettagli dell'errore](./media/resource-manager-troubleshoot-tips/error-details.png)

## <a name="enable-debug-logging"></a>Abilitazione della registrazione di debug
In alcuni casi sono necessarie maggiori informazioni sulla richiesta e la risposta per individuare la causa dell'errore. Con PowerShell o l'interfaccia della riga di comando di Azure è possibile richiedere la registrazione di informazioni aggiuntive durante la distribuzione.

- PowerShell

   In PowerShell impostare il parametro **DeploymentDebugLogLevel** su All, ResponseContent o RequestContent.

  ```powershell
  New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile c:\Azure\Templates\storage.json -DeploymentDebugLogLevel All
  ```

   Esaminare il contenuto della richiesta con il cmdlet seguente:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.request | ConvertTo-Json
  ```

   In alternativa, esaminare il contenuto della risposta con:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.response | ConvertTo-Json
  ```

   Queste informazioni consentono di stabilire se nel modello sia impostato un valore errato.

- Interfaccia della riga di comando di Azure

   Esaminare le operazioni di distribuzione con il comando seguente:

  ```azurecli
  az group deployment operation list --resource-group ExampleGroup --name vmlinux
  ```

- Modello annidato

   Per registrare le informazioni di debug relative a un modello nidificato, usare l'elemento **debugSetting**.

  ```json
  {
      "apiVersion": "2016-09-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri": "{template-uri}",
              "contentVersion": "1.0.0.0"
          },
          "debugSetting": {
             "detailLevel": "requestContent, responseContent"
          }
      }
  }
  ```


## <a name="create-a-troubleshooting-template"></a>Creare un modello per la risoluzione dei problemi
Talvolta il modo più semplice per risolvere i problemi del modello è testarne alcune parti. A tale scopo, è possibile creare un modello semplificato che consente di concentrarsi sulla parte da cui si ritiene abbia origine l'errore. Si supponga, ad esempio, di ricevere un errore quando si fa riferimento a una risorsa. Anziché gestire un intero modello, crearne uno che restituisca la parte potenzialmente problematica. Ciò può aiutare a capire se i parametri trasmessi siano corretti, a usare correttamente le funzioni del modello e a recuperare le risorse desiderate.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
        "type": "string"
    },
    "storageResourceGroup": {
        "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
        "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
        "type" : "object"
    }
  }
}
```

In alternativa, si supponga di incontrare errori di distribuzione presumibilmente correlati a dipendenze impostate in modo errato. Testare il modello suddividendolo in modelli semplificati. Creare innanzitutto un modello che distribuisce una sola risorsa (ad esempio SQL Server). Quando si è certi che la risorsa sia definita correttamente, aggiungere una risorsa che dipende da essa (ad esempio un database SQL). Una volta definite correttamente queste due risorse, aggiungere altre risorse che dipendono da esse (ad esempio criteri di controllo). Tra una distribuzione di test e l'altra, eliminare il gruppo di risorse per assicurarsi di testare le dipendenze in modo adeguato. 

## <a name="check-deployment-sequence"></a>Controllare la sequenza di distribuzione

Molti errori di distribuzione si verificano quando le risorse vengono distribuite secondo una sequenza imprevista. Questi errori vengono generati quando le dipendenze non sono impostate correttamente. Quando manca una dipendenza necessaria, una risorsa tenta di usare un valore per un'altra risorsa che tuttavia non esiste ancora e viene visualizzato un errore che indica che non è stata trovata una risorsa. Questo tipo di errore può verificarsi in modo intermittente perché i tempi di distribuzione delle singole risorse possono variare. È ad esempio possibile che il primo tentativo di distribuire le risorse abbia esito positivo perché una risorsa obbligatoria viene casualmente completata in tempo, ma che ciò non avvenga al secondo tentativo e che questo abbia quindi esito negativo. 

È tuttavia opportuno evitare di impostare dipendenze non necessarie. La presenza di dipendenze non necessarie prolunga la durata della distribuzione impedendo di distribuire in parallelo risorse che non sono interdipendenti. Si potrebbero anche creare dipendenze circolari che bloccano la distribuzione. La funzione [reference](resource-group-template-functions-resource.md#reference) crea una dipendenza implicita nella risorsa specificata, se tale risorsa viene distribuita nello stesso modello. Si potrebbero quindi avere dipendenze aggiuntive rispetto a quelle specificate nella proprietà **dependsOn**. La funzione [resourceId](resource-group-template-functions-resource.md#resourceid) non crea una dipendenza implicita né esegue la convalida dell'esistenza della risorsa.

In caso di problemi relativi alle dipendenze, è necessario approfondire l'ordine di distribuzione delle risorse. Per visualizzare l'ordine delle operazioni di distribuzione:

1. Selezionare la cronologia delle distribuzioni per il gruppo di risorse.

   ![selezionare la cronologia delle distribuzioni](./media/resource-manager-troubleshoot-tips/select-deployment.png)

2. Selezionare una distribuzione dalla cronologia e fare clic su **Eventi**.

   ![selezionare gli eventi di distribuzione](./media/resource-manager-troubleshoot-tips/select-deployment-events.png)

3. Esaminare la sequenza degli eventi per ogni risorsa. Prestare attenzione allo stato di ciascuna operazione. Ad esempio, l'immagine seguente mostra tre account di archiviazione distribuiti in parallelo. Si noti che i tre account di archiviazione vengono avviati contemporaneamente.

   ![distribuzione parallela](./media/resource-manager-troubleshoot-tips/deployment-events-parallel.png)

   L'immagine successiva mostra tre account di archiviazione che non vengono distribuiti in parallelo. Il secondo account di archiviazione dipende dal primo e il terzo account di archiviazione dipende dal secondo. Pertanto, il primo account di archiviazione viene avviato, accettato e completato prima che sia avviato il successivo.

   ![distribuzione sequenziale](./media/resource-manager-troubleshoot-tips/deployment-events-sequence.png)

Persino per scenari più complessi è possibile usare la stessa tecnica per individuare quando è stata avviata e completata la distribuzione per ogni risorsa. Esaminare gli eventi di distribuzione per verificare se la sequenza è diversa dal previsto. In tal caso, valutare nuovamente le dipendenze per la risorsa.

Resource Manager identifica le dipendenze circolari durante la convalida del modello. Restituisce quindi un messaggio di errore che indica specificamente la presenza di una dipendenza circolare. Per risolvere una dipendenza circolare:

1. Nel modello trovare la risorsa identificata nella dipendenza circolare. 
2. Esaminare la proprietà **dependsOn** e le occorrenze della funzione **reference** per tale risorsa per verificare le risorse da cui dipende. 
3. Esaminare tali risorse per verificare da quali risorse dipendono. Seguire le dipendenze finché non si rileva una risorsa che dipende dalla risorsa originale.
5. Per le risorse coinvolte nella dipendenza circolare, esaminare attentamente tutte le occorrenze della proprietà **dependsOn** per identificare eventuali dipendenze non necessarie. Rimuovere tali dipendenze. Se non si è certi che una dipendenza sia necessaria, provare a rimuoverla. 
6. Ridistribuire il modello.

La rimozione di valori dalla proprietà **dependsOn** può causare errori durante la distribuzione del modello. Se si verifica un errore, riaggiungere la dipendenza al modello. 

Se con questo approccio non si rivolve la dipendenza circolare, provare a spostare parte della logica di distribuzione in risorse figlio, come estensioni o impostazioni di configurazione. Configurare tali risorse figlio in modo che vengano distribuite dopo le risorse coinvolte nella dipendenza circolare. Si supponga, ad esempio, di distribuire due macchine virtuali e che sia necessario impostare in ognuna proprietà che fanno riferimento all'altra. È possibile eseguire la distribuzione nell'ordine seguente:

1. VM 1
2. VM 2
3. L'estensione in VM 1 dipende da VM 1 e VM 2. L'estensione imposta in VM 1 valori ottenuti da VM 2.
4. L'estensione in VM 2 dipende da VM 1 e VM 2. L'estensione imposta in VM 2 valori ottenuti da VM 1.

Lo stesso approccio è applicabile alle app del servizio app. Provare a spostare i valori di configurazione in una risorsa figlio della risorsa app. È possibile distribuire due app Web nell'ordine seguente:

1. App Web 1
2. App Web 2
3. La configurazione per App Web 1 dipende da App Web 1 e App Web 2. Contiene impostazioni dell'app con valori di App Web 2.
4. La configurazione per App Web 2 dipende da App Web 1 e App Web 2. Contiene impostazioni dell'app con valori di App Web 1.


## <a name="next-steps"></a>Passaggi successivi
* Per la risoluzione di comuni errori di distribuzione, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Per altre informazioni sulle azioni di controllo, vedere [Operazioni di controllo con Resource Manager](resource-group-audit.md).
* Per altre informazioni sulle azioni che consentono di determinare gli errori di distribuzione, vedere [Visualizzare le operazioni di distribuzione con il portale di Azure](resource-manager-deployment-operations.md).

