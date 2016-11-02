<properties
   pageTitle="Connettore Generic LDAP | Microsoft Azure"
   description="Questo articolo descrive come configurare il connettore Generic LDAP di Microsoft."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/30/2016"
   ms.author="billmath"/>


# <a name="generic-ldap-connector-technical-reference"></a>Documentazione tecnica sul connettore Generic LDAP
Questo articolo descrive il connettore Generic LDAP ed è applicabile ai prodotti seguenti:

- Microsoft Identity Manager 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   È necessario usare l'hotfix 4.1.3671.0 o versione successiva ( [KB3092178](https://support.microsoft.com/kb/3092178)).

Per MIM2016 e FIM2010R2 il connettore è disponibile come download dall' [Area download Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

Quando si fa riferimento alle RFC IETF, questo documento usa il formato (RFC [numero RFC]/[sezione nel documento RFC]), ad esempio, (RFC 4512/4.3).
Altre informazioni sono disponibili all'indirizzo http://tools.ietf.org/html/rfc4500 (è necessario sostituire 4500 con il numero di RFC corretto).

## <a name="overview-of-the-generic-ldap-connector"></a>Panoramica del connettore Generic LDAP
Il connettore Generic LDAP consente di integrare il servizio di sincronizzazione con il server LDAP v3.

Determinate operazioni e gli elementi dello schema, ad esempio quelli necessari per eseguire l'importazione delta, non sono specificati nelle RFC IETF. Per queste operazioni sono supportate solo le directory LDAP specificate in modo esplicito.

A livello generale, le funzionalità seguenti sono supportate dalla versione corrente del connettore:

Funzionalità | Supporto
--- | --- |
Origine dati connessa | Il connettore è supportato con i server LDAP v3 (conforme a RFC 4510). È stato testato con quanto segue:  <li>Microsoft Active Directory Lightweight Directory Services (AD LDS)</li><li>Catalogo globale Microsoft Active Directory</li><li>389 Directory Server</li><li>Apache Directory Server</li><li>IBM Tivoli DS</li><li>Isode Directory</li><li>NetIQ eDirectory</li><li>Novell eDirectory</li><li>Open DJ</li><li>Open DS</li><li>Open LDAP (openldap.org)</li><li>Oracle (in precedenza Sun) Directory Server Enterprise Edition</li><li>RadiantOne Virtual Directory Server (VDS)</li><li>Sun One Directory Server</li>**Directory rilevanti non supportate:** <li>Servizi di dominio Microsoft Active Directory (AD DS) [Usare invece Active Directory Connector]</li><li>Oracle Internet Directory (OID)</li>
Scenari   | <li>Gestione del ciclo di vita degli oggetti</li><li>Gestione di gruppi</li><li>Gestione delle password</li>
Operazioni |Le operazioni seguenti sono supportate in tutte le directory LDAP:  <li>Importazione completa</li><li>Esportazione</li>Le operazioni seguenti sono supportate solo nelle directory specificate:<li>Importazione delta</li><li>Imposta password, Cambia password</li>
Schema | <li>Lo schema viene rilevato dallo schema LDAP (RFC3673 e RFC4512/4.2)</li><li>Supporta classi strutturali, classi aux e la classe di oggetti extensibleObject (RFC4512/4.3)</li>

### <a name="delta-import-and-password-management-support"></a>Supporto per l'importazione delta e la gestione delle password
Directory supportate per l'importazione delta e la gestione delle password:

- Microsoft Active Directory Lightweight Directory Services (AD LDS)
    - Supporta tutte le operazioni per l'importazione delta
    - Supporta l'impostazione della password
- Catalogo globale Microsoft Active Directory
    - Supporta tutte le operazioni per l'importazione delta
    - Supporta l'impostazione della password
- 389 Directory Server
    - Supporta tutte le operazioni per l'importazione delta
    - Supporta l'impostazione e la modifica delle password
- Apache Directory Server
    - Non supporta l'importazione delta perché questa directory non ha un registro delle modifiche persistenti
    - Supporta l'impostazione della password
- IBM Tivoli DS
    - Supporta tutte le operazioni per l'importazione delta
    - Supporta l'impostazione e la modifica delle password
- Isode Directory
    - Supporta tutte le operazioni per l'importazione delta
    - Supporta l'impostazione e la modifica delle password
- Novell eDirectory e NetIQ eDirectory
    - Supporta le operazioni di aggiunta, aggiornamento e ridenominazione per l'importazione delta
    - Non supporta le operazioni di eliminazione per l'importazione delta
    - Supporta l'impostazione e la modifica delle password
- Open DJ
    - Supporta tutte le operazioni per l'importazione delta
    - Supporta l'impostazione e la modifica delle password
- Open DS
    - Supporta tutte le operazioni per l'importazione delta
    - Supporta l'impostazione e la modifica delle password
- Open LDAP (openldap.org)
    - Supporta tutte le operazioni per l'importazione delta
    - Supporta l'impostazione della password
    - Non supporta la modifica della password
- Oracle (in precedenza Sun) Directory Server Enterprise Edition
    - Supporta tutte le operazioni per l'importazione delta
    - Supporta l'impostazione e la modifica delle password
- RadiantOne Virtual Directory Server (VDS)
    - È necessario usare la versione 7.1.1 o successiva
    - Supporta tutte le operazioni per l'importazione delta
    - Supporta l'impostazione e la modifica delle password
-  Sun One Directory Server
    - Supporta tutte le operazioni per l'importazione delta
    - Supporta l'impostazione e la modifica delle password

### <a name="prerequisites"></a>Prerequisiti
Prima di usare il connettore, verificare che nel server di sincronizzazione sia disponibile quanto segue:

- Microsoft .NET 4.5.2 Framework o versione successiva

### <a name="detecting-the-ldap-server"></a>Rilevamento del server LDAP
Il connettore si basa su varie tecniche per rilevare e identificare il server LDAP. Il connettore usa Root DSE, il nome del fornitore e la versione e controlla lo schema per trovare oggetti univoci e attributi la cui esistenza è nota in alcuni server LDAP. Questi dati, se trovati, vengono usati per popolare le opzioni di configurazione del connettore.

### <a name="connected-data-source-permissions"></a>Autorizzazioni dell'origine dati connessa
Per eseguire le operazioni di importazione ed esportazione degli oggetti nella directory connessa, l'account del connettore deve avere autorizzazioni sufficienti. Il connettore deve avere autorizzazioni di scrittura per poter esportare e autorizzazioni di lettura per importare. La configurazione delle autorizzazioni viene eseguita nelle esperienze di gestione della directory di destinazione stessa.

### <a name="ports-and-protocols"></a>Porte e protocolli
Il connettore usa il numero di porta specificato nella configurazione, che per impostazione predefinita è 389 per LDAP e 636 per LDAPS.

Per LDAPS è necessario usare SSL 3.0 o TLS. SSL 2.0 non è supportato e non può essere attivato.

### <a name="required-controls-and-features"></a>Funzionalità e controlli necessari
I controlli e le funzionalità LDAP seguenti devono essere disponibili nel server LDAP per il funzionamento corretto del connettore:   
`1.3.6.1.4.1.4203.1.5.3` Filtri True/False

Il filtro True/False spesso non viene segnalato come supportato dalle directory LDAP e potrebbe essere visualizzato nella **Global Page** in **Mandatory Features Not Found**. Viene usato per creare filtri **OR** nelle query LDAP, ad esempio quando si importano più tipi di oggetto. Se è possibile importare più di un tipo di oggetto, il server LDAP supporta questa funzionalità.

Se si usa una directory in cui un identificatore univoco è l'ancoraggio, dovrà essere disponibile anche quanto segue (per altre informazioni, vedere la sezione [Configurare gli ancoraggi](#configure-anchors) più avanti in questo articolo):  
`1.3.6.1.4.1.4203.1.5.1` Tutti gli attributi operativi

Se la directory ha più oggetti di quanti sia possibile inserirne in una sola chiamata alla directory, è consigliabile usare il paging. Per il funzionamento del paging, è necessaria una delle opzioni seguenti:

**Opzione 1:**  
`1.2.840.113556.1.4.319` pagedResultsControl

**Opzione 2:**  
`2.16.840.1.113730.3.4.9` VLVControl  
`1.2.840.113556.1.4.473` SortControl

Se entrambe le opzioni sono abilitate nella configurazione del connettore, viene usato pagedResultsControl.

`1.2.840.113556.1.4.417` ShowDeletedControl

ShowDeletedControl viene usato solo con il metodo di importazione delta USNChanged per poter visualizzare gli oggetti eliminati.

Il connettore prova a rilevare le opzioni presenti nel server. Se le opzioni non possono essere rilevate, viene visualizzato un avviso nella pagina globale delle proprietà del connettore. Non tutti i server LDAP presentano tutti i controlli o le funzionalità che supportano e anche se è presente questo avviso, il connettore potrebbe funzionare senza problemi.

### <a name="delta-import"></a>Importazione delta
L'importazione delta è disponibile solo quando è stata rilevata una directory di supporto. Attualmente vengono usati i metodi seguenti:

- LDAP Accesslog. Vedere [http://www.openldap.org/doc/admin24/overlays.html#Access Logging](http://www.openldap.org/doc/admin24/overlays.html#Access Logging)
- LDAP Changelog. Vedere [http://tools.ietf.org/html/draft-good-ldap-changelog-04](http://tools.ietf.org/html/draft-good-ldap-changelog-04)
- TimeStamp. Per Novell/NetIQ eDirectory il connettore usa l'ultima data/ora per ottenere gli oggetti creati e aggiornati. Novell/NetIQ eDirectory non fornisce un modo equivalente per  recuperare gli oggetti eliminati. Questa opzione può essere usata anche se nessun altro metodo di importazione delta è attivo nel server LDAP. Questa opzione non consente di importare gli oggetti eliminati.
- USNChanged. Vedere: [https://msdn.microsoft.com/library/ms677627.aspx](https://msdn.microsoft.com/library/ms677627.aspx)

### <a name="not-supported"></a>Non supportate
Le funzionalità LDAP seguenti non sono supportate:

- Riferimenti LDAP tra server (RFC 4511/4.1.10)

## <a name="create-a-new-connector"></a>Creare un nuovo connettore
Per creare un connettore Generic LDAP, in **Synchronization Service** selezionare **Management Agent** e quindi **Create**. Selezionare il connettore **Generic LDAP (Microsoft)** .

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericldap/createconnector.png)

### <a name="connectivity"></a>Connettività
Nella pagina Connectivity (Connettività) è necessario specificare le informazioni relative a host, porta e binding. A seconda del valore di Binding selezionato, potrebbero essere fornite altre informazioni nelle sezioni seguenti.

![Connettività](./media/active-directory-aadconnectsync-connector-genericldap/connectivity.png)

- L'impostazione Connection Timeout viene usata solo per la prima connessione al server quando viene rilevato lo schema.
- Se il valore di Binding è Anonymous, non vengono usati né nome utente/password né il certificato.
- Per gli altri valori di Binding immettere le informazioni in nome utente/password o selezionare un certificato.
- Se si usa Kerberos per l'autenticazione, fornire anche le informazioni per l'area autenticazione o il dominio dell'utente.

La casella di testo **Attribute Aliases** viene usata per gli attributi definiti nello schema con la sintassi RFC4522. Questi attributi non possono essere rilevati durante il rilevamento dello schema ed è necessario l'intervento dell'utente per consentire al connettore di identificarli. Ad esempio, è necessario immettere quanto segue nella casella Attribute Aliases per identificare correttamente l'attributo userCertificate come attributo binario:

`userCertificate;binary`

Di seguito è riportato un esempio del possibile aspetto di questa configurazione:

![Connettività](./media/active-directory-aadconnectsync-connector-genericldap/connectivityattributes.png)

Selezionare la casella di controllo **Include operational attributes in schema** per includere anche gli attributi creati dal server. Sono d esempio inclusi gli attributi relativi all'ora di creazione e dell'ultimo aggiornamento dell'oggetto.

Selezionare **Include extensible attributes in schemA** (Includi attributi estensibili nello schema) se vengono usati oggetti estensibili (RFC4512/4.3). L'abilitazione di questa opzione consente l'uso di tutti gli attributi in tutti gli oggetti. Se si seleziona questa opzione, le dimensioni dello schema diventano molto estese, quindi a meno che la directory connessa non usi questa funzionalità, è consigliabile lasciare deselezionata l'opzione.

### <a name="global-parameters"></a>Global Parameters
Nella pagina Global Parameters configurare il DN per il log delle modifiche delta e altre funzionalità LDAP. La pagina è già popolata con le informazioni fornite dal server LDAP.

![Connettività](./media/active-directory-aadconnectsync-connector-genericldap/globalparameters.png)

La sezione superiore mostra le informazioni fornite dal server, ad esempio il nome del server. Il connettore verifica anche che i controlli obbligatori siano presenti in Root DSE. Se questi controlli non sono elencati, viene visualizzato un avviso. Alcune directory LDAP non elencano tutte le funzioni in Root DSE ed è possibile che il connettore funzioni senza problemi anche se è presente un avviso.

Le caselle di controllo **Supported Controls** (Controlli supportati) controllano il comportamento di alcune operazioni:

- Se si seleziona l'eliminazione di un albero, viene eliminata una gerarchia con una chiamata LDAP. Se si deseleziona l'eliminazione di un albero, il connettore esegue un'operazione di eliminazione ricorsiva, se necessario.
- Se si selezionano i risultati di paging, il connettore esegue un'importazione con paging usando le dimensioni specificate nei passaggi di esecuzione.
- VLVControl e SortControl sono un'alternativa a pagedResultsControl per leggere i dati dalla directory LDAP.
- Se tutte e tre le opzioni (pagedResultsControl, VLVControl e SortControl) sono deselezionate, il connettore importa tutti gli oggetti in una sola operazione, che potrebbe non riuscire se la directory è di grandi dimensioni.
- ShowDeletedControl viene usata solo quando il metodo di importazione delta è USNChanged.

Il DN del log delle modifiche è il contesto dei nomi usato dal log delle modifiche differenziali, ad esempio **cn=changelog**. Questo valore deve essere specificato per poter eseguire l'importazione differenziale.

Di seguito è riportato un elenco di DN del log delle modifiche predefiniti:

Directory | Log delle modifiche delta
--- | ---
Microsoft AD LDS e Catalogo globale Active Directory | Rilevato automaticamente. USNChanged.
Apache Directory Server | Non disponibile.
Directory 389 | Log delle modifiche. Valore predefinito da usare: **cn=changelog**
IBM Tivoli DS | Log delle modifiche. Valore predefinito da usare: **cn=changelog**
Isode Directory | Log delle modifiche. Valore predefinito da usare: **cn=changelog**
Novell/NetIQ eDirectory | Non disponibile. TimeStamp. Il connettore usa data/ora dell'ultimo aggiornamento per ottenere i record aggiunti e aggiornati.
Open DJ/DS | Log delle modifiche.  Valore predefinito da usare: **cn=changelog**
Open LDAP | Log di accesso. Valore predefinito da usare: **cn=accesslog**
Oracle DSEE | Log delle modifiche. Valore predefinito da usare: **cn=changelog**
RadiantOne VDS | Directory virtuale. Dipende dalla directory connessa a VDS.
Sun One Directory Server | Log delle modifiche. Valore predefinito da usare: **cn=changelog**

L'attributo password è il nome dell'attributo che il connettore dovrà usare per impostare la password nelle operazioni di modifica e impostazione della password.
Per impostazione predefinita, questo valore è **userPassword** , ma può essere modificato quando necessario per un particolare sistema LDAP.

Nell'elenco delle partizioni aggiuntive è possibile aggiungere altri spazi dei nomi non rilevati automaticamente. Questa impostazione può essere ad esempio usata se diversi server costituiscono un cluster logico e devono quindi essere importati contemporaneamente. Così come Active Directory può includere più domini in una foresta, ma tutti i domini condividono uno schema, lo stesso approccio può essere simulato immettendo gli spazi dei nomi aggiuntivi in questa casella. Ogni spazio dei nomi può eseguire l'importazione da diversi server e viene configurato anche nella pagina di configurazione delle partizioni e delle gerarchie. Usare CTRL+INVIO per ottenere una nuova riga.

### <a name="configure-provisioning-hierarchy"></a>Configurare una gerarchia di provisioning
Questa pagina viene usata per associare il componente DN, ad esempio OU, al tipo di oggetto di cui deve essere eseguito il provisioning, ad esempio organizationalUnit.

![Gerarchia di provisioning](./media/active-directory-aadconnectsync-connector-genericldap/provisioninghierarchy.png)

Configurando una gerarchia di provisioning, è possibile configurare il connettore per creare automaticamente una struttura quando necessario. Ad esempio, se è presente uno spazio dei nomi dc=contoso,dc=com e viene eseguito il provisioning di un nuovo oggetto cn=Joe, ou=Seattle, c=US, dc=contoso, dc=com, il connettore può creare un oggetto di tipo paese per US e un'unità organizzativa (organizationalUnit) per Seattle, se non sono già presenti nella directory.

### <a name="configure-partitions-and-hierarchies"></a>Configurare partizioni e gerarchie
Nella pagina Configure Partitions and Hierarchies selezionare tutti gli spazi dei nomi con gli oggetti da importare ed esportare.

![Partitions](./media/active-directory-aadconnectsync-connector-genericldap/partitions.png)

Per ogni spazio dei nomi è anche possibile configurare le impostazioni di connettività che sostituiscono i valori specificati nella schermata Connectivity (Connettività). Se questi valori vengono lasciati sul valore vuoto predefinito, vengono usate le informazioni dalla schermata Connectivity (Connettività).

È anche possibile selezionare i contenitori e le OU in cui il connettore deve esportare e importare.

### <a name="configure-anchors"></a>Configurare gli ancoraggi
Questa pagina ha  sempre un valore preconfigurato e non può essere modificato. Se il fornitore del server è stato identificato, l'ancoraggio potrebbe essere popolato con un attributo non modificabile, ad esempio, il GUID di un oggetto. Se non è stato rilevato o non ha un attributo non modificabile, il connettore usa un DN (nome distinto) come ancoraggio.

![ancoraggi](./media/active-directory-aadconnectsync-connector-genericldap/anchors.png)

Di seguito è riportato un elenco di server LDAP e l' ancoraggio usato:

Directory | Attributo di ancoraggio
--- | ---
Microsoft AD LDS e Catalogo globale Active Directory | objectGUID
389 Directory Server | dn
Apache Directory | dn
IBM Tivoli DS | dn
Isode Directory | dn
Novell/NetIQ eDirectory | GUID
Open DJ/DS | dn
Open LDAP | dn
Oracle ODSEE | dn
RadiantOne VDS | dn
Sun One Directory Server | dn

## <a name="other-notes"></a>Altre note
Questa sezione fornisce informazioni di aspetti specifici di questo connettore o che, per altri motivi, è importante conoscere.

### <a name="delta-import"></a>Importazione delta
La filigrana delta in Open LDAP è la data/ora UTC. Per questo motivo, è necessario sincronizzare gli orologi tra il servizio di sincronizzazione FIM e Open LDAP. In caso contrario, potrebbero essere omesse alcune voci nel log delle modifiche delta.

Per Novell eDirectory l'importazione differenziale non rileva le eliminazioni di oggetti. Per questo motivo è necessario eseguire un'importazione completa periodicamente per trovare tutti gli oggetti eliminati.

Per le directory con un log delle modifiche differenziali basato su data/ora, è consigliabile eseguire un'importazione completa a intervalli regolari. Questo processo consente al motore di sincronizzazione di trovare eventuali differenze tra il server LDAP e gli elementi attualmente presenti nello spazio del connettore.

## <a name="troubleshooting"></a>Risoluzione dei problemi

-   Per informazioni su come abilitare la registrazione per risolvere i problemi relativi al connettore, vedere l'articolo relativo a [come abilitare la traccia ETW per i connettori](http://go.microsoft.com/fwlink/?LinkId=335731).



<!--HONumber=Oct16_HO2-->


