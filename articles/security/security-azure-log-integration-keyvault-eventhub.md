---
title: Integrare i log da Azure Key Vault tramite Hub eventi | Microsoft Docs
description: Esercitazione che illustra la procedura necessaria per rendere i log di Key Vault disponibili per un SIEM tramite l'integrazione dei log di Azure
services: security
author: barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.topic: article
ms.date: 08/07/2017
ms.author: Barclayn
ms.custom: AzLog
ms.openlocfilehash: 3cd80817bf8b2ef2f66e9942eddc186a3eb5b5e4
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Esercitazione sull'integrazione dei log di Azure: elaborazione degli eventi di Azure Key Vault tramite Hub eventi

È possibili usare l'integrazione dei log di Azure per recuperare gli eventi registrati e renderli disponibili per il sistema di gestione delle informazioni e degli eventi di sicurezza (SIEM). Questa esercitazione illustra un esempio di come usare l'integrazione dei log di Azure per elaborare i log acquisiti tramite 	Hub eventi di Azure.
 
Usare questa esercitazione per acquisire familiarità con l'interazione tra l'integrazione dei log di Azure e Hub eventi seguendo la procedura di esempio e comprendendo in che modo ogni passaggio supporta la soluzione. È quindi possibile applicare quanto appreso qui per creare i passaggi personalizzati per supportare i requisiti specifici della propria azienda.

>[!WARNING]
Non copiare o incollare i passaggi e i comandi in questa esercitazione. Si tratta solo di esempi. Non usare i comandi di PowerShell "così come sono" nell'ambiente live, in quanto devono essere personalizzati in base all'ambiente univoco.


Questa esercitazione illustra il processo per estrarre l'attività di Azure Key Vault registrata in un hub eventi e renderla disponibile come file JSON nel sistema SIEM. È quindi possibile configurare il sistema SIEM per elaborare i file JSON.

>[!NOTE]
>La maggior parte dei passaggi in questa esercitazione implica la configurazione dell'insieme di credenziali delle chiavi, degli account di archiviazione e degli hub eventi. La procedura di integrazione dei log di Azure specifica si trova alla fine di questa esercitazione. Non eseguire questi passaggi in un ambiente di produzione. Sono concepiti solo per un ambiente lab. È necessario personalizzare la procedura prima di usarla nell'ambiente di produzione.

Le informazioni offerte consentono di comprendere i motivi di ogni passaggio. I collegamenti ad altri articoli offrono maggiori dettagli su determinati argomenti.

Per altre informazioni sui servizi citati in questa esercitazione, vedere: 

- [Insieme di credenziali chiave Azure](../key-vault/key-vault-whatis.md)
- [Hub eventi di Azure](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Integrazione dei log di Azure](security-azure-log-integration-overview.md)


## <a name="initial-setup"></a>Configurazione iniziale

Per poter completare la procedura descritta in questo articolo, è necessario quanto segue:

1. Una sottoscrizione di Azure e l'account per tale sottoscrizione con diritti di amministratore. Se non si ha una sottoscrizione, è possibile creare un [account gratuito](https://azure.microsoft.com/free/).
 
2. Un sistema con accesso a Internet che soddisfi i requisiti per l'installazione del servizio di integrazione dei log di Azure. Il sistema può trovarsi su un servizio cloud o essere ospitato in locale.

3. [Integrazione dei log di Azure](https://www.microsoft.com/download/details.aspx?id=53324) installato. Per l'installazione:

   a. Usare il desktop remoto per connettersi al sistema citato nel passaggio 2.   
   b. Copiarvi il programma di installazione di Integrazione dei Log di Azure nel sistema. È possibile [scaricare i file di installazione](https://www.microsoft.com/download/details.aspx?id=53324).   
   c. Avviare il programma di installazione e accettare le Condizioni di licenza software Microsoft.   
   d. Se si intende inserire informazioni di telemetria, lasciare selezionata la casella di controllo. Se invece si preferisce non inviare le informazioni sull'uso a Microsoft, deselezionare la casella di controllo.
   
   Per altre informazioni sul servizio di integrazione dei log di Azure e su come installarlo, vedere [Integrazione dei log di Azure con la registrazione di Diagnostica di Azure e l'inoltro di eventi di Windows](security-azure-log-integration-get-started.md).

4. Versione più recente di PowerShell.
 
   Se è installato Windows Server 2016 e la versione disponibile di PowerShell è almeno la versione 5.0. Se si usa qualsiasi altra versione di Windows Server, l'utente potrebbe aver installato una versione precedente di PowerShell. È possibile controllare la versione immettendo ```get-host``` in una finestra di PowerShell. Se PowerShell 5.0 non è installato, è possibile [scaricarlo](https://www.microsoft.com/download/details.aspx?id=50395).

   Dopo aver installato almeno la versione di PowerShell 5.0, è possibile procedere con l'installazione della versione più recente:
   
   a. In una finestra di PowerShell immettere il comando ```Install-Module Azure```. Completare la procedura d'installazione.    
   b. Immettere il comando ```Install-Module AzureRM```. Completare la procedura d'installazione.

   Per altre informazioni, vedere [Installare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0).


## <a name="create-supporting-infrastructure-elements"></a>Creare elementi dell'infrastruttura di supporto

1. Aprire una finestra di PowerShell con privilegi elevati e passare a **C:\Programmi\Integrazione log di Microsoft Azure**.
2. Importare i cmdlet AzLog eseguendo lo script LoadAzLogModule.ps1. Immettere il comando `.\LoadAzLogModule.ps1`. Si noti l'uso di ".\" in questo comando. Verrà visualizzata una schermata analoga alla seguente:</br>

   ![Elenco dei moduli caricati](./media/security-azure-log-integration-keyvault-eventhub/loaded-modules.png)

3. Immettere il comando `Login-AzureRmAccount`. Nella finestra di accesso inserire le informazioni sulle credenziali per la sottoscrizione che verrà usata per questa esercitazione.

   >[!NOTE]
   >Se questa è la prima volta che si accede ad Azure da questo computer, allora si visualizzerà un messaggio su come consentire a Microsoft di raccogliere dati sull'uso di PowerShell. È consigliabile abilitare la raccolta dati perché verrà usata per migliorare Azure PowerShell.

4. Con la corretta autenticazione si accede e vengono visualizzate le informazioni nella schermata seguente. Annotare l'ID e il nome della sottoscrizione, necessari per completare i passaggi successivi.

   ![Finestra di PowerShell](./media/security-azure-log-integration-keyvault-eventhub/login-azurermaccount.png)
5. Creare variabili per archiviare i valori che verranno usati successivamente. Immettere ognuna delle seguenti righe di PowerShell. Potrebbe essere necessario regolare i valori per adattarli all'ambiente.
    - ```$subscriptionName = ‘Visual Studio Ultimate with MSDN’``` Il nome della sottoscrizione potrebbe essere diverso. È possibile visualizzarlo come parte dell'output del comando precedente.
    - ```$location = 'West US'``` (Verrà usata questa variabile per passare la posizione in cui si devono creare le risorse. È possibile modificare questa variabile con qualsiasi località di propria scelta.
    - ```$random = Get-Random```
    - ``` $name = 'azlogtest' + $random``` Si può indicare qualsiasi nome, ma deve contenere solo numeri e lettere minuscole.
    - ``` $storageName = $name``` Questa variabile verrà usata per il nome dell'account di archiviazione.
    - ```$rgname = $name ``` Questa variabile verrà usata per il nome del gruppo di risorse.
    - ``` $eventHubNameSpaceName = $name``` Nome dello spazio dei nomi dell'hub eventi.
6. Specificare la sottoscrizione che si userà:
    
    ```Select-AzureRmSubscription -SubscriptionName $subscriptionName```
7. Creare un gruppo di risorse:
    
    ```$rg = New-AzureRmResourceGroup -Name $rgname -Location $location```
    
   Se si immette `$rg` a questo punto, verrà visualizzato un output simile a quello della schermata di seguito:

   ![Output dopo la creazione di un gruppo di risorse](./media/security-azure-log-integration-keyvault-eventhub/create-rg.png)
8. Creare un account di archiviazione che verrà usato per tenere traccia delle informazioni sullo stato:
    
    ```$storage = New-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
9. Creare lo spazio dei nomi dell'hub eventi necessario per creare un hub eventi.
    
    ```$eventHubNameSpace = New-AzureRmEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
10. Ottenere l'ID della regola che verrà usato con il provider di informazioni:
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey' ```
11. Ottenere tutte le possibili posizioni di Azure e aggiungere i nomi a una variabile che può essere usata in un passaggio successivo:
    
    a. ```$locationObjects = Get-AzureRMLocation```    
    b. ```$locations = @('global') + $locationobjects.location```
    
    A questo punto, se si immette `$locations`, verranno visualizzati i nomi delle località senza le informazioni aggiuntive restituite da Get-AzureRmLocation.
12. Creare un profilo di log di Azure Resource Manager: 
    
    ```Add-AzureRmLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Per altre informazioni sui profili log di Azure, vedere [Panoramica del log attività di Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

> [!NOTE]
> È possibile ricevere un messaggio di errore quando si tenta di creare un profilo di log. È possibile consultare quindi la documentazione relativa a Get-AzureRmLogProfile e Remove-AzureRmLogProfile. Se si esegue Get-AzureRmLogProfile verranno visualizzate le informazioni sul profilo di log. È possibile eliminare il profilo di log esistente immettendo il comando ```Remove-AzureRmLogProfile -name 'Log Profile Name' ```.
>
>![Errore del profilo di Resource Manager](./media/security-azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

1. Creare l'insieme di credenziali delle chiavi:

   ```$kv = New-AzureRmKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location ```

2. Configurare la registrazione per l'insieme di credenziali delle chiavi:

   ```Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true ```

## <a name="generate-log-activity"></a>Generare l'attività di log

Le richieste devono essere inviate a Key Vault per generare l'attività dei log. Azioni quali la generazione di chiavi, l'archiviazione di segreti o la lettura di segreti da Key Vault creeranno le voci dei log.

1. Visualizzare le chiavi di archiviazione corrente:
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
2. Generare un nuovo **key2**:
    
   ```New-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
3. Visualizzare nuovamente le chiavi e vedere che **key2** contiene un valore diverso:
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
4. Impostare e leggere un segreto per generare voci di log aggiuntive:
    
   a. ```Set-AzureKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)``` b. ```(Get-AzureKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![Segreto restituito](./media/security-azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Configurare Integrazione dei log di Azure

Dopo aver configurato tutti gli elementi necessari per la registrazione di Key Vault in un hub eventi, è necessario configurare Integrazione dei log di Azure:

1. ```$storage = Get-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename```
2. ```$eventHubKey = Get-AzureRmEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
3. ```$storagekeys = Get-AzureRmStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
4. ``` $storagekey = $storagekeys[0].Value```

Eseguire il comando AzLog per ogni hub eventi:

1. ```$eventhubs = Get-AzureRmEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
2. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

Dopo circa un minuto di esecuzione degli ultimi comandi, verranno visualizzati i file JSON generati. È possibile verificarlo monitorando la directory **C:\Utenti\AzLog\EventHubJson**.

## <a name="next-steps"></a>Passaggi successivi

- [Domande frequenti su Integrazione dei log di Azure](security-azure-log-integration-faq.md)
- [Introduzione all'integrazione dei log di Azure](security-azure-log-integration-get-started.md)
- [Integrare i log delle risorse di Azure nei sistemi SIEM](security-azure-log-integration-overview.md)
