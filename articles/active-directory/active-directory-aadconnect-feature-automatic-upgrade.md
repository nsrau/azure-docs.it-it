<properties
   pageTitle="Azure AD Connect: aggiornamento automatico | Microsoft Azure"
   description="Questo argomento illustra la funzionalità di aggiornamento automatico predefinita nel servizio di sincronizzazione Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/15/2016"
   ms.author="andkjell"/>

# Azure AD Connect: aggiornamento automatico
Questa funzionalità è stata introdotta nella build 1.1.105.0 rilasciata nel mese di febbraio 2016.

## Panoramica
La funzionalità per l'**aggiornamento automatico** semplifica al massimo la verifica dell'aggiornamento dell'installazione di Azure AD Connect. Questa funzionalità è abilitata per impostazione predefinita per le installazioni rapide e consente di assicurare che l'installazione venga aggiornata automaticamente al rilascio di una nuova versione.

L'aggiornamento automatico è abilitato per impostazione predefinita per gli scenari seguenti:

- Installazione mediante le impostazioni rapide
- Uso di un database locale di SQL Express richiesto sempre dalle impostazioni rapide
- L'account AD è l'account MSOL\_ predefinito creato dalle impostazioni rapide
- Il metaverse include meno di 100.000 oggetti

Lo stato corrente dell'aggiornamento automatico può essere visualizzato con il cmdlet `Get-ADSyncAutoUpgrade` di PowerShell e include gli stati seguenti:

| Stato | Commento |
| ---- | ---- |
| Enabled | L'aggiornamento automatico è abilitato. |
| Suspended | Impostato solo dal sistema. Il sistema non è più idoneo per la ricezione di aggiornamenti automatici. |
| Disabled | L'aggiornamento automatico è disabilitato. |

Per passare da **Enabled** a **Disabled**, è possibile usare `Set-ADSyncAutoUpgrade`. Lo stato **Suspended** deve essere impostato solo dal sistema.

L'aggiornamento automatico usa Azure AD Connect Health come infrastruttura di aggiornamento. Per un corretto funzionamento dell'aggiornamento automatico, assicurarsi di avere aperto gli URL nel proxy per **Azure AD Connect Health**, come documentato in [URL e intervalli di indirizzi IP per Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Se l'interfaccia utente di **Synchronization Service Manager** è in esecuzione sul server, l'aggiornamento verrà sospeso fino alla chiusura dell'interfaccia utente.

## Risoluzione dei problemi
Se l'installazione di Connect non viene aggiornata automaticamente, seguire questa procedura per individuare eventuali errori.

In primo luogo, tenere presente che l'aggiornamento automatico non viene eseguito il primo giorno di rilascio di una nuova versione. Il tentativo di aggiornamento viene eseguito intenzionalmente con una tempistica casuale. Non allarmarsi se l'installazione non viene aggiornata immediatamente.

Se si sospetta la presenza di un errore, eseguire prima `Get-ADSyncAutoUpgrade` per verificare che l'aggiornamento automatico sia abilitato.

Avviare il registro eventi e esaminare il registro eventi **applicazioni**. Aggiungere un filtro del registro eventi per l'origine relativa all'**aggiornamento di Azure AD Connect** e l'intervallo di ID evento **300-399**. ![Filtro del registro eventi per l'aggiornamento automatico](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogfilter.png)

Verranno restituiti i registri eventi associati allo stato dell'aggiornamento automatico. ![Filtro del registro eventi per l'aggiornamento automatico](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogresult.png)

La prima metà dei risultati offre una panoramica dello stato.

| Prefisso dei risultati | Descrizione |
| --- | --- |
| Esito positivo | L'installazione è stata aggiornata correttamente. |
| UpgradeAborted | Una condizione temporanea ha interrotto l'aggiornamento. Verrà ripetuto nuovamente e si prevede che venga eseguito correttamente. |
| UpgradeNotSupported | Il sistema ha una configurazione che impedisce l'aggiornamento automatico del sistema. Verrà ripetuto per verificare se lo stato viene modificato, ma si prevede che il sistema debba essere aggiornato manualmente. |

Di seguito è riportato un elenco dei messaggi più comuni. L'elenco non include tutti i messaggi, ma il messaggio dei risultati indicherà chiaramente il problema.

| Messaggio dei risultati | Descrizione |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedSyncExeInUse | L'[interfaccia utente di Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md) è aperta nel server.
| UpgradeAbortedInsufficientDiskSpace | Lo spazio su disco sufficiente non è sufficiente per supportare un aggiornamento. |
| UpgradeAbortedSyncCycleDisabled | L'opzione SyncCycle dell'[utilità di pianificazione](active-directory-aadconnectsync-feature-scheduler.md) è stata disabilitata. |
| UpgradeAbortedSyncOrConfigurationInProgress | L'installazione guidata è in esecuzione o è stata pianificata una sincronizzazione al di fuori dell'utilità di pianificazione. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedCustomizedSyncRules | Sono state aggiunte regole personalizzate alla configurazione. |
| UpgradeNotSupportedDeviceWritebackEnabled | È stata abilitata la funzionalità di [writeback dei dispositivi](active-directory-aadconnect-feature-device-writeback.md). |
| UpgradeNotSupportedGroupWritebackEnabled | È stata abilitata la funzionalità di [writeback dei gruppi](active-directory-aadconnect-feature-preview.md#group-writeback). |
| UpgradeNotSupportedMetaverseSizeExceeeded | Il metaverse include più di 100.000 oggetti. |
| UpgradeNotSupportedMultiForestSetup | Viene stabilita la connessione a più foreste. L'installazione rapida si connetterà a una sola foresta. |
| UpgradeNotSupportedNonMsolAccount | L'[account AD Connector](active-directory-aadconnect-accounts-permissions.md#active-directory-account) non è più l'account MSOL predefinito.
| UpgradeNotSupportedStagingModeEnabled | Il server è impostato in [modalità di gestione temporanea](active-directory-aadconnectsync-operations.md#staging-mode). |

## Passaggi successivi
Ulteriori informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0420_2016-->