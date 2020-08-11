---
title: 'Azure AD Connect: Cronologia del rilascio delle versioni | Microsoft Docs'
description: Questo articolo elenca tutte le versioni di Azure AD Connect e Azure AD Sync
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 08/07/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b0b0a98e80fb7f343411b49ff2ccd11046bcdc5
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88052930"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: Cronologia delle versioni
Il team di Azure Active Directory (Azure AD) aggiorna regolarmente Azure AD Connect con nuove funzionalità. Le nuove funzionalità potrebbero non essere disponibili in tutti i paesi.

Lo scopo di questo articolo è consentire agli utenti di esaminare le versioni rilasciate e conoscere le modifiche della versione più recente.



Questa tabella contiene un elenco degli argomenti correlati:

Argomento |  Dettagli
--------- | --------- |
Passaggi da eseguire per l'aggiornamento da Azure AD Connect | Metodi per [eseguire l'aggiornamento da una versione precedente alla versione più recente](how-to-upgrade-previous-version.md) di Azure AD Connect.
Autorizzazioni necessarie | Per le autorizzazioni necessarie per applicare un aggiornamento, vedere [account e autorizzazioni](reference-connect-accounts-permissions.md#upgrade).
Download| [Scaricare Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771).

>[!NOTE]
>Il rilascio di una nuova versione di Azure AD Connect è un processo che richiede diversi passaggi di controllo della qualità per garantire la funzionalità operativa del servizio e, durante questo processo, il numero di una nuova versione e lo stato della versione verranno aggiornati in modo da riflettere lo stato più recente.
Durante questo processo, il numero della versione verrà visualizzato con una "X" nella posizione del numero di versione secondario, come in "1.3.X.0", che indica che le note sulla versione di questo documento sono valide per tutte le versioni che iniziano con "1.3". Non appena è stato completato il processo di rilascio, il numero di versione di rilascio verrà aggiornato alla versione rilasciata più di recente e lo stato della versione verrà aggiornato a "Rilasciato per il download e l'aggiornamento automatico".
Non tutte le versioni di Azure AD Connect saranno disponibili per l'aggiornamento automatico. Lo stato della versione indicherà se una versione sarà disponibile per l'aggiornamento automatico o solo per il download. Se l'aggiornamento automatico è stato abilitato nel server di Azure AD Connect, tale server eseguirà automaticamente l'aggiornamento per la versione più recente di Azure AD Connect, rilasciata per l'aggiornamento automatico. Si noti che non tutte le configurazioni di Azure AD Connect sono idonee per l'aggiornamento automatico. Seguire questo collegamento per altre informazioni sull'[aggiornamento automatico](how-to-connect-install-automatic-upgrade.md)

>[!IMPORTANT]
> A partire dal 1° novembre 2020, verrà avviata l'implementazione di un processo di deprecazione, in base al quale le versioni di Azure AD Connect rilasciate più di 18 mesi prima saranno deprecate. Da quel momento inizieremo questo processo deprecando tutte le versioni di Azure AD Connect con la versione 1.3.20.0 (rilasciata il 24/4/2019) e versioni precedenti e procederemo alla valutazione della deprecazione delle versioni precedenti di Azure AD Connect ogni volta che viene rilasciata una nuova versione.
>
> È necessario assicurarsi di eseguire una versione recente di Azure AD Connect per ricevere un'esperienza di supporto ottimale. 
>
>Se si esegue una versione deprecata di Azure AD Connect è possibile che non siano disponibili gli aggiornamenti di sicurezza più recenti, i miglioramenti delle prestazioni, la risoluzione dei problemi e gli strumenti di diagnostica e i miglioramenti del servizio e, se è necessaria l'assistenza, potrebbe non essere possibile fornire il livello di servizio necessario per l'organizzazione.
>
>Se è stata abilitata la sincronizzazione di Azure AD Connect, si inizierà a ricevere automaticamente notifiche sull'integrità che segnalano eventuali deprecazioni imminenti quando si esegue una delle versioni precedenti.
>
>Per altre informazioni su come aggiornare Azure AD Connect alla versione più recente, vedere [questo articolo](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version).
>
>Per informazioni sulla cronologia delle versioni nelle versioni deprecate, vedere [Azure ad Connect versione archivio cronologia](reference-connect-version-history-archive.md) delle versioni

## <a name="15450"></a>1.5.45.0

### <a name="release-status"></a>Stato della versione
07/29/2020: rilasciato per il download

### <a name="functional-changes"></a>Modifiche funzionali
Si tratta di una versione di correzione di bug. Non sono presenti modifiche funzionali in questa versione.

### <a name="fixed-issues"></a>Problemi risolti

- È stato risolto un problema per cui l'amministratore non è in grado di abilitare l'accesso Single Sign-on facile se l'account del computer AZUREADSSOACC è già presente nella "Active Directory".
- È stato risolto un problema che causava un errore di gestione temporanea durante l'importazione delta dell'API v2 per un oggetto in conflitto riparato tramite il portale per l'integrità.
- Correzione di un problema nella configurazione di importazione/esportazione in cui la regola personalizzata disabilitata è stata importata come abilitata.

## <a name="15420"></a>1.5.42.0

### <a name="release-status"></a>Stato della versione
07/10/2020: rilasciato per il download

### <a name="functional-changes"></a>Modifiche funzionali
Questa versione include un'anteprima pubblica della funzionalità per esportare la configurazione di un server di Azure AD Connect esistente in un. File JSON che può quindi essere usato durante l'installazione di un nuovo server Azure AD Connect per creare una copia del server originale.

Una descrizione dettagliata di questa nuova funzionalità è disponibile in [questo articolo](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-import-export-config)

### <a name="fixed-issues"></a>Problemi risolti
- Correzione di un bug in cui si verificherebbe un falso avviso sulle dimensioni del database locale nelle compilazioni localizzate durante l'aggiornamento.
- Correzione di un bug in cui si verificherebbe un errore falso negli eventi dell'app per il nome dell'account o lo scambio del nome di dominio.
- Correzione di un errore in cui non è possibile installare Azure AD Connect in un controller di dominio, restituendo l'errore "Impossibile trovare il membro".


## <a name="15300"></a>1.5.30.0

### <a name="release-status"></a>Stato della versione
07/05/2020: resa disponibile per il download

### <a name="fixed-issues"></a>Problemi risolti
Questa versione di aggiornamento rapido risolve un problema per cui i domini non selezionati venivano selezionati erroneamente dall'interfaccia utente della procedura guidata se erano stati selezionati solo i contenitori nipote.


>[!NOTE]
>Questa versione include la nuova API dell'endpoint V2 Azure AD Connect Sync.  Questo nuovo endpoint V2 è attualmente disponibile in anteprima pubblica.  Questa versione o una versione successiva richiede la nuova API endpoint V2.  Tuttavia, la semplice installazione di questa versione non abilita l'endpoint V2. Si continuerà a usare l'endpoint V1 a meno che non si abiliti l'endpoint V2.  Per abilitarlo e fornire il consenso esplicito per l'anteprima pubblica, è necessario seguire la procedura descritta in [API dell'endpoint V2 Azure AD Connect Sync (anteprima pubblica](how-to-connect-sync-endpoint-api-v2.md)).  

## <a name="15290"></a>1.5.29.0

### <a name="release-status"></a>Stato della versione
23/04/2020: resa disponibile per il download

### <a name="fixed-issues"></a>Problemi risolti
Questa versione di aggiornamento rapido risolve un problema introdotto nella versione 1.5.20.0, in cui un amministratore tenant con autenticazione a più fattori non era in grado di abilitare DSSO.

## <a name="15220"></a>1.5.22.0

### <a name="release-status"></a>Stato della versione
20/04/2020: resa disponibile per il download

### <a name="fixed-issues"></a>Problemi risolti
Questa versione di aggiornamento rapido risolve un problema della versione 1.5.20.0 se è stata clonata la regola **In from AD - Group Join** e non è stata clonata la regola **In from AD - Group Common**.

## <a name="15200"></a>1.5.20.0

### <a name="release-status"></a>Stato della versione
09/04/2020: resa disponibile per il download

### <a name="fixed-issues"></a>Problemi risolti
- Questa versione di aggiornamento rapido risolve un problema relativo alla versione 1.5.18.0 se è abilitata la funzionalità di filtri di gruppo e si usa mS-DS-ConsistencyGuid come ancoraggio di origine.
- È stato risolto un problema nel modulo ADSyncConfig di PowerShell, in cui richiamare il comando DSACLS usato in tutti i cmdlet Set-ADSync * per le autorizzazioni provocherebbe uno degli errori seguenti:
     - `GrantAclsNoInheritance : The parameter is incorrect.   The command failed to complete successfully.`
     - `GrantAcls : No GUID Found for computer …`

> [!IMPORTANT]
> Se la regola di sincronizzazione **In from AD - Group Join** è stata clonata e non è stata clonata la regola di sincronizzazione **In from AD - Group Common** e si prevede di eseguire l'aggiornamento, completare i passaggi seguenti come parte dell'aggiornamento:
> 1. Durante l'aggiornamento deselezionare l'opzione **Avvia il processo di sincronizzazione al termine della configurazione**.
> 2. Modificare la regola di sincronizzazione del join clonato e aggiungere le due trasformazioni seguenti:
>     - Impostare il flusso diretto `objectGUID` su `sourceAnchorBinary`.
>     - Impostare il flusso di espressione `ConvertToBase64([objectGUID])` su `sourceAnchor`.     
> 3. Abilitare l'utilità di pianificazione usando `Set-ADSyncScheduler -SyncCycleEnabled $true`.



## <a name="15180"></a>1.5.18.0

### <a name="release-status"></a>Stato della versione
02/04/2020: resa disponibile per il download

### <a name="functional-changes-adsyncautoupgrade"></a>Modifiche funzionali ADSyncAutoUpgrade 

- Aggiunta del supporto per la funzionalità mS-DS-ConsistencyGuid per gli oggetti di gruppo. In questo modo è possibile spostare gruppi tra foreste o riconnettere i gruppi in Active Directory ad Azure AD in cui è stato modificato il valore objectID del gruppo Active Directory, ad esempio quando un server di Active Directory viene ricompilato dopo una calamità. Per altre informazioni vedere [Esecuzione della migrazione di gruppi da una foresta a un'altra](how-to-connect-migrate-groups.md).
- L'attributo mS-DS-ConsistencyGuid viene impostato automaticamente su tutti i gruppi sincronizzati e non è necessario eseguire alcuna operazione per abilitare questa funzionalità. 
- Get-ADSyncRunProfile è stato rimosso perché non è più in uso. 
- È stato modificato l'avviso visualizzato quando si tenta di usare un account amministratore Enterprise o di dominio per l'account del connettore di Active Directory Domain Services per fornire un contesto maggiore. 
- È stato aggiunto un nuovo cmdlet per rimuovere gli oggetti dallo spazio connettore, lo strumento CSDelete.exe precedente è stato rimosso e sostituito con il nuovo cmdlet Remove-ADSyncCSObject. Il cmdlet Remove-ADSyncCSObject accetta CsObject come input. Questo oggetto può essere recuperato tramite il cmdlet Get-ADSyncCSObject.

>[!NOTE]
>Lo strumento CSDelete.exe precedente è stato rimosso e sostituito con il nuovo cmdlet Remove-ADSyncCSObject 

### <a name="fixed-issues"></a>Problemi risolti

- Correzione di un bug nella foresta writeback del gruppo/selettore OU durante la riesecuzione della procedura guidata Azure AD Connect dopo la disabilitazione della funzionalità. 
- È stata introdotta una nuova pagina di errore che viene visualizzata se mancano i valori del registro DCOM necessari con un nuovo collegamento alla guida. Le informazioni sono scritte anche nei file di log. 
- È stato risolto un problema relativo alla creazione dell'account di sincronizzazione Azure Active Directory in cui l'abilitazione di estensioni di directory o PHS potrebbe non riuscire perché prima dell'uso l'account non è stato propagato in tutte le repliche del servizio. 
- Correzione di un bug nell'utilità di compressione degli errori di sincronizzazione che non gestiva correttamente i caratteri surrogati. 
- Correzione di un bug per l'aggiornamento automatico che lasciava il server nello stato utilità di pianificazione sospesa. 

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>Stato della versione
12/9/2019: Versione per il download. Non disponibile tramite aggiornamento automatico.
### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità
- La sincronizzazione dell'hash delle password è stata aggiornata per Azure AD Domain Services per tenere in considerazione correttamente la spaziatura interna negli hash Kerberos.  In questo modo si otterrà un miglioramento delle prestazioni durante la sincronizzazione delle password da pubblici AD a Azure AD Domain Services.
- È stato aggiunto il supporto per le sessioni affidabili tra l'agente di autenticazione e il bus di servizio.
- Questa versione applica TLS 1.2 per la comunicazione tra l'agente di autenticazione e i servizi cloud.
- È stata aggiunta una cache DNS per le connessioni WebSocket tra l'agente di autenticazione e i servizi cloud.
- È stata aggiunta la possibilità di indirizzare un agente specifico dal cloud per testare la connettività degli agenti.

### <a name="fixed-issues"></a>Problemi risolti
- La versione 1.4.18.0 presentava un bug in cui il cmdlet di PowerShell per DSSO usava le credenziali di accesso di Windows anziché le credenziali di amministratore fornite durante l'esecuzione di PS. Di conseguenza, non è stato possibile abilitare ICTAMMOLO in più foreste tramite l'interfaccia utente di Azure AD Connect. 
- È stata apportata una correzione per abilitare ICTAMMOLO simultaneamente in tutti gli insiemi di strutture tramite l'interfaccia utente di Azure AD Connect

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>Stato della versione
11/08/2019: resa disponibile per il download. Non disponibile tramite aggiornamento automatico.

>[!IMPORTANT]
>A causa di una modifica dello schema interno in questa versione di Azure AD Connect, se si gestiscono impostazioni di configurazione della relazione di trust AD FS con MSOnline PowerShell, è necessario aggiornare il modulo MSOnline PowerShell alla versione 1.1.183.57 o successiva.

### <a name="fixed-issues"></a>Problemi risolti

Questa versione corregge un problema con i dispositivi di Azure AD ibrido aggiunti a quelli esistenti. Questa versione contiene una nuova regola di sincronizzazione del dispositivo che corregge questo problema.
La modifica di questa regola può causare l'eliminazione di dispositivi obsoleti da Azure AD. Questo non è un problema, perché questi oggetti dispositivo non vengono usati da Azure AD durante l'autorizzazione dell'accesso condizionale. Per alcuni clienti, il numero di dispositivi che verranno eliminati tramite questa modifica della regola può superare la soglia di eliminazione. Se l'eliminazione degli oggetti dispositivo in Azure AD supera la soglia di eliminazione dell'esportazione, è consigliabile consentire l'eliminazione delle eliminazioni. [Come consentire il flusso delle eliminazioni quando superano la soglia di eliminazione](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>Stato della versione
28/9/2019: resa disponibile per l'aggiornamento automatico per selezionare i tenant. Non disponibile per il download.

Questa versione corregge un bug in cui alcuni server aggiornati automaticamente da una versione precedente alla versione 1.4.18.0 hanno riscontrato problemi con la reimpostazione della password self-service (SSPR) e il writeback delle password.

### <a name="fixed-issues"></a>Problemi risolti

In determinate circostanze, i server che sono stati aggiornati automaticamente alla versione 1.4.18.0 non riabilitano la reimpostazione della password self-service e il writeback delle password al termine dell'aggiornamento. Questa versione di aggiornamento automatico corregge il problema e riattiva la reimpostazione della password self-service e il writeback delle password.

Correzione di un bug nell'utilità di compressione degli errori di sincronizzazione che non gestiva correttamente i caratteri surrogati.

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>È in corso l'analisi di un evento imprevisto in cui alcuni clienti riscontrano problemi con i dispositivi esistenti Azure AD ibrido aggiunti dopo l'aggiornamento a questa versione di Azure AD Connect. Si consiglia ai clienti che hanno distribuito un join Azure AD ibrido di posticipare l'aggiornamento a questa versione fino a quando la causa principale di questi problemi non sarà identificata e risolta. Verranno fornite altre informazioni il prima possibile.

>[!IMPORTANT]
>Con questa versione di Azure AD Connect alcuni clienti potrebbero vedere alcuni o tutti i dispositivi Windows scomparire da Azure AD. Questo non è un problema, perché queste identità di dispositivo non vengono usate da Azure AD durante l'autorizzazione dell'accesso condizionale. Per altre informazioni vedere [Informazioni sulla scomparsa di dispositivi Azure AD Connect 1.4.xx.x](reference-connect-device-disappearance.md)


### <a name="release-status"></a>Stato della versione
25/9/2019: resa disponibile solo per l'aggiornamento automatico.

### <a name="new-features-and-improvements"></a>Miglioramenti e nuove funzionalità
- I nuovi strumenti per la risoluzione dei problemi consentono di risolvere i problemi relativi agli scenari di "non sincronizzazione dell'utente", "non sincronizzazione del gruppo" o "membri del gruppo non sincronizzati".
- Aggiungere il supporto per i cloud nazionali in Azure AD Connect script per la risoluzione dei problemi 
- I clienti devono essere informati che gli endpoint WMI deprecati per MIIS_Service ora sono stati rimossi. Tutte le operazioni WMI dovrebbero ora essere eseguite tramite i cmdlet di PS.
- Miglioramento della sicurezza reimpostando la delega vincolata sull'oggetto AZUREADSSOACC
- Quando si aggiunge o modifica una regola di sincronizzazione, se nello schema del connettore sono presenti attributi usati nella regola, ma non aggiunti al connettore, gli attributi vengono aggiunti automaticamente al connettore. Lo stesso vale per il tipo di oggetto su cui la regola ha effetto. Se viene aggiunto un elemento al connettore, il connettore verrà contrassegnato per l'importazione completa al successivo ciclo di sincronizzazione.
- L'uso di un amministratore Enterprise o di dominio come account connettore non è più supportato nelle nuove distribuzioni di Azure AD Connect. Le distribuzioni di Azure AD Connect correnti che usano un amministratore aziendale o di dominio come account del connettore non saranno interessate da questa versione.
- In Gestione sincronizzazione viene eseguita una sincronizzazione completa per la creazione/modifica/eliminazione di regole. Per qualsiasi modifica della regola, viene visualizzata una finestra popup che informa l'utente se verrà eseguita l'importazione completa o la sincronizzazione completa.
- Sono stati aggiunti dei passaggi di mitigazione per gli errori relativi alle password nella pagina "connettori > proprietà > connettività"
- È stato aggiunto un avviso di deprecazione per Sync Service Manager nella pagina delle proprietà del connettore. Questo avviso informa l'utente che le modifiche devono essere apportate tramite la procedura guidata di Azure AD Connect.
- È stato aggiunto un nuovo errore per i problemi relativi ai criteri password di un utente.
- Impedire la configurazione errata dei filtri di gruppo per filtri di dominio e unità organizzativa. Quando il dominio o l'unità organizzativa del gruppo immesso è già stato escluso, i filtri di gruppo visualizzano un errore e l'utente non può andare avanti fino alla risoluzione del problema.
- Gli utenti non possono più creare un connettore per Active Directory Domain Services o Windows Azure Active Directory nell'interfaccia utente di Synchronization Service Manager.
- Accessibilità fissa dei controlli dell'interfaccia utente personalizzati in Synchronization Service Manager.
- Sono state abilitate sei attività di gestione federative per tutti i metodi di accesso in Azure AD Connect.  (In precedenza, per tutti gli accessi era disponibile solo l'attività "Aggiorna AD FS certificato TLS/SSL".)
- È stato aggiunto un avviso che viene visualizzato quando si modifica il metodo di accesso dalla federazione a PHS o PTA. L'avviso informa che tutti i domini e gli utenti di Azure AD verranno convertiti in autenticazione gestita.
- Sono stati rimossi i certificati per la firma di token dall'attività "Reimposta Azure AD e attendibilità AD FS" ed è stata aggiunta una sottoattività separata per aggiornare questi certificati.
- È stata aggiunta una nuova attività di gestione della federazione denominata "Gestione certificati" con attività secondarie per aggiornare i certificati TLS o per la firma di token per la farm AD FS.
- È stata aggiunta una nuova sottoattività di gestione della federazione denominata "Definizione del server primario", che consente agli amministratori di specificare un nuovo server primario per la farm AD FS.
- È stata aggiunta una nuova attività di gestione della federazione denominata "Gestione server" con attività secondarie per la distribuzione di un server di AD FS, la distribuzione di un server proxy applicazione Web e la definizione del server primario.
- È stata aggiunta una nuova attività di gestione delle federazione denominata "Visualizza la configurazione della federazione", che visualizza le impostazioni di AD FS correnti.  (A causa di questa aggiunta, le impostazioni AD FS sono state rimosse dalla pagina "Verifica della soluzione".)

### <a name="fixed-issues"></a>Problemi risolti
- Risoluzione del problema di errore di sincronizzazione per lo scenario in cui un oggetto utente che acquisisce l'oggetto contatto corrispondente ha un riferimento automatico (ad esempio, l'utente è il proprio manager).
- I popup della guida ora vengono visualizzati sullo stato attivo della tastiera.
- Per l'aggiornamento automatico, se un'app in conflitto è in esecuzione da 6 ore, terminare e continuare con l'aggiornamento.
- Limitazione del numero di attributi che un cliente può selezionare a 100 per oggetto quando si selezionano le estensioni della directory. In questo modo si eviterà che l'errore si verifichi durante l'esportazione poiché Azure ha un massimo di 100 attributi di estensione per oggetto.
- Correzione di un bug per rendere più affidabile lo script AD Connectivity
- Correzione di un bug per rendere Azure AD Connect installare in un computer utilizzando un servizio WCF Named Pipes esistente più affidabile.
- Diagnostica migliorata e risoluzione dei problemi relativi a criteri di gruppo che non consentono l'avvio del servizio ADSync al momento dell'installazione iniziale.
- Correzione di un bug per cui il nome visualizzato per un computer Windows non era scritto correttamente.
- Correzione di un bug in cui il tipo di sistema operativo per un computer Windows non era scritto correttamente.
- Correzione di un bug per cui i computer non Windows 10 venivano sincronizzati in modo imprevisto. In conseguenza a questa modifica, i computer non Windows 10 sincronizzati in precedenza verranno ora eliminati. Questa operazione non influisce sulle funzionalità perché la sincronizzazione dei computer Windows viene usata solo per l'aggiunta a un dominio Azure AD ibrido, che funziona solo per i dispositivi Windows 10.
- Sono stati aggiunti diversi nuovi cmdlet (interni) al modulo PowerShell ADSync.


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>C'è un problema noto relativo all'aggiornamento di Azure AD Connect da una versione precedente alla versione 1.3.21.0, in cui il portale di O365 non riflette la versione aggiornata anche se Azure AD Connect è stato aggiornato correttamente.
>
> Per risolvere questo problema, è necessario importare il modulo **AdSync**, quindi eseguire il cmdlet di PowerShell`Set-ADSyncDirSyncConfiguration` nel server Azure AD Connect.  È possibile seguire la procedura seguente:
>
>1. Aprire PowerShell in modalità di amministratore.
>2. Eseguire `Import-Module "ADSync"`.
>3. Eseguire `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""`.
 
### <a name="release-status"></a>Stato della versione 

14/05/2019: resa disponibile per il download

### <a name="fixed-issues"></a>Problemi risolti 

- Correzione di una vulnerabilità di elevazione dei privilegi presente nella versione 1.3.20.0 di Microsoft Azure Active Directory Connect.  Questa vulnerabilità, in determinate condizioni, può consentire a un utente malintenzionato di eseguire due cmdlet di PowerShell nel contesto di un account con privilegi ed eseguire azioni con privilegi.  Questa patch di sicurezza risolve il problema disabilitando questi cmdlet. Per altre informazioni, vedere [Patch di sicurezza](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000).


## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
