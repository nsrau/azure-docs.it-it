---
title: Risoluzione dei problemi di Azure Active Directory Domain Services | Microsoft Docs '
description: Informazioni su come risolvere gli errori comuni durante la creazione o la gestione di Azure Active Directory Domain Services
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80654400"
---
# <a name="common-errors-and-troubleshooting-steps-for-azure-active-directory-domain-services"></a>Errori comuni e passaggi per la risoluzione dei problemi per Azure Active Directory Domain Services

Come parte centrale dell'identità e dell'autenticazione per le applicazioni, Azure Active Directory Domain Services (Azure AD DS) a volte presenta dei problemi. Se si verificano problemi, sono disponibili alcuni messaggi di errore comuni e i passaggi di risoluzione dei problemi associati che consentono di eseguire di nuovo le operazioni. In qualsiasi momento, è anche possibile [aprire una richiesta di supporto tecnico di Azure][azure-support] per ulteriore assistenza per la risoluzione dei problemi.

Questo articolo fornisce le procedure per la risoluzione dei problemi comuni in Azure AD DS.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Non è possibile abilitare i Servizi di dominio Azure AD per la directory Azure AD

In caso di problemi di abilitazione di Azure AD DS, esaminare gli errori e i passaggi comuni seguenti per risolverli:

| **Messaggio di errore di esempio** | **Risoluzione** |
| --- |:--- |
| *Il nome aaddscontoso.com è già in uso in questa rete. Specificare un nome non in uso.* |[Conflitto di nomi di dominio nella rete virtuale](troubleshoot.md#domain-name-conflict) |
| *Non è stato possibile abilitare i servizi di dominio in questo tenant Azure AD. Il servizio non dispone delle autorizzazioni appropriate per l'applicazione denominata "Azure AD Domain Services Sync". Eliminare l'applicazione denominata ' Azure AD Domain Services Sync ', quindi provare ad abilitare Domain Services per il tenant di Azure AD.* |[Servizi di dominio non dispone delle autorizzazioni appropriate per l'applicazione di sincronizzazione Azure AD Domain Services](troubleshoot.md#inadequate-permissions) |
| *Non è stato possibile abilitare i servizi di dominio in questo tenant Azure AD. L'applicazione Domain Services nel tenant Azure AD non dispone delle autorizzazioni necessarie per abilitare i servizi di dominio. Eliminare l'applicazione con l'identificatore dell'applicazione d87dcbc6-A371-462E-88e3-28ad15ec4e64, quindi provare ad abilitare i servizi di dominio per il tenant Azure AD.* |[L'applicazione Domain Services non è configurata correttamente nel tenant di Azure AD](troubleshoot.md#invalid-configuration) |
| *Non è stato possibile abilitare i servizi di dominio in questo tenant Azure AD. L'applicazione Microsoft Azure AD è disabilitata nel tenant del Azure AD. Abilitare l'applicazione con l'identificatore dell'applicazione 00000002-0000-0000-C000-000000000000, quindi provare ad abilitare i servizi di dominio per il tenant Azure AD.* |[L'applicazione Microsoft Graph è disabilitata nel tenant di Azure AD](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Conflitto di nomi di dominio

**Messaggio di errore**

*Il nome aaddscontoso.com è già in uso in questa rete. Specificare un nome non in uso.*

**Risoluzione**

Verificare che non sia presente un ambiente di servizi di dominio Active Directory con lo stesso nome di dominio nello stesso o in una rete virtuale con peering. Ad esempio, è possibile avere un dominio di servizi di dominio Active Directory denominato *aaddscontoso.com* che viene eseguito in macchine virtuali di Azure. Quando si tenta di abilitare un dominio gestito di Azure AD DS con lo stesso nome di dominio di *aaddscontoso.com* nella rete virtuale, l'operazione richiesta ha esito negativo.

Questo errore è dovuto a conflitti di nomi per il nome di dominio nella rete virtuale. Una ricerca DNS controlla se un ambiente di servizi di dominio Active Directory esistente risponde sul nome di dominio richiesto. Per risolvere l'errore, usare un nome diverso per configurare il dominio gestito di Azure AD DS oppure effettuare il deprovisioning del dominio di servizi di dominio Active Directory esistente, quindi riprovare ad abilitare Azure AD DS.

### <a name="inadequate-permissions"></a>Autorizzazioni non adeguate

**Messaggio di errore**

*Non è stato possibile abilitare i servizi di dominio in questo tenant Azure AD. Il servizio non dispone delle autorizzazioni appropriate per l'applicazione denominata "Azure AD Domain Services Sync". Eliminare l'applicazione denominata ' Azure AD Domain Services Sync ', quindi provare ad abilitare Domain Services per il tenant di Azure AD.*

**Risoluzione**

Controllare se è presente un'applicazione denominata *Azure ad Domain Services Sync* nella directory Azure ad. Se l'applicazione esiste, eliminarla, quindi riprovare ad abilitare Azure AD DS. Per verificare la presenza di un'applicazione esistente ed eliminarla, se necessario, attenersi alla procedura seguente:

1. Nella portale di Azure selezionare **Azure Active Directory** dal menu di spostamento a sinistra.
1. Selezionare **Applicazioni aziendali**. Scegliere *tutte le applicazioni* dal menu a discesa **tipo di applicazione** , quindi selezionare **applica**.
1. Nella casella di ricerca immettere *Azure ad Domain Services Sync*. Se l'applicazione esiste, selezionarla e scegliere **Elimina**.
1. Dopo aver eliminato l'applicazione, provare ad abilitare di nuovo Azure AD DS.

### <a name="invalid-configuration"></a>Configurazione non valida.

**Messaggio di errore**

*Non è stato possibile abilitare i servizi di dominio in questo tenant Azure AD. L'applicazione Domain Services nel tenant Azure AD non dispone delle autorizzazioni necessarie per abilitare i servizi di dominio. Eliminare l'applicazione con l'identificatore dell'applicazione d87dcbc6-A371-462E-88e3-28ad15ec4e64, quindi provare ad abilitare i servizi di dominio per il tenant Azure AD.*

**Risoluzione**

Controllare se è presente un'applicazione denominata *AzureActiveDirectoryDomainControllerServices* con un identificatore dell'applicazione *d87dcbc6-a371-462e-88e3-28ad15ec4e64* nella directory Azure ad. Se l'applicazione esiste, eliminarla e riprovare ad abilitare Azure AD DS.

Usare lo script di PowerShell seguente per cercare un'istanza dell'applicazione esistente ed eliminarla, se necessario:

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

*Non è stato possibile abilitare i servizi di dominio in questo tenant Azure AD. L'applicazione Microsoft Azure AD è disabilitata nel tenant del Azure AD. Abilitare l'applicazione con l'identificatore dell'applicazione 00000002-0000-0000-C000-000000000000, quindi provare ad abilitare i servizi di dominio per il tenant Azure AD.*

**Risoluzione**

Controllare se è stata disabilitata un'applicazione con l'identificatore *00000002-0000-0000-C000-000000000000*. Questa applicazione è Microsoft Azure AD e fornisce accesso al tenant di Azure AD mediante l'API Graph. Per sincronizzare il tenant di Azure AD, questa applicazione deve essere abilitata.

Per controllare lo stato dell'applicazione e abilitarla, se necessario, completare i passaggi seguenti:

1. Nella portale di Azure selezionare **Azure Active Directory** dal menu di spostamento a sinistra.
1. Selezionare **Applicazioni aziendali**. Scegliere *tutte le applicazioni* dal menu a discesa **tipo di applicazione** , quindi selezionare **applica**.
1. Nella casella di ricerca immettere *00000002-0000-0000-C000-00000000000*. Selezionare l'applicazione, quindi scegliere **Proprietà**.
1. Se **abilitata per l'accesso degli utenti** è impostata su *No*, impostare il valore su *Sì*, quindi selezionare **Salva**.
1. Una volta abilitata l'applicazione, provare ad abilitare di nuovo Azure AD DS.

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Impossibile accedere al dominio gestito di Servizi di dominio di Azure AD

Se uno o più utenti nel tenant di Azure AD non possono accedere al dominio gestito di Azure AD DS, completare le procedure di risoluzione dei problemi seguenti:

* **Formato delle credenziali** : provare a usare il formato UPN per specificare le credenziali `dee@aaddscontoso.onmicrosoft.com`, ad esempio. Il formato UPN è il modo consigliato per specificare le credenziali in Azure AD DS. Verificare che il nome UPN sia configurato correttamente in Azure AD.

    Il *sAMAccountName* per l'account, ad esempio *AADDSCONTOSO\driley* , può essere generato automaticamente se sono presenti più utenti con lo stesso prefisso UPN nel tenant o se il prefisso UPN è troppo lungo. Pertanto, il formato di *sAMAccountName* per l'account può essere diverso da quello previsto o da usare nel dominio locale.

* **Sincronizzazione delle password** : assicurarsi di avere abilitato la sincronizzazione delle password per [gli utenti solo cloud][cloud-only-passwords] o per gli [ambienti ibridi usando Azure ad Connect][hybrid-phs].
    * **Account sincronizzati ibridi:** Se gli account utente interessati sono sincronizzati da una directory locale, verificare le aree seguenti:
    
      * L' [ultima versione consigliata di Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594)è stata distribuita o aggiornata a.
      * È stato configurato Azure AD Connect per [eseguire una sincronizzazione completa][hybrid-phs].
      * A seconda delle dimensioni della directory, è possibile che gli account utente e gli hash delle credenziali siano disponibili in Azure AD DS. Prima di tentare di eseguire l'autenticazione nel dominio gestito, assicurarsi di attendere abbastanza tempo.
      * Se il problema persiste dopo la verifica dei passaggi precedenti, provare a riavviare il *servizio di sincronizzazione Microsoft Azure ad*. Dal server Azure AD Connect aprire un prompt dei comandi ed eseguire i comandi seguenti:
    
        ```console
        net stop 'Microsoft Azure AD Sync'
        net start 'Microsoft Azure AD Sync'
        ```

    * **Account solo cloud**: se l'account utente interessato è un account utente solo cloud, assicurarsi che l' [utente abbia modificato la password dopo l'abilitazione di Azure AD DS][cloud-only-passwords]. Questa reimpostazione della password causa la generazione degli hash delle credenziali necessari per Azure AD Domain Services.

* **Verificare che l'account utente sia attivo**: per impostazione predefinita, cinque tentativi di password non validi entro 2 minuti dal dominio gestito causano il blocco di un account utente per 30 minuti. L'utente non può effettuare l'accesso mentre l'account è bloccato. Dopo 30 minuti, l'account utente viene sbloccato automaticamente.
  * Tentativi di password non validi nel dominio gestito Azure AD DS non bloccano l'account utente in Azure AD. L'account utente viene bloccato solo all'interno del dominio gestito. Controllare lo stato dell'account utente nella *console di amministrazione di Active Directory (ADAC)* usando la [macchina virtuale di gestione][management-vm], non in Azure ad.
  * È anche possibile [configurare i criteri granulari][password-policy] per le password per modificare la soglia e la durata di blocco predefinite.

* **Account esterni** : verificare che l'account utente interessato non sia un account esterno nel tenant del Azure ad. Esempi di account esterni includono account Microsoft, `dee@live.com` ad esempio, o account utente da una directory Azure ad esterna. Azure AD DS non archivia le credenziali per gli account utente esterni, in modo che non possano accedere al dominio gestito.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Sono presenti uno o più avvisi nel dominio gestito

Se sono presenti avvisi attivi sul dominio gestito Azure AD DS, potrebbe impedire il corretto funzionamento del processo di autenticazione.

Per verificare se sono presenti avvisi attivi, [verificare lo stato di integrità di un dominio gestito di Azure AD DS][check-health]. Se vengono visualizzati avvisi, risolvere i [problemi e risolverli][troubleshoot-alerts].

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Gli utenti rimossi dal tenant di Azure AD non vengono rimossi dal dominio gestito

Azure AD protegge da eliminazioni accidentali di oggetti utente. Quando si elimina un account utente da un tenant di Azure AD, l'oggetto utente corrispondente viene spostato nel Cestino. Quando questa operazione di eliminazione viene sincronizzata con il dominio gestito di Azure AD DS, l'account utente corrispondente è contrassegnato come disabilitato. Questa funzionalità consente di ripristinare o annullare l'eliminazione dell'account utente.

L'account utente rimane nello stato disabilitato nel dominio gestito di Azure AD DS, anche se si ricrea un account utente con lo stesso UPN nella directory Azure AD. Per rimuovere l'account utente dal dominio gestito di Azure AD DS, è necessario eliminarlo forzatamente dal tenant di Azure AD.

Per rimuovere completamente un account utente da un dominio gestito Azure AD DS, eliminare definitivamente l'utente dal tenant di Azure AD usando il cmdlet di PowerShell [Remove-MsolUser][Remove-MsolUser] con `-RemoveFromRecycleBin` il parametro.

## <a name="next-steps"></a>Passaggi successivi

Se continuano a verificarsi problemi, è possibile [aprire una richiesta di supporto tecnico di Azure][azure-support] per ulteriore assistenza per la risoluzione dei problemi.

<!-- INTERNAL LINKS -->
[cloud-only-passwords]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[hybrid-phs]: tutorial-configure-password-hash-sync.md
[management-vm]: tutorial-create-management-vm.md
[password-policy]: password-policy.md
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
[Remove-MsolUser]: /powershell/module/MSOnline/Remove-MsolUser
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
