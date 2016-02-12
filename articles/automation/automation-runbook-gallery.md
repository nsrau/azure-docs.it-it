<properties 
	pageTitle="Raccolte di runbook e moduli per Automazione di Azure | Microsoft Azure"
	description="I runbook e i moduli di Microsoft e della community sono disponibili per l'installazione e l'uso nell'ambiente di Automazione di Azure. In questo articolo viene descritto come accedere a queste risorse e come contribuire alla raccolta di runbook."
	services="automation"
	documentationCenter=""
	authors="bwren"
	manager="stevenka"
	editor="tysonn" />
<tags 
	ms.service="automation"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="02/03/2016"
	ms.author="bwren" />


# Raccolte di runbook e moduli per l'automazione di Azure

Anziché creare runbook e moduli personalizzati in Automazione di Azure, è possibile accedere a una serie di soluzioni già create da Microsoft e dalla community. È possibile usare questi soluzione senza alcuna modifica oppure usarle come punto di partenza e modificarle in base a esigenze specifiche.

È possibile ottenere runbook dalla [raccolta di runbook](#runbooks-in-runbook-gallery) e i moduli da [PowerShell Gallery](#modules-in-powerShell-gallery). È inoltre possibile contribuire alla community condividendo soluzioni sviluppate.

## Runbook nella raccolta di runbook

La [raccolta di runbook](http://gallery.technet.microsoft.com/scriptcenter/site/search?f[0].Type=RootCategory&f[0].Value=WindowsAzure&f[1].Type=SubCategory&f[1].Value=WindowsAzure_automation&f[1].Text=Automation) offre un'ampia gamma di runbook di Microsoft e della community che è possibile importare in Automazione di Azure. È possibile scaricare un runbook dalla raccolta disponibile nello [Script Center di TechNet](http://gallery.technet.microsoft.com/) oppure importare runbook direttamente dalla raccolta dal portale di Azure e dal portale di anteprima di Azure.

È possibile importare solo direttamente dalla raccolta di runbook usando il portale di Azure o il portale di anteprima di Azure. Non è possibile eseguire questa funzione tramite Windows PowerShell.

>[AZURE.NOTE] È necessario convalidare il contenuto di qualsiasi runbook ottenuto dalla raccolta di runbook e prestare particolare attenzione durante la loro installazione ed esecuzione in un ambiente di produzione. |

### Per importare un runbook dalla raccolta di runbook con il portale di Azure

1. Nel portale di gestione di Azure fare clic su **Nuovo**, **Servizi app**, **Automazione**, **Runbook**, **Da raccolta**.
2. Selezionare una categoria per visualizzare i runbook correlati e quindi selezionare un runbook per visualizzarne i dettagli. Dopo aver selezionato il runbook desiderato, fare clic sul pulsante con la freccia destra.<br> ![Raccolta di runbook](media/automation-runbook-gallery/runbook-gallery.png)
3. Esaminare il contenuto del runbook e prendere nota di eventuali requisiti nella descrizione. Al termine, fare clic sul pulsante con la freccia destra.
4. Immettere i dettagli del runbook e quindi fare clic sul pulsante con il segno di spunta. Il nome di runbook è già specificato.
5. Il runbook verrà visualizzato nella scheda **Runbook** per l'account di automazione.

### Per importare un runbook dalla raccolta di runbook con il portale di anteprima di Azure

1. Nel portale di anteprima di Azure aprire l'account di automazione. 
2. Fare clic sul riquadro **Runbook** per aprire l'elenco dei runbook.
3. Fare clic sul pulsante **Sfoglia raccolta**. <br> ![Pulsante Sfoglia raccolta](media/automation-runbook-gallery/browse-gallery-button.png)
4. Individuare l'elemento della raccolta desiderato e selezionarlo per visualizzarne i dettagli. <br> ![Sfoglia raccolta](media/automation-runbook-gallery/browse-gallery.png)
4. Fare clic su **Visualizza progetto di origine** per visualizzare l'elemento nello [Script Center di TechNet](http://gallery.technet.microsoft.com/).
5. Per importare un elemento, fare clic su di esso per visualizzarne i dettagli e quindi scegliere il pulsante **Importa**.<br> ![Pulsante Importa](media/automation-runbook-gallery/gallery-item-detail.png)
6. Facoltativamente, modificare il nome del runbook e quindi fare clic su **OK** per importare il runbook.
5. Il runbook verrà visualizzato nella scheda **Runbook** per l'account di automazione.


### Aggiunta di un runbook alla raccolta di runbook

Microsoft consiglia di aggiungere alla raccolta dei runbook i runbook ritenuti più utili per gli altri clienti. È possibile aggiungere un runbook [caricandolo nello Script Center](http://gallery.technet.microsoft.com/site/upload), a condizione che si considerino i seguenti dettagli.

- È necessario specificare *Windows Azure* nel campo **Categoria** e *Automazione* nel campo **Sottocategoria** per il runbook da visualizzare nella procedura guidata.  

- Il caricamento deve interessare un singolo file con estensione PS1 o GRAPHRUNBOOK. Se il runbook richiede eventuali moduli, runbook figlio o asset, è consigliabile elencare questi elementi nella descrizione dell'invio e nella sezione dei commenti del runbook. Se si dispone di una soluzione che richiede più runbook, caricare ciascuna soluzione separatamente ed elencare i nomi dei runbook correlati in ognuna delle relative descrizioni. Assicurarsi di usare gli stessi tag in modo che vengano visualizzati nella stessa categoria. Un utente dovrà fare riferimento alla descrizione per sapere se sono necessari altri runbook per il corretto funzionamento della soluzione.

- Inserire un frammento di codice di PowerShell o di un flusso di lavoro PowerShell nella descrizione mediante l’icona **Inserisci sezione di codice**.

- Nei risultati della raccolta di runbook verrà visualizzato il riepilogo del caricamento. Sarà pertanto necessario fornire informazioni dettagliate che consentano a un utente di identificare le funzionalità del runbook.

- È consigliabile assegnare da uno a tre dei seguenti tag al caricamento. Il runbook verrà elencato nella procedura guidata nelle categorie corrispondenti ai relativi tag. Qualsiasi tag non incluso nell'elenco verrà ignorato dalla procedura guidata. Se non si specifica alcun tag corrispondente, il runbook verrà elencato nella categoria Altro.

 - Backup
 - Capacity Management
 - Controllo modifiche
 - Conformità
 - Ambiente di testing/sviluppo
 - Ripristino di emergenza
 - Monitoraggio
 - Applicazione di patch
 - Provisioning
 - Correzione
 - Gestione del ciclo di vita VM


- L'automazione aggiorna la raccolta una volta all'ora. Pertanto i contributi non verranno visualizzati immediatamente. Se dopo un'ora il runbook non viene visualizzato nella raccolta, verificare i requisiti descritti nella sezione [Aggiunta di un runbook alla raccolta di runbook](#AddRunbook).

## Moduli in PowerShell Gallery

I moduli di PowerShell contengono cmdlet che è possibile usare all'interno dei runbook, mentre in [PowerShell Gallery](http://www.powershellgallery.com) sono disponibili moduli esistenti che è possibile installare in Automazione di Azure. È possibile avviare questa raccolta dal portale di anteprima di Azure e installarli direttamente in Automazione di Azure oppure è possibile scaricarli e installarli manualmente. Non è possibile installare i moduli direttamente dal portale di Azure, ma è possibile scaricarli e installarli come qualsiasi altro modulo.

### Per importare un modulo da PowerShell Gallery con il portale di anteprima di Azure

1. Nel portale di anteprima di Azure aprire l'account di automazione. 
2. Fare clic nel riquadro **Asset** per aprire l'elenco di asset.
3. Fare clic nel riquadro **Moduli** per aprire l'elenco di moduli.
3. Fare clic sul pulsante **PowerShell Gallery** per avviare la raccolta di PowerShell in un'altra finestra del browser. <br> ![PowerShell Gallery](media/automation-runbook-gallery/powershell-gallery-button.png)
4. Fare clic sul menu **Moduli** per accedere all'elenco dei moduli disponibili.<br> ![Pulsante PowerShell Gallery](media/automation-runbook-gallery/powershell-gallery.png)
4. Cercare il modulo desiderato e selezionarlo per visualizzarne i dettagli.
5. Per installare il modulo direttamente in Automazione di Azure, fare clic sul pulsante **Distribuisci in Automazione di Azure**.<br> ![Pulsante PowerShell Gallery](media/automation-runbook-gallery/powershell-gallery-detail.png)
6. Il portale di anteprima di Azure viene visualizzato in un riquadro **Distribuzione personalizzata**. Specificare se si installerà il modulo in un **account di automazione nuovo o esistente** e immettere il **nome dell'account di automazione**. Se si usa un account esistente, la **posizione dell'account di automazione** viene ignorata. 
7. Selezionare **Gruppo di risorse** e specificare un gruppo di risorse esistente o crearne uno nuovo per il modulo.
6. È necessario selezionare **Note legali** e fare clic su **Acquista**. Si noti che nonostante il nome di questo pulsante non viene effettivamente addebitato alcun costo per l'installazione di un modulo.
7. Per importare il modulo, fare clic su **Crea**. Poiché ogni attività deve essere estratta potrebbero volerci alcuni minuti.  
8. Si riceverà una notifica indicante che è in corso la distribuzione del modulo e quindi un'altra notifica quando l'operazione è completata. 


## Richiesta di un runbook o un modulo

È possibile inviare richieste a [User Voice](https://feedback.azure.com/forums/246290-azure-automation/). Se è necessario supporto per la scrittura di un runbook o per inviare domande relative a PowerShell, inserire una domanda nel [forum](http://social.msdn.microsoft.com/Forums/windowsazure/it-IT/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## Articoli correlati

- [Creazione o importazione di un runbook in Automazione di Azure](automation-creating-importing-runbook.md)
- [Informazioni sul flusso di lavoro PowerShell](automation-powershell-workflow.md)

<!---HONumber=AcomDC_0204_2016-->