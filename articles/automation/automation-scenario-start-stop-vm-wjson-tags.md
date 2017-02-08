---
title: Uso di tag in formato JSON per creare una pianificazione per l&quot;avvio e l&quot;arresto di una VM di Azure | Documentazione Microsoft
description: Questo articolo illustra come usare stringhe JSON nei tag per automatizzare la pianificazione di arresto e avvio delle macchine virtuali.
services: automation
documentationcenter: 
author: MGoedtel
manager: jwhit
editor: tysonn
ms.assetid: 6afed5d2-e939-4749-8b2c-9312b4c16fb2
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/18/2016
ms.author: magoedte;paulomarquesc
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 0d4098199cec948541eddba8fa88242606e2ec5c


---
# <a name="azure-automation-scenario-using-json-formatted-tags-to-create-a-schedule-for-azure-vm-startup-and-shutdown"></a>Scenario di Automazione di Azure: uso di tag in formato JSON per creare una pianificazione per l'avvio e l'arresto di una macchina virtuale di Azure
Spesso i clienti vogliono pianificare l'avvio e l'arresto delle macchine virtuali per ridurre i costi della sottoscrizione o supportare i requisiti aziendali e tecnici.

Lo scenario seguente consente di configurare l'avvio e l'arresto automatizzati delle macchine virtuali usando un tag denominato Schedule a livello di gruppo di risorse o di macchina virtuale in Azure. Questa pianificazione può essere configurata da domenica a sabato con un orario di avvio e un orario di arresto.

Sono disponibili opzioni predefinite. Sono state illustrate le seguenti operazioni:

* [Set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) con impostazioni di ridimensionamento automatico che consentono di aumentare o ridurre il numero di istanze.
* [DevTest Labs](../devtest-lab/devtest-lab-overview.md) , un servizio con funzionalità predefinite per la pianificazione di operazioni di avvio e arresto.

Queste opzioni supportano tuttavia solo scenari specifici e non possono essere applicate alle macchine virtuali di tipo infrastruttura distribuita come servizio (IaaS).

Quando il tag Schedule viene applicato a un gruppo di risorse, viene applicato anche a tutte le macchine virtuali all'interno di quel gruppo di risorse. Se una pianificazione viene applicata direttamente anche a una macchina virtuale, l'ultima pianificazione avrà la precedenza nell'ordine seguente:

1. Pianificazione applicata a un gruppo di risorse
2. Pianificazione applicata a un gruppo di risorse e a una macchina virtuale nel gruppo di risorse
3. Pianificazione applicata a una macchina virtuale

Fondamentalmente, questo scenario accetta una stringa JSON con un formato specificato, che viene aggiunta come valore per il tag denominato Schedule. Un runbook elenca quindi tutti i gruppi di risorse e le macchine virtuali e identifica le pianificazioni per ogni macchina virtuale in base agli scenari elencati in precedenza. Successivamente scorre in ciclo le macchine virtuali con pianificazioni associate e valuta l'azione che deve essere eseguita. Ad esempio, determina quali macchine virtuali devono essere interrotte, arrestate o ignorate.

Questi runbook eseguono l'autenticazione con l'[account RunAs di Azure](automation-sec-configure-azure-runas-account.md).

## <a name="download-the-runbooks-for-the-scenario"></a>Scaricare i runbook per lo scenario
Questo scenario è costituito da quattro runbook del flusso di lavoro PowerShell scaricabili dalla [Raccolta TechNet](https://gallery.technet.microsoft.com/Azure-Automation-Runbooks-84f0efc7) o dal repository [GitHub](https://github.com/paulomarquesdacosta/azure-automation-scheduled-shutdown-and-startup) per questo progetto.

| Runbook | Descrizione |
| --- | --- |
| Test-ResourceSchedule |Controlla la pianificazione di ogni macchina virtuale ed esegue l'arresto o l'avvio delle macchine virtuali in base alla pianificazione. |
| Add-ResourceSchedule |Aggiunge il tag Schedule a una macchina virtuale o a un gruppo di risorse. |
| Update-ResourceSchedule |Modifica il tag Schedule esistente sostituendolo con uno nuovo. |
| Remove-ResourceSchedule |Rimuove il tag Schedule da una macchina virtuale o da un gruppo di risorse. |

## <a name="install-and-configure-this-scenario"></a>Installare e configurare lo scenario
### <a name="install-and-publish-the-runbooks"></a>Installare e pubblicare i runbook
Dopo aver scaricato i runbook, è possibile importarli seguendo la procedura descritta in [Creazione o importazione di un runbook in Automazione di Azure](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-azure-automation).  Pubblicare ogni runbook dopo averlo importato correttamente nell'account di automazione.

### <a name="add-a-schedule-to-the-test-resourceschedule-runbook"></a>Aggiungere una pianificazione al runbook Test-ResourceSchedule
Seguire questa procedura per abilitare la pianificazione per il runbook Test-ResourceSchedule. Questo è il runbook che verifica quali macchine virtuali devono essere avviate, arrestare o lasciate invariate.

1. Nel portale di Azure aprire l'account di automazione e fare clic sul riquadro **Runbook** .
2. Nel pannello **Test-ResourceSchedule** fare clic sul riquadro **Pianificazioni**.
3. Nel pannello **Pianificazioni** fare clic su **Aggiungi pianificazione**.
4. Nel pannello **Pianificazioni** selezionare **Collegare una pianificazione al runbook**. Selezionare quindi **Crea una nuova pianificazione**.
5. Nel pannello **Nuova pianificazione** digitare il nome della pianificazione, ad esempio *HourlyExecution*.
6. Per la voce della pianificazione **Avvia**impostare l'ora di inizio su un incremento di ora.
7. Selezionare **Ricorrenza** e quindi in **Ricorre ogni** (intervallo) selezionare **1 ora**.
8. Verificare che l'opzione **Imposta scadenza** sia impostata su **No** e quindi fare clic su **Crea** per salvare la nuova pianificazione.
9. Nel pannello delle opzioni **Pianifica runbook** selezionare **Parametri e impostazioni di esecuzione**. Nel pannello **Parametri** di Test-ResourceSchedule immettere il nome della sottoscrizione nel campo **SubscriptionName**.  Questo è l'unico parametro obbligatorio per il runbook.  Al termine, fare clic su **OK**.

Una volta completata, la pianificazione del runbook avrà un aspetto simile alla seguente:

![Runbook Test-ResourceSchedule configurato](./media/automation-scenario-start-stop-vm-wjson-tags/automation-schedule-config.png)<br>

## <a name="format-the-json-string"></a>Formattare la stringa JSON
Fondamentalmente, questa soluzione accetta una stringa JSON con un formato specificato, che viene aggiunta come valore per il tag denominato Schedule. Un runbook elenca quindi tutti i gruppi di risorse e le macchine virtuali e identifica le pianificazioni per ogni macchina virtuale.

Il runbook scorre in ciclo le macchine virtuali con pianificazioni associate e valuta le azioni che devono essere eseguite. Di seguito è riportato un esempio di come devono essere formattate le soluzioni:

```json
{
    "TzId": "Eastern Standard Time",
    "0": {
        "S": "11",
        "E": "17"
    },
    "1": {
        "S": "9",
        "E": "19"
    },
    "2": {
        "S": "9",
        "E": "19"
    },
}
```

Ecco alcune informazioni dettagliate su questa struttura:

1. Il formato di questa struttura JSON è ottimizzato per aggirare il limite di 256 caratteri del valore di un singolo tag in Azure.
2. *TzId* rappresenta il fuso orario della macchina virtuale. Questo ID può essere ottenuto usando la classe TimeZoneInfo .NET in una sessione di PowerShell**[System.TimeZoneInfo]::GetSystemTimeZones()**.

   ![GetSystemTimeZones in PowerShell](./media/automation-scenario-start-stop-vm-wjson-tags/automation-get-timzone-powershell.png)

   * I giorni della settimana sono rappresentati da un valore numerico da 0 a 6. Il valore zero corrisponde a domenica.
   * L'ora di inizio è rappresentata dall'attributo **S** e dal relativo valore nel formato di 24 ore.
   * L'ora di fine o di arresto è rappresentata dall'attributo **E** e dal relativo valore nel formato di 24 ore.

     Se il valore degli attributi **S** ed **E** è zero (0), lo stato della macchina virtuale rimarrà invariato al momento della valutazione.
3. Se si vuole ignorare la valutazione per un giorno specifico della settimana, non aggiungere la sezione per quel giorno della settimana. Nell'esempio seguente verrà valutato solo il lunedì, mentre gli altri giorni della settimana saranno ignorati:

    ```json
    {
        "TzId": "Eastern Standard Time",
        "1": {
            "S": "11",
            "E": "17"
        }
    }
    ```

## <a name="tag-resource-groups-or-vms"></a>Aggiungere tag a gruppi di risorse o macchine virtuali
Per arrestare le macchine virtuali, è necessario aggiungere un tag alle macchine virtuali o ai gruppi di risorse in cui si trovano. Le macchine virtuali che non hanno un tag Schedule non vengono valutate. Di conseguenza, non vengono avviate né arrestate.

Esistono due modi per aggiungere tag a gruppi di risorse o macchine virtuali con questa soluzione. È possibile farlo direttamente nel portale di Azure oppure usare i runbook Add-ResourceSchedule, Update-ResourceSchedule e Remove-ResourceSchedule.

### <a name="tag-through-the-portal"></a>Aggiungere tag tramite il portale
Seguire questa procedura per aggiungere un tag a una macchina virtuale o a un gruppo di risorse nel portale:

1. Rendere flat la stringa JSON e verificare che non siano presenti spazi.  La stringa JSON dovrebbe avere un aspetto simile al seguente:

    ```json
    {"TzId":"Eastern Standard Time","0":{"S":"11","E":"17"},"1":{"S":"9","E":"19"},"2": {"S":"9","E":"19"},"3":{"S":"9","E":"19"},"4":{"S":"9","E":"19"},"5":{"S":"9","E":"19"},"6":{"S":"11","E":"17"}}
    ```

2. Selezionare l'icona **Tag** di una macchina virtuale o un gruppo di risorse per applicare questa pianificazione.

   ![Opzione per i tag delle VM](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-tag-option.png)

3. I tag vengono definiti in base a una coppia chiave/valore. Digitare **Pianificazione** nel campo **Chiave** e quindi incollare la stringa JSON nel campo **Valore**. Fare clic su **Salva**. Il nuovo tag verrà visualizzato nell'elenco dei tag per la risorsa.

   ![Tag di pianificazione delle VM](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-schedule-tag.png)

### <a name="tag-from-powershell"></a>Aggiungere tag da PowerShell
Tutti i runbook importati contengono informazioni della Guida all'inizio dello script che descrivono come eseguirli direttamente da PowerShell. È possibile chiamare i runbook Add-ScheduleResource e Update-ScheduleResource da PowerShell. A questo scopo, passare i parametri necessari che consentono di creare o aggiornare il tag Schedule in un macchina virtuale o un gruppo di risorse all'esterno del portale.

Per creare, aggiungere ed eliminare i tag tramite PowerShell, è prima necessario [configurare l'ambiente PowerShell per Azure](/powershell/azureps-cmdlets-docs). Al termine della configurazione, è possibile procedere con i passaggi seguenti.

### <a name="create-a-schedule-tag-with-powershell"></a>Creare un tag di pianificazione con PowerShell
1. Aprire una sessione di PowerShell. Usare quindi l'esempio seguente per autenticarsi con l'account RunAs e specificare una sottoscrizione:

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

2. Definire una tabella hash di pianificazione. Di seguito è riportato un esempio di come dovrebbe essere costruita:

    ```powershell
    $schedule= @{ "TzId"="Eastern Standard Time"; "0"= @{"S"="11";"E"="17"};"1"= @{"S"="9";"E"="19"};"2"= @{"S"="9";"E"="19"};"3"= @{"S"="9";"E"="19"};"4"= @{"S"="9";"E"="19"};"5"= @{"S"="9";"E"="19"};"6"= @{"S"="11";"E"="17"}}
    ```

3. Definire i parametri richiesti dal runbook. Nell'esempio seguente, la destinazione è una VM:

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; "VmName"="VM01";"Schedule"=$schedule}
    ```

    Se si sta aggiungendo un tag a un gruppo di risorse, rimuovere il parametro *VMName* dalla tabella hash $params come segue:

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; "Schedule"=$schedule}
    ```

4. Eseguire il runbook Add-ResourceSchedule con i parametri seguenti per creare il tag Schedule:

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Add-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

5. Per aggiornare un tag di una macchina virtuale o un gruppo di risorse, eseguire il runbook **Update-ResourceSchedule** con i parametri seguenti:

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Update-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

### <a name="remove-a-schedule-tag-with-powershell"></a>Rimuovere un tag di pianificazione con PowerShell
1. Aprire una sessione di PowerShell, eseguire il codice seguente per autenticarsi con l'account RunAs e quindi selezionare e specificare una sottoscrizione:

    ```powershell
    Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

2. Definire i parametri richiesti dal runbook. Nell'esempio seguente, la destinazione è una VM:

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01";"VmName"="VM01"}
    ```

    Se si sta rimuovendo un tag da un gruppo di risorse, rimuovere il parametro *VMName* dalla tabella hash $params come segue:

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"}
    ```

3. Per rimuovere il tag Schedule, eseguire il runbook Remove-ResourceSchedule:

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

4. Per aggiornare un tag di una macchina virtuale o un gruppo di risorse, eseguire il runbook Remove-ResourceSchedule con i parametri seguenti:

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

> [!NOTE]
> È consigliabile monitorare proattivamente questi runbook, e lo stato della macchina virtuale, per verificare che le macchine virtuali vengano arrestate e avviate di conseguenza.
>

È possibile visualizzare i dettagli del processo del runbook Test-ResourceSchedule nel portale di Azure selezionando il riquadro **Processi** del runbook stesso. Nel riepilogo del processo saranno visualizzati i parametri di input e il flusso di output, oltre alle informazioni generali sul processo e alle eventuali eccezioni che si sono verificate.

Il **Riepilogo processi** include i messaggi dai flussi di output, di avviso e di errore. Selezionare il riquadro **Output** per visualizzare i risultati dettagliati dell'esecuzione del runbook.

![Output di Test-ResourceSchedule](./media/automation-scenario-start-stop-vm-wjson-tags/automation-job-output.png)

## <a name="next-steps"></a>Passaggi successivi
* Per iniziare a usare runbook del flusso di lavoro PowerShell, vedere [Il primo runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md).
* Per altre informazioni sui tipi di runbook, i relativi vantaggi e le limitazioni, vedere [Tipi di runbook di Automazione di Azure](automation-runbook-types.md).
* Per altre informazioni sulla funzionalità di supporto degli script PowerShell, vedere il blog relativo al [Announcing Native PowerShell Script Support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)(Supporto di script PowerShell nativi in Automazione di Azure).
* Per altre informazioni sulla registrazione e sull'output dei runbook, vedere [Output di runbook e messaggi in Automazione di Azure](automation-runbook-output-and-messages.md).
* Per altre informazioni sull'account RunAs di Azure e su come usarlo per eseguire l'autenticazione dei runbook, vedere [Autenticare runbook con account RunAs di Azure](automation-sec-configure-azure-runas-account.md).



<!--HONumber=Dec16_HO2-->


