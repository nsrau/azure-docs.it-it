---
title: Integrare i log da Key Vault tramite hub eventi - Azure| Microsoft Docs
description: Esercitazione che illustra la procedura necessaria per rendere i log di Key Vault disponibili per un SIEM tramite l'integrazione dei log di Azure
services: security
author: barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.topic: article
ms.date: 06/29/2017
ms.author: Barclayn
ms.custom: AzLog
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 9d1e5505ad6da7e24d51d2301f806695e0d9e2c2
ms.contentlocale: it-it
ms.lasthandoff: 06/30/2017



---

# <a name="azure-log-integration-tutorial---processing-azure-key-vault-events-using-event-hubs"></a>Esercitazione sull'integrazione dei log di Azure - Elaborazione degli eventi di Azure Key Vault tramite gli hub eventi

Integrazione dei log di Azure (AzLog) consente di recuperare gli eventi registrati e di renderli disponibili per le informazioni di sicurezza e la gestione degli eventi (SIEM). Questa esercitazione illustra il processo per estrarre l'attività di Key Vault registrata in un hub eventi e renderla disponibile come file JSON in SIEM. È quindi possibile configurare il SIEM per elaborare i file JSON.

>[!NOTE]
La maggior parte dei passaggi coinvolti in questa esercitazione implica la configurazione di Key Vault, degli account di archiviazione e degli hub eventi. La procedura di integrazione dei log di Azure specifica si trova alla fine del presente documento.

Le informazioni fornite lungo il percorso aiutano a comprendere i motivi presenti dietro ad ogni passaggio e, quando opportuno, verranno inclusi collegamenti appropriati ad altri articoli per offrire altri dettagli su determinati argomenti.

Si noti che la maggior parte di questi passaggi implica la configurazione dell'hub eventi e di Key Vault.  

- [Insieme di credenziali chiave Azure](../key-vault/key-vault-whatis.md)
- [Hub eventi](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Integrazione dei log di Azure](security-azure-log-integration-overview.md)


## <a name="initial-setup"></a>Configurazione iniziale

Per poter completare la procedura descritta in questo articolo, è necessario quanto segue:

1. Una sottoscrizione di Azure e l'account per tale sottoscrizione con diritti di amministratore. Se non si dispone di una sottoscrizione, è possibile ottenere una [sottoscrizione gratuita](https://azure.microsoft.com/free/)
2. Un sistema con accesso a Internet che soddisfi i requisiti per l'installazione di Integrazione dei log di Azure. Il sistema può trovarsi su un servizio cloud o essere ospitato in locale.
3. [Integrazione dei log di Azure](https://www.microsoft.com/download/details.aspx?id=53324) installato.
  - Usare il desktop remoto per connettersi al sistema citato nel passaggio 2.
  - Copiarvi il programma di installazione di Integrazione dei Log di Azure. È possibile [scaricare i file di installazione](https://www.microsoft.com/download/details.aspx?id=53324)
  - Avviare il programma di installazione e accettare i termini del Contratto di Licenza.
  - Decidere se si forniranno informazioni di telemetria e selezionare o deselezionare la casella se si preferisce non inviare informazioni sull'utilizzo a Microsoft.
4. Controllare la versione di PowerShell.
   - Se Windows Server 2016 è installato, si dispone almeno della versione di PowerShell 5.0; se si usa qualsiasi altra versione di Windows server è possibile che siano installate versioni precedenti di PowerShell. È possibile controllare la versione digitando ```get-host``` in una finestra di PowerShell.
   - Se PowerShell 5.0 non è installato, è possibile scaricarlo [qui](https://www.microsoft.com/download/details.aspx?id=50395).
   - Dopo aver installato almeno la versione di PowerShell 5.0, è possibile procedere con l'installazione della versione più recente di Azure PowerShell.
    - Aprire una finestra di PowerShell e digitare:
   </br>```Install-Module Azure```, quindi premere il tasto **Invio**. Eseguire l'installazione e al termine continuare con il passaggio successivo. </br>
   Digitare ```Install-Module AzureRM``` e premere il tasto **Invio**. Eseguire la procedura di installazione.

A questo punto si dispone della versione più recente di Azure PowerShell e di Integrazione dei log di Azure installati in un sistema e si è pronti a procedere con i passaggi successivi dell'esercitazione.

>[!NOTE]
- Per la procedura dettagliata e per informazioni sull'installazione di Azure PowerShell, consultare l'articolo intitolato [Install Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0) (Installare Azure PowerShell)
- Per la procedura di installazione dettagliata di Integrazione dei log di Azure, consultare l'articolo [Integrazione dei log di Azure con la registrazione di Diagnostica di Azure e l'inoltro di eventi di Windows](security-azure-log-integration-get-started.md). L'articolo non riguarda solo l'installazione di Integrazione dei log di Azure, ma include anche informazioni generali sull'argomento.

## <a name="creating-supporting-infrastructure-elements"></a>Creazione di elementi dell'infrastruttura di supporto

1. Aprire una finestra di PowerShell con privilegi elevati e passare a **C:\Programmi\Integrazione log di Microsoft Azure**.
2. Il primo passaggio consiste nell'ottenere i cmdlet AzLog importati. È possibile farlo eseguendo lo script LoadAzlogModule.ps1 (notare i caratteri ". \" nel comando seguente). Digitare **.\LoadAzlogModule.ps1** e premere INVIO.
Verrà visualizzata una schermata simile alla figura seguente. </br>

    ![Elenco dei moduli caricati](./media/security-azure-log-integration-keyvault-eventhub/loaded-modules.png)

3. Digitare **Login-AzureRmAccount** e premere INVIO. Verrà avviata una finestra di dialogo di accesso. Inserire le informazioni sulle credenziali per la sottoscrizione che verrà usata per questa esercitazione.

    >[!NOTE]
    Se questa è la prima volta che si accede ad Azure da questo computer, allora si visualizzerà un messaggio su come consentire a Microsoft di raccogliere dati sull'utilizzo di PowerShell. È consigliabile abilitare questa funzione perché verrà usata per migliorare Azure PowerShell.

4. Dopo aver deciso come procedere con il prompt di raccolta dei dati e dopo aver eseguito l'autenticazione si verrà connessi e verranno visualizzate alcune informazioni sullo schermo come mostrato di seguito. Prendere nota delle informazioni di sottoscrizione.

    ![Elenco dei moduli caricati](./media/security-azure-log-integration-keyvault-eventhub/login-azurermaccount.png)
5. È necessario creare alcune variabili per archiviare alcuni valori che verranno usati in un secondo momento.
    - Digitare ciascuna delle righe di PowerShell riportate di seguito e fare clic su **Invio** dopo ciascuna di esse. Prestare attenzione ai commenti accanto a ognuna di esse. Potrebbe essere necessario regolare i valori per adattarli all'ambiente:
        - ```$subscriptionName = ‘Visual Studio Ultimate with MSDN’``` (Il nome della sottoscrizione può essere diverso e potrebbe essere visualizzato come parte dell'output del comando precedente)
        - ```$location = 'West US'``` (Verrà usata questa variabile per passare la posizione in cui si devono creare le risorse. È possibile modificare questa opzione con qualsiasi altra posizione di propria scelta)
        - ```$random = Get-Random```
        - ``` $name = 'azlogtest' + $random``` (Si può indicare qualsiasi nome, ma deve contenere solo numeri e lettere minuscole)
        - ``` $storageName = $name``` (Verrà usata per il nome dell'account di archiviazione)
        - ```$rgname = $name ``` (Verrà usato per il nome del gruppo di risorse)
        - ``` $eventHubNameSpaceName = $name``` (È il nome dello spazio dei nomi dell'hub eventi)
6. Successivamente, è necessario specificare la sottoscrizione che si userà.
    - Digitare ```Select-AzureRmSubscription -SubscriptionName $subscriptionName``` e premere **Invio**
7. Successivamente si creerà un gruppo di risorse.
    - Digitare ```$rg = New-AzureRmResourceGroup -Name $rgname -Location $location``` e premere **Invio**. In questo modo verrà creato un gruppo di risorse. Se a questo punto si digita $rg e si preme **Invio**, verrà visualizzato un output simile alle informazioni indicate di seguito:

        ![Elenco dei moduli caricati](./media/security-azure-log-integration-keyvault-eventhub/create-rg.png)
8. Successivamente, si creerà un account di archiviazione che verrà usato per tenere traccia delle informazioni sullo stato.
    - ```$storage = New-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
9. Quindi, si creerà lo spazio dei nomi dell'hub eventi, necessario per creare un hub eventi.
    - ```$eventHubNameSpace = New-AzureRmEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
10. Successivamente, si otterrà l'ID della regola che verrà usato con il provider di informazioni.
    - ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey' ```
11. Si otterranno ora tutte le possibili posizioni di Azure e si aggiungeranno i nomi a una variabile che può essere usata in un passaggio successivo.
    - ```$locationObjects = Get-AzureRMLocation```
    - ```$locations = @('global') + $locationobjects.location```Se si digita $locations e si preme **Invio**, si visualizzeranno i nomi delle località senza le informazioni aggiuntive restituiti da get-AzureRmLocation.
12. È ora possibile creare un profilo di log di Azure Resource Manager. È possibile ottenere altre informazioni sul profilo dei log di Azure nell'articolo [Panoramica del log attività di Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)
    - ```Add-AzureRmLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```

>[!NOTE]
È possibile ricevere un messaggio di errore quando si tenta di creare un profilo di log. È possibile consultare la documentazione relativa a Get-AzureRmLogProfile e Remove-AzureRmLogProfile. Se si esegue Get-AzureRmLogProfile verranno visualizzate informazioni sul profilo di log. È possibile eliminare il profilo di log esistente digitando ```Remove-AzureRmLogProfile -name 'Log Profile Name' ```


![Errore del profilo di Resource Manager](./media/security-azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="creating-a-key-vault"></a>Creazione di un Key Vault

Innanzitutto si creerà Azure Key Vault digitando:

```$kv = New-AzureRmKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location ```

Successivamente, si configurerà la registrazione per Key Vault.

```Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true ```

## <a name="generating-log-activity"></a>Generazione dei log di attività

 Le richieste devono essere inviate a Key Vault per generare l'attività dei log. Azioni quali la generazione di chiavi, l'archiviazione di segreti o la lettura di segreti da Key Vault creerà le voci dei log.

 - Il passaggio successivo visualizza semplicemente le chiavi di archiviazione attuali.
    - ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
 - La riga successiva genera una nuova **key2**
    - ```New-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
 - È ora possibile visualizzare nuovamente le chiavi e vedere che **key2** contiene un valore diverso.
    - ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
- Infine, l'impostazione e la lettura di un segreto generano voci dei log aggiuntive
    - ```Set-AzureKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)```
    - ```(Get-AzureKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

        ![Segreto restituito](./media/security-azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configuring-azure-log-integration"></a>Configurazione di Integrazione dei log di Azure

Dopo aver configurato tutti gli elementi necessari per la registrazione di Key Vault in un hub eventi, è necessario eseguire una procedura per configurare Integrazione dei log di Azure.

1. ```$storage = Get-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename```
2. ```$eventHubKey = Get-AzureRmEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
3. ```$storagekeys = Get-AzureRmStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
4. ``` $storagekey = $storagekeys[0].Value```

Si eseguirà il comando AzLog per ogni hub eventi:

1.  ```$eventhubs = Get-AzureRmEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
2.  ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

Dopo circa un minuto di esecuzione degli ultimi comandi, verranno visualizzati i file json generati. È possibile confermare monitorando la directory **c:\Utenti\AzLog\EventHubJson**

## <a name="next-steps"></a>Passaggi successivi

- [Domande frequenti su Integrazione dei log di Azure](security-azure-log-integration-faq.md)
- [Introduzione all'integrazione dei log di Azure](security-azure-log-integration-get-started.md)
- [Integrare i log delle risorse di Azure nei sistemi SIEM](security-azure-log-integration-overview.md)

