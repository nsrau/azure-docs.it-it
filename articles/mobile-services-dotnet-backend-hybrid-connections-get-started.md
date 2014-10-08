<properties linkid="mobile-services-dotnet-backend-hybrid-connections-get-started" urlDisplayName="Connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections" pageTitle="Connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections - Azure Mobile Services" metaKeywords="" description="Learn how to connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections" metaCanonical="" services="" documentationCenter="Mobile" title="Connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections" authors="yavorg" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="yavorg"></tags>

# Connettersi a un'istanza di SQL Server locale da un servizio mobile di Azure mediante Connessioni ibride

Quando un'azienda passa al cloud è spesso necessario mantenere parte delle risorse nell'ambiente locale, per motivi tecnici, di conformità o di sicurezza. Servizi mobili consente di creare facilmente un livello di mobilità ospitato nel cloud al di sopra di queste risorse, per poi effettuarvi la connessione sicura in locale mediante la funzionalità Connessioni ibride. Sono supportate tutte le risorse che usano una porta TCP statica, compresi Microsoft SQL Server, MySQL, le API Web HTTP e la maggior parte dei servizi Web personalizzati.

In questa esercitazione si apprenderà come modificare un servizio mobile back-end .NET in modo da usare un database SQL Server locale anziché il database SQL di Azure di cui viene effettuato il provisioning con il servizio. Anche se i servizi mobili back-end JavaScript supportano Connessioni ibride, questo argomento fa riferimento solo ai servizi mobili back-end .NET.

In questo argomento vengono descritte le operazioni di base seguenti:

1.  [Prerequisiti][]
2.  [Installare SQL Server Express, abilitare TCP/IP e creare un database SQL Server locale][]
3.  [Creare una connessione ibrida][]
4.  [Installare l'istanza locale di Hybrid Connection Manager per completare la connessione][]
5.  [Modificare un servizio mobile per l'uso della connessione][]

<a name="Prerequisites"></a>

## Prerequisiti

Per completare l'esercitazione, sono necessari i prodotti seguenti. Sono tutti disponibili in versioni gratuite, quindi è possibile avviare le attività di sviluppo per Azure in modo completamente gratuito.

-   **Visual Studio 2013**: per scaricare una versione di valutazione gratuita di Visual Studio 2013, vedere [Download di Visual Studio][]. Installare questo prodotto prima di continuare.

-   **SQL Server 2014 Express with Tools**: scaricare Microsoft SQL Server Express gratuitamente dalla [pagina del database della piattaforma Web Microsoft][]. Scegliere la versione **Express** (non LocalDB). La versione **Express with Tools** include SQL Server Management Studio, che verrà usato in questa esercitazione.

Sarà anche necessario un computer locale che si connetterà ad Azure mediante Connessioni ibride. Il computer deve soddisfare i criteri seguenti:

-   Deve essere in grado di connettersi ad Azure usando la porta 5671
-   Deve essere in grado di raggiungere il *nome host*: *numero di porta* della risorsa locale a cui si vuole eseguire la connessione. La risorsa può essere o meno ospitata nello stesso computer.

<a name="InstallSQL"></a>

## Installare SQL Server Express, abilitare TCP/IP e creare un database SQL Server locale

Per usare un'istanza di SQL Server locale o un database SQL Server Express con una connessione ibrida, TCP/IP deve essere abilitato su una porta statica. A differenza delle istanze denominate, le istanze predefinite di SQL Server usano la porta statica 1433.

Per istruzioni dettagliate su come configurare SQL Server in modo da soddisfare le condizioni illustrate in precedenza, vedere [Installare SQL Server Express, abilitare TCP/IP e creare un database SQL Server locale][1]. Se SQL Server è già installato in una configurazione e in un ambiente che soddisfano le condizioni descritte sopra, è possibile ignorare questo passaggio e iniziare dal passaggio [Creare un database SQL Server locale][].

Ai fini di questa esercitazione, si suppone che il nome del database sia **OnPremisesDB**, che sia in esecuzione sulla porta **1433** e che il nome host del computer sia **onPremisesServer**.

<a name="CreateHC"></a>

## Creare una connessione ibrida

1.  Nel computer locale accedere al [portale di gestione di Azure][].

2.  Nella parte inferiore del pannello di navigazione selezionare **+NUOVO**, **Servizi app**, **Servizio BizTalk** e quindi **Creazione personalizzata**

    ![Create BizTalk service][]

3.  Inserire il **Nome servizio BizTalk** e selezionare un'**Edizione**.

    ![Configure new BizTalk Service][]

    In questa esercitazione viene usato **mobile1**. Sarà necessario fornire un nome univoco per il nuovo servizio BizTalk.

4.  Dopo aver creato il servizio BizTalk, selezionare la scheda **Connessioni ibride** e fare clic su **Aggiungi**.

    ![Add Hybrid Connection][]

    Verrà creata una nuova connessione ibrida.

5.  Inserire un **Nome** e un **Nome host** per la connessione ibrida e impostare la **Porta** su `1433`.

    ![Configure Hybrid Connection][]

    Il nome host è il nome del server locale. In questo modo la connessione ibrida viene configurata in modo da accedere all'istanza di SQL Server in esecuzione sulla porta 1433.

6.  Dopo la creazione, la nuova connessione verrà visualizzata nella tabella seguente.

    ![Hybrid Connection successfully created][]

    Lo stato della nuova connessione sarà **Installazione locale incompleta**.

A questo punto è necessario installare Hybrid Connection Manager nel computer locale.

<a name="InstallHCM"></a>

## Installare l'istanza locale di Hybrid Connection Manager per completare la connessione

Hybrid Connection Manager consente al computer locale di connettersi ad Azure e inoltrare traffico TCP. È necessario installare questo software nel computer locale al quale si sta tentando di accedere da Azure.

1.  Selezionare la connessione appena creata e sulla barra inferiore fare clic su **Installazione locale**.

    ![On-Premises Setup][]

2.  Fare clic su **Installa e configura**.

    ![Install Hybrid Connection Manager][]

    Verrà installata un'istanza personalizzata di Hybrid Connection Manager, preconfigurata per il funzionamento con la connessione ibrida appena creata.

3.  Completare il resto dei passaggi di installazione di Hybrid Connection Manager.

    ![Hybrid Connection Manager setup][]

    Al termine dell'installazione, lo stato della connessione ibrida cambierà in **1 Istanza connessa**. Può essere necessario aggiornare il browser e attendere qualche minuto. L'installazione locale è stata completata.

    ![Hybrid Connection connected][]

<a name="CreateService"></a>

## Modificare un servizio mobile per l'uso della connessione

### Associare la connessione ibrida al servizio

1.  Nella scheda **Servizi mobili** del portale selezionare un servizio mobile esistente o crearne uno nuovo.

    > [WACOM.NOTE]Assicurarsi di selezionare un servizio creato usando il back-end .NET oppure creare un nuovo servizio mobile back-end .NET. Per informazioni su come creare un nuovo servizio mobile back-end .NET, vedere [Introduzione a Servizi mobili][]

2.  Nella scheda **Configura** del servizio mobile individuare la sezione **Connessioni ibride** e selezionare **Aggiungi una connessione ibrida**.

    ![Associate Hybrid Connection][]

3.  Selezionare la connessione ibrida appena creata nella scheda Servizi BizTalk, premere **OK**.

    ![Pick associated Hybrid Connection][]

Il servizio mobile è ora associato alla nuova connessione ibrida.

### Aggiornare il servizio in modo da usare la stringa di connessione locale

Per finire, è necessario creare un'impostazione dell'app per archiviare il valore della stringa di connessione nel server SQL Server locale. È quindi necessario modificare il servizio mobile perché usa la nuova stringa di connessione.

1.  Nella scheda **Configura**, in **Impostazioni app**, aggiungere una nuova impostazione app denominata `onPremisesDatabase` con un valore simile a `Server=onPremisesServer,1433;Database=OnPremisesDB;User ID=sa;Password={password}`.

    ![App setting for connection string][]

    Sostituire `{password}` con la password sicura per il database locale.

2.  Fare clic su **Salva** per salvare la connessione ibrida e l'impostazione app appena creati.

3.  In Visual Studio 2013 aprire il progetto che definisce il servizio mobile basato su .NET.

    Per informazioni su come scaricare il progetto di back-end .NET, vedere [Introduzione a Servizi mobili][].

4.  In Esplora soluzioni espandere la cartella **Modelli** e aprire il file di modello dati, che termina in *Context.cs*.

5.  Modificare il costruttore di istanza **DbContext** per renderlo simile al frammento di codice seguente:

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base(ConfigurationManager.AppSettings["onPremisesDatabase"])
            {
            }

            // snipped
        }

    Il servizio userà ora la nuova stringa di connessione ibrida definita nella impostazioni dell'app in Azure.

6.  Pubblicare le modifiche e usare un'app client connessa al servizio mobile per richiamare alcune operazioni per generare le modifiche al database.

7.  Aprire SQL Management Studio nel computer locale in cui è in esecuzione SQL Server, quindi in Esplora oggetti espandere il database **OnPremisesDB** e quindi espandere **Tabelle**.

8.  Fare clic con il pulsante destro del mouse sulla tabella **hybridService1.TodoItems** e scegliere **Seleziona prime 1000 righe** per visualizzare i risultati.

    ![SQL Management Studio][]

Il servizio mobile ha eseguito il push delle modifiche generate nell'app nel database locale.

## Vedere anche

-   [Sito web delle connessioni ibride][]
-   [Panoramica delle connessioni ibride][]
-   [Servizi BizTalk: Schede Dashboard, Monitor, Scala, Configura e Connessione ibrida][]

<!-- IMAGES -->

  [Prerequisiti]: #Prerequisites
  [Installare SQL Server Express, abilitare TCP/IP e creare un database SQL Server locale]: #InstallSQL
  [Creare una connessione ibrida]: #CreateHC
  [Installare l'istanza locale di Hybrid Connection Manager per completare la connessione]: #InstallHCM
  [Modificare un servizio mobile per l'uso della connessione]: #CreateService
  [Download di Visual Studio]: http://www.visualstudio.com/downloads/download-visual-studio-vs
  [pagina del database della piattaforma Web Microsoft]: http://www.microsoft.com/web/platform/database.aspx
  [1]: /en-us/documentation/articles/web-sites-hybrid-connection-connect-on-premises-sql-server#InstallSQL
  [Creare un database SQL Server locale]: /en-us/documentation/articles/web-sites-hybrid-connection-connect-on-premises-sql-server#CreateSQLDB
  [portale di gestione di Azure]: http://go.microsoft.com/fwlink/p/?linkid=213885&clcid=0x409
  [Create BizTalk service]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/1.png
  [Configure new BizTalk Service]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/2.png
  [Add Hybrid Connection]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/3.png
  [Configure Hybrid Connection]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/4.png
  [Hybrid Connection successfully created]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/5.png
  [On-Premises Setup]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/5-1.png
  [Install Hybrid Connection Manager]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/6.png
  [Hybrid Connection Manager setup]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/7.png
  [Hybrid Connection connected]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/8.png
  [Introduzione a Servizi mobili]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
  [Associate Hybrid Connection]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/9.png
  [Pick associated Hybrid Connection]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/10.png
  [App setting for connection string]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/11.png
  [SQL Management Studio]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/12.png
  [Sito web delle connessioni ibride]: http://azure.microsoft.com/en-us/services/biztalk-services/
  [Panoramica delle connessioni ibride]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [Servizi BizTalk: Schede Dashboard, Monitor, Scala, Configura e Connessione ibrida]: http://azure.microsoft.com/en-us/documentation/articles/biztalk-dashboard-monitor-scale-tabs/
