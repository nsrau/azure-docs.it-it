---
title: 'Azure AD Connect: Cronologia delle versioni | Microsoft Docs'
description: Questo articolo elenca tutte le versioni di Azure AD Connect e Azure AD Sync
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/08/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: d89135c8f3d5011d7549158a29050e3569defbcc
ms.lasthandoff: 03/23/2017


---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: Cronologia delle versioni
Il team di Azure Active Directory (Azure AD) aggiorna regolarmente Azure AD Connect con nuove funzionalità. Le nuove funzionalità potrebbero non essere disponibili in tutti i paesi.

Lo scopo di questo articolo è consentire agli utenti di esaminare le versioni rilasciate e verificare l'opportunità di effettuare l'aggiornamento alla versione più recente.

Di seguito è riportato un elenco degli argomenti correlati:


Argomento |  Dettagli
--------- | --------- |
Passaggi da eseguire per l'aggiornamento da Azure AD Connect | Metodi per [eseguire l'aggiornamento da una versione precedente alla versione più recente](active-directory-aadconnect-upgrade-previous-version.md) di Azure AD Connect.
Autorizzazioni necessarie | Per le autorizzazioni necessarie per applicare un aggiornamento, vedere [account e autorizzazioni](./active-directory-aadconnect-accounts-permissions.md#upgrade).
Scaricare| [Scaricare Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="114430"></a>1.1.443.0
Data di rilascio: marzo 2017

**Problemi risolti:**

Servizio di sincronizzazione Azure AD Connect
* Risolto un problema che causava un errore nella procedura guidata di Azure AD Connect se il nome visualizzato di Azure AD Connector non conteneva il dominio onmicrosoft.com iniziale assegnato al tenant di Azure AD.
* Risolto un problema che causava un errore nella procedura guidata di Azure AD Connect durante la connessione al database SQL quando la password dell'account del servizio di sincronizzazione conteneva caratteri speciali come apostrofi, due punti e spazi.
* Risolto un problema che causava l'errore "The dimage has an anchor that is different than the image" (Dimage contiene un ancoraggio diverso dall'immagine) in un server Azure AD Connect in modalità di gestione temporanea, dopo aver escluso temporaneamente un oggetto AD locale dalla sincronizzazione e averlo nuovamente incluso.
* Risolto un problema che causava l'errore "The object located by DN is a phantom" (L'oggetto localizzato da DN è un fantasma) in un server Azure AD Connect in modalità di gestione temporanea, dopo aver escluso temporaneamente un oggetto AD locale dalla sincronizzazione e averlo nuovamente incluso.

Gestione di AD FS.
* Risolto un problema per cui la procedura guidata di Azure AD Connect non aggiornava la configurazione di AD FS e non impostava le attestazioni corrette nel trust della relying party dopo aver configurato un ID di accesso alternativo.
* Risolto un problema per cui la procedura guidata di Azure AD Connect non era in grado di gestire correttamente i server AD FS con account di servizio configurati usando il formato userPrincipalName invece del formato sAMAccountName.

Autenticazione pass-through
* Risolto un problema che causava un errore nella procedura guidata di Azure AD Connect se si selezionava Autenticazione pass-through ma la registrazione del relativo connettore aveva esito negativo.
* Risolto un problema che causava il bypass dei controlli di convalida da parte di Azure AD Connect sul metodo di accesso selezionato con la funzionalità Desktop SSO attivata.

Reimpostazione delle password
* Correzione di un problema che potrebbe causare il mancato tentativo di riconnessione da parte del server Azure AAD Connect, se la connessione è stata terminata da un firewall o un proxy.

**Nuove funzionalità o miglioramenti:**

Servizio di sincronizzazione Azure AD Connect
* Il cmdlet Get-ADSyncScheduler restituisce ora una nuova proprietà booleana denominata SyncCycleInProgress. Se il valore restituito è true, significa che è in corso un ciclo di sincronizzazione pianificato.
* La cartella di destinazione per archiviare i log di installazione e configurazione di Azure AD Connect è stata modificata da %localappdata%\AADConnect a %programdata%\AADConnect per migliorare l'accessibilità.

Gestione di AD FS.
* Aggiunto il supporto per l'aggiornamento del certificato SSL Farm AD FS.
* Aggiunto il supporto per la gestione di AD FS 2016.
* È ora possibile specificare un gMSA (account del servizio gestito di gruppo) durante l'installazione di AD FS.
* È ora possibile configurare SHA-256 come algoritmo di hash della firma per il trust della relying party di Azure AD.

Reimpostazione delle password
* Miglioramenti per consentire al prodotto di funzionare in ambienti con regole più severe del firewall.
* Migliore affidabilità della connessione al bus di servizio.

## <a name="113800"></a>1.1.380.0
Data di rilascio: dicembre 2016

**Problema risolto:**

* Risolto il problema secondo cui in questa build manca la regola di attestazione issuerid per Active Directory Federation Services (AD FS).

>[!NOTE]
>Questa build non è disponibile ai clienti tramite la funzionalità di aggiornamento automatico di Azure AD Connect.

## <a name="113710"></a>1.1.371.0
Data di rilascio: dicembre 2016

**Problema noto:**

* La regola di attestazione issuerid per AD FS manca in questa build. La regola di attestazione issuerid è obbligatoria se si esegue la federazione di più domini con Azure Active Directory (Azure AD). Se si utilizza Azure AD Connect per gestire la distribuzione di AD FS in locale, l'aggiornamento a questa build rimuoverà la regola di attestazione issuerid esistente dalla configurazione di AD FS. È possibile risolvere il problema aggiungendo la regola dopo l'installazione o l'aggiornamento. Per altre informazioni sull'aggiunta della regola di attestazione issuerid, fare riferimento a questo articolo in [Supporto di più domini per la federazione con Azure AD](active-directory-aadconnect-multiple-domains.md).

**Problema risolto:**

* L'installazione o l'aggiornamento di Azure AD Connect ha esito negativo se la porta 9090 non è aperta per la connessione in uscita.

>[!NOTE]
>Questa build non è disponibile ai clienti tramite la funzionalità di aggiornamento automatico di Azure AD Connect.

## <a name="113700"></a>1.1.370.0
Data di rilascio: dicembre 2016

**Problemi noti:**

* La regola di attestazione issuerid per AD FS manca in questa build. La regola di attestazione issuerid è obbligatoria se si esegue la federazione di più domini con Azure AD. Se si utilizza Azure AD Connect per gestire la distribuzione di AD FS in locale, l'aggiornamento a questa build rimuoverà la regola di attestazione issuerid esistente dalla configurazione di AD FS. È possibile risolvere il problema aggiungendo la regola dopo l'installazione o l'aggiornamento. Per altre informazioni sull'aggiunta della regola di attestazione issuerid, fare riferimento a questo articolo in [Supporto di più domini per la federazione con Azure AD](active-directory-aadconnect-multiple-domains.md).
* La porta 9090 deve essere aperta in uscita per completare l'installazione.

**Nuove funzionalità:**

* Autenticazione pass-through (anteprima).

>[!NOTE]
>Questa build non è disponibile ai clienti tramite la funzionalità di aggiornamento automatico di Azure AD Connect.

## <a name="113430"></a>1.1.343.0
Data di rilascio: novembre 2016

**Problema noto:**

* La regola di attestazione issuerid per AD FS manca in questa build. La regola di attestazione issuerid è obbligatoria se si esegue la federazione di più domini con Azure AD. Se si utilizza Azure AD Connect per gestire la distribuzione di AD FS in locale, l'aggiornamento a questa build rimuoverà la regola di attestazione issuerid esistente dalla configurazione di AD FS. È possibile risolvere il problema aggiungendo la regola dopo l'installazione o l'aggiornamento. Per altre informazioni sull'aggiunta della regola di attestazione issuerid, fare riferimento a questo articolo in [Supporto di più domini per la federazione con Azure AD](active-directory-aadconnect-multiple-domains.md).

**Problemi risolti:**

* In alcuni casi, l'installazione di Azure AD Connect, ha esito negativo perché non è possibile creare un account di servizio locale la cui password soddisfi il livello di complessità specificato dai criteri di password aziendale.
* È stato risolto un problema in cui le regole di unione non vengono rivalutate quando un oggetto nello spazio connettore viene contemporaneamente esce dall'ambito di una regola di unione ed nell'ambito di un'altra. Questa situazione può verificarsi se sono presenti due o più regole di unione, le cui condizioni di unione si escludono a vicenda.
* È stato risolto un problema in cui le regole di sincronizzazione in ingresso (di Azure AD), che non contengono regole di unione, non vengono elaborate se dispongono di valori di precedenza inferiori rispetto a quelle contenenti le regole di unione.

**Miglioramenti:**

* Aggiunta del supporto per l'installazione di Azure AD Connect in Windows Server 2016 Standard o versione successiva.
* Aggiunta del supporto per l'uso di SQL Server 2016 come database remoto per Azure AD Connect.

## <a name="112810"></a>1.1.281.0
Data di rilascio: agosto 2016

**Problemi risolti:**

* Le modifiche apportate all'intervallo di sincronizzazione non vengono applicate fino al termine del ciclo di sincronizzazione successivo.
* La procedura guidata di Azure AD Connect non accetta un account di Azure AD il cui nome utente inizi con un carattere di sottolineatura (\_).
* La procedura guidata di Azure AD Connect non riesce ad autenticare l'account Azure AD specificato se la password dell'account contiene troppi caratteri speciali. Viene visualizzato il messaggio di errore "La convalida delle credenziali non è riuscita. Si è verificato un errore imprevisto".
* La disinstallazione del server di gestione temporanea disabilita la sincronizzazione delle password nel tenant Azure AD e rende impossibile la sincronizzazione delle password con il server attivo.
* In casi rari, la sincronizzazione delle password ha esito negativo quando non sono archiviati hash password per l'utente.
* Quando il server Azure AD Connect è abilitato per la modalità di gestione temporanea, il writeback delle password non viene temporaneamente disabilitato.
* La procedura guidata di Azure AD Connect non visualizza l'effettiva configurazione della sincronizzazione delle password e del writeback delle password quando il server è in modalità di gestione temporanea. Indica sempre le due funzionalità come disabilitate.
* Le modifiche apportate alla configurazione della sincronizzazione delle password e del writeback delle password non vengono salvate in modo permanente dalla procedura guidata di Azure AD Connect quando il server è in modalità di gestione temporanea.

**Miglioramenti:**

* Aggiornamento del cmdlet Start-ADSyncSyncCycle per indicare se può avviare o meno un nuovo ciclo di sincronizzazione.
* Aggiunta del cmdlet Stop-ADSyncSyncCycle per terminare il ciclo di sincronizzazione e l'operazione in corso.
* Aggiornamento del cmdlet Stop-ADSyncScheduler per terminare il ciclo di sincronizzazione e l'operazione in corso.
* Quando si configura [Estensioni della directory](active-directory-aadconnectsync-feature-directory-extensions.md) nella procedura guidata di Azure AD Connect, è ora possibile selezionare l'attributo Azure AD di tipo "Teletex string".

## <a name="111890"></a>1.1.189.0
Data di rilascio: giugno 2016

**Problemi risolti e miglioramenti:**

* Ora Azure AD Connect può essere installato su un server conforme a FIPS.
  * Per la sincronizzazione della password, vedere [Sincronizzazione password e FIPS](active-directory-aadconnectsync-implement-password-synchronization.md#password-synchronization-and-fips).
* Risolto un problema in cui un nome NetBIOS non poteva essere risolto nel FQDN in Active Directory Connector.

## <a name="111800"></a>1.1.180.0
Data di rilascio: maggio 2016

**Nuove funzionalità:**

* Visualizza avvisi e indicazioni riguardo alla verifica dei domini se la verifica non è stata effettuata prima di eseguire Azure AD Connect.
* Aggiunto il supporto per [Microsoft Cloud Germany](active-directory-aadconnect-instances.md#microsoft-cloud-germany).
* Aggiunto il supporto per la versione più recente dell'infrastruttura [cloud di Microsoft Azure per enti pubblici](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud) con nuovi requisiti per gli URL.

**Problemi risolti e miglioramenti:**

* Aggiunti i filtri all'editor delle regole di sincronizzazione per semplificare il reperimento delle regole.
* Miglioramento delle prestazioni quando si elimina uno spazio connettore.
* Risolto un problema che si verificava quando lo stesso oggetto veniva sia eliminato che aggiunto nella stessa esecuzione (elimina/aggiungi).
* Una regola di sincronizzazione disabilitata ora non riattiva più gli oggetti e gli attributi inclusi all'aggiornamento del sistema o dello schema della directory.

## <a name="111300"></a>1.1.130.0
Data di rilascio: aprile 2016

**Nuove funzionalità:**

* Aggiunto il supporto per gli attributi multivalore alle [estensioni della directory](active-directory-aadconnectsync-feature-directory-extensions.md).
* Aggiunto il supporto per altre varianti di configurazione dell' [aggiornamento automatico](active-directory-aadconnect-feature-automatic-upgrade.md) da considerare idonee per l'aggiornamento.
* Aggiunti alcuni cmdlet per l' [utilità di pianificazione personalizzata](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Data di rilascio: marzo 2016

**Problemi risolti:**

* È stato verificato che non sia possibile usare l'installazione rapida in Windows Server 2008 (pre-R2), poiché la sincronizzazione delle password non è supportata in questo sistema operativo.
* L'aggiornamento da DirSync con una configurazione personalizzata del filtro non funzionava come previsto.
* Durante l'aggiornamento a una versione più recente, senza modifiche alla configurazione, è consigliabile non pianificare una importazione/sincronizzazione completa.

## <a name="111100"></a>1.1.110.0
Data di rilascio: febbraio 2016

**Problemi risolti:**

* L'aggiornamento da versioni precedenti non funziona se l'installazione non viene eseguita nella cartella predefinita C:\Programmi.
* Se si esegue l'installazione e si deseleziona **Avvia il processo di sincronizzazione** al termine dell'Installazione guidata, una nuova esecuzione dell'Installazione guidata non abiliterà l'utilità di pianificazione.
* L'utilità di pianificazione non funzionerà come previsto nei server in cui il formato di data/ora non è US-en. `Get-ADSyncScheduler` non potrà restituire gli orari corretti.
* Se è stata installata una versione precedente di Azure AD Connect con AD FS come opzione di accesso e aggiornamento, non è possibile eseguire nuovamente l'installazione guidata.

## <a name="111050"></a>1.1.105.0
Data di rilascio: febbraio 2016

**Nuove funzionalità:**

* [Automatic upgrade](active-directory-aadconnect-feature-automatic-upgrade.md) per i clienti con impostazioni rapide.
* Supporto per l'amministratore globale mediante Azure Multi-Factor Authentication e Privileged Identity Management nell'installazione guidata.
  * Se si usa Multi-Factor Authentication, è necessario impostare il proxy in modo che consenta il traffico per https://secure.aadcdn.microsoftonline-p.com.
  * Per il corretto funzionamento di Multi-Factor Authentication, è necessario aggiungere https://secure.aadcdn.microsoftonline-p.com all'elenco di siti attendibili.
* Possibilità di modificare il metodo di accesso dell'utente dopo l'installazione iniziale.
* Possibilità di usare i [filtri basati su dominio e unità organizzativa](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) nell'Installazione guidata. Ciò consente anche la connessione a foreste in cui non sono disponibili tutti i domini.
* L'[Utilità di pianificazione](active-directory-aadconnectsync-feature-scheduler.md) è incorporata nel motore di sincronizzazione.

**Funzionalità passate dal livello di anteprima al livello di disponibilità generale:**

* [Writeback dei dispositivi](active-directory-aadconnect-feature-device-writeback.md).
* [Estensioni della directory](active-directory-aadconnectsync-feature-directory-extensions.md).

**Nuove funzionalità di anteprima:**

* Il nuovo intervallo predefinito per i cicli di sincronizzazione è pari a 30 minuti. In tutte le versioni precedenti è pari a tre ore. Aggiunge il supporto per la modifica del comportamento dell' [utilità di pianificazione](active-directory-aadconnectsync-feature-scheduler.md) .

**Problemi risolti:**

* La pagina di verifica dei domini DNS non riconosceva sempre i domini.
* Richiesta di credenziali di amministratore del dominio durante la configurazione di AD FS.
* Gli account AD locali non vengono riconosciuti dall'installazione guidata se si trovano in un dominio con un albero DNS diverso da quello del dominio radice.

## <a name="1091310"></a>1.0.9131.0
Data di rilascio: dicembre 2015

**Problemi risolti:**

* La sincronizzazione delle password potrebbe non funzionare quando si modificano le password in Active Directory Domain Services (AD DS), ma funziona quando si imposta la password.
* Quando si dispone di un server proxy, l'autenticazione ad Azure AD potrebbe non riuscire durante l'installazione o se viene annullato un aggiornamento nella pagina di configurazione.
* L'aggiornamento da una versione precedente di Azure AD Connect con un'istanza completa di SQL Server avrà esito negativo se non si è SA (amministratore di sistema) in SQL Server.
* L'aggiornamento da una versione precedente di Azure AD Connect con una versione remota di SQL Server mostrerà il messaggio di errore "Impossibile accedere al database SQL di ADSync".

## <a name="1091250"></a>1.0.9125.0
Data di rilascio: novembre 2015

**Nuove funzionalità:**

* È possibile riconfigurare AD FS a trust AD Azure.
* È possibile aggiornare lo schema di Active Directory e rigenerare le regole di sincronizzazione.
* È possibile disattivare una regola di sincronizzazione.
* È possibile definire "AuthoritativeNull" come un nuovo valore letterale in una regola di sincronizzazione.

**Nuove funzionalità di anteprima:**

* [Azure AD Connect Health per la sincronizzazione](../connect-health/active-directory-aadconnect-health-sync.md).
* Supporto per la sincronizzazione della password dei [Servizi di dominio di Azure AD](../active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)

**Nuovo scenario supportato:**

* Supporta più organizzazioni di Exchange locali Per altre informazioni, vedere [Distribuzioni ibride con più foreste di Active Directory](https://technet.microsoft.com/library/jj873754.aspx).

**Problemi risolti:**

* Problemi sulla sincronizzazione delle password:
  * Un oggetto spostato dal all’esterno all’interno dell’ambito non avrà la password sincronizzata. Questo include unità Organizzativa e filtro attributi.
  * Selezione di una nuova unità Organizzativa da includere nella sincronizzazione non richiede una sincronizzazione completa della password.
  * Quando un utente disabilitato è abilitato la password non viene sincronizzata.
  * La coda di tentativi di password è infinita e il limite precedente di 5.000 oggetti da ritirare è stato rimosso.
  * [Risoluzione dei problemi migliorata](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshooting-password-synchronization).
* Impossibile connettersi ad Active Directory con livello di funzionalità foresta Windows Server 2016.
* Impossibile modificare il gruppo usato per il filtro di gruppo dopo l'installazione iniziale.
* Non si creerà un nuovo profilo utente nel server di Azure AD Connect per ogni utente che effettua una modifica della password con attivato il writeback delle password.
* Non è possibile usare valori Intero lungo negli ambiti di regole di sincronizzazione.
* La casella di controllo "writeback dispositivi" rimane disabilitata se sono presenti controller di dominio non raggiungibili.

## <a name="1086670"></a>1.0.8667.0
Data di rilascio: agosto 2015

**Nuove funzionalità:**

* L'installazione guidata di Azure AD Connect è ora localizzata in tutte le lingue in cui è disponibile Windows Server.
* È stato aggiunto il supporto per lo sblocco dell'account quando si usa la gestione delle password di Azure AD.

**Problemi risolti:**

* Si verifica un arresto anomalo dell'installazione guidata di Azure AD Connect se l'installazione viene continuata da un utente diverso da quello che l'ha avviata.
* Se una disinstallazione precedente di Azure AD Connect non riesce a disinstallare completamente il servizio di sincronizzazione Azure AD Connect, non sarà possibile effettuare la reinstallazione.
* Non è possibile installare Azure AD Connect usando l'installazione rapida se l'utente non si trova nel dominio radice della foresta o se viene usata una versione di Active Directory in una lingua diversa dall'inglese.
* Se il nome di dominio completo dell'account utente di Active Directory non può essere risolto, verrà visualizzato un messaggio di errore fuorviante analogo a "Commit dello schema non riuscito".
* Se l'account usato in Active Directory Connector viene modificato all'esterno della procedura guidata, le esecuzioni successive della procedura guidata avranno esito negativo.
* A volte non è possibile installare Azure AD Connect in un controller di dominio.
* Non è possibile abilitare e disabilitare la "Modalità di gestione temporanea" se sono stati aggiunti attributi di estensione.
* Il writeback delle password ha esito negativo in alcune configurazioni a causa di una password non valida in Active Directory Connector.
* Non è possibile aggiornare DirSync se si usa un nome distinto (DN) nel filtro di attributi.
* Utilizzo eccessivo della CPU quando si utilizza la reimpostazione della password.

**Funzionalità di anteprima rimosse:**

* La funzionalità di anteprima [Utente writeback](active-directory-aadconnect-feature-preview.md#user-writeback) è stata temporaneamente rimossa in base ai suggerimenti espressi dai clienti dell’anteprima. Verrà aggiunto di nuovo in un secondo momento, una volta valutato il feedback fornito.

## <a name="1086410"></a>1.0.8641.0
Data di rilascio: giugno 2015

**Rilascio iniziale di Azure AD Connect.**

Il nome è stato modificato da Azure AD Sync ad Azure AD Connect.

**Nuove funzionalità:**

* Installazione mediante le [impostazioni rapide](active-directory-aadconnect-get-started-express.md)
* È possibile [configurare la farm AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
* È possibile [aggiornare da DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md)
* [Impedire eliminazioni accidentali](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
* Introdotta la [modalità di gestione temporanea](active-directory-aadconnectsync-operations.md#staging-mode)

**Nuove funzionalità di anteprima:**

* [Writeback degli utenti](active-directory-aadconnect-feature-preview.md#user-writeback)
* [Writeback dei gruppi](active-directory-aadconnect-feature-preview.md#group-writeback)
* [Writeback dei dispositivi](active-directory-aadconnect-feature-device-writeback.md)
* [Estensioni della directory](active-directory-aadconnect-feature-preview.md)

## <a name="104940501"></a>1.0.494.0501
Data di rilascio: maggio 2015

**Nuovo requisito:**

* Azure AD Sync richiede ora .NET Framework versione 4.5.1 per l'installazione.

**Problemi risolti:**

* Il writeback delle password da Azure AD ha esito negativo con un errore di connettività del bus di servizio.

## <a name="104910413"></a>1.0.491.0413
Data di rilascio: aprile 2015

**Problemi risolti e miglioramenti:**

* Active Directory Connector non elabora correttamente le eliminazioni se il Cestino è abilitato e sono presenti più domini nella foresta.
* Le prestazioni delle operazioni di importazione sono state migliorate per Azure Active Directory Connector.
* Quando un gruppo supera il limite per le appartenenze (per impostazione predefinita, il limite è impostato su 50.000 oggetti), viene eliminato in Azure Active Directory. Con il nuovo comportamento, il gruppo non viene eliminato, viene generato un errore e le nuove modifiche alle appartenenze non vengono esportate.
* Non è possibile effettuare il provisioning di un nuovo oggetto se nello spazio del connettore è già presente un'eliminazione a fasi con lo stesso nome di dominio.
* Alcuni oggetti sono contrassegnati per la sincronizzazione durante una sincronizzazione Delta, anche se non sono previste modifiche a fasi sull'oggetto.
* La forzatura della sincronizzazione delle password rimuove anche l'elenco dei controller di dominio preferiti.
* Si sono verificati problemi di CSExportAnalyzer con alcuni stati degli oggetti.

**Nuove funzionalità:**

* Un join può ora connettersi al tipo di oggetto "ANY" nel metaverse.

## <a name="104850222"></a>1.0.485.0222
Data di rilascio: febbraio 2015

**Miglioramenti:**

* Prestazioni migliorate per l'importazione.

**Problemi risolti:**

* La sincronizzazione delle password rispetta l'attributo cloudFiltered usato dal filtro di attributi. Gli oggetti filtrati non sono più inclusi nell'ambito per la sincronizzazione delle password.
* Nelle rare situazioni in cui la topologia include un numero elevato di controller di dominio, la sincronizzazione delle password non funziona.
* Errore di tipo "server arrestato" durante l'importazione da Azure AD Connector dopo l'abilitazione della gestione dei dispositivi in Azure AD/Intune.
* L'aggiunta di entità di protezione esterne da più domini nella stessa foresta provoca un errore di join ambiguo.

## <a name="104751202"></a>1.0.475.1202
Data di rilascio: dicembre 2014

**Nuove funzionalità:**

* È ora supportata l'esecuzione della sincronizzazione delle password con filtri basati sugli attributi. Per informazioni dettagliate, vedere [Sincronizzazione delle password con i filtri](active-directory-aadconnectsync-configure-filtering.md).
* L'attributo msDS-ExternalDirectoryObjectID viene scritto di nuovo in Active Directory. Questa funzionalità aggiunge il supporto per applicazioni di Office 365. Usa OAuth2 per accedere alle cassette postali online e locali in una distribuzione ibrida di Exchange.

**Problemi di aggiornamento risolti:**

* Nel server è disponibile una versione più recente dell'Assistente per l'accesso.
* Per l'installazione di Azure AD Sync è stato usato un percorso di installazione personalizzato.
* Un criterio di join personalizzato non valido blocca l'aggiornamento.

**Altre correzioni:**

* I problemi dei modelli per Office Pro Plus sono stati risolti.
* I problemi di installazione provocati dai nomi utente che iniziano con un trattino sono stati risolti.
* Il problema relativo alla perdita dell'impostazione sourceAnchor durante la seconda esecuzione dell'Installazione guidata è stato risolto.
* Il problema relativo alla traccia ETW per la sincronizzazione delle password è stato risolto.

## <a name="104701023"></a>1.0.470.1023
Data di rilascio: ottobre 2014

**Nuove funzionalità:**

* Sincronizzazione delle password da più istanze di Active Directory locali ad Azure AD.
* Interfaccia utente di installazione localizzata per tutte le lingue in cui è disponibile Windows Server.

**Aggiornamento da AADSync 1.0 GA**

Se è già stato installato Azure AD Sync, è necessario eseguire un'altra operazione nel caso in cui siano state modificate le regole di sincronizzazione predefinite. In seguito all'aggiornamento alla versione 1.0.470.1023, le regole di sincronizzazione modificate vengono duplicate. Per ogni regola di sincronizzazione modificata eseguire le operazioni seguenti:

1.  Trovare la regola di sincronizzazione modificata e prendere nota delle modifiche.
* Eliminare la regola di sincronizzazione.
* Trovare la nuova regola di sincronizzazione creata da Azure AD Sync e riapplicare le modifiche.

**Autorizzazioni per l'account Active Directory**

Per poter leggere gli hash delle password da Active Directory, è necessario concedere autorizzazioni aggiuntive all'account di Active Directory. Le autorizzazioni da concedere sono "Replica modifiche directory" e "Replica di tutte le modifiche directory". Per poter leggere gli hash delle password, sono necessarie entrambe le autorizzazioni.

## <a name="104190911"></a>1.0.419.0911
Data di rilascio: settembre 2014

**Rilascio iniziale di Azure AD Sync.**

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

