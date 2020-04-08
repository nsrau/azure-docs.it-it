---
title: Funzionalità del gateway applicazione di AzureAzure Application Gateway features
description: Informazioni sulle funzionalità del gateway applicazione di AzureLearn about Azure Application Gateway features
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: victorh
ms.openlocfilehash: f021eed959ef88a1ef3671e1d0ace8080710c92a
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810237"
---
# <a name="azure-application-gateway-features"></a>Funzionalità del gateway applicazione di AzureAzure Application Gateway features

Il [gateway applicazione di Azure](overview.md) è un servizio di bilanciamento del carico del traffico Web che consente di gestire il traffico verso le applicazioni Web.

![Concetti relativi al gateway applicazione](media/overview/figure1-720.png)

Il gateway applicazione include le funzionalità seguenti:Application Gateway includes the following features:

- [Terminazione Secure Sockets Layer (SSL/TLS)](#secure-sockets-layer-ssltls-termination)
- [Scalabilità automatica](#autoscaling)
- [Ridondanza di zona](#zone-redundancy)
- [VIP statico](#static-vip)
- [Web application firewall](#web-application-firewall)
- [Controller di ingresso per il servizio Azure Kubernetes](#ingress-controller-for-aks)
- [Routing basato su URL](#url-based-routing)
- [Hosting di più siti](#multiple-site-hosting)
- [Reindirizzamento](#redirection)
- [Affinità di sessione](#session-affinity)
- [Traffico Websocket e HTTP/2](#websocket-and-http2-traffic)
- [Esaurimento delle connessioni](#connection-draining)
- [Pagine di errore personalizzate](#custom-error-pages)
- [Riscrivere le intestazioni HTTP](#rewrite-http-headers)
- [Ridimensionamento](#sizing)

## <a name="secure-sockets-layer-ssltls-termination"></a>Terminazione Secure Sockets Layer (SSL/TLS)

Il gateway applicazione supporta la terminazione SSL/TLS nel gateway, dopo la quale il traffico scorre generalmente non crittografato verso i server back-end. Questa funzionalità consente ai server Web di non gestire il costoso carico di crittografia e decrittografia. Ma a volte la comunicazione non crittografata con i server non è un'opzione accettabile. Questo può dipendere dai requisiti di sicurezza e conformità o dal fatto che l'applicazione può accettare solo connessioni sicure. Per queste applicazioni, il gateway applicazione supporta la crittografia SSL/TLS end-to-end.

Per ulteriori informazioni, vedere [Panoramica della terminazione SSL e SSL end-to-end con il gateway applicazioneFor](ssl-overview.md) more information, see Overview of SSL termination and end to end SSL with Application Gateway

## <a name="autoscaling"></a>Scalabilità automatica

Il Standard_v2 del gateway applicazione supporta la scalabilità automatica e può aumentare o ridurre le dimensioni in base alla modifica dei modelli di carico del traffico. La scalabilità automatica elimina anche la necessità di scegliere un numero di istanze o le dimensioni della distribuzione durante il provisioning. 

Per ulteriori informazioni sulle funzionalità di Standard_v2 del gateway applicazione, vedere [Autoscaling v2 SKU](application-gateway-autoscaling-zone-redundant.md).

## <a name="zone-redundancy"></a>Ridondanza della zona

Un gateway applicazione Standard_v2 può estendersi su più zone di disponibilità, offrendo una migliore resilienza degli errori e rimuovendo la necessità di eseguire il provisioning di gateway applicazione separati in ogni zona.

## <a name="static-vip"></a>Indirizzo VIP statico

Il gateway applicazione Standard_v2 SKU supporta esclusivamente il tipo VIP statico. Questa funzionalità garantisce che l'indirizzo VIP associato al gateway applicazione non cambi nel corso del ciclo di vita del gateway applicazione.

## <a name="web-application-firewall"></a>Web application firewall

Web Application Firewall (WAF) è un servizio che fornisce protezione centralizzata delle applicazioni Web da exploit e vulnerabilità comuni. WAF si basa su regole dei [set di regole principali OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.1 (solo WAF_v2), 3.0 e 2.2.9. 

Le applicazioni Web sono sempre più vittime di attacchi che sfruttano le più comuni vulnerabilità note. Per citarne alcuni, tra i più comuni troviamo gli attacchi SQL injection e gli attacchi di scripting intersito. Impedire questo tipo di attacchi nel codice dell'applicazione può essere un'operazione complessa e potrebbe richiedere una manutenzione rigorosa, l'applicazione di patch e il monitoraggio a più livelli della topologia dell'applicazione. Un Web application firewall centralizzato semplifica notevolmente la gestione della sicurezza e offre agli amministratori delle applicazioni migliori garanzie contro le minacce o le intrusioni. Una soluzione WAF è anche in grado di reagire più velocemente a una minaccia alla sicurezza tramite l'applicazione di patch su una vulnerabilità nota in una posizione centrale, anziché proteggere ogni singola applicazione Web. I gateway applicazione esistenti possono essere convertiti facilmente in un gateway applicazione abilitato per Web Application Firewall.

Per altre informazioni, vedere [Che cos'è Azure Web Application Firewall?](../web-application-firewall/overview.md).

## <a name="ingress-controller-for-aks"></a>Controller di ingresso per il servizio Azure Kubernetes
Il controller di ingresso del gateway applicazione consente di usare il gateway applicazione come ingresso per un cluster del [servizio Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/). 

Il controller in ingresso viene eseguito come un pod all'interno del cluster AKS e utilizza [Kubernetes Ingress Resources](https://kubernetes.io/docs/concepts/services-networking/ingress/) e le converte in una configurazione del gateway applicazione, che consente al gateway di bilanciare il carico del traffico verso i pod Kubernetes. Il controller di ingresso supporta solo gli SKU gateway applicazione Standard_v2 e WAF_v2. 

Per altre informazioni, vedere [Controller di ingresso del gateway applicazione di Azure](ingress-controller-overview.md).

## <a name="url-based-routing"></a>Routing basato su URL

Il routing basato su percorso URL consente di instradare il traffico a pool di server back-end in base ai percorsi URL della richiesta. Uno degli scenari è il rounting delle richieste di tipi di contenuto diversi a pool diversi.

Ad esempio, per le richieste `http://contoso.com/video/*` viene eseguito il rounting verso VideoServerPool mentre per le richieste `http://contoso.com/images/*` viene eseguito il rounting verso ImageServerPool. In caso di mancata corrispondenza dei percorsi, viene selezionato DefaultServerPool.

Per ulteriori informazioni, consultate [Cenni preliminari sul routing basato sul percorso URL.](url-route-overview.md)

## <a name="multiple-site-hosting"></a>Hosting di più siti

L'hosting di più siti consente di configurare più siti Web nella stessa istanza del gateway applicazione. Questa funzionalità consente di configurare una topologia più efficiente per le distribuzioni aggiungendo fino a 100 siti Web a un gateway applicazione (per prestazioni ottimali). Ogni sito Web può essere indirizzato al proprio pool. Ad esempio, il gateway applicazione può servire il traffico per `contoso.com` e `fabrikam.com` da due pool di server denominati ContosoServerPool e FabrikamServerPool.

Per le richieste `http://contoso.com` viene eseguito il routing verso ContosoServerPool mentre per le richieste `http://fabrikam.com` viene eseguito il routing verso FabrikamServerPool.

Analogamente, la stessa distribuzione del gateway applicazione può ospitare due sottodomini dello stesso dominio padre. Gli esempi di uso di sottodomini possono includere `http://blog.contoso.com` e `http://app.contoso.com` ospitati in una singola distribuzione del gateway applicazione.

Per ulteriori informazioni, vedere [Hosting di più siti del gateway applicazione](multiple-site-overview.md).

## <a name="redirection"></a>Reindirizzamento

Uno scenario comune per molte applicazioni Web è il supporto del reindirizzamento automatico da HTTP a HTTPS per assicurare che tutte le comunicazioni tra l'applicazione e gli utenti avvengano tramite un percorso crittografato.

È possibile che in passato siano state usate tecniche come la creazione di un pool dedicato il cui unico scopo è quello di reindirizzare le richieste ricevute su HTTP ad HTTPS. Il gateway applicazione consente di reindirizzare il traffico sul gateway applicazione. Questo semplifica la configurazione delle applicazioni, ottimizza l'utilizzo delle risorse e supporta i nuovi scenari di reindirizzamento, tra cui il reindirizzamento globale e basato sul percorso. Il supporto del reindirizzamento nel gateway applicazione non è limitato al solo reindirizzamento da HTTP ad HTTPS. Si tratta di un meccanismo di reindirizzamento generico che consente di eseguire il reindirizzamento da e verso qualsiasi porta definita mediante regole. Supporta anche il reindirizzamento a un sito esterno.

Il supporto del reindirizzamento nel gateway applicazione offre le funzionalità seguenti:

- Reindirizzamento globale da una porta a un'altra porta nel gateway. che consente il reindirizzamento da HTTP a HTTPS in un sito.
- Reindirizzamento basato sul percorso. Questo tipo di reindirizzamento consente il reindirizzamento da HTTP a HTTPS solo in un'area specifica del sito, ad esempio l'area del carrello acquisti indicata da `/cart/*`.
- Reindirizzamento a un sito esterno.

Per ulteriori informazioni, vedere Panoramica del [reindirizzamento del gateway applicazione](redirect-overview.md).

## <a name="session-affinity"></a>Affinità di sessione

L'affinità di sessione basata su cookie è utile quando si vuole mantenere una sessione utente nello stesso server. Usando cookie gestiti dal gateway, il gateway applicazione può indirizzare il traffico successivo proveniente da una sessione utente allo stesso server per l'elaborazione. Questo è importante nei casi in cui lo stato della sessione viene salvato in locale sul server per una sessione utente.

Per ulteriori informazioni, vedere [Funzionamento di un gateway applicazione](how-application-gateway-works.md#modifications-to-the-request).

## <a name="websocket-and-http2-traffic"></a>Traffico Websocket e HTTP/2

Il gateway applicazione offre il supporto nativo per i protocolli WebSocket e HTTP/2. Non esistono impostazioni configurabili dall'utente per abilitare o disabilitare in modo selettivo il supporto di WebSocket.

I protocolli WebSocket HTTP/2 consentono una comunicazione full duplex tra un server e un client su una connessione TCP con esecuzione prolungata. Questo consente una comunicazione più interattiva tra il server Web e il client che può essere bidirezionale senza necessità di polling che invece è richiesto nelle implementazioni basate su HTTP. Questi protocolli hanno un overhead basso, a differenza di HTTP, e possono riutilizzare la stessa connessione TCP per più richieste/risposte, con conseguente utilizzo delle risorse più efficiente. Questi protocolli sono progettati per usare le porte HTTP 80 e 443 tradizionali.

Per altre informazioni, vedere [Supporto per WebSocket](application-gateway-websocket.md) e [supporto per HTTP/2](configuration-overview.md#http2-support).

## <a name="connection-draining"></a>Esaurimento delle connessioni

Lo svuotamento delle connessioni aiuta a rimuovere in modo controllato i membri del pool back-end durante gli aggiornamenti pianificati del servizio. Questa modalità viene abilitata tramite l'impostazione http back-end e può essere applicata a tutti i membri di un pool back-end durante la creazione delle regole. Una volta abilitato, il gateway applicazione garantisce che tutte le istanze di annullamento della registrazione di un pool back-end non ricevano alcuna nuova richiesta consentendo il completamento delle richieste esistenti entro un limite di tempo configurato. Questo vale sia per le istanze back-end che vengono rimosse dal pool back-end in modo esplicito mediante una modifica di configurazione dell'utente, sia per le istanze back-end che vengono segnalate come non integre, come determinato dai probe di integrità. L'unica eccezione sono le richieste associate per l'annullamento della registrazione delle istanze, che sono state annullate in modo esplicito, a causa dell'affinità di sessione gestita dal gateway e continuano a essere propigate alle istanze di annullamento della registrazione.

Per ulteriori informazioni, vedere [Cenni preliminari](configuration-overview.md#connection-draining)sulla configurazione del gateway applicazione .

## <a name="custom-error-pages"></a>Pagine di errore personalizzate

Il gateway applicazione consente di creare pagine di errore personalizzate da visualizzare al posto delle pagine di errore predefinite. Se si usa una pagina di errore personalizzata, è possibile usare il proprio layout e marchio aziendali.

Per altre informazioni, vedere [Errori personalizzati](custom-error.md).

## <a name="rewrite-http-headers"></a>Riscrivere le intestazioni HTTP

Le intestazioni HTTP consentono al client e al server di passare informazioni aggiuntive insieme alla richiesta o alla risposta. La riscrittura delle intestazioni HTTP consente di affrontare diversi scenari importanti, ad esempio:

- Aggiunta di campi di intestazione relativi alla sicurezza come HSTS/X-XSS-Protection.
- Rimozione di campi di intestazione della risposta che possono rivelare informazioni riservate.
- Rimozione delle informazioni sulle porte dalle intestazioni X-Forwarded-For.

Il gateway applicazione supporta la possibilità di aggiungere, rimuovere o aggiornare le intestazioni di richieste e risposte HTTP durante lo spostamento dei pacchetti di richiesta e risposta tra il client e i pool back-end. Consente inoltre di aggiungere le condizioni necessarie per garantire che le intestazioni specificate vengono riscritte solo in presenza di determinate condizioni.

Per altre informazioni, vedere [Riscrivere le intestazioni HTTP](rewrite-http-headers.md).

## <a name="sizing"></a>Ridimensionamento

I Standard_v2 del gateway applicazione possono essere configurati per la scalabilità automatica o le distribuzioni a dimensione fissa. Questo SKU non offre dimensioni di istanza diverse. Per altre informazioni sulle prestazioni e sui prezzi di v2, vedere [Ridimensionamento automatico nello SKU v2](application-gateway-autoscaling-zone-redundant.md#pricing).

Lo standard del gateway applicazione è disponibile in tre dimensioni: **Small**, **Medium**e **Large**. Le dimensioni delle istanze piccole sono destinate a scenari di sviluppo e test.

Per un elenco completo dei limiti del gateway applicazione, vedere i [limiti del servizio Gateway applicazione](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

La tabella seguente illustra una velocità effettiva media delle prestazioni per ogni istanza del gateway applicazione v1 con offload SSL abilitato:

| Dimensioni medie risposta della pagina di back-end | Piccolo | Media | large |
| --- | --- | --- | --- |
| 6 KB |7,5 Mbps |13 Mbps |50 Mbps |
| 100 kB |35 Mbps |100 Mbps |200 Mbps |

> [!NOTE]
> Questi valori sono indicazioni approssimative della velocità effettiva di un gateway applicazione. La velocità effettiva dipende da vari dettagli ambientali come le dimensioni medie delle pagine, la posizione delle istanze back-end e il tempo di elaborazione per gestire una pagina. Per dati esatti sulle prestazioni, è consigliabile eseguire propri test. Questi valori vengono forniti solo come indicazioni per la pianificazione della capacità.

## <a name="version-feature-comparison"></a>Confronto delle funzionalità di versione

Per un confronto tra funzionalità del gateway applicazione v1-v2, vedere Scalabilità automatica e gateway applicazione con ridondanza di zona v2For an Application Gateway v1-v2 feature comparison, see [Autoscaling and zona-redundant Application Gateway v2](application-gateway-autoscaling-zone-redundant.md#feature-comparison-between-v1-sku-and-v2-sku)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul funzionamento del gateway applicazione - [Funzionamento di un gateway applicazione](how-application-gateway-works.md)