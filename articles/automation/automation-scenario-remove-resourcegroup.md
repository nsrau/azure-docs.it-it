<properties
    pageTitle="Automatizzare la rimozione di gruppi di risorse | Microsoft Azure"
    description="Versione del flusso di lavoro PowerShell di uno scenario di Automazione di Azure che include runbook per rimuovere tutti i gruppi di risorse nella sottoscrizione."
    services="automation"
    documentationCenter=""
    authors="MGoedtel"
    manager="jwhit"
    editor=""
	/>  
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="magoedte"/>  

# Scenario di Automazione di Azure: automatizzare la rimozione di gruppi di risorse

Molti clienti creano più gruppi di risorse, alcuni dei quali sono dedicati alla gestione di applicazioni di produzione e altri sono ambienti di staging, di test e di sviluppo. La rimozione di un gruppo di risorse con un clic è più complessa rispetto all'automazione della distribuzione di queste risorse. L'uso di Automazione per gestirla rientra tra i casi d'uso tipici ed è una grande opportunità per semplificare un'attività di gestione così comune. È utile anche se si usa una sottoscrizione di Azure per cui è previsto un limite di spesa tramite un'offerta riservata ai membri, ad esempio MSDN o il programma Microsoft Partner Network Cloud Essentials.

Questo scenario si basa su un runbook di PowerShell ed è progettato per rimuovere uno o più gruppi di risorse specificati dalla sottoscrizione. Per impostazione predefinita, il runbook supporta il test prima di iniziare. In questo modo non si rischia di eliminarlo per errore senza essere del tutto certi che si è pronti a completare questa procedura.

## Come ottenere lo scenario

Questo scenario è costituito da un runbook di PowerShell che è possibile scaricare da [PowerShell Gallery](https://www.powershellgallery.com/packages/Remove-ResourceGroup/1.0/DisplayScript) o importare direttamente dalla [raccolta di runbook](automation-runbook-gallery.md) nel portale di Azure.<br><br>

Runbook | Descrizione|
----------|------------|
Remove-ResourceGroup | Rimuove uno o più gruppi di risorse di Azure e le relative risorse dalla sottoscrizione.  
<br> Per questo runbook vengono definiti i parametri di input seguenti:

Parametro | Descrizione|
----------|------------|
NameFilter (obbligatorio) | Consente di specificare un filtro basato sui nomi per limitare i gruppi di risorse che si vuole eliminare. Per passare più valori, è possibile usare un elenco delimitato da virgole.<br>Il filtro non rileva la distinzione tra maiuscole e minuscole e troverà la corrispondenza con tutti i gruppi di risorse contenenti la stringa.|
PreviewMode (facoltativo) | Eseguire il runbook per vedere quali gruppi di risorse verrebbero eliminati, ma non eseguire alcuna azione.<br>Il valore predefinito è **true** che consente di evitare l'eliminazione accidentale di uno o più gruppi di risorse passati al runbook.  

## Installazione e configurazione dello scenario

### Prerequisiti

Questo runbook esegue l'autenticazione con l'[account RunAs di Azure](automation-sec-configure-azure-runas-account.md).

### Installare e pubblicare i runbook

Dopo aver scaricato il runbook, è possibile importarlo usando la procedura descritta tra quelle di [importazione di un runbook](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-Azure-Automation). Pubblicare il runbook dopo averlo importato correttamente nell'account di automazione.


## Uso del runbook

I passaggi seguenti illustrano l'esecuzione di questo runbook e consentono di acquisire familiarità con il suo funzionamento. In questo esempio verrà semplicemente testato il runbook, il gruppo di risorse non verrà realmente eliminato.

1. Dal portale di Azure aprire l'account di Automazione e fare clic sul riquadro **Runbook**.
2. Selezionare il runbook **Remove-ResourceGroup** e fare clic su **Avvia**.
3. Quando si avvia il runbook, viene aperto il pannello **Avvia runbook** ed è possibile configurare i valori seguenti per i parametri. Immettere il nome di uno o più gruppi di risorse della sottoscrizione di cui si vuole eseguire il test. Un'eventuale eliminazione accidentale non creerà problemi.<br> ![Parametri di Remove-ResouceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-input-parameters.png)
    
    >[AZURE.NOTE] Verificare che l'opzione **Previewmode** sia impostata su **true** per evitare di eliminare uno o più gruppi di risorse selezionati. **Si noti** che questo runbook non rimuoverà il gruppo di risorse contenente l'account di Automazione che esegue il runbook stesso.

4. Dopo avere configurato i valori di tutti i parametri, fare clic su **OK** e il runbook verrà inserito in coda per l'esecuzione.

Per visualizzare i dettagli del processo del runbook **Remove-ResourceGroup** nel portale di Azure, selezionare il riquadro **Processi** del runbook. Nel riepilogo del processo saranno visualizzati i parametri di input e il flusso di output, oltre alle informazioni generali sul processo e alle eventuali eccezioni che si sono verificate.<br> ![Stato del processo del runbook Remove-ResourceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-status.png).

Il **Riepilogo processi** include i messaggi dai flussi di output, di avviso e di errore. Selezionare il riquadro **Output** per visualizzare i risultati dettagliati dell'esecuzione del runbook.<br>![Risultati di output del runbook Remove-ResourceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-output.png)

## Passaggi successivi

- Per iniziare a creare il runbook, vedere [Creazione o importazione di un runbook in Automazione di Azure](automation-creating-importing-runbook.md)
- Per iniziare a usare runbook del flusso di lavoro PowerShell, vedere [Il primo runbook del flusso di lavoro PowerShell](automation-first-runbook-textual.md).

<!---HONumber=AcomDC_0928_2016-->