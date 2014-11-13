<properties urlDisplayName="Provision BizTalk Services in management portal" pageTitle="Creare Servizi BizTalk nel portale di gestione | Azure" metaKeywords="Get started Azure biztalk services, provision, Azure unstructured data" description="Informazioni su come effettuare il provisioning di un servizio BizTalk nel portale di gestione di Azure e su come creare un account di archiviazione e un server di database SQL facoltativo." metaCanonical="http://www.windowsazure.com/it-it/manage/services/biztalk-services/provisioning-biztalk-service" services="biztalk-services" documentationCenter="" title="Servizi BizTalk: Provisioning tramite il portale di gestione di Azure" authors="mandia" solutions="" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia" />

# Creare un servizio BizTalk mediante il portale di gestione di Azure

Questo argomento descrive i passaggi per creare un servizio BizTalk di Azure nel portale di gestione di Azure. In particolare:

-   [Creare un servizio BizTalk][Creare un servizio BizTalk]
-   [Passaggi di post-provisioning][Passaggi di post-provisioning]
-   [Descrizione dei requisiti][Descrizione dei requisiti]
-   [Connessioni ibride - Nuovo][Connessioni ibride - Nuovo]

<div class="dev-callout">

**Suggerimento**
Per accedere al portale di gestione di Azure, è necessario un account Azure e una sottoscrizione di Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Vedere [Versione di valutazione gratuita di Azure][Versione di valutazione gratuita di Azure].

</div>

## <a name="BizTalk"></a>Creare un servizio BizTalk

A seconda dell'edizione scelta, non tutte le impostazioni del servizio BizTalk Service saranno disponibili.

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Nella parte inferiore del pannello di navigazione selezionare **NUOVO**:
    ![Select the New button][Select the New button]

3.  Selezionare **SERVIZI APP** \> **SERVIZIO BIZTALK** \> **CREAZIONE PERSONALIZZATA**:
    ![Select BizTalk Service and select Custom Create][Select BizTalk Service and select Custom Create]

4.  Immettere le impostazioni del servizio BizTalk.

    |-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Nome del servizio BizTalk** | È possibile inserire qualsiasi nome, ma deve essere specifico. Di seguito sono riportati alcuni esempi:                                                                                                                                                                                       
                                      *società* .biztalk.windows.net                                                                                                                                                                                                                                                                
                                      *societàapplicazione*.biztalk.windows.net                                                                                                                                                                                                                                                     
                                      *applicazione* .biztalk.windows.net                                                                                                                                                                                                                                                           
                                      ".biztalk.windows.net" viene aggiunto automaticamente al nome digitato. Viene creato un URL usato per accedere al servizio BizTalk, ad esempio **https://*applicazione*.biztalk.windows.net**.                                                                                                |
    | **Edizione**                  | Se si è nella fase di testing/sviluppo, scegliere l'edizione **Developer**. Se si è nella fase di produzione, usare [Servizi BizTalk: Tabella delle edizioni][Servizi BizTalk: Tabella delle edizioni] per determinare se la scelta corretta per il proprio scenario aziendale sia l'edizione **Premium**, **Standard** o **Basic**. |
    | **Area**                      | Selezionare l'area geografica per ospitare il servizio BizTalk.                                                                                                                                                                                                                               |
    | **URL del dominio**           | **Facoltativo**. Per impostazione predefinita, l'URL del dominio è *NomeServizioBizTalk*.biztalk.windows.net. È tuttavia possibile specificare un dominio personalizzato. Se ad esempio il dominio è *contoso*, è possibile immettere:                                                        
                                      *Società*.contoso.com                                                                                                                                                                                                                                                                         
                                      *SocietàApplicazione*.contoso.com                                                                                                                                                                                                                                                             
                                      *Applicazione*.contoso.com                                                                                                                                                                                                                                                                    
                                      *NomeServizioBizTalk*.contoso.com                                                                                                                                                                                                                                                             |

    Fare clic sulla freccia AVANTI.

5.  Immettere le impostazioni per archiviazione e database:

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>Account di monitoraggio/archiviazione</strong></td>
    <td align="left">Selezionare un account di archiviazione esistente o scegliere di crearne uno nuovo.<br /><br /> Se si crea un nuovo account di archiviazione, specificarne il nome in <strong>Nome account di archiviazione</strong>.</td>
    </tr>
    <tr class="even">
    <td align="left"><strong>Database di rilevamento</strong></td>
    <td align="left">Se si usa un database SQL di Azure esistente, non potrà essere usato da un altro servizio BizTalk. È necessario disporre dell'account di accesso e della password immessi durante la creazione del server di database SQL di Azure.<br /><br />
    <div class="dev-callout">
    <strong>Suggerimento</strong>
    <p>Creare il database di rilevamento e l'account di monitoraggio/archiviazione nella stessa area geografica del servizio BizTalk.</p>
    </div></td>
    </tr>
    </tbody>
    </table>

    Fare clic sulla freccia AVANTI.

6.  Immettere le impostazioni del database:

    |-----------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Nome**                          | Disponibile quando nella schermata precedente è stata selezionata l'opzione **Crea nuova istanza di database SQL**.                                  
                                          Immettere il nome del database SQL da usare con il servizio BizTalk.                                                                                 |
    | **Server**                        | Disponibile quando nella schermata precedente è stata selezionata l'opzione **Crea nuova istanza di database SQL**.                                  
                                          Selezionare un server di database SQL o crearne uno nuovo.                                                                                           |
    | **Nome di accesso al server**     | Immettere il proprio nome utente di accesso.                                                                                                         |
    | **Password di accesso al server** | Immettere la password di accesso.                                                                                                                    |
    | **Area**                          | Disponibile quando è stata selezionata l'opzione **Crea nuova istanza di database SQL**. Selezionare l'area geografica per ospitare il database SQL. |

Per completare la procedura guidata, fare clic sul segno di spunta. Icona di avanzamento visualizzata:
![Progress icon displays when complete][Progress icon displays when complete]

Al termine, viene creato il servizio BizTalk di Azure che sarà disponibile per le applicazioni. Le impostazioni predefinite sono sufficienti. Se si desidera modificare le impostazioni predefinite, selezionare **SERVIZI BIZTALK** nel pannello di navigazione sinistro e selezionare il proprio servizio BizTalk. Altre impostazioni sono disponibili nelle [schede Dashboard, Monitoraggio e Scala][schede Dashboard, Monitoraggio e Scala] in alto.

A seconda dello stato del servizio BizTalk, alcune operazioni potrebbero non essere completate. Per un elenco di queste operazioni, vedere [Servizi BizTalk: Tabella degli stati del servizio][Servizi BizTalk: Tabella degli stati del servizio].

## <a name="PostProv"></a>Passaggi di post-provisioning

-   [Installare il certificato in un computer locale][Installare il certificato in un computer locale]
-   [Aggiungere un certificato per l'ambiente di produzione][Aggiungere un certificato per l'ambiente di produzione]
-   [Ottenere lo spazio dei nomi ACS][Ottenere lo spazio dei nomi ACS]

#### <a name="InstallCert"></a>Installare il certificato in un computer locale

Come parte del provisioning del servizio BizTalk, un certificato autofirmato viene creato e associato alla sottoscrizione del servizio BizTalk. È necessario scaricare questo certificato e installarlo nei computer da cui si distribuiscono applicazioni del servizio BizTalk o si inviano messaggi all'endpoint del servizio BizTalk.

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Fare clic su **SERVIZI BIZTALK** nel pannello di navigazione sinistro e quindi selezionare la sottoscrizione del servizio BizTalk.
3.  Fare clic sulla scheda **Dashboard**.
4.  Click **Scarica certificato SSL**.
    ![Modify SSL Certificate][Modify SSL Certificate]
5.  Fare doppio clic sul certificato ed eseguire la procedura guidata per installarlo. Assicurarsi di installare il certificato nell'archivio **Autorità di certificazione radice attendibili**.

#### <a name="AddCert"></a>Aggiungere un certificato per l'ambiente di produzione

Il certificato autofirmato creato automaticamente durante il provisioning di Servizi BizTalk è destinato all'uso solo negli ambienti di sviluppo. Per gli scenari di produzione, deve essere sostituito con un certificato di produzione.

1.  Nella scheda **Dashboard** fare clic su **Aggiorna certificato SSL**.
2.  Cercare il certificato SSL privato (*NomeCertificato*.pfx) che include il nome del servizio BizTalk, immettere la password e selezionare il segno di spunta.

#### <a name="ACS"></a>Ottenere lo spazio dei nomi ACS

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Selezionare **SERVIZI BIZTALK** nel pannello di navigazione sinistro e quindi scegliere il servizio BizTalk.
3.  Sulla barra delle applicazioni selezionare**Informazioni di connessione**:
    ![Select Connection Information][Select Connection Information]

4.  Copiare i valori di ACS.

Quando si distribuisce un progetto di servizio BizTalk da Visual Studio, si inserisce questo spazio dei nomi. Lo spazio dei nomi del servizio di controllo di accesso viene creato automaticamente per il servizio BizTalk.

I valori di ACS possono essere usati con qualsiasi applicazione. Dopo la creazione di Servizi BizTalk di Azure, lo spazio del nomi ACS controlla l'autenticazione con la distribuzione del servizio BizTalk. Se si desidera modificare la sottoscrizione o gestire lo spazio dei nomi, selezionare **ACTIVE DIRECTORY** nel pannello di navigazione sinistro e quindi selezionare lo spazio dei nomi. Le opzioni sono elencate nella bassa delle applicazioni.

Facendo clic su **Gestisci** è possibile aprire il portale di gestione del servizio di controllo di accesso. Nel portale di gestione di Access Control, il servizio BizTalk Service usa le **identità del servizio**:
![ACS Service Identities in the Access Control Management Portal][ACS Service Identities in the Access Control Management Portal]

Le identità del servizio di controllo di accesso sono set di credenziali che consentono ad applicazioni o client di eseguire l'autenticazione direttamente con il servizio di controllo di accesso e di ricevere un token.

**Importante**
 Il servizio BizTalk usa **Owner** come identità predefinita del servizio e il valore **Password**. Se si usa il valore della chiave simmetrica anziché il valore della password, potrebbe verificarsi l'errore seguente:

*Could not connect to the Access Control Management Service account with the specified credentials*

In [Gestione dello spazio dei nomi del servizio di controllo di accesso][Gestione dello spazio dei nomi del servizio di controllo di accesso] sono fornite alcune linee guida e consigli utili.

## <a name="Requirements"></a>Descrizione dei requisiti

Questi requisiti non si applicano all'edizione Free.

<table border="1">

<tr bgcolor="FAF9F9">

<td>
**Elementi necessari**

</td>

<td>
**Perché sono necessari**

</td>

</tr>

<tr>

<td>
Sottoscrizione di Azure

</td>

<td>
La sottoscrizione determina chi può accedere al portale di gestione di Azure. Il titolare dell'account crea la sottoscrizione nell'area relativa alle [sottoscrizioni Azure][sottoscrizioni Azure].
L'account Azure può avere più sottoscrizioni e può essere gestito da chiunque sia abilitato. Il titolare di un account Azure crea ad esempio una sottoscrizione denominata *SottoscrizioneServizioBizTalk* e concede agli amministratori di BizTalk dell'azienda (ad esempio ContosoAdminSBT@live.com) l'accesso alla sottoscrizione. In questo scenario, gli amministratori di BizTalk accedono al portale di gestione di Azure e dispongono dei diritti di amministratore completi per tutti i servizi ospitati nella sottoscrizione, inclusi Servizi BizTalk di Azure. Gli amministratori di BizTalk non sono titolari dell'account Azure e pertanto non possono accedere alle informazioni di fatturazione.
[In Gestire le sottoscrizioni e gli account di archiviazione nel portale di gestione di Azure][In Gestire le sottoscrizioni e gli account di archiviazione nel portale di gestione di Azure] sono disponibili altre informazioni.

</td>

</tr>

<tr>

<td>
Database SQL di Azure

</td>

<td>
Archivia tabelle, viste e stored procedure usate dal servizio BizTalk, includi dati di rilevamento.
Quando si crea un servizio BizTalk, è possibile usare un server SQL Azure, un database SQL di Azure o creare automaticamente un server o un database nuovo.
La scalabilità del database SQL viene configurata automaticamente. In genere, la scalabilità predefinita è sufficiente per un servizio BizTalk. La modifica della scalabilità incide sui prezzi. Vedere [Account e fatturazione nel database SQL di Azure][Account e fatturazione nel database SQL di Azure]
**Note**

-   Quando si crea un nuovo server SQL di Azure e un nuovo database, Servizi di Azure viene abilitato automaticamente. Il servizio BizTalk richiede che Servizi di Azure sia abilitato.
-   Se si crea un nuovo database SQL di Azure su un server SQL di Azure esistente, le regole del firewall del server non vengono modificate. Di conseguenza, è possibile che altri servizi di Azure non possano accedere ai database del server.

</td>

</tr>

<tr>

<td>
Spazio dei nomi del servizio di controllo di accesso di Azure

</td>

<td>
Consente di eseguire l'autenticazione con Servizi BizTalk di Azure. Quando si distribuisce un progetto di servizio BizTalk da Visual Studio, si inserisce questo spazio dei nomi. Quando si crea un servizio BizTalk, lo spazio dei nomi del servizio di controllo di accesso viene creato automaticamente.

</td>

</tr>
</p>
<tr>
<td>
Account di archiviazione di Azure

</td>
<td>
Consente l'accesso alle tabelle, ai BLOB e alle code usati dal servizio BizTalk per salvare quando indicato di seguito:

-   File di log che monitorano il servizio BizTalk. L'output del monitoraggio viene anche visualizzato nella scheda Monitoring del portale di gestione di Azure.
-   Quando si crea un contratto X12 o AS2 tra partner, è possibile abilitare la funzionalità di archiviazione per archiviare le proprietà dei messaggi. I dati vengono salvati nell'account di archiviazione.

 Quando si crea un servizio BizTalk, è possibile usare un account di archiviazione esistente o crearne automaticamente uno nuovo.
 Le impostazioni di archiviazione predefinite sono sufficienti per un servizio BizTalk.
 Quando si crea un account di archiviazione, vengono create automaticamente una chiave primaria e una chiave secondaria. Queste chiavi controllano l'accesso all'account di archiviazione. Il servizio BizTalk usa automaticamente la chiave primaria.
 Vedere [Archiviazione][Archiviazione] per altre informazioni.

</td>
</tr>
<tr>
<td>
Certificato SSL privato

</td>
<td>
Quando si crea un servizio BizTalk di Azure, viene creato anche un URL HTTPS che include il nome del servizio BizTalk. Questo URL viene configurato automaticamente per l'uso del certificato autofirmato solo per l'ambiente di sviluppo. Per l'ambiente di produzione, è necessario un certificato SSL privato.
 **Informazioni importanti del certificato SSL**

-   La data di scadenza del certificato deve essere inferiore ai 5 anni.
-   Tutti i certificati privati richiedono una password. Non dimenticare la password e, come procedura consigliata, condividerla con gli amministratori.
-   I certificati autofirmati possono essere usati in ambienti di sviluppo/test. Quando si usano certificati autofirmati, è necessario importare il certificato nell'archivio dei certificati personale e nell'archivio dei certificati delle autorità di certificazione radice disponibili nell'elenco locale.

Quando si invia la richiesta del certificato di produzione all'autorità di certificazione, specificare le proprietà del certificato seguenti:

-   **Utilizzo chiavi avanzato**: Servizi BizTalk di Azure richiede almeno l'autenticazione server.
-   **Nome comune**: Immettere il nome di dominio completo dell'URL del servizio BizTalk di Azure. Vedere [Creare un servizio BizTalk][Creare un servizio BizTalk] in questo argomento.

 È possibile aggiungere un nuovo certificato o un certificato diverso al termine della creazione del servizio BizTalk.

</td>
</tr>
</table>
## <a name="HC"></a>Connessioni ibride

Quando si crea un servizio BizTalk di Azure, è disponibile la scheda relativa alle **connessioni ibride**:

![scheda per le connessioni ibride][scheda per le connessioni ibride]

Le connessioni ibride consentono la connessione di un sito Web o di un servizio mobile di Azure, a una risorsa locale che usa una porta TCP statica, ad esempio SQL Server, MySQL, API Web HTTP, Servizi mobili e la maggior parte dei servizi Web personalizzati. Le connessioni ibride e BizTalk Adapter Service presentano differenze. BizTalk Adapter Service viene usato per la connessione di Servizi BizTalk di a un sistema line-of-business locale.

Per altre informazioni, inclusa la creazione e la gestione di connessioni ibride, vedere la [panoramica delle connessioni ibride][panoramica delle connessioni ibride].

## Passaggi successivi

Dopo la creazione del servizio BizTalk, è possibile acquisire familiarità con le diverse schede nell'articolo [Servizi BizTalk: Schede Dashboard, Monitor e Scale][schede Dashboard, Monitoraggio e Scala]. Il servizio BizTalk è pronto per le applicazioni. Per iniziare a creare applicazioni, vedere [Servizi BizTalk di Azure][Servizi BizTalk di Azure].

## Vedere anche

-   [Servizi BizTalk: Grafico edizioni][Servizi BizTalk: Tabella delle edizioni]
-   [Servizi BizTalk: Tabella degli stati del servizio][Servizi BizTalk: Tabella degli stati del servizio]
-   [Servizi BizTalk: Backup e ripristino][Servizi BizTalk: Backup e ripristino]
-   [Servizi BizTalk: limitazione][Servizi BizTalk: limitazione]
-   [Servizi BizTalk: nome e chiave dell'autorità emittente][Servizi BizTalk: nome e chiave dell'autorità emittente]
-   [Come iniziare a usare l'SDK di Servizi BizTalk di Azure][Come iniziare a usare l'SDK di Servizi BizTalk di Azure]
-   [Connessioni ibride][panoramica delle connessioni ibride]

  [Creare un servizio BizTalk]: #BizTalk
  [Passaggi di post-provisioning]: #PostProv
  [Descrizione dei requisiti]: #Requirements
  [Connessioni ibride - Nuovo]: #HC
  [Versione di valutazione gratuita di Azure]: http://go.microsoft.com/fwlink/p/?LinkID=239738
  [portale di gestione di Azure]: http://go.microsoft.com/fwlink/p/?LinkID=213885
  [Select the New button]: ./media/biztalk-provision-services/WABS_New.png
  [Select BizTalk Service and select Custom Create]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
  [Servizi BizTalk: Tabella delle edizioni]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [Progress icon displays when complete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
  [schede Dashboard, Monitoraggio e Scala]: http://go.microsoft.com/fwlink/p/?LinkID=302281
  [Servizi BizTalk: Tabella degli stati del servizio]: http://go.microsoft.com/fwlink/p/?LinkID=329870
  [Installare il certificato in un computer locale]: #InstallCert
  [Aggiungere un certificato per l'ambiente di produzione]: #AddCert
  [Ottenere lo spazio dei nomi ACS]: #ACS
  [Modify SSL Certificate]: ./media/biztalk-provision-services/WABS_QuickGlance.png
  [Select Connection Information]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
  [ACS Service Identities in the Access Control Management Portal]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
  [Gestione dello spazio dei nomi del servizio di controllo di accesso]: http://go.microsoft.com/fwlink/p/?LinkID=285670
  [sottoscrizioni Azure]: https://account.windowsazure.com/Subscriptions
  [In Gestire le sottoscrizioni e gli account di archiviazione nel portale di gestione di Azure]: http://go.microsoft.com/fwlink/p/?LinkID=267577
  [Account e fatturazione nel database SQL di Azure]: http://go.microsoft.com/fwlink/p/?LinkID=234930
  [Archiviazione]: http://go.microsoft.com/fwlink/p/?LinkID=285671
  [scheda per le connessioni ibride]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png
  [panoramica delle connessioni ibride]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [Servizi BizTalk di Azure]: http://go.microsoft.com/fwlink/p/?LinkID=235197
  [Servizi BizTalk: Backup e ripristino]: http://go.microsoft.com/fwlink/p/?LinkID=329873
  [Servizi BizTalk: limitazione]: http://go.microsoft.com/fwlink/p/?LinkID=302282
  [Servizi BizTalk: nome e chiave dell'autorità emittente]: http://go.microsoft.com/fwlink/p/?LinkID=303941
  [Come iniziare a usare l'SDK di Servizi BizTalk di Azure]: http://go.microsoft.com/fwlink/p/?LinkID=302335
