---
title: Integrare i log da Azure Key Vault tramite Hub eventi | Microsoft Docs
description: Esercitazione che illustra la procedura necessaria per rendere i log di Key Vault disponibili per un SIEM tramite l'integrazione dei log di Azure
services: security
author: barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.topic: article
ms.date: 05/28/2019
ms.author: Barclayn
ms.custom: AzLog
ms.openlocfilehash: 985c6eadbc1ef4da4a15825d263b3ebe95de8107
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68615902"
---
# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Esercitazione su Integrazione log di Azure: Elaborare gli eventi di Azure Key Vault tramite Hub eventi

>[!IMPORTANT]
> La funzionalità di integrazione dei log di Azure verrà deprecata da 06/15/2019. Il download di AzLog è stato disabilitato il 27 giugno 2018. Per materiale sussidiario su cosa fare dopo, vedere il post [Use Azure monitor to integrate with SIEM tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) (Usare Monitoraggio di Azure per eseguire l'integrazione con gli strumenti per le informazioni di sicurezza e gestione degli eventi) 

È possibili usare l'integrazione dei log di Azure per recuperare gli eventi registrati e renderli disponibili per il sistema di gestione delle informazioni e degli eventi di sicurezza (SIEM). Questa esercitazione illustra un esempio di come usare l'integrazione dei log di Azure per elaborare i log acquisiti tramite Hub eventi di Azure.

Il metodo preferito per l'integrazione dei log di Azure consiste nell'usare un connettore per Monitoraggio di Azure del fornitore SIEM e seguire queste [istruzioni](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md). Tuttavia, se il fornitore SIEM non offre un connettore per Monitoraggio di Azure, è possibile usare Integrazione log di Azure come soluzione temporanea (se SIEM è supportato da Integrazione log di Azure) fino a quando non sia disponibile un connettore di questo tipo.

 
Usare questa esercitazione per acquisire familiarità con l'interazione tra l'integrazione dei log di Azure e Hub eventi seguendo la procedura di esempio e comprendendo in che modo ogni passaggio supporta la soluzione. È quindi possibile applicare quanto appreso qui per creare i passaggi personalizzati per supportare i requisiti specifici della propria azienda.

> [!WARNING]
> Non copiare o incollare i passaggi e i comandi in questa esercitazione. Si tratta solo di esempi. Non usare i comandi di PowerShell "così come sono" nell'ambiente live. in quanto devono essere personalizzati in base all'ambiente univoco.


Questa esercitazione illustra il processo per estrarre l'attività di Azure Key Vault registrata in un hub eventi e renderla disponibile come file JSON nel sistema SIEM. È quindi possibile configurare il sistema SIEM per elaborare i file JSON.

>[!NOTE]
>La maggior parte dei passaggi in questa esercitazione implica la configurazione dell'insieme di credenziali delle chiavi, degli account di archiviazione e degli hub eventi. La procedura di integrazione dei log di Azure specifica si trova alla fine di questa esercitazione. Non eseguire questi passaggi in un ambiente di produzione. Sono concepiti solo per un ambiente lab. È necessario personalizzare la procedura prima di usarla nell'ambiente di produzione.

Le informazioni offerte consentono di comprendere i motivi di ogni passaggio. I collegamenti ad altri articoli offrono maggiori dettagli su determinati argomenti.

Per altre informazioni sui servizi citati in questa esercitazione, vedere: 

- [Insieme di credenziali chiave Azure](/azure/key-vault/key-vault-whatis)
- [Hub eventi di Azure](/azure/event-hubs/event-hubs-what-is-event-hubs)
- [Integrazione dei log di Azure](azure-log-integration-overview.md)


## <a name="initial-setup"></a>Installazione iniziale

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Per poter completare la procedura descritta in questo articolo, è necessario quanto segue:

* Una sottoscrizione di Azure e l'account per tale sottoscrizione con diritti di amministratore. Se non si ha una sottoscrizione, è possibile creare un [account gratuito](https://azure.microsoft.com/free/).
 
* Un sistema con accesso a Internet che soddisfi i requisiti per l'installazione del servizio di integrazione dei log di Azure. Il sistema può trovarsi su un servizio cloud o essere ospitato in locale.

* Integrazione log di Azure installato. Per l'installazione:

   a. Usare il desktop remoto per connettersi al sistema citato nel passaggio 2.   
   b. Copiarvi il programma di installazione di Integrazione dei Log di Azure nel sistema. c. Avviare il programma di installazione e accettare le Condizioni di licenza software Microsoft.

* Se si intende inserire informazioni di telemetria, lasciare selezionata la casella di controllo. Se invece si preferisce non inviare le informazioni sull'uso a Microsoft, deselezionare la casella di controllo.

   Per altre informazioni sul servizio di integrazione dei log di Azure e su come installarlo, vedere [Integrazione dei log di Azure con la registrazione di Diagnostica di Azure e l'inoltro di eventi di Windows](azure-log-integration-get-started.md).

* Versione più recente di PowerShell.

   Se è installato Windows Server 2016 e la versione disponibile di PowerShell è almeno la versione 5.0. Se si usa qualsiasi altra versione di Windows Server, l'utente potrebbe aver installato una versione precedente di PowerShell. È possibile controllare la versione immettendo ```get-host``` in una finestra di PowerShell. Se PowerShell 5.0 non è installato, è possibile [scaricarlo](https://www.microsoft.com/download/details.aspx?id=50395).

   Dopo aver installato almeno PowerShell 5,0, è possibile continuare a installare la versione più recente seguendo le istruzioni riportate in [install Azure PowerShell](/powershell/azure/install-az-ps).


## <a name="create-supporting-infrastructure-elements"></a>Creare elementi dell'infrastruttura di supporto

1. Aprire una finestra di PowerShell con privilegi elevati e passare a **C:\Programmi\Integrazione log di Microsoft Azure**.
1. Importare i cmdlet AzLog eseguendo lo script LoadAzLogModule.ps1. Immettere il comando `.\LoadAzLogModule.ps1`. (Si noti l'uso di ".\" in questo comando.) Verrà visualizzata una schermata analoga alla seguente:</br>

   ![Elenco dei moduli caricati](./media/azure-log-integration-keyvault-eventhub/loaded-modules.png)

1. Immettere il comando `Connect-AzAccount`. Nella finestra di accesso inserire le informazioni sulle credenziali per la sottoscrizione che verrà usata per questa esercitazione.

   >[!NOTE]
   >Se questa è la prima volta che si accede ad Azure da questo computer, allora si visualizzerà un messaggio su come consentire a Microsoft di raccogliere dati sull'uso di PowerShell. È consigliabile abilitare la raccolta dati perché verrà usata per migliorare Azure PowerShell.

1. Al termine dell'autenticazione, si è connessi. Annotare l'ID e il nome della sottoscrizione, necessari per completare i passaggi successivi.

1. Creare variabili per archiviare i valori che verranno usati successivamente. Immettere ognuna delle seguenti righe di PowerShell. Potrebbe essere necessario regolare i valori per adattarli all'ambiente.
    - ```$subscriptionName = 'Visual Studio Ultimate with MSDN'``` Il nome della sottoscrizione potrebbe essere diverso. È possibile visualizzarlo come parte dell'output del comando precedente.
    - ```$location = 'West US'``` (Verrà usata questa variabile per passare la posizione in cui si devono creare le risorse. È possibile modificare questa variabile con qualsiasi località di propria scelta.
    - ```$random = Get-Random```
    - ```$name = 'azlogtest' + $random``` Si può indicare qualsiasi nome, ma deve contenere solo numeri e lettere minuscole.
    - ```$storageName = $name``` Questa variabile verrà usata per il nome dell'account di archiviazione.
    - ```$rgname = $name``` Questa variabile verrà usata per il nome del gruppo di risorse.
    - ```$eventHubNameSpaceName = $name``` Nome dello spazio dei nomi dell'hub eventi.
1. Specificare la sottoscrizione che si userà:
    
    ```Select-AzSubscription -SubscriptionName $subscriptionName```
1. Creare un gruppo di risorse:
    
    ```$rg = New-AzResourceGroup -Name $rgname -Location $location```
    
   Se si immette `$rg` a questo punto, verrà visualizzato un output simile a quello della schermata di seguito:

   ![Output dopo la creazione di un gruppo di risorse](./media/azure-log-integration-keyvault-eventhub/create-rg.png)
1. Creare un account di archiviazione che verrà usato per tenere traccia delle informazioni sullo stato:
    
    ```$storage = New-AzStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
1. Creare lo spazio dei nomi dell'hub eventi necessario per creare un hub eventi.
    
    ```$eventHubNameSpace = New-AzEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
1. Ottenere l'ID della regola che verrà usato con il provider di informazioni:
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey'```
1. Ottenere tutte le possibili posizioni di Azure e aggiungere i nomi a una variabile che può essere usata in un passaggio successivo:
    
    a. ```$locationObjects = Get-AzLocation```    
    b. ```$locations = @('global') + $locationobjects.location```
    
    Se si immette `$locations` a questo punto, vengono visualizzati i nomi delle località senza le informazioni aggiuntive restituite da Get-AzLocation.
1. Creare un profilo di log di Azure Resource Manager: 
    
    ```Add-AzLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Per altre informazioni sui profili log di Azure, vedere [Panoramica del log attività di Azure](../../azure-monitor/platform/activity-logs-overview.md).

> [!NOTE]
> È possibile ricevere un messaggio di errore quando si tenta di creare un profilo di log. È quindi possibile esaminare la documentazione relativa a Get-AzLogProfile e Remove-AzLogProfile. Se si esegue Get-AzLogProfile, vengono visualizzate informazioni sul profilo di log. È possibile eliminare il profilo di log esistente immettendo il comando ```Remove-AzLogProfile -name 'Log Profile Name'```.
>
>![Errore del profilo di Resource Manager](./media/azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

1. Creare l'insieme di credenziali delle chiavi:

   ```$kv = New-AzKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location```

1. Configurare la registrazione per l'insieme di credenziali delle chiavi:

   ```Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true```

## <a name="generate-log-activity"></a>Generare l'attività di log

Le richieste devono essere inviate a Key Vault per generare l'attività dei log. Azioni quali la generazione di chiavi, l'archiviazione di segreti o la lettura di segreti da Key Vault creeranno le voci dei log.

1. Visualizzare le chiavi di archiviazione corrente:
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Generare un nuovo **key2**:
    
   ```New-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
1. Visualizzare nuovamente le chiavi e vedere che **key2** contiene un valore diverso:
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Impostare e leggere un segreto per generare voci di log aggiuntive:
    
   a. ```Set-AzKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)``` b. ```(Get-AzKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![Segreto restituito](./media/azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Configurare Integrazione dei log di Azure

Dopo aver configurato tutti gli elementi necessari per la registrazione di Key Vault in un hub eventi, è necessario configurare Integrazione dei log di Azure:

1. ```$storage = Get-AzStorageAccount -ResourceGroupName $rgname -Name $storagename```
1. ```$eventHubKey = Get-AzEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
1. ```$storagekeys = Get-AzStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
1. ```$storagekey = $storagekeys[0].Value```

Eseguire il comando AzLog per ogni hub eventi:

1. ```$eventhubs = Get-AzEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
1. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

Dopo circa un minuto di esecuzione degli ultimi comandi, verranno visualizzati i file JSON generati. È possibile verificarlo monitorando la directory **C:\Utenti\AzLog\EventHubJson**.

## <a name="next-steps"></a>Passaggi successivi

- [Domande frequenti su Integrazione dei log di Azure](azure-log-integration-faq.md)
- [Introduzione all'integrazione dei log di Azure](azure-log-integration-get-started.md)
- [Integrare i log delle risorse di Azure nei sistemi SIEM](azure-log-integration-overview.md)
