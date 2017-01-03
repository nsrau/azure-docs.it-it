---
title: Come usare il Controllo di accesso (Java) | Documentazione Microsoft
description: Informazioni su come sviluppare e usare Controllo di accesso con Java in Azure.
services: active-directory
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 247dfd59-0221-4193-97ec-4f3ebe01d3c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/01/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6afe2958800abd211a8f9e9298964ea25e43394c


---
# <a name="how-to-authenticate-web-users-with-azure-access-control-service-using-eclipse"></a>Come autenticare gli utenti Web con il Servizio di controllo di accesso di Azure usando Eclipse
In questa guida verrà descritto come usare il Servizio di controllo di accesso di Azure (ACS) nel plug-nel Toolkit di Azure per Eclipse. Per altre informazioni su ACS, vedere la sezione [Passaggi successivi](#next_steps) .

> [!NOTE]
> Il filtro dei Servizi di controllo di accesso di Azure è una Community Technology Preview. Come versione preliminare, non è formalmente supportata da Microsoft.
> 
> 

## <a name="what-is-acs"></a>Informazioni su ACS
La maggior parte degli sviluppatori non ha esperienza nell'ambito delle identità e in genere non desidera dedicare tempo a sviluppare meccanismi di autenticazione e autorizzazione per le applicazioni e i servizi. ACS è un servizio di Azure tramite cui viene fornito un metodo semplice per autenticare gli utenti che devono accedere alle applicazioni e ai servizi Web, senza dover includere nel codice una logica di autenticazione complessa.

In ACS sono disponibili le funzionalità seguenti:

* Integrazione con Windows Identity Foundation (WIF).
* Supporto per i provider di identità Web più diffusi, tra cui Windows Live ID, Google, Yahoo! e Facebook.
* Supporto per Active Directory Federation Services (ADFS) 2.0.
* Un servizio di gestione basato sul protocollo OData che offre accesso programmatico alle impostazioni di ACS.
* Un portale di gestione che consente l'accesso amministrativo alle impostazioni di ACS.

Per altre informazioni sul servizio di controllo di accesso, vedere [Servizio di controllo di accesso 2.0][Access Control Service 2.0].

## <a name="concepts"></a>Concetti
Il servizio ACS di Azure usa l'identità basata sulle attestazioni, un approccio coerente alla creazione di meccanismi di autenticazione per le applicazioni in esecuzione locale o nel cloud. L'identità basata sulle attestazioni offre un metodo comune che le applicazioni e i servizi possono usare per acquisire le informazioni di identità necessarie sugli utenti interni o esterni all'organizzazione oppure su Internet.

Per completare le attività in questa guida è necessario comprendere i concetti illustrati di seguito:

**Client** : nel contesto di questa guida alle procedure si tratta di un browser che tenta di ottenere l'accesso a un'applicazione Web.

**Applicazione relying party (RP)** : un'applicazione relying party è un sito Web o un servizio che affida l'autenticazione a un'autorità esterna. In gergo tecnico, si dice che considera attendibile tale autorità. In questa guida viene illustrato come configurare un'applicazione per considerare attendibile ACS.

**Token** : un token è una raccolta di dati di sicurezza rilasciata quando un utente viene autenticato. Contiene un set di *attestazioni*, attributi dell'utente autenticato. Un'attestazione può corrispondere al nome dell'utente, a un identificatore del ruolo svolto dall'utente, alla sua età e così via. I token sono di norma firmati digitalmente, pertanto è sempre possibile risalire all'emittente e non è possibile alterarne il contenuto. Un utente ottiene accesso a un'applicazione relying party presentando un token valido, rilasciato da un'autorità che l'applicazione RP considera attendibile.

**Provider di identità (IP)** : un provider di identità è un'autorità che autentica le identità degli utenti e rilascia token di sicurezza. L'effettivo rilascio di token è implementato tramite un servizio speciale denominato Servizio token di sicurezza (STS). Esempi tipici di provider di identità comprendono Windows Live ID, Facebook, archivi di utenti business (come Active Directory) e così via.
Quando ACS viene configurato in modo da considerare attendibile un provider di identità, il sistema accetta e convalida i token rilasciati da tale provider. ACS può considerare attendibili più provider di identità contemporaneamente, quindi se un'applicazione considera attendibile ACS è possibile consentire agli utenti di eseguire l'autenticazione tramite uno qualsiasi degli IP considerati attendibili da ACS.

**Provider di federazione (FP)** : i provider di identità (IP) hanno una conoscenza diretta degli utenti, li autenticano usando le relative credenziali e rilasciano attestazioni sulle informazioni disponibili sugli utenti. Un provider di federazione è un'autorità di tipo diverso, in quanto anziché autenticare gli utenti direttamente, funge da intermediario di autenticazione tra una relying party e uno o più indirizzi IP. Provider di identità e provider di federazione rilasciano token di sicurezza, quindi usano entrambi il Servizio token di sicurezza (STS). ACS è un provider di federazione.

**Motore di regole ACS** : la logica adottata per trasformare i token in ingresso da IP attendibili a token destinati all'utilizzo da parte dell'applicazione relying party è codificata sotto forma di semplici regole di trasformazione delle attestazioni. ACS è dotato di un motore di regole che applica qualsiasi logica di trasformazione specificata per la relying party.

**Spazio dei nomi ACS** : lo spazio dei nomi costituisce la partizione di primo livello di ACS da usare per organizzare le impostazioni. Lo spazio dei nomi contiene un elenco di provider di identità attendibili, le applicazioni RP da servire, le regole con cui il motore delle regole elaborerà i token in arrivo e così via. Lo spazio dei nomi espone vari endpoint che verranno usati dall'applicazione e dallo sviluppatore affinché ACS svolga la sua funzione.

Nella figura seguente viene illustrato il funzionamento dell'autenticazione ACS con un'applicazione Web:

![Diagramma di flusso di ACS][acs_flow]

1. Il client, in questo caso un browser, richiede una pagina dalla relying party.
2. Poiché la richiesta non è stata ancora autenticata, l'applicazione RP reindirizza l'utente all'autorità che considera attendibile, ovvero ACS. ACS presenta all'utente l'elenco dei provider di identità specificati per questa applicazione RP. L'utente seleziona il provider di identità appropriato.
3. Il client passa alla pagina di autenticazione del provider di identità e chiede all'utente di eseguire l'accesso.
4. Dopo l'autenticazione del client, ad esempio dopo l'immissione delle credenziali di identità, il provider di identità rilascia un token di sicurezza.
5. Dopo il rilascio del token di sicurezza, il provider di identità reindirizza il client ad ACS e il client invia ad ACS il token di sicurezza rilasciato dal provider di identità.
6. ACS convalida il token di sicurezza rilasciato dal provider di identità, inserisce le attestazioni di identità contenute nel motore regole ACS, calcola le attestazioni di identità di output e rilascia un nuovo token di sicurezza che contiene tali attestazioni di identità di output.
7. ACS reindirizza il client all'applicazione RP. Il client invia il nuovo token di sicurezza rilasciato da ACS all'applicazione relying party. L'applicazione relying party convalida la firma nel token di sicurezza rilasciato da ACS, convalida le attestazioni contenute nel token e restituisce la pagina richiesta.

## <a name="prerequisites"></a>Prerequisiti
Per completare le attività in questa guida è necessario quanto segue:

* Java Developer Kit (JDK) v 1.6 o versione successiva.
* IDE Eclipse per sviluppatori Java EE, Indigo o versione successiva. È possibile scaricare il pacchetto all'indirizzo <http://www.eclipse.org/downloads/>. 
* La distribuzione di un server Web basato su Java o un server applicazioni, come Apache Tomcat, GlassFish, JBoss Application Server o Jetty.
* Una sottoscrizione di Azure, che può essere ottenuta all'indirizzo <http://www.microsoft.com/windowsazure/offers/>.
* Il Toolkit di Azure per Eclipse, versione di aprile 2014 o successive. Per altre informazioni, vedere [Installazione di Azure Toolkit per Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690946.aspx).
* Un certificato X.509 da usare con l'applicazione. Questo certificato è necessario in entrambi i formati di certificato pubblico (.cer) e certificato di scambio di informazioni personali (.PFX). Le opzioni per la creazione di questo certificato verranno descritte più avanti in questa esercitazione.
* Familiarità con l'emulatore di calcolo di Azure e le tecniche di distribuzione illustrate in [Creazione di un'applicazione Hello World per Azure in Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx).

## <a name="create-an-acs-namespace"></a>Creare uno spazio dei nomi ACS
Per iniziare a usare il Servizio di controllo di accesso (ACS) in Azure, è necessario creare uno spazio dei nomi ACS. Lo spazio dei nomi offre un ambito univoco per fare riferimento alle risorse di ACS dall'interno dell'applicazione.

1. Accedere al [portale di gestione di Azure][Azure Management Portal].
2. Fare clic su **Active Directory**. 
3. Per creare un nuovo spazio dei nomi di Controllo di accesso, fare clic su **Nuovo**, **Servizi app**, **Controllo di accesso** e quindi su **Creazione rapida**. 
4. Immettere un nome per lo spazio dei nomi. Azure verificherà che il nome sia univoco.
5. Selezionare l'area in cui viene usato lo spazio dei nomi. Per prestazioni ottimali, usare l'area in cui si sta distribuendo l'applicazione.
6. Se sono disponibili più sottoscrizioni, selezionare quella che si vuole usare per lo spazio dei nomi ACS.
7. Fare clic su **Create**.

Azure creerà e attiverà lo spazio dei nomi. Prima di continuare, attendere che lo stato del nuovo spazio dei nomi sia **Active** . 

## <a name="add-identity-providers"></a>Aggiunta di un provider di identità
In questa attività si aggiungeranno provider di identità da usare con l'applicazione relying party per l'autenticazione. A scopo dimostrativo, questa attività illustra come aggiungere Windows Live come provider di identità, ma è possibile usare uno qualsiasi dei provider di identità elencati nel portale di gestione ACS.

1. Nel [portale di gestione di Azure][Azure Management Portal] fare clic su **Active Directory**, scegliere uno spazio dei nomi di Controllo di accesso e quindi fare clic su **Gestisci**. Verrà visualizzato il portale di gestione ACS.
2. Nel riquadro di spostamento sinistro del portale di gestione ACS fare clic su **Identity providers**.
3. Windows Live ID è abilitato per impostazione predefinita e non può essere eliminato. Ai fini di questa esercitazione, verrà usato solo Windows Live ID. In questa schermata è tuttavia possibile aggiungere altri provider di identità, facendo clic sul pulsante **Add** .

Windows Live ID è abilitato come provider di identità per lo spazio dei nomi ACS. Nel passaggio successivo verrà specificata l'applicazione Web Java (che verrà creata in seguito) come applicazione relying party.

## <a name="add-a-relying-party-application"></a>Aggiungere un'applicazione relying party
In questa attività, ACS verrà configurato affinché riconosca l'applicazione Web Java come applicazione relying party valida.

1. Nel portale di gestione ACS fare clic su **Relying party applications**.
2. Nella pagina **Relying Party Applications** (Applicazioni relying party) fare clic su **Add** (Aggiungi).
3. Nella pagina **Add Relying Party Application** eseguire le operazioni seguenti:
   
   1. Nel campo **Name**digitare il nome dell'applicazione relying party. Ai fini di questa esercitazione, digitare **Azure Web App**.
   2. In **Mode** (Modalità) selezionare **Enter settings manually** (Immettere le informazioni manualmente).
   3. In **Realm**digitare l'URI cui si riferisce il token di sicurezza rilasciato da ACS. Per questa attività digitare **http://localhost:8080/**.
      ![Area di autenticazione dell'applicazione relying party nell'emulatore di calcolo][relying_party_realm_emulator]
   4. In **Return URL** digitare l'URL a cui ACS restituisce il token di sicurezza. Per questa attività, digitare **http://localhost:8080/MyACSHelloWorld/index.jsp**
      ![URL restituito dell'applicazione relying party nell'emulatore di calcolo][relying_party_return_url_emulator]
   5. Accettare i valori predefiniti nei campi rimanenti.
4. Fare clic su **Salva**.

L'applicazione Web Java è configurata correttamente quando viene eseguita nell'emulatore di calcolo di Azure (all'indirizzo http://localhost:8080/) e costituisce un'applicazione relying party nello spazio dei nomi ACS. Il passaggio successivo prevede la creazione di regole usate da ACS per elaborare attestazioni per l'applicazione relying party.

## <a name="create-rules"></a>Creazione di regole
In questa attività verranno definite le regole in base alle quali le attestazioni vengono passate dai provider di identità all'applicazione relying party. Ai fini di questa guida, ACS verrà semplicemente configurato per copiare i tipi di attestazione e i valori in ingresso direttamente nella categoria token di output senza filtrarli o modificarli.

1. Nella pagina principale del portale di gestione ACS fare clic su **Rule groups**.
2. Nella pagina **Rule groups** (Gruppi di regole) fare clic su **Default Rule Group for Azure Web App** (Gruppo di regole predefinito per app Web di Azure).
3. Nella pagina **Edit Rule Group** (Modifica gruppo di regole) fare clic su **Generate** (Genera).
4. Nella pagina **Generate Rules: Default Rule Group for Azure Web App** (Generazione regole: gruppo di regole predefinito per app Web di Azure) assicurarsi che l'opzione Windows Live ID sia selezionata e quindi fare clic su **Generate** (Genera).    
5. Nella pagina **Edit Rule Group** (Modifica gruppo di regole) fare clic su **Save** (Salva).

## <a name="upload-a-certificate-to-your-acs-namespace"></a>Caricamento di un certificato nello spazio dei nomi ACS
In questa attività verrà caricato un certificato .PFX che verrà utilizzato per firmare le richieste di token create dallo spazio dei nomi ACS.

1. Nella pagina principale del portale di gestione ACS fare clic su **Certificates and keys**.
2. Nella pagina **Certificates and keys** (Chiavi e certificati) fare clic su **Add** (Aggiungi) sopra **Token Signing** (Firma di token).
3. Nella pagina **Add Token-Signing Certificate or Key** :
   1. Nella sezione **Used for** (Usato per) fare clic su **Relying Party Application** (Applicazione relying party) e selezionare **Azure Web App** (App Web di Azure), impostato in precedenza come nome dell'applicazione relying party.
   2. Nella sezione **Type** (Tipo) selezionare **X.509 Certificate** (Certificato X.509).
   3. Nella sezione **Certificate** fare clic sul pulsante Browse e passare al file del certificato X.509 che si intende usare. Si tratta di un file .PFX. Selezionare il file, fare clic su **Open** (Apri) e quindi immettere la password del certificato nella casella di testo **Password**. Si noti che a fini di test è possibile utilizzare un certificato autofirmato. Per creare un certificato autofirmato, fare clic sul pulsante **New** (Nuovo) nella finestra di dialogo **ACS Filter Library** (Libreria di filtri ACS) illustrata più avanti oppure usare l'utilità **encutil.exe** dal [sito Web del progetto][project website] di Azure Starter Kit per Java.
   4. Assicurarsi che **Make Primary** sia selezionato. La pagina **Add Token-Signing Certificate or Key** dovrebbe essere simile alla seguente.
       ![Aggiungere un certificato di firma di token][add_token_signing_cert]
   5. Fare clic su **Save** (Salva) per salvare le impostazioni e chiudere la pagina **Add Token-Signing Certificate or Key** (Aggiungi certificato o chiave di firma token).

A questo punto, rivedere le informazioni nella pagina di integrazione applicazioni e copiare l'URI necessario per configurare l'applicazione Web Java per l'uso di ACS.

## <a name="review-the-application-integration-page"></a>Rivedere la pagina di integrazione applicazioni
Nella pagina di integrazione applicazioni del portale di gestione ACS è possibile trovare tutte le informazioni e il codice necessario per configurare l'applicazione Web Java (applicazione relying party) in modo che funzioni con ACS. Queste informazioni sono necessarie per la configurazione dell'applicazione Web Java per l'autenticazione federata.

1. Nel portale di gestione ACS fare clic su **Application integration**.  
2. Nella pagina **Application Integration** (Integrazione applicazioni) fare clic su **Login Pages** (Pagine di accesso).
3. Nella pagina **Login Page Integration** (Integrazione pagine di accesso) fare clic su **Azure Web App** (App Web di Azure).

Nella pagina **Login Page Integration: Azure Web App** (Integrazione pagine di accesso: App Web di Azure), per l'applicazione Web Java verrà usato l'URL visualizzato in **Option 1: Link to an ACS-hosted login page** (Opzione 1: Collegamento a una pagina di accesso ospitata in ACS). Questo valore è necessario per aggiungere la libreria dei filtri dei Servizi di controllo di accesso di Azure all'applicazione Java.

## <a name="create-a-java-web-application"></a>Creazione di un'applicazione Web Java
1. Nel menu di Eclipse fare clic su **File**, **New** (Nuovo) e quindi su **Dynamic Web Project** (Progetto Web dinamico). Se **Dynamic Web Project** (Progetto Web dinamico) non è elencato tra i progetti disponibili dopo aver fatto clic su **File**, **New** (Nuovo), fare clic su **File**, **New** (Nuovo), **Project** (Progetto), espandere **Web**, fare clic su **Dynamic Web Project** (Progetto Web dinamico) e su **Next** (Avanti). Ai fini di questa esercitazione, denominare il progetto **MyACSHelloWorld**. Assicurarsi di usare questo nome, poiché i passaggi successivi dell'esercitazione prevedono che il file WAR sia denominato MyACSHelloWorld. L'aspetto della schermata sarà simile al seguente:
   
    ![Creazione di un progetto Hello World a titolo di esempio per ACS][create_acs_hello_world]
   
    Fare clic su **Finish**.
2. Nella visualizzazione Project Explorer di Eclipse espandere **MyACSHelloWorld**. Fare clic con il pulsante destro del mouse su **WebContent**, scegliere **New** e quindi fare clic su **JSP File**.
3. Nella finestra di dialogo **New JSP File** (Nuovo file JSP) assegnare al file il nome **index.jsp**. Mantenere il nome MyACSHelloWorld/WebContent per la cartella padre, come illustrato di seguito:
   
    ![Aggiunta di un file JSP a titolo di esempio per ACS][add_jsp_file_acs]
   
    Fare clic su **Avanti**.
4. Nella finestra di dialogo **Select JSP Template** (Seleziona modello JSP) selezionare **New JSP File (html)** (Nuovo file JSP (html)) e fare clic su **Finish** (Fine).
5. Quando in Eclipse viene aperto il file index.jsp, aggiungere il testo in modo da visualizzare **Hello ACS World!** all'interno dell'elemento `<body>` esistente. Il contenuto `<body>` aggiornato risulterà simile al seguente:
   
        <body>
          <b><% out.println("Hello ACS World!"); %></b>
        </body>
   
    Salvare index.jsp.

## <a name="add-the-acs-filter-library-to-your-application"></a>Aggiungere una libreria di filtri ACS all'applicazione
1. In Project Explorer di Eclipse fare clic con il pulsante destro del mouse su **MyACSHelloWorld**, scegliere **Build Path** (Percorso di compilazione) e quindi fare clic su **Configure Build Path** (Configura il percorso di compilazione).
2. Nella finestra di dialogo **Java Build Path** (Percorso compilazione Java) fare clic sulla scheda **Libraries** (Librerie).
3. Fare clic su **Add Library**.
4. Fare clic su **Azure Access Control Services Filter (by MS Open Tech)** (Filtro servizi di controllo di accesso di Azure - di MS Open Tech) e quindi su **Next** (Avanti). Verrà visualizzata la finestra di dialogo **Azure Access Control Services Filter** .  Il campo **Location** può avere un percorso diverso, a seconda del percorso in cui è stato installato Eclipse e il numero della versione potrebbe variare a seconda degli aggiornamenti del software.
   
    ![Aggiunta di una libreria di filtri ACS][add_acs_filter_lib]
5. Con il browser aperto alla pagina **Login Page Integration** (Integrazione pagine di accesso) del portale di gestione, copiare l'URL riportato nel campo **Option 1: Link to an ACS-hosted login page** (Opzione 1: Collegamento a una pagina di accesso ospitata in ACS) e incollarlo nel campo **ACS Authentication Endpoint** (Endpoint di autenticazione ACS) della finestra di dialogo di Eclipse.
6. Con il browser aperto alla pagina **Edit Relying Party Application** (Modifica applicazione relying party) del portale di gestione, copiare l'URL riportato nel campo **Realm** (Area autenticazione) e incollarlo nel campo **Relying Party Realm** (Area autenticazione relying party) della finestra di dialogo di Eclipse.
7. Per usare un certificato esistente, nella sezione **Security** (Sicurezza) della finestra di dialogo di Eclipse fare clic su **Browse** (Sfoglia), passare al certificato che si intende usare, selezionarlo e fare clic su **Open** (Apri). In caso contrario, se si intende creare un certificato nuovo, fare clic su **New** (Nuovo) per visualizzare la finestra di dialogo **New Certificate** (Nuovo certificato) e quindi specificare la password, il nome del file CER e il nome del file PFX per il nuovo certificato.
8. Selezionare **Embed the certificate in the WAR file**. Quando si incorpora il certificato in questo modo, viene incluso nella distribuzione senza che sia necessario aggiungerlo manualmente come un componente. Se invece è necessario archiviare il certificato all'esterno del file WAR, è possibile aggiungere il certificato come componente del ruolo e deselezionare **Embed the certificate in the WAR file**.
9. [Facoltativo] Mantenere l'opzione **Require HTTPS connections** selezionata. Se viene visualizzata questa opzione, è necessario accedere all'applicazione utilizzando il protocollo HTTPS. Se non si desidera richiedere connessioni HTTPS, deselezionare questa opzione.
10. Per la distribuzione nell'emulatore di calcolo, le impostazioni di **Azure ACS Filter** avranno un aspetto simile a quanto illustrato di seguito.
    
    ![Impostazioni del filtro ACS di Azure per una distribuzione nell'emulatore di calcolo.][add_acs_filter_lib_emulator]
11. Fare clic su **Finish**.
12. Fare clic su **Yes** nella finestra di dialogo che conferma la creazione di un file web.xml.
13. Fare clic su **OK** per chiudere la finestra di dialogo **Java Build Path** (Percorso compilazione Java).

## <a name="deploy-to-the-compute-emulator"></a>Distribuire l'emulatore di calcolo
1. In Project Explorer di Eclipse fare clic con il pulsante destro del mouse su **MyACSHelloWorld**, scegliere **Azure** e quindi fare clic su **Package for Azure** (Pacchetto per Azure).
2. In **Project name** (Nome progetto) digitare **MyAzureACSProject** e fare clic su **Next** (Avanti).
3. Selezionare un JDK e un server applicazioni. Questi passaggi vengono descritti in dettaglio nell'esercitazione [Creazione di un'applicazione Hello World per Azure in Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) .
4. Fare clic su **Finish**.
5. Fare clic sul pulsante **Run in Azure Emulator** .
6. Dopo l'avvio dell'applicazione Web Java nell'emulatore di calcolo, chiudere tutte le istanze del browser (per evitare che sessioni correnti del browser interferiscano con il test di accesso di ACS.
7. Eseguire l'applicazione aprendo nel browser <http://localhost:8080/MyACSHelloWorld/> o <https://localhost:8080/MyACSHelloWorld/>, se è stata selezionata l'opzione **Require HTTPS connections** (Richiedi connessioni HTTPS). Verrà richiesto di immettere un account di accesso Windows Live ID, quindi si verrà reindirizzati all'URL restituito specificato per l'applicazione relying party.
8. Terminata la visualizzazione dell'applicazione, fare clic sul pulsante **Reset Azure Emulator** .

## <a name="deploy-to-azure"></a>Distribuzione in Azure
Per la distribuzione in Azure è necessario modificare l'area di autenticazione dell'applicazione relying party e dell'URL restituito per lo spazio dei nomi ACS.

1. Nella pagina **Edit Relying Party Application** (Modifica applicazione relying party) del portale di gestione di Azure modificare **Realm** (Area autenticazione) per far sì che corrisponda all'URL del sito distribuito. Sostituire **example** con il nome DNS specificato per la distribuzione.
   
    ![Area di autenticazione dell'applicazione relying party da utilizzare in produzione][relying_party_realm_production]
2. Modificare **Return URL** affinché corrisponda all'URL dell'applicazione. Sostituire **example** con il nome DNS specificato per la distribuzione.
   
    ![URL restituito dell'applicazione relying party da usare in produzione][relying_party_return_url_production]
3. Fare clic su **Save** per salvare le modifiche apportate all'area di autenticazione dell'applicazione relying party e all'URL restituito.
4. Tenere aperta la pagina **Login Page Integration** nel browser poiché in seguito verrà richiesto di copiare alcuni dati.
5. In Project Explorer di Eclipse fare clic con il pulsante destro del mouse su **MyACSHelloWorld**, scegliere **Build Path** (Percorso di compilazione) e quindi fare clic su **Configure Build Path** (Configura il percorso di compilazione).
6. Fare clic sulla scheda **Libraries** (Librerie), su **Azure Access Control Services Filter** (Filtro servizi di controllo di accesso di Azure ) e quindi su **Edit** (Modifica).
7. Con il browser aperto alla pagina **Login Page Integration** (Integrazione pagine di accesso) del portale di gestione, copiare l'URL riportato nel campo **Option 1: Link to an ACS-hosted login page** (Opzione 1: Collegamento a una pagina di accesso ospitata in ACS) e incollarlo nel campo **ACS Authentication Endpoint** (Endpoint di autenticazione ACS) della finestra di dialogo di Eclipse.
8. Con il browser aperto alla pagina **Edit Relying Party Application** (Modifica applicazione relying party) del portale di gestione, copiare l'URL riportato nel campo **Realm** (Area autenticazione) e incollarlo nel campo **Relying Party Realm** (Area autenticazione relying party) della finestra di dialogo di Eclipse.
9. Per usare un certificato esistente, nella sezione **Security** (Sicurezza) della finestra di dialogo di Eclipse fare clic su **Browse** (Sfoglia), passare al certificato che si intende usare, selezionarlo e fare clic su **Open** (Apri). In caso contrario, se si intende creare un certificato nuovo, fare clic su **New** (Nuovo) per visualizzare la finestra di dialogo **New Certificate** (Nuovo certificato) e quindi specificare la password, il nome del file CER e il nome del file PFX per il nuovo certificato.
10. Mantenere l'opzione **Embed the certificate in the WAR file** selezionata, nel caso in cui si intenda incorporare il certificato nel file WAR.
11. [Facoltativo] Mantenere l'opzione **Require HTTPS connections** selezionata. Se viene visualizzata questa opzione, è necessario accedere all'applicazione utilizzando il protocollo HTTPS. Se non si desidera richiedere connessioni HTTPS, deselezionare questa opzione.
12. Per la distribuzione in Azure, le impostazioni del filtro ACS di Azure avranno un aspetto simile a quelle illustrate di seguito.
    
    ![Impostazioni del filtro ACS di Azure per la distribuzione in produzione][add_acs_filter_lib_production]
13. Fare clic su **Finish** (Fine) per chiudere la finestra di dialogo **Edit Library** (Modifica libreria).
14. Fare clic su **OK** per chiudere la finestra di dialogo **Properties for MyACSHelloWorld** (Proprietà per MyACSHelloWorld).
15. In Eclipse fare clic sul pulsante **Publish to Azure Cloud** . Rispondere alle richieste visualizzate, come nella sezione **Per distribuire l'applicazione in Azure** dell'argomento [Creazione di un'applicazione Hello World per Azure in Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) . 

Dopo la distribuzione dell'applicazione Web, chiudere le sessioni del browser aperte ed eseguire l'applicazione Web. Verrà richiesto di accedere con le credenziali di Windows Live ID e si verrà reindirizzati all'URL restituito dell'applicazione relying party.

Al termine dell'utilizzo dell'applicazione ACS Hello World, ricordare di eliminare la distribuzione (per ulteriori informazioni sull'eliminazione di una distribuzione, vedere l'esercitazione [Creazione di un'applicazione Hello World per Azure in Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) ).

## <a name="a-namenextstepsanext-steps"></a><a name="next_steps"></a>Passaggi successivi
Per un esame del linguaggio Security Assertion Markup Language (SAML) restituito da ACS all'applicazione, vedere [Come visualizzare il codice SAML restituito dal Servizio di controllo di accesso di Azure][How to view SAML returned by the Azure Access Control Service]. Per continuare a esplorare le funzionalità di ACS ed esercitarsi con scenari più complessi, vedere [Servizio di controllo di accesso 2.0][Access Control Service 2.0].

In questo esempio è stata usata l'opzione **Embed the certificate in the WAR file** . Questa opzione semplifica la distribuzione del certificato. Se invece si desidera mantenere il certificato di firma separato dal file WAR, è possibile applicare la tecnica seguente:

1. Nella sezione **Security** (Sicurezza) della finestra di dialogo **Azure Access Control Services Filter** (Filtro servizi di controllo di accesso di Azure) digitare **${env.JAVA_HOME}/mycert.cer** e deselezionare **Embed the certificate in the WAR file** (Incorpora il certificato nel file WAR). Se il nome del certificato in uso è diverso, modificare il nome del file mycert.cer di conseguenza. Fare clic su **Finish** (Fine) per chiudere la finestra di dialogo.
2. Copiare il certificato come componente nella distribuzione: in Project Explorer di Eclipse espandere **MyAzureACSProject**, fare clic con il pulsante destro del mouse su **WorkerRole1**, scegliere **Properties** (Proprietà), espandere **Azure Role** (Ruolo di Azure), quindi fare clic su **Components** (Componenti).
3. Fare clic su **Aggiungi**.
4. Nella finestra di dialogo **Add Component** :
   
   1. Nella sezione **Import** :
      1. usare il pulsante **File** per passare al certificato che si intende usare. 
      2. In **Method** (Metodo) selezionare **copy** (Copia).
   2. In **As Name**fare clic sulla casella di testo e accettare il nome predefinito.
   3. Nella sezione **Deploy** :
      1. In **Method** (Metodo) selezionare **copy** (Copia).
      2. In **To directory** (Directory di destinazione) digitare **%JAVA_HOME%**.
   4. La finestra di dialogo **Add Component** avrà un aspetto analogo al seguente:
      
       ![Aggiunta di un componente certificato][add_cert_component]
   5. Fare clic su **OK**.

A questo punto, il certificato verrà incluso nella distribuzione. Si noti che a prescindere dal fatto che il certificato sia incorporato nel file WAR o aggiunto alla distribuzione come componente, è necessario caricare il certificato nello spazio dei nomi come descritto nella sezione [Caricamento di un certificato nello spazio dei nomi ACS][Upload a certificate to your ACS namespace].

[What is ACS?]: #what-is
[Concepts]: #concepts
[Prerequisites]: #pre
[Create a Java web application]: #create-java-app
[Create an ACS Namespace]: #create-namespace
[Add Identity Providers]: #add-IP
[Add a Relying Party Application]: #add-RP
[Create Rules]: #create-rules
[Upload a certificate to your ACS namespace]: #upload-certificate
[Review the Application Integration Page]: #review-app-int
[Configure Trust between ACS and Your ASP.NET Web Application]: #config-trust
[Add the ACS Filter library to your application]: #add_acs_filter_library
[Deploy to the compute emulator]: #deploy_compute_emulator
[Deploy to Azure]: #deploy_azure
[Next steps]: #next_steps
[project website]: http://wastarterkit4java.codeplex.com/releases/view/61026
[How to view SAML returned by the Azure Access Control Service]: /en-us/develop/java/how-to-guides/view-saml-returned-by-acs/
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Windows Identity Foundation]: http://www.microsoft.com/download/en/details.aspx?id=17331
[Windows Identity Foundation SDK]: http://www.microsoft.com/download/en/details.aspx?id=4451
[Azure Management Portal]: https://manage.windowsazure.com
[acs_flow]: ./media/active-directory-java-authenticate-users-access-control-eclipse/ACSFlow.png

<!-- Eclipse-specific -->
[add_acs_filter_lib]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibrary.png
[add_acs_filter_lib_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryEmulator.png
[add_acs_filter_lib_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryProduction.png

[relying_party_realm_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmEmulator.png
[relying_party_return_url_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLEmulator.png
[relying_party_realm_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmProduction.png
[relying_party_return_url_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLProduction.png
[add_cert_component]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddCertificateComponent.png
[add_jsp_file_acs]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddJSPFileACS.png
[create_acs_hello_world]: ./media/active-directory-java-authenticate-users-access-control-eclipse/CreateACSHelloWorld.png
[add_token_signing_cert]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddTokenSigningCertificate.png




<!--HONumber=Jan17_HO1-->


