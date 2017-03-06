---
title: Integrazione di directory tra il server Azure Multi-Factor Authentication e Active Directory
description: Pagina relativa ad Azure Multi-Factor Authentication che illustra come eseguire l&quot;integrazione tra il server Azure Multi-Factor Authentication e Active Directory, in modo da consentire la sincronizzazione delle directory.
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: def7a534-cfb2-492a-9124-87fb1148ab1f
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/16/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 999361daa2faebe3e88cab0b6085a938d6f40e9d
ms.openlocfilehash: 07b14394b0e07fd78d70d77aeef8a2feb17284e0
ms.lasthandoff: 02/17/2017


---
# <a name="directory-integration-between-azure-mfa-server-and-active-directory"></a>Integrazione di directory tra il server Azure MFA e Active Directory
Usare la sezione Integrazione directory del server Azure MFA per l'integrazione con Active Directory o un'altra directory LDAP. È possibile configurare attributi corrispondenti allo schema di directory e impostare la sincronizzazione automatica degli utenti.

## <a name="settings"></a>Impostazioni
Per impostazione predefinita, il server Azure Multi-Factor Authentication (MFA) è configurato per importare o sincronizzare utenti da Active Directory.  La scheda Integrazione directory permette di eseguire l'override del comportamento predefinito e di eseguire il binding a una directory LDAP diversa, a una directory ADAM oppure a un controller di dominio di Active Directory specifico.  Permette anche di usare l'autenticazione LDAP per il proxy LDAP o per binding LDAP come destinazione RADIUS, la preautenticazione per l'autenticazione IIS oppure l'autenticazione primaria per il portale utenti.  La tabella seguente illustra le singole impostazioni.

![Impostazioni](./media/multi-factor-authentication-get-started-server-dirint/dirint.png)

| Funzionalità | Descrizione |
| --- | --- |
| Usa Active Directory |Selezionare l'opzione Usa Active Directory per usare Active Directory per l'importazione e la sincronizzazione.  Questa è l'impostazione predefinita. <br>Nota: per un corretto funzionamento dell'integrazione con Active Directory, è necessario aggiungere il computer a un dominio e accedere con un account di dominio. |
| Includi domini trusted |Selezionare **Includi domini attendibili** per fare in modo che l'agente provi a connettersi ai domini ritenuti attendibili dal dominio corrente, a un altro dominio nella foresta o a domini coinvolti in un trust tra foreste.  Se non si importano o sincronizzano utenti da domini trusted, deselezionare la casella di controllo per migliorare le prestazioni.  La casella di controllo è selezionata per impostazione predefinita. |
| Usa configurazione LDAP specifica |Selezionare l'opzione Usa configurazione LDAP specifica per usare le impostazioni LDAP specificate per l'importazione e la sincronizzazione. Nota: quando l'opzione Usa configurazione LDAP specifica è selezionata, l'interfaccia utente modifica i riferimenti da Active Directory a LDAP. |
| Pulsante Modifica |Il pulsante Modifica permette la modifica delle impostazioni di configurazione correnti per LDAP. |
| Usa query con ambito di attributi |Indica se è necessario usare le query con ambito di attributi.  Le query con ambito di attributi permettono ricerche efficienti nella directory, qualificando i record in base alle voci disponibili nell'attributo di un altro record.  Il server Azure Multi-Factor Authentication usa le query con ambito di attributi per eseguire query efficienti relative agli utenti appartenenti a un gruppo di sicurezza.   <br>Nota: in alcuni casi le query con ambito di attributi sono supportate ma non è consigliabile usarle.  Ad esempio, è possibile che in Active Directory si verifichino problemi con le query con ambito di attributi quando un gruppo di sicurezza include membri appartenenti a più di un dominio. In questo caso deselezionare la casella di controllo. |

La tabella seguente illustra le impostazioni di configurazione per LDAP.

| Funzionalità | Descrizione |
| --- | --- |
| Server |Immettere il nome host o indirizzo IP del server che esegue la directory LDAP.  È anche possibile specificare un server di backup separato da un punto e virgola. <br>Nota: se per Tipo di binding è selezionato SSL, è necessario un nome host completo. |
| Nome distinto di base |Immettere il nome distinto dell'oggetto directory di base da cui vengono avviate tutte le query su directory.  Ad esempio, dc=abc,dc=com. |
| Tipo di binding - Query |Selezionare il tipo di binding appropriato da usare quando si associa a cercare la directory LDAP.  Il tipo selezionato viene usato per le importazioni, la sincronizzazione e la risoluzione del nome utente. <br><br>  Anonimo: consente di eseguire un binding anonimo.  Non vengono usati un nome distinto di binding e una password di binding.  Questa impostazione funziona solo se la directory LDAP consente il binding anonimo e le autorizzazioni consentono l'esecuzione di query sugli attributi e i record appropriati.  <br><br> Semplice: consente di passare il nome distinto di binding e la password di binding come testo normale per eseguire il binding alla directory LDAP.  Serve unicamente a scopo di test, per verificare che il server sia raggiungibile e che l'account di binding abbia l'accesso appropriato. Dopo aver installato il certificato appropriato, usare SSL.  <br><br> SSL: consente di crittografare il nome distinto di binding e la password di binding tramite SSL per eseguire il binding alla directory LDAP.  Viene installato in locale un certificato considerato attendibile dalla directory LDAP.  <br><br> Windows: consente di usare il nome utente di binding e la password di binding per eseguire la connessione sicura a un controller di dominio di Active Directory o a una directory ADAM.  Se il valore Nome utente di binding viene lasciato vuoto, per il binding viene usato l'account dell'utente connesso. |
| Tipo di binding - Autenticazioni |Selezionare il tipo di binding appropriato da usare quando si esegue l'autenticazione di binding LDAP.  Vedere le descrizioni del tipo di binding in Tipo di binding - Query.  In questo modo, ad esempio, è consentito l'uso di un binding anonimo per le query, mentre il binding SSL viene usato per proteggere le autenticazioni di binding LDAP. |
| Nome distinto di binding o nome utente di binding |Immettere il nome distinto del record utente per l'account da usare quando si esegue il binding alla directory LDAP.<br><br>Il nome distinto di binding viene usato solo quando il tipo di binding è Semplice o SSL.  <br><br>Immettere il nome utente dell'account Windows da usare quando si esegue il binding alla directory LDAP con il tipo di binding Windows.  Se il valore viene lasciato vuoto, per il binding viene usato l'account dell'utente connesso. |
| Password di binding |Immettere la password di binding per il nome distinto di binding o nome utente usato per il binding alla directory LDAP.  Per configurare la password per il servizio ADSync del server Multi-Factor Authentication, abilitare la sincronizzazione e assicurarsi che il servizio sia in esecuzione nel computer locale.  La password viene salvata in Gestione nomi utente e password archiviati di Windows nell'account usato dal servizio ADSync del server Multi-Factor Authentication.  La password viene salvata anche nell'account usato dall'interfaccia utente del server Multi-Factor Authentication e in quello usato dal servizio del server Multi-Factor Authentication.  <br><br>Dato che la password viene archiviata solo in Gestione nomi utente e password archiviati di Windows nel server locale, è necessario ripetere questo passaggio in ogni server Multi-Factor Authentication che richiede l'accesso alla password. |
| Limite dimensione query |Specificare le dimensioni massime per il numero massimo di utenti restituito da una ricerca nella directory.  Questo limite deve corrispondere alla configurazione nella directory LDAP.  Per le ricerche estese in cui non è supportato il paging, l'importazione e la sincronizzazione provano a recuperare gli utenti in batch.  Se il limite di dimensioni specificato è superiore al limite configurato nella directory LDAP, alcuni utenti non vengano trovati. |
| Pulsante Test |Fare clic su **Test** per verificare il binding al server LDAP.  <br><br>Non è necessario selezionare l'opzione **Usa configurazione LDAP specifica** per verificare il binding. In questo modo è possibile verificare il binding prima di usare la configurazione LDAP. |

## <a name="filters"></a>Filtri
I filtri permettono di configurare criteri per qualificare i record durante l'esecuzione di una ricerca di directory.  Il filtro permette di definire l'ambito degli oggetti da sincronizzare.  

![Filtri](./media/multi-factor-authentication-get-started-server-dirint/dirint2.png)

Azure Multi-Factor Authentication offre le tre opzioni di filtro seguenti:

* **Filtro contenitore** : specificare i criteri di filtro usati per qualificare record contenitori quando si esegue una ricerca di directory.  Per Active Directory e ADAM, in genere si usa (|(objectClass=organizationalUnit)(objectClass=container)).  Per altre directory LDAP, usare criteri di filtro che qualificano ogni tipo di oggetto contenitore, a seconda dello schema di directory.  <br>Nota: se viene lasciato vuoto, per impostazione predefinita viene usato ((objectClass=organizationalUnit)(objectClass=container)).
* **Filtro gruppo di sicurezza** : specificare i criteri di filtro usati per qualificare record gruppi di sicurezza quando si esegue una ricerca di directory.  Per Active Directory e ADAM, in genere si usa (&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648)).  Per altre directory LDAP, usare criteri di filtro che qualificano ogni tipo di oggetto gruppo di sicurezza, a seconda dello schema di directory.  <br>Nota: se viene lasciato vuoto, per impostazione predefinita viene usato (&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648)).
* **Filtro utente** : specificare i criteri di filtro usati per qualificare record utenti quando si esegue una ricerca di directory.  Per Active Directory e ADAM, in genere si usa (&(objectClass=user)(objectCategory=person)).  Per altre directory LDAP, usare (objectClass=inetOrgPerson) o un criterio simile, a seconda dello scherma di directory. <br>Nota: se viene lasciato vuoto, per impostazione predefinita viene usato (&(objectCategory=person)(objectClass=user)).

## <a name="attributes"></a>Attributi
È possibile personalizzare gli attributi per una directory specifica in base alle necessità.  Questo permette di aggiungere attributi personalizzati e perfezionare la sincronizzazione per limitarla solo agli attributi necessari. Usare il nome dell'attributo definito nello schema di directory per valore di ogni campo attributo. La tabella seguente contiene informazioni aggiuntive su ognuna delle funzionalità.

Gli attributi possono essere immessi manualmente e non devono corrispondere necessariamente a un attributo dell'elenco.

![Attributi](./media/multi-factor-authentication-get-started-server-dirint/dirint3.png)

| Funzionalità | Descrizione |
| --- | --- |
| Identificatore univoco |Immettere il nome dell'attributo da usare come identificatore univoco di record contenitori, gruppi di sicurezza e utenti.  In Active Directory si usa in genere objectGUID. In altre implementazioni LDAP è possibile usare entryUUID o un valore simile.  Il valore predefinito è objectGUID. |
| Tipo di identificatore univoco |Selezionare il tipo di attributo dell'identificatore univoco.  In Active Directory l'attributo objectGUID è di tipo GUID. In altre implementazioni LDAP è possibile usare il tipo Stringa o Matrice di byte ASCII.  Il valore predefinito è GUID. <br><br>È importante impostare correttamente questo tipo, perché il relativo identificatore univoco fa riferimento agli elementi di sincronizzazione. Il tipo identificatore univoco viene usato per trovare direttamente l'oggetto nella directory.  Se si imposta questo tipo su Stringa mentre nella directory il valore viene archiviato come matrice di byte di caratteri ASCII, la sincronizzazione non può funzionare correttamente. |
| Nome distinto |Immettere il nome dell'attributo che contiene il nome distinto per ogni record.  In Active Directory si usa in genere distinguishedName. In altre implementazioni LDAP è possibile usare entryDN o un valore simile.  Il valore predefinito è distinguishedName. <br><br>Se non esiste un attributo contenente solo il nome distinto, è possibile usare l'attributo adspath.  La parte "LDAP://\<server\>/" del percorso viene automaticamente rimossa lasciando solo il nome distinto dell'oggetto. |
| Nome del contenitore |Immettere il nome dell'attributo che contiene il nome in un record contenitore.  Il valore di questo attributo viene visualizzato nella Gerarchia contenitori durante l'importazione da Active Directory o l'aggiunta di elementi di sincronizzazione.  Il valore predefinito è name. <br><br>Se contenitori diversi usano attributi diversi per i relativi nomi, è possibile specificare più attributi del nome contenitore separati da un punto e virgola.  Il primo attributo del nome contenitore trovato in un oggetto contenitore viene usato per visualizzarne il nome. |
| Nome gruppo di sicurezza |Immettere il nome dell'attributo che contiene il nome in un record gruppo di sicurezza.  Il valore di questo attributo viene visualizzato nell'elenco Gruppo di sicurezza durante l'importazione da Active Directory o l'aggiunta di elementi di sincronizzazione.  Il valore predefinito è name. |
| Nome utente |Immettere il nome dell'attributo che contiene il nome utente in un record utente.  Il valore di questo attributo viene usato come nome utente del server Multi-Factor Authentication.  È possibile specificare anche un secondo attributo come backup del primo.  Il secondo attributo viene usato solo se il primo non contiene un valore per l'utente.  I valori predefiniti sono userPrincipalName e sAMAccountName. |
| Nome |Immettere il nome dell'attributo che contiene il nome in un record utente.  Il valore predefinito è givenName. |
| Cognome |Immettere il nome dell'attributo che contiene il cognome in un record utente.  Il valore predefinito è sn. |
| Indirizzo di posta elettronica |Immettere il nome dell'attributo che contiene l'indirizzo e-mail in un record utente.  L'indirizzo di posta elettronica viene usato per inviare all'utente messaggi di benvenuto e di aggiornamento.  Il valore predefinito è mail. |
| Gruppo utenti |Immettere il nome dell'attributo che contiene il gruppo utenti in un record utente.  Il gruppo utenti consente di filtrare utenti nell'agente e nei report del portale di gestione del server Multi-Factor Authentication. |
| Descrizione |Immettere il nome dell'attributo che contiene la descrizione in un record utente.  La descrizione viene usata solo per le ricerche.  Il valore predefinito è description. |
| Lingua telefonata |Immettere il nome dell'attributo che contiene il nome breve della lingua da usare per le chiamate vocali all'utente. |
| Lingua SMS |Immettere il nome dell'attributo che contiene il nome breve della lingua da usare per i messaggi SMS inviati all'utente. |
| Lingua app mobile |Immettere il nome dell'attributo che contiene il nome breve della lingua da usare per i messaggi SMS inviati dall'app telefono all'utente. |
| Lingua token OATH |Immettere il nome dell'attributo che contiene il nome breve della lingua da usare per i messaggi SMS inviati dal token OATH all'utente. |
| Telefono ufficio |Immettere il nome dell'attributo che contiene il numero di telefono dell'ufficio in un record utente.  Il valore predefinito è telephoneNumber. |
| Telefono abitazione |Immettere il nome dell'attributo che contiene il numero di telefono dell'abitazione in un record utente.  Il valore predefinito è homePhone. |
| Cercapersone |Immettere il nome dell'attributo che contiene il cercapersone in un record utente.  Il valore predefinito è pager. |
| Cellulare |Immettere il nome dell'attributo che contiene il numero di telefono cellulare in un record utente.  Il valore predefinito è mobile. |
| Fax |Immettere il nome dell'attributo che contiene il numero di fax in un record utente.  Il valore predefinito è facsimileTelephoneNumber. |
| Telefono IP |Immettere il nome dell'attributo che contiene il numero di telefono IP in un record utente.  Il valore predefinito è ipPhone. |
| Personalizzate |Immettere il nome dell'attributo che contiene un numero di telefono personalizzato in un record utente.  Il valore predefinito è blank. |
| Estensione |Immettere il nome dell'attributo che contiene l'interno telefonico in un record utente.  Il valore di questo campo viene usato come interno solo per il numero di telefono principale.  Il valore predefinito è blank. <br><br>Se l'attributo Estensione non è specificato, è possibile includere l'interno come parte dell'attributo del telefono. In questo caso, anteporre una "x" all'interno in modo che venga analizzato correttamente.  Ad esempio, 555-123-4567 x890 indica che 555-123-4567 è il numero di telefono e 890 è l'interno. |
| Pulsante Ripristina impostazioni predefinite |Fare clic su **Ripristina impostazioni predefinite** per ripristinare il valore predefinito di tutti gli attributi.  Le impostazioni predefinite dovrebbero funzionare correttamente con lo schema normale di Active Directory o ADAM. |

Per modificare gli attributi, fare clic su **Modifica** nella scheda Attributi.  Verrà visualizzata una finestra in cui è possibile modificare gli attributi. Selezionare i puntini **...** accanto a un attributo per aprire una finestra in cui è possibile scegliere gli attributi da visualizzare. 

![Modifica attributi](./media/multi-factor-authentication-get-started-server-dirint/dirint4.png)

## <a name="synchronization"></a>Sincronizzazione
La sincronizzazione consente di mantenere il database utenti di Azure MFA sincronizzato con gli utenti di Active Directory o di un'altra directory LDAP (Lightweight Directory Access Protocol). Il processo è simile alla procedura di importazione manuale di utenti da Active Directory, con la differenza che periodicamente viene eseguito il polling delle modifiche apportate ai gruppi di sicurezza e agli utenti di Active Directory da elaborare.  Permette anche di disattivare o rimuovere gli utenti che sono stati rimossi da un contenitore, da un gruppo di sicurezza o da Active Directory.

Il servizio di sincronizzazione di Active Directory del server Multi-Factor Authentication è un servizio di Windows che esegue il polling periodico di Active Directory.  Non deve essere confuso con Azure AD Sync o Azure AD Connect.  Il servizio di sincronizzazione di Active Directory del server Multi-Factor Authentication, benché basato su una codebase simile, è specifico per il server Azure Multi-Factor Authentication.  Viene installato in stato di arresto e avviato dal servizio del server Multi-Factor Authentication nel momento in cui viene configurato per l'esecuzione.  Se si ha una configurazione multiserver di Multi-Factor Authentication, tenere presente che il servizio di sincronizzazione di Active Directory del server Multi-Factor Authentication può essere eseguito solo su un unico server.

Il servizio di sincronizzazione di Active Directory del server Multi-Factor Authentication usa l'estensione del server LDAP DirSync fornita da Microsoft per eseguire il polling delle modifiche in modo efficiente.  È necessario che il chiamante del controllo DirSync abbia il diritto "directory get changes" e il diritto esteso di controllo di accesso DS-Replication-Get-Changes.  Per impostazione predefinita, questi diritti vengono assegnati agli account amministratore e LocalSystem nei controller di dominio  e il servizio di sincronizzazione è configurato per l'esecuzione come LocalSystem.  Di conseguenza, è più semplice eseguire il servizio in un controller di dominio.  Se si configura il servizio perché esegua sempre una sincronizzazione completa, è possibile eseguirlo come account con autorizzazioni minori.  Si tratta di una procedura meno efficiente, ma che richiede meno privilegi per l'account.

Se la directory LDAP supporta ed è configurata per DirSync, il polling delle modifiche apportate a utenti e gruppi di sicurezza viene eseguito nello stesso modo in cui avviene in Active Directory.  Se la directory LDAP non supporta il controllo DirSync, viene eseguita una sincronizzazione completa durante ogni ciclo.

![Sincronizzazione](./media/multi-factor-authentication-get-started-server-dirint/dirint5.png)

La tabella seguente contiene informazioni aggiuntive su ciascuna delle impostazioni della scheda Sincronizzazione.

| Funzionalità | Descrizione |
| --- | --- |
| Abilita sincronizzazione con Active Directory |Se questa opzione è selezionata, il servizio del server Multi-Factor Authentication esegue periodicamente il polling delle modifiche ad Active Directory. <br><br>Nota: affinché il servizio del server Multi-Factor Authentication possa iniziare a elaborare le modifiche è necessario aggiungere almeno un elemento di sincronizzazione ed eseguire una sincronizzazione. |
| Sincronizza ogni |Specificare l'intervallo di attesa tra il polling e l'elaborazione delle modifiche da parte del servizio del server Multi-Factor Authentication. <br><br> Nota: l'intervallo specificato corrisponde al lasso di tempo che intercorre tra l'inizio di ogni ciclo.  Se il tempo impiegato per l'elaborazione delle modifiche supera l'intervallo specificato, il servizio riesegue subito il polling. |
| Rimuovi utenti non più presenti in Active Directory |Se questa opzione è selezionata, il servizio del server Multi-Factor Authentication elabora gli oggetti contrassegnati per la rimozione dell'utente di Active Directory eliminato e rimuove l'utente del server Multi-Factor Authentication correlato. |
| Esegui sempre una sincronizzazione completa |Se questa opzione è selezionata, il servizio del server Multi-Factor Authentication esegue sempre una sincronizzazione completa.  Se invece è deselezionata, il servizio del server Multi-Factor Authentication effettua una sincronizzazione incrementale eseguendo query solo sugli utenti modificati.  Per impostazione predefinita, questa opzione è deselezionata. <br><br>Se l'opzione è deselezionata, il server Azure MFA esegue una sincronizzazione incrementale solo se la directory supporta il controllo DirSync e l'account usato per il binding alla directory è autorizzato a eseguire query incrementali su DirSync.  Se l'account non ha le autorizzazioni appropriate o se nella sincronizzazione sono coinvolti più domini, il server Azure MFA esegue una sincronizzazione completa. |
| Richiedi l'approvazione dell'amministratore per la disabilitazione o la rimozione di più di X utenti |È possibile configurare gli elementi di sincronizzazione per disabilitare o rimuovere gli utenti che non sono più membri del contenitore o del gruppo di sicurezza dell'elemento.  Come misura di sicurezza, è possibile anche richiedere l'approvazione dell'amministratore quando il numero di utenti da disabilitare o rimuovere supera una determinata soglia.  Se questa opzione è selezionata, viene richiesta l'approvazione per la soglia specificata.  Il valore predefinito è 5 e l'intervallo valido è compreso tra 1 e 999. <br><br> L'approvazione viene facilitata inviando prima una notifica e-mail agli amministratori. La notifica tramite posta elettronica fornisce istruzioni per verificare e approvare la disabilitazione e la rimozione degli utenti.  L'approvazione viene richiesta all'avvio dell'interfaccia utente del server Multi-Factor Authentication. |

Il pulsante **Sincronizza** consente di eseguire una sincronizzazione completa per gli elementi di sincronizzazione specificati.  È necessario eseguire una sincronizzazione completa ogni volta che vengono aggiunti, modificati, rimossi o riordinati elementi di sincronizzazione.  È necessario eseguirla anche prima di attivare il servizio ADSync di Multi-Factor Authentication, perché imposta il punto di partenza da cui il servizio eseguirà il polling delle modifiche incrementali.  Se sono state apportate modifiche agli elementi di sincronizzazione, ma non è ancora stata eseguita una sincronizzazione completa, verrà richiesto di eseguirla.

Il pulsante **Rimuovi** consente all'amministratore di eliminare uno o più elementi di sincronizzazione dall'elenco degli elementi di sincronizzazione del server Multi-Factor Authentication.

> [!WARNING]
> Dopo la rimozione, un record di elemento di sincronizzazione non può più essere recuperato. Se è stato eliminato per errore, è necessario aggiungerlo di nuovo.

L'elemento o gli elementi di sincronizzazione rimossi dal server Multi-Factor Authentication  non vengono più elaborati dal server.

I pulsanti Sposta su e Sposta giù consentono all'amministratore di modificare l'ordine degli elementi di sincronizzazione.  L'ordine è importante poiché uno stesso utente può essere membro di più elementi di sincronizzazione, ad esempio di un contenitore e di un gruppo di sicurezza.  Le impostazioni applicate all'utente durante la sincronizzazione derivano dal primo elemento di sincronizzazione presente nell'elenco a cui è associato l'utente.  È quindi consigliabile inserire gli elementi di sincronizzazione in ordine di priorità.

> [!TIP]
> Dopo aver rimosso elementi di sincronizzazione è opportuno eseguire una sincronizzazione completa.  Dopo aver ordinato gli elementi di sincronizzazione è opportuno eseguire una sincronizzazione completa.  Fare clic su **Sincronizza ora** per eseguire una sincronizzazione completa.

## <a name="multi-factor-auth-servers"></a>Server Multi-Factor Authentication
È possibile impostare server Multi-Factor Authentication aggiuntivi da usare come proxy RADIUS di backup, proxy LDAP o per l'autenticazione IIS. La configurazione di sincronizzazione è condivisa tra tutti gli agenti, ma solo uno di essi può eseguire il servizio del server Multi-Factor Authentication. Questa scheda consente di selezionare il server Multi-Factor Authentication da abilitare per la sincronizzazione.

![Server Multi-Factor Authentication](./media/multi-factor-authentication-get-started-server-dirint/dirint6.png)


