---
title: Sicurezza in Servizio app di Azure e Funzioni di Azure | Microsoft Docs
description: Informazioni su come Servizio app di Azure consente di proteggere l'app e su come è possibile proteggere ulteriormente l'app dalle minacce.
keywords: servizio app di azure, app web, app per dispositivi mobili, app per le api, app per le funzioni, sicurezza, proteggere, protetto, conformità, conforme, certificato, certificati, https, ftps, tls, attendibile, crittografia, crittografare, crittografato, restrizione ip, autenticazione, autorizzazione, msi, identità del servizio gestita, segreti, segreto, applicazione di patch, patch, versione, isolamento, isolamento rete, ddos, mitm
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2018
ms.author: cephalin
ms.openlocfilehash: 40fdd22bdbb3fc0676688430069d58c0422a7ca2
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382117"
---
# <a name="security-in-azure-app-service-and-azure-functions"></a>Sicurezza in Servizio app di Azure e Funzioni di Azure

Questo articolo illustra come [Servizio app di Azure](app-service-web-overview.md) consente di proteggere l'app Web, il back-end dell'app per dispositivi mobili, l'app per le API e [Funzioni di Azure](/azure/azure-functions/). Spiega inoltre come è possibile proteggere ulteriormente l'app con le funzionalità incorporate di Servizio app di Azure.

Ai componenti della piattaforma di Servizio app di Azure, incluse le macchine virtuali di Azure, l'archiviazione, le connessioni di rete, i framework Web e le funzionalità di gestione e integrazione, viene applicata attivamente una protezione avanzata. Servizio app di Azure viene sottoposto continuamente a rigorosi controlli della conformità per assicurarsi che:

- Le risorse dell'app siano [protette](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) dalle risorse di Azure degli altri clienti.
- [Le istanze di macchine virtuali e il software di runtime vengano regolarmente aggiornati](app-service-patch-os-runtime.md) in modo da contrastare le vulnerabilità appena individuate. 
- La comunicazione di segreti (ad esempio stringhe di connessione) tra l'app e altre risorse di Azure (ad esempio [database SQL](https://azure.microsoft.com/services/sql-database/)) rimanga all'interno di Azure e non superi i confini di rete. I segreti siano sempre crittografati quando vengono archiviati.
- Tutte le comunicazioni tramite le funzionalità di connettività di Servizio app di Azure, ad esempio la [connessione ibrida](app-service-hybrid-connections.md), siano crittografate. 
- Tutte le connessioni con strumenti di gestione remota, come Azure PowerShell, l'interfaccia della riga di comando di Azure, Azure SDK e le API REST, siano crittografate.
- La gestione delle minacce 24 ore su 24 protegga l'infrastruttura e la piattaforma da malware, attacchi Distributed Denial of Service (DDoS), attacchi man-in-the-middle (MITM) e altre minacce.

Per altre informazioni sulla sicurezza della piattaforma e dell'infrastruttura in Azure, vedere [Centro protezione di Azure](https://azure.microsoft.com/overview/trusted-cloud/).

Nelle sezioni seguenti viene illustrato come rafforzare la protezione dell'app del servizio app dalle minacce.

## <a name="https-and-certificates"></a>HTTPS e certificati

Servizio app di Azure consente di proteggere le app con [HTTPS](https://wikipedia.org/wiki/HTTPS). Al momento della creazione, il nome di dominio predefinito dell'app (\<nome_app >.azurewebsites.net) è già accessibile tramite HTTPS. Se si [configura un dominio personalizzato per l'app](app-service-web-tutorial-custom-domain.md), è opportuno anche [proteggerlo con un certificato personalizzato](app-service-web-tutorial-custom-ssl.md) in modo che i browser client possano eseguire connessioni HTTPS protette al dominio personalizzato. È possibile eseguire questa operazione in due modi:

- **Certificato del servizio app**:-creare un certificato direttamente in Azure. Il certificato è protetto in [Azure Key Vault](/azure/key-vault/)e può essere importato nell'app del servizio app. Per altre informazioni, vedere [Acquistare e configurare un certificato SSL per il servizio app di Azure](web-sites-purchase-ssl-web-site.md).
- **Certificato di terze parti**: caricare un certificato SSL personalizzato acquistato da un'autorità di certificazione attendibile e associarlo all'app del servizio app. Servizio app di Azure supporta sia i certificati per singoli domini che i certificati con caratteri jolly. Supporta inoltre i certificati autofirmati a scopo di test. Per altre informazioni, vedere [Associare un certificato SSL personalizzato esistente ad app Web di Azure](app-service-web-tutorial-custom-ssl.md).

## <a name="insecure-protocols-http-tls-10-ftp"></a>Protocolli non sicuri (HTTP, TLS 1.0, FTP)

Per proteggere l'app da tutte le connessioni non crittografate (HTTP), Servizio app di Azure fornisce una configurazione che consente di applicare HTTPS con un solo clic. Le richieste non sicure vengono rifiutate prima ancora che raggiungano il codice dell'applicazione. Per altre informazioni, vedere [Applicare HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https).

[TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 non è più considerato sicuro dagli standard del settore, come [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Servizio app di Azure consente di disabilitare i protocolli non aggiornati [applicando TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions).

Il servizio app supporta FTP e FTPS per la distribuzione dei file. Se possibile, però, è opportuno usare FTPS invece di FTP. Quando uno o entrambi questi protocolli non sono in uso, è necessario [disabilitarli](app-service-deploy-ftp.md#enforce-ftps).

## <a name="static-ip-restrictions"></a>Limitazioni relative agli indirizzi IP statici

Per impostazione predefinita, l'app del servizio app accetta le richieste da tutti gli indirizzi IP da Internet, ma è possibile limitare tale accesso a un sottoinsieme ristretto di indirizzi IP. Il servizio app in Windows consente di definire un elenco di indirizzi IP che possono accedere all'app. Questo elenco può includere singoli indirizzi IP o un intervallo di indirizzi IP definito da una subnet mask. Per altre informazioni, vedere [Restrizioni IP statico del Servizio app di Azure](app-service-ip-restrictions.md).

Per il servizio app in Windows è inoltre possibile limitare gli indirizzi IP dinamicamente tramite la configurazione del file _web.config_. Per altre informazioni, vedere [Dynamic IP Security<dynamicIpSecurity>](https://docs.microsoft.com/iis/configuration/system.webServer/security/dynamicIpSecurity/) (sicurezza degli IP dinamici).

## <a name="client-authentication-and-authorization"></a>Autenticazione e autorizzazione dei client

Servizio app di Azure fornisce una soluzione chiavi in mano di autenticazione e autorizzazione di utenti o app client. Quando abilitata, questa soluzione consente l'accesso di utenti e app client con un utilizzo del codice dell'applicazione minimo o nullo. È possibile implementare una soluzione di autenticazione e autorizzazione a scelta o lasciar gestire automaticamente questo processo al servizio app. Il modulo di autenticazione e autorizzazione gestisce le richieste Web prima di passarle al codice dell'applicazione e rifiuta le richieste non autorizzate prima che raggiungano il codice.

La soluzione di autorizzazione e autenticazione del servizio app supporta più provider di autenticazione, tra cui Azure Active Directory, gli account Microsoft, Facebook, Google e Twitter. Per altre informazioni, vedere [Autenticazione e autorizzazione nel servizio app di Azure](app-service-authentication-overview.md).

## <a name="service-to-service-authentication"></a>Autenticazione da servizio a servizio

Per eseguire l'autenticazione con un servizio back-end, il servizio app fornisce due meccanismi diversi a seconda delle esigenze:

- **Identità del servizio**: si accede alla risorsa remota usando l'identità dell'app stessa. Il servizio app consente di creare facilmente un'[identità del servizio gestita](app-service-managed-service-identity.md), che può essere usata per eseguire l'autenticazione con altri servizi, come [Database SQL di Azure](/azure/sql-database/) o [Azure Key Vault](/azure/key-vault/). Per un'esercitazione completa di questo approccio, vedere [Proteggere la connessione al database SQL di Azure dal servizio app con un'identità del servizio gestita](app-service-web-tutorial-connect-msi.md).
- **OBO (On-behalf-of)**: è possibile impostare l'accesso delegato alle risorse remote per conto dell'utente. Con Azure Active Directory come provider di autenticazione, l'app del servizio app può eseguire l'accesso delegato a un servizio remoto, come l'[API Graph di Azure Active Directory](../active-directory/develop/active-directory-graph-api.md) o un'app per le API remota nel servizio app. Per un'esercitazione completa di questo approccio, vedere [Autenticare e autorizzare gli utenti end-to-end nel servizio app di Azure](app-service-web-tutorial-auth-aad.md).

## <a name="connectivity-to-remote-resources"></a>Connettività alle risorse remote

Esistono tre tipi di risorse remote a cui l'app potrebbe dover accedere: 

- [Risorse di Azure](#azure-resources)
- [Risorse all'interno di una rete virtuale di Azure](#resources-inside-an-azure-virtual-network)
- [Risorse locali](#on-premises-resources)

In ognuno di questi casi, il servizio app fornisce un modo per stabilire connessioni sicure, ma è comunque necessario osservare le procedure consigliate per la sicurezza. Ad esempio, usare sempre connessioni crittografate anche se la risorsa back-end consente le connessioni non crittografate. Inoltre, assicurarsi che il servizio back-end di Azure accetti il set minimo di indirizzi IP. Gli indirizzi IP in uscita per l'app sono disponibili in [Indirizzi IP in ingresso e in uscita in Servizio app di Azure](app-service-ip-addresses.md).

### <a name="azure-resources"></a>Risorse di Azure

Quando l'app si connette a risorse di Azure, ad esempio [Database SQL](https://azure.microsoft.com/services/sql-database/) e [Archiviazione di Azure](/azure/storage/), la connessione rimane all'interno di Azure e non supera i confini di rete. Tuttavia, la connessione passa attraverso le reti condivise di Azure, pertanto occorre sempre assicurarsi che la connessione sia crittografata. 

Se l'app è ospitata in un [ambiente del servizio app](environment/intro.md), è necessario [connettersi a servizi di Azure supportati usando gli endpoint del servizio Rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md).

### <a name="resources-inside-an-azure-virtual-network"></a>Risorse all'interno di una rete virtuale di Azure

L'app può accedere alle risorse in una [rete virtuale di Azure](/azure/virtual-network/) tramite l'[integrazione della rete virtuale](web-sites-integrate-with-vnet.md). L'integrazione con una rete virtuale viene stabilita usando una VPN da punto a sito. L'app può quindi accedere alle risorse nella rete virtuale usando i rispettivi indirizzi IP privati. La connessione da punto a sito, tuttavia, attraversa ancora le reti condivise in Azure. 

Per isolare completamente la connettività delle risorse dalle reti condivise in Azure, creare l'app in un [ambiente del servizio app](environment/intro.md). Poiché un ambiente del servizio app viene sempre distribuito in una rete virtuale dedicata, la connettività tra l'app e le risorse all'interno della rete virtuale è completamente isolata. Per informazioni su altri aspetti della sicurezza di rete in un ambiente del servizio app, vedere [Isolamento rete](#network-isolation).

### <a name="on-premises-resources"></a>Risorse locali

È possibile accedere in modo sicuro alle risorse locali, ad esempio database, in tre modi: 

- [Connessioni ibride](app-service-hybrid-connections.md), che stabiliscono una connessione da punto a punto alla risorsa remota tramite un tunnel TCP. Il tunnel TCP viene stabilito tramite TLS 1.2 con chiavi di firma di accesso condiviso (SAS).
- [Integrazione della rete virtuale](web-sites-integrate-with-vnet.md) con VPN da sito a sito, come descritto in [Risorse all'interno di una rete virtuale di Azure](#resources-inside-an-azure-virtual-network), ma la rete virtuale può essere connessa alla rete locale tramite una [VPN da sito a sito](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). In questa topologia di rete l'app può connettersi alle risorse locali, ad esempio ad altre risorse nella rete virtuale.
- [Ambiente del servizio app](environment/intro.md) con VPN da sito a sito, come descritto in [Risorse all'interno di una rete virtuale di Azure](#resources-inside-an-azure-virtual-network), ma la rete virtuale può essere connessa alla rete locale tramite una [VPN da sito a sito](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). In questa topologia di rete l'app può connettersi alle risorse locali, ad esempio ad altre risorse nella rete virtuale.

## <a name="application-secrets"></a>Segreti dell'applicazione

Non archiviare i segreti dell'applicazione, come le credenziali del database, i token API e le chiavi private, nel codice o nei file di configurazione. L'approccio comunemente accettato consiste nell'accedere ai segreti come [variabili di ambiente](https://wikipedia.org/wiki/Environment_variable) usando il modello standard nel linguaggio preferito. Nel servizio app, il modo per definire le variabili di ambiente è attraverso [le impostazioni dell'app](web-sites-configure.md#app-settings) (e, in particolare per le applicazioni .NET, le [stringhe di connessione](web-sites-configure.md#connection-strings)). Le impostazioni dell'app e le stringhe di connessione vengono archiviate in Azure in formato crittografato e vengono decrittografate solo prima di essere inserite nella memoria processi dell'app all'avvio di quest'ultima. Le chiavi di crittografia vengono sottoposte a rotazione regolarmente.

In alternativa, è possibile integrare l'app del servizio app con [Azure Key Vault](/azure/key-vault/) per la gestione avanzata dei segreti. [Accedendo all'insieme di credenziali delle chiavi con un'identità del servizio gestita](../key-vault/tutorial-web-application-keyvault.md), l'app del servizio app può accedere in modo sicuro ai segreti di cui ha bisogno.

## <a name="network-isolation"></a>Isolamento rete

Fatta eccezione per il piano tariffario **Isolato**, tutti i piani eseguono le app nell'infrastruttura di rete condivisa del servizio app. Ad esempio, gli indirizzi IP pubblici e i servizi di bilanciamento del carico di front-end vengono condivisi con altri tenant. Il piano **Isolato** offre un isolamento di rete completo mediante l'esecuzione delle app all'interno di un [ambiente del servizio app](environment/intro.md) dedicato. Un ambiente del servizio app viene eseguito nell'istanza di [rete virtuale di Azure](/azure/virtual-network/) dell'utente. Consente di: 

- Limitare l'accesso alla rete con [gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md). 
- Servire le app tramite un endpoint pubblico dedicato, con front-end dedicati.
- Servire le applicazioni interne mediante un servizio di bilanciamento del carico interno, che consente l'accesso solo dall'interno della rete virtuale di Azure. Questo servizio ha un indirizzo IP della subnet privata, che offre un isolamento totale delle app da Internet.
- [Usare un servizio di bilanciamento del carico interno dietro un WAF (Web Application Firewall)](environment/integrate-with-application-gateway.md). Il WAF offre una protezione a livello aziendale per le applicazioni pubbliche, fornendo funzionalità come la protezione DDoS, il filtro degli URI e la prevenzione di attacchi SQL injection.

Per altre informazioni, vedere [Introduzione agli ambienti del servizio app di Azure](environment/intro.md). 
