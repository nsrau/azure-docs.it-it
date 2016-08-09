<properties 
	pageTitle="Uso di Gestione connessione ibrida | Microsoft Azure App Service" 
	description="Installare e configurare Gestione connessione ibrida e connettersi ai connettori locali in Azure App Service" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/10/2016" 
	ms.author="mandia"/>

# Connettersi ai connettori locali in Azure App Service usando Gestione connessione ibrida

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2014-12-01-preview delle app per la logica.

Per usare un sistema locale, il servizio app di Azure usa Gestione connessione ibrida. Alcuni connettori possono connettersi a un sistema locale, ad esempio SQL Server, SAP, SharePoint e così via.

Gestione connessione ibrida è un programma di installazione di tipo ClickOnce che viene installato in un server IIS all'interno della rete protetta da un firewall. Con l'inoltro del bus di servizio di Azure, Gestione connessione ibrida autentica il sistema locale con il connettore in Azure.

> [AZURE.NOTE] Gestione connessione ibrida è necessario solo se ci si connette a una risorsa locale protetta da un firewall. Se non ci si connette a un sistema locale, non è necessario usare Gestione connessione ibrida.

Per iniziare, è necessario:

- Stringa di connessione della firma di accesso condiviso dello spazio dei nomi per l'inoltro del bus di servizio di Azure. Vedere i [prezzi del bus di servizio](https://azure.microsoft.com/pricing/details/service-bus/) per determinare quale livello include gli inoltri.
- Informazioni per l'accesso al sistema locale, inclusi il nome utente e la password. Per connettersi ad esempio a un'istanza di SQL Server locale, è necessario avere l'account di accesso e la password di SQL Server.
- Informazioni del server locale, inclusi il nome del server e il numero di porta. Per connettersi ad esempio a un'istanza di SQL Server locale, è necessario avere il nome dell'istanza di SQL Server e il numero di porta TCP.

## Recuperare la stringa di connessione del bus di servizio

Nel portale di Azure copiare la stringa di connessione della firma di accesso condiviso radice del bus di servizio. Questa stringa di connessione connette il connettore di Azure al sistema locale.

1. Nel [portale di Azure classico](http://go.microsoft.com/fwlink/p/?LinkID=213885) selezionare lo spazio dei nomi del bus di servizio e selezionare **Informazioni di connessione**:

	![][SB_ConnectInfo]

2. Copiare la stringa di connessione della firma di accesso condiviso:

	![][SB_SAS]

## Installare Gestione connessione ibrida

1. Nel [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) selezionare il connettore creato. Per aprirlo, è possibile selezionare **Sfoglia**, **App per le API** e quindi il connettore o l'app per le API. <br/><br/> In **Connessione ibrida** l'installazione è **incompleta**: <br/> ![][2]

2. Selezionare **Connessione ibrida**. Viene elencata la stringa di connessione del bus di servizio immessa in precedenza.
3. Copiare la **Stringa di configurazione primaria**: <br/> ![][PrimaryConfigString]

4. In **Gestione connessione ibrida locale** è possibile scaricare Gestione connessione ibrida o installare il programma direttamente dal portale. <br/><br/> Per installare direttamente dal portale, accedere al server IIS locale, passare al portale e selezionare **Scarica e configura**. <br/><br/> Per scaricare Gestione connessione ibrida, accedere al server IIS locale e selezionare l'**applicazione ClickOnce** (http://hybridclickonce.azurewebsites.net/install/Microsoft.Azure.BizTalk.Hybrid.ClickOnce.application). L'installazione viene avviata automaticamente in modo da poterla eseguire.

5. Nella finestra **Installazione del listener** immettere la **Stringa di configurazione primaria** incollata precedentemente (nel passaggio 3) e selezionare **Installa**.

Al termine dell'installazione, verrà visualizzata la schermata seguente: <br/> ![][3]

A questo punto, quando si passa di nuovo al connettore, lo stato della connessione ibrida sarà **Connesso**: Potrebbe essere necessario chiudere il connettore e riaprirlo: <br/> ![][4]

> [AZURE.NOTE] Per passare alla stringa di connessione secondaria, eseguire nuovamente l'installazione della connessione ibrida e immettere la **Stringa di configurazione secondaria**.


## Porte TCP e sicurezza

Quando si crea una connessione ibrida, viene creato un sito Web nel server IIS locale. Il server IIS può trovarsi in una rete perimetrale. I requisiti della porta TCP sul server IIS includono:

Porta TCP | Motivo
--- | ---
 | Non sono necessarie porte TCP in ingresso.
9350 - 9354 | Queste porte vengono usate per la trasmissione dei dati. La gestione dell'inoltro del bus di servizio verifica la porta 9350 per determinare se è disponibile la connettività TCP. Se è disponibile, presuppone che sia disponibile anche la porta 9352. Il traffico dati passerà per la porta 9352. <br/><br/>Consentire le connessioni in uscita in queste porte.
5671 | Se la porta 9352 viene usata per il traffico dati, la porta 5671 verrà usata come canale di controllo. <br/><br/>Consentire le connessioni in uscita in questa porta. 
80, 443 | Se le porte 9352 e 5671 non sono utilizzabili, verranno usate le porte 80 e 443 come porte di fallback per la trasmissione dei dati e il canale di controllo.<br/><br/>Consentire le connessioni in uscita in queste porte.
Porta di sistema locale | Nel sistema locale aprire la porta usata dal sistema. Ad esempio, SQL Server usa in genere la porta 1433. Aprire questa porta TCP.

[Hosting con protezione da firewall con il bus di servizio](http://msdn.microsoft.com/library/azure/ee706729.aspx)

## Risoluzione dei problemi

![][HCMFlow]

### Risoluzione dei problemi locali

1. Nel server IIS verificare che sia installato il ruolo Web IIS e che siano stati avviati tutti i servizi IIS.
2. Nel server IIS verificare che sia installato Gestione connessione ibrida e che sia in esecuzione:
 - In Gestione IIS (inetmgr) dovrebbe essere elencato il sito Web ***MicrosoftAzureBizTalkHybridListener*** e dovrebbe essere in esecuzione.
 - Questo sito Web usa ***HybridListenerAppPool*** che viene eseguito come account utente predefinito locale di *NetworkService*. Dovrebbe essere avviato anche questo pool di applicazioni.
3. Nel server IIS verificare che sia installato il connettore e che sia in esecuzione:
 - Viene creato un sito Web per il connettore del servizio app. Se ad esempio è stato creato un connettore SQL, sarà presente un sito Web ***MicrosoftSqlConnector\_nnn***. In Gestione IIS (inetmgr) verificare che sia elencato questo sito Web e che sia avviato.
 - Questo sito Web usa un proprio pool di applicazioni IIS denominato ***HybridAppPoolnnn***. Questo pool di applicazioni viene eseguito come account utente predefinito locale di *NetworkService*. Questo sito Web e il pool di applicazioni devono essere entrambi avviati.
 - Individuare il connettore locale. Se ad esempio il sito Web del connettore usa la porta 6569, passare a http://localhost:6569. Non è configurato un documento predefinito, per cui è previsto il messaggio `HTTP Error 403.14 - Forbidden error`.
4. Nel firewall verificare che siano aperte le porte TCP elencate in questo argomento.
5. Esaminare il sistema di origine o di destinazione:
 - Alcuni sistemi locali richiedono file di dipendenza aggiuntivi. Se ad esempio ci si connette a un server SAP locale, è necessario installare alcuni file SAP aggiuntivi nel server IIS.
 - Verificare la connettività al sistema con l'account di accesso. Ad esempio, deve essere aperta la porta TCP usata dal sistema, come la porta 1433 per SQL Server. L'account di accesso immesso nel portale di Azure deve avere l'accesso al sistema.
6. Nel server IIS controllare i registri eventi per verificare la presenza di eventuali errori.
7. Pulire e reinstallare Gestione connessione ibrida:
 - In IIS eliminare manualmente il sito Web del connettore e il relativo pool di applicazioni.
 - Eseguire nuovamente Gestione connessione ibrida e verificare di immettere la **Stringa di configurazione primaria** corretta per il connettore.



### Nel portale di Azure classico:

1. Verificare che lo stato dello spazio dei nomi del bus di servizio sia **Attivo**.
2. Quando si crea il connettore, immettere la stringa di connessione della firma di accesso condiviso del bus di servizio. Non immettere la stringa di connessione ACS.


## Domande frequenti

**DOMANDA**: esistono due programmi Gestione connessione ibrida. Qual è la differenza?

**Risposta**: esiste la tecnologia [Connessioni ibride](../biztalk-services/integration-hybrid-connection-overview.md) usata principalmente dalle app Web (in precedenza siti Web) e dalle app per dispositivi mobili (in precedenza servizi mobili) per connettersi al sistema locale. Questa Gestione connessione ibrida ha un proprio programma di [installazione](../biztalk-services/integration-hybrid-connection-create-manage.md) e usa un servizio BizTalk di Azure (in modo invisibile). Supporta solo i protocolli TCP e HTTP.

Anche con i connettori del servizio app di Azure è presente un programma Gestione connessione ibrida. Questa Gestione connessione ibrida *non* usa un servizio BizTalk di Azure (in modo invisibile) e supporta altri protocolli oltre a TCP e HTTP. Vedere l'[Elenco di connettori e app per le API](app-service-logic-connectors-list.md).

Entrambi usano il bus di servizio di Azure per connettersi al sistema locale.

**DOMANDA**: quando si crea un'app per le API personalizzata, è possibile usare Gestione connessione ibrida del servizio app per connettersi a un sistema locale?

**Risposta**: non nel senso tradizionale. È possibile usare un connettore incorporato e configurare Gestione connessione ibrida del servizio app per connettersi al sistema locale. Usare quindi questo connettore con l'app per le API personalizzata, eventualmente con un'app per la logica. Non è attualmente possibile sviluppare o creare una propria app per le API ibrida, ad esempio il connettore SQL o il connettore file.

Se l'API personalizzata usa una porta TCP o HTTP, è possibile usare [Connessioni ibride](../biztalk-services/integration-hybrid-connection-overview.md) e Gestione connessione ibrida. In questo scenario viene usato un servizio BizTalk di Azure. Provare a [connettersi a un'istanza di SQL Server locale da un'app Web](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md).


## Altre informazioni

[Monitorare le app per la logica](app-service-logic-monitor-your-logic-apps.md)<br/> [Prezzi del bus di servizio](https://azure.microsoft.com/pricing/details/service-bus/)



[SB_ConnectInfo]: ./media/app-service-logic-hybrid-connection-manager/SB_ConnectInfo.png
[SB_SAS]: ./media/app-service-logic-hybrid-connection-manager/SB_SAS.png
[PrimaryConfigString]: ./media/app-service-logic-hybrid-connection-manager/PrimaryConfigString.png
[HCMFlow]: ./media/app-service-logic-hybrid-connection-manager/HCMFlow.png
[2]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-hybrid-connection-manager/HybridSetup.jpg
[4]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupComplete.jpg

 

<!---HONumber=AcomDC_0727_2016-->