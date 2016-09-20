<properties
   pageTitle="Configurare un account utente Azure AD | Microsoft Azure"
   description="Questo articolo descrive come configurare le credenziali dell'account utente Azure AD per i runbook di Automazione di Azure ai fini dell'autenticazione con risorse ARM e ASM."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="utente di Azure Active Directory, Azure Service Management, account utente Azure AD" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/12/2016"
   ms.author="magoedte" />

# Autenticare i runbook con Azure Service Management e Azure Resource Manager

Questo articolo descrive le procedure da eseguire per configurare un account utente Azure AD per i runbook di Automazione di Azure in esecuzione in risorse di Azure Service Management (ASM) o Azure Resource Manager (ARM). Nonostante si tratti di un'identità di autenticazione ancora supportata per i runbook basati su Azure Resource Manager (ARM), il metodo consigliato prevede l'utilizzo del nuovo account RunAs di Azure.

## Creare un nuovo utente di Azure Active Directory

1. Accedere al portale di Azure classico come amministratore del servizio per la sottoscrizione di Azure da gestire.
2. Selezionare **Active Directory** e quindi il nome della directory dell'organizzazione.
3. Selezionare la scheda **Utenti** e quindi nell'area di comando selezionare **Aggiungi utente**.
4. Nella pagina **Informazioni sull'utente** in **Tipo di utente** selezionare **Nuovo utente nell'organizzazione**.
5. Immettere un nome utente.
6. Selezionare il nome di directory associata alla sottoscrizione di Azure nella pagina Active Directory.
7. Nella pagina **Profilo utente** specificare nome e cognome, un nome descrittivo e un utente dall'elenco **Ruoli**. Non selezionare **Abilita Multi-Factor Authentication**.
8. Prendere nota del nome completo dell'utente e della password temporanea.
9. Selezionare **Impostazioni -> Amministratori > Aggiungi**.
10. Digitare il nome completo dell'utente creato.
11. Selezionare la sottoscrizione che si vuole far gestire dall'utente.
12. Disconnettersi da Azure e ripetere l'accesso con l'account appena creato. Verrà chiesto di cambiare la password dell'utente.


## Creare un account di Automazione nel portale di Azure classico
In questa sezione verranno eseguite le procedure seguenti per creare un nuovo account di Automazione di Azure nel portale di Azure. Questo account verrà usato con i runbook che gestiscono le risorse nelle modalità ASM e ARM.

>[AZURE.NOTE] Gli account di Automazione creati con il portale di Azure classico possono essere gestiti sia dal portale di Azure che dal portale di Azure classico e da entrambi i set di cmdlet. Una volta creato l'account, non fa alcuna differenza come si creano e gestiscono le risorse all'interno dell'account. Se si prevede di continuare a usare il portale di Azure classico, sarà necessario usare questo portale anche per creare gli account di Automazione.


1. Accedere al portale di Azure classico come amministratore del servizio per la sottoscrizione di Azure da gestire.
2. Selezionare **Automazione**.
3. Nella pagina **Automazione** selezionare **Crea un account di Automazione**.
4. Nella casella **Crea un account di Automazione** digitare un nome per il nuovo account di Automazione e selezionare un'area dall'elenco a discesa **Area**.
5. Fare clic su **OK** per accettare le impostazioni e creare l'account.
6. Al termine della creazione, verrà visualizzato nell'elenco della pagina **Automazione**.
7. Fare clic sull'account per visualizzare la pagina Dashboard.
8. Nella pagina del dashboard di Automazione selezionare **Asset**.
9. Nella pagina **Asset** selezionare l'opzione **Aggiungi impostazioni** nella parte inferiore della pagina.
10. Nella pagina **Aggiungi impostazioni** selezionare **Aggiungi credenziali**.
11. Nella pagina **Definisci credenziali** selezionare **Credenziali per Windows PowerShell** nell'elenco a discesa **Tipo di credenziali** e specificare un nome per le credenziali.
12. Nella pagina **Definisci credenziali** successiva digitare il nome utente dell'account utente Active Directory nel campo **Nome utente** e specificare la password nei campi **Password** e **Conferma password**. Fare clic su **OK** per salvare le modifiche.

## Creare un account di Automazione nel portale di Azure

In questa sezione verranno eseguite le procedure seguenti per creare un nuovo account di Automazione di Azure nel portale di Azure. Questo account verrà usato con i runbook che gestiscono le risorse nella modalità Azure Resource Manager (ARM).

1. Accedere al portale di Azure come amministratore del servizio per la sottoscrizione di Azure da gestire.
2. Selezionare **Account di automazione**.
3. Nel pannello Account di automazione fare clic su **Aggiungi**.<br>![Aggiungi account di Automazione](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties.png)
2. Nella casella **Nome** del pannello **Aggiungi account di Automazione** digitare un nome per il nuovo account di Automazione.
5. Se si hanno più sottoscrizioni, specificare quella per il nuovo account, un **Gruppo di risorse** nuovo o esistente e la **Località** per il data center di Azure.
3. Selezionare il valore **No** per l'opzione **Crea un account RunAs di Azure** e fare clic sul pulsante **Crea**.

    >[AZURE.NOTE] Se si sceglie di non creare l'account RunAs selezionando **No**, verrà visualizzato un messaggio di avviso nel pannello **Aggiungi account di Automazione**. Durante la creazione e l'assegnazione al ruolo **Collaboratore** nella sottoscrizione, l'account non ha un'identità di autenticazione corrispondente nel servizio directory delle sottoscrizioni e, di conseguenza, non ha accesso alle risorse nella sottoscrizione. Questo impedisce ai runbook che fanno riferimento a questo account di autenticarsi ed eseguire attività sulle risorse di Azure Resource Manager.

    ![Aggiungi account di Automazione, avviso](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties-error.png)

4. Mentre Azure crea l'account di Automazione, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

Al termine della creazione delle credenziali, sarà necessario creare un asset credenziali per associare l'account di Automazione all'account utente Active Directory creato in precedenza. Per ora, si è solo creato l'account di Automazione, ma questo non è associato ad alcuna identità di autenticazione. Seguire la procedura illustrata nell'articolo [Asset credenziali in Automazione di Azure](../automation/automation-credentials.md#creating-a-new-credential) e specificare il valore per il **nome utente** nel formato **dominio\\utente**.

## Usare le credenziali in un Runbook

È possibile recuperare le credenziali in un runbook usando l'attività [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) e quindi usarle con [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) per connettersi alla sottoscrizione di Azure. Se le credenziali appartengono a un amministratore di più sottoscrizioni di Azure, occorre usare anche [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) per specificare la sottoscrizione corretta. L'esempio di Windows PowerShell seguente illustra il codice presente in genere all'inizio della maggior parte dei Runbook di automazione di Azure.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
	Add-AzureAccount –Credential $cred
	Select-AzureSubscription –SubscriptionName "My Subscription"

È necessario ripetere queste righe dopo ogni [checkpoints](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) del Runbook. Se il Runbook è sospeso e quindi ripreso da un altro thread di lavoro, sarà necessario eseguire di nuovo l'autenticazione.

## Passaggi successivi
* Esaminare i vari tipi di runbook e le procedure per creare runbook personalizzati nell'articolo [Tipi di runbook di Automazione di Azure](../automation/automation-runbook-types.md)

<!---HONumber=AcomDC_0914_2016-->