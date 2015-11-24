<properties
   pageTitle="Configurazione di Automazione di Azure"
   description="Illustra i passaggi da eseguire per configurare Automazione di Azure nelle fasi iniziali."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/10/2015"
   ms.author="bwren" />

# Configurazione di Automazione di Azure

Questo articolo illustra le azioni da eseguire per iniziare a usare Automazione di Azure.

## Account di Automazione

Al primo avvio di Automazione di Azure sarà necessario creare almeno un account di Automazione. Gli account di Automazione consentono di isolare le risorse di Automazione (runbook, asset, configurazioni) dalle risorse di Automazione contenute in altri account di Automazione. È possibile usare gli account di Automazione per separare le risorse di Automazione in ambienti logici distinti. Ad esempio, è possibile usare un account per lo sviluppo e un altro per la produzione.

Le risorse di Automazione per ogni account di Automazione sono associate a una singola area di Azure, ma gli account di Automazione possono gestire servizi di Azure in qualsiasi area. Il motivo principale per cui creare gli account di Automazione in aree diverse è la presenza di criteri che richiedono dati e risorse per essere isolati in un'area specifica.

>[AZURE.NOTE]Gli account di automazione e le risorse che contengono, creati con il portale di anteprima di Azure, non sono accessibili nel portale di Azure. Se si vogliono gestire questi account o le relative risorse con Windows PowerShell, è necessario usare i moduli di Gestione risorse di Azure.
>
>Gli account di automazione creati con il portale di Azure possono essere gestiti da entrambi i portali e set di cmdlet. Una volta creato l'account, non fa alcuna differenza come si creano e gestiscono le risorse all'interno dell'account. Se si prevede di continuare a usare il portale di Azure, è necessario usarlo invece del portale di anteprima di Azure per creare gli account di automazione.


Un account di Automazione può essere sospeso se si verifica un problema con l'account Azure, ad esempio un pagamento scaduto. In tal caso, non sarà possibile accedere all'account, tutti i lavori in esecuzione verranno sospesi e tutte le pianificazione verranno disabilitate. Sarà possibile visualizzare l'account ma non le risorse in esso contenute. Dopo avere risolto il problema e abilitato l'account di Automazione, sarà necessario abilitare le pianificazioni e riavviare tutti i Runbook sospesi.


## Configurazione dell'autenticazione per le risorse di Azure

Quando si accede alle risorse di Azure tramite i [cmdlet di Azure](http://msdn.microsoft.com/library/azure/jj554330.aspx), è necessario eseguire l'autenticazione della sottoscrizione di Azure. In Automazione di Azure questa operazione viene in genere effettuata con un account aziendale in Azure Active Directory configurato come amministratore della sottoscrizione. È quindi possibile creare le [credenziali](http://msdn.microsoft.com/library/dn940015.aspx) di questo account utente e usarle con [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) nel Runbook.

>[AZURE.NOTE]Non è possibile usare gli account Microsoft, precedentemente noti come LiveID, con Automazione di Azure.

## Per creare un nuovo utente di Azure Active Directory per gestire una sottoscrizione di Azure

1. Accedere al portale di Azure come amministratore del servizio per la sottoscrizione di Azure da gestire.
2. Selezionare **Active Directory**
3. Selezionare il nome di directory associato alla sottoscrizione di Azure. Se necessario, è possibile cambiare l'associazione in **Impostazioni > Sottoscrizioni > Modifica directory**.
4. [Creare un nuovo utente di Active Directory](http://msdn.microsoft.com/library/azure/hh967632.aspx). Selezionare **Nuovo utente nell'organizzazione** per **Tipo di utente** e non selezionare **Abilita Multi-Factor Authentication**.
5. Prendere nota del nome completo dell'utente e della password temporanea.
7. Selezionare **Impostazioni -> Amministratori > Aggiungi**.
8. Digitare il nome completo dell'utente creato.
9. Selezionare la sottoscrizione che si vuole far gestire dall'utente.
10. Disconnettersi da Azure e ripetere l'accesso con l'account appena creato. Verrà chiesto di cambiare la password dell'utente.
11. Creare un nuovo [asset credenziali di Automazione di Azure](http://msdn.microsoft.com/library/dn940015.aspx) per l'account utente creato. Il **Tipo di credenziali** deve essere **Credenziali per Windows PowerShell**.

## Creare un account di automazione

Un account di automazione è un contenitore per le risorse di Automazione di Azure. Fornisce un modo per separare gli ambienti o organizzare ulteriormente i flussi di lavoro. Se è già stato creato un account di automazione, è possibile ignorare questo passaggio.

1. Accedere al [portale di anteprima di Azure](https://portal.azure.com/).

2. Nel portale di anteprima di Azure fare clic su **Nuovo** > **Gestione** > **Account di automazione**

3. Nel pannello **Aggiungi un nuovo account di automazione** configurare i dettagli dell'account di automazione.

>[AZURE.NOTE]Quando si crea un account di automazione tramite il portale di anteprima di Azure, l'account e tutte le risorse associate non vengono riportati nel portale di gestione classico.

Ecco l'elenco dei parametri da configurare:

|Parametro |Descrizione |
|:---|:---|
| Nome | Nome dell'Account di automazione, deve essere un valore univoco. |
| Gruppo di risorse | I gruppi di risorse semplificano la visualizzazione e la gestione delle risorse di Azure correlate. Nel portale di anteprima di Azure è possibile scegliere un gruppo di risorse esistente o crearne uno nuovo per l'account di automazione, mentre nel portale di gestione di Azure tutti gli account di automazione vengono inseriti in un gruppo di risorse predefinito. |
| Sottoscrizione | Scegliere una sottoscrizione dall'elenco delle sottoscrizioni disponibili. |
| Area | L'area indica la posizione in cui verranno archiviate le risorse di automazione incluse nell'account. È possibile scegliere un'area qualsiasi dall'elenco. Questa operazione non influisce sulla funzionalità dell'account, ma consente di eseguire più velocemente i runbook se l'area dell'account è vicina a quella in cui sono archiviate altre risorse di Azure. |
| Opzioni dell'account | Questa opzione consente di scegliere quali risorse verranno create nel nuovo account di automazione. Se si seleziona **Sì** verrà creato un runbook dell'esercitazione. |

![Creazione di un account](media/automation-configuration/automation-01-create-automation-account.png)

>[AZURE.NOTE]Quando un account di automazione creato con il portale di gestione classico viene [spostato in un gruppo di risorse diverso](../resource-group-move-resources.md) usando il portale di anteprima di Azure, l'account di automazione non sarà più disponibile nel portale di Azure classico, perché in quest'ultimo gli account di Gestione risorse di Azure non sono supportati.



## Usare le credenziali in un Runbook

È possibile recuperare le credenziali in un Runbook tramite l'attività [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) e quindi usarle con [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) per connettersi alla sottoscrizione di Azure. Se le credenziali appartengono a un amministratore di più sottoscrizioni di Azure, occorre usare anche [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) per specificare la sottoscrizione corretta. L'esempio di Windows PowerShell seguente illustra il codice presente in genere all'inizio della maggior parte dei Runbook di automazione di Azure.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
	Add-AzureAccount –Credential $cred
	Select-AzureSubscription –SubscriptionName "My Subscription"

È necessario ripetere queste righe dopo ogni [checkpoints](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) del Runbook. Se il Runbook è sospeso e quindi ripreso da un altro thread di lavoro, sarà necessario eseguire di nuovo l'autenticazione.

## Articoli correlati
- [Automazione di Azure: Autenticazione in Azure tramite Azure Active Directory](http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/)
 

<!---HONumber=Nov15_HO4-->