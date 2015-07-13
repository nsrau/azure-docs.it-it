<properties 
	pageTitle="Panoramica delle connessioni ibride | Azure" 
	description="Informazioni sulle connessioni ibride, inclusa la sicurezza, le porte TCP e le configurazioni supportate. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/14/2015" 
	ms.author="mandia"/>


# Panoramica delle connessioni ibride
Questo argomento presenta la funzionalità Connessioni ibride, descrive le configurazioni supportate e indica le porte TCP necessarie.


## Informazioni sulle connessioni ibride

La funzionalità Connessioni ibride è inclusa in Servizi BizTalk di Azure. La funzionalità Connessioni ibride consente di connettere app Web di Azure (in precedenza siti Web) e app per dispositivi mobili di Azure (in precedenza Servizi mobili) alle risorse locali protette da firewall:

![connessioni ibride][HCImage]

Vantaggi della funzionalità Connessioni ibride:

- App Web e app per dispositivi mobili possono accedere ai dati e ai servizi locali esistenti in modo sicuro.
- Più app Web o app per dispositivi mobili possono condividere una connessione ibrida per accedere a una risorsa locale. 
- Per accedere alla rete, è necessario un numero minimo di porte TCP.
- Le applicazioni che usano la funzionalità Connessioni ibride accedono solo alla risorsa locale specifica pubblicata tramite la connessione ibrida.
- È possibile connettere qualsiasi risorsa locale che usa una porta TCP statica, ad esempio SQL Server, MySQL, API Web HTTP e la maggior parte dei servizi Web personalizzati.

	> [AZURE.NOTE]I servizi basati su TCP che usano porte dinamiche, ad esempio la modalità FTP passiva o la modalità passiva estesa, non sono attualmente supportati.

- È possibile usare la funzionalità con tutti i framework supportati da App Web di Azure (.NET, PHP, Java, Python, Node.js) e App per dispositivi mobili di Azure (Node.js, .NET).
- App Web e app per dispositivi mobili possono accedere alle risorse locali come se l'app Web o l'app per dispositivi mobili si trovasse nella rete locale. Ad esempio, è possibile usare la stessa stringa di connessione sia per la rete locale che in Azure.


La funzionalità Connessioni ibride offre inoltre agli amministratori dell'organizzazione controllo e visibilità sulle risorse aziendali a cui accedono le applicazioni ibride:

- Tramite le impostazioni di Criteri di gruppo, gli amministratori possono consentire connessioni ibride alla rete e indicare le risorse a cui le applicazioni ibride possono accedere.
- Log eventi e di controllo sulla rete aziendale offrono visibilità sulle risorse a cui accedono le connessioni ibride.


## Configurazioni supportate

La funzionalità Connessioni ibride supporta le combinazioni di framework e applicazioni seguenti:

- Accesso di .NET Framework a SQL Server
- Accesso di .NET Framework a servizi HTTP/HTTPS con WebClient
- Accesso di PHP a SQL Server e MySQL
- Accesso di Java a SQL Server, MySQL e Oracle
- Accesso di Java a servizi HTTP/HTTPS

Quando si usa la funzionalità Connessioni ibride per accedere a SQL Server locale, tenere presente quanto segue:

- Le istanze denominate di SQL Express devono essere configurate per l'utilizzo di porte statiche. Per impostazione predefinita, le istanze denominate di SQL Express usano le porte dinamiche.
- Le istanze predefinite di SQL Express usano porte statiche, ma è necessario abilitare il protocollo TCP. Per impostazione predefinita, il protocollo TCP non è abilitato.
- Quando si usano i Gruppi di disponibilità o il clustering, la modalità `MultiSubnetFailover=true` non è attualmente supportata.
- La modalità `ApplicationIntent=ReadOnly` non è attualmente supportata.
- Potrebbe essere necessaria l'autenticazione SQL come metodo di autorizzazione end-to-end supportato dall'applicazione Azure e dall'istanza di SQL Server locale.


## Sicurezza e porte

La funzionalità Connessioni ibride usa l'autorizzazione con firma di accesso condiviso per proteggere le connessioni tra le applicazioni Azure e Gestione connessioni ibride locale. Vengono create chiavi di connessione separate per l'applicazione e per Gestione connessioni ibride locale. È possibile eseguire il rollover e revocare queste chiavi di connessione in modo indipendente.

La funzionalità Connessioni ibride garantisce la distribuzione sicura delle chiavi alle applicazioni e a Gestione connessioni ibride locale.

Vedere [Creare e gestire connessioni ibride](integration-hybrid-connection-create-manage.md).

**L'autorizzazione dell'applicazione è separata dalla connessione ibrida**. È possibile usare qualsiasi metodo di autorizzazione appropriato. Il metodo di autorizzazione dipende dai metodi di autorizzazione end-to-end supportati nel cloud di Azure e nei componenti locali. Si supponga, ad esempio, che l'applicazione Azure acceda a un'istanza di SQL Server locale. In questo scenario l'autorizzazione SQL potrebbe costituire il metodo di autorizzazione end-to-end supportato.

#### Porte TCP
La funzionalità Connessioni ibride richiede solo la connettività TCP o HTTP in uscita dalla rete privata. Non è necessario aprire le porte firewall o modificare la configurazione del perimetro di rete per consentire la connettività in ingresso alla rete.

Le seguenti porte TCP vengono usate dalle connessioni ibride:

Porta | Perché sono necessari
--- | ---
9350 - 9354 | Queste porte vengono usate per la trasmissione dei dati. La gestione dell'inoltro del bus di servizio verifica la porta 9350 per determinare se è disponibile la connettività TCP. Se è disponibile, presuppone che sia disponibile anche la porta 9352. Il traffico dati passerà per la porta 9352. <br/><br/>Consentire le connessioni in uscita in queste porte.
5671 | Se la porta 9352 viene usata per il traffico dati, la porta 5671 verrà usata come canale di controllo. <br/><br/>Consentire le connessioni in uscita in questa porta. 
80, 443 | Se le porte 9352 e 5671 non sono utilizzabili, verranno usate le porte 80 e 443 come porte di fallback per la trasmissione dei dati e il canale di controllo.<br/><br/>Consentire le connessioni in uscita in queste porte.<br/><br/>**Nota** Non è consigliabile usare queste porte di fallback al posto delle altre porte TCP. HTTP/WebSocket viene utilizzato come protocollo al posto del protocollo TCP nativo per i canali di dati. Può comportare prestazioni ridotte.



## Avanti

[Creare e gestire connessioni ibride](integration-hybrid-connection-create-manage.md)<br/> [Connettere un sito Web d Azure a una risorsa locale](../web-sites-hybrid-connection-get-started.md)<br/> [Connettersi a un’istanza di SQL Server locale da un’app Web di Azure](../web-sites-hybrid-connection-connect-on-premises-sql-server.md)<br/> [Servizi mobili di Azure e connessioni ibride](../mobile-services-dotnet-backend-hybrid-connections-get-started.md)


## Vedere anche

[API REST per la gestione di servizi BizTalk in Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx) [Servizi BizTalk: tabella delle edizioni](biztalk-editions-feature-chart.md)<br/> [Creare un servizio BizTalk tramite il portale di gestione di Azure](biztalk-provision-services.md)<br/> [Servizi BizTalk: schede Dashboard, Monitor e Scale](biztalk-dashboard-monitor-scale-tabs.md)<br/>

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
[HybridConnectionTab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png

<!---HONumber=62-->