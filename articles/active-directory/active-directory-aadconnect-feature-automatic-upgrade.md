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
   ms.date="06/27/2016"
   ms.author="andkjell"/>

# Azure AD Connect: aggiornamento automatico
Questa funzionalità è stata introdotta nella build 1.1.105.0 rilasciata nel mese di febbraio 2016.

## Panoramica
La funzionalità per l'**aggiornamento automatico** semplifica al massimo la verifica dell'aggiornamento dell'installazione di Azure AD Connect. Questa funzionalità è abilitata per impostazione predefinita per le installazioni rapide e gli aggiornamenti di DirSync. Quando viene rilasciata una nuova versione, l'installazione viene aggiornata automaticamente.

L'aggiornamento automatico è abilitato per impostazione predefinita per gli scenari seguenti:

- Impostazioni rapide e aggiornamenti di DirSync.
- Uso del database LocalDB di SQL Server Express, richiesto sempre dalle impostazioni rapide. Anche DirSync con SQL Server Express usa il database LocalDB.
- L'account Active Directory è l'account MSOL\_ predefinito creato dalle impostazioni rapide e da DirSync.
- Il metaverse include meno di 100.000 oggetti.

Lo stato corrente dell'aggiornamento automatico può essere visualizzato con il cmdlet `Get-ADSyncAutoUpgrade` di PowerShell e include gli stati seguenti:

Stato | Commento
---- | ----
Enabled | L'aggiornamento automatico è abilitato.
Suspended | Impostato solo dal sistema. Il sistema non è più idoneo per la ricezione di aggiornamenti automatici.
Disabled | L'aggiornamento automatico è disabilitato.

Per passare da **Enabled** a **Disabled**, è possibile usare `Set-ADSyncAutoUpgrade`. Lo stato **Suspended** deve essere impostato solo dal sistema.

L'aggiornamento automatico usa Azure AD Connect Health per l'infrastruttura di aggiornamento. Per un corretto funzionamento dell'aggiornamento automatico, assicurarsi di avere aperto gli URL nel server proxy per **Azure AD Connect Health**, come documentato in [URL e intervalli di indirizzi IP per Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Se l'interfaccia utente di **Synchronization Service Manager** è in esecuzione sul server, l'aggiornamento verrà sospeso fino alla chiusura dell'interfaccia utente.

## Risoluzione dei problemi
Se l'installazione di Connect non viene aggiornata automaticamente, seguire questa procedura per individuare eventuali errori.

In primo luogo, tenere presente che l'aggiornamento automatico non viene eseguito il primo giorno di rilascio di una nuova versione. Il tentativo di aggiornamento viene eseguito intenzionalmente con una tempistica casuale. Non allarmarsi se l'installazione non viene aggiornata immediatamente.

Se si sospetta la presenza di un errore, eseguire prima `Get-ADSyncAutoUpgrade` per verificare che l'aggiornamento automatico sia abilitato.

Avviare il visualizzatore eventi ed esaminare il registro eventi dell'**applicazione**. Aggiungere un filtro del registro eventi per l'origine relativa all'**aggiornamento di Azure AD Connect** e l'intervallo di ID evento **300-399**. ![Filtro del registro eventi per l'aggiornamento automatico](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogfilter.png)

Verranno visualizzati i registri eventi associati allo stato dell'aggiornamento automatico. ![Filtro del registro eventi per l'aggiornamento automatico](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogresult.png)

Il codice risultato presenta un prefisso con una panoramica dello stato.

Prefisso codice risultato | Descrizione
--- | ---
Esito positivo | L'installazione è stata aggiornata correttamente.
UpgradeAborted | Una condizione temporanea ha interrotto l'aggiornamento. Quest'ultimo verrà ripetuto nuovamente e si prevede che verrà eseguito correttamente in seguito.
UpgradeNotSupported | La configurazione del sistema impedisce l'aggiornamento automatico del sistema stesso. Verrà ripetuto per verificare se lo stato viene modificato, ma si prevede che il sistema debba essere aggiornato manualmente.

Di seguito è riportato un elenco dei messaggi più comuni. L'elenco non include tutti i messaggi, ma il messaggio dei risultati indicherà chiaramente il problema.

Messaggio dei risultati | Descrizione
--- | ---
**UpgradeAborted** |
UpgradeAbortedCouldNotSetUpgradeMarker | Non è possibile scrivere nel Registro di sistema.
UpgradeAbortedInsufficientDatabasePermissions | Il gruppo Administrators predefinito non ha le autorizzazioni per il database. Per risolvere il problema, eseguire manualmente l'aggiornamento alla versione più recente di Azure AD Connect.
UpgradeAbortedInsufficientDiskSpace | Lo spazio su disco sufficiente non è sufficiente per supportare un aggiornamento.
UpgradeAbortedSecurityGroupsNotPresent | Non è possibile trovare e risolvere tutti i gruppi di sicurezza usati dal motore di sincronizzazione.
UpgradeAbortedServiceCanNotBeStarted | L'avvio del servizio NT **Microsoft Azure AD Sync** non è riuscito.
UpgradeAbortedServiceCanNotBeStopped | L'arresto del servizio NT **Microsoft Azure AD Sync** non è riuscito.
UpgradeAbortedServiceIsNotRunning | Il servizio NT **Microsoft Azure AD Sync** non è in esecuzione.
UpgradeAbortedSyncCycleDisabled | L'opzione SyncCycle dell'[utilità di pianificazione](active-directory-aadconnectsync-feature-scheduler.md) è stata disabilitata.
UpgradeAbortedSyncExeInUse | L'[interfaccia utente di Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md) è aperta nel server.
UpgradeAbortedSyncOrConfigurationInProgress | L'installazione guidata è in esecuzione o è stata pianificata una sincronizzazione al di fuori dell'utilità di pianificazione.
**UpgradeNotSupported** |
UpgradeNotSupportedCustomizedSyncRules | Sono state aggiunte regole personalizzate alla configurazione.
UpgradeNotSupportedDeviceWritebackEnabled | È stata abilitata la funzionalità di [writeback dei dispositivi](active-directory-aadconnect-feature-device-writeback.md).
UpgradeNotSupportedGroupWritebackEnabled | È stata abilitata la funzionalità di [writeback dei gruppi](active-directory-aadconnect-feature-preview.md#group-writeback).
UpgradeNotSupportedInvalidPersistedState | L'installazione non è un aggiornamento delle impostazioni rapide o di DirSync.
UpgradeNotSupportedMetaverseSizeExceeeded | Il metaverse include più di 100.000 oggetti.
UpgradeNotSupportedMultiForestSetup | Viene stabilita la connessione a più foreste. L'installazione rapida si connetterà a una sola foresta.
UpgradeNotSupportedNonLocalDbInstall | Non si sta usando un database LocalDB di SQL Server Express.
UpgradeNotSupportedNonMsolAccount | L'[account AD Connect](active-directory-aadconnect-accounts-permissions.md#active-directory-account) non è più l'account MSOL\_ predefinito.
UpgradeNotSupportedStagingModeEnabled | Il server è in [modalità di gestione temporanea](active-directory-aadconnectsync-operations.md#staging-mode).
UpgradeNotSupportedUserWritebackEnabled | È stata abilitata la funzionalità di [writeback degli utenti](active-directory-aadconnect-feature-preview.md#user-writeback).

## Passaggi successivi
Ulteriori informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0629_2016-->