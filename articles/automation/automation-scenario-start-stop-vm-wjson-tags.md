<properties
   pageTitle="Utilizzo di tag in formato JSON per creare una pianificazione per l'avvio e l'arresto di una VM di Azure | Microsoft Azure"
   description="In questo articolo viene illustrato come utilizzare le stringhe JSON nei tag per automatizzare la pianificazione di arresto e avvio delle VM."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/18/2016"
   ms.author="magoedte;paulomarquesc" />

# Scenario di automazione di Azure - Utilizzo di tag in formato JSON per creare una pianificazione per l'avvio e l'arresto di una VM di Azure

In genere i clienti desiderano pianificare l'avvio e l'arresto delle macchine virtuali per ridurre i costi di sottoscrizione o supportare i requisiti aziendali e tecnici.

Lo scenario seguente consente di configurare avvio e arresto automatizzati delle VM utilizzando un tag denominato Pianificazione a livello di gruppo di risorse o di macchina virtuale in Azure. Questa pianificazione può essere configurata da domenica a sabato con un orario di avvio e un orario di arresto. Mentre alcune opzioni sono disponibili immediatamente, come l'utilizzo di [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) con impostazioni di ridimensionamento automatico che consentiranno l'aumento o la riduzione e il servizio [DevTest Labs](../devtest-lab/devtest-lab-overview.md) con la capacità integrata di pianificare l'avvio e l'arresto, queste opzioni supportano solo scenari specifici e non possono essere applicate a VM IaaS.

Quando il tag Pianificazione viene applicato a un gruppo di risorse, viene applicato a tutte le macchine virtuali all'interno di tale gruppo di risorse. Se una pianificazione viene anche applicata direttamente a una VM, l'ultima pianificazione avrà la precedenza nell'ordine seguente:

1.  Pianificazione applicata a un gruppo di risorse
2.  Pianificazione applicata a un gruppo di risorse e a una macchina virtuale nel gruppo di risorse
3.  Pianificazione applicata a una macchina virtuale

Fondamentalmente, in questo scenario viene presa una stringa JSON con un formato specificato e viene aggiunta come valore per il tag Pianificazione. Successivamente, un runbook elenca tutti i gruppi di risorse e le macchine virtuali e identifica le pianificazioni per ogni VM in base agli scenari precedenti, poi esamina queste VM con pianificazioni associate e valuta l'azione da eseguire, cioè interrompere, arrestare o ignorare.

Questi runbook eseguono l'autenticazione con l'[account RunAs di Azure](../automation/automation-sec-configure-azure-runas-account.md).

## Come ottenere lo scenario

Questo scenario è costituito da quattro runbook del flusso di lavoro di PowerShell che è possibile scaricare dalla [Raccolta TechNet](https://gallery.technet.microsoft.com/Azure-Automation-Runbooks-84f0efc7) o dal repository [GitHub](https://github.com/paulomarquesdacosta/azure-automation-scheduled-shutdown-and-startup) per questo progetto.

Runbook | Descrizione 
----------|----------
Test-ResourceSchedule | Controlla la pianificazione di ogni macchina virtuale (esegue l'arresto o l'avvio delle macchine virtuali in base alla pianificazione)
Add-ResourceSchedule | Aggiunge il tag Pianificazione a una VM o a un gruppo di risorse
Update-ResourceSchedule | Modifica un tag Pianificazione esistente sostituendolo con uno nuovo
Remove-ResourceSchedule | Rimuove il tag Pianificazione da una VM o da un gruppo di risorse 


## Installazione e configurazione dello scenario

### Installare e pubblicare i runbook

Dopo aver scaricato i runbook, è possibile importarli usando la procedura descritta nella sezione [Importazione di un runbook](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-Azure-Automation) dell'articolo Creazione o importazione di un runbook in Automazione di Azure. Pubblicare ogni runbook dopo che è stato importato correttamente nell'account di Automazione.


### Aggiunta di una pianificazione a Test-ResourceSchedule

Seguire questi passaggi per abilitare la pianificazione sul runbook Test-ResourceSchedule. Si tratta del runbook che verifica quale macchina virtuale verrà avviata, arrestata o lasciata inalterata.

1. Nel portale di Azure aprire l'account di automazione e fare clic sul riquadro **Runbook**.
2. Nel pannello **Test-ResourceSchedule** fare clic sul riquadro **Pianificazioni**.
3. Nel pannello **Pianificazioni** fare clic su **Aggiungi pianificazione**.
4. Nel pannello **Pianificazioni** selezionare **Collega una pianificazione al runbook** e nel pannello **Pianificazione** selezionare **Crea una nuova pianificazione**.
5.  Nel pannello **Nuova pianificazione** digitare il nome della pianificazione. Ad esempio, HourlyExecution.
6. Per la voce della pianificazione **Avvia** impostare l'ora di inizio su un incremento di ora arrotondato.
7. Selezionare **Ricorrenza** e quindi in **Ricorre ogni** selezionare **1 ora**.
8. Verificare che **Imposta scadenza** sia impostata su **No** e quindi fare clic su **Crea** per salvare la nuova pianificazione.
9. Nel pannello delle opzioni **Pianifica runbook** selezionare **Parametri e impostazioni di esecuzione** e nel pannello **Parametri** di Test-ResourceSchedule immettere il nome della sottoscrizione nel campo **SubscriptionName**. Questo è l'unico parametro obbligatorio per il runbook. Al termine, fare clic su **OK**.
   

Una volta completata, la pianificazione del runbook avrà un aspetto simile alla seguente:<br> ![Runbook Test-ResourceSchedule configurato](./media/automation-scenario-start-stop-vm-wjson-tags/automation-schedule-config.png)<br>

## Formato JSON di pianificazione

Fondamentalmente, in questa soluzione viene presa una stringa JSON con un formato specificato e viene aggiunta come valore per il tag Pianificazione. Successivamente, un runbook elenca tutti i gruppi di risorse e le macchine virtuali e identifica le pianificazioni per ogni macchina virtuale, poi esamina le macchine virtuali con pianificazioni associate e valuta l'azione da eseguire. Di seguito è riportato un esempio di come deve essere la formattazione.

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

Informazioni dettagliate su questa struttura:

1. Il formato di questa struttura JSON è ottimizzato per aggirare il limite di 256 caratteri del valore di un singolo tag in Azure

2. *TzId* rappresenta il fuso orario della macchina virtuale. Questo ID può essere ottenuto usando la classe TimeZoneInfo .NET in una sessione di PowerShell **[System.TimeZoneInfo]::GetSystemTimeZones()**.<br>

    ![GetSystemTimeZones in PowerShell](./media/automation-scenario-start-stop-vm-wjson-tags/automation-get-timzone-powershell.png)

    - I giorni della settimana sono rappresentati da un valore numerico tra 0 e 6. Il valore 0 corrisponde a domenica.
    - L'ora di inizio è rappresentata dall'attributo **S** e dal relativo valore nel formato di 24 ore.
    - L'ora di fine o di arresto è rappresentata dall'attributo **E** e dal relativo valore nel formato di 24 ore.

    Se il valore degli attributi S ed E è pari a zero (0), lo stato della macchina virtuale rimarrà immutato al momento della valutazione.

3. Se si desidera ignorare la valutazione per un giorno specifico della settimana, non aggiungere la sezione del giorno della settimana correlato. Nell'esempio seguente, verrà valutato solo il lunedì, mentre gli altri giorni della settimana verranno ignorati.
   
        {
          "TzId": "Eastern Standard Time",
           "1": {
             "S": "11",
             "E": "17"
           }
        }

## Assegnazione di tag a gruppi di risorse o VM

Per arrestare le VM, è necessario assegnare un tag alle VM stesse o al gruppo di risorse in cui si trovano. Le macchine virtuali prive del tag Pianificazione non vengono valutate, pertanto non vengono avviate né arrestate. Esistono due modi per aggiungere un tag a gruppi di risorse o macchine virtuali con questa soluzione, ovvero direttamente dal portale o con i runbook **Add-ResourceSchedule**, **Update-ResourceSchedule** e **Remove ResourceSchedule**.

### Assegnazione di tag tramite il portale

Seguire questi passaggi per assegnare un tag a una macchina virtuale o a un gruppo di risorse nel portale.

1. Rendere flat la stringa JSON e verificare che non vi siano spazi. La stringa JSON dovrebbe avere un aspetto simile al seguente:

        {"TzId":"Eastern Standard Time","0":{"S":"11","E":"17"},"1":{"S":"9","E":"19"},"2": {"S":"9","E":"19"},"3":{"S":"9","E":"19"},"4":{"S":"9","E":"19"},"5":{"S":"9","E":"19"},"6":{"S":"11","E":"17"}}
   

2. Selezionare una macchina virtuale o un gruppo di risorse per applicare questa pianificazione selezionando l'icona Tag.<br>![Opzione tag delle VM](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-tag-option.png)
3. I tag vengono definiti dopo una coppia chiave/valore. Digitare **Pianificazione** nel campo **Chiave**, incollare la stringa JSON nel campo **Valore** e quindi fare clic su **Salva**. A questo punto, il nuovo tag sarà visualizzato nell'elenco dei tag per la risorsa.<br>![Tag di pianificazione delle VM](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-schedule-tag.png)


### Assegnazione di tag da PowerShell

Tutti i runbook importati contengono informazioni della Guida all'inizio dello script per descrivere come eseguirli direttamente da PowerShell. I runbook Add-ScheduleResource e Update-ScheduleResource possono essere chiamati da PowerShell trasmettendo i parametri obbligatori che consentono di creare o aggiornare il tag Pianificazione in una VM o in un gruppo di risorse all'esterno del portale.

Per creare, aggiungere ed eliminare i tag tramite PowerShell, è prima necessario [configurare l'ambiente PowerShell per Azure](../powershell-install-configure.md). Al termine della configurazione, è possibile procedere con i passaggi seguenti.

### Creazione di un tag di pianificazione con PowerShell

1. Aprire una sessione di PowerShell ed eseguire il passaggio seguente per l'autenticazione con l'account RunAs e specificare una sottoscrizione:
    
        Conn = Get-AutomationConnection -Name AzureRunAsConnection
        Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
        -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
        Select-AzureRmSubscription -SubscriptionName "MySubscription"
   
2. Definire una tabella hash di pianificazione. Di seguito è riportato un esempio di come dovrebbe essere costruita:
    
        $schedule= @{ "TzId"="Eastern Standard Time"; "0"= @{"S"="11";"E"="17"};"1"= @{"S"="9";"E"="19"};"2"= @{"S"="9";"E"="19"};"3"= @{"S"="9";"E"="19"};"4"= @{"S"="9";"E"="19"};"5"= @{"S"="9";"E"="19"};"6"= @{"S"="11";"E"="17"}}

3. Definire i parametri richiesti dal runbook. Nell'esempio seguente, la destinazione è una VM:

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; `
        "VmName"="VM01";"Schedule"=$schedule}

    Se si sta assegnando un tag a un gruppo di risorse, è sufficiente rimuovere il parametro *VMName* dalla tabella hash $params come segue:

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; `
        "Schedule"=$schedule}

4. Per creare il tag Pianificazione, seguire il runbook **Add-ResourceSchedule** con i parametri seguenti:

        Start-AzureRmAutomationRunbook -Name "Add-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"

5. Se si vuole aggiornare un tag di una macchina virtuale o un gruppo di risorse, eseguire il runbook **Update-ResourceSchedule** con i parametri seguenti:

        Start-AzureRmAutomationRunbook -Name "Update-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"

### Rimozione di un tag di pianificazione con PowerShell

1. Aprire una sessione di PowerShell ed eseguire il passaggio seguente per l'autenticazione con l'account RunAs, poi selezionare e specificare una sottoscrizione:
    
        Conn = Get-AutomationConnection -Name AzureRunAsConnection
        Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
        -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
        Select-AzureRmSubscription -SubscriptionName "MySubscription"

2. Definire i parametri richiesti dal runbook. Nell'esempio seguente, la destinazione è una VM:

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01" ` 
        ;"VmName"="VM01"}

    Se si sta rimuovendo un tag da un gruppo di risorse, è sufficiente rimuovere il parametro *VMName* dalla tabella hash $params come segue:

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"}

3. Per rimuovere il tag Pianificazione, eseguire il runbook **Remove-ResourceSchedule**:

        Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"

4. Se si vuole aggiornare un tag di una macchina virtuale o un gruppo di risorse, eseguire il runbook **Remove-ResourceSchedule** con i parametri seguenti:

        Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"


>[AZURE.NOTE] Si consiglia di monitorare proattivamente questi runbook (e lo stato della macchina virtuale) per verificare che le macchine virtuali vengano arrestate e avviate di conseguenza.

È possibile visualizzare i dettagli del processo del runbook **Test-ResourceSchedule** nel portale di Azure selezionando il riquadro Processi del runbook. Il riepilogo del processo visualizzerà i parametri di input e il flusso di output, oltre alle informazioni generali sul processo e alle eventuali eccezioni che si sono verificate. La cronologia includerà messaggi provenienti dal flusso di output, oltre a flussi di errore e di avviso. Selezionare il riquadro Output per visualizzare i risultati dettagliati dell'esecuzione del runbook.<br> ![Output di Test-ResourceSchedule](./media/automation-scenario-start-stop-vm-wjson-tags/automation-job-output.png)

## Passaggi successivi

-  Per iniziare a usare i runbook del flusso di lavoro di PowerShell, vedere [Il primo runbook del flusso di lavoro di PowerShell](automation-first-runbook-textual.md)
-  Per altre informazioni sui tipi di runbook, i relativi vantaggi e le limitazioni, vedere [Tipi di runbook di Automazione di Azure](automation-runbook-types.md)
-  Per altre informazioni sulla funzionalità di supporto degli script PowerShell, vedere il blog [Announcing Native PowerShell Script Support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) (Annuncio del supporto di script PowerShell nativi in Automazione di Azure)
-  Per altre informazioni sulla registrazione e sull'output dei runbook, vedere [Output di runbook e messaggi in Automazione di Azure](automation-runbook-output-and-messages.md)
-  Per altre informazioni su un account RunAs di Azure e come usarlo per eseguire l'autenticazione dei runbook, vedere [Autenticare runbook con l'account RunAs di Azure](../automation/automation-sec-configure-azure-runas-account.md)

<!---HONumber=AcomDC_0720_2016-->