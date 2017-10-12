---
title: Panoramica delle connessioni ibride | Documentazione Microsoft
description: Informazioni sulle connessioni ibride, sulla sicurezza, sulle porte TCP e sulle configurazioni supportate. MABS, WABS.
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: erikre
editor: 
ms.assetid: 216e4927-6863-46e7-aa7c-77fec575c8a6
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/18/2016
ms.author: ccompy
ms.openlocfilehash: 992c438ab2dac8dc20ba284bd095afbcd801a62f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="hybrid-connections-overview"></a>Panoramica delle connessioni ibride

> [!IMPORTANT]
> La funzionalità Connessioni ibride di BizTalk è stata ritirata e sostituita dalla funzionalità Connessioni ibride del Servizio app. Per altre informazioni, ad esempio come gestire le connessioni ibride di BizTalk esistenti, vedere [Connessioni ibride del Servizio app di Azure](../app-service/app-service-hybrid-connections.md).

Presenta la funzionalità Connessioni ibride, descrive le configurazioni supportate e indica le porte TCP necessarie.

## <a name="what-is-a-hybrid-connection"></a>Informazioni sulle connessioni ibride
La funzionalità Connessioni ibride è inclusa in Servizi BizTalk di Azure. La funzionalità Connessioni ibride consente di connettere in modo facile e intuitivo la funzione App Web di Azure App Service (in precedenza siti Web) e la funzione App per dispositivi mobili di Azure App Service (in precedenza Servizi mobili) alle risorse locali protette da firewall.

![connessioni ibride][HCImage]

Vantaggi della funzionalità Connessioni ibride:

* App Web e app per dispositivi mobili possono accedere ai dati e ai servizi locali esistenti in modo sicuro.
* Più app Web o app per dispositivi mobili possono condividere una connessione ibrida per accedere a una risorsa locale.
* Per accedere alla rete, è necessario un numero minimo di porte TCP.
* Le applicazioni che usano la funzionalità Connessioni ibride accedono solo alla risorsa locale specifica pubblicata tramite la connessione ibrida.
* È possibile connettere qualsiasi risorsa locale che usa una porta TCP statica, ad esempio SQL Server, MySQL, API Web HTTP e la maggior parte dei servizi Web personalizzati.
  
  > [!NOTE]
  > I servizi basati su TCP che usano porte dinamiche, ad esempio la modalità FTP passiva o la modalità passiva estesa, non sono attualmente supportati. Anche LDAP non è supportato. LDAP usa una porta TCP statica, ma può anche usare UDP. Di conseguenza, non è supportato.
  > 
  > 
* È possibile usare la funzionalità con tutti i framework supportati da App Web (.NET, PHP, Java, Python, Node.js) e App per dispositivi mobili (Node.js, .NET).
* App Web e app per dispositivi mobili possono accedere alle risorse locali come se l'app Web o l'app per dispositivi mobili si trovasse nella rete locale. Ad esempio, è possibile usare la stessa stringa di connessione sia per la rete locale che in Azure.

La funzionalità Connessioni ibride offre inoltre agli amministratori dell'organizzazione controllo e visibilità sulle risorse aziendali a cui accedono le applicazioni ibride:

* Tramite le impostazioni di Criteri di gruppo, gli amministratori possono consentire connessioni ibride alla rete e indicare le risorse a cui le applicazioni ibride possono accedere.
* Log eventi e di controllo sulla rete aziendale offrono visibilità sulle risorse a cui accedono le connessioni ibride.

## <a name="example-scenarios"></a>Scenari di esempio
La funzionalità Connessioni ibride supporta le combinazioni di framework e applicazioni seguenti:

* Accesso di .NET Framework a SQL Server
* Accesso di .NET Framework a servizi HTTP/HTTPS con WebClient
* Accesso di PHP a SQL Server e MySQL
* Accesso di Java a SQL Server, MySQL e Oracle
* Accesso di Java a servizi HTTP/HTTPS

Quando si usa la funzionalità Connessioni ibride per accedere a SQL Server locale, tenere presente quanto segue:

* Le istanze denominate di SQL Express devono essere configurate per l'utilizzo di porte statiche. Per impostazione predefinita, le istanze denominate di SQL Express usano le porte dinamiche.
* Le istanze predefinite di SQL Express usano porte statiche, ma è necessario abilitare il protocollo TCP. Per impostazione predefinita, il protocollo TCP non è abilitato.
* Quando si usano i Gruppi di disponibilità o il clustering, la modalità `MultiSubnetFailover=true` non è attualmente supportata.
* La modalità `ApplicationIntent=ReadOnly` non è attualmente supportata.
* Potrebbe essere necessaria l'autenticazione SQL come metodo di autorizzazione end-to-end supportato dall'applicazione Azure e dall'istanza di SQL Server locale.

## <a name="security-and-ports"></a>Sicurezza e porte
La funzionalità Connessioni ibride usa l'autorizzazione con firma di accesso condiviso per proteggere le connessioni tra le applicazioni Azure e Gestione connessioni ibride locale. Vengono create chiavi di connessione separate per l'applicazione e per Gestione connessioni ibride locale. È possibile eseguire il rollover e revocare queste chiavi di connessione in modo indipendente.

La funzionalità Connessioni ibride garantisce la distribuzione sicura delle chiavi alle applicazioni e a Gestione connessioni ibride locale.

Vedere [Creare e gestire connessioni ibride](integration-hybrid-connection-create-manage.md).

*L'autorizzazione dell'applicazione è separata dalla connessione ibrida*. È possibile usare qualsiasi metodo di autorizzazione appropriato. Il metodo di autorizzazione dipende dai metodi di autorizzazione end-to-end supportati nel cloud di Azure e nei componenti locali. Si supponga, ad esempio, che l'applicazione Azure acceda a un'istanza di SQL Server locale. In questo scenario l'autorizzazione SQL potrebbe costituire il metodo di autorizzazione end-to-end supportato.

#### <a name="tcp-ports"></a>Porte TCP
La funzionalità Connessioni ibride richiede solo la connettività TCP o HTTP in uscita dalla rete privata. Non è necessario aprire le porte firewall o modificare la configurazione del perimetro di rete per consentire la connettività in ingresso alla rete.

Le seguenti porte TCP vengono usate dalle connessioni ibride:

| Porta | Perché sono necessari |
| --- | --- |
| 9350 - 9354 |Queste porte vengono usate per la trasmissione dei dati. La gestione dell'inoltro del bus di servizio verifica la porta 9350 per determinare se è disponibile la connettività TCP. Se è disponibile, presuppone che sia disponibile anche la porta 9352. Il traffico dati passerà per la porta 9352. <br/><br/>Consentire le connessioni in uscita in queste porte. |
| 5671 |Quando la porta 9352 viene utilizzata per il traffico dati, la porta 5671 viene utilizzata come il canale di controllo. <br/><br/>Consentire le connessioni in uscita in questa porta. |
| 80, 443 |Queste porte vengono usate per alcune richieste di dati in Azure. Inoltre, se le porte 9352 e 5671 non possono essere impiegate, *allora*verranno usate le porte 80 e 443 come porte di fallback per la trasmissione dei dati e il canale di controllo.<br/><br/>Consentire le connessioni in uscita in queste porte. <br/><br/>**Nota** Non è consigliabile usare queste porte come porte di fallback al posto delle altre porte TCP. HTTP/WebSocket viene utilizzato come protocollo al posto del protocollo TCP nativo per i canali di dati. Può comportare prestazioni ridotte. |

## <a name="next-steps"></a>Passaggi successivi
[Create and manage Hybrid Connections](integration-hybrid-connection-create-manage.md)<br/>

## <a name="see-also"></a>Vedere anche
[REST API for Managing BizTalk Services on Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)
 (API REST per la gestione dei servizi BizTalk in Microsoft Azure)[Servizi BizTalk: Grafico edizioni](biztalk-editions-feature-chart.md)<br/>
[Creazione di servizi BizTalk tramite il portale di Azure](biztalk-provision-services.md)<br/>
[Servizi BizTalk: schede Dashboard, Monitora e Scala](biztalk-dashboard-monitor-scale-tabs.md)<br/>

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
