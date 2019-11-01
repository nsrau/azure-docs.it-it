---
title: Abilitare la sincronizzazione degli hash delle password per Azure Active Directory Domain Services | Microsoft Docs
description: Questa esercitazione illustra come abilitare la sincronizzazione degli hash delle password tramite Azure AD Connect per un dominio gestito di Azure Active Directory Domain Services.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 41e61376d12d447dd480a39ef7200db6af7cca89
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73172855"
---
# <a name="tutorial-enable-password-synchronization-in-azure-active-directory-domain-services-for-hybrid-environments"></a>Esercitazione: Abilitare la sincronizzazione delle password in Azure Active Directory Domain Services per ambienti ibridi

Per gli ambienti ibridi è possibile usare Azure AD Connect per configurare un tenant di Azure Active Directory (Azure AD) per la sincronizzazione con un ambiente Active Directory Domain Services locale. Per impostazione predefinita, Azure AD Connect non sincronizza gli hash delle password di NT LAN Manager (NTLM) e Kerberos legacy necessari per Azure Active Directory Domain Services.

Per usare Azure Active Directory Domain Services con account sincronizzati da un ambiente Active Directory Domain Services locale, è necessario configurare Azure AD Connect in modo da sincronizzare gli hash delle password necessari per l'autenticazione NTLM e Kerberos. Dopo la configurazione di Azure AD Connect, in seguito a un evento di creazione o modifica della password di un account locale verranno sincronizzati anche gli hash delle password legacy per Azure AD.

Non è necessario eseguire questa procedura se si usano account solo cloud senza un ambiente Active Directory Domain Services locale.

In questa esercitazione si apprenderà:

> [!div class="checklist"]
> * Per quale motivo gli hash delle password NTLM e Kerberos legacy sono necessari
> * Come configurare la sincronizzazione degli hash delle password legacy per Azure AD Connect

Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessarie le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale tramite Azure AD Connect.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
    * Se necessario, [abilitare Azure AD Connect per la sincronizzazione degli hash delle password][enable-azure-ad-connect].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, [creare e configurare un'istanza di Azure Active Directory Domain Services][create-azure-ad-ds-instance].

## <a name="password-hash-synchronization-using-azure-ad-connect"></a>Sincronizzazione degli hash delle password con Azure AD Connect

Azure AD Connect viene usato per sincronizzare oggetti quali account utente e gruppi da un ambiente Active Directory Domain Services locale in un tenant di Azure AD. Come parte del processo, la sincronizzazione degli hash delle password consente agli account di usare la stessa password nell'ambiente Active Directory Domain Services locale e in Azure AD.

Per autenticare gli utenti nel dominio gestito, Azure Active Directory Domain Services necessita degli hash delle password in un formato idoneo per l'autenticazione NTLM e Kerberos. Azure AD non archivia gli hash delle password nel formato necessario per l'autenticazione NTLM o Kerberos finché non si abilita Azure Active Directory Domain Services per il tenant. Per motivi di sicurezza, Azure AD non archivia nemmeno le credenziali di tipo password in un formato non crittografato. Azure AD quindi non può generare automaticamente questi hash delle password NTLM o Kerberos in base alle credenziali esistenti degli utenti.

È possibile configurare Azure AD Connect in modo da sincronizzare gli hash delle password NTLM o Kerberos necessari per Azure Active Directory Domain Services. Assicurarsi di aver completato i passaggi per [abilitare Azure ad Connect per la sincronizzazione degli hash delle password][enable-azure-ad-connect]. Se è disponibile un'istanza esistente di Azure AD Connect, [scaricarla e aggiornarla alla versione più recente][azure-ad-connect-download] per essere certi di poter sincronizzare gli hash delle password legacy per NTLM e Kerberos. Questa funzionalità non è disponibile nelle versioni precedenti di Azure AD Connect o con lo strumento DirSync legacy. È richiesto Azure AD Connect *1.1.614.0* o versione successiva.

## <a name="enable-synchronization-of-password-hashes"></a>Abilitare la sincronizzazione degli hash delle password

Dopo aver installato e configurato Azure AD Connect per la sincronizzazione con Azure AD, configurare ora la sincronizzazione degli hash delle password legacy per NTLM e Kerberos. Per configurare le impostazioni necessarie e quindi avviare una sincronizzazione completa delle password con Azure AD, si usa uno script PowerShell. Al termine del processo di sincronizzazione degli hash delle password di Azure AD Connect, gli utenti possono accedere tramite Azure Active Directory Domain Services alle applicazioni che usano hash delle password NTLM o Kerberos legacy.

1. Nel computer in cui è installato Azure AD Connect fare clic su Start e quindi su **Azure AD Connect > Synchronization Service** (Azure AD Connect > Servizio di sincronizzazione).
1. Selezionare la scheda **Connettori**. Sono elencate le informazioni di connessione usate per stabilire la sincronizzazione tra l'ambiente Azure Active Directory Domain Services locale e Azure AD.

    L'opzione **Tipo** indica *Windows Azure Active Directory (Microsoft)* per il connettore Azure AD oppure *Active Directory Domain Services* per il connettore Azure Active Directory Domain Services locale. Prendere nota dei nomi di connettore da usare nello script PowerShell nel passaggio successivo.

    ![Elencare i nomi dei connettori in Synchronization Service Manager](media/tutorial-configure-password-hash-sync/service-sync-manager.png)

    In questo esempio di screenshot vengono usati i connettori seguenti:

    * Il connettore Azure AD è denominato *contoso.onmicrosoft.com - AAD*
    * Il connettore Azure Active Directory Domain Services locale è denominato *onprem.contoso.com*

1. Copiare e incollare lo script PowerShell seguente nel computer in cui è installato Azure AD Connect. Lo script attiva una sincronizzazione completa delle password che include gli hash delle password legacy. Aggiornare le variabili `$azureadConnector` e `$adConnector` con i nomi di connettore del passaggio precedente.

    Eseguire questo script in ogni foresta Active Directory per sincronizzare gli hash delle password NTLM e Kerberos dell'account locale con Azure AD.

    ```powershell
    # Define the Azure AD Connect connector names and import the required PowerShell module
    $azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"
    $adConnector = "<CASE SENSITIVE AD DS CONNECTOR NAME>"
    Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"

    # Create a new ForceFullPasswordSync configuration parameter object then
    # update the existing connector with this new configuration
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c

    # Disable and re-enable Azure AD Connect to force a full password synchronization
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true
    ```

    A seconda delle dimensioni della directory in termini di numero di account e gruppi, la sincronizzazione degli hash delle password legacy con Azure AD potrebbe richiedere molto tempo. Dopo la sincronizzazione con Azure AD, le password vengono quindi sincronizzate con il dominio gestito di Azure Active Directory Domain Services.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso:

> [!div class="checklist"]
> * Per quale motivo gli hash delle password NTLM e Kerberos legacy sono necessari
> * Come configurare la sincronizzazione degli hash delle password legacy per Azure AD Connect

> [!div class="nextstepaction"]
> [Informazioni su come funziona la sincronizzazione in un dominio gestito di Azure AD Domain Services](synchronization.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-azure-ad-connect]: ../active-directory/hybrid/how-to-connect-install-express.md

<!-- EXTERNAL LINKS -->
[azure-ad-connect-download]: https://www.microsoft.com/download/details.aspx?id=47594
