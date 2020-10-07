---
title: Panoramica
description: Informazioni sul modo in cui il Servizio app di Azure semplifica lo sviluppo e l'hosting di applicazioni Web
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.topic: overview
ms.date: 07/06/2020
ms.custom: devx-track-dotnet, mvc, seodec18
ms.openlocfilehash: 1b72224441741990a1fc94400dfe718ea9d1b0b3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "88961483"
---
# <a name="app-service-overview"></a>Panoramica del Servizio app di Azure

*Il Servizio app di Azure* è un servizio per l'hosting di applicazioni Web, API REST e back-end mobili, basato su HTTP. È possibile usare il linguaggio di sviluppo preferito tra .NET, .NET Core, Java, Ruby, Node.js, PHP o Python. Le applicazioni vengono eseguite e dimensionate con facilità negli ambienti basati sia su Windows che su [Linux](#app-service-on-linux).

Il servizio app non si limita ad aggiungere la potenza di Microsoft Azure all'applicazione, in termini di sicurezza, bilanciamento del carico, ridimensionamento automatico e gestione automatizzata. Permettono anche di usufruire delle funzionalità DevOps, come la distribuzione continua da Azure DevOps, GitHub, Docker Hub e altre origini, la gestione dei pacchetti, gli ambienti di staging, il dominio personalizzato e i certificati TLS/SSL. 

Il servizio app permette di pagare solo le risorse di calcolo di Azure usate. La quantità di risorse di calcolo usate è determinata dal _piano di servizio app_ in cui vengono eseguite le app. Per altre informazioni, vedere [Panoramica approfondita dei piani del servizio app di Azure](overview-hosting-plans.md).

## <a name="why-use-app-service"></a>Perché usare il servizio app?

Ecco alcune delle funzionalità principali del servizio app:

* **Più linguaggi e framework**: il servizio app offre un supporto eccellente per ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP o Python. È anche possibile eseguire [PowerShell e altri script o eseguibili](webjobs-create.md) come servizi in background.
* **Ambiente di produzione gestito**: il servizio app [applica automaticamente le patch e mantiene i framework del sistema operativo e del linguaggio](overview-patch-os-runtime.md). In questo modo è possibile dedicarsi alla scrittura di app senza preoccuparsi della piattaforma.
* **Containerizzazione e Docker**: consente di ottimizzare l'app per Docker e ospitare un contenitore Windows o Linux personalizzato nel servizio app. Esecuzione di app multi-contenitore con Docker Compose. Migrazione delle competenze Docker direttamente nel servizio app.
* **Ottimizzazione della metodologia DevOps**: è possibile configurare [l'integrazione continua e la distribuzione continua](deploy-continuous-deployment.md) con Azure DevOps, GitHub, BitBucket, Hub Docker o Registro Azure Container, alzare di livello gli aggiornamenti tramite [ambienti di testing e di staging](deploy-staging-slots.md), e gestire le app nel servizio app con [Azure PowerShell](/powershell/azure/) o l'[interfaccia della riga di comando multipiattaforma](/cli/azure/install-azure-cli).
* **Scalabilità globale con disponibilità elevata**: è possibile [aumentare le prestazioni](manage-scale-up.md) o il [numero di istanze](../azure-monitor/platform/autoscale-get-started.md) manualmente o automaticamente. Le app possono essere ospitate ovunque nell'infrastruttura globale di data center Microsoft e il [contratto di servizio](https://azure.microsoft.com/support/legal/sla/app-service/) per il servizio app assicura disponibilità elevata.
* **Connessioni a piattaforme SaaS e dati locali**: è possibile scegliere tra oltre 50 [connettori](../connectors/apis-list.md) per sistemi aziendali (come SAP), servizi SaaS (come Salesforce) e servizi Internet (come Facebook), nonché accedere ai dati locali con [connessioni ibride](app-service-hybrid-connections.md) e [reti virtuali di Azure](web-sites-integrate-with-vnet.md).
* **Sicurezza e conformità** : il servizio app è [conforme a ISO, SOC e PCI](https://www.microsoft.com/en-us/trustcenter). Autenticazione degli utenti con [Azure Active Directory](configure-authentication-provider-aad.md), [Google](configure-authentication-provider-google.md), [Facebook](configure-authentication-provider-facebook.md), [Twitter](configure-authentication-provider-twitter.md) o [account Microsoft](configure-authentication-provider-microsoft.md). Creare [restrizioni per gli indirizzi IP](app-service-ip-restrictions.md) e [gestire le identità del servizio](overview-managed-identity.md).
* **Modelli di applicazione**: in [Azure Marketplace](https://azure.microsoft.com/marketplace/) è possibile scegliere da un esteso elenco di modelli di applicazione, come WordPress, Joomla e Drupal.
* **Integrazione con Visual Studio e Visual Studio Code**: gli strumenti dedicati di Visual Studio e Visual Studio Code semplificano il processo di creazione, distribuzione e debug.
* **API e funzionalità mobili**: il servizio app offre un supporto CORS pronto all'uso per gli scenari API RESTful. Inoltre, semplifica gli scenari di app per dispositivi mobili consentendo l'autenticazione, la sincronizzazione dei dati offline, le notifiche push e altro ancora.
* **Codice senza server**:è possibile eseguire un frammento di codice o uno script su richiesta senza dover eseguire il provisioning esplicito o la gestione dell'infrastruttura e pagare solo il tempo di calcolo usato effettivamente dal codice. Vedere in proposito [Funzioni di Azure](../azure-functions/index.yml).

Oltre al servizio app, Azure offre altri servizi che possono essere usati per l'hosting di siti e applicazioni Web. Per la maggior parte degli scenari il servizio app è la scelta migliore.  Per un'architettura di microservizi, valutare se usare il [servizio Azure Spring Cloud](../spring-cloud/index.yml) o [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric).  Se è necessario un maggior controllo sulle VM in cui viene eseguito il codice, prendere in considerazione l'uso di [Macchine virtuali di Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Per altre informazioni su come scegliere uno di questi servizi di Azure, vedere [Confronto tra Servizio app di Azure, Macchine virtuali, Service Fabric e Servizi cloud](/azure/architecture/guide/technology-choices/compute-decision-tree).

## <a name="app-service-on-linux"></a>Servizio app in Linux

Il servizio app può anche ospitare le app Web in modo nativo in Linux per gli stack di applicazioni supportate. Può anche eseguire contenitori Linux personalizzati (noti anche come app Web per contenitori).

### <a name="built-in-languages-and-frameworks"></a>Linguaggi e framework predefiniti

Il servizio app in Linux supporta numerose immagini predefinite specifiche del linguaggio. È sufficiente distribuire il codice. Le lingue supportate comprendono: Node.js, Java (JRE 8 e JRE 11), PHP, Python, .NET Core e Ruby. Eseguire [`az webapp list-runtimes --linux`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes) per visualizzare i linguaggi più recenti e le versioni supportate. Se il runtime richiesto dall'applicazione non è supportato nelle immagini predefinite, è possibile distribuirlo con un contenitore personalizzato.

### <a name="limitations"></a>Limitazioni

- Il Servizio app in Linux non è supportato nel piano tariffario [Condiviso](https://azure.microsoft.com/pricing/details/app-service/plans/). 
- Non è possibile combinare app Windows e Linux nello stesso piano di servizio app.  
- Non è possibile combinare app Windows e Linux nella stessa area per lo stesso gruppo di risorse.
- Il portale di Azure mostra solo le funzionalità attualmente funzionanti per le app Linux. Man mano che le funzionalità vengono abilitate, vengono attivate nel portale.
- Quando viene distribuito nelle immagini predefinite, al codice e al contenuto viene allocato un volume di archiviazione per il contenuto Web, supportato da Archiviazione di Azure. La latenza del disco di questo volume è maggiore e più variabile rispetto alla latenza del file system del contenitore. Le app che richiedono un accesso elevato in sola lettura a file di contenuto possono trarre vantaggio dall'opzione relativa al contenitore personalizzato, che colloca i file nel file system del contenitore anziché nel volume del contenuto.

## <a name="next-steps"></a>Passaggi successivi

Creare la prima app Web.

> [!div class="nextstepaction"]
> [ASP.NET Core (in Windows o Linux)](quickstart-dotnetcore.md)

> [!div class="nextstepaction"]
> [ASP.NET (in Windows)](quickstart-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP (in Windows o Linux)](quickstart-php.md)

> [!div class="nextstepaction"]
> [Ruby (in Linux)](quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js (in Windows o Linux)](quickstart-nodejs.md)

> [!div class="nextstepaction"]
> [Java (in Windows o Linux)](quickstart-java.md)

> [!div class="nextstepaction"]
> [Python (in Linux)](quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML (in Windows o Linux)](quickstart-html.md)

> [!div class="nextstepaction"]
> [Contenitore personalizzato (Windows o Linux)](tutorial-custom-container.md)