---
title: Guida pratica sulle notifiche tramite posta elettronica per l'ottimizzazione automatica - Database SQL di Azure | Microsoft Docs
description: Il database SQL di Azure analizza le query SQL e si adatta automaticamente al carico di lavoro dell'utente.
services: sql-database
documentationcenter: 
author: danimir
manager: drasumic
ms.reviewer: carlrab
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 02/05/2018
ms.author: v-daljep
ms.openlocfilehash: a1b10c1a12d9a9215022cc77615901a0e4d144f8
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="email-notifications-for-automatic-tuning"></a>Notifiche tramite posta elettronica per l'ottimizzazione automatica

I suggerimenti di ottimizzazione per i database SQL vengono generati dall'opzione [Ottimizzazione automatica](sql-database-automatic-tuning.md) per database SQL di Azure. Questa soluzione monitorizza e analizza costantemente i carichi di lavoro di database SQL e offre suggerimenti di ottimizzazione personalizzati per ogni database inerenti, ad esempio, alla creazione dell'indice, all'eliminazione dell'indice e all'ottimizzazione dei piani di esecuzione delle query.

I suggerimenti di ottimizzazione automatica per database SQL possono essere visualizzati nel [portale di Azure](sql-database-advisor-portal.md), recuperati con chiamate all'[API REST](https://docs.microsoft.com/en-us/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) oppure usando comandi di [T-SQL](https://azure.microsoft.com/en-us/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) e [ PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabaserecommendedaction). Questo articolo si basa sull'utilizzo di uno script di PowerShell per recuperare suggerimenti di ottimizzazione automatica.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Automatizzare le notifiche tramite posta elettronica per i suggerimenti di ottimizzazione automatica

La soluzione seguente consente di automatizzare l'invio di notifiche tramite posta elettronica contenenti suggerimenti di ottimizzazione automatica. La soluzione descritta consiste nell'esecuzione automatica di uno script di PowerShell per recuperare i suggerimenti di ottimizzazione usando [Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-intro) e automatizzare la pianificazione di un processo di recapito di posta elettronica usando [Microsoft Flow](https://flow.microsoft.com).

## <a name="create-azure-automation-account"></a>Creare un account di Automazione di Azure

Per usare Automazione di Azure, il primo passaggio consiste nel creare un account di automazione e configurarlo con le risorse di Azure da usare per l'esecuzione dello script di PowerShell. Per altre informazioni su Automazione di Azure e le relative funzionalità, vedere [Introduzione ad Automazione di Azure](https://docs.microsoft.com/en-us/azure/automation/automation-offering-get-started).

Seguire questa procedura per creare un account di Automazione di Azure tramite il metodo di selezione e configurazione dell'app Automazione dal Marketplace:

- Accedere al portale di Azure
- Fare clic su "**+ Crea una risorsa**" nell'angolo superiore sinistro
- Cercare "**Automazione**" (premere Invio)
- Fare clic sull'app Automazione nei risultati della ricerca

![Aggiunta di Automazione di Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

- Nel riquadro "Crea un account di Automazione" fare clic su "**Crea**"
- Inserire le informazioni necessarie: immettere un nome per questo account di automazione e selezionare le risorse di Azure e l'ID della sottoscrizione di Azure da usare per l'esecuzione dello script di PowerShell
- Per l'opzione "**Crea un account RunAs di Azure**" selezionare **Sì** per configurare il tipo di account in cui viene eseguito lo script di PowerShell con l'aiuto di Automazione di Azure. Per altre informazioni sui tipi di account, vedere [Account RunAs](https://docs.microsoft.com/en-us/azure/automation/automation-create-runas-account)
- Terminare la creazione dell'account di automazione facendo clic su **Crea**

> [!TIP]
> Annotare il nome dell'account di Automazione di Azure, l'ID della sottoscrizione e le risorse (ad esempio, copiandoli e incollandoli in un blocco note), esattamente come sono stati immessi durante la creazione dell'app Automazione. Queste informazioni saranno necessarie più avanti.
>

Se si hanno più sottoscrizioni di Azure per le quali si vuole creare la stessa automazione, è necessario ripetere questo processo anche per le altre sottoscrizioni.

## <a name="update-azure-automation-modules"></a>Aggiornare i moduli di Automazione di Azure

Lo script di PowerShell per recuperare i suggerimenti di ottimizzazione automatica usa i comandi [Get-AzureRmResource](https://docs.microsoft.com/en-us/powershell/module/AzureRM.Resources/Get-AzureRmResource) e [Get-AzureRmSqlDatabaseRecommendedAction](https://docs.microsoft.com/en-us/powershell/module/AzureRM.Sql/Get-AzureRmSqlDatabaseRecommendedAction) per determinare la necessità di aggiornamento dei moduli di Azure alla versione 4 o successiva.

Seguire questa procedura per aggiornare i moduli di Azure PowerShell:

- Accedere al riquadro dell'app Automazione e selezionare "**Moduli**" nel menu a sinistra (scorrere verso il basso poiché la voce di menu si trova sotto Risorse condivise).
- Nel riquadro Moduli fare clic su "**Aggiorna moduli di Azure**" nella parte superiore e attendere finché non viene visualizzato il messaggio "I moduli di Azure sono stati aggiornati". Il processo potrebbe richiedere alcuni minuti.

![Aggiornare i moduli di Automazione di Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-02.png)

Per i moduli AzureRM.Resources e AzureRM.Sql è necessaria la versione 4 o successiva.

## <a name="create-azure-automation-runbook"></a>Creare un runbook di Automazione di Azure

Il passaggio successivo consiste nella creazione di un runbook in Automazione di Azure all'interno del quale si trova lo script di PowerShell per il recupero dei suggerimenti di ottimizzazione.

Per creare un nuovo runbook di Automazione di Azure, seguire questa procedura:

- Accedere all'account di Automazione di Azure creato nel passaggio precedente
- Nel riquadro dell'account di automazione fare clic sulla voce di menu "**Runbook**" sul lato sinistro per creare un nuovo runbook di Automazione di Azure con lo script di PowerShell. Per altre informazioni sulla creazione di runbook di automazione, vedere [Creazione di un nuovo runbook](../automation/automation-creating-importing-runbook.md).
- Per aggiungere un nuovo runbook, fare clic sull'opzione di menu "**+Aggiungi un runbook**" e quindi fare clic su "**Creazione rapida: Crea un nuovo runbook**".
- Nel riquadro del runbook digitare il nome del runbook (ai fini di questo esempio si userà "**AutomaticTuningEmailAutomation**"), selezionare il tipo di runbook **PowerShell** e scrivere una descrizione di questo runbook per illustrarne lo scopo.
- Fare clic sul pulsante **Crea** per completare la creazione del nuovo runbook

![Aggiungere un runbook di Automazione di Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

Seguire questa procedura per caricare uno script di PowerShell nel runbook creato:

- Nel riquadro "**Modifica runbook di PowerShell**" selezionare"**RUNBOOK**" nella struttura del menu ed espandere la visualizzazione finché non appare il nome del runbook (in questo esempio " **AutomaticTuningEmailAutomation**"). Selezionare questo runbook.
- Nella prima riga di "Modifica runbook di PowerShell" (a partire dal numero 1), copiare e incollare il codice di script di PowerShell seguente. Questo script di PowerShell viene fornito così com'è per iniziare. Modificare lo script in base alle esigenze.

Nell'intestazione dello script di PowerShell fornito è necessario sostituire `<SUBSCRIPTION_ID_WITH_DATABASES>` con l'ID della sottoscrizione di Azure. Per informazioni su come recuperare l'ID della sottoscrizione di Azure, vedere [Getting your Azure Subscription GUID](https://blogs.msdn.microsoft.com/mschray/2016/03/18/getting-your-azure-subscription-guid-new-portal/) (Recupero del codice GUID della sottoscrizione).

In caso di più sottoscrizioni, è possibile aggiungerle come delimitate da virgole alla proprietà "$subscriptions" nell'intestazione dello script.

```PowerShell
# PowerShell script to retrieve Azure SQL Database Automatic tuning recommendations.
#
# Provided “as-is” with no implied warranties or support.
# The script is released to the public domain.
#
# Replace <SUBSCRIPTION_ID_WITH_DATABASES> in the header with your Azure subscription ID.
#
# Microsoft Azure SQL Database team, 2018-01-22.

# Set subscriptions : IMPORTANT – REPLACE <SUBSCRIPTION_ID_WITH_DATABASES> WITH YOUR SUBSCRIPTION ID 
$subscriptions = ("<SUBSCRIPTION_ID_WITH_DATABASES>", "<SECOND_SUBSCRIPTION_ID_WITH_DATABASES>", "<THIRD_SUBSCRIPTION_ID_WITH_DATABASES>")

# Get credentials
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {    
    Select-AzureRmSubscription -SubscriptionId $subscriptionId    
    $rgs = Get-AzureRmResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzureRmResource -ResourceGroupName $rgname -ResourceType $resourceType    

            # Loop through all databases
            # Extract resource groups, servers and databases
            foreach ($resource in $resources) {
                $resourceId = $resource.ResourceId
                if ($resourceId -match ".*RESOURCEGROUPS/(?<content>.*)/PROVIDERS.*") {
                    $ResourceGroupName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*SERVERS/(?<content>.*)/DATABASES.*") {
                    $ServerName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*/DATABASES/(?<content>.*)") {
                    $DatabaseName = $matches['content']
                } else {
                    continue 
                }

                # Skip if master
                if ($DatabaseName -eq "master") {
                    continue
                }

                # Loop through all Automatic tuning recommendation types
                foreach ($advisor in $advisors) {
                    $recs = Get-AzureRmSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
                    foreach ($r in $recs) {
                        if ($r.State.CurrentValue -eq "Active") {
                            $object = New-Object -TypeName PSObject
                            $object | Add-Member -Name 'SubscriptionId' -MemberType Noteproperty -Value $subscriptionId
                            $object | Add-Member -Name 'ResourceGroupName' -MemberType Noteproperty -Value $r.ResourceGroupName
                            $object | Add-Member -Name 'ServerName' -MemberType Noteproperty -Value $r.ServerName
                            $object | Add-Member -Name 'DatabaseName' -MemberType Noteproperty -Value $r.DatabaseName
                            $object | Add-Member -Name 'Script' -MemberType Noteproperty -Value $r.ImplementationDetails.Script
                            $results += $object
                        }
                    }
                }                
            }
        }
    }
}

# Format and output results for the email
$table = $results | Format-List
Write-Output $table
```

Fare clic sul pulsante "**Salva**" nell'angolo superiore destro per salvare lo script. Quando si è soddisfatti dello script, fare clic sul pulsante "**Pubblica**" per pubblicare il runbook. 

Nel riquadro principale del runbook è possibile scegliere di fare clic sul pulsante "**Avvia**" per **testare** lo script. Fare clic su "**Output**" per visualizzare i risultati dello script eseguito. Questo output diventerà il contenuto del messaggio di posta elettronica. L'output dello script di esempio può essere visualizzato nella schermata seguente.

![Eseguire i suggerimenti di ottimizzazione automatica con Automazione di Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

Modificare il contenuto personalizzando lo script di PowerShell in base alle esigenze.

Nel corso della procedura precedente è stato caricato in Automazione di Azure lo script di PowerShell che consente di recuperare i suggerimenti di ottimizzazione automatica. Il passaggio successivo consiste nell'automatizzazione e nella pianificazione del processo di recapito della posta elettronica.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Automatizzare i processi di posta elettronica con Microsoft Flow

Per completare la soluzione, come ultimo passaggio, creare un flusso di automazione in Microsoft Flow composto da tre azioni (processi): 

1. "**Azure Automation - Create job**" (Automazione di Azure - Creare il processo): consente di eseguire lo script di PowerShell per recuperare i suggerimenti di ottimizzazione automatica nel runbook di Automazione di Azure
2. "**Azure Automation - Get job output**" (Automazione di Azure - Ottenere l'output del processo): consente di recuperare l'output dello script di PowerShell eseguito
3. "**Office 365 Outlook – Send an email**" (Office 365 Outlook - Inviare un messaggio di posta elettronica): consente di inviare un messaggio di posta elettronica. I messaggi di posta elettronica vengono inviati usando l'account Office 365 dell'utente che ha creato il flusso.

Per altre informazioni sulle funzionalità di Microsoft Flow, vedere [Attività iniziali con Microsoft Flow](https://docs.microsoft.com/en-us/flow/getting-started).

I prerequisiti per questo passaggio comprendono la sottoscrizione di un account di [Microsoft Flow](https://flow.microsoft.com) e l'accesso. Dopo aver eseguito l'accesso alla soluzione, seguire questa procedura per configurare un **nuovo flusso**:

- Accedere alla voce di menu "**Flussi personali**"
- In Flussi personali selezionare il collegamento "**+Crea da zero**" nella parte superiore della pagina
- Fare clic sul collegamento "**Search for hundreds of connectors and triggers**" (Cerca centinaia di connettori e trigger) nella parte inferiore della pagina
- Nel campo di ricerca digitare "**ricorrenza**" e selezionare "**Pianificazione - Ricorrenza**" nei risultati della ricerca per pianificare l'esecuzione del processo di recapito della posta elettronica.
- Nel campo Frequenza del riquadro Ricorrenza selezionare la frequenza di pianificazione per l'esecuzione del flusso, ad esempio per inviare messaggi di posta elettronica automatici ogni minuto, ora, giorno, settimana e così via.

Il passaggio successivo consiste nell'aggiunta dei tre processi (creazione, acquisizione dell'output e invio del messaggio di posta elettronica) al flusso ricorrente appena creato. Per aggiungere al flusso i tre processi richiesti, seguire questa procedura:

1. Creare l'azione per eseguire lo script di PowerShell per recuperare i suggerimenti di ottimizzazione
- Selezionare "**+Nuovo passaggio**", seguito da "**Aggiungi un'azione**" nel riquadro del flusso Ricorrenza
- Nel campo di ricerca digitare "**automation**" (automazione) e selezionare"**Azure Automation – Create job**" (Automazione di Azure - Creare il processo) nei risultati della ricerca
- Nel riquadro di creazione del processo configurare le proprietà del processo. Per questa configurazione saranno necessari l'ID della sottoscrizione di Azure, il gruppo di risorse e l'account di Automazione **precedentemente annotati** dal **riquadro dell'account di Automazione**. Per altre informazioni sulle opzioni disponibili in questa sezione, vedere [Azure Automation - Create Job](https://docs.microsoft.com/connectors/azureautomation/#Create_job) (Automazione di Azure - Creare il processo).
- Completare la creazione di questa azione facendo clic su "**Salva flusso**"

2. Creare un'azione per recuperare l'output dello script di PowerShell eseguito
- Selezionare "**+Nuovo passaggio**", seguito da "**Aggiungi un'azione**" nel riquadro del flusso Ricorrenza
- Nel campo di ricerca digitare "**automation**" (automazione) e selezionare"**Azure Automation – Get job output**" (Automazione di Azure - Ottenere l'output del processo) nei risultati della ricerca. Per altre informazioni sulle opzioni disponibili in questa sezione, vedere [Azure Automation – Get job output](https://docs.microsoft.com/connectors/azureautomation/#Get_job_output) (Automazione di Azure - Ottenere l'output del processo).
- Compilare i campi richiesti (simile alla creazione del processo precedente): inserire l'ID della sottoscrizione, il gruppo di risorse e un account di Automazione (come specificati nel riquadro dell'account di Automazione)
- Fare clic all'interno del campo "**ID processo**" per consentire la visualizzazione del menu "**Contenuto dinamico**". In questo menu selezionare l'opzione "**ID processo**".
- Completare la creazione di questa azione facendo clic su "**Salva flusso**"

3. Creare l'azione per inviare messaggi di posta elettronica mediante l'integrazione di Office 365
- Selezionare "**+Nuovo passaggio**", seguito da "**Aggiungi un'azione**" nel riquadro del flusso Ricorrenza
- Nel campo di ricerca digitare "**send an email**" (inviare un messaggio di posta elettronica) e selezionare"**Office 365 Outlook - Send an email**" (Office 365 Outlook - Inviare un messaggio di posta elettronica) nei risultati della ricerca
- Nel campo "**A**" digitare l'indirizzo di posta elettronica a cui inviare il messaggio di notifica
- Nel campo "**Oggetto**" digitare l'oggetto del messaggio di posta elettronica, ad esempio "Notifica tramite posta elettronica dei suggerimenti di ottimizzazione automatica"
- Fare clic all'interno del campo "**Corpo**" per consentire la visualizzazione del menu "**Contenuto dinamico**". In questo menu, sotto "**Get job output**" (Ottenere l'output del processo), selezionare "**Contenuto**" 
- Completare la creazione di questa azione facendo clic su "**Salva flusso**"

> [!TIP]
> Per inviare messaggi di posta elettronica automatizzati a destinatari diversi, creare flussi separati. In questi flussi aggiuntivi, modificare l'indirizzo di posta elettronica del destinatario nel campo "A" e la riga dell'oggetto del messaggio di posta elettronica nel campo "Oggetto". La creazione di nuovi runbook in Automazione di Azure con script di PowerShell personalizzati (ad esempio, modificando l'ID della sottoscrizione di Azure) consente un'ulteriore personalizzazione degli scenari automatizzati, ad esempio l'invio di messaggi di posta elettronica a destinatari separati in merito ai suggerimenti di ottimizzazione automatica per sottoscrizioni separate.
>

Si è così conclusa la procedura per configurare il flusso di lavoro del processo di recapito di posta elettronica. Nell'immagine seguente è illustrato il flusso intero costituito dalle tre azioni create.

![Visualizzare il flusso delle notifiche tramite posta elettronica per l'ottimizzazione automatica](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

Per testare il flusso, fare clic su "**Esegui ora**" nell'angolo superiore destro all'interno del riquadro di flusso.

Le statistiche di esecuzione dei processi automatizzati, con esito positivo delle notifiche di posta elettronica inviate, possono essere visualizzate nel riquadro Analisi di flusso.

![Esecuzione del flusso delle notifiche tramite posta elettronica per l'ottimizzazione automatica](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

Il riquadro Analisi di flusso è utile per monitorare l'esito delle esecuzioni dei processi e, se necessario, per risolvere i problemi.  Nel caso in cui sia necessario risolvere un problema, è anche possibile esaminare il log di esecuzione dello script di PowerShell accessibile tramite l'app Automazione di Azure.

L'output finale del messaggio di posta elettronica automatizzato è simile al seguente messaggio di posta elettronica ricevuto dopo aver compilato ed eseguito questa soluzione:

![Esempio di output di notifiche tramite posta elettronica per l'ottimizzazione automatica](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

Modificando lo script di PowerShell è possibile modificare l'output e la formattazione del messaggio di posta elettronica automatizzato in base alle proprie esigenze.

In base agli scenari personalizzati, è possibile anche personalizzare ulteriormente la soluzione in modo da creare notifiche tramite posta elettronica basate su uno specifico evento di ottimizzazione. 

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su come l'ottimizzazione automatica contribuisca a migliorare le prestazioni del database, vedere [Ottimizzazione automatica nel database SQL di Azure](sql-database-automatic-tuning.md).
- Per consentire all'ottimizzazione automatica nel database SQL di Azure di gestire il carico di lavoro, vedere [Abilitare l'ottimizzazione automatica](sql-database-automatic-tuning-enable.md).
- Per esaminare e applicare manualmente le raccomandazioni di ottimizzazione automatica, vedere [Trovare e applicare raccomandazioni per le prestazioni](sql-database-advisor-portal.md).
