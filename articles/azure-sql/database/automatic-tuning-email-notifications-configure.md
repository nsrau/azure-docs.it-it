---
title: Guida alla procedura di ottimizzazione automatica delle notifiche tramite posta elettronica
description: Abilitare le notifiche tramite posta elettronica per l'ottimizzazione automatica delle query del database SQL di Azure.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 06/03/2019
ms.openlocfilehash: a373a28a180b2a6c72f6a291b9d1437a2e88d9ff
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500955"
---
# <a name="email-notifications-for-automatic-tuning"></a>Notifiche tramite posta elettronica per l'ottimizzazione automatica
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Le raccomandazioni per l'ottimizzazione del database SQL di Azure vengono generate dall' [ottimizzazione automatica](automatic-tuning-overview.md)del database SQL di Azure. Questa soluzione monitora e analizza continuamente i carichi di lavoro dei database offrendo consigli di ottimizzazione personalizzati per ogni singolo database correlato alla creazione dell'indice, all'eliminazione degli indici e all'ottimizzazione dei piani di esecuzione delle query.

Le indicazioni per l'ottimizzazione automatica del database SQL di Azure possono essere visualizzate nel [portale di Azure](database-advisor-find-recommendations-portal.md), recuperate con chiamate [API REST](/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) o tramite i comandi [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) e [PowerShell](/powershell/module/az.sql/get-azsqldatabaserecommendedaction) . Questo articolo si basa sull'utilizzo di uno script di PowerShell per recuperare suggerimenti di ottimizzazione automatica.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato da Database SQL di Azure, ma tutte le attività di sviluppo future sono incentrate sul modulo Az.Sql. Per informazioni su questi cmdlet, vedere [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Automatizzare le notifiche tramite posta elettronica per le indicazioni di ottimizzazione automatica

La soluzione seguente automatizza l'invio di notifiche di posta elettronica contenenti suggerimenti per l'ottimizzazione automatica. La soluzione descritta consiste nell'esecuzione automatica di uno script di PowerShell per recuperare i suggerimenti di ottimizzazione usando [Automazione di Azure](../../automation/automation-intro.md) e automatizzare la pianificazione di un processo di recapito di posta elettronica usando [Microsoft Flow](https://flow.microsoft.com).

## <a name="create-azure-automation-account"></a>Creare un account di Automazione di Azure

Per usare Automazione di Azure, il primo passaggio consiste nel creare un account di automazione e configurarlo con le risorse di Azure da usare per l'esecuzione dello script di PowerShell. Per altre informazioni su Automazione di Azure e le relative funzionalità, vedere [Introduzione ad Automazione di Azure](../../automation/index.yml).

Seguire questa procedura per creare un account di automazione di Azure tramite il metodo di selezione e configurazione di un'app di automazione da Azure Marketplace:

1. Accedere al portale di Azure.
1. Fare clic su "**+ Crea una risorsa**" nell'angolo superiore sinistro.
1. Cercare "**automazione**" (premere INVIO).
1. Fare clic sull'app di automazione nei risultati della ricerca.

    ![Aggiunta di Automazione di Azure](./media/automatic-tuning-email-notifications-configure/howto-email-01.png)

1. Una volta all'interno del riquadro "crea un account di automazione", fare clic su "**Crea**".
1. Compilare le informazioni necessarie: immettere un nome per questo account di automazione, selezionare l'ID sottoscrizione di Azure e le risorse di Azure da usare per l'esecuzione dello script di PowerShell.
1. Per l'opzione "**Crea account RunAs di Azure**", selezionare **Sì** per configurare il tipo di account con cui viene eseguito lo script PowerShell con l'ausilio di automazione di Azure. Per ulteriori informazioni sui tipi di account, vedere [account RunAs](../../automation/manage-runas-account.md).
1. Concludere la creazione dell'account di automazione facendo clic su **Crea**.

> [!TIP]
> Annotare il nome dell'account di Automazione di Azure, l'ID della sottoscrizione e le risorse (ad esempio, copiandoli e incollandoli in un blocco note), esattamente come sono stati immessi durante la creazione dell'app Automazione. Queste informazioni saranno necessarie più avanti.

Se si hanno più sottoscrizioni di Azure per le quali si vuole creare la stessa automazione, è necessario ripetere questo processo anche per le altre sottoscrizioni.

## <a name="update-azure-automation-modules"></a>Aggiornare i moduli di Automazione di Azure

Lo script di PowerShell per recuperare la raccomandazione di ottimizzazione automatica usa i comandi [Get-AzResource](/powershell/module/az.Resources/Get-azResource) e [Get-AzSqlDatabaseRecommendedAction](/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction) per i quali è richiesto il modulo Azure 4 e versioni successive.

- Nel caso in cui i moduli di Azure debbano essere aggiornati, vedere [AZ Module Support in automazione di Azure](../../automation/shared-resources/modules.md).

## <a name="create-azure-automation-runbook"></a>Creare Runbook di automazione di Azure

Il passaggio successivo consiste nella creazione di un runbook in Automazione di Azure all'interno del quale si trova lo script di PowerShell per il recupero dei suggerimenti di ottimizzazione.

Per creare un nuovo runbook di Automazione di Azure, seguire questa procedura:

1. Accedere all'account di automazione di Azure creato nel passaggio precedente.
1. Nel riquadro account di automazione fare clic sulla voce di menu "**manuali operativi**" sul lato sinistro per creare un nuovo Runbook di automazione di Azure con lo script di PowerShell. Per altre informazioni sulla creazione di manuali operativi di automazione, vedere [creare un nuovo Runbook](../../automation/manage-runbooks.md#create-a-runbook).
1. Per aggiungere un nuovo Runbook, fare clic sull'opzione di menu "**+ Aggiungi Runbook**" e quindi fare clic su "**creazione rapida-crea una nuova Runbook**".
1. Nel riquadro Runbook digitare il nome della Runbook (ai fini di questo esempio viene usato "**AutomaticTuningEmailAutomation**"), selezionare il tipo di Runbook come **PowerShell** e scrivere una descrizione di questo Runbook per descriverne lo scopo.
1. Fare clic sul pulsante **Crea** per completare la creazione di un nuovo Runbook.

    ![Aggiungere un runbook di Automazione di Azure](./media/automatic-tuning-email-notifications-configure/howto-email-03.png)

Seguire questa procedura per caricare uno script di PowerShell nel runbook creato:

1. All'interno del riquadro "**Edit PowerShell Runbook**" selezionare "**manuali operativi**" nell'albero dei menu ed espandere la visualizzazione finché non viene visualizzato il nome del Runbook (in questo esempio "**AutomaticTuningEmailAutomation**"). Selezionare questo runbook.
1. Nella prima riga del comando "Edit PowerShell Runbook" (a partire dal numero 1), copiare e incollare il codice dello script di PowerShell seguente. Questo script di PowerShell viene fornito così com'è per iniziare. Modificare lo script in base alle esigenze.

Nell'intestazione dello script di PowerShell fornito è necessario sostituire `<SUBSCRIPTION_ID_WITH_DATABASES>` con l'ID della sottoscrizione di Azure. Per informazioni su come recuperare l'ID della sottoscrizione di Azure, vedere [Getting your Azure Subscription GUID](/archive/blogs/mschray/getting-your-azure-subscription-guid-new-portal) (Recupero del codice GUID della sottoscrizione).

Nel caso di più sottoscrizioni, è possibile aggiungerle come delimitate da virgole alla proprietà "$subscriptions" nell'intestazione dello script.

```powershell
# PowerShell script to retrieve Azure SQL Database automatic tuning recommendations.
#
# Provided "as-is" with no implied warranties or support.
# The script is released to the public domain.
#
# Replace <SUBSCRIPTION_ID_WITH_DATABASES> in the header with your Azure subscription ID.
#
# Microsoft Azure SQL Database team, 2018-01-22.

# Set subscriptions : IMPORTANT – REPLACE <SUBSCRIPTION_ID_WITH_DATABASES> WITH YOUR SUBSCRIPTION ID
$subscriptions = ("<SUBSCRIPTION_ID_WITH_DATABASES>", "<SECOND_SUBSCRIPTION_ID_WITH_DATABASES>", "<THIRD_SUBSCRIPTION_ID_WITH_DATABASES>")

# Get credentials
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {
    Select-AzSubscription -SubscriptionId $subscriptionId
    $rgs = Get-AzResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzResource -ResourceGroupName $rgname -ResourceType $resourceType

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

                # Loop through all automatic tuning recommendation types
                foreach ($advisor in $advisors) {
                    $recs = Get-AzSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
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

Per salvare lo script, fare clic sul pulsante "**Salva**" nell'angolo superiore destro. Quando si è soddisfatti dello script, fare clic sul pulsante "**pubblica**" per pubblicare il Runbook.

Nel riquadro principale Runbook è possibile scegliere di fare clic sul pulsante "**Avvia**" per **testare** lo script. Fare clic su "**output**" per visualizzare i risultati dello script eseguito. Questo output diventerà il contenuto del messaggio di posta elettronica. L'output dello script di esempio può essere visualizzato nella schermata seguente.

![Eseguire i suggerimenti di ottimizzazione automatica con Automazione di Azure](./media/automatic-tuning-email-notifications-configure/howto-email-04.png)

Modificare il contenuto personalizzando lo script di PowerShell in base alle esigenze.

Con i passaggi precedenti, lo script di PowerShell per recuperare le indicazioni di ottimizzazione automatica viene caricato in automazione di Azure. Il passaggio successivo consiste nell'automatizzazione e nella pianificazione del processo di recapito della posta elettronica.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Automatizzare i processi di posta elettronica con Microsoft Flow

Per completare la soluzione, come ultimo passaggio, creare un flusso di automazione in Microsoft Flow composto da tre azioni (processi):

- "**Automazione di Azure-crea processo**": usato per eseguire lo script di PowerShell per recuperare le raccomandazioni di ottimizzazione automatica all'interno del Runbook di automazione di Azure.
- "**Automazione di Azure-ottenere l'output del processo**": usato per recuperare l'output dallo script di PowerShell eseguito.
- "**Office 365 Outlook-invia un messaggio di posta elettronica**": usato per inviare messaggi di posta elettronica. I messaggi di posta elettronica vengono inviati usando l'account aziendale o dell'Istituto di istruzione del singolo utente che crea il flusso.

Per altre informazioni sulle funzionalità di Microsoft Flow, vedere [Attività iniziali con Microsoft Flow](/flow/getting-started).

Il prerequisito per questo passaggio consiste nell'iscriversi per un account di [Microsoft Flow](https://flow.microsoft.com) e per accedere. Dopo aver eseguito l'accesso alla soluzione, seguire questa procedura per configurare un **nuovo flusso**:

1. Accedere alla voce di menu "**flussi personali**".
1. All'interno di flussi personali selezionare il collegamento "**+ Crea da zero**" nella parte superiore della pagina.
1. Fare clic sul collegamento "**Cerca centinaia di connettori e trigger**" nella parte inferiore della pagina.
1. Nel campo di ricerca digitare "**ricorrenza**" e selezionare "**pianificazione-ricorrenza**" dai risultati della ricerca per pianificare l'esecuzione del processo di recapito tramite posta elettronica.
1. Nel campo Frequenza del riquadro Ricorrenza selezionare la frequenza di pianificazione per l'esecuzione del flusso, ad esempio per inviare messaggi di posta elettronica automatici ogni minuto, ora, giorno, settimana e così via.

Il passaggio successivo consiste nell'aggiunta dei tre processi (creazione, acquisizione dell'output e invio del messaggio di posta elettronica) al flusso ricorrente appena creato. Per aggiungere al flusso i tre processi richiesti, seguire questa procedura:

1. Creare l'azione per eseguire lo script di PowerShell per recuperare i suggerimenti di ottimizzazione

   - Selezionare "**+ nuovo passaggio**", seguito da "**Aggiungi un'azione**" all'interno del riquadro del flusso di ricorrenza.
   - Nel campo di ricerca digitare "**Automation**" e selezionare "**automazione di Azure – crea processo**" dai risultati della ricerca.
   - Nel riquadro di creazione del processo configurare le proprietà del processo. Per questa configurazione saranno necessari l'ID della sottoscrizione di Azure, il gruppo di risorse e l'account di Automazione **precedentemente annotati** dal **riquadro dell'account di Automazione**. Per altre informazioni sulle opzioni disponibili in questa sezione, vedere [Azure Automation - Create Job](/connectors/azureautomation/#create-job) (Automazione di Azure - Creare il processo).
   - Per completare la creazione di questa azione, fare clic su "**Salva flusso**".

2. Creare un'azione per recuperare l'output dallo script di PowerShell eseguito

   - Selezionare "**+ nuovo passaggio**", seguito da "**Aggiungi un'azione**" all'interno del riquadro del flusso di ricorrenza
   - Nel campo di ricerca digitare "**Automation**" e selezionare "**automazione di Azure-ottenere l'output del processo**" dai risultati della ricerca. Per altre informazioni sulle opzioni disponibili in questa sezione, vedere [Azure Automation – Get job output](/connectors/azureautomation/#get-job-output) (Automazione di Azure - Ottenere l'output del processo).
   - Popolare i campi richiesti (simile alla creazione del processo precedente): popolare l'ID sottoscrizione, il gruppo di risorse e l'account di automazione di Azure (immessi nel riquadro dell'account di automazione).
   - Fare clic all'interno del campo "**ID processo**" per visualizzare il menu "**contenuto dinamico**". Dall'interno di questo menu, selezionare l'opzione "**ID processo**".
   - Per completare la creazione di questa azione, fare clic su "**Salva flusso**".

3. Creare un'azione per inviare messaggi di posta elettronica tramite l'integrazione con Office 365

   - Selezionare "**+ nuovo passaggio**", seguito da "**Aggiungi un'azione**" all'interno del riquadro del flusso di ricorrenza.
   - Nel campo di ricerca digitare "**Invia un messaggio di posta elettronica**" e selezionare "**Office 365 Outlook-invia un messaggio di posta elettronica**" dai risultati della ricerca.
   - Nel campo "**a**" immettere l'indirizzo di posta elettronica a cui è necessario inviare il messaggio di posta elettronica di notifica.
   - Nel campo "**oggetto**" digitare l'oggetto del messaggio di posta elettronica, ad esempio "notifica di posta elettronica consigliata ottimizzazione automatica".
   - Fare clic all'interno del campo "**corpo**" per visualizzare il menu "**contenuto dinamico**". Dall'interno di questo menu, in "**Get Job output**", selezionare "**Content**".
   - Per completare la creazione di questa azione, fare clic su "**Salva flusso**".

> [!TIP]
> Per inviare messaggi di posta elettronica automatizzati a destinatari diversi, creare flussi separati. In questi flussi aggiuntivi modificare l'indirizzo di posta elettronica del destinatario nel campo "a" e la riga dell'oggetto del messaggio di posta elettronica nel campo "oggetto". La creazione di nuovi manuali operativi in automazione di Azure con script di PowerShell personalizzati, ad esempio con la modifica dell'ID sottoscrizione di Azure, consente di personalizzare ulteriormente gli scenari automatizzati, ad esempio l'invio di messaggi di posta elettronica a destinatari distinti in consigli di ottimizzazione automatica per sottoscrizioni separate.

Si è così conclusa la procedura per configurare il flusso di lavoro del processo di recapito di posta elettronica. Nell'immagine seguente è illustrato il flusso intero costituito dalle tre azioni create.

![Visualizzare il flusso delle notifiche tramite posta elettronica per l'ottimizzazione automatica](./media/automatic-tuning-email-notifications-configure/howto-email-05.png)

Per testare il flusso, fare clic su "**Esegui ora**" nell'angolo superiore destro all'interno del riquadro del flusso.

Le statistiche di esecuzione dei processi automatizzati, con esito positivo delle notifiche di posta elettronica inviate, possono essere visualizzate nel riquadro Analisi di flusso.

![Esecuzione del flusso delle notifiche tramite posta elettronica per l'ottimizzazione automatica](./media/automatic-tuning-email-notifications-configure/howto-email-06.png)

Il riquadro analisi di flusso è utile per monitorare la riuscita delle esecuzioni dei processi e, se necessario, per la risoluzione dei problemi.  Nel caso di risoluzione dei problemi, è anche possibile esaminare il log di esecuzione dello script di PowerShell accessibile tramite l'app di automazione di Azure.

L'output finale del messaggio di posta elettronica automatizzato è simile al seguente messaggio di posta elettronica ricevuto dopo aver compilato ed eseguito questa soluzione:

![Esempio di output di notifiche tramite posta elettronica per l'ottimizzazione automatica](./media/automatic-tuning-email-notifications-configure/howto-email-07.png)

Modificando lo script di PowerShell è possibile modificare l'output e la formattazione del messaggio di posta elettronica automatizzato in base alle proprie esigenze.

In base agli scenari personalizzati, è possibile anche personalizzare ulteriormente la soluzione in modo da creare notifiche tramite posta elettronica basate su uno specifico evento di ottimizzazione.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su come l'ottimizzazione automatica contribuisca a migliorare le prestazioni del database, vedere [Ottimizzazione automatica nel database SQL di Azure](automatic-tuning-overview.md).
- Per consentire all'ottimizzazione automatica nel database SQL di Azure di gestire il carico di lavoro, vedere [Abilitare l'ottimizzazione automatica](automatic-tuning-enable.md).
- Per rivedere e applicare manualmente le indicazioni relative all'ottimizzazione automatica, vedere [trovare e applicare le raccomandazioni per le prestazioni](database-advisor-find-recommendations-portal.md).