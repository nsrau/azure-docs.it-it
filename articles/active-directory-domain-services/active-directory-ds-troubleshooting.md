---
title: 'Azure Active Directory Domain Services: guida alla risoluzione dei problemi | Microsoft Docs'
description: Guida alla risoluzione dei problemi di Servizi di dominio di Azure AD
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: d6695b0c40f56093e8701dfe6394143268114453
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Guida alla risoluzione dei problemi di Servizi di dominio Azure Active Directory
Questo articolo offre suggerimenti per la risoluzione dei problemi che possono verificarsi quando si configura o si amministra Servizi di dominio di Azure Active Directory (AD).

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Non è possibile abilitare i Servizi di dominio Azure AD per la directory Azure AD
Le informazioni riportate in questa sezione consentono di risolvere gli errori che si verificano quando si tenta di abilitare Azure Active Directory Domain Services per la directory e il tentativo ha esito negativo oppure lo stato viene impostato su "Disabilitato".

Seguire la procedura di risoluzione dei problemi corrispondente al messaggio di errore ricevuto.

| **Messaggio di errore** | **Risoluzione** |
| --- |:--- |
| *The name contoso100.com is already in use on this network. Specify a name that is not in use.* (Il nome contoso100.com è già in uso nella rete. Specificare un nome non in uso). |[Conflitto di nomi di dominio nella rete virtuale](active-directory-ds-troubleshooting.md#domain-name-conflict) |
| *Domain Services could not be enabled in this Azure AD tenant. The service does not have adequate permissions to the application called 'Azure AD Domain Services Sync'. Delete the application called 'Azure AD Domain Services Sync' and then try to enable Domain Services for your Azure AD tenant.* (Non è possibile abilitare Domain Services in questo tenant di Azure AD. Il servizio non dispone di autorizzazioni adeguate per l'applicazione denominata "Azure AD Domain Services Sync". Eliminare l'applicazione denominata "Azure AD Domain Services Sync" e quindi tentare di abilitare Domain Services per il tenant di Azure AD). |[Domain Services non dispone di autorizzazioni adeguate per l'applicazione Azure AD Domain Services Sync](active-directory-ds-troubleshooting.md#inadequate-permissions) |
| *Domain Services could not be enabled in this Azure AD tenant. The Domain Services application in your Azure AD tenant does not have the required permissions to enable Domain Services. Delete the application with the application identifier d87dcbc6-a371-462e-88e3-28ad15ec4e64 and then try to enable Domain Services for your Azure AD tenant.* (Non è possibile abilitare Domain Services in questo tenant di Azure AD. L'applicazione Domain Services presente nel tenant di Azure AD non dispone delle autorizzazioni necessarie per abilitare Domain Services. Eliminare l'applicazione con l'identificatore di applicazione d87dcbc6-a371-462e-88e3-28ad15ec4e64 e quindi tentare di abilitare Domain Services per il tenant di Azure AD). |[L'applicazione Servizi di dominio non è configurata in modo appropriato per il tenant](active-directory-ds-troubleshooting.md#invalid-configuration) |
| *Domain Services could not be enabled in this Azure AD tenant. The Microsoft Azure AD application is disabled in your Azure AD tenant. Enable the application with the application identifier 00000002-0000-0000-c000-000000000000 and then try to enable Domain Services for your Azure AD tenant.* (Non è possibile abilitare Domain Services in questo tenant di Azure AD. L'applicazione Microsoft Azure AD è disabilitata nel tenant di Azure AD. Abilitare l'applicazione con l'identificatore di applicazione 00000002-0000-0000-c000-000000000000 e quindi tentare di abilitare Domain Services per il tenant di Azure AD). |[L'applicazione Microsoft Graph è disabilitata nel tenant di Azure AD](active-directory-ds-troubleshooting.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Conflitto di nomi di dominio
**Messaggio di errore:**

*The name contoso100.com is already in use on this network. Specify a name that is not in use.* (Il nome contoso100.com è già in uso nella rete. Specificare un nome non in uso).

**Correzione:**

Assicurarsi che non sia presente un dominio esistente con lo stesso nome di dominio disponibile nella rete virtuale. Ad esempio, si supponga che un dominio denominato 'contoso.com' sia già disponibile nella rete virtuale selezionata. Provare successivamente ad abilitare un dominio gestito di Servizi di dominio Azure AD con lo stesso nome di dominio, ovvero "contoso.com", alla rete virtuale. Si verifica un errore quando si prova ad abilitare Azure AD Domain Services.

L'errore è dovuto a conflitti di nomi per il nome di dominio nella rete virtuale. In questa situazione è necessario usare un nome diverso per configurare il dominio gestito di Servizi di dominio Azure AD. In alternativa, è possibile eseguire il deprovisioning del dominio esistente e quindi abilitare Servizi di dominio Azure AD.

### <a name="inadequate-permissions"></a>Autorizzazioni non adeguate
**Messaggio di errore:**

*Domain Services could not be enabled in this Azure AD tenant. The service does not have adequate permissions to the application called 'Azure AD Domain Services Sync'. Delete the application called 'Azure AD Domain Services Sync' and then try to enable Domain Services for your Azure AD tenant.* (Non è possibile abilitare Domain Services in questo tenant di Azure AD. Il servizio non dispone di autorizzazioni adeguate per l'applicazione denominata "Azure AD Domain Services Sync". Eliminare l'applicazione denominata "Azure AD Domain Services Sync" e quindi tentare di abilitare Domain Services per il tenant di Azure AD).

**Correzione:**

Verificare se nella directory Azure AD è presente un'applicazione con nome "Azure AD Domain Services Sync". Se l'applicazione esiste, eliminarla e quindi abilitare di nuovo Active Directory Domain Services.

Per verificare se l'applicazione è presente e per eliminarla, se esiste, seguire questa procedura:

1. Passare al **portale di Azure classico** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Selezionare il nodo **Active Directory** nel riquadro sinistro.
3. Selezionare il tenant di Azure AD (directory) per il quale si desidera abilitare Servizi di dominio Azure AD.
4. Passare alla scheda **Applicazioni** .
5. Selezionare l'opzione **Applicazioni di proprietà dell'azienda** nell'elenco a discesa.
6. Cercare un'applicazione denominata **Azure AD Domain Services Sync**. Se l'applicazione esiste, eliminarla.
7. Dopo avere eliminato l'applicazione, provare ad abilitare di nuovo Servizi di dominio Azure AD.

### <a name="invalid-configuration"></a>Configurazione non valida.
**Messaggio di errore:**

*Domain Services could not be enabled in this Azure AD tenant. The Domain Services application in your Azure AD tenant does not have the required permissions to enable Domain Services. Delete the application with the application identifier d87dcbc6-a371-462e-88e3-28ad15ec4e64 and then try to enable Domain Services for your Azure AD tenant.* (Non è possibile abilitare Domain Services in questo tenant di Azure AD. L'applicazione Domain Services presente nel tenant di Azure AD non dispone delle autorizzazioni necessarie per abilitare Domain Services. Eliminare l'applicazione con l'identificatore di applicazione d87dcbc6-a371-462e-88e3-28ad15ec4e64 e quindi tentare di abilitare Domain Services per il tenant di Azure AD).

**Correzione:**

Verificare se nella directory di Azure AD è presente un'applicazione denominata "AzureActiveDirectoryDomainControllerServices" (con identificatore di applicazione d87dcbc6-a371-462e-88e3-28ad15ec4e64). Se l'applicazione esiste, sarà necessario eliminarla e quindi abilitare di nuovo Servizi di dominio Azure AD.

Usare lo script di PowerShell seguente per trovare l'applicazione ed eliminarla.

> [!NOTE]
> Questo script usa i cmdlet di **Azure AD PowerShell versione 2**. Per l'elenco completo di tutti i cmdlet disponibili e per indicazioni su come scaricare il modulo, leggere la [documentazione di riferimento di Azure AD PowerShell](https://msdn.microsoft.com/library/azure/mt757189.aspx).
>
>

```
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
<br>

### <a name="microsoft-graph-disabled"></a>Microsoft Graph disabilitato
**Messaggio di errore:**

Domain Services could not be enabled in this Azure AD tenant. The Microsoft Azure AD application is disabled in your Azure AD tenant. Enable the application with the application identifier 00000002-0000-0000-c000-000000000000 and then try to enable Domain Services for your Azure AD tenant. (Non è possibile abilitare Domain Services in questo tenant di Azure AD. L'applicazione Microsoft Azure AD è disabilitata nel tenant di Azure AD. Abilitare l'applicazione con l'identificatore di applicazione 00000002-0000-0000-c000-000000000000 e quindi tentare di abilitare Domain Services per il tenant di Azure AD).

**Correzione:**

Verificare se è stata disabilitata un'applicazione con identificatore di applicazione 00000002-0000-0000-c000-000000000000. Questa applicazione è Microsoft Azure AD e fornisce accesso al tenant di Azure AD mediante l'API Graph. Azure Active Directory Domain Services richiede questa applicazione per essere in grado di sincronizzare il tenant di Azure AD con il dominio gestito.

Per risolvere questo errore, abilitare l'applicazione e quindi tentare di abilitare Domain Services per il tenant di Azure AD.

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Impossibile accedere al dominio gestito di Servizi di dominio di Azure AD
Se uno o più utenti nel tenant di Azure AD non sono in grado di accedere al dominio gestito appena creato, seguire questa procedura di risoluzione dei problemi:

* **Accedere usando il formato UPN:** provare ad accedere usando il formato UPN (ad esempio "joeuser@contoso.com") anziché il formato SAMAccountName ("CONTOSO\joeuser"). L'attributo SAMAccountName può essere generato automaticamente per gli utenti il cui prefisso UPN è troppo lungo o è identico a un altro utente nel dominio gestito. Il formato UPN è garantito come univoco all'interno di un tenant di Azure AD.

> [!NOTE]
> Si consiglia di usare il formato UPN per accedere al dominio gestito di Servizi di dominio Azure AD.
>
>

* Assicurarsi di avere [abilitato la sincronizzazione delle password](active-directory-ds-getting-started-password-sync.md) secondo i passaggi descritti nella Guida introduttiva.
* **Account esterni** : assicurarsi che l'account utente interessato non sia un account esterno nel tenant di Azure AD. Esempi di account esterni sono gli account Microsoft (ad esempio "joe@live.com") o gli account utente di una directory esterna di Azure AD. Servizi di dominio di Azure AD non dispone di credenziali per questo tipo di account utente, pertanto questi utenti non sono in grado di accedere al dominio gestito.
* **Account sincronizzati:** : se gli account utente interessati sono sincronizzati da una directory locale, verificare quanto segue:

  * È stata eseguita la distribuzione o l'aggiornamento all' [ultima versione consigliata di Azure AD Connect](https://www.microsoft.com/en-us/download/details.aspx?id=47594).
  * Azure AD Connect è stato configurato per [eseguire una sincronizzazione completa](active-directory-ds-getting-started-password-sync.md).
  * A seconda delle dimensioni della directory, potrebbero essere necessari alcuni minuti prima che gli account utente e gli hash delle credenziali siano disponibili in Servizi di dominio di Azure AD. Assicurarsi di attendere il tempo sufficiente prima di ritentare l'autenticazione (il tempo può variare a seconda delle dimensioni della directory - da alcune ore fino a un giorno o due per directory di grandi dimensioni).
  * Se il problema persiste dopo la verifica dei passaggi precedenti, provare a riavviare il servizio Microsoft Azure AD Sync. Dal computer di sincronizzazione avviare un prompt dei comandi ed eseguire i comandi seguenti:

    1. net stop 'Microsoft Azure AD Sync'
    2. net start 'Microsoft Azure AD Sync'
* **Account solo cloud**: se l'account utente interessato è un account utente solo cloud, assicurarsi che l'utente abbia modificato la password dopo l'abilitazione di Servizi di dominio di Azure AD. Questa operazione comporta la generazione degli hash delle credenziali necessari per Servizi di dominio di Azure AD.

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Gli utenti rimossi dal tenant di Azure AD non vengono rimossi dal dominio gestito
Azure AD impedisce l'eliminazione accidentale di oggetti utente. Quando si elimina un account utente dal tenant di Azure AD, il corrispondente oggetto utente viene spostato nel Cestino. Quando questa operazione di eliminazione viene sincronizzata con il dominio gestito, il corrispondente account utente viene contrassegnato come disabilitato. Questa funzionalità consente di ripristinare o annullare l'eliminazione dell'account utente in un secondo tempo.

L'account utente rimane nello stato disabilitato nel dominio gestito, anche se si crea nuovamente un account utente con lo stesso UPN nella directory di Azure AD. Per rimuovere l'account utente dal dominio gestito, occorre eliminarlo dal tenant di Azure AD.

Per rimuovere completamente l'account utente dal dominio gestito, eliminare in modo permanente l'utente dal tenant di Azure AD. Usare il cmdlet Remove-MsolUser PowerShell con l'opzione "-RemoveFromRecycleBin", come descritto in questo [articolo di MSDN](https://msdn.microsoft.com/library/azure/dn194132.aspx).

## <a name="contact-us"></a>Contattaci
Contattare il team di prodotto di Servizi di dominio Azure AD per [condividere commenti e suggerimenti o per chiedere supporto tecnico](active-directory-ds-contact-us.md).
