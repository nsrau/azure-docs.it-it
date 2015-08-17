<properties
   pageTitle="Funzionalità Hybrid Runbook Workers di Automazione di Azure"
   description="Questo articolo fornisce informazioni sull'installazione e l'uso di Hybrid Runbook Workers, una funzionalità di Automazione di Azure che consente di eseguire i runbook nei computer del data center locale."
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
   ms.date="07/10/2015"
   ms.author="bwren" />

# Funzionalità Hybrid Runbook Workers di Automazione di Azure

I runbook in Automazione di Azure non possono accedere alle risorse nel data center locale perché vengono eseguiti nel cloud di Azure. La funzionalità Hybrid Runbook Workers di Automazione di Azure consente di eseguire i runbook su computer posizionati nel data center per gestire le risorse locali. I Runbook vengono infatti archiviati e gestiti in Automazione di Azure e quindi distribuiti a uno o più computer locali in cui vengono eseguiti.

Questa funzionalità è illustrata nella figura seguente.

![Panoramica di Hybrid Runbook Workers](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-overview.png)

Grazie a tale funzionalità, è possibile designare uno o più computer del data center come computer di lavoro runbook ibridi ed eseguire i runbook da Automazione di Azure. Ogni computer di lavoro richiede l'agente di gestione Microsoft con una connessione ad Azure Operational Insights e all'ambiente runbook di Automazione di Azure. Operational Insights viene usato solo per installare e gestire l'agente di gestione e per monitorare la funzionalità del computer di lavoro. La distribuzione dei runbook e l'istruzione per eseguirli vengono gestite mediante Automazione di Azure.

![Componenti di Hybrid Runbook Workers](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-components.png)

Non sono previsti requisiti di firewall in ingresso per supportare Hybrid Runbook Workers. L'agente nel computer locale avvia tutte le comunicazioni con Automazione di Azure nel cloud. Quando un runbook viene avviato, Automazione di Azure crea un'istruzione che viene recuperata dall'agente, che quindi effettua il pull del runbook e di tutti i parametri prima dell'esecuzione, oltre a recuperare da Automazione di Azure gli eventuali [asset](http://msdn.microsoft.com/library/dn939988.aspx) usati dal runbook.

## Gruppi di computer di lavoro runbook ibridi

Ogni computer di lavoro runbook ibrido è un membro di un gruppo di computer di lavoro runbook ibridi che è possibile specificare quando si installa l'agente. Un gruppo può includere un solo agente, ma è possibile installarvi più agenti per garantire una disponibilità elevata.

Quando si avvia un runbook in un computer di lavoro runbook ibrido, è necessario specificare il gruppo in cui verrà eseguito. I membri del gruppo determineranno quale computer di lavoro gestirà la richiesta. Non è possibile scegliere un computer di lavoro specifico.

## Installazione di Hybrid Runbook Workers

### Preparare l'ambiente di Automazione di Azure

Per preparare l'ambiente di Automazione di Azure per Hybrid Runbook Workers, seguire questa procedura.

#### 1\. Creare un'area di lavoro di Operational Insights
Se ancora non si dispone di un'area di lavoro di Operational Insights nel proprio account Azure, crearne una seguendo le istruzioni contenute nell'articolo relativo alla [configurazione dell'area di lavoro di Operational Insights](../operational-insights-setup-workspace). Se già si dispone di un'area di lavoro, è possibile usarla.

#### 2\. Distribuire la soluzione di automazione
La soluzione di automazione in Operational Insights effettua il push dei componenti necessari per configurare e supportare l'ambiente runbook. Seguire le istruzioni contenute nell'articolo relativo alle [soluzioni Operational Insights](../operational-insights-add-solution) per installare il pacchetto di **Automazione di Azure**.

### Configurare i computer locali
Seguire questa procedura per ogni computer locale che verrà usato come computer di lavoro Runbook ibrido.


#### 1\. Installare l'agente di gestione Microsoft
L'agente di gestione Microsoft connette il computer a Operational Insights e gli consente di eseguire la logica delle soluzioni. Per installare l'agente nel computer locale e connetterlo a Operational Insights, seguire le istruzioni contenute in [Connettere i computer direttamente a Operational Insights](../operational-insights-direct-agent).

#### 2\. Installare l'ambiente runbook e connettersi ad Automazione di Azure
Quando si aggiunge un computer a Operational Insights, la soluzione di automazione effettua il push del modulo **HybridRegistration** di PowerShell, che contiene il cmdlet **Add-HybridRunbookWorker**. È possibile usare questo cmdlet per installare l'ambiente runbook nel computer e registrarlo in Automazione di Azure.

Aprire una sessione di PowerShell in modalità amministratore ed eseguire il comando seguente per importare il modulo.

	Import-Module HybridRegistration

Se si riceve un messaggio di errore che informa che non è stato possibile trovare il file del modulo, potrebbe essere necessario eseguire il comando seguente, in cui viene usato l'intero percorso del file del modulo.

	Import-Module "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomationFiles\HybridRegistration\HybridRegistration.psd1"

Eseguire quindi il cmdlet **Add-HybridRunbookWorker** con la sintassi seguente:

	Add-HybridRunbookWorker –Name <String> -EndPoint <Url> -Token <String>


- **Name** è il nome del gruppo di computer di lavoro runbook ibridi. Se il gruppo già esiste nell'account di automazione, il computer corrente vi verrà aggiunto direttamente. Se il gruppo ancora non esiste, verrà aggiunto.
- **EndPoint** è l'URL del servizio Agente. È possibile ottenerlo dal portale di anteprima di Azure nel pannello **Gestisci chiavi**.  
- **Token** è il valore **Chiave di accesso primaria** nel pannello **Gestisci chiavi**. È possibile aprire tale pannello facendo clic sull'icona della chiave nel pannello Elementi per l'account di automazione.<br><br>![Panoramica di Hybrid Runbook Workers](media/automation-hybrid-runbook-worker/elements-panel-keys.png)


#### 3\. Installare i moduli di PowerShell
I runbook possono usare tutte le attività e i cmdlet definiti nei moduli installati nell'ambiente di Automazione di Azure. Questi moduli tuttavia non vengono distribuiti automaticamente nei computer locale, pertanto è necessario installarli manualmente. L'unica eccezione è rappresentata dal modulo Azure, che viene installato per impostazione predefinita, garantendo l'accesso ai cmdlet per tutte le attività e i servizi di Azure per Automazione di Azure.

Poiché lo scopo principale della funzionalità Hybrid Runbook Workers è gestire le risorse locali, probabilmente sarà necessario installare i moduli che supportano tali risorse. Per informazioni sull'installazione dei moduli di Windows PowerShell, vedere il [relativo articolo](http://msdn.microsoft.com/library/dd878350.aspx).

## Avvio dei runbook in Hybrid Runbook Workers

L'articolo relativo all'[avvio di un runbook in Automazione di Azure](automation-starting-a-runbook.md) illustra diversi modi in cui è possibile eseguire l'avvio dei runbook. Hybrid Runbook Workers aggiunge un'opzione **RunOn** in cui è possibile specificare il nome di un gruppo di computer di lavoro runbook ibridi. Se si specifica un gruppo, il runbook verrà recuperato ed eseguito dai computer di lavoro inclusi in tale gruppo. Se non si specifica l'opzione, verrà eseguito come di consueto in Automazione di Azure.

Quando si avvia un runbook nel portale di anteprima di Azure, viene visualizzata un'opzione **Esegui in ** che consente di selezionare **Azure** o **Computer di lavoro ibrido**. Se si seleziona **Computer di lavoro ibrido**, sarà quindi possibile selezionare il gruppo da un elenco a discesa.

Usare il parametro **RunOn**. È possibile eseguire il comando seguente per avviare un runbook denominato Test-Runbook in un gruppo di computer di lavoro runbook ibridi denominato MyHybridGroup usando Windows PowerShell.

	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"

>[AZURE.NOTE]Il parametro **RunOn** è stato aggiunto al cmdlet **Start-AzureAutomationRunbook** nella versione 0.9.1 di Microsoft Azure PowerShell. È consigliabile [scaricare la versione più recente](http://azure.microsoft.com/downloads) se la versione installata è precedente. È sufficiente installare questa versione nella workstation in cui si avvierà il runbook da Windows PowerShell. Non è necessario installarla nel computer di lavoro, a meno che non si intenda avviare i runbook da tale computer. Non è attualmente possibile avviare un runbook in un computer di lavoro runbook ibrido da un altro runbook, in quanto nell'account di automazione dovrebbe essere installata la versione più recente di Azure PowerShell. Tale versione verrà aggiornata automaticamente in Automazione di Azure e verrà presto inviata automaticamente tramite push ai computer di lavoro.


## Creazione dei runbook per Hybrid Runbook Workers

Non esiste alcuna differenza nella struttura dei runbook che vengono eseguiti in Automazione di Azure e di quelli eseguiti in Hybrid Runbook Workers. I runbook usati nell'uno o nell'altro caso saranno tuttavia molto diversi perché i runbook per Hybrid Runbook Workers in genere gestiranno le risorse locali nel data center, mentre i runbook in Automazione di Azure di solito gestiscono le risorse nel cloud di Azure.

### Autorizzazioni per i runbook

I runbook verranno eseguiti nel contesto dell'account di sistema locale nel computer di lavoro runbook ibrido, pertanto devono autenticarsi per le risorse a cui accederanno. Non possono usare la stessa [modalità adottata in genere per i runbook che si autenticano per le risorse di Azure](automation-configuring.md#configuring-authentication-to-azure-resources), in quanto accedono a risorse esterne ad Azure.

Nel runbook è possibile usare asset di tipo [credenziale](http://msdn.microsoft.com/library/dn940015.aspx) e [certificato](http://msdn.microsoft.com/library/dn940013.aspx) con cmdlet che consentono di specificare le credenziali in modo da poter eseguire l'autenticazione per risorse diverse. L'esempio seguente illustra una parte di un runbook che riavvia un computer. Recupera le credenziali da un asset di tipo credenziale e il nome del computer da un asset di tipo variabile e quindi usa questi valori con il cmdlet Restart-Computer.

	$Cred = Get-AutomationCredential "MyCredential"
	$Computer = Get-AutomationVariable "ComputerName"

	Restart-Computer -ComputerName $Computer  -Credential $Cred

È anche possibile usare [InlineScript](automation-powershell-workflow.md#inline-script), che consentirà di eseguire blocchi di codice in un altro computer con le credenziali specificate mediante il [parametro comune PSCredential](http://technet.microsoft.com/library/jj129719.aspx).


### Creazione e testing dei runbook

È possibile modificare un runbook per Hybrid Runbook Workers in Automazione di Azure, ma si potrebbero incontrare difficoltà se si tenta di testare il runbook nell'editor. I moduli di PowerShell che accedono alle risorse locali potrebbero non essere installati nell'ambiente di Automazione di Azure e in questo caso il test avrebbe esito negativo. Se si sceglie di installare i moduli necessari, il runbook verrà eseguito, ma non sarà in grado di accedere alle risorse locali per un test completo.

## Relazione con Service Management Automation

[Service Management Automation (SMA)](https://technet.microsoft.com/library/dn469260.aspx) è un componente di Windows Azure Pack (WAP) che consente di eseguire nel data center locale gli stessi runbook supportati da Automazione di Azure. A differenza di Automazione di Azure, SMA richiede un'installazione locale che includa il portale di gestione di Windows Azure Pack e un database per contenere i runbook e la configurazione SMA. Automazione di Azure offre questi servizi nel cloud e richiede solo di gestire i computer di lavoro runbook ibridi nell'ambiente locale.

Gli utenti SMA già esistenti possono spostare i runbook in Automazione di Azure affinché vengano usati con Hybrid Runbook Workers senza alcuna modifica, presupponendo che eseguano la propria autenticazione per le risorse come illustrato in [Creazione dei runbook per Hybrid Runbook Workers](#creating-runbooks-for-hybrid-runbook-worker). I runbook in SMA vengono eseguiti nel contesto dell'account del servizio nel server di lavoro, che può garantire tale autenticazione per i runbook.

Per determinare se per le proprie esigenze sia più opportuno ricorrere ad Automazione di Azure con Hybrid Runbook Workers o a Service Management Automation, è possibile usare i criteri seguenti.

- SMA richiede un'installazione locale di Windows Azure Pack che prevede risorse locali e costi di manutenzione superiori rispetto ad Automazione di Azure, che richiede solo l'installazione di un agente nei computer di lavoro runbook locali. Gli agenti vengono gestiti da Operational Insights, riducendo ulteriormente i costi di manutenzione.
- Automazione di Azure archivia i runbook nel cloud e li distribuisce ai computer di lavoro Runbook ibridi in locale. Se i criteri di sicurezza non consentono questo comportamento, è consigliabile usare SMA.
- Windows Azure Pack è disponibile come download gratuito, mentre per Automazione di Azure si potrebbe incorrere in spese di sottoscrizione. Azure. È necessario gestire più database per SMA.
- Automazione di Azure con Hybrid Runbook Workers consente di gestire i runbook per le risorse cloud e le risorse locali in un'unica posizione invece di avere una gestione separata per Automazione di Azure e SMA.
- Automazione di Azure offre funzionalità avanzate, tra cui la creazione grafica, non disponibili in SMA.


## Articoli correlati

- [Avvio di un runbook in Automazione di Azure](automation-starting-a-runbook.md)
- [Modifica di un runbook in Automazione di Azure](https://msdn.microsoft.com/library/dn879137.aspx)
 

<!---HONumber=August15_HO6-->