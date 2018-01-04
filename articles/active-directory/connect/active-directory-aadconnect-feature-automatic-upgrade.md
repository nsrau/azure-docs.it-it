---
title: 'Azure AD Connect: aggiornamento automatico | Documentazione Microsoft'
description: "Questo argomento illustra la funzionalità di aggiornamento automatico predefinita nel servizio di sincronizzazione Azure AD Connect."
services: active-directory
documentationcenter: 
author: AndKjell
manager: mtillman
editor: 
ms.assetid: 6b395e8f-fa3c-4e55-be54-392dd303c472
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 49fc908006f19135b4c4553eef97d2f396db33ae
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect: aggiornamento automatico
Questa funzionalità è stata introdotta nella build 1.1.105.0 rilasciata nel mese di febbraio 2016.

## <a name="overview"></a>Panoramica
La funzionalità per l' **aggiornamento automatico** semplifica al massimo la verifica dell'aggiornamento dell'installazione di Azure AD Connect. Questa funzionalità è abilitata per impostazione predefinita per le installazioni rapide e gli aggiornamenti di DirSync. Quando viene rilasciata una nuova versione, l'installazione viene aggiornata automaticamente.
L'aggiornamento automatico è abilitato per impostazione predefinita per gli scenari seguenti:

* Impostazioni rapide e aggiornamenti di DirSync.
* Uso del database LocalDB di SQL Server Express, richiesto sempre per le impostazioni rapide. Anche DirSync con SQL Server Express usa il database LocalDB.
* L'account Active Directory è l'account MSOL_ predefinito creato dalle impostazioni rapide e da DirSync.
* Il metaverse include meno di 100.000 oggetti.

Lo stato corrente dell'aggiornamento automatico può essere visualizzato con il cmdlet `Get-ADSyncAutoUpgrade` di PowerShell e include gli stati seguenti:

| Stato | Commento |
| --- | --- |
| Enabled |L'aggiornamento automatico è abilitato. |
| Suspended |Impostato solo dal sistema. Il sistema non è più idoneo per la ricezione di aggiornamenti automatici. |
| Disabled |L'aggiornamento automatico è disabilitato. |

Per passare da **Enabled** a **Disabled**, è possibile usare `Set-ADSyncAutoUpgrade`. Lo stato **Suspended**deve essere impostato solo dal sistema.

L'aggiornamento automatico usa Azure AD Connect Health per l'infrastruttura di aggiornamento. Per il corretto funzionamento dell'aggiornamento automatico, assicurarsi di avere aperto gli URL nel server proxy per **Azure AD Connect Health** , come documentato in [URL e intervalli di indirizzi IP per Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).


Se l'interfaccia utente di **Synchronization Service Manager** è in esecuzione nel server, l'aggiornamento viene sospeso fino alla chiusura dell'interfaccia utente.

## <a name="troubleshooting"></a>risoluzione dei problemi
Se l'installazione di Connect non viene aggiornata automaticamente, seguire questa procedura per individuare eventuali errori.

In primo luogo, tenere presente che l'aggiornamento automatico non viene eseguito il primo giorno di rilascio di una nuova versione. Il tentativo di aggiornamento viene eseguito intenzionalmente con una tempistica casuale. Non allarmarsi se l'installazione non viene aggiornata immediatamente.

Se si sospetta la presenza di un errore, eseguire prima `Get-ADSyncAutoUpgrade` per verificare che l'aggiornamento automatico sia abilitato.

Assicurarsi quindi che gli URL richiesti siano stati aperti nel proxy o nel firewall. L'aggiornamento automatico usa Azure AD Connect Health come descritto nella sezione [Panoramica](#overview). Se si usa un proxy, assicurarsi che Health sia stato configurato per l'uso di un [server proxy](../connect-health/active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Testare anche la [connettività di Health](../connect-health/active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) ad Azure AD.

Dopo avere verificato la connettività ad Azure AD, è necessario esaminare i log eventi. Avviare il Visualizzatore eventi ed esaminare il log eventi **Applicazione** . Aggiungere un filtro del log eventi per l'origine **Aggiornamento di Azure AD Connect** e l'intervallo di ID evento **300-399**.  
![Filtro del registro eventi per l'aggiornamento automatico](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogfilter.png)  

Verranno visualizzati i log eventi associati allo stato dell'aggiornamento automatico.  
![Filtro del registro eventi per l'aggiornamento automatico](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogresult.png)  

Il codice risultato presenta un prefisso con una panoramica dello stato.

| Prefisso codice risultato | DESCRIZIONE |
| --- | --- |
| Success |L'installazione è stata aggiornata correttamente. |
| UpgradeAborted |Una condizione temporanea ha interrotto l'aggiornamento. Verrà ripetuto di nuovo e si prevede che in seguito riuscirà. |
| UpgradeNotSupported |La configurazione del sistema impedisce l'aggiornamento automatico del sistema stesso. Verrà ripetuto per verificare se lo stato viene modificato, ma si prevede che il sistema debba essere aggiornato manualmente. |

Ecco un elenco dei messaggi più comuni. L'elenco non include tutti i messaggi, ma il messaggio dei risultati indicherà chiaramente il problema.

| Messaggio dei risultati | DESCRIZIONE |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |Non è possibile scrivere nel Registro di sistema. |
| UpgradeAbortedInsufficientDatabasePermissions |Il gruppo Administrators predefinito non ha le autorizzazioni per il database. Per risolvere il problema, eseguire manualmente l'aggiornamento alla versione più recente di Azure AD Connect. |
| UpgradeAbortedInsufficientDiskSpace |Lo spazio su disco sufficiente non è sufficiente per supportare un aggiornamento. |
| UpgradeAbortedSecurityGroupsNotPresent |Non è possibile trovare e risolvere tutti i gruppi di sicurezza usati dal motore di sincronizzazione. |
| UpgradeAbortedServiceCanNotBeStarted |L'avvio del servizio NT **Microsoft Azure AD Sync** non è riuscito. |
| UpgradeAbortedServiceCanNotBeStopped |L'arresto del servizio NT **Microsoft Azure AD Sync** non è riuscito. |
| UpgradeAbortedServiceIsNotRunning |Il servizio NT **Microsoft Azure AD Sync** non è in esecuzione. |
| UpgradeAbortedSyncCycleDisabled |L'opzione SyncCycle dell' [Utilità di pianificazione](active-directory-aadconnectsync-feature-scheduler.md) è stata disabilitata. |
| UpgradeAbortedSyncExeInUse |L' [interfaccia utente di Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md) è aperta nel server. |
| UpgradeAbortedSyncOrConfigurationInProgress |L'installazione guidata è in esecuzione o è stata pianificata una sincronizzazione al di fuori dell'utilità di pianificazione. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedAdfsSignInMethod | È stato selezionato Adfs come metodo di accesso. | 
| UpgradeNotSupportedCustomizedSyncRules |Sono state aggiunte regole personalizzate alla configurazione. |
| UpgradeNotSupportedDeviceWritebackEnabled |È stata abilitata la funzionalità di [writeback dei dispositivi](active-directory-aadconnect-feature-device-writeback.md) . |
| UpgradeNotSupportedGroupWritebackEnabled |È stata abilitata la funzionalità di [writeback dei gruppi](active-directory-aadconnect-feature-preview.md#group-writeback) . |
| UpgradeNotSupportedInvalidPersistedState |L'installazione non è un aggiornamento delle impostazioni rapide o di DirSync. |
| UpgradeNotSupportedMetaverseSizeExceeeded |Il metaverse include più di 100.000 oggetti. |
| UpgradeNotSupportedMultiForestSetup |Viene stabilita la connessione a più foreste. L'installazione rapida si connette a una sola foresta. |
| UpgradeNotSupportedNonLocalDbInstall |Non si sta usando un database LocalDB di SQL Server Express. |
| UpgradeNotSupportedNonMsolAccount |L'[account AD Connect](active-directory-aadconnect-accounts-permissions.md#active-directory-account) non è più l'account MSOL_ predefinito. |
| UpgradeNotSupportedNotConfiguredSignInMethod | Durante la configurazione di AAD Connect, nella selezione del metodo di accesso si è scelto *Non configurare*. | 
| UpgradeNotSupportedPtaSignInMethod | È stata selezionato Autenticazione pass-through come metodo di accesso. |
| UpgradeNotSupportedStagingModeEnabled |Il server è in [modalità di staging](active-directory-aadconnectsync-operations.md#staging-mode). |
| UpgradeNotSupportedUserWritebackEnabled |È stata abilitata la funzionalità di [writeback degli utenti](active-directory-aadconnect-feature-preview.md#user-writeback) . |

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).
