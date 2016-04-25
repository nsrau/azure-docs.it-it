<properties
   pageTitle="Aggiungere runbook di Automazione di Azure ai piani di ripristino | Microsoft Azure"
   description="In questo articolo viene descritto come Azure Site Recovery consente ora di estendere i piani di ripristino tramite Automazione di Azure per completare attività complesse durante il ripristino in Azure"
   services="site-recovery"
   documentationCenter=""
   authors="ruturaj"
   manager="mkjain"
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="powershell"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required"
   ms.date="12/14/2015"
   ms.author="ruturajd@microsoft.com"/>


# Aggiungere runbook di automazione di Azure ai piani di ripristino


In questa esercitazione viene descritto come Azure Site Recovery si integra con Automazione di Azure per fornire estendibilità ai piani di ripristino. I piani di ripristino possono gestire il ripristino delle macchine virtuali protette tramite Azure Site Recovery per la replica nel cloud secondario e la replica negli scenari di Azure. Consentono inoltre di effettuare il ripristino **costantemente accurato**, **ripetibile** e **automatizzato**. Se viene eseguito il failover delle macchine virtuali in Azure, l’integrazione con Automazione di Azure si estende ai piani di ripristino e offre la possibilità di eseguire i runbook, consentendo in tal modo attività di automazione efficaci.

Se non si è ancora sentito parlare di Automazione di Azure, effettuare l'iscrizione [qui](https://azure.microsoft.com/services/automation/) e scaricare gli script di esempio [qui](https://azure.microsoft.com/documentation/scripts/). Ulteriori informazioni su [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) e su come gestire il ripristino in Azure usando piani di ripristino sono disponibili [qui](https://azure.microsoft.com/blog/?p=166264).

In questa esercitazione, verrà illustrato come integrare i runbook di Automazione Azure nei piani di ripristino. Verranno automatizzate attività semplici che in precedenza richiedevano un intervento manuale e verrà illustrato come convertire un ripristino in più passaggi in un'azione di ripristino a singolo clic. Verrà esaminato inoltre come risolvere eventuali problemi di un semplice script.

## Proteggere l'applicazione in Azure

Si inizierà con una semplice applicazione costituita da due macchine virtuali. In questo caso, viene usata un'applicazione HRweb di Fabrikam. Fabrikam-HRweb-frontend e Fabrikam-Hrweb-backend sono le due macchine virtuali protette in Azure tramite Azure Site Recovery. Per proteggere le macchine virtuali tramite Azure Site Recover, attenersi alla procedura seguente.

1.  Abilitare la protezione per le macchine virtuali.

2.  Assicurarsi che le macchine virtuali abbiano completato la replica iniziale e stiano eseguendo la replica.

3.  Attendere che la replica iniziale venga completata e che lo stato della replica sia Protetto.

![](media/site-recovery-runbook-automation/01.png)
---------------------

In questa esercitazione si creerà un piano di ripristino per l'applicazione Fabrikam HRweb per eseguire il failover dell'applicazione in Azure. Il piano di ripristino verrà quindi integrato con un runbook che creerà un endpoint sulla macchina virtuale di Azure sottoposta a failover, per utilizzare le pagine web sulla porta 80.

Innanzitutto, verrà creato un piano di ripristino per l’applicazione.

## Creare il piano di ripristino

Per ripristinare l'applicazione in Azure, è necessario creare un piano di ripristino. Usando un piano di ripristino, è possibile specificare l'ordine di ripristino delle macchine virtuali. La macchina virtuale inserita nel gruppo 1 verrà ripristinata e avviata per prima e quindi seguirà la macchina virtuale nel gruppo 2.

Creare un piano di ripristino simile al seguente.

![](media/site-recovery-runbook-automation/12.png)

Per ulteriori informazioni sui piani di ripristino, leggere la documentazione [qui](https://msdn.microsoft.com/library/azure/dn788799.aspx "qui").

Quindi, verranno creati gli elementi necessari in Automazione di Azure.

## Creare l'account di automazione e i relativi asset

Per creare i runbook è necessario un account di Automazione di Azure. Se non si dispone già di un account, passare alla scheda Automazione di Azure identificata da ![](media/site-recovery-runbook-automation/02.png)e creare un nuovo account.

1.  Assegnare all'account un nome con cui identificarlo.

2.  Specificare un'area geografica in cui si desidera inserire l'account.

Si consiglia di inserire l'account nella stessa area dell’insieme di credenziali per il ripristino automatico di sistema.

![](media/site-recovery-runbook-automation/03.png)

Successivamente, creare gli asset seguenti nell’account.

### Aggiungere un nome di sottoscrizione come asset

1.  Aggiungere una nuova impostazione ![](media/site-recovery-runbook-automation/04.png) negli asset di Automazione di Azure e selezionare ![](media/site-recovery-runbook-automation/05.png)

2.  Selezionare **String** come tipo di variable

3.  Specificare **AzureSubscriptionName** come nome della variabile

    ![](media/site-recovery-runbook-automation/06.png)

4.  Specificare il nome effettivo della sottoscrizione di Azure come valore della variabile.

	![](media/site-recovery-runbook-automation/07_1.png)

È possibile identificare il nome della sottoscrizione dalla pagina delle impostazioni dell'account nel portale di Azure.

### Aggiungere una credenziale di accesso di Azure come asset

Automazione di Azure usa Azure PowerShell per connettersi alla sottoscrizione e opera sugli elementi disponibili. A tale scopo, è necessario eseguire l'autenticazione usando l'account Microsoft o un account aziendale o dell’istituto di istruzione. È possibile archiviare le credenziali dell'account in un asset da utilizzare in modo sicuro nel runbook.

1.  Aggiungere una nuova impostazione ![](media/site-recovery-runbook-automation/04.png) negli asset di Automazione di Azure e selezionare ![](media/site-recovery-runbook-automation/09.png)

2.  Selezionare **Credenziali per Windows PowerShell** come tipo di credenziali

3.  Specificare il nome **AzureCredential**

    ![](media/site-recovery-runbook-automation/10.png)

4.  Specificare il nome utente e la password con cui effettuare l'accesso.

Entrambe queste impostazioni sono ora disponibili negli asset.

![](media/site-recovery-runbook-automation/11.png)

Ulteriori informazioni su come connettersi alla sottoscrizione tramite Powershell sono disponibili [qui](../powershell-install-configure.md).

Successivamente, verrà creato un runbook in Automazione di Azure che può aggiungere un endpoint per la macchina virtuale front-end dopo il failover.

## Contesto di Automazione di Azure

Il ripristino automatico di sistema passa una variabile di contesto al runbook per consentire all’utente di scrivere script deterministici. Si potrebbe sostenere che i nomi del servizio cloud e della macchina virtuale siano prevedibili, ma ciò non si verifica sempre per determinati scenari, come quello in cui il nome della macchina virtuale potrebbe essere stato modificato a causa di caratteri non supportati in Azure. Di conseguenza, queste informazioni vengono passate al piano di ripristino automatico di sistema come parte del *contesto*.

La variabile di contesto avrà un aspetto simile a quello riportato nell’esempio seguente.

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                {"CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test"}

                }

        }


La tabella che segue contiene il nome e la descrizione di ciascuna variabile nel contesto.

**Nome variabile** | **Descrizione**
---|---
RecoveryPlanName | Nome del piano di esecuzione. Consente di agire in base al nome utilizzando lo stesso script
FailoverType | Specifica se il failover è di test, pianificato o non pianificato.
FailoverDirection | Specifica se il ripristino avviene su primario o secondario
GroupID | Identifica il numero del gruppo all'interno del piano di ripristino quando il piano è in esecuzione
VmMap | Array di tutte le macchine virtuali presenti nel gruppo.
VMMap key | Chiave univoca (GUID) per ciascuna VM. È uguale all'ID di VMM della macchina virtuale, se applicabile.
RoleName | Nome della VM di Azure in fase di ripristino
CloudServiceName | Nome servizio cloud di Azure in cui viene creata la macchina virtuale.


Per identificare la chiave VmMap nel contesto, è anche possibile andare alla pagina delle proprietà della macchina virtuale in Ripristino automatico di sistema e analizzare la proprietà VM GUID.

![](media/site-recovery-runbook-automation/13.png)

## Creare un runbook di Automazione

A questo punto creare il runbook per aprire la porta 80 nella macchina virtuale front-end.

1.  Creare un nuovo runbook nell'account di Automazione di Azure con il nome **OpenPort80**

	![](media/site-recovery-runbook-automation/14.png)

2.  Passare alla visualizzazione Autore del runbook e inserire la modalità bozza.

3.  Specificare innanzitutto la variabile da utilizzare come contesto del piano di ripristino

	```
		param (
			[Object]$RecoveryPlanContext
		)

	```

4.  Quindi, connettersi alla sottoscrizione tramite le credenziali e il nome della sottoscrizione

	```
		$Cred = Get-AutomationPSCredential -Name 'AzureCredential'

		# Connect to Azure
		$AzureAccount = Add-AzureAccount -Credential $Cred
		$AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
		Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
	```

	Qui vengono usati gli asset di Azure: **AzureCredential** e **AzureSubscriptionName**.

5.  Specificare i dettagli di endpoint e il GUID della macchina virtuale per il quale si vuole esporre l'endpoint, in questo caso la macchina virtuale front-end.

	```
		# Specify the parameters to be used by the script
		$AEProtocol = "TCP"
		$AELocalPort = 80
		$AEPublicPort = 80
		$AEName = "Port 80 for HTTP"
		$VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"
	```

	Vengono specificati il protocollo di endpoint di Azure, la porta locale nella macchina virtuale e la porta pubblica mappata. Queste variabili rappresentano i parametri richiesti dai comandi di Azure che consentono di aggiungere endpoint alle macchine virtuali. VMGUID contiene il GUID della macchina virtuale su cui è necessario operare.

6.  Lo script estrae ora il contesto per la proprietà VM GUID specificata e crea un endpoint nella macchina virtuale a cui fa riferimento.

	```
		#Read the VM GUID from the context
		$VM = $RecoveryPlanContext.VmMap.$VMGUID

		if ($VM -ne $null)
		{
			# Invoke pipeline commands within an InlineScript

			$EndpointStatus = InlineScript {
				# Invoke the necessary pipeline commands to add a Azure Endpoint to a specified Virtual Machine
				# Commands include: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including parameters)

				$Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
					Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
					Update-AzureVM
				Write-Output $Status
			}
		}
	```

7. Al termine, premere Pubblica ![](media/site-recovery-runbook-automation/20.png) per fare in modo che lo script sia disponibile per l'esecuzione.

Di seguito è riportato lo script completo per riferimento

```
  workflow OpenPort80
  {
	param (
		[Object]$RecoveryPlanContext
	)

	$Cred = Get-AutomationPSCredential -Name 'AzureCredential'

	# Connect to Azure
	$AzureAccount = Add-AzureAccount -Credential $Cred
	$AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
	Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

	# Specify the parameters to be used by the script
	$AEProtocol = "TCP"
	$AELocalPort = 80
	$AEPublicPort = 80
	$AEName = "Port 80 for HTTP"
	$VMGUID = "7a1069c6-c1d6-49c5-8c5d-33bfce8dd183"

	#Read the VM GUID from the context
	$VM = $RecoveryPlanContext.VmMap.$VMGUID

	if ($VM -ne $null)
	{
		# Invoke pipeline commands within an InlineScript

		$EndpointStatus = InlineScript {
			# Invoke the necessary pipeline commands to add an Azure Endpoint to a specified Virtual Machine
			# This set of commands includes: Get-AzureVM | Add-AzureEndpoint | Update-AzureVM (including necessary parameters)

			$Status = Get-AzureVM -ServiceName $Using:VM.CloudServiceName -Name $Using:VM.RoleName | `
				Add-AzureEndpoint -Name $Using:AEName -Protocol $Using:AEProtocol -PublicPort $Using:AEPublicPort -LocalPort $Using:AELocalPort | `
				Update-AzureVM
			Write-Output $Status
		}
	}
  }
```

## Aggiungere lo script al piano di ripristino

Quando lo script è pronto, è possibile aggiungerlo al piano di ripristino creato in precedenza.

1.  Nel piano di ripristino creato, scegliere di aggiungere uno script dopo il gruppo 2. ![](media/site-recovery-runbook-automation/15.png)

2.  Specificare un nome per lo script. Si tratta semplicemente di un nome descrittivo per lo script, per la visualizzazione all'interno del piano di ripristino.

3.  Nel failover allo script di Azure, selezionare il nome dell'account di Automazione di Azure.

4.  Nei runbook di Azure, selezionare il runbook che è stato creato.

![](media/site-recovery-runbook-automation/16.png)

## Script sul lato primario

Quando si esegue un failover in Azure, è possibile scegliere di eseguire gli script sul lato primario. Questi script verranno eseguiti nel server VMM durante il failover. Gli script sul lato primario sono disponibili solo nelle fasi precedente e successiva all’arresto. Questo perché in genere il sito primario non è disponibile in caso di emergenza. Durante un failover non pianificato, solo se si opta per le operazioni del sito primario, si tenterà di eseguire gli script sul lato primario. Se non sono raggiungibili o sono in timeout, il failover continuerà a ripristinare le macchine virtuali. Gli script sul lato primario non sono disponibili per i siti VMware/Physical/Hyper-V senza VMM protetti in Azure durante il failover in Azure. Tuttavia, se si esegue il failback da Azure in locale, gli script sul lato primario (runbook) possono essere usati per tutte le destinazioni, ad eccezione di VMware.

## Testare il piano di ripristino

Dopo avere aggiunto il runbook al piano è possibile avviare un failover di test e vederlo in azione. È sempre consigliabile eseguire un failover di test per testare l'applicazione e il piano di ripristino per assicurarsi che non siano presenti errori.

1.  Selezionare il piano di ripristino e avviare un failover di test.

2.  Durante l'esecuzione del piano, è possibile verificare se il runbook è stato eseguito o meno tramite il relativo stato.

    ![](media/site-recovery-runbook-automation/17.png)

3.  È inoltre possibile visualizzare lo stato di esecuzione dettagliato del runbook nella pagina dei processi di Automazione di Azure relativa al runbook.

    ![](media/site-recovery-runbook-automation/18.png)

4.  Dopo il failover, a parte il risultato dell'esecuzione del runbook, è possibile verificare se l'esecuzione ha avuto esito positivo o meno, visitando la pagina della macchina virtuale di Azure e analizzando gli endpoint.

![](media/site-recovery-runbook-automation/19.png)

## Script di esempio

Anche se in questa esercitazione è stata illustrata l'automazione di un’attività comunemente utilizzata di aggiunta di un endpoint a una macchina virtuale di Azure, tramite Automazione di Azure è possibile eseguire una serie di altre attività di automazione efficaci. Microsoft e la community di Automazione di Azure mettono a disposizione runbook di esempio, utili per iniziare a creare soluzioni personalizzate, nonché runbook di utilità, utili come componenti di base per attività di automazione più estese. Iniziare a usare i runbook dalla raccolta e generare piani di ripristino efficaci ad un solo clic per le applicazioni che usano Azure Site Recovery.

## Risorse aggiuntive

[Panoramica di Automazione di Azure](http://msdn.microsoft.com/library/azure/dn643629.aspx "Panoramica di Automazione di Azure")

[Script di esempio di Automazione di Azure](http://gallery.technet.microsoft.com/scriptcenter/site/search?f[0].Type=User&f[0].Value=SC%20Automation%20Product%20Team&f[0].Text=SC%20Automation%20Product%20Team "Script di esempio di Automazione di Azure")

<!---HONumber=AcomDC_0413_2016-->