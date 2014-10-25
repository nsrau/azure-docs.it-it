<properties linkid="manage-services-integration-hybrid-connection" urlDisplayName="Hybrid Connections Overview - BizTalk Services" pageTitle="Hybrid Connections Overview | Azure" metaKeywords="BizTalk Services, BizTalk, websites, web sites, hybrid connections, Azure" description="Learn about hybrid connections, including Security." metaCanonical="" services="integration-services" documentationCenter="" title="Hybrid Connections Overview" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="mandia"></tags>

# Panoramica delle connessioni ibride

Questo argomento presenta la funzionalità Connessioni ibride, descrive le configurazioni supportate e indica le porte TCP necessarie. In particolare:

-   [Informazioni sulle connessioni ibride][Informazioni sulle connessioni ibride]
-   [Configurazioni supportate][Configurazioni supportate]
-   [Sicurezza][Sicurezza]

## <a name="HCOverview"></a>Informazioni sulle connessioni ibride

La funzionalità Connessioni ibride consente di connettere siti Web di Azure e servizi mobili di Azure alle risorse locali in modo facile e intuitivo. La funzionalità Connessioni ibride è inclusa in Servizi BizTalk di Azure:

![Connessioni ibride][Connessioni ibride]

Vantaggi della funzionalità Connessioni ibride:

-   Siti Web e servizi mobili possono accedere ai dati e ai servizi locali esistenti in modo sicuro.
-   Più siti Web o servizi mobili possono condividere una connessione ibrida per accedere a una risorsa locale.
-   Per accedere alla rete, è necessario un numero minimo di porte TCP.
-   Le applicazioni che usano la funzionalità Connessioni ibride accedono solo alla risorsa locale specifica pubblicata tramite la connessione ibrida.
-   È possibile connettere qualsiasi risorsa locale che usa una porta TCP statica, ad esempio SQL Server, MySQL, API Web HTTP e la maggior parte dei servizi Web personalizzati.

> [WACOM.NOTE] I servizi basati su TCP che usano porte dinamiche, ad esempio la modalità FTP passiva o la modalità passiva estesa, non sono attualmente supportati.

-   È possibile usare la funzionalità con tutti i framework supportati da Siti Web di Azure (.NET, PHP, Java, Python, Node.js) e da Servizi mobili di Azure (Node.js, .NET).
-   Siti Web e servizi mobili possono accedere alle risorse locali come se il sito Web o il servizio mobile si trovasse nella rete locale. Ad esempio, è possibile usare la stessa stringa di connessione sia per la rete locale che in Azure.

La funzionalità Connessioni ibride offre inoltre agli amministratori dell'organizzazione controllo e visibilità sulle risorse aziendali a cui accedono le applicazioni ibride:

-   Tramite le impostazioni di Criteri di gruppo, gli amministratori possono consentire connessioni ibride alla rete e indicare le risorse a cui le applicazioni ibride possono accedere.
-   Log eventi e di controllo sulla rete aziendale offrono visibilità sulle risorse a cui accedono le connessioni ibride.

## <a name="KnownIssues"></a>Configurazioni supportate

La funzionalità Connessioni ibride supporta le combinazioni di framework e applicazioni seguenti:

-   Accesso di .NET Framework a SQL Server
-   Accesso di .NET Framework a servizi HTTP/HTTPS con WebClient
-   Accesso di PHP a SQL Server e MySQL
-   Accesso di Java a SQL Server, MySQL e Oracle
-   Accesso di Java a servizi HTTP/HTTPS

Quando si usa la funzionalità Connessioni ibride per accedere a SQL Server locale, tenere presente quanto segue:

-   Le istanze denominate di SQL Express devono essere configurate per l'utilizzo di porte statiche. Per impostazione predefinita, le istanze denominate di SQL Express usano le porte dinamiche.
-   Le istanze predefinite di SQL Express usano porte statiche, ma è necessario abilitare il protocollo TCP. Per impostazione predefinita, il protocollo TCP non è abilitato.
-   Quando si usano i Gruppi di disponibilità o il clustering, la modalità MultiSubnetFailover=true non è attualmente supportata.
-   La modalità ApplicationIntent=ReadOnly non è attualmente supportata.
-   Potrebbe essere necessaria l'autenticazione SQL come metodo di autorizzazione end-to-end supportato dall'applicazione Azure e dall'istanza di SQL Server locale.

## <a name="HCSecurity"></a>Sicurezza

La funzionalità Connessioni ibride usa l'autorizzazione con firma di accesso condiviso per proteggere le connessioni tra le applicazioni Azure e Gestione connessioni ibride locale. Vengono create chiavi di connessione separate per l'applicazione e per Gestione connessioni ibride locale. È possibile eseguire il rollover e revocare queste chiavi di connessione in modo indipendente.

La funzionalità Connessioni ibride garantisce la distribuzione sicura delle chiavi alle applicazioni e a Gestione connessioni ibride locale.

Vedere [Creare e gestire connessioni ibride][Creare e gestire connessioni ibride].

**L'autorizzazione dell'applicazione è separata dalla connessione ibrida**. È possibile usare qualsiasi metodo di autorizzazione appropriato. Il metodo di autorizzazione dipende dai metodi di autorizzazione end-to-end supportati nel cloud di Azure e nei componenti locali. Si supponga, ad esempio, che l'applicazione Azure acceda a un'istanza di SQL Server locale. In questo scenario l'autorizzazione SQL potrebbe costituire il metodo di autorizzazione end-to-end supportato.

#### Porte TCP

| **Porta** | Motivo                                                                                                      |
|-----------|-------------------------------------------------------------------------------------------------------------|
| 80        | Porta HTTP. Usata per la convalida del certificato.                                                         |
| 443       | Porta HTTPS                                                                                                 |
| 5671      | Usata per la connessione ad Azure. Se la porta TCP 5671 non è disponibile, viene usata la porta TCP 443.    |
| 9352      | Usata per il push e il pull dei dati. Se la porta TCP 9352 non è disponibile, viene usata la porta TCP 443. |

## Passaggi successivi

-   [Creare e gestire connessioni ibride][Creare e gestire connessioni ibride]
-   [Connettere un sito Web di Azure a una risorsa locale][Connettere un sito Web di Azure a una risorsa locale]
-   [Procedura dettagliata relativa a Connessioni ibride: Connettersi a un'istanza di SQL Server locale da un sito Web di Azure][Procedura dettagliata relativa a Connessioni ibride: Connettersi a un'istanza di SQL Server locale da un sito Web di Azure]
-   [Servizi mobili di Azure e Connessioni ibride][Servizi mobili di Azure e Connessioni ibride]

## Vedere anche

-   [API REST per la gestione dei servizi BizTalk in Microsoft Azure][API REST per la gestione dei servizi BizTalk in Microsoft Azure]
-   [Servizi BizTalk: Grafico edizioni][Servizi BizTalk: Grafico edizioni]
-   [Creare un servizio BizTalk mediante il portale di gestione di Azure][Creare un servizio BizTalk mediante il portale di gestione di Azure]
-   [Servizi BizTalk: schede Dashboard, Monitor e Scale][Servizi BizTalk: schede Dashboard, Monitor e Scale]

  [Informazioni sulle connessioni ibride]: #HCOverview
  [Configurazioni supportate]: #KnownIssues
  [Sicurezza]: #HCSecurity
  [Connessioni ibride]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
  [Creare e gestire connessioni ibride]: http://azure.microsoft.com/it-it/documentation/articles/integration-hybrid-connection-create-manage
  [Connettere un sito Web di Azure a una risorsa locale]: http://go.microsoft.com/fwlink/p/?LinkId=397538
  [Procedura dettagliata relativa a Connessioni ibride: Connettersi a un'istanza di SQL Server locale da un sito Web di Azure]: http://go.microsoft.com/fwlink/?LinkID=397979
  [Servizi mobili di Azure e Connessioni ibride]: http://azure.microsoft.com/it-it/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started
  [API REST per la gestione dei servizi BizTalk in Microsoft Azure]: http://msdn.microsoft.com/library/azure/dn232347.aspx
  [Servizi BizTalk: Grafico edizioni]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [Creare un servizio BizTalk mediante il portale di gestione di Azure]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [Servizi BizTalk: schede Dashboard, Monitor e Scale]: http://go.microsoft.com/fwlink/p/?LinkID=302281
