---
title: Creare un account utente di Azure AD | Microsoft Docs
description: Questo articolo descrive come creare le credenziali di un account utente di Azure AD per i runbook di Automazione di Azure ai fini dell'autenticazione in Azure e nella distribuzione classica di Azure.
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
keywords: utente di Azure Active Directory, Azure Service Management, account utente Azure AD
ms.assetid: fcfe266d-b22e-4dfb-8272-adcab09fc0cf
ms.service: automation
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2017
ms.author: magoedte
ms.translationtype: HT
ms.sourcegitcommit: b309108b4edaf5d1b198393aa44f55fc6aca231e
ms.openlocfilehash: 4eaa3e36ededddeb5268ec4f49b9daee2f824cee
ms.contentlocale: it-it
ms.lasthandoff: 08/15/2017

---
# <a name="authenticate-runbooks-with-azure-classic-deployment-and-resource-manager"></a>Autenticare i runbook con la distribuzione classica di Azure e Resource Manager
Questo articolo descrive le procedure da eseguire per configurare un account utente di Azure AD per i runbook di Automazione di Azure in esecuzione in risorse di Azure Resource Manager o del modello di distribuzione classica di Azure.  Anche se questa identità di autenticazione è ancora supportata per i runbook basati su Azure Resource Manager, il metodo consigliato consiste nell'usare un account RunAs di Azure.       

## <a name="create-a-new-azure-active-directory-user"></a>Creare un nuovo utente di Azure Active Directory
1. Accedere al portale di Azure classico come amministratore del servizio per la sottoscrizione di Azure da gestire.
2. Selezionare **Active Directory**e quindi il nome della directory dell'organizzazione.
3. Selezionare la scheda **Utenti** e quindi nell'area di comando selezionare **Aggiungi utente**.
4. Nella pagina **Informazioni sull'utente** in **Tipo di utente** selezionare **Nuovo utente nell'organizzazione**.
5. Immettere un nome utente.  
6. Selezionare il nome di directory associata alla sottoscrizione di Azure nella pagina Active Directory.
7. Nella pagina **Profilo utente** specificare nome e cognome, un nome descrittivo e un utente dall'elenco **Ruoli**.  Non selezionare **Abilita Multi-Factor Authentication**.
8. Prendere nota del nome completo dell'utente e della password temporanea.
9. Selezionare **Impostazioni -> Amministratori > Aggiungi**.
10. Digitare il nome completo dell'utente creato.
11. Selezionare la sottoscrizione che si vuole far gestire dall'utente.
12. Disconnettersi da Azure e ripetere l'accesso con l'account appena creato. Verrà chiesto di cambiare la password dell'utente.

## <a name="create-an-automation-account-in-azure-classic-portal"></a>Creare un account di Automazione nel portale di Azure classico
La procedura descritta in questa sezione consente di creare nel portale di Azure un account di Automazione di Azure da usare con i runbook che gestiscono risorse nella distribuzione classica di Azure.  

> [!NOTE]
> Gli account di Automazione creati con il portale di Azure classico possono essere gestiti sia dal portale di Azure che dal portale di Azure classico e da entrambi i set di cmdlet. Una volta creato l'account, non fa alcuna differenza come si creano e gestiscono le risorse all'interno dell'account. Se si prevede di continuare a usare il portale di Azure classico, è consigliabile creare gli account di Automazione con tale portale anziché con il portale di Azure.
> 
> 

1. Accedere al portale di Azure classico come amministratore del servizio per la sottoscrizione di Azure da gestire.
2. Selezionare **Automazione**.
3. Nella pagina **Automazione** selezionare **Crea un account di Automazione**.
4. Nella casella **Crea un account di Automazione** digitare un nome per il nuovo account di Automazione e selezionare un'area dall'elenco a discesa **Area**.  
5. Fare clic su **OK** per accettare le impostazioni e creare l'account.
6. Al termine della creazione, verrà visualizzato nell'elenco della pagina **Automazione** .
7. Fare clic sull'account per visualizzare la pagina Dashboard.  
8. Nella pagina del dashboard di Automazione selezionare **Asset**.
9. Nella pagina **Asset** selezionare l'opzione **Aggiungi impostazioni** nella parte inferiore della pagina.
10. Nella pagina **Aggiungi impostazioni** selezionare **Aggiungi credenziali**.
11. Nella pagina **Definisci credenziali** selezionare **Credenziali per Windows PowerShell** nell'elenco a discesa **Tipo di credenziali** e specificare un nome per le credenziali.
12. Nella pagina **Definisci credenziali** successiva digitare il nome utente dell'account utente Active Directory nel campo **Nome utente** e specificare la password nei campi **Password** e **Conferma password**. Fare clic su **OK** per salvare le modifiche.

## <a name="create-an-automation-account-in-the-azure-portal"></a>Creare un account di Automazione nel portale di Azure
La procedura descritta in questa sezione consente di creare nel portale di Azure un account di Automazione di Azure da usare con i runbook che gestiscono risorse in modalità Azure Resource Manager.  

1. Accedere al portale di Azure come amministratore del servizio per la sottoscrizione di Azure da gestire.
2. Selezionare **Account di automazione**.
3. Nel pannello Account di automazione fare clic su **Aggiungi**.<br><br>![Aggiungi account di Automazione](media/automation-create-aduser-account/add-automation-acct-properties.png)
4. Nella casella **Nome** del pannello **Aggiungi account di Automazione** digitare un nome per il nuovo account di Automazione.
5. Se si hanno più sottoscrizioni, specificare quella per il nuovo account, un **Gruppo di risorse** nuovo o esistente e la **Località** per il data center di Azure.
6. Selezionare il valore **Sì** per l'opzione **Crea un account RunAs di Azure** e fare clic sul pulsante **Crea**.  
   
    > [!NOTE]
    > Se si sceglie di non creare l'account RunAs selezionando l'opzione **No**, verrà visualizzato un messaggio di avviso nel pannello **Aggiungi account di Automazione**.  Durante la creazione e l'assegnazione al ruolo **Collaboratore** nella sottoscrizione, l'account non ha un'identità di autenticazione corrispondente nel servizio directory delle sottoscrizioni e, di conseguenza, non ha accesso alle risorse nella sottoscrizione.  Questo impedisce ai runbook che fanno riferimento a questo account di autenticarsi ed eseguire attività sulle risorse di Azure Resource Manager.
    > 
    >

    <br>![Aggiungi account di Automazione, avviso](media/automation-create-aduser-account/add-automation-acct-properties-error.png)<br>  
7. Mentre Azure crea l'account di Automazione, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

Al termine della creazione delle credenziali, è necessario creare un asset credenziali per associare l'account di Automazione all'account utente di Active Directory creato in precedenza.  Per ora, si è solo creato l'account di Automazione, ma questo non è associato ad alcuna identità di autenticazione.  Seguire la procedura illustrata nell'articolo [Asset credenziali in Automazione di Azure](automation-credentials.md#creating-a-new-credential-asset) e specificare il valore per il **nome utente** nel formato **dominio\utente**.

## <a name="use-the-credential-in-a-runbook"></a>Usare le credenziali in un Runbook
È possibile recuperare le credenziali in un runbook usando l'attività [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) e quindi usarle con [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) per connettersi alla sottoscrizione di Azure. Se le credenziali appartengono a un amministratore di più sottoscrizioni di Azure, occorre usare anche [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) per specificare la sottoscrizione corretta. L'esempio di Windows PowerShell seguente illustra il codice presente in genere all'inizio della maggior parte dei Runbook di automazione di Azure.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
    Add-AzureAccount –Credential $cred
    Select-AzureSubscription –SubscriptionName "My Subscription"

È necessario ripetere queste righe dopo ogni [checkpoints](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) del Runbook. Se il Runbook è sospeso e quindi ripreso da un altro thread di lavoro, sarà necessario eseguire di nuovo l'autenticazione.

## <a name="next-steps"></a>Passaggi successivi
* Esaminare i vari tipi di runbook e le procedure per creare runbook personalizzati nell'articolo [Tipi di runbook di Automazione di Azure](automation-runbook-types.md)


