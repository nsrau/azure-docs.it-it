<properties
   pageTitle="Azure AD Connect: Cronologia delle versioni | Microsoft Azure"
   description="Questo argomento elenca tutte le versioni di Azure AD Connect e Azure AD Sync."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/11/2016"
   ms.author="andkjell"/>

# Azure AD Connect: Cronologia delle versioni

Il team di Azure Active Directory aggiorna regolarmente Azure AD Connect con nuove funzionalità. Le nuove funzionalità potrebbero non essere disponibili in tutti i paesi.

Lo scopo di questo articolo è consentire agli utenti di esaminare le versioni rilasciate e verificare l'opportunità di effettuare l'aggiornamento alla versione più recente.

Di seguito è riportato l'elenco degli argomenti correlati:

| Argomento | |
| --------- | --------- |
| Passaggi da eseguire per l'aggiornamento da Azure AD Connect | Metodi per [eseguire l'aggiornamento da una versione precedente alla versione più recente](active-directory-aadconnect-upgrade-previous-version.md) di Azure AD Connect. |
| Autorizzazioni necessarie | Per le autorizzazioni necessarie per applicare un aggiornamento, vedere [account e autorizzazioni](active-directory-aadconnect-accounts-permissions.md#upgrade) |
| Download| [Scaricare Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) |

## 1\.1.180.0
Data di rilascio: maggio 2016

**Nuove funzionalità:**

- Visualizza avvisi e indicazioni riguardo alla verifica dei domini se la verifica non è stata effettuata prima di eseguire Azure AD Connect.
- Aggiunto il supporto per [Microsoft Cloud Germany](active-directory-aadconnect-instances.md#microsoft-cloud-germany).
- Aggiunto il supporto per la versione più recente dell'infrastruttura [cloud di Microsoft Azure per enti pubblici](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud) con nuovi requisiti URL.

**Problemi risolti e miglioramenti:**

- Aggiunti i filtri all'editor delle regole di sincronizzazione per semplificare il reperimento delle regole.
- Miglioramento delle prestazioni quando si elimina uno spazio connettore.
- Risolto un problema che si verificava quando lo stesso oggetto veniva sia eliminato che aggiunto nella stessa esecuzione (elimina/aggiungi).
- Una regola di sincronizzazione disabilitata non verrà più riabilitata, inclusi oggetti e attributi di aggiornamento del sistema o dello schema della directory.

## 1\.1.130.0
Data di rilascio: aprile 2016

**Nuove funzionalità:**

- Aggiunto il supporto per gli attributi multivalore alle [Estensioni della directory](active-directory-aadconnectsync-feature-directory-extensions.md).
- Aggiunto il supporto per altre varianti di configurazione dell'[aggiornamento automatico](active-directory-aadconnect-feature-automatic-upgrade.md) da considerare idonee per l'aggiornamento.
- Aggiunta di alcuni cmdlet per l'[utilità di pianificazione personalizzata](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler).

## 1\.1.119.0
Data di rilascio: marzo 2016

**Problemi risolti:**

- È stato verificato che non sia possibile usare l'installazione rapida in Windows Server 2008 (pre-R2), poiché la sincronizzazione delle password non è supportata in questo sistema operativo.
- L'aggiornamento da DirSync con una configurazione personalizzata del filtro non funzionava come previsto.
- Durante l'aggiornamento a una versione più recente, senza modifiche alla configurazione, è consigliabile non pianificare una importazione/sincronizzazione completa.

## 1\.1.110.0
Data di rilascio: febbraio 2016

**Problemi risolti:**

- L'aggiornamento da versioni precedenti non funziona se l'installazione non viene eseguita nella cartella predefinita **C:\\Programmi**.
- Se si esegue l'installazione e si deseleziona **Avvia il processo di sincronizzazione** al termine dell'installazione guidata, una nuova esecuzione dell'installazione guidata non abilita l'utilità di pianificazione.
- L'utilità di pianificazione non funzionerà come previsto nei server in cui il formato di data/ora non è US-en. `Get-ADSyncScheduler` non potrà restituire gli orari corretti.
- Se è stata installata una versione precedente di Azure AD Connect con ADFS come opzione di accesso e aggiornamento, non è possibile eseguire nuovamente l'installazione guidata.

## 1\.1.105.0
Data di rilascio: febbraio 2016

**Nuove funzionalità:**

- Funzionalità di [aggiornamento automatico](active-directory-aadconnect-feature-automatic-upgrade.md) per i clienti con impostazioni rapide.
- Supporto per l'uso di MFA e PIM da parte dell'amministratore globale nell'installazione guidata.
    - Se si usa l'autenticazione MFA, è necessario impostare il proxy in modo che consenta il traffico per https://secure.aadcdn.microsoftonline-p.com.
    - Per il corretto funzionamento dell'autenticazione MFA, è necessario aggiungere https://secure.aadcdn.microsoftonline-p.com all'elenco di siti attendibili.
- Possibilità di modificare il metodo di accesso dell'utente dopo l'installazione iniziale.
- Possibilità di usare i [filtri basati su dominio e unità organizzativa](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) nell'installazione guidata. Ciò consente anche la connessione a foreste in cui non sono disponibili tutti i domini.
- L'[utilità di pianificazione](active-directory-aadconnectsync-feature-scheduler.md) è incorporata nel motore di sincronizzazione.

**Funzionalità passate dal livello di anteprima al livello di disponibilità generale:**

- [Writeback dei dispositivi](active-directory-aadconnect-feature-device-writeback.md).
- [Estensioni della directory](active-directory-aadconnectsync-feature-directory-extensions.md).

**Nuove funzionalità di anteprima:**

- Il nuovo intervallo predefinito per i cicli di sincronizzazione è pari a 30 minuti. In tutte le versioni precedenti è pari a 3 ore. Aggiunge il supporto per la modifica del comportamento dell'[utilità di pianificazione](active-directory-aadconnectsync-feature-scheduler.md).

**Problemi risolti:**

- La pagina di verifica dei domini DNS non riconosceva sempre i domini.
- Richiesta di credenziali di amministratore del dominio durante la configurazione di AD FS.
- Gli account AD locali non vengono riconosciuti dall'installazione guidata se si trovano in un dominio con un albero DNS diverso da quello del dominio radice.

## 1\.0.9131.0
Data di rilascio: dicembre 2015

**Problemi risolti:**

- La sincronizzazione delle password potrebbe non funzionare quando si modificano le password in AD DS, ma funziona quando si imposta la password.
- Quando si dispone di un server proxy, l'autenticazione ad Azure AD potrebbe non riuscire durante l'installazione o durante un aggiornamento nella pagina di configurazione.
- L'aggiornamento da una versione precedente di Azure AD Connect con una versione completa di SQL Server avrà esito negativo se non si è SA in SQL.
- L'aggiornamento da una versione precedente di Azure AD Connect con una versione remota di SQL Server mostrerà il messaggio di errore "Impossibile accedere al database SQL di ADSync".

## 1\.0.9125.0
Data di rilascio: novembre 2015

**Nuove funzionalità:**

- È possibile riconfigurare l’ADFS a trust AD Azure.
- È possibile aggiornare lo schema di Active Directory e rigenerare le regole di sincronizzazione.
- È possibile disattivare una regola di sincronizzazione.
- È possibile definire "AuthoritativeNull" come un nuovo valore letterale in una regola di sincronizzazione.

**Nuove funzionalità di anteprima:**

- [Azure AD Connect Health per la sincronizzazione](active-directory-aadconnect-health-sync.md).
- Supporto per la sincronizzazione della password dei [Servizi di dominio di Azure AD](active-directory-get-started.md)

**Nuovo scenario supportato:**

- Supporta più organizzazioni di Exchange locali Per altre informazioni, vedere la pagina [Distribuzioni ibride con più foreste di Active Directory](https://technet.microsoft.com/library/jj873754.aspx)

**Problemi risolti:**

- Problemi sulla sincronizzazione delle password:
    - Un oggetto spostato dal all’esterno all’interno dell’ambito non avrà la password sincronizzata. Questo include unità Organizzativa e filtro attributi.
    - Selezione di una nuova unità Organizzativa da includere nella sincronizzazione non richiede una sincronizzazione completa della password.
    - Quando un utente disabilitato è abilitato la password non viene sincronizzata.
    - La coda di tentativi di password è infinita e il limite precedente di 5.000 oggetti da ritirare è stato rimosso.
    - [Risoluzione dei problemi migliorata](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization).
- Impossibile connettersi ad Active Directory con livello di funzionalità foresta Windows Server 2016.
- Impossibile modificare il gruppo utilizzato per il filtro di gruppo dopo l'installazione iniziale.
- Non si creerà un nuovo profilo utente nel server di Azure AD Connect per ogni utente che effettua una modifica della password con attivato il writeback delle password.
- Non è possibile usare valori Intero lungo negli ambiti di regole sincronizzate.
- La casella di controllo "dispositivo writeback" rimane disabilitato se sono presenti controller di dominio non raggiungibili.

## 1\.0.8667.0
Data di rilascio: agosto 2015

**Nuove funzionalità:**

- L'installazione guidata di Azure AD Connect è ora localizzata in tutte le lingue in cui è disponibile Windows Server.
- È stato aggiunto il supporto per lo sblocco dell'account quando si usa la gestione delle password di Azure AD.

**Problemi risolti:**

- Si verifica un arresto anomalo dell'installazione guidata di Azure AD Connect se l'installazione viene continuata da un utente diverso da quello che l'ha avviata.
- Se una disinstallazione precedente di Azure AD Connect non riesce a disinstallare completamente il servizio di sincronizzazione Azure AD Connect, non sarà possibile effettuare la reinstallazione.
- Non è possibile installare Azure AD Connect usando l'installazione rapida se l'utente non si trova nel dominio radice della foresta o se viene usata una versione di Active Directory in una lingua diversa dall'inglese.
- Se il nome di dominio completo dell'account utente di Active Directory non può essere risolto, verrà visualizzato un messaggio di errore fuorviante analogo a "Commit dello schema non riuscito".
- Se l'account usato in Active Directory Connector viene modificato all'esterno della procedura guidata, le esecuzioni successive della procedura guidata avranno esito negativo.
- A volte non è possibile installare Azure AD Connect in un controller di dominio.
- Non è possibile abilitare e disabilitare la "Modalità di gestione temporanea" se sono stati aggiunti attributi di estensione.
- Il writeback delle password ha esito negativo in alcune configurazioni a causa di una password non valida in Active Directory Connector.
- Non è possibile aggiornare DirSync se si usa dn nel filtro di attributi.
- Utilizzo eccessivo della CPU quando si utilizza la reimpostazione della password.

**Funzionalità di anteprima rimosse:**

- La funzionalità di anteprima [Utente writeback](active-directory-aadconnect-feature-preview.md#user-writeback) è stata temporaneamente rimossa in base ai suggerimenti espressi dai clienti dell’anteprima. Verrà aggiunto di nuovo in un secondo momento, una volta valutato il feedback fornito.

## 1\.0.8641.0
Data di rilascio: giugno 2015

**Rilascio iniziale di Azure AD Connect.**

Il nome è stato modificato da Azure AD Sync ad Azure AD Connect.

**Nuove funzionalità:**

- Installazione mediante le [impostazioni rapide](active-directory-aadconnect-get-started-express.md)
- È possibile [configurare ADFS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs):
- È possibile [aggiornare da DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md)
- [Impedire eliminazioni accidentali](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
- Introdotta la [modalità di gestione temporanea](active-directory-aadconnectsync-operations.md#staging-mode)

**Nuove funzionalità di anteprima:**

- [Writeback degli utenti](active-directory-aadconnect-feature-preview.md#user-writeback)
- [Writeback dei gruppi](active-directory-aadconnect-feature-preview.md#group-writeback)
- [Writeback dei dispositivi](active-directory-aadconnect-feature-device-writeback.md)
- [Estensioni della directory](active-directory-aadconnect-feature-preview.md#directory-extensions)


## 1\.0.494.0501
Data di rilascio: maggio 2015

**Nuovo requisito:**

- Azure AD Sync richiede ora .NET Framework versione 4.5.1 per l'installazione.

**Problemi risolti:**

- Il writeback delle password da Azure AD ha esito negativo con un errore di connettività del bus di servizio.

## 1\.0.491.0413
Data di rilascio: aprile 2015

**Problemi risolti e miglioramenti:**

- Active Directory Connector non elabora correttamente le eliminazioni se il Cestino è abilitato e sono presenti più domini nella foresta.
- Le prestazioni delle operazioni di importazione sono state migliorate per Azure Active Directory Connector.
- Quando un gruppo supera il limite per le appartenenze (per impostazione predefinita, il limite è impostato su 50.000 oggetti), viene eliminato in Azure Active Directory. Il nuovo comportamento prevede la conservazione del gruppo e la generazione di un errore. Non verranno inoltre esportate nuove modifiche relative all'appartenenza.
- Non è possibile effettuare il provisioning di un nuovo oggetto se nello spazio del connettore è già presente un'eliminazione a fasi con lo stesso nome di dominio.
- Alcuni oggetti sono contrassegnati per la sincronizzazione durante una sincronizzazione Delta, anche se non sono previste modifiche a fasi sull'oggetto.
- La forzatura della sincronizzazione delle password rimuove anche l'elenco dei controller di dominio preferiti.
- Si sono verificati problemi di CSExportAnalyzer con alcuni stati degli oggetti.

**Nuove funzionalità:**

- Un join può ora connettersi al tipo di oggetto "ANY" nel metaverse.

## 1\.0.485.0222
Data di rilascio: febbraio 2015

**Miglioramenti:**

- Prestazioni migliorate per l'importazione.

**Problemi risolti:**

- La sincronizzazione delle password rispetta l'attributo cloudFiltered usato dal filtro di attributi. Gli oggetti filtrati non saranno più inclusi nell'ambito per la sincronizzazione delle password.
- Nelle rare situazioni in cui la topologia include un numero molto elevato di controller di dominio, la sincronizzazione delle password non funziona.
- Errore di tipo "server arrestato" durante l'importazione da Azure AD Connector dopo l'abilitazione della gestione dei dispositivi in Azure AD/Intune.
- L'aggiunta di entità di protezione esterne da più domini nella stessa foresta provoca un errore di join ambiguo.

## 1\.0.475.1202
Data di rilascio: dicembre 2014

**Nuove funzionalità:**

- È ora supportata l'esecuzione della sincronizzazione delle password con filtri basati sugli attributi. Per informazioni dettagliate, vedere [Sincronizzazione delle password con i filtri](active-directory-aadconnectsync-configure-filtering.md).
- L'attributo msDS-ExternalDirectoryObjectID viene scritto di nuovo in AD. Ciò aggiunge supporto per le applicazioni Office 365 usando OAuth2 per accedere alle cassette postali online e locali in una distribuzione ibrida di Exchange.

**Problemi di aggiornamento risolti:**

- Nel server è disponibile una versione più recente dell'Assistente per l'accesso.
- Per l'installazione di Azure AD Sync è stato usato un percorso di installazione personalizzato.
- Un criterio di join personalizzato non valido blocca l'aggiornamento.

**Altre correzioni:**

- I problemi dei modelli per Office Pro Plus sono stati risolti.
- I problemi di installazione provocati dai nomi utente che iniziano con un trattino sono stati risolti.
- Il problema relativo alla perdita dell'impostazione sourceAnchor durante la seconda esecuzione dell'Installazione guidata è stato risolto.
- Il problema relativo alla traccia ETW per la sincronizzazione delle password è stato risolto.

## 1\.0.470.1023
Data di rilascio: ottobre 2014

**Nuove funzionalità:**

- Sincronizzazione delle password da più istanze di AD locali ad Azure AD.
- Interfaccia utente di installazione localizzata per tutte le lingue in cui è disponibile Windows Server.

**Aggiornamento da AADSync 1.0 GA**

Se è già stato installato Azure AD Sync, è necessario eseguire un'altra operazione nel caso in cui siano state modificate le regole di sincronizzazione predefinite. In seguito all'aggiornamento alla versione 1.0.470.1023, le regole di sincronizzazione modificate vengono duplicate. Per ogni regola di sincronizzazione modificata eseguire le operazioni seguenti:

- Trovare la regola di sincronizzazione modificata e prendere nota delle modifiche.
- Eliminare la regola di sincronizzazione.
- Trovare la nuova regola di sincronizzazione creata da Azure AD Sync e riapplicare le modifiche.

**Autorizzazioni per l'account di AD**

Per poter leggere gli hash delle password da AD, è necessario concedere autorizzazioni aggiuntive all'account di AD. Le autorizzazioni da concedere sono "Replica modifiche directory" e "Replica di tutte le modifiche directory". Per poter leggere gli hash delle password, sono necessarie entrambe le autorizzazioni.

## 1\.0.419.0911
Data di rilascio: settembre 2014

**Rilascio iniziale di Azure AD Sync.**

## Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0518_2016-->