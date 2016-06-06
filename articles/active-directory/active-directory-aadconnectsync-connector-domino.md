<properties
   pageTitle="Servizio di sincronizzazione Azure AD Connect: connettore Lotus Domino | Microsoft Azure"
   description="Questo articolo descrive come configurare il connettore Lotus Domino di Microsoft."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="05/24/2016"
   ms.author="andkjell"/>

# Documentazione tecnica sul connettore Lotus Domino

Questo articolo descrive il connettore Lotus Domino ed è applicabile ai prodotti seguenti:

- Microsoft Identity Manager 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   È necessario usare l'hotfix 4.1.3671.0 o versione successiva ([KB3092178](https://support.microsoft.com/kb/3092178)).

Per MIM2016 e FIM2010R2 il connettore è disponibile come download dall'[Area download Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

## Panoramica del connettore Lotus Domino

Il connettore Lotus Domino consente di integrare il servizio di sincronizzazione con il server Lotus Domino di IBM.

A livello generale, le funzionalità seguenti sono supportate dalla versione corrente del connettore:

Funzionalità | Supporto
--- | ---
Origine dati connessa | Server: <li>Lotus Domino 8.5.x</li><li>Lotus Domino 9.x</li>Client:<li>Lotus Notes 9.x</li>
Scenari | <li>Gestione del ciclo di vita degli oggetti</li><li>Gestione dei gruppi</li><li>Gestione delle password</li>
Operazioni | <li>Importazione completa e delta</li><li>Esportazione</li><li>Impostazione e modifica di password nella password HTTP</li>
Schema | <li>Person (utente mobile, contatto ovvero persone senza certificato)</li><li>Group</li><li>Resource (risorsa, chat room, riunione online)</li><li>Mail-in database</li><li>Individuazione dinamica di attributi per gli oggetti supportati</li>

Il connettore Lotus Domino sfrutta il client Lotus Notes per comunicare con il server Lotus Domino. Di conseguenza, è necessario installare un client Lotus Notes supportato nel server di sincronizzazione. La comunicazione tra il client e il server viene implementata tramite l'interfaccia di Lotus Notes .NET Interop (Interop.domino.dll). Questa interfaccia facilita la comunicazione tra la piattaforma Microsoft.NET e il client Lotus Notes e supporta l'accesso a documenti e viste di Lotus Domino. Per l'importazione delta è anche possibile usare l'interfaccia nativa C++, a seconda del metodo di importazione delta selezionato.

### Prerequisiti

Prima di usare il connettore, verificare che nel server di sincronizzazione sia disponibile quanto segue, oltre a eventuali hotfix indicati in precedenza:

- Microsoft .NET 4.5.2 Framework o versione successiva
- Il client Lotus Notes deve essere installato nel server di sincronizzazione
- Il connettore Lotus Domino richiede che il database con schema LDAP Lotus Domino predefinito (schema.nsf) predefinito sia presente nel server Domino Directory. È necessario verificare che sia disponibile. In caso contrario, è possibile installarlo eseguendo o riavviando il servizio LDAP nel server Domino.

### Autorizzazioni dell'origine dati connessa

Per eseguire una qualsiasi delle attività supportate nel connettore Lotus Domino, è necessario essere membro dei gruppi seguenti:

- Full Access administrators
- Administrators
- Database Administrators

La tabella seguente elenca le autorizzazioni richieste per ogni operazione:

Operazione | Diritti di accesso
--- | ---
Importazione | <li>Leggere i documenti pubblici</li><li> Amministratore con accesso completo. Quando si è membri del gruppo Full Access Administrators, si ha automaticamente accesso effettivo ad ACL.</li>
Esportazione e impostazione della password | Accesso effettivo: <li>Creare documenti</li><li>Eliminare documenti</li><li>Leggere documenti pubblici</li><li>Scrivere documenti pubblici</li><li>Replicare o copiare documenti</li>Oltre all'accesso precedente, è necessario assegnare i ruoli seguenti per le operazioni di esportazione: <li>CreateResource</li><li>GroupCreator</li><li>GroupModifier</li><li>UserCreator</li><li>UserModifier</li>

### Operazioni dirette e AdminP

Le operazioni vengono trasferite alla directory Domino direttamente o tramite il processo AdminP. Le tabelle seguenti includono l'elenco di tutti gli oggetti, le operazioni e, se applicabile, il metodo di implementazione correlato supportati:

**Rubrica primaria**

Oggetto | Creazione | Aggiornamento | Eliminazione
--- | --- | --- | ---
Person | AdminP | Diretto | AdminP
Group | AdminP | Diretto | AdminP
MailInDB | Diretto | Diretto | Diretto
Resource | AdminP | Diretto | AdminP

**Rubrica secondaria**

Oggetto | Creazione | Aggiornamento | Eliminazione
--- | --- | --- | ---
Person | N/D | Diretto | Diretto
Group | Diretto | Diretto | Diretto
MailInDB | Diretto | Diretto | Diretto
Resource | N/D | N/D | N/D

Quando si crea una risorsa, viene creato un documento di Notes. Analogamente, quando si elimina una risorsa, il documento di Notes viene eliminato.

### Porte e protocolli

Il client IBM Lotus Notes e i server Domino comunicano tramite NRPC (Notes Remote Procedure Call) che dovrà usare TCP/IP. Il numero di porta predefinito è 1352, ma può essere modificato dall'amministratore di Domino.

### Non supportate

Le operazioni seguenti non sono supportate nella versione corrente del connettore Lotus Domino:

- Spostamento di cassette postali tra server.

## Creare un nuovo connettore

### Installazione e configurazione del software client

Lotus Notes deve essere installato nel server **prima** dell'installazione del connettore.

Quando si esegue l'installazione, assicurarsi di selezionare **Single User Install**. L'opzione predefinita **Multi-User Install** non funzionerà.

![Notes1](./media/active-directory-aadconnectsync-connector-domino/notes1.png)

Nella pagina delle funzionalità installare solo quelle di Lotus Notes richieste e **Client Single Logon**. L'accesso singolo è necessario per consentire al connettore di accedere al server Domino.

![Notes2](./media/active-directory-aadconnectsync-connector-domino/notes2.png)

**Nota:** è necessario avviare Lotus Notes una volta con un utente presente nello stesso server dell'account che verrà usato come account del servizio del connettore. Assicurarsi anche di chiudere il client Lotus Notes nel server. Il client non può essere in esecuzione mentre il connettore tenta di connettersi al server Domino.

### Create Connector

Per creare un connettore Lotus Domino, in **Synchronization Service** selezionare **Management Agent** e quindi **Create**. Selezionare il connettore **Lotus Domino (Microsoft)**.

![CreateConnector](./media/active-directory-aadconnectsync-connector-domino/createconnector.png)

Se la versione del servizio di sincronizzazione offre la possibilità di impostare un valore per **Architecture**, assicurarsi che per il connettore sia selezionata l'impostazione predefinita per l'esecuzione in **Process**.

### Connettività

Nella pagina Connectivity è necessario specificare il nome del server Lotus Domino e immettere le credenziali di accesso.

![Connettività](./media/active-directory-aadconnectsync-connector-domino/connectivity.png)

La proprietà Domino Server supporta due formati per il nome del server:

- ServerName
- ServerName/DirectoryName

**ServerName/DirectoryName** è il formato preferito per questo attributo, perché consente una risposta più veloce quando il connettore contatta il server Domino.

Il file USerID fornito è archiviato nel database di configurazione del servizio di sincronizzazione.

Per **Delta Import** sono disponibili queste opzioni:

- **None**. Il connettore non eseguirà importazioni delta.
- **Add/Update**. Il connettore eseguirà l'importazione delta, l'aggiunta e l'aggiornamento delle operazioni. Per l'eliminazione è necessaria un'operazione **Full Import**. Questa operazione usa .Net interop di Notes.
- **Add/Update/Delete**. Il connettore eseguirà l'importazione delta, l'aggiunta, l'aggiornamento e l'eliminazione delle operazioni. Questa operazione usa interop .Net di Notes.

In **Schema Options** sono disponibili le opzioni seguenti:

- **Default Schema**. È l'opzione predefinita e il connettore rileva lo schema dal server Domino.
- **DSML-Schema**. Usata solo se il server Domino non espone lo schema. È quindi possibile creare invece un file DSML con lo schema e importarlo. Per altre informazioni su DSML, vedere [OASIS](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=dsml).

Quando si fa clic su Next, vengono verificati i parametri di configurazione UserID e password.

### Parametri globali

Nella pagina Global Parameters configurare il fuso orario e l'opzione per l'operazione di importazione ed esportazione.

![Parametri globali](./media/active-directory-aadconnectsync-connector-domino/globalparameters.png)

Il parametro **Domino Server Time Zone** definisce la posizione del server Domino.

Questa opzione di configurazione è necessaria per supportare le operazioni di **importazione delta**, perché consente al servizio di sincronizzazione di determinare le modifiche tra le ultime due importazioni.

#### Impostazioni di importazione, metodo

Per **Perform Full Import By** sono disponibili le opzioni seguenti:

- Search
- View (Recommended)

**Search** usa l'indicizzazione in Domino, ma è normale che gli indici non siano aggiornati in tempo reale e i dati restituiti dal server non siano sempre corretti. Per un sistema con molte modifiche, questa opzione in genere non funziona molto bene e fornisce false eliminazioni in alcune situazioni. Tuttavia, **Search** è più veloce di **View**.

**View** è l'opzione consigliata perché fornisce lo stato corretto dei dati. È leggermente più lenta rispetto a Search.

#### Creazione di oggetti Virtual Contact

L'impostazione **Enable creation of \_Contact object** include le opzioni seguenti:

- None
- Non-Reference Values
- Reference and Non-Reference Values

In Domino gli attributi di riferimento possono contenere molti formati diversi per fare riferimento ad altri oggetti. Per poter rappresentare diverse varianti, il connettore implementa oggetti \_Contact, detti anche Virtual Contact (VC). Questi oggetti vengono creati per poter essere uniti a oggetti MV esistenti e usati come nuovi oggetti, conservando in questo modo gli attributi di riferimento.

Se questa impostazione è abilitata e se il contenuto di un attributo di riferimento non è un formato di nome distinto, viene creato un oggetto \_Contact. Ad esempio, un attributo member di un gruppo può contenere un indirizzo SMTP. È anche possibile che negli attributi di riferimento siano presenti shortName e altri attributi. Per questo scenario, selezionare **Non-Reference Values**. Si tratta dell'impostazione più comune per le implementazioni di Domino.

Quando Lotus Domino viene configurato con rubriche separate e diversi nomi distinti che rappresentano lo stesso oggetto, è anche possibile creare oggetti \_Contact per tutti i valori di riferimento presenti in una rubrica. Per questo scenario, selezionare l'opzione **Reference and Non-Reference Values**.

Se nell'attributo **FullName** in Domino sono presenti più valori, è consigliabile abilitare anche la creazione di Virtual Contact per la risoluzione dei riferimenti. Questo attributo può avere più valori dopo un matrimonio o un divorzio. Selezionare la casella di controllo **Enable ... FullName has multiple values** per questo scenario.

Unendo gli attributi corretti, gli oggetti \_Contact potranno essere aggiunti all'oggetto MV.

Al DN di questi verrà aggiunto VC=\_Contact.

#### Impostazioni di importazione, oggetto conflict

**Exclude Conflict Object**

In un'implementazione di Domino di grandi dimensioni è possibile che più oggetti abbiano lo stesso DN a causa di problemi di replica. In questi casi, il connettore vedrà due oggetti con UniversalID diversi, ma lo stesso DN. Questa condizione comporta la creazione di un oggetto temporaneo nello spazio del connettore. Il connettore può ignorare gli oggetti selezionati in Domino come vittime della replica. È consigliabile mantenere selezionata questa casella di controllo.

#### Impostazioni di esportazione

Se l'opzione **Use AdminP for updating references** non è selezionata, l'esportazione degli attributi di riferimento, ad esempio member, sarà una chiamata diretta e non userà il processo AdminP. Questa opzione deve essere usata solo se AdminP non è stato configurato per mantenere l'integrità referenziale.

#### Informazioni di routing

In Domino è possibile che in un attributo di riferimento siano incorporate informazioni di routing come suffisso del DN. Ad esempio, l'attributo member di un gruppo può contenere **CN=example/organization@ABC**. Il suffisso @ABC si riferisce alle informazioni di routing. Le informazioni di routing vengono usate da Domino per inviare messaggi di posta elettronica al sistema Domino corretto, che può essere un sistema in un'altra organizzazione. Nel campo Routing Information è possibile specificare i suffissi di routing usati all'interno dell'organizzazione nell'ambito del connettore. Se uno di questi valori viene trovato come suffisso in un attributo di riferimento, le informazioni di routing vengono rimosse dal riferimento in modo che corrisponda al DN dell'oggetto nello spazio del connettore. Se non è possibile far corrispondere il suffisso di routing in un valore di riferimento a uno di quelli specificati, viene creato un oggetto \_Contact. Questi oggetti \_Contact verranno creati con **RO = @<RoutingSuffix>** inserito nel DN. Per questi oggetti \_Contact vengono aggiunti anche gli attributi seguenti per consentirne l'unione a un oggetto reale, se necessario: \_routingName, \_contactName, \_displayName e UniversalID.

#### Altre rubriche

Se non è installata la funzionalità **Directory Assistance**, che fornisce il nome della rubrica secondaria, è possibile immettere manualmente le rubriche.

#### Trasformazione multivalore

Molti attributi in Lotus Domino sono di tipo multivalore. Gli attributi del metaverse corrispondenti sono in genere a valore singolo. Se si configura l'opzione relativa all'operazione Import ed Export, si abilita il connettore alla necessaria conversione degli attributi interessati, semplificando di conseguenza la configurazione.

**Esportazione**

L'opzione relativa all'operazione Export supporta due modalità:

- Append item
- Replace item

**Replace Item**: quando si seleziona questa opzione, il connettore rimuove sempre i valori correnti dell'attributo in Domino e li sostituisce con i valori forniti, che possono essere a valore singolo o multivalore.

Esempio: l'attributo Assistant di un oggetto person presenta i valori seguenti:

- CN=Greg Winston/OU= Contoso /O=Americas,NAB=names.nsf
- CN=John Smith/OU= Contoso /O=Americas,NAB=names.nsf

Se un nuovo assistente di nome **David Alexander** viene assegnato a questo oggetto person, il risultato sarà:

- CN=David Alexander/OU= Contoso /O=Americas,NAB=names.nsf

**Append Item**: quando si seleziona questa opzione, il connettore manterrà i valori esistenti dell'attributo in Domino e inserirà i nuovi valori nella parte superiore dell'elenco di dati.

Esempio: l'attributo Assistant di un oggetto person presenta i valori seguenti:

- CN=Greg Winston/OU= Contoso /O=Americas,NAB=names.nsf
- CN=John Smith/OU= Contoso /O=Americas,NAB=names.nsf

Se un nuovo assistente di nome **David Alexander** viene assegnato a questo oggetto person, il risultato sarà:

- CN=David Alexander/OU= Contoso /O=Americas,NAB=names.nsf
- CN=Greg Winston/OU= Contoso /O=Americas,NAB=names.nsf
- CN=John Smith/OU= Contoso /O=Americas,NAB=names.nsf

**Import**

L'opzione relativa all'operazione Import supporta due modalità:

- Default
- Multivalued to Single Value

**Default**: quando si seleziona l'opzione predefinita, verranno importati tutti i valori di tutti gli attributi.

**Multivalued to Single Value**: quando si seleziona questa opzione, un attributo multivalore viene convertito in un attributo a valore singolo. Se esiste più di un valore, verrà usato quello nella parte superiore, che in genere è anche l'ultimo.

Esempio:

L'attributo Assistant di un oggetto person presenta i valori seguenti:

- CN=David Alexander/OU= Contoso /O=Americas,NAB=names.nsf
- CN=Greg Winston/OU= Contoso /O=Americas,NAB=names.nsf
- CN=John Smith/OU= Contoso /O=Americas,NAB=names.nsf

L'aggiornamento più recente a questo attributo è **David Alexander**. Poiché l'opzione relativa all'operazione Import è impostata su Multivalued to Single Value, il connettore importa solo **David Alexander** nel relativo spazio.

La logica per convertire gli attributi multivalore in attributi a valore singolo non è applicabile all'attributo member di un oggetto group e all'attributo fullname di un oggetto person.

È anche possibile configurare regole di trasformazione per l'importazione e l'esportazione per gli attributi multivalore per ogni attributo, come eccezione alla regola globale. Per configurare questa opzione, immettere [tipooggetto].[nomeattributo] nelle caselle di testo **Import Exclusion Attribute List** ed **Export Exclusion Attribute List**. Ad esempio, se si immette Person.Assistant e viene impostato il flag globale per importare tutti i valori, verrà importato solo il primo valore per l'assistente.

#### File di certificazione

Sono elencate tutte le unità organizzative/dell'organizzazione. Per poter esportare oggetti person nella rubrica primaria, è necessario un file certificazione con la relativa password.

È possibile usare **Password for all Certifers(s)** se tutti i file di certificazione hanno la stessa password. È quindi possibile immettere la password e specificare solo il file di certificazione.

Se si esegue solo l'importazione, non è necessario specificare alcun file di certificazione.

### Configurare una gerarchia di provisioning

Quando si configura il connettore Lotus Domino, è possibile ignorare la pagina Configure Provisioning Hierarchy della finestra di dialogo, perché questo connettore non supporta il provisioning della gerarchia.

![Gerarchia di provisioning](./media/active-directory-aadconnectsync-connector-domino/provisioninghierarchy.png)

### Configurare partizioni e gerarchie

Quando si configurano partizioni e gerarchie, è necessario selezionare la rubrica primaria denominata NAB=names.nsf.

Oltre alla rubrica primaria, è possibile selezionare rubriche secondarie, se presenti.

![Partizioni](./media/active-directory-aadconnectsync-connector-domino/partitions.png)

### Selezionare gli attributi

Quando si configurano gli attributi, è necessario selezionare tutti quelli con prefisso **\_MMS\_**. Questi attributi sono obbligatori quando si effettua il provisioning di nuovi oggetti in Lotus Domino.

![Attributi](./media/active-directory-aadconnectsync-connector-domino/attributes.png)

## Gestione del ciclo di vita degli oggetti

Questa sezione fornisce una panoramica dei diversi oggetti disponibili in Domino.

### Oggetti person

L'oggetto person rappresenta gli utenti nell'organizzazione e nelle unità organizzative. Oltre agli attributi predefiniti, l'amministratore di Domino può aggiungere attributi personalizzati a un oggetto Person. Come minimo, un oggetto Person deve includere tutti gli attributi obbligatori. Per un elenco completo degli attributi obbligatori, vedere [Proprietà di Lotus Notes](#lotus-notes-properties). Per registrare un oggetto person, devono essere soddisfatti i prerequisiti seguenti

- La rubrica (names.nsf) deve essere stata definita ed essere la rubrica primaria.
- È necessario avere l'ID certificazione e la password O/OU per la registrazione di un determinato utente nell'organizzazione o unità organizzativa.
- È necessario definire un set specifico di proprietà di Lotus Notes per un oggetto person. Vengono usate per il provisioning dell'oggetto person. Per altre informazioni, vedere la sezione intitolata [Proprietà di Lotus Notes](#lotus-notes-properties) più avanti in questo documento.
- La password HTTP iniziale per un oggetto person è un attributo e viene impostata durante il provisioning.
- L'oggetto person deve essere uno dei tre tipi supportati seguenti:
    1. Normal User, ha un file di posta elettronica e un file di ID utente
    2. Roaming User, un tipo Normal User che include tutti i file di database mobili
    3. Contact, utente senza file di ID

Gli oggetti Person, con l'eccezione di Contact, possono essere ulteriormente raggruppati in US Users e International Users come definito dal valore della proprietà \_MMS\_IDRegType. Questi sono gli utenti che usano il client Lotus Notes per accedere ai server Lotus Domino, ai database, hanno un ID di Notes e un documento Person. Se usano la posta elettronica di Notes, avranno anche un file di posta elettronica. L'utente deve essere registrato per diventare attivo. Per altre informazioni, vedere gli articoli relativi a:

- [Configurazione degli utenti Lotus Notes](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_SETTING_UP_NOTES_USERS.html)
- [Registrazione degli utenti](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_REGISTERING_USERS.html)
- [Gestione degli utenti](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_USERS_5151.html)
- [Ridenominazione degli utenti](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_USER_AUTOMATICALLY.html)

Tutte queste operazioni vengono eseguite in Lotus Domino e quindi importate nel servizio di sincronizzazione.

### Risorse e chat

Una risorsa è un altro tipo di database di Lotus Domino. Le risorse possono essere sale riunioni con vari tipi di apparecchiature, ad esempio i proiettori. Esistono sottotipi di risorse supportate dal connettore Lotus Domino definiti in base all'attributo Resource Type:

Type of Resource | Resource Type Attribute
--- | ---
Room | 1
Resource (Other) | 2
Online Meeting | 3

Per il funzionamento del tipo di oggetto Resource, è necessario quanto segue:

- Il database Resource Reservation deve essere già esistente nel server Domino connesso
- Il sito è già definito per il tipo Resource

Il database Resource Reservation contiene 3 tipi di documenti:

- Site Profile
- Resource
- Reservation

Per altre informazioni sulla configurazione del database Resource Reservation, vedere l'articolo relativo alla [configurazione del database Resource Reservations](https://www-01.ibm.com/support/knowledgecenter/SSKTMJ_8.0.1/com.ibm.help.domino.admin.doc/DOC/H_SETTING_UP_THE_RESOURCE_RESERVATIONS_DATABASE.html).

**Creare, aggiornare ed eliminare risorse**

Le operazioni Create, Update e Delete vengono eseguite dal connettore Lotus Domino nel database Resource Reservation. Di conseguenza, le risorse vengono create come documenti in Names.nsf, ovvero la rubrica primaria. Per altri informazioni sulla modifica e l'eliminazione di risorse, vedere l'articolo relativo alla [modifica ed eliminazione di documenti sulle risorse](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_EDITING_AND_DELETING_RESOURCE_DOCUMENTS.html).

**Operazione di importazione ed esportazione per le risorse**

Le risorse possono essere importate ed esportate nel servizio di sincronizzazione come qualsiasi altro tipo di oggetto. Selezionare il tipo di oggetto come Resource durante la configurazione. Perché l'operazione di esportazione abbia esito positivo, è necessario avere i dettagli per Resource type, Conference Database e Site name.

### Database di posta elettronica in ingresso

Mail-In Database è un database progettato per ricevere messaggi di posta elettronica. Si tratta di una cassetta postale di Lotus Domino, il database Notes basato sul modello Mail, che non è associato a un account utente di Lotus Domino specifico, ovvero non ha un proprio file di ID e password. Un database di posta elettronica in ingresso ha una proprietà UserID univoca ("nome breve") associata ad esso e il proprio indirizzo di posta elettronica.

Ogni utente richiede un solo account di Lotus Domino. Se è necessario avere una cassetta postale separata con il proprio indirizzo di posta elettronica che può essere condiviso tra diversi utenti (ad esempio: group@contoso.com), viene creato un database di posta elettronica in ingresso invece di un account di Notes aggiuntivo. L'accesso a questa cassetta postale è controllato tramite l'elenco di controllo di accesso (ACL), che contiene i nomi degli utenti di Notes ai quali è consentito aprire la cassetta postale usando i propri file di ID e le password. Non è richiesta una password separata per accedere a un database di posta elettronica in ingresso.

Per un elenco degli attributi richiesti, vedere la sezione intitolata [Attributi obbligatori](#mandatory-attributes) più avanti in questo articolo.

Quando un database è progettato per ricevere un messaggio di posta elettronica, viene creato un documento Mail-In Database in Lotus Domino. Questo documento deve trovarsi nella Directory Domino di ogni server nel quale è archiviata una copia del database. Per una descrizione dettagliata sulla creazione di un documento per il database di posta elettronica, vedere l'articolo relativo alla [creazione di un documento Mail-In Database](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_A_MAILIN_DATABASE_DOCUMENT_FOR_A_NEW_DATABASE_OVERVIEW.html).

Prima di creare un Mail-In Database, è necessario che il database, creato dall'amministratore di Lotus, esista già nel server Domino.

### Gestione di gruppi

È possibile ottenere una panoramica dettagliata della gestione dei gruppi di Lotus Domino nelle risorse seguenti:

- [Uso di gruppi](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_USING_GROUPS_OVER.html)
- [Creazione di un gruppo](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS_MIDTOPIC_55038956829238418.html)
- [Creazione e modifica dei gruppi](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS.html)
- [Gestione dei gruppi](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_GROUPS_1804.html)
- [Ridenominazione di un gruppo](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_GROUP_STEPS.html)

### Gestione delle password

Per un utente di Lotus Domino registrato esistono due tipi di password:

1. Password utente (archiviata nel file User.id)
2. Password Internet/HTTP

Il connettore Lotus Domino supporta solo operazioni con password HTTP.

Per eseguire la gestione delle password, è consigliabile abilitarla per il connettore in Management Agent Designer. Per abilitare la gestione delle password, selezionare **Enable password management** nella pagina della finestra di dialogo **Configure Extensions**.

![Configurare le estensioni](./media/active-directory-aadconnectsync-connector-domino/configureextensions.png)

Il connettore Lotus Domino supporta le operazioni seguenti con password Internet:

- Set Password: imposta una nuova password HTTP/Internet per l'utente in Domino. Per impostazione predefinita, l'account sarà anche sbloccato. Il flag di sblocco viene esposto nell'interfaccia WMI del motore di sincronizzazione.
- Change Password: in questo scenario è possibile che un utente voglia modificare la password o che gli venga richiesto di cambiare la password dopo un periodo di tempo specificato. Per eseguire questa operazione, è obbligatorio avere la vecchia e la nuova password. Una volta modificata, la nuova password viene aggiornata in Lotus Domino.

Per altre informazioni, vedere:

- [Uso della funzionalità di blocco in Internet](http://www.ibm.com/developerworks/lotus/library/domino8-lockout/)
- [Gestione delle password in Internet](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_NOTES_AND_INTERNET_PASSWORD_SYNCHRONIZATION_7570_OVER.html)

## Informazioni di riferimento

Questa sezione elenca ad esempio le descrizioni degli attributi e i requisiti relativi agli attributi per il connettore Lotus Domino.

### Proprietà di Lotus Notes

Quando si effettua il provisioning degli oggetti Person nella directory di Lotus Domino, gli oggetti devono avere un set specifico di proprietà con valori specifici popolati. Questi valori sono necessari solo per le operazioni Create.

La tabella seguente include l'elenco di queste proprietà con la relativa descrizione.

Proprietà | Descrizione
--- | ---
\_MMS\_AltFullName | Nome completo alternativo dell'utente.
\_MMS\_AltFullNameLanguage | Lingua da usare per specificare il nome alternativo dell'utente.
\_MMS\_CertDaysToExpire | Numero di giorni dalla data corrente prima della scadenza del certificato. Se non è specificata, la data predefinita è di due anni dalla data corrente.
\_MMS\_Certifier | Proprietà che contiene il nome della gerarchia organizzativa del file di certificazione. Ad esempio: OU=OrganizationUnit,O=Org,C=Country.
\_MMS\_IDPath | Se la proprietà è vuota, nessun file di identificazione utente viene creato localmente nel server di sincronizzazione. Se la proprietà contiene un nome file, viene creato un file di ID utente nella cartella madata. La proprietà può anche contenere un percorso completo in cui viene creato il file di ID utente in questa posizione.
\_MMS\_IDRegType | Le persone possono essere classificate nei contatti. US Users e International Users. La tabella seguente elenca i valori possibili:<li>0 - Contact</li><li>1 - U.S. user</li><li>2 - International user</li>
\_MMS\_IDStoreType | Proprietà obbligatoria per US Users e International Users. La proprietà contiene un valore intero che specifica se l'ID utente viene archiviato come allegato nella rubrica di Notes o nei file di posta elettronica della persona. Se il file di ID utente è un allegato nella rubrica, può facoltativamente essere creato come un file con \_MMS\_IDPath.<li>Empty: file di ID archiviato nell'insieme di credenziali degli ID, nessun file di identificazione (usato per i contatti).</li><li> 1. Allegato nella rubrica di Lotus Notes. La proprietà \_MMS\_Password deve essere impostata per i file di identificazione utente allegati</li><li>2. ID archiviato nel file di posta elettronica della persona. La proprietà \_MMS\_UseAdminP deve essere impostata su false per consentire la creazione del file di posta elettronica durante la registrazione dell'oggetto Person. La proprietà \_MMS\_Password deve essere impostata per i file di identificazione utente.</li>
\_MMS\_MailQuotaSizeLimit | Numero di megabyte consentiti per il database dei file di posta elettronica.
\_MMS\_MailQuotaWarningThreshold | Numero di megabyte consentiti per il database dei file di posta elettronica prima che venga generato un avviso.
\_MMS\_MailTemplateName | File modello di posta elettronica usato per creare il file di posta elettronica dell'utente. Se viene specificato un modello, il file di posta elettronica viene creato con il modello specificato. Se non viene specificato un modello, per creare il file viene usato il file modello predefinito.
\_MMS\_OU | Proprietà facoltativa che corrisponde al nome dell'unità organizzativa nel file di certificazione. Questa proprietà deve essere vuota per i contatti.
\_MMS\_Password | Proprietà obbligatoria per gli utenti. La proprietà contiene la password per il file di identificazione dell'oggetto.
\_MMS\_UseAdminP | Proprietà che deve essere impostata su true se il file di posta elettronica deve essere creato dal processo AdminP nel server Domino (asincrono per il processo di esportazione). Se la proprietà è impostata su false, il file di posta elettronica viene creato con l'utente di Domino (sincrono nel processo di esportazione).

Per un utente con un file di identificazione associato la proprietà \_MMS\_Password deve contenere un valore. Per l'accesso alla posta elettronica tramite il client Lotus Notes, le proprietà MailServer e MailFile devono contenere un valore.

Per accedere alla posta elettronica tramite un Web browser, le proprietà seguenti devono contenere valori:

- MailFile: proprietà obbligatoria che contiene il percorso del server Lotus Domino in cui è archiviato il file della posta.
- MailServer: proprietà obbligatoria che contiene il nome del server Lotus Domino. Questo è il nome da usare quando si crea il file della posta di Lotus sul server Domino.
- HTTPPassword: proprietà facoltativa che contiene la password di accesso Web per l'oggetto.

Per accedere al server Domino senza funzionalità di posta elettronica, la proprietà HTTPPassword deve contenere un valore e le proprietà MailFile e MailServer possono essere vuote.

Con \_MMS\_ IDStoreType = 2 (ID di archiviazione nel file di posta elettronica), la proprietà MailSystem di NotesRegistrationclass sarà impostata su REG\_MAILSYSTEM\_INOTES (3).

### Attributi obbligatori

Il connettore Lotus Domino supporta principalmente quattro tipi di oggetti (tipi di documento) nel servizio di sincronizzazione:

- Group
- Mail-In Database
- Person
- Contact (Person senza file di certificazione)
- Resource

Questa sezione elenca gli attributi obbligatori per ogni tipo di oggetto supportato per poter esportare l'oggetto in un server Domino.

Tipo di oggetto | Attributi obbligatori
--- | ---
Group | <li>ListName</li>
Main-In Database | <li>FullName</li><li>MailFile</li><li>MailServer</li><li>MailDomain</li>
Person | <li>LastName</li><li>MailFile</li><li>ShortName</li><li>\_MMS\_Password</li><li>\_MMS\_IDStoreType</li><li>\_MMS\_Certifier</li><li>\_MMS\_IDRegType</li><li>\_MMS\_UseAdminP</li>
Contact (Person senza file di certificazione) | <li>\_MMS\_IDRegType</li>
Resource | <li>FullName</li><li>ResourceType</li><li>ConfDB</li><li>ResourceCapacity</li><li>Site</li><li>DisplayName</li><li>MailFile</li><li>MailServer</li><li>MailDomain</li>

## Domande e problemi comuni

### Il rilevamento dello schema non funziona

Per poter rilevare lo schema, è necessario che il file schema.nsf sia presente nel server Domino. Questo file verrà visualizzato solo se LDAP è installato nel server. Se lo schema non è rilevabile, verificare quanto segue:

- Il file schema.nsf è presente nella cartella radice del server Domino.
- L'utente ha le autorizzazioni per visualizzare il file schema.nsf.
- Forzare il riavvio del server LDAP. Aprire "Lotus Domino Console" e usare il comando "Tell LDAP ReloadSchema" per ricaricare lo schema.

### Non tutte le rubriche secondarie sono visibili

Il connettore Domino si basa sulla funzionalità Directory Assistance per trovare le rubriche secondarie. Se mancano le rubriche secondarie, verificare se [Directory Assistance](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_DIRECTORY_ASSISTANCE.html) è stato abilitato e configurato nel server Domino.

### Attributi personalizzati in Domino

Esistono diverse modalità in Domino per estendere lo schema in modo che venga visualizzato come un attributo personalizzato utilizzabile dal connettore.

**Approccio 1: Estendere lo schema di Lotus Domino**

1. Creare una copia del modello Domino Directory {PUBNAMES.NTF} seguendo [questa procedura](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (non personalizzare il modello IBM Lotus Domino Directory predefinito):
2. Aprire la copia del modello Domino Directory {CONTOSO.NTF} appena creata in Domino Designer e seguire questi passaggi seguenti:
    - Fare clic su Shared Elements ed espandere Subforms.
    - Fare doppio clic sulla sottomaschera ${ObjectName}InheritableSchema, dove {ObjectName} è il nome della classe di oggetti strutturali predefinita, ad esempio, Person.
    - Denominare l'attributo che si vuole aggiungere allo schema {MyPersonAtrribute} e in corrispondenza di questo attributo creare un campo selezionando il menu "Create" e quindi "Field" dal menu.
    - Nel campo aggiunto impostarne le proprietà selezionando Type, Style, Size, Font e altri parametri correlati nella finestra Properties del campo.
    - Come valore predefinito, mantenere lo stesso nome specificato per quell'attributo. Ad esempio, se il nome dell'attributo è AttributoPersonale, mantenere lo stesso nome come valore predefinito.
    - Salvare la sottomaschera ${ObjectName}InheritableSchema con i valori aggiornati.
3. Sostituire il modello Directory Domino {PUBNAMES.NTF} con il nuovo modello personalizzato {CONTOSO.NTF} seguendo [questa procedura](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
4. Chiudere Domino Admin e aprire Domino Console per riavviare il servizio LDAP e ricaricare lo schema LDAP:
    - In Domino Console immettere il comando nel campo di testo **Domino Command** per riavviare il servizio LDAP - [Restart Task LDAP](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html).
    - Per ricaricare lo schema LDAP, usare il comando Tell LDAP - Tell LDAP ReloadSchema.
5. Aprire Domino Admin e selezionare la scheda People & Groups per vedere che l'attributo aggiunto è riportato in Domino Add Person.
6. Aprire Schema.nsf dalla scheda "Files" e vedere che l'attributo aggiunto è riportato nella classe di oggetti LDAP dominoPerson.

**Approccio 2: Creare una classe ausiliaria con l'attributo personalizzato e associarla alla classe di oggetti**

1. Creare una copia del modello Domino Directory [PUBNAMES.NTF] seguendo [questa procedura](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (non personalizzare mai il modello IBM Lotus Domino Directory predefinito):
2. Aprire la copia del modello Domino Directory [CONTOSO.NTF] appena creata in Domino Designer.
3. Nel riquadro sinistro selezionare Shared Code e quindi Subforms.
4. Fare clic su New Subform.
5. Seguire questa procedura per specificare le proprietà per la nuova sottomaschera:
    - Con la nuova sottomaschera aperta scegliere Design - Subform Properties.
    - Accanto alla proprietà Name immettere un nome per la classe di oggetti ausiliaria, ad esempio, TestSubform.
    - Mantenere selezionata la proprietà Options "Include in Insert Subform... dialog".
    - Deselezionare la proprietà Options "Render pass through HTML in Notes".
    - Lasciare le altre proprietà invariate e chiudere la finestra Subform Properties.
    - Salvare e chiudere la nuova sottomaschera.
6. Seguire questa procedura per aggiungere un campo per definire la classe di oggetti ausiliaria:
    - Aprire la sottomaschera appena creata.
    - Scegliere Create - Field.
    - Accanto a Name nella scheda Basic della finestra di dialogo Field specificare un nome qualsiasi, ad esempio: {MyPersonTestAttribute}.
    - Nel campo aggiunto impostarne le proprietà selezionando Type, Style, Size, Font e le proprietà correlate.
    - Come valore predefinito, mantenere lo stesso nome specificato per quell'attributo. Ad esempio, se il nome dell'attributo è AttributoDiTestPersonalizzato, mantenere il valore predefinito con lo stesso nome.
    - Salvare la sottomaschera con i valori aggiornati e seguire questa procedura:
        - Nel riquadro sinistro selezionare Shared Code e quindi Subforms.
        - Selezionare la nuova sottomaschera e scegliere Design - Design Properties.
        - Fare clic sulla terza scheda da sinistra e selezionare **Propagate this prohibition of design change**.
7. Aprire la sottomaschera ${ObjectName}ExtensibleSchema, dove {ObjectName} è il nome della classe di oggetti strutturali predefinita, ad esempio, Person.
8. Immettere Resource e selezionare la sottomaschera appena creata, ad esempio, SottomascheraDiTest e salvare la sottomaschera ${ObjectName}ExtensibleSchema.
9. Sostituire il modello Directory Domino {PUBNAMES.NTF} con il nuovo modello personalizzato {CONTOSO.NTF} seguendo [questa procedura](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
10. Chiudere Domino Admin e aprire Domino Console per riavviare il servizio LDAP e ricaricare lo schema LDAP:
    - In Domino Console immettere il comando nel campo di testo **Domino Command** per riavviare il servizio LDAP - [Restart Task LDAP](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html).
    - Per ricaricare lo schema LDAP, usare il comando Tell LDAP - Tell LDAP ReloadSchema.
11. Aprire Domino Admin e selezionare la scheda People & Groups per vedere che l'attributo aggiunto è riportato in Domino Add Person nella scheda Others.
12. Aprire Schema.nsf dalla scheda **Files** e vedere che l'attributo aggiunto è riportato nella classe di oggetti ausiliaria LDAP TestSubform.

**Approccio 3: Aggiungere l'attributo personalizzato alla classe ExtensibleObject**

1. Aprire il file {Schema.nsf} inserito nella directory radice.
2. Selezionare LDAP Object Classes dal menu a sinistra in **All Schema Documents** e fare clic sul pulsante **Add Object class**:
3. Specificare il nome LDAP nel formato {zzzExtensibleSchema} (dove zzz è il nome della classe di oggetti strutturali predefinito, ad esempio Person) con riferimento all'oggetto per cui si vuole estendere lo schema. Ad esempio, per estendere lo schema per la classe di oggetti Person, fornire il nome LDAP {PersonExtensibleSchema}.
4. Fornire il nome della classe Superior Object per cui si vuole estendere lo schema. Ad esempio, per estendere lo schema per la classe di oggetti Person, fornire il nome della classe Superior Object {dominoPerson}:
5. Specificare un OID valido corrispondente alla classe di oggetti.
6. Selezionare gli attributi estesi/personalizzati nei campi relativi ai tipi di attributo Mandatory oppure Optional in base al requisito:
7. Dopo aver aggiunto gli attributi obbligatori a ExtensibleObjectClass, fare clic sul pulsante **Save & Close**.
8. Verrà creata una ExtensibleObjectClass per la rispettiva classe di oggetti predefinita con attributi estesi.

## Risoluzione dei problemi

-	Per informazioni su come abilitare la registrazione per risolvere i problemi relativi al connettore, vedere l'articolo relativo a [come abilitare la traccia ETW per i connettori](http://go.microsoft.com/fwlink/?LinkId=335731).

<!---HONumber=AcomDC_0525_2016-->