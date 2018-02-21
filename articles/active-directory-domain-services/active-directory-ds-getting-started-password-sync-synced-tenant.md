---
title: 'Azure AD Domain Services: abilitare la sincronizzazione password | Documentazione Microsoft'
description: Introduzione ad Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 8731f2b2-661c-4f3d-adba-2c9e06344537
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/15/2017
ms.author: maheshu
ms.openlocfilehash: 0f6204e8f0f779809cd9c657acbcbcf39d57d481
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="enable-password-synchronization-to-azure-active-directory-domain-services"></a>Abilitare la sincronizzazione password con Azure Active Directory Domain Services
Nelle attività precedenti è stato abilitato Azure Active Directory Domain Services per il tenant di Azure Active Directory (Azure AD). L'attività successiva prevede l'abilitazione della sincronizzazione degli hash delle credenziali necessari per l'autenticazione NTLM (NT LAN Manager) e Kerberos con Azure AD Domain Services. Al termine della configurazione della sincronizzazione delle credenziali, gli utenti potranno accedere al dominio gestito con le credenziali aziendali.

La procedura da eseguire è diversa per gli account utente solo cloud rispetto agli account utente sincronizzati dalla directory locale tramite Azure AD Connect.

<br>
| **Tipo di account utente** | **Passaggi da eseguire** |
| --- | --- |
| **Account utente sincronizzati da una directory locale** |**&#x2713;** [Seguire le istruzioni in questo articolo](active-directory-ds-getting-started-password-sync-synced-tenant.md#task-5-enable-password-synchronization-to-your-managed-domain-for-user-accounts-synced-with-your-on-premises-ad) | 
| **Account utente cloud creati in Azure AD** |**&#x2713;** [Sincronizzare le password per gli account utente solo cloud con il dominio gestito](active-directory-ds-getting-started-password-sync.md) |
<br>

> [!TIP]
> **Potrebbe essere necessario completare entrambi i set di passaggi.**
> Se il tenant di Azure AD include una combinazione di utenti solo cloud e utenti dell'istanza locale di AD, è necessario eseguire entrambi i set di passaggi.
>

## <a name="task-5-enable-password-synchronization-to-your-managed-domain-for-user-accounts-synced-with-your-on-premises-ad"></a>Attività 5: Abilitare la sincronizzazione password nel dominio gestito per gli account utente sincronizzati con l'istanza locale di AD
Un tenant di Azure AD viene impostato per la sincronizzazione con la directory locale dell'organizzazione con Azure AD Connect. Per impostazione predefinita, Azure AD Connect non sincronizza gli hash delle credenziali NTLM e Kerberos con Azure AD. Per usare Servizi di dominio Azure AD, è necessario configurare Azure AD Connect per sincronizzare gli hash delle credenziali necessari per l'autenticazione NTLM e Kerberos. I passaggi seguenti consentono di sincronizzare gli hash delle credenziali necessari dalla directory locale con il tenant di Azure AD.

> [!NOTE]
> **Se l'organizzazione include account utente sincronizzati dalla directory locale, è necessario abilitare la sincronizzazione degli hash NTLM e Kerberos per usare il dominio gestito.** Un account utente sincronizzato è un account creato nella directory locale e viene sincronizzato con il tenant di Azure AD tramite Azure AD Connect.
>
>

### <a name="install-or-update-azure-ad-connect"></a>Installare o aggiornare Azure AD Connect
Installare l'ultima versione consigliata di Azure AD Connect in un computer aggiunto a un dominio. Se esiste già un'istanza del programma di installazione di Azure AD Connect, è necessario aggiornarla per usare la versione più recente di Azure AD Connect. Per evitare problemi o bug noti che potrebbero essere già stati corretti, usare sempre la versione più recente di Azure AD Connect.

**[Scaricare Azure AD Connect](http://www.microsoft.com/download/details.aspx?id=47594)**

Versione consigliata: **1.1.614.0** - pubblicata il 5 settembre 2017.

> [!WARNING]
> L'installazione dell'ultima versione consigliata di Azure AD Connect è NECESSARIA per abilitare le credenziali di password legacy (obbligatorio per l'autenticazione NTLM e Kerberos) da sincronizzare nel tenant di Azure AD. Questa funzionalità non è disponibile nelle versioni precedenti di Azure AD Connect o con lo strumento DirSync legacy.
>
>

Le istruzioni per l'installazione di Azure AD Connect sono disponibili nell'articolo [Introduzione ad Azure AD Connect](../active-directory/active-directory-aadconnect.md)

### <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>Abilitare la sincronizzazione di hash di credenziali NTLM e Kerberos in Azure AD
Eseguire lo script di PowerShell seguente in ogni foresta di AD. Lo script consente la sincronizzazione degli hash delle password NTLM e Kerberos di tutti gli utenti locali con il tenant di Azure AD. Lo script avvia anche una sincronizzazione completa in Azure AD Connect.

```powershell
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

A seconda delle dimensioni della directory (numero di utenti, gruppi e così via), la sincronizzazione degli hash delle credenziali con Azure AD richiede tempo. Le password saranno utilizzabili nel dominio gestito dei servizi di dominio Azure Active Directory non appena le hash di credenziali saranno sincronizzate con Azure.

<br>

## <a name="related-content"></a>Contenuti correlati
* [Abilitare la sincronizzazione password in Servizi di dominio Azure AD per una directory di Azure AD solo cloud](active-directory-ds-getting-started-password-sync.md)
* [Amministrare un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-administer-domain.md)
* [Aggiungere una macchina virtuale Windows a un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Aggiungere una macchina virtuale Red Hat Enterprise Linux a un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
