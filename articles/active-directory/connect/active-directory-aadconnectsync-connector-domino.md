---
title: Connettore Lotus Domino | Documentazione Microsoft
description: Questo articolo descrive come configurare il connettore Lotus Domino di Microsoft.
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: e07fd469-d862-470f-a3c6-3ed2a8d745bf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: aebe0b74c952045375e264bed88d33d936e34b92
ms.lasthandoff: 03/14/2017


---
# <a name="lotus-domino-connector-technical-reference"></a>Documentazione tecnica sul connettore Lotus Domino
Questo articolo descrive il connettore Lotus Domino ed è applicabile ai prodotti seguenti:

* Microsoft Identity Manager 2016 (MIM2016)
* Forefront Identity Manager 2010 R2 (FIM2010R2)
  * È necessario usare l'hotfix 4.1.3671.0 o versione successiva ( [KB3092178](https://support.microsoft.com/kb/3092178)).

Per MIM2016 e FIM2010R2 il connettore è disponibile come download dall' [Area download Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

## <a name="overview-of-the-lotus-domino-connector"></a>Panoramica del connettore Lotus Domino
Il connettore Lotus Domino consente di integrare il servizio di sincronizzazione con il server Lotus Domino di IBM.

A livello generale, le funzionalità seguenti sono supportate dalla versione corrente del connettore:

| Funzionalità | Supporto |
| --- | --- |
| Origine dati connessa |Server:  <li>Lotus Domino 8.5.x</li><li>Lotus Domino 9.x</li>Client:<li>Lotus Domino 8.5.x</li><li>Lotus Notes 9.x</li> |
| Scenari |<li>Gestione del ciclo di vita degli oggetti</li><li>Gestione di gruppi</li><li>Gestione delle password</li> |
| Operazioni |<li>Importazione completa e delta</li><li>Esportazione</li><li>Impostare e modificare la password in Password HTTP</li> |
| Schema |<li>Persona (utente mobile, contatto (persone senza certificato))</li><li>Gruppo</li><li>Risorsa (risorsa, stanza, riunione online)</li><li>Mail-In Database</li><li>Individuazione dinamica degli attributi per gli oggetti supportati</li> |

Il connettore Lotus Domino usa il client Lotus Notes per comunicare con il server Lotus Domino. A causa di questa dipendenza, è necessario installare un client Lotus Notes supportato nel server di sincronizzazione. La comunicazione tra il client e il server viene implementata tramite l'interfaccia di Lotus Notes .NET Interop (Interop.domino.dll). Questa interfaccia facilita la comunicazione tra la piattaforma Microsoft.NET e il client Lotus Notes e supporta l'accesso a documenti e viste di Lotus Domino. Per l'importazione differenziale è anche possibile usare l'interfaccia nativa C++, a seconda del metodo di importazione differenziale selezionato.

### <a name="prerequisites"></a>Prerequisiti
Prima di usare il connettore, verificare che nel server di sincronizzazione siano disponibili i prerequisiti seguenti:

* Microsoft .NET 4.5.2 Framework o versione successiva
* Il client Lotus Notes deve essere installato nel server di sincronizzazione
* Il connettore Lotus Domino richiede che il database con schema LDAP Lotus Domino predefinito (schema.nsf) predefinito sia presente nel server Domino Directory. In caso contrario, è possibile installarlo eseguendo o riavviando il servizio LDAP nel server Domino.

### <a name="connected-data-source-permissions"></a>Autorizzazioni dell'origine dati connessa
Per eseguire una qualsiasi delle attività supportate nel connettore Lotus Domino, è necessario essere membro dei gruppi seguenti:

* Full Access administrators
* Administrators
* Database Administrators

La tabella seguente elenca le autorizzazioni richieste per ogni operazione:

| Operazione | Diritti di accesso |
| --- | --- |
| Importa |<li>Leggere documenti pubblici</li><li> Amministratore con accesso completo. Quando si è membri del gruppo Full Access Administrators, si ha automaticamente l'accesso effettivo ad ACL.</li> |
| Esportazione e impostazione della password |Accesso effettivo:  <li>Creare i documenti</li><li>Eliminare documenti</li><li>Leggere documenti pubblici</li><li>Scrivere documenti pubblici</li><li>Replicare o copiare documenti</li>Per le operazioni di esportazione sono necessari anche i seguenti ruoli: <li>CreateResource</li><li>GroupCreator</li><li>GroupModifier</li><li>UserCreator</li><li>UserModifier</li> |

### <a name="direct-operations-and-adminp"></a>Operazioni dirette e AdminP
Le operazioni vengono trasferite alla directory Domino direttamente o tramite il processo AdminP. Le tabelle seguenti includono l'elenco di tutti gli oggetti, le operazioni e, se applicabile, il metodo di implementazione correlato supportati:

**Rubrica primaria**

| Oggetto | Create | Aggiornamento | Elimina |
| --- | --- | --- | --- |
| Person |AdminP |Diretto |AdminP |
| Gruppo |AdminP |Diretto |AdminP |
| MailInDB |Diretto |Diretto |Diretto |
| Risorsa |AdminP |Diretto |AdminP |

**Rubrica secondaria**

| Oggetto | Create | Aggiornamento | Elimina |
| --- | --- | --- | --- |
| Person |N/D |Diretto |Diretto |
| Gruppo |Diretto |Diretto |Diretto |
| MailInDB |Diretto |Diretto |Diretto |
| Risorsa |N/D |N/D |N/D |

Quando si crea una risorsa, viene creato un documento di Notes. Analogamente, quando si elimina una risorsa, il documento di Notes viene eliminato.

### <a name="ports-and-protocols"></a>Porte e protocolli
Il client IBM Lotus Notes e i server Domino comunicano tramite NRPC (Notes Remote Procedure Call) che dovrà usare TCP/IP. Il numero di porta predefinito è 1352, ma può essere modificato dall'amministratore di Domino.

### <a name="not-supported"></a>Non supportate
Le operazioni seguenti non sono supportate nella versione corrente del connettore Lotus Domino:

* Spostamento di cassette postali tra server.

## <a name="create-a-new-connector"></a>Creare un nuovo connettore
### <a name="client-software-installation-and-configuration"></a>Installazione e configurazione del software client
Lotus Notes deve essere installato nel server **prima** dell'installazione del connettore.

Quando si esegue l'installazione, assicurarsi di selezionare **Single User Install**. L'opzione predefinita **Multi-User Install** (Installazione multiutente) non funziona.  
![Notes1](./media/active-directory-aadconnectsync-connector-domino/notes1.png)

Nella pagina delle funzionalità installare solo quelle di Lotus Notes richieste e **Client Single Logon** (Accesso singolo client). L'accesso singolo è necessario per consentire al connettore di accedere al server Domino.  
![Notes2](./media/active-directory-aadconnectsync-connector-domino/notes2.png)

**Nota:** avviare Lotus Notes una volta con un utente presente nello stesso server dell'account che viene usato come account del servizio del connettore. Assicurarsi anche di chiudere il client Lotus Notes nel server. Il client non può essere in esecuzione mentre il connettore tenta di connettersi al server Domino.

### <a name="create-connector"></a>Create Connector
Per creare un connettore Lotus Domino, in **Synchronization Service** selezionare **Management Agent** e quindi **Create**. Selezionare il connettore **Lotus Domino (Microsoft)**.  
![CreateConnector](./media/active-directory-aadconnectsync-connector-domino/createconnector.png)

Se la versione del servizio di sincronizzazione offre la possibilità di configurare un valore per **Architecture** (Architettura), assicurarsi che per il connettore sia selezionato il valore predefinito per l'esecuzione in **Process** (Processo).

### <a name="connectivity"></a>Connettività
Nella pagina Connectivity è necessario specificare il nome del server Lotus Domino e immettere le credenziali di accesso.  
![Connettività](./media/active-directory-aadconnectsync-connector-domino/connectivity.png)

La proprietà Domino Server supporta due formati per il nome del server:

* ServerName
* ServerName/DirectoryName

**ServerName/DirectoryName** è il formato preferito per questo attributo, perché consente una risposta più veloce quando il connettore contatta il server Domino.

Il file USerID fornito è archiviato nel database di configurazione del servizio di sincronizzazione.

Per **Delta Import** sono disponibili queste opzioni:

* **None**. Il connettore non esegue importazioni differenziali.
* **Add/Update**. Il connettore esegue l'importazione differenziale, l'aggiunta e l'aggiornamento delle operazioni. Per l'eliminazione è necessaria un'operazione **Full Import** (Importazione completa). Questa operazione usa .Net interop.
* **Add/Update/Delete**. Il connettore esegue l'importazione differenziale, l'aggiunta, l'aggiornamento e l'eliminazione delle operazioni. Questa operazione usa le interfacce C++ native.

In **Schema Options** sono disponibili le opzioni seguenti:

* **Default Schema**. Il connettore rileva lo schema dal server Domino. Questa selezione è l'opzione predefinita.
* **DSML-Schema**. Usata solo se il server Domino non espone lo schema. È quindi possibile creare invece un file DSML con lo schema e importarlo. Per altre informazioni su DSML, vedere [OASIS](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=dsml).

Quando si fa clic su Next, vengono verificati i parametri di configurazione UserID e password.

### <a name="global-parameters"></a>Global Parameters
Nella pagina Global Parameters configurare il fuso orario e l'opzione per l'operazione di importazione ed esportazione.  
![Global Parameters](./media/active-directory-aadconnectsync-connector-domino/globalparameters.png)

Il parametro **Domino Server Time Zone** definisce la posizione del server Domino.

Questa opzione di configurazione è necessaria per supportare le operazioni di **importazione delta** , perché consente al servizio di sincronizzazione di determinare le modifiche tra le ultime due importazioni.

>[!Note]
A partire dall'aggiornamento di marzo 2017, la schermata dei parametri globali include l'opzione di eliminare il database di posta dell'utente durante l'eliminazione dell'utente.

![Eliminare la cassetta postale di un utente](./media/active-directory-aadconnectsync-connector-domino/AdminP.png)

#### <a name="import-settings-method"></a>Impostazioni di importazione, metodo
Per **Perform Full Import By** sono disponibili le opzioni seguenti:

* Search
* View (Recommended)

**Search** (Ricerca) usa l'indicizzazione in Domino, ma è normale che gli indici non siano aggiornati in tempo reale e i dati restituiti dal server non siano sempre corretti. Per un sistema con molte modifiche, questa opzione in genere non funziona bene e fornisce false eliminazioni in alcune situazioni. **Search** è tuttavia più veloce di **View**.

**View** è l'opzione consigliata perché fornisce lo stato corretto dei dati. È leggermente più lenta rispetto a Search.

#### <a name="creation-of-virtual-contact-objects"></a>Creazione di oggetti Virtual Contact
L'impostazione **Enable creation of \_Contact object** include le opzioni seguenti:

* None
* Non-Reference Values
* Reference and Non-Reference Values

In Domino gli attributi di riferimento possono contenere molti formati diversi per fare riferimento ad altri oggetti. Per poter rappresentare diverse varianti, il connettore implementa oggetti \_Contact, anche detti **Virtual Contacts** (Contatti virtuali). Questi oggetti vengono creati in modo che possano essere aggiunti a oggetti MV esistenti o che possano essere proiettati come nuovi oggetti. Ciò consente di conservare i riferimenti agli attributi.

Se questa impostazione è abilitata e se il contenuto di un attributo di riferimento non è un formato di nome distinto, viene creato un oggetto \_Contact. Ad esempio, un attributo member di un gruppo può contenere un indirizzo SMTP. È anche possibile che negli attributi di riferimento siano presenti shortName e altri attributi. Per questo scenario, selezionare **Non-Reference Values**. Questa configurazione rappresenta l'impostazione più comune per le implementazioni di Domino.

Quando Lotus Domino viene configurato con rubriche separate e diversi nomi distinti che rappresentano lo stesso oggetto, è anche possibile creare oggetti \_Contact per tutti i valori di riferimento presenti in una rubrica. Per questo scenario, selezionare l'opzione **Reference and Non-Reference Values** .

Se nell'attributo **FullName** in Domino sono presenti più valori, è consigliabile abilitare anche la creazione di contatti virtuali per la risoluzione dei riferimenti. Ad esempio, questo attributo può avere più valori dopo un matrimonio o un divorzio. Selezionare la casella di controllo **Enable ... FullName has multiple values** per questo scenario.

Unendo gli attributi corretti, gli oggetti \_Contact potranno essere aggiunti all'oggetto MV.

Al DN di questi viene aggiunto VC=\_Contact.

#### <a name="import-settings-conflict-object"></a>Impostazioni di importazione, oggetto conflict
**Exclude Conflict Object**

In un'implementazione di Domino di grandi dimensioni è possibile che più oggetti abbiano lo stesso DN a causa di problemi di replica. In questi casi, il connettore vedrà due oggetti con UniversalID diversi, ma lo stesso DN. Questo conflitto comporta la creazione di un oggetto temporaneo nello spazio del connettore. Il connettore può ignorare gli oggetti selezionati in Domino come vittime della replica. È consigliabile mantenere selezionata questa casella di controllo.

#### <a name="export-settings"></a>Esportazione impostazioni
Se l'opzione **Use AdminP for updating references** (Usa AdminP per aggiornare i riferimenti) non è selezionata, l'esportazione degli attributi di riferimento, ad esempio member, è una chiamata diretta e non usa il processo AdminP. Usare questa opzione solo se AdminP non è stato configurato per mantenere l'integrità referenziale.

#### <a name="routing-information"></a>Informazioni di routing
In Domino è possibile che in un attributo di riferimento siano incorporate informazioni di routing come suffisso del DN. Ad esempio, l'attributo member di un gruppo può contenere **CN=example/organization@ABC**. Il suffisso @ABC si riferisce alle informazioni di routing. Le informazioni di routing vengono usate da Domino per inviare messaggi di posta elettronica al sistema Domino corretto, che può essere un sistema in un'altra organizzazione. Nel campo relativo alle informazioni di routing è possibile specificare i suffissi di routing usati all'interno dell'organizzazione nell'ambito del connettore. Se uno di questi valori viene trovato come suffisso in un attributo di riferimento, le informazioni di routing vengono rimosse dal riferimento. Se non è possibile far corrispondere il suffisso di routing in un valore di riferimento a uno dei valori specificati, viene creato un oggetto \_Contact. Questi oggetti \_Contact vengono creati con **RO=@<RoutingSuffix>** inserito nel DN. Per questi oggetti \_Contact vengono aggiunti anche gli attributi seguenti per consentirne l'unione a un oggetto reale, se necessario: \_routingName, \_contactName, \_displayName e UniversalID.

#### <a name="additional-address-books"></a>Altre rubriche
Se non è installata la funzionalità **Directory Assistance** , che fornisce il nome della rubrica secondaria, è possibile immettere manualmente le rubriche.

#### <a name="multivalued-transformation"></a>Trasformazione multivalore
Molti attributi in Lotus Domino sono di tipo multivalore. Gli attributi del metaverse corrispondenti sono in genere a valore singolo. Se si configura l'opzione relativa all'operazione di importazione ed esportazione, si abilita il connettore alla necessaria conversione degli attributi interessati.

**Export**  
(Esporta) L'opzione relativa all'operazione di esportazione supporta due modalità:

* Append item
* Replace item

**Replace Item** (Sostituisci elemento): quando si seleziona questa opzione, il connettore rimuove sempre i valori correnti dell'attributo in Domino e li sostituisce con i valori forniti. che possono essere a valore singolo o multivalore.

Esempio: l'attributo Assistant di un oggetto person presenta i valori seguenti:

* CN=Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
* CN=John Smith/OU=Contoso/O=Americas,NAB=names.nsf

Se un nuovo assistente di nome **David Alexander** viene assegnato a questo oggetto person, il risultato sarà:

* CN=David Alexander/OU=Contoso/O=Americas,NAB=names.nsf

**Append Item** (Aggiungi elemento): quando si seleziona questa opzione, il connettore mantiene i valori esistenti dell'attributo in Domino e inserisce i nuovi valori nella parte superiore dell'elenco di dati.

Esempio: l'attributo Assistant di un oggetto person presenta i valori seguenti:

* CN=Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
* CN=John Smith/OU=Contoso/O=Americas,NAB=names.nsf

Se un nuovo assistente di nome **David Alexander** viene assegnato a questo oggetto person, il risultato sarà:

* CN=David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
* CN=Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
* CN=John Smith/OU=Contoso/O=Americas,NAB=names.nsf

**Importazioneazione**  
(Importa) L'opzione relativa all'operazione di importazione supporta due modalità:

* Default
* Multivalued to Single Value

**Default** (Predefinito): quando si seleziona l'opzione predefinita, vengono importati tutti i valori di tutti gli attributi.

**Multivalued to Single Value** : quando si seleziona questa opzione, un attributo multivalore viene convertito in un attributo a valore singolo. Se esiste più di un valore, viene usato quello nella parte superiore, che in genere è anche l'ultimo.

Esempio: l'attributo Assistant di un oggetto person presenta i valori seguenti:

* CN=David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
* CN=Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
* CN=John Smith/OU=Contoso/O=Americas,NAB=names.nsf

L'aggiornamento più recente a questo attributo è **David Alexander**. Poiché l'opzione relativa all'operazione Import è impostata su Multivalued to Single Value, il connettore importa solo **David Alexander** nel relativo spazio.

La logica per convertire gli attributi multivalore in attributi a valore singolo non è applicabile all'attributo member di un gruppo e all'attributo fullname di una persona.

È anche possibile configurare regole di trasformazione per l'importazione e l'esportazione per gli attributi multivalore per ogni attributo, come eccezione alla regola globale. Per configurare questa opzione, immettere [tipooggetto].[nomeattributo] nelle caselle di testo **Import Exclusion Attribute List** ed **Export Exclusion Attribute List**. Ad esempio, se si immette Person.Assistant e viene impostato il flag globale per importare tutti i valori, viene importato solo il primo valore per l'assistente.

#### <a name="certifiers"></a>File di certificazione
Tutte le unità organizzative/dell'organizzazione vengono elencate dal connettore. Per poter esportare oggetti person nella rubrica primaria, è necessario un file certificazione con la relativa password.

Se tutti i file di certificazione hanno la stessa password, è possibile usare **Password for all Certifers** (Password per tutti i certificatori). È quindi possibile immettere la password e specificare solo il file di certificazione.

Se si esegue solo l'importazione, non è necessario specificare alcun file di certificazione.

### <a name="configure-provisioning-hierarchy"></a>Configurare una gerarchia di provisioning
Quando si configura il connettore Lotus Domino, ignorare questa pagina della finestra di dialogo, perché questo connettore non supporta il provisioning della gerarchia.  
![Gerarchia di provisioning](./media/active-directory-aadconnectsync-connector-domino/provisioninghierarchy.png)

### <a name="configure-partitions-and-hierarchies"></a>Configurare partizioni e gerarchie
Quando si configurano partizioni e gerarchie, è necessario selezionare la rubrica primaria denominata NAB=names.nsf. Oltre alla rubrica primaria, è possibile selezionare rubriche secondarie, se presenti.  
![Partitions](./media/active-directory-aadconnectsync-connector-domino/partitions.png)

### <a name="select-attributes"></a>Selezionare gli attributi
Quando si configurano gli attributi, è necessario selezionare tutti quelli con prefisso **\_MMS\_**. Questi attributi sono obbligatori quando si effettua il provisioning di nuovi oggetti in Lotus Domino.

![Attributi](./media/active-directory-aadconnectsync-connector-domino/attributes.png)

## <a name="object-lifecycle-management"></a>Gestione del ciclo di vita degli oggetti
Questa sezione fornisce una panoramica dei diversi oggetti disponibili in Domino.

### <a name="person-objects"></a>Oggetti person
L'oggetto person rappresenta gli utenti nell'organizzazione e nelle unità organizzative. Oltre agli attributi predefiniti, l'amministratore di Domino può aggiungere attributi personalizzati a un oggetto Person. Come minimo, un oggetto Person deve includere tutti gli attributi obbligatori. Per un elenco completo degli attributi obbligatori, vedere [Proprietà di Lotus Notes](#lotus-notes-properties). Per registrare un oggetto person, devono essere soddisfatti i prerequisiti seguenti:

* La rubrica (names.nsf) deve essere stata definita ed essere la rubrica primaria.
* È necessario avere l'ID certificazione e la password O/OU per la registrazione di un determinato utente nell'organizzazione o unità organizzativa.
* È necessario definire un set specifico di proprietà di Lotus Notes per un oggetto person. Queste proprietà vengono usate per il provisioning dell'oggetto person. Per informazioni dettagliate, vedere la sezione intitolata [Proprietà di Lotus Notes](#lotus-notes-properties) più avanti in questo documento.
* La password HTTP iniziale per un oggetto person è un attributo e viene impostata durante il provisioning.
* L'oggetto person deve essere uno dei tre tipi supportati seguenti:
  1. Normal User, ha un file di posta elettronica e un file di ID utente
  2. Roaming User, un tipo Normal User che include tutti i file di database mobili
  3. Contact, utente senza file di ID

Gli oggetti person, con l'eccezione dei contatti, possono essere ulteriormente raggruppati in utenti USA e utenti internazionali, come definito dal valore della proprietà \_MMS\_IDRegType. Questi sono gli utenti che usano il client Lotus Notes per accedere ai server Lotus Domino, hanno un ID di Notes e un documento Person. Se usano la posta elettronica di Notes, hanno anche un file di posta elettronica. L'utente deve essere registrato per diventare attivo. Per altre informazioni, vedere:

* [Configurazione degli utenti Lotus Notes](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_SETTING_UP_NOTES_USERS.html)
* [Registrazione degli utenti](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_REGISTERING_USERS.html)
* [Gestione degli utenti](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_USERS_5151.html)
* [Ridenominazione degli utenti](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_USER_AUTOMATICALLY.html)

Tutte queste operazioni vengono eseguite in Lotus Domino e quindi importate nel servizio di sincronizzazione.

### <a name="resources-and-rooms"></a>Risorse e chat
Una risorsa è un altro tipo di database di Lotus Domino. Le risorse possono essere sale riunioni con vari tipi di apparecchiature, ad esempio i proiettori. Esistono sottotipi di risorse supportate dal connettore Lotus Domino definiti in base all'attributo Resource Type:

| Type of Resource | Resource Type Attribute |
| --- | --- |
| Room |1 |
| Resource (Other) |2 |
| Online Meeting |3 |

Per il funzionamento del tipo di oggetto Resource, è necessario quanto segue:

* Il database Resource Reservation deve essere già esistente nel server Domino connesso
* Il sito è già definito per il tipo Resource

Il database Resource Reservation contiene tre tipi di documenti:

* Site Profile
* Risorsa
* Reservation

Per altre informazioni sulla configurazione del database Resource Reservation, vedere l'articolo [Setting up the Resource Reservations database](https://www-01.ibm.com/support/knowledgecenter/SSKTMJ_8.0.1/com.ibm.help.domino.admin.doc/DOC/H_SETTING_UP_THE_RESOURCE_RESERVATIONS_DATABASE.html)(Configurazione del database Resource Reservation).

**Create, Update, and Delete Resources**  
(Crea, aggiorna ed elimina risorse) Le operazioni di creazione, aggiornamento ed eliminazione vengono eseguite dal connettore Lotus Domino nel database Resource Reservation. Le risorse vengono create come documenti in Names.nsf, ovvero la rubrica primaria. Per altri informazioni sulla modifica e l'eliminazione di risorse, vedere [Editing and deleting Resource documents](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_EDITING_AND_DELETING_RESOURCE_DOCUMENTS.html) (Modifica ed eliminazione di documenti di Resource).

**Import and Export operation for Resources**  
(Operazione di importazione ed esportazione per le risorse) Le risorse possono essere importate ed esportate nel servizio di sincronizzazione come qualsiasi altro tipo di oggetto. Selezionare il tipo di oggetto come Resource durante la configurazione. Perché l'operazione di esportazione abbia esito positivo, è necessario avere i dettagli per Resource type, Conference Database e Site name.

### <a name="mail-in-databases"></a>Database di posta elettronica in ingresso
Mail-In Database è un database progettato per ricevere messaggi di posta elettronica. Si tratta di una cassetta postale di Lotus Domino che non è associata a un account utente di Lotus Domino specifico, ovvero non ha un proprio file di ID e password. Un database di posta elettronica in ingresso ha una proprietà UserID univoca ("nome breve") associata ad esso e il proprio indirizzo di posta elettronica.

Se è necessario avere una cassetta postale separata con il proprio indirizzo di posta elettronica che può essere condiviso tra diversi utenti, ad esempio group@contoso.com, viene creato un database di posta elettronica in ingresso. L'accesso a questa cassetta postale è controllato tramite l'elenco di controllo di accesso (ACL), che contiene i nomi degli utenti di Notes ai quali è consentito aprire la cassetta postale.

Per un elenco degli attributi richiesti, vedere la sezione intitolata [Attributi obbligatori](#mandatory-attributes) più avanti in questo articolo.

Quando un database è progettato per ricevere un messaggio di posta elettronica, viene creato un documento Mail-In Database in Lotus Domino. Questo documento deve trovarsi nella Directory Domino di ogni server nel quale è archiviata una copia del database. Per una descrizione dettagliata della creazione di un documento per il database di posta elettronica, vedere [Creating a Mail-In Database document](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_A_MAILIN_DATABASE_DOCUMENT_FOR_A_NEW_DATABASE_OVERVIEW.html)(Creazione di un documento del database di posta elettronica in ingresso).

Prima di creare un Mail-In Database, è necessario che il database, creato dall'amministratore di Lotus, esista già nel server Domino.

### <a name="group-management"></a>Gestione di gruppi
È possibile ottenere una panoramica dettagliata della gestione dei gruppi di Lotus Domino nelle risorse seguenti:

* [Uso di gruppi](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_USING_GROUPS_OVER.html)
* [Creazione di un gruppo](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS_MIDTOPIC_55038956829238418.html)
* [Creazione e modifica dei gruppi](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS.html)
* [Gestione dei gruppi](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_GROUPS_1804.html)
* [Ridenominazione di un gruppo](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_GROUP_STEPS.html)

### <a name="password-management"></a>Gestione delle password
Per un utente di Lotus Domino registrato esistono due tipi di password:

1. Password utente (archiviata nel file User.id)
2. Password Internet/HTTP

Il connettore Lotus Domino supporta solo operazioni con password HTTP.

Per eseguire la gestione delle password, è consigliabile abilitarla per il connettore in Management Agent Designer. Per abilitare la gestione delle password, selezionare **Enable password management** (Abilita la gestione delle password) nella pagina della finestra di dialogo **Configure Extensions** (Configura le estensioni).  
![Configurare le estensioni](./media/active-directory-aadconnectsync-connector-domino/configureextensions.png)

Il connettore Lotus Domino supporta le operazioni seguenti con password Internet:

* Set Password (Imposta password): imposta una nuova password HTTP/Internet per l'utente in Domino. Per impostazione predefinita, l'account viene anche sbloccato. Il flag di sblocco viene esposto nell'interfaccia WMI del motore di sincronizzazione.
* Change Password: in questo scenario è possibile che un utente voglia modificare la password o che gli venga richiesto di cambiare la password dopo un periodo di tempo specificato. Per eseguire questa operazione, è obbligatorio avere la vecchia e la nuova password. Una volta modificata, la nuova password viene aggiornata in Lotus Domino.

Per altre informazioni, vedere:

* [Uso della funzionalità di blocco in Internet](http://www.ibm.com/developerworks/lotus/library/domino8-lockout/)
* [Gestione delle password in Internet](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_NOTES_AND_INTERNET_PASSWORD_SYNCHRONIZATION_7570_OVER.html)

## <a name="reference-information"></a>Informazioni di riferimento
Questa sezione elenca ad esempio le descrizioni degli attributi e i requisiti relativi agli attributi per il connettore Lotus Domino.

### <a name="lotus-notes-properties"></a>Proprietà di Lotus Notes
Quando si effettua il provisioning degli oggetti Person nella directory di Lotus Domino, gli oggetti devono avere un set specifico di proprietà con valori specifici popolati. Questi valori sono necessari solo per le operazioni Create.

La tabella seguente include l'elenco di queste proprietà con la relativa descrizione.

| Proprietà | Description |
| --- | --- |
| \_MMS_AltFullName |Nome completo alternativo dell'utente. |
| \_MMS_AltFullNameLanguage |Lingua da usare per specificare il nome alternativo dell'utente. |
| \_MMS_CertDaysToExpire |Numero di giorni dalla data corrente prima della scadenza del certificato. Se non è specificata, la data predefinita è di due anni dalla data corrente. |
| \_MMS_Certifier |Proprietà che contiene il nome della gerarchia organizzativa del file di certificazione. Ad esempio: OU=OrganizationUnit,O=Org,C=Country. |
| \_MMS_IDPath |Se la proprietà è vuota, nessun file di identificazione utente viene creato localmente nel server di sincronizzazione. Se la proprietà contiene un nome file, viene creato un file di ID utente nella cartella madata. Questa proprietà può contenere anche un percorso completo. |
| \_MMS_IDRegType |Le persone possono essere classificate come contatti, utenti USA e utenti internazionali. La tabella seguente elenca i possibili valori: <li>0 - Contatto</li><li>1 - Utente Stati Uniti</li><li>2 - Utente internazionale</li> |
| \_MMS_IDStoreType |Proprietà obbligatoria per US Users e International Users. La proprietà contiene un valore intero che specifica se l'ID utente viene archiviato come allegato nella rubrica di Notes o nei file di posta elettronica della persona. Se il file dell'ID utente è un allegato nella rubrica, può facoltativamente essere creato come un file con \_MMS_IDPath. <li>Empty (Vuoto): file di ID archiviato nell'insieme di credenziali degli ID, nessun file di identificazione (utilizzato per i contatti).</li><li> 1. Allegato nella rubrica di Lotus Notes. La proprietà \_MMS_Password deve essere configurata per i file di identificazione degli utenti che sono allegati</li><li>2 - Archiviare l'ID nel file di posta elettronica della persona. Il valore \_MMS_UseAdminP deve essere impostato su false per consentire la creazione del file di posta durante la registrazione della persona. La proprietà \_MMS_Password deve essere configurata per i file di identificazione degli utenti.</li> |
| \_MMS_MailQuotaSizeLimit |Numero di megabyte consentiti per il database dei file di posta elettronica. |
| \_MMS_MailQuotaWarningThreshold |Numero di megabyte consentiti per il database dei file di posta elettronica prima che venga generato un avviso. |
| \_MMS_MailTemplateName |File modello di posta elettronica usato per creare il file di posta elettronica dell'utente. Se viene specificato un modello, il file di posta elettronica viene creato con il modello specificato. Se non viene specificato un modello, per creare il file viene usato il file modello predefinito. |
| \_MMS_OU |Proprietà facoltativa che corrisponde al nome dell'unità organizzativa nel file di certificazione. Questa proprietà deve essere vuota per i contatti. |
| \_MMS_Password |Proprietà obbligatoria per gli utenti. La proprietà contiene la password per il file di identificazione dell'oggetto. |
| \_MMS_UseAdminP |Proprietà che deve essere impostata su true se il file di posta elettronica deve essere creato dal processo AdminP nel server Domino (asincrono per il processo di esportazione). Se la proprietà è impostata su false, il file di posta elettronica viene creato con l'utente di Domino (sincrono nel processo di esportazione). |

Per un utente con un file di identificazione associato la proprietà _\_MMS_Password deve contenere un valore. Per l'accesso alla posta elettronica tramite il client Lotus Notes, le proprietà MailServer e MailFile devono contenere un valore.

Per accedere alla posta elettronica tramite un Web browser, le proprietà seguenti devono contenere valori:

* MailFile: proprietà obbligatoria che contiene il percorso del server Lotus Domino in cui è archiviato il file della posta.
* MailServer: proprietà obbligatoria che contiene il nome del server Lotus Domino. Questo valore è il nome da usare quando si crea il file della posta di Lotus sul server Domino.
* HTTPPassword: proprietà facoltativa che contiene la password di accesso Web per l'oggetto.

Per accedere al server Domino senza capacità di posta, è necessario che la proprietà HTTPPassword contenga un valore. La proprietà MailFile e la proprietà MailServer possono essere vuote.

Con \_MMS_ IDStoreType = 2 (ID di archiviazione nel file di posta elettronica) la proprietà MailSystem di NotesRegistrationclass viene impostata su REG_MAILSYSTEM_INOTES (3).

### <a name="mandatory-attributes"></a>Attributi obbligatori
Il connettore Lotus Domino supporta principalmente questi tipi di oggetti (tipi di documento):

* Gruppo
* Mail-In Database
* Person
* Contact (Person senza file di certificazione)
* Risorsa

Questa sezione elenca gli attributi obbligatori per ogni oggetto supportato per l'esportazione in un server Domino.

| Tipo di oggetto | Attributi obbligatori |
| --- | --- |
| Gruppo |<li>ListName</li> |
| Main-In Database |<li>FullName</li><li>MailFile</li><li>MailServer</li><li>MailDomain</li> |
| Person |<li>LastName</li><li>MailFile</li><li>ShortName</li><li>\_MMS_Password</li><li>\_MMS_IDStoreType</li><li>\_MMS_Certifier</li><li>\_MMS_IDRegType</li><li>\_MMS_UseAdminP</li> |
| Contact (Person senza file di certificazione) |<li>\_MMS_IDRegType</li> |
| Risorsa |<li>FullName</li><li>ResourceType</li><li>ConfDB</li><li>ResourceCapacity</li><li>Sito</li><li>displayName</li><li>MailFile</li><li>MailServer</li><li>MailDomain</li> |

## <a name="common-issues-and-questions"></a>Domande e problemi comuni
### <a name="schema-detection-does-not-work"></a>Il rilevamento dello schema non funziona
Per poter rilevare lo schema, è necessario che il file schema.nsf sia presente nel server Domino. Questo file viene visualizzato solo se LDAP è installato nel server. Se lo schema non è rilevabile, verificare quanto segue:

* Il file schema.nsf è presente nella cartella radice del server Domino.
* L'utente ha le autorizzazioni per visualizzare il file schema.nsf.
* Forzare il riavvio del server LDAP. Aprire **Lotus Domino Console** (Console Lotus Domino) e usare il comando **Tell LDAP ReloadSchema** per ricaricare lo schema.

### <a name="not-all-secondary-address-books-are-visible"></a>Non tutte le rubriche secondarie sono visibili
Il connettore Domino si basa sulla funzionalità **Directory Assistance** per trovare le rubriche secondarie. Se mancano le rubriche secondarie, verificare se [Directory Assistance](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_DIRECTORY_ASSISTANCE.html) è stato abilitato e configurato nel server Domino.

### <a name="custom-attributes-in-domino"></a>Attributi personalizzati in Domino
Esistono diverse modalità in Domino per estendere lo schema in modo che venga visualizzato come un attributo personalizzato utilizzabile dal connettore.

**Approccio 1: Estendere lo schema di Lotus Domino**

1. Creare una copia del modello Domino Directory {PUBNAMES.NTF} seguendo [questa procedura](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (non personalizzare il modello IBM Lotus Domino Directory predefinito):
2. Aprire la copia del modello Domino Directory {CONTOSO.NTF} creata in Domino Designer e seguire questa procedura:
   * Fare clic su Shared Elements (Elementi condivisi) ed espandere Subforms (Sottomaschere).
   * Fare doppio clic sulla sottomaschera ${ObjectName}InheritableSchema, dove {ObjectName} è il nome della classe di oggetti strutturali predefinita, ad esempio Person.
   * Specificare il nome dell'attributo da aggiungere allo schema {MyPersonAtrribute} e corrispondente all'attributo specifico. Creare un campo scegliendo **Field** (Campo) dal menu **Create** (Crea).
   * Nel campo aggiunto impostarne le proprietà selezionando il tipo, lo stile, le dimensioni, il carattere e altri parametri correlati nella finestra delle proprietà del campo.
   * Come valore predefinito, mantenere lo stesso nome specificato per quell'attributo. Ad esempio, se il nome dell'attributo è MyPersonAttribute, mantenere lo stesso nome come valore predefinito.
   * Salvare la sottomaschera ${ObjectName}InheritableSchema con i valori aggiornati.
3. Sostituire il modello Directory Domino {PUBNAMES.NTF} con il nuovo modello personalizzato {CONTOSO.NTF} seguendo [questa procedura](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
4. Chiudere Domino Admin e aprire Domino Console per riavviare il servizio LDAP e ricaricare lo schema LDAP:
   * Nella console Domino immettere il comando nel campo di testo **Domino Command** (Comando Domino) per riavviare il servizio LDAP - [Restart Task LDAP](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html).
   * Per ricaricare lo schema LDAP, usare il comando Tell LDAP - Tell LDAP ReloadSchema.
5. Aprire Domino Admin e selezionare la scheda relativa a persone e gruppi per verificare che l'attributo aggiunto sia riportato nella finestra di aggiunta di persone di Domino.
6. Aprire Schema.nsf dalla scheda **Files** e verificare che l'attributo aggiunto sia riportato nella classe di oggetti LDAP dominoPerson.

**Approccio 2: Creare una classe ausiliaria con l'attributo personalizzato e associarla alla classe di oggetti**

1. Creare una copia del modello Domino Directory {PUBNAMES.NTF} seguendo [questa procedura](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html). Non personalizzare in nessun caso il modello IBM Lotus Domino Directory predefinito:
2. Aprire la copia del modello Domino Directory {CONTOSO.NTF} creata in Domino Designer.
3. Nel riquadro sinistro selezionare Shared Code e quindi Subforms.
4. Fare clic su New Subform.
5. Seguire questa procedura per specificare le proprietà per la nuova sottomaschera:
   * Con la nuova sottomaschera aperta scegliere Design - Subform Properties.
   * Accanto alla proprietà Name immettere un nome per la classe di oggetti ausiliaria, ad esempio, TestSubform.
   * Mantenere selezionata la proprietà Options "Include in Insert Subform... dialog".
   * Deselezionare la proprietà Options "Render pass through HTML in Notes".
   * Lasciare le altre proprietà invariate e chiudere la finestra Subform Properties.
   * Salvare e chiudere la nuova sottomaschera.
6. Seguire questa procedura per aggiungere un campo per definire la classe di oggetti ausiliaria:
   * Aprire la sottomaschera creata.
   * Scegliere Create - Field.
   * Accanto a Name nella scheda Basic della finestra di dialogo Field specificare un nome qualsiasi, ad esempio: {MyPersonTestAttribute}.
   * Nel campo aggiunto impostarne le proprietà selezionando il tipo, lo stile, le dimensioni e il tipo di carattere corrispondenti e le proprietà correlate.
   * Come valore predefinito, mantenere lo stesso nome specificato per quell'attributo. Ad esempio, se il nome dell'attributo è MyPersonTestAttribute, mantenere lo stesso nome come valore predefinito.
   * Salvare la sottomaschera con i valori aggiornati e seguire questa procedura:
     * Nel riquadro sinistro selezionare Shared Code e quindi Subforms.
     * Selezionare la nuova sottomaschera e scegliere Design - Design Properties.
     * Fare clic sulla terza scheda da sinistra e selezionare **Propagate this prohibition of design change**.
7. Aprire la sottomaschera ${ObjectName}ExtensibleSchema, dove {ObjectName} è il nome della classe di oggetti strutturali predefinita, ad esempio, Person.
8. Immettere Resource e selezionare la sottomaschera creata, ad esempio TestSubform, e salvare la sottomaschera ${ObjectName}ExtensibleSchema.
9. Sostituire il modello Directory Domino {PUBNAMES.NTF} con il nuovo modello personalizzato {CONTOSO.NTF} seguendo [questa procedura](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
10. Chiudere Domino Admin e aprire Domino Console per riavviare il servizio LDAP e ricaricare lo schema LDAP:
    * Nella console Domino immettere il comando nel campo di testo **Domino Command** (Comando Domino) per riavviare il servizio LDAP - [Restart Task LDAP](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html).
    * Per ricaricare lo schema LDAP, usare il comando Tell LDAP **Tell LDAP ReloadSchema**.
11. Aprire Domino Admin e selezionare la scheda relativa a persone e gruppi per verificare che l'attributo aggiunto sia riportato nella scheda relativa alle altre opzioni della finestra di aggiunta di persone di Domino.
12. Aprire Schema.nsf dalla scheda **Files** e vedere che l'attributo aggiunto è riportato nella classe di oggetti ausiliaria LDAP TestSubform.

**Approccio 3: Aggiungere l'attributo personalizzato alla classe ExtensibleObject**

1. Aprire il file {Schema.nsf} inserito nella directory radice.
2. Selezionare LDAP Object Classes (Classi di oggetti LDAP) dal menu a sinistra in **All Schema Documents** (Tutti i documenti dello schema) e fare clic sul pulsante **Add Object class** (Aggiungi classe di oggetti):
3. Specificare il nome LDAP con formato {zzzSchemaEstensibile}, dove zzz è il nome della classe di oggetti strutturali predefinita, ad esempio Person. Ad esempio, per estendere lo schema per la classe di oggetti Person, fornire il nome LDAP {PersonExtensibleSchema}.
4. Fornire il nome della classe Superior Object per cui si vuole estendere lo schema. Ad esempio, per estendere lo schema per la classe di oggetti Person, fornire il nome della classe Superior Object {dominoPerson}:
5. Specificare un OID valido corrispondente alla classe di oggetti.
6. Selezionare gli attributi estesi/personalizzati nei campi relativi ai tipi di attributo Mandatory oppure Optional in base al requisito:
7. Dopo aver aggiunto gli attributi obbligatori a ExtensibleObjectClass, fare clic su **Save & Close** (Salva e chiudi).
8. Viene creata una ExtensibleObjectClass per la rispettiva classe di oggetti predefinita con attributi estesi.

## <a name="troubleshooting"></a>Risoluzione dei problemi
* Per informazioni su come abilitare la registrazione per risolvere i problemi relativi al connettore, vedere l'articolo relativo a [come abilitare la traccia ETW per i connettori](http://go.microsoft.com/fwlink/?LinkId=335731).

