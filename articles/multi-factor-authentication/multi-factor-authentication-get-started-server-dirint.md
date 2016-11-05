---
title: Integrazione di directory tra il server Azure Multi-Factor Authentication e Active Directory
description: Pagina relativa ad Azure Multi-Factor Authentication che illustra come eseguire l'integrazione tra il server Azure Multi-Factor Authentication e Active Directory, in modo da consentire la sincronizzazione delle directory.
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: curtand

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/04/2016
ms.author: kgremban

---
# Integrazione di directory tra il server Azure MFA e Active Directory
La sezione Integrazione directory permette di configurare il server per l'integrazione con Active Directory o un'altra directory LDAP, oltre a configurare attributi corrispondenti allo schema di directory e impostare la sincronizzazione automatica degli utenti.

## Impostazioni
Per impostazione predefinita, il server Azure Multi-Factor Authentication è configurato per importare o sincronizzare utenti da Active Directory. La scheda permette di eseguire l'override del comportamento predefinito e di eseguire il binding a una directory LDAP diversa, a una directory ADAM o a un controller di dominio di Active Directory specifico. Permette anche di usare l'autenticazione LDAP per il proxy LDAP o per binding LDAP come destinazione RADIUS, la preautenticazione per l'autenticazione IIS oppure l'autenticazione primaria per il portale utenti. La tabella seguente illustra le singole impostazioni.

![Impostazioni](./media/multi-factor-authentication-get-started-server-dirint/dirint.png)

| Funzionalità | Descrizione |
| --- | --- |
| Usa Active Directory |Selezionare l'opzione Usa Active Directory per usare Active Directory per l'importazione e la sincronizzazione. Questa è l'impostazione predefinita. <br>Nota: per un corretto funzionamento dell'integrazione con Active Directory, il computer deve appartenere a un dominio e occorre accedere con un account di dominio. |
| Includi domini trusted |Selezionare la casella di controllo Includi domini trusted per fare in modo che l'agente tenti di connettersi ai domini ritenuti attendibili dal dominio corrente, a un altro dominio nella foresta o a domini coinvolti in un trust tra foreste. Se non si importano o sincronizzano utenti da domini trusted, deselezionare la casella di controllo per migliorare le prestazioni. La casella di controllo è selezionata per impostazione predefinita. |
| Usa configurazione LDAP specifica |Selezionare l'opzione Usa configurazione LDAP specifica per usare le impostazioni LDAP specificate per l'importazione e la sincronizzazione. Nota: quando l'opzione Usa configurazione LDAP specifica è selezionata, l'interfaccia utente modifica i riferimenti da Active Directory a LDAP. |
| Pulsante Modifica |Il pulsante Modifica permette la modifica delle impostazioni di configurazione correnti per LDAP. |
| Usa query con ambito di attributi |Indica se è necessario usare le query con ambito di attributi. Le query con ambito di attributi permettono ricerche efficienti nella directory, qualificando i record in base alle voci disponibili nell'attributo di un altro record. Il server Azure Multi-Factor Authentication usa le query con ambito di attributi per eseguire query efficienti relative agli utenti appartenenti a un gruppo di sicurezza. <br>Nota: in alcuni casi le query con ambito di attributi sono supportate ma non è consigliabile usarle. Ad esempio, è possibile che in Active Directory si verifichino problemi con le query con ambito di attributi quando un gruppo di sicurezza include membri appartenenti a più di un dominio. In questo caso è consigliabile deselezionare la casella di controllo. |

La tabella seguente illustra le impostazioni di configurazione per LDAP.

| Funzionalità | Descrizione |
| --- | --- |
| Server |Immettere il nome host o indirizzo IP del server che esegue la directory LDAP. È anche possibile specificare un server di backup separato da un punto e virgola. <br>Nota: se per Tipo di binding è selezionato SSL, in genere è richiesto un nome host completo. |
| Nome distinto di base |Immettere il nome distinto dell'oggetto directory di base da cui verranno avviato tutte le query di directory. Ad esempio, dc=abc,dc=com. |
| Tipo di binding - Query |Selezionare il tipo di binding appropriato da usare quando si associa a cercare la directory LDAP. Il tipo selezionato viene usato per le importazioni, la sincronizzazione e la risoluzione del nome utente. <br><br> Anonimo: consente di eseguire un binding anonimo. Nome distinto di binding e Password di binding non verrà usato. Questa impostazione funziona solo se la directory LDAP consente il binding anonimo e le autorizzazioni consentono l'esecuzione di query degli attributi e dei record appropriati. <br><br> Semplice: consente di passare i valori di Nome distinto di binding e Password di binding come testo normale per eseguire il binding alla directory LDAP. Questo deve essere usato solo a scopo di test per verificare che il server sia raggiungibile e che l'account di binding disponga dell'accesso appropriato. Dopo l'installazione del certificato appropriato si consiglia di usare SSL. <br><br> SSL: consente di crittografare tramite SSL i valori di Nome distinto di binding e Password di binding per eseguire il binding alla directory LDAP. È necessario che sia installato in locale un certificato considerato attendibile dalla directory LDAP. <br><br> Windows: consente di usare Nome utente di binding e Password di binding per effettuare la connessione sicura a un controller di dominio di Active Directory o a una directory ADAM. Se il valore Nome utente di binding viene lasciato vuoto, per il binding viene usato l'account dell'utente connesso. |
| Tipo di binding - Autenticazioni |Selezionare il tipo di binding appropriato da usare quando si esegue l'autenticazione di binding LDAP. Vedere le descrizioni del tipo di binding in Tipo di binding - Query. In questo modo, ad esempio, è consentito l'uso di un binding anonimo per le query, mentre il binding SSL viene usato per proteggere le autenticazioni di binding LDAP. |
| Nome distinto di binding o nome utente di binding |Immettere il nome distinto del record utente per l'account da usare quando si esegue il binding alla directory LDAP.<br><br>Il nome distinto di binding viene usato solo quando il tipo di binding è Semplice o SSL. <br><br>Immettere il nome utente dell'account Windows da usare quando si esegue il binding alla directory LDAP con il tipo di binding Windows. Se il valore viene lasciato vuoto, per il binding viene usato l'account dell'utente connesso. |
| Password di binding |Immettere la password di binding per il nome distinto di binding o nome utente usato per il binding alla directory LDAP. Per configurare la password per il servizio AdSync di multi-Factor Authentication Server, deve essere abilitata la sincronizzazione e il servizio deve essere in esecuzione sul computer locale. La password viene salvata in Gestione nomi utente e password archiviati di Windows con l'account usato dal servizio di sincronizzazione di Active Directory del server Multi-Factor Authentication. La password viene salvata anche con l'account usato dall'interfaccia utente del server Multi-Factor Authentication e con quello usato dal servizio del server Multi-Factor Authentication. <br><br>Nota: poiché la password viene archiviata solo in Gestione nomi utente e password archiviati di Windows del server locale, è necessario eseguire questo passaggio in ogni server Multi-Factor Authentication che richiede l'accesso alla password. |
| Limite dimensione query |Specificare le dimensioni massime per il numero massimo di utenti che restituirà una ricerca di directory. Questo limite deve corrispondere alla configurazione nella directory LDAP. Per le ricerche estese dove non è supportato il paging, importazione e la sincronizzazione tenterà di recuperare gli utenti in batch. Se il limite di dimensioni specificato è superiore al limite configurato nella directory LDAP, alcuni utenti non vengano trovati. |
| Pulsante Test |Fare clic sul pulsante Test per verificare il binding al server LDAP. <br><br> Nota: per verificare il binding non è necessario che sia selezionata l'opzione per l'uso della configurazione LDAP In questo modo, infatti, è possibile verificare il binding prima di usare la configurazione LDAP. |

## Filtri
I filtri permettono di configurare criteri per qualificare i record durante l'esecuzione di una ricerca di directory. La configurazione del filtro permette di definire l'ambito degli oggetti da sincronizzare.

![Filtri](./media/multi-factor-authentication-get-started-server-dirint/dirint2.png)

Azure Multi-Factor Authentication offre le tre opzioni seguenti.

* **Filtro contenitore**: specificare i criteri di filtro usati per qualificare record contenitori quando si esegue una ricerca di directory. Per Active Directory e ADAM, viene in genere usato il filtro (|(objectClass=organizationalUnit)(objectClass=container)). Per le altre directory LDAP, è opportuno usare i criteri di filtro che qualificano ogni tipo di oggetto contenitore a seconda dello schema di directory. <br>Nota: se questo campo viene lasciato vuoto, per impostazione predefinita viene usato ((objectClass=organizationalUnit)(objectClass=container)).
* **Filtro gruppo di sicurezza**: specificare i criteri di filtro usati per qualificare record gruppi di sicurezza quando si esegue una ricerca di directory. Per Active Directory e ADAM, viene in genere usato il filtro (&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648)). Per le altre directory LDAP, è opportuno usare i criteri di filtro che qualificano ogni tipo di oggetto gruppo di sicurezza a seconda dello schema di directory. <br>Nota: se questo campo viene lasciato vuoto, per impostazione predefinita viene usato (&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648)).
* **Filtro utente**: specificare i criteri di filtro usati per qualificare record utenti quando si esegue una ricerca di directory. Per Active Directory e ADAM, viene in genere usato il filtro (&(objectClass=user)(objectCategory=person)). Per le altre directory LDAP, è opportuno usare (objectClass=inetOrgPerson) o un criterio simile, a seconda dello scherma di directory. <br>Nota: se questo campo viene lasciato vuoto, per impostazione predefinita viene usato (&(objectCategory=person)(objectClass=user)).

## Attributi
Se necessario, è possibile personalizzare gli attributi per una directory specifica. Ciò permette di aggiungere attributi personalizzati e perfezionare la sincronizzazione per limitarla solo agli attributi necessari. Il valore di ogni campo attributo deve corrispondere al nome dell'attributo definito nello schema di directory. Per altre informazioni, vedere la tabella seguente.

![Attributi](./media/multi-factor-authentication-get-started-server-dirint/dirint3.png)

| Funzionalità | Descrizione |
| --- | --- |
| Identificatore univoco |Immettere il nome dell'attributo da usare come identificatore univoco di record contenitori, gruppi di sicurezza e utenti. In Active Directory si usa in genere objectGUID. Nelle altre implementazioni di LDAP è possibile usare entryUUID o un attributo simile. Il valore predefinito è objectGUID. |
| ... Pulsanti (Seleziona attributo) |Accanto a ogni campo attributo è presente un pulsante "..." che consente di visualizzare la finestra di dialogo Seleziona attributo in cui è possibile selezionare un attributo da un elenco. <br><br>Finestra di dialogo Seleziona attributo.<br><br>Nota: gli attributi possono essere immessi manualmente e non devono corrispondere necessariamente a un attributo dell'elenco. |
| Tipo di identificatore univoco |Selezionare il tipo di attributo dell'identificatore univoco. In Active Directory l'attributo objectGUID è di tipo GUID. Nelle altre implementazioni di LDAP può essere di tipo Stringa o Matrice di byte ASCII. Il valore predefinito è GUID. <br><br>Nota: è importante impostare correttamente questo valore perché l'identificatore univoco fa riferimento agli elementi di sincronizzazione e il tipo di identificatore univoco viene usato per trovare direttamente l'oggetto nella directory. Se si imposta questo valore su Stringa ma nella directory il valore viene archiviato come matrice di byte di caratteri ASCII, la sincronizzazione non funziona correttamente. |
| Nome distinto |Immettere il nome dell'attributo che contiene il nome distinto per ogni record. In Active Directory si usa in genere distinguishedName. Nelle altre implementazioni di LDAP è possibile usare entryDN o un attributo simile. Il valore predefinito è distinguishedName. <br><br>Nota: se non esiste un attributo contenente solo il nome distinto, è possibile usare l'attributo adspath. La parte "LDAP://<server>/" del percorso viene automaticamente rimossa lasciando solo il nome distinto dell'oggetto. |
| Nome del contenitore |Immettere il nome dell'attributo che contiene il nome in un record contenitore. Il valore di questo attributo viene visualizzato nella Gerarchia contenitori durante l'importazione da Active Directory o l'aggiunta di elementi di sincronizzazione. Il valore predefinito è name. <br><br>Nota: se contenitori diversi usano attributi diversi per i relativi nomi, è possibile specificare più attributi di nome contenitore separati da un punto e virgola. Il primo attributo di nome contenitore trovato in un oggetto contenitore viene usato per visualizzarne il nome. |
| Nome gruppo di sicurezza |Immettere il nome dell'attributo che contiene il nome in un record gruppo di sicurezza. Il valore di questo attributo viene visualizzato nell'elenco Gruppo di sicurezza durante l'importazione da Active Directory o l'aggiunta di elementi di sincronizzazione. Il valore predefinito è name. |
| Utenti |Gli attributi seguenti vengono usati per cercare, visualizzare, importare e sincronizzare informazioni utente dalla directory. |
| Nome utente |Immettere il nome dell'attributo che contiene il nome utente in un record utente. Il valore di questo attributo viene usato come nome utente del server Multi-Factor Authentication. È possibile specificare anche un secondo attributo come backup del primo. Il secondo attributo viene usato solo se il primo non contiene un valore per l'utente. I valori predefiniti sono userPrincipalName e sAMAccountName. |
| Nome |Immettere il nome dell'attributo che contiene il nome in un record utente. Il valore predefinito è givenName. |
| Cognome |Immettere il nome dell'attributo che contiene il cognome in un record utente. Il valore predefinito è sn. |
| Indirizzo di posta elettronica |Immettere il nome dell'attributo che contiene l'indirizzo e-mail in un record utente. L'indirizzo e-mail viene usato per inviare all'utente messaggi di benvenuto e di aggiornamento. Il valore predefinito è mail. |
| Gruppo utenti |Immettere il nome dell'attributo che contiene il gruppo utenti in un record utente. Il gruppo utenti consente di filtrare utenti nell'agente e nei report del portale di gestione del server Multi-Factor Authentication. |
| Descrizione |Immettere il nome dell'attributo che contiene la descrizione in un record utente. La descrizione viene usata solo per le ricerche. Il valore predefinito è description. |
| Lingua chiamata vocale |Immettere il nome dell'attributo che contiene il nome breve della lingua da usare per le chiamate vocali all'utente. |
| Lingua SMS |Immettere il nome dell'attributo che contiene il nome breve della lingua da usare per i messaggi SMS inviati all'utente. |
| Lingua app telefono |Immettere il nome dell'attributo che contiene il nome breve della lingua da usare per i messaggi SMS inviati dall'app telefono all'utente. |
| Lingua token OATH |Immettere il nome dell'attributo che contiene il nome breve della lingua da usare per i messaggi SMS inviati dal token OATH all'utente. |
| Telefoni |Gli attributi seguenti vengono usati per importare o sincronizzare i numeri di telefono dell'utente. Se per un tipo di telefono si specifica un nome attributo, il tipo di telefono non sarà disponibile durante l'importazione da Active Directory o l'aggiunta di elementi di sincronizzazione. |
| Business |Immettere il nome dell'attributo che contiene il numero di telefono dell'ufficio in un record utente. Il valore predefinito è telephoneNumber. |
| Home |Immettere il nome dell'attributo che contiene il numero di telefono dell'abitazione in un record utente. Il valore predefinito è homePhone. |
| Cercapersone |Immettere il nome dell'attributo che contiene il cercapersone in un record utente. Il valore predefinito è pager. |
| Mobile |Immettere il nome dell'attributo che contiene il numero di telefono cellulare in un record utente. Il valore predefinito è mobile. |
| Fax |Immettere il nome dell'attributo che contiene il numero di fax in un record utente. Il valore predefinito è facsimileTelephoneNumber. |
| Telefono IP |Immettere il nome dell'attributo che contiene il numero di telefono IP in un record utente. Il valore predefinito è ipPhone. |
| Personalizzate |Immettere il nome dell'attributo che contiene un numero di telefono personalizzato in |
| un record utente. Il valore predefinito è blank. | |
| Estensione |Immettere il nome dell'attributo che contiene l'interno telefonico in un record utente. Il valore di questo campo viene usato come interno solo del numero di telefono principale. Il valore predefinito è blank. <br><br>Nota: se l'attributo Interno non è specificato, è possibile includere l'interno come parte integrante dell'attributo del telefono. L'interno deve essere preceduto da una "x" per poter essere analizzato. Ad esempio, 555-123-4567 x890 indica che 555-123-4567 è il numero di telefono e 890 l'interno. |
| Pulsante Ripristina impostazioni predefinite |Fare clic sul pulsante Ripristina impostazioni predefinite per ripristinare il valore predefinito di tutti gli attributi. Le impostazioni predefinite dovrebbero funzionare correttamente con lo schema normale di Active Directory o ADAM. |

Per modificare gli attributi, è sufficiente fare clic sul pulsante Modifica nella scheda Attributi. Verrà visualizzata una finestra che permette di modificare gli attributi.

![Modifica attributi](./media/multi-factor-authentication-get-started-server-dirint/dirint4.png)

## Sincronizzazione
La sincronizzazione consente di mantenere il database utenti di Azure Multi-Factor sincronizzato con gli utenti di Active Directory o di un'altra directory LDAP. Il processo è simile alla procedura di importazione manuale di utenti da Active Directory, con la differenza che periodicamente viene eseguito il polling delle modifiche apportate ai gruppi di sicurezza e agli utenti di Active Directory da elaborare. Consente inoltre di disabilitare o rimuovere gli utenti rimossi da un contenitore o un gruppo di sicurezza e di rimuovere gli utenti eliminati da Active Directory.

Il servizio di sincronizzazione di Active Directory del server Multi-Factor Authentication è un servizio di Windows che esegue il polling periodico di Active Directory. Non deve essere confuso con Azure AD Sync o Azure AD Connect. Il servizio di sincronizzazione di Active Directory del server Multi-Factor Authentication, benché basato su una codebase simile, è specifico per il server Azure Multi-Factor Authentication. Viene installato in stato di arresto e avviato dal servizio del server Multi-Factor Authentication nel momento in cui viene configurato per l'esecuzione. Se si ha una configurazione multiserver di Multi-Factor Authentication, tenere presente che il servizio di sincronizzazione di Active Directory del server Multi-Factor Authentication può essere eseguito solo su un unico server.

Il servizio di sincronizzazione di Active Directory del server Multi-Factor Authentication usa l'estensione del server LDAP DirSync fornita da Microsoft per eseguire il polling delle modifiche in modo efficiente. È necessario che il chiamante del controllo DirSync abbia il diritto "directory get changes" e il diritto esteso di controllo di accesso DS-Replication-Get-Changes. Per impostazione predefinita, questi diritti vengono assegnati agli account amministratore e LocalSystem nei controller di dominio e il servizio di sincronizzazione è configurato per l'esecuzione come LocalSystem. Di conseguenza, è più semplice eseguire il servizio in un controller di dominio. Il servizio può essere eseguito con un account con autorizzazioni minori se viene configurato per eseguire sempre una sincronizzazione completa. Si tratta di una procedura meno efficiente, ma che richiede meno privilegi per l'account.

Se è stato configurato per l'uso di LDAP e la directory LDAP supporta il controllo DirSync, il polling delle modifiche apportate a utenti e gruppi di sicurezza viene eseguito nello stesso modo in cui avviene in Active Directory. Se la directory LDAP non supporta il controllo DirSync, viene eseguita una sincronizzazione completa durante ogni ciclo.

![Sincronizzazione](./media/multi-factor-authentication-get-started-server-dirint/dirint5.png)

Per altre informazioni su ogni impostazione della scheda Sincronizzazione, vedere la tabella seguente.

| Funzionalità | Descrizione |
| --- | --- |
| Abilita sincronizzazione con Active Directory |Se questa opzione è selezionata, viene avviato il servizio del server Multi-Factor Authentication che esegue periodicamente il polling delle modifiche di Active Directory. <br><br>Nota: affinché il servizio del server Multi-Factor Authentication possa iniziare a elaborare le modifiche è necessario aggiungere almeno un elemento di sincronizzazione ed eseguire una sincronizzazione. |
| Sincronizza ogni |Specificare l'intervallo di attesa tra il polling e l'elaborazione delle modifiche da parte del servizio del server Multi-Factor Authentication. <br><br> Nota: l'intervallo specificato corrisponde al lasso di tempo che intercorre tra l'inizio di ogni ciclo. Se il tempo impiegato per l'elaborazione delle modifiche supera l'intervallo specificato, il servizio rieseguirà subito il polling. |
| Rimuovi utenti non più presenti in Active Directory |Se questa opzione è selezionata, il servizio del server Multi-Factor Authentication elabora gli oggetti contrassegnati per la rimozione dell'utente di Active Directory eliminato e rimuove l'utente del server Multi-Factor Authentication correlato. |
| Esegui sempre una sincronizzazione completa |Se questa opzione è selezionata, il servizio del server Multi-Factor Authentication esegue sempre una sincronizzazione completa. Se invece è deselezionata, il servizio del server Multi-Factor Authentication effettua una sincronizzazione incrementale eseguendo query solo sugli utenti modificati. Per impostazione predefinita, questa opzione è deselezionata. <br><br> Nota: se l'opzione è deselezionata, è possibile eseguire una sincronizzazione incrementale solo se la directory supporta il controllo DirSync e l'account usato per il binding alla directory dispone delle autorizzazioni appropriate per eseguire query incrementali su DirSync. Se l'account non ha le autorizzazioni appropriate o se nella sincronizzazione sono coinvolti più domini, è consigliabile eseguire una sincronizzazione completa. |
| Richiedi l'approvazione dell'amministratore per la disabilitazione o la rimozione di più di X utenti |È possibile configurare gli elementi di sincronizzazione per disabilitare o rimuovere gli utenti che non sono più membri del contenitore o del gruppo di sicurezza dell'elemento. Come misura di sicurezza, è possibile anche richiedere l'approvazione dell'amministratore quando il numero di utenti da disabilitare o rimuovere supera una determinata soglia. Se questa opzione è selezionata, viene richiesta l'approvazione per la soglia specificata. Il valore predefinito è 5 e l'intervallo valido è compreso tra 1 e 999. <br><br> L'approvazione viene facilitata inviando prima una notifica e-mail agli amministratori. La notifica tramite posta elettronica fornisce istruzioni per verificare e approvare la disabilitazione e la rimozione degli utenti. L'approvazione viene richiesta all'avvio dell'interfaccia utente del server Multi-Factor Authentication. |

Il pulsante **Sincronizza** consente di eseguire una sincronizzazione completa per gli elementi di sincronizzazione specificati. È necessario eseguire una sincronizzazione completa ogni volta che vengono aggiunti, modificati, rimossi o riordinati elementi di sincronizzazione. È inoltre necessaria prima di attivare il servizio di sincronizzazione di Active Directory del servizio Multi-Factor Authentication, poiché consente di impostare il punto di partenza da cui il servizio eseguirà il polling delle modifiche incrementali. Se sono state apportate modifiche agli elementi di sincronizzazione e non è stata eseguita una sincronizzazione completa, quando si passa a un'altra sezione o si chiude l'interfaccia utente viene chiesto di eseguire una sincronizzazione.

Il pulsante **Rimuovi** consente all'amministratore di eliminare uno o più elementi di sincronizzazione dall'elenco degli elementi di sincronizzazione del server Multi-Factor Authentication.

> [!WARNING]
> Dopo la rimozione, un record di elemento di sincronizzazione non può più essere recuperato. Se è stato eliminato per errore, è necessario aggiungerlo di nuovo.
> 
> 

L'elemento o gli elementi di sincronizzazione rimossi dal server Multi-Factor Authentication non vengono più elaborati dal server.

I pulsanti Sposta su e Sposta giù consentono all'amministratore di modificare l'ordine degli elementi di sincronizzazione. L'ordine è importante poiché uno stesso utente può essere membro di più elementi di sincronizzazione, ad esempio di un contenitore e di un gruppo di sicurezza. Le impostazioni applicate all'utente durante la sincronizzazione derivano dal primo elemento di sincronizzazione presente nell'elenco a cui è associato l'utente. È quindi consigliabile inserire gli elementi di sincronizzazione in ordine di priorità.

> [!TIP]
> Dopo aver rimosso elementi di sincronizzazione è opportuno eseguire una sincronizzazione completa. Dopo aver ordinato gli elementi di sincronizzazione è opportuno eseguire una sincronizzazione completa. Fare clic sul pulsante Sincronizza per eseguire una sincronizzazione completa.
> 
> 

## Server Multi-Factor Authentication
È possibile impostare server Multi-Factor Authentication aggiuntivi da usare come proxy RADIUS di backup, proxy LDAP o per l'autenticazione IIS. La configurazione di sincronizzazione viene condivisa tra tutti gli agenti, ma solo uno di essi può eseguire il servizio del server Multi-Factor Authentication. Questa scheda consente di selezionare il server Multi-Factor Authentication da abilitare per la sincronizzazione.

![Server Multi-Factor Authentication](./media/multi-factor-authentication-get-started-server-dirint/dirint6.png)

<!---HONumber=AcomDC_0921_2016-->