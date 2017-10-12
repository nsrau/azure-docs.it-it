---
title: Risolvere i problemi di Data factory di Azure
description: Informazioni su come risolvere i problemi relativi all'uso di Data factory di Azure.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 38fd14c1-5bb7-4eef-a9f5-b289ff9a6942
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: de40d4afce48e94df0f69c5edca46838268a5e29
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-data-factory-issues"></a>Risolvere i problemi di Data factory
Questo articolo contiene suggerimenti per la risoluzione dei problemi correlati all'uso di Azure Data Factory. L'articolo non elenca tutti i problemi che si possono verificare usando il servizio, ma suggerisce come individuare e risolvere alcuni di essi.   

## <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi
### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Errore: La sottoscrizione non è registrata per l'uso dello spazio dei nomi 'Microsoft.DataFactory'
Se viene visualizzato questo errore, il provider di risorse di Azure Data Factory non è stato registrato nel computer in uso. Eseguire le operazioni seguenti:

1. Avviare Azure PowerShell.
2. Accedere al proprio account di Azure usando il comando seguente.

    ```powershell
    Login-AzureRmAccount
    ```
3. Eseguire il comando seguente per registrare il provider di Azure Data Factory.

    ```powershell        
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Problema: errore di mancata autorizzazione quando si esegue un cmdlet di Data factory
È probabile che non si stia usando l'account o la sottoscrizione di Azure corretta con Azure PowerShell. Usare i cmdlet seguenti per selezionare l'account e la sottoscrizione di Azure corretti per Azure PowerShell.

1. Login-AzureRmAccount: usare l'ID utente e la password corretti
2. Get-AzureRmSubscription: visualizza tutte le sottoscrizioni per l'account.
3. Select-AzureRmSubscription &lt;nome della sottoscrizione&gt;: selezionare la sottoscrizione corretta. Usare la stessa sottoscrizione selezionata per creare un Data Factory nel portale di Azure.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Problema: impossibile avviare l'installazione rapida del Gateway di gestione dati dal portale di Azure
L'installazione rapida del Gateway di gestione dati richiede Internet Explorer o un Web browser compatibile con Microsoft ClickOnce. Se non è possibile avviare l'installazione rapida, eseguire una di queste operazioni:

* Usare Internet Explorer o un Web browser compatibile con Microsoft ClickOnce.

    Se si usa Chrome, accedere al [Chrome Web Store](https://chrome.google.com/webstore/), eseguire una ricerca con la parola chiave "ClickOnce", scegliere una delle estensioni ClickOnce e installarla.

    Seguire la stessa procedura per Firefox (installazione di un componente aggiuntivo). Fare clic sul pulsante Apri menu sulla barra degli strumenti (tre righe orizzontali nell'angolo superiore destro), fare clic su Componenti aggiuntivi, eseguire una ricerca con la parola chiave "ClickOnce", scegliere una delle estensioni ClickOnce e installarla.
* Usare il collegamento all' **installazione manuale** visualizzato sullo stesso pannello nel portale. Adottare questo approccio per scaricare il file di installazione ed eseguirlo manualmente. Al termine dell'installazione viene visualizzata la finestra di configurazione di Gateway di gestione dati. Copiare la **chiave** dalla schermata del portale e usarla in Gestione configurazione per registrare manualmente il gateway con il servizio.  

### <a name="problem-fail-to-connect-to-on-premises-sql-server"></a>Problema: impossibile connettersi all'istanza di SQL Server locale
Avviare **Gestione configurazione di Gateway di gestione dati** nel computer gateway e usare la scheda **Risoluzione dei problemi** per testare la connessione a SQL Server dal computer gateway. Per suggerimenti sulla risoluzione di problemi correlati alla connessione o al gateway, vedere [Risoluzione dei problemi del gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .   

### <a name="problem-input-slices-are-in-waiting-state-for-ever"></a>Problema: le sezioni di input rimangono nello stato Waiting
Le sezioni potrebbero essere **In attesa** per diversi motivi. Uno dei motivi più comuni è che la proprietà **external** non è impostata su **true**. Eventuali set di dati prodotti all'esterno dell'ambito di Azure Data Factory devono essere contrassegnati con la proprietà **external** . Questa proprietà indica che i dati sono esterni e non sono supportati da alcuna pipeline nel Data Factory. Le sezioni di dati vengono contrassegnate con **Pronto** quando i dati sono disponibili nel rispettivo archivio.

Per l'uso della proprietà **external** , vedere l'esempio seguente. È possibile specificare facoltativamente **externalData*** quando si imposta la proprietà external su true.

Per altre informazioni su questa proprietà, vedere [Set di dati in Azure Data Factory](data-factory-create-datasets.md) .

```json
{
  "name": "CustomerTable",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "MyLinkedService",
    "typeProperties": {
      "folderPath": "MyContainer/MySubFolder/",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      }
    }
  }
}
```

Per risolvere l'errore, aggiungere la proprietà **external** e la sezione **externalData** facoltativa alla definizione JSON della tabella di input e ricreare la tabella.

### <a name="problem-hybrid-copy-operation-fails"></a>Problema: l'operazione di copia ibrida non riesce
Per le procedure di risoluzione dei problemi relativi alla copia da e verso un archivio dati locale usando il gateway di gestione dati, vedere [Risoluzione dei problemi del gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues).

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Problema: non è possibile eseguire il provisioning di HDInsight su richiesta
Quando si usa un servizio collegato di tipo HDInsightOnDemand, è necessario specificare un linkedServiceName che punta a un archivio BLOB di Azure. Il servizio Data Factory usa questa risorsa di archiviazione per archiviare Questo account di archiviazione verrà usato per copiare i log e i file di supporto per il cluster HDInsight su richiesta.  In alcuni casi il provisioning di un cluster HDInsight su richiesta ha esito negativo con l'errore seguente:

```
Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.
```

Questo errore di solito indica che il percorso dell'account di archiviazione specificato nel linkedServiceName non si trova nella stessa posizione del data center in cui viene eseguito il provisioning di HDInsight. Esempio: se il Data Factory si trova negli Stati Uniti occidentali e l'archiviazione di Azure è negli Stati Uniti orientali, il provisioning su richiesta ha esito negativo negli Stati Uniti occidentali.

È anche disponibile una seconda proprietà JSON additionalLinkedServiceNames in cui è possibile specificare account di archiviazione aggiuntivi in HDInsight su richiesta. Gli account di archiviazione aggiuntivi collegati devono trovarsi nello stesso percorso del cluster HDInsight oppure avranno esito negativo, producendo lo stesso errore.

### <a name="problem-custom-net-activity-fails"></a>Problema: l'attività .NET personalizzata non riesce
Per una procedura dettagliata, vedere la sezione [Eseguire il debug della pipeline](data-factory-use-custom-activities.md#troubleshoot-failures) .

## <a name="use-azure-portal-to-troubleshoot"></a>Usare il portale di Azure per la risoluzione dei problemi
### <a name="using-portal-blades"></a>Uso dei pannelli del portale
Per una procedura dettagliata, vedere la sezione [Monitorare la pipeline](data-factory-build-your-first-pipeline-using-editor.md#monitor-pipeline) .

### <a name="using-monitor-and-manage-app"></a>Uso dell'app di monitoraggio e gestione
Per informazioni dettagliate, vedere [Monitorare e gestire le pipeline di Azure Data Factory con la nuova app di monitoraggio e gestione](data-factory-monitor-manage-app.md) .

## <a name="use-azure-powershell-to-troubleshoot"></a>Usare Azure PowerShell per la risoluzione dei problemi
### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Usare Azure PowerShell per risolvere un errore
Per informazioni su come monitorare le pipeline di Data Factory con Azure PowerShell, vedere la sezione [Monitorare la pipeline](data-factory-build-your-first-pipeline-using-powershell.md#monitor-pipeline) .

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
