---
title: Risoluzione dei problemi relativi a Servizi di dominio Azure Active Directory - Documenti Microsoft'
description: Informazioni su come risolvere gli errori comuni durante la creazione o la gestione di Servizi di dominio Azure Active Directory
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: 84efe294533186fdcf2e0a3356a7d6b01eccaf5f
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654400"
---
# <a name="common-errors-and-troubleshooting-steps-for-azure-active-directory-domain-services"></a>Errori comuni e procedure di risoluzione dei problemi per Servizi di dominio Azure Active DirectoryCommon errors and troubleshooting steps for Azure Active Directory Domain Services

Come parte centrale dell'identità e dell'autenticazione per le applicazioni, Servizi di dominio Azure Active Directory (Azure AD DS) talvolta presenta problemi. Se si verificano problemi, sono presenti alcuni messaggi di errore comuni e procedure di risoluzione dei problemi associate per consentire di eseguire nuovamente le operazioni. In qualsiasi momento, è anche possibile aprire una richiesta di supporto di [Azure][azure-support] per ulteriore assistenza per la risoluzione dei problemi.

Questo articolo illustra i passaggi per la risoluzione dei problemi comuni in Servizi di dominio Active Directory di Azure.This article provides troubleshooting steps for common issues in Azure AD DS.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Non è possibile abilitare i Servizi di dominio Azure AD per la directory Azure AD

In caso di problemi con l'abilitazione di Azure AD DS, esaminare gli errori comuni seguenti e i passaggi per risolverli:If you have problems enabling Azure AD DS, review the following common errors and steps to resolve them:

| **Messaggio di errore di esempio** | **Risoluzione** |
| --- |:--- |
| *Il nome aaddscontoso.com è già in uso su questa rete. Specificare un nome non in uso.* |[Conflitto di nomi di dominio nella rete virtuale](troubleshoot.md#domain-name-conflict) |
| *Impossibile abilitare Servizi di dominio in questo tenant di Azure AD. Il servizio non dispone di autorizzazioni adeguate per l'applicazione denominata 'Sincronizzazione Servizi di dominio Azure AD'. Eliminare l'applicazione denominata 'Sincronizzazione Servizi di dominio Azure AD' e quindi provare ad abilitare Servizi di dominio per il tenant di Azure AD.* |[Servizi di dominio non dispone delle autorizzazioni appropriate per l'applicazione di sincronizzazione di Servizi di dominio Azure ADDomain Services doesn't have appropriate permissions to the Azure AD Domain Services Sync application](troubleshoot.md#inadequate-permissions) |
| *Impossibile abilitare Servizi di dominio in questo tenant di Azure AD. L'applicazione Servizi di dominio nel tenant di Azure AD non dispone delle autorizzazioni necessarie per abilitare Servizi di dominio. Eliminare l'applicazione con l'identificatore dell'applicazione d87dcbc6-a371-462e-88e3-28ad15ec4e64 e quindi provare ad abilitare Servizi di dominio per il tenant di Azure AD.* |[L'applicazione Servizi di dominio non è configurata correttamente nel tenant di Azure ADThe Domain Services application isn't configured properly in your Azure AD tenant](troubleshoot.md#invalid-configuration) |
| *Impossibile abilitare Servizi di dominio in questo tenant di Azure AD. L'applicazione Microsoft Azure AD è disabilitata nel tenant di Azure AD. Abilitare l'applicazione con l'identificatore dell'applicazione 00000002-0000-0000-c000-00000000000 e quindi provare ad abilitare Servizi di dominio per il tenant di Azure AD.* |[L'applicazione Microsoft Graph è disabilitata nel tenant di Azure AD](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Conflitto di nomi di dominio

**Messaggio di errore**

*Il nome aaddscontoso.com è già in uso su questa rete. Specificare un nome non in uso.*

**Risoluzione**

Verificare di non avere un ambiente di dominio Active Directory esistente con lo stesso nome di dominio nella stessa rete virtuale o in una rete virtuale con peering. Ad esempio, è possibile avere un dominio di dominio Active Directory denominato aaddscontoso.com che viene eseguito nelle macchine virtuali di Azure.For example, you may have an AD DS domain named *aaddscontoso.com* that run on Azure VMs. Quando si tenta di abilitare un dominio gestito di Servizi di dominio Active Directory di Azure con lo stesso nome di dominio di *aaddscontoso.com* nella rete virtuale, l'operazione richiesta ha esito negativo.

Questo errore è dovuto a conflitti di nomi per il nome di dominio nella rete virtuale. Una ricerca DNS controlla se un ambiente di servizi di dominio Active Directory esistente risponde al nome di dominio richiesto. Per risolvere questo errore, usare un nome diverso per configurare il dominio gestito di Servizi di dominio Active Directory di Azure oppure eseguire il deprovisioning del dominio di Servizi di dominio Active Directory esistente e quindi riprovare ad abilitare Azure AD DS.

### <a name="inadequate-permissions"></a>Autorizzazioni non adeguate

**Messaggio di errore**

*Impossibile abilitare Servizi di dominio in questo tenant di Azure AD. Il servizio non dispone di autorizzazioni adeguate per l'applicazione denominata 'Sincronizzazione Servizi di dominio Azure AD'. Eliminare l'applicazione denominata 'Sincronizzazione Servizi di dominio Azure AD' e quindi provare ad abilitare Servizi di dominio per il tenant di Azure AD.*

**Risoluzione**

Verificare se è presente un'applicazione denominata *Sincronizzazione Servizi di dominio Azure AD* nella directory di Azure AD. Se questa applicazione esiste, eliminarla, quindi riprovare ad abilitare Servizi di dominio Active Directory di Azure.If this application exists, delete it, then try again to enable Azure AD DS. Per verificare la presenza di un'applicazione esistente ed eliminarla, se necessario, completare la procedura seguente:

1. Nel portale di Azure selezionare **Azure Active Directory** dal menu di spostamento a sinistra.
1. Selezionare **Applicazioni aziendali**. Scegliere *Tutte le applicazioni* dal menu a discesa Tipo di **applicazione,** quindi selezionare **Applica**.
1. Nella casella di ricerca immettere *Sincronizzazione Servizi di dominio Azure AD*. Se l'applicazione esiste, selezionarla e scegliere **Elimina**.
1. Dopo aver eliminato l'applicazione, provare ad abilitare di nuovo Servizi di dominio Active Directory di Azure.Once you've deleted the application, try to enable Azure AD DS again.

### <a name="invalid-configuration"></a>Configurazione non valida.

**Messaggio di errore**

*Impossibile abilitare Servizi di dominio in questo tenant di Azure AD. L'applicazione Servizi di dominio nel tenant di Azure AD non dispone delle autorizzazioni necessarie per abilitare Servizi di dominio. Eliminare l'applicazione con l'identificatore dell'applicazione d87dcbc6-a371-462e-88e3-28ad15ec4e64 e quindi provare ad abilitare Servizi di dominio per il tenant di Azure AD.*

**Risoluzione**

Verificare se si dispone di un'applicazione esistente denominata *AzureActiveDirectoryDomainControllerServices* con un identificatore di applicazione *d87dcbc6-a371-462e-88e3-28ad15ec4e64* nella directory di Azure AD. Se questa applicazione esiste, eliminarla e riprovare ad abilitare Servizi di dominio Active Directory di Azure.If this application exists, delete it and then try again to enable Azure AD DS.

Usare lo script di PowerShell seguente per cercare un'istanza dell'applicazione esistente ed eliminarla se necessario:Use the following PowerShell script to search for an existing application instance and delete it if needed:

```powershell
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```

### <a name="microsoft-graph-disabled"></a>Microsoft Graph disabilitato

**Messaggio di errore**

*Impossibile abilitare Servizi di dominio in questo tenant di Azure AD. L'applicazione Microsoft Azure AD è disabilitata nel tenant di Azure AD. Abilitare l'applicazione con l'identificatore dell'applicazione 00000002-0000-0000-c000-00000000000 e quindi provare ad abilitare Servizi di dominio per il tenant di Azure AD.*

**Risoluzione**

Verificare se è stata disabilitata un'applicazione con l'identificatore *00000002-0000-0000-c000-00000000000*. Questa applicazione è Microsoft Azure AD e fornisce accesso al tenant di Azure AD mediante l'API Graph. Per sincronizzare il tenant di Azure AD, questa applicazione deve essere abilitata.

Per controllare lo stato di questa applicazione e abilitarla se necessario, completare i seguenti passaggi:

1. Nel portale di Azure selezionare **Azure Active Directory** dal menu di spostamento a sinistra.
1. Selezionare **Applicazioni aziendali**. Scegliere *Tutte le applicazioni* dal menu a discesa Tipo di **applicazione,** quindi selezionare **Applica**.
1. Nella casella di ricerca immettere *00000002-0000-0000-c000-00000000000*. Selezionare l'applicazione , quindi scegliere **Proprietà**.
1. Se **Abilitato per l'accesso degli utenti** è impostato su *No*, impostare il valore su *Sì*, quindi selezionare **Salva**.
1. Dopo aver abilitato l'applicazione, provare ad abilitare di nuovo Servizi di dominio Active Directory di Azure.Once you've enabled the application, try to enable Azure AD DS again.

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Impossibile accedere al dominio gestito di Servizi di dominio di Azure AD

Se uno o più utenti nel tenant di Azure AD non riescono ad accedere al dominio gestito di Azure AD DS, completare la procedura di risoluzione dei problemi seguente:If one or more users in your Azure AD tenant can't sign in to the Azure AD DS managed domain, complete the following troubleshooting steps:

* **Formato credenziali:** provare a utilizzare il formato UPN `dee@aaddscontoso.onmicrosoft.com`per specificare le credenziali, ad esempio . Il formato UPN è il modo consigliato per specificare le credenziali in Azure AD DS. Assicurarsi che questo UPN sia configurato correttamente in Azure AD.

    Il *SAMAccountName* per l'account, ad esempio *AADDSCONTOSO,driley* può essere generato automaticamente se nel tenant sono presenti più utenti con lo stesso prefisso UPN o se il prefisso UPN è troppo lungo. Pertanto, il formato *SAMAccountName* per l'account potrebbe essere diverso da quello previsto o utilizzato nel dominio locale.

* **Sincronizzazione password:** assicurarsi di aver abilitato la sincronizzazione delle password per [gli utenti solo cloud][cloud-only-passwords] o per gli ambienti [ibridi tramite Azure AD Connect.][hybrid-phs]
    * **Account sincronizzati ibridi:** Se gli account utente interessati vengono sincronizzati da una directory locale, verificare le aree seguenti:
    
      * È stata distribuita o aggiornata [l'ultima versione consigliata di Azure AD Connect.](https://www.microsoft.com/download/details.aspx?id=47594)
      * Azure AD Connect è stato configurato per [eseguire una sincronizzazione completa.][hybrid-phs]
      * Depending on the size of your directory, it may take a while for user accounts and credential hashes to be available in Azure AD DS. Assicurarsi di attendere abbastanza a lungo prima di tentare di eseguire l'autenticazione nel dominio gestito.
      * Se il problema persiste dopo aver verificato i passaggi precedenti, provare a riavviare il servizio di *sincronizzazione*di Microsoft Azure AD . Dal server Azure AD Connect aprire un prompt dei comandi ed eseguire i comandi seguenti:From your Azure AD Connect server, open a command prompt and run the following commands:
    
        ```console
        net stop 'Microsoft Azure AD Sync'
        net start 'Microsoft Azure AD Sync'
        ```

    * **Account solo cloud:** se l'account utente interessato è un account utente solo cloud, assicurarsi che l'utente abbia modificato la password dopo aver abilitato Servizi di dominio Active Directory di [Azure.][cloud-only-passwords] Questa reimpostazione della password causa la generazione degli accessi a credenziali necessari per i servizi di dominio Azure AD.

* **Verificare che l'account utente sia attivo:** per impostazione predefinita, cinque tentativi con password non valida entro 2 minuti nel dominio gestito causano il blocco di un account utente per 30 minuti. L'utente non può accedere mentre l'account è bloccato. Dopo 30 minuti, l'account utente viene sbloccato automaticamente.
  * I tentativi di password non valida nel dominio gestito di Servizi di dominio Active Directory di Azure non bloccano l'account utente in Azure AD. L'account utente è bloccato solo all'interno del dominio gestito. Controllare lo stato dell'account utente nella Console di amministrazione di *Active Directory (ADAC)* usando la macchina virtuale di [gestione,][management-vm]non in Azure AD.
  * È inoltre possibile [configurare criteri granulari][password-policy] per le password per modificare la soglia e la durata di blocco predefinite.

* **Account esterni:** verificare che l'account utente interessato non sia un account esterno nel tenant di Azure AD. Esempi di account esterni `dee@live.com` includono account Microsoft come o account utente da una directory esterna di Azure AD. Servizi di dominio Active Directory di Azure non archivia le credenziali per gli account utente esterni, pertanto non possono accedere al dominio gestito.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Sono presenti uno o più avvisi nel dominio gestito

Se sono presenti avvisi attivi nel dominio gestito di Servizi di dominio Active Directory, è possibile che il processo di autenticazione funzioni correttamente.

Per verificare se sono presenti avvisi attivi, [controllare lo stato di integrità di un dominio gestito di Azure AD DS.][check-health] Se vengono visualizzati avvisi, [risolvere i problemi e risolverli.][troubleshoot-alerts]

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Gli utenti rimossi dal tenant di Azure AD non vengono rimossi dal dominio gestito

Azure AD protegge dall'eliminazione accidentale di oggetti utente. Quando si elimina un account utente da un tenant di Azure AD, l'oggetto utente corrispondente viene spostato nel Cestino. Quando questa operazione di eliminazione viene sincronizzata con il dominio gestito di Azure AD DS, l'account utente corrispondente viene contrassegnato come disabilitato. Questa funzionalità consente di ripristinare l'account utente o annullare l'eliminazione.

L'account utente rimane nello stato disabilitato nel dominio gestito di Servizi di dominio Active Directory di Azure, anche se si ricrea un account utente con lo stesso UPN nella directory di Azure AD. Per rimuovere l'account utente dal dominio gestito di Servizi di dominio Active Directory di Azure, è necessario eliminarlo con la rete dal tenant di Azure AD.

Per rimuovere completamente un account utente da un dominio gestito di Servizi di dominio Active Directory di Azure, eliminare `-RemoveFromRecycleBin` l'utente definitivamente dal tenant di Azure AD usando il cmdlet [Remove-MsolUser][Remove-MsolUser] PowerShell con il parametro.

## <a name="next-steps"></a>Passaggi successivi

Se i problemi persistono, aprire una richiesta di supporto di [Azure][azure-support] per ulteriore assistenza per la risoluzione dei problemi.

<!-- INTERNAL LINKS -->
[cloud-only-passwords]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[hybrid-phs]: tutorial-configure-password-hash-sync.md
[management-vm]: tutorial-create-management-vm.md
[password-policy]: password-policy.md
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
[Remove-MsolUser]: /powershell/module/MSOnline/Remove-MsolUser
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
