---
title: Protezione di Funzioni di Azure
description: Informazioni su come proteggere l'esecuzione del codice funzione in Azure dagli attacchi comuni.
ms.date: 4/13/2020
ms.topic: conceptual
ms.openlocfilehash: e0c5036681aace103ea69d1e9cc73e96dc30821f
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502682"
---
# <a name="securing-azure-functions"></a>Protezione di Funzioni di Azure

In molti casi, la pianificazione di sviluppo, distribuzione e funzionamento sicuri delle funzioni serverless è molto simile a quella di qualsiasi applicazione ospitata sul Web o nel cloud. [Servizio app di Azure](../app-service/index.yml) rende disponibile l'infrastruttura di hosting per le app per le funzioni. Questo articolo illustra le strategie di sicurezza per l'esecuzione del codice funzione e le modalità in cui il Servizio app è utile nel proteggere le funzioni. 

[!INCLUDE [app-service-security-intro](../../includes/app-service-security-intro.md)]

Per alcune raccomandazioni sulla sicurezza relative ad [Azure Security Benchmark](../security/benchmarks/overview.md), vedere [Informazioni di base sulla sicurezza di Azure per Funzioni di Azure](security-baseline.md).

## <a name="secure-operation"></a>Utilizzo sicuro 

Questa sezione illustra come configurare ed eseguire l'app per le funzioni nel modo più sicuro possibile. 

### <a name="security-center"></a>Centro sicurezza

Il Centro sicurezza si integra con l'app per le funzioni nel portale. Offre gratuitamente una rapida valutazione delle potenziali vulnerabilità della sicurezza relative alla configurazione. Le app per le funzioni in esecuzione in un piano dedicato possono anche usare le funzionalità di sicurezza in tempo reale del Centro sicurezza, con un costo aggiuntivo. Per altre informazioni, vedere [Proteggere le app Web e le API del Servizio app di Azure](../security-center/security-center-app-services.md). 

### <a name="log-and-monitor"></a>Log e monitoraggio

Una modalità per rilevare gli attacchi consiste nell'eseguire attività di monitoraggio e registrare i dati di analisi. Funzioni si integra con Application Insights per raccogliere i dati di log, prestazioni ed errori per l'app per le funzioni. Application Insights, oltre a rilevare automaticamente le anomalie nelle prestazioni, include strumenti di analisi avanzati che consentono di diagnosticare i problemi e capire come vengono usate le funzioni. Per altre informazioni, vedere [Monitorare Funzioni di Azure](functions-monitoring.md).

Funzioni si integra anche con i log di Monitoraggio di Azure per consentire all'utente di consolidare i log delle app per le funzioni con gli eventi del sistema e semplificare l'analisi. È possibile usare le impostazioni di diagnostica per configurare l'esportazione di streaming dei log e delle metriche della piattaforma per le funzioni nella destinazione scelta, ad esempio un'area di lavoro Log Analytics. Per altre informazioni, vedere [Monitoraggio di Funzioni di Azure con i log di Monitoraggio di Azure](functions-monitor-log-analytics.md). 

Per il rilevamento delle minacce a livello aziendale e l'automazione delle risposte, trasmettere i log e gli eventi a un'area di lavoro Log Analytics. È quindi possibile connettere Azure Sentinel a questa area di lavoro. Per altre informazioni, vedere [Che cos'è Azure Sentinel?](../sentinel/overview.md).  

Per altre raccomandazioni sulla sicurezza per l'osservabilità, consultare le [Informazioni di base sulla sicurezza di Azure per Funzioni di Azure](security-baseline.md#logging-and-monitoring). 

### <a name="require-https"></a>Richiedere HTTPS

Per impostazione predefinita, i client possono connettersi agli endpoint della funzione tramite HTTP o HTTPS. È consigliabile reindirizzare HTTP a HTTPs perché HTTPS usa il protocollo SSL/TLS per garantire una connessione protetta, crittografata e autenticata. Per informazioni su come fare, vedere [Applicare HTTPS](../app-service/configure-ssl-bindings.md#enforce-https).

Quando si richiede HTTPS, è necessario richiedere anche la versione più recente di TLS. Per informazioni su come fare, vedere [Applicare versioni di TLS](../app-service/configure-ssl-bindings.md#enforce-tls-versions).

Per altre informazioni, vedere [Proteggere le connessioni (TLS)](../app-service/overview-security.md#https-and-certificates).

### <a name="function-access-keys"></a>Chiavi di accesso alle funzioni

[!INCLUDE [functions-authorization-keys](../../includes/functions-authorization-keys.md)]

#### <a name="system-key"></a>Chiave di sistema 

Le estensioni specifiche possono richiedere una chiave gestita dal sistema per accedere agli endpoint del webhook. Le chiavi di sistema sono progettate per gli endpoint delle funzioni specifici per l'estensione chiamati dai componenti interni. Ad esempio, per il [trigger della Griglia di eventi](functions-bindings-event-grid-trigger.md) è necessario che la sottoscrizione usi una chiave di sistema durante la chiamata all'endpoint del trigger. Durable Functions usa anche chiavi di sistema per chiamare le [API dell'estensione Durable Task](durable/durable-functions-http-api.md). 

L'ambito delle chiavi di sistema è determinato dall'estensione, ma in genere si applica all'intera app per le funzioni. Le chiavi di sistema possono essere create solo da estensioni specifiche e non è possibile impostarne i relativi valori in modo esplicito. Analogamente ad altre chiavi, è possibile generare un nuovo valore per la chiave nel portale o usando le API della chiave.

#### <a name="keys-comparison"></a>Confronto tra le chiavi

La tabella seguente confronta gli utilizzi per diversi tipi di chiavi di accesso:

| Azione                                        | Scope                    | Chiavi valide         |
|-----------------------------------------------|--------------------------|--------------------|
| Eseguire una funzione                            | Funzione specifica        | Funzione           |
| Eseguire una funzione                            | Qualsiasi funzione             | Funzione o host   |
| Chiamare un endpoint amministratore                        | App per le funzioni             | Host (solo master) |
| Chiamare le API dell'estensione Durable Task              | App per le funzioni<sup>1</sup> | Sistema<sup>2</sup> |
| Chiamare un webhook specifico per l'estensione (interno) | App per le funzioni<sup>1</sup> | sistema<sup>2</sup> |

<sup>1</sup>Ambito determinato dall'estensione.  
<sup>2</sup>Nomi specifici impostati per estensione.

Per altre informazioni sulle chiavi di accesso, vedere l'[articolo sull'associazione di trigger HTTP](functions-bindings-http-webhook-trigger.md#obtaining-keys).

### <a name="authenticationauthorization"></a>Autenticazione/autorizzazione

Anche se le chiavi della funzione possono mitigare in qualche modo l'accesso indesiderato, l'unico modo per proteggere in modo sicuro gli endpoint della funzione consiste nell'implementare l'autenticazione positiva dei client che accedono alle funzioni. È quindi possibile prendere decisioni sulle autorizzazioni in base all'identità.  

[!INCLUDE [functions-enable-auth](../../includes/functions-enable-auth.md)]

### <a name="permissions"></a>Autorizzazioni

Come per qualsiasi applicazione o servizio, l'obiettivo è eseguire l'app per le funzioni con il minor numero di autorizzazioni possibile. 

#### <a name="user-management-permissions"></a>Autorizzazioni di gestione dell'utente

Funzioni supporta il controllo degli [accessi in base al ruolo di Azure incorporato (RBAC di Azure)](../role-based-access-control/overview.md). I ruoli di Azure supportati dalle funzioni sono [collaboratore](../role-based-access-control/built-in-roles.md#contributor), [proprietario](../role-based-access-control/built-in-roles.md#owner)e [lettore](../role-based-access-control/built-in-roles.md#owner). 

Le autorizzazioni sono effettive a livello di app per le funzioni. Il ruolo di Collaboratore è necessario per eseguire la maggior parte delle attività a livello di app per le funzioni. Solo il ruolo di Proprietario può eliminare un'app per le funzioni. 

#### <a name="organize-functions-by-privilege"></a>Organizzare le funzioni per privilegio 

Le stringhe di connessione e altre credenziali archiviate nelle impostazioni dell'applicazione conferiscono a tutte le funzioni nell'app per le funzioni lo stesso set di autorizzazioni nella risorsa associata. Provare a ridurre al minimo il numero di funzioni con accesso a credenziali specifiche spostando le funzioni che non usano tali credenziali in un'app per le funzioni separata. È sempre possibile usare tecniche come il [concatenamento delle funzioni](/learn/modules/chain-azure-functions-data-using-bindings/) per spostare i dati tra funzioni in app per le funzioni diverse.  

#### <a name="managed-identities"></a>Identità gestite

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

Per altre informazioni, vedere [Come usare le identità gestite nel servizio app e in Funzioni di Azure](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json).

#### <a name="restrict-cors-access"></a>Limitare l'accesso CORS

[La condivisione di risorse tra le origini (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) è un modo per consentire alle app Web in esecuzione in un altro dominio di effettuare richieste agli endpoint del trigger HTTP. Il servizio app offre supporto predefinito per la gestione delle intestazioni CORS necessarie nelle richieste HTTP. Le regole CORS sono definite a livello di app per le funzioni.  

Di solito si tende a usare un carattere jolly che consente a tutti i siti di accedere all'endpoint. In questo modo, però, si vanifica lo scopo di CORS, che è quello di prevenire gli attacchi di scripting tra siti. Aggiungere, invece, una voce CORS separata per il dominio di ogni app Web che deve accedere all'endpoint. 

### <a name="managing-secrets"></a>Gestione dei segreti 

Per potersi connettere ai vari servizi e alle risorse è necessario eseguire il codice, le app per le funzioni devono poter accedere ai segreti, quali le stringhe di connessione e le chiavi del servizio. Questa sezione descrive come archiviare i segreti necessari per le funzioni.

Non archiviare mai segreti nel codice funzione. 

#### <a name="application-settings"></a>Impostazioni dell'applicazione

Per impostazione predefinita, le stringhe di connessione e i segreti usati dall'app per le funzioni e le associazioni vengono archiviati come impostazioni applicazione. In questo modo le credenziali sono disponibili sia per il codice funzione che per le varie associazioni usate dalla funzione. Il nome dell'impostazione applicazione (chiave) viene usato per recuperare il valore effettivo, ovvero il segreto. 

Per ogni app per le funzioni, ad esempio, è necessario un account di archiviazione associato, che viene usato dal runtime. Per impostazione predefinita, la connessione a questo account di archiviazione è archiviata in un'impostazione applicazione denominata `AzureWebJobsStorage`.

Le impostazioni dell'app e le stringhe di connessione vengono archiviate crittografate in Azure. Vengono decrittografate solo prima di essere inserite nella memoria del processo dell'app all'avvio dell'app. Le chiavi di crittografia vengono sottoposte a rotazione regolarmente. Se invece si preferisce gestire l'archiviazione protetta dei segreti, l'impostazione dell'app deve fare riferimento ad Azure Key Vault. 

#### <a name="key-vault-references"></a>Riferimenti a Key Vault

Sebbene le impostazioni applicazione siano sufficienti per la maggior parte delle funzioni, è consigliabile condividere gli stessi segreti tra più servizi. In questo caso, l'archiviazione ridondante dei segreti comporta un numero maggiore di potenziali vulnerabilità. Un approccio più sicuro prevede l'uso di un servizio di archiviazione segreta centrale e di riferimenti a questo servizio anziché ai segreti stessi.      

[Azure Key Vault](../key-vault/general/overview.md) è un servizio che supporta la gestione centralizzata dei segreti con controllo completo sui criteri di accesso e sulla cronologia di controllo. È possibile usare un riferimento a Key Vault invece di una stringa di connessione o di una chiave nelle impostazioni applicazione. Per altre informazioni, vedere [Usare i riferimenti a Key Vault per Servizio app e Funzioni di Azure](../app-service/app-service-key-vault-references.md?toc=%2fazure%2fazure-functions%2ftoc.json).

### <a name="set-usage-quotas"></a>Impostare le quote di utilizzo

È consigliabile impostare una quota di utilizzo per le funzioni in esecuzione in un piano a consumo. Quando si imposta un limite giornaliero di GB al secondo per la somma dell'esecuzione totale delle funzioni nell'app per le funzioni, l'esecuzione viene arrestata quando si raggiunge il limite. Questo potrebbe contribuire a ridurre il rischio di esecuzione di un codice dannoso per le funzioni. Per informazioni su come stimare il consumo per le funzioni, vedere [Stima dei costi del piano a consumo](functions-consumption-costs.md). 

### <a name="data-validation"></a>Convalida dei dati

I trigger e le associazioni usate dalle funzioni non garantiscono anche la convalida dei dati. Il codice deve convalidare i dati ricevuti da un trigger o da un'associazione di input. Se un servizio upstream viene compromesso, gli input non convalidati non devono passare nelle funzioni. Ad esempio, se la funzione archivia i dati di una coda di Archiviazione di Azure in un database relazionale, è necessario convalidare i dati e impostare i parametri dei comandi per evitare attacchi intrusivi nel codice SQL. 

È consigliabile non dare per scontato che i dati in ingresso nella funzione siano già stati convalidati o puliti. È anche consigliabile verificare che i dati scritti nelle associazioni di output siano validi. 

### <a name="handle-errors"></a>Gestire gli errori

Sebbene sembri banale, è importante scrivere una corretta gestione degli errori nelle funzioni. Gli errori non gestiti si propagano nell'host e vengono gestiti dal runtime. Le diverse associazioni gestiscono l'elaborazione degli errori in modo diverso. Per altre informazioni, vedere [Gestione degli errori di Funzioni di Azure](functions-bindings-error-pages.md).

### <a name="disable-remote-debugging"></a>Disabilitare il debug remoto

Assicurarsi che il debug remoto sia disabilitato, tranne quando si esegue il debug attivo delle funzioni. È possibile disabilitare il debug remoto nella scheda **Impostazioni generali** della **Configurazione** dell'app per le funzioni nel portale. 

### <a name="restrict-cors-access"></a>Limitare l'accesso CORS

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

Non usare caratteri jolly nell'elenco di origini consentite. Elencare invece i domini specifici da cui si prevede di ricevere le richieste.

### <a name="store-data-encrypted"></a>Archiviare i dati crittografati

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

## <a name="secure-deployment"></a>Distribuzione sicura

Gli strumenti e l'integrazione di Funzioni di Azure semplificano la pubblicazione del codice progetto della funzione locale in Azure. È importante comprendere il funzionamento della distribuzione quando si considera la sicurezza per una topologia di Funzioni di Azure.   

### <a name="deployment-credentials"></a>Credenziali per la distribuzione

Le distribuzioni del servizio app richiedono un insieme di credenziali per la distribuzione. Queste credenziali per la distribuzione vengono usate per proteggere le distribuzioni dell'app per le funzioni. Le credenziali per la distribuzione sono gestite dalla piattaforma del Servizio app e vengono crittografate quando sono inattive. 

Sono disponibili due tipi di credenziali per la distribuzione:

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

In questo momento, Key Vault non è supportato per le credenziali di distribuzione. Per altre informazioni su come gestire le credenziali per la distribuzione, vedere [Configurazione delle credenziali per la distribuzione del Servizio app di Azure](../app-service/deploy-configure-credentials.md).

### <a name="disable-ftp"></a>Disabilitare l'FTP

Per impostazione predefinita, per ogni app per le funzioni è abilitato un endpoint FTP. È possibile accedere all'endpoint FTP usando le credenziali di distribuzione. 

L'FTP non è consigliato per la distribuzione del codice funzione. Le distribuzioni FTP sono manuali e richiedono la sincronizzazione dei trigger. Per altre informazioni, vedere la [distribuzione dell'FTP](functions-deployment-technologies.md#ftp). 

Quando non si prevede l'uso dell'FTP, è necessario disabilitarlo nel portale. Se si sceglie di usarlo, è necessario [applicare FTPS](../app-service/deploy-ftp.md#enforce-ftps).

### <a name="secure-the-scm-endpoint"></a>Proteggere l'endpoint scm

Ogni app per le funzioni ha un endpoint di servizio `scm` corrispondente usato dal servizio Strumenti avanzati (Kudu) per le distribuzioni e altre [estensioni del sito](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) del Servizio app. L'endpoint scm per un'app per le funzioni è sempre un URL nel formato `https://<FUNCTION_APP_NAME.scm.azurewebsites.net>`. Quando si usa l'isolamento rete per proteggere le funzioni, è necessario tenere presente anche questo endpoint. 

Se si dispone di un endpoint scm separato, è possibile controllare le distribuzioni e altre funzionalità avanzate degli strumenti per le app per le funzioni che sono isolate o in esecuzione in una rete virtuale. L'endpoint scm supporta sia l'autenticazione di base, che usa le credenziali di distribuzione, che il Single Sign-On con le credenziali del portale di Azure. Per altre informazioni, vedere [Accesso al servizio Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service). 

### <a name="continuous-security-validation"></a>Valutazione continua della sicurezza

Poiché la sicurezza deve essere considerata in ogni fase del processo di sviluppo, è opportuno implementare anche la convalida della sicurezza in un ambiente di distribuzione continua. Questa operazione viene talvolta chiamata DevSecOps. L'uso di Azure DevOps per la pipeline di distribuzione consente di integrare la convalida nel processo di distribuzione. Per altre informazioni, vedere [Informazioni su come aggiungere la convalida della sicurezza continua alla pipeline CI/CD](/azure/devops/migrate/security-validation-cicd-pipeline).  

## <a name="network-security"></a>Sicurezza di rete

La limitazione dell'accesso di rete per l'app per le funzioni consente di controllare gli utenti che possono accedere agli endpoint delle funzioni. Funzioni sfrutta l'infrastruttura del servizio app per consentire alle funzioni di accedere alle risorse senza usare indirizzi instradabili tramite Internet o per limitare l'accesso a Internet a un endpoint della funzione. Per altre informazioni su queste opzioni di rete, vedere [Opzioni di rete di Funzioni di Azure](functions-networking-options.md).

### <a name="set-access-restrictions"></a>Impostare le restrizioni di accesso

Le restrizioni di accesso consentono di definire gli elenchi di regole Consenti/Nega per controllare il traffico verso l'app. Le regole vengono valutate in ordine di priorità. Se non è stata definita alcuna regola, l'app accetterà il traffico da qualsiasi indirizzo. Per altre informazioni, vedere [Restrizioni di accesso al Servizio app di Azure #](../app-service/app-service-ip-restrictions.md?toc=%2fazure%2fazure-functions%2ftoc.json).

### <a name="private-site-access"></a>Accesso al sito privato

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

### <a name="deploy-your-function-app-in-isolation"></a>Distribuire l'app per le funzioni in isolamento

[!INCLUDE [functions-deploy-isolation](../../includes/functions-deploy-isolation.md)]

### <a name="use-a-gateway-service"></a>Usare un servizio gateway

I servizi gateway, quali [gateway applicazione di Azure](../application-gateway/overview.md) e [Frontdoor di Azure](../frontdoor/front-door-overview.md) consentono di configurare un web application firewall (WAF). Le regole WAF vengono usate per monitorare o bloccare gli attacchi rilevati, offrendo un livello di protezione aggiuntiva per le funzioni. Per configurare un WAF, è necessario che l'app per le funzioni sia in esecuzione in un ambiente del servizio app di Azure o che usi endpoint privati (anteprima). Per altre informazioni, vedere [Uso di endpoint privati](../app-service/networking/private-endpoint.md).    

## <a name="next-steps"></a>Passaggi successivi

+ [Informazioni di base sulla sicurezza di Azure per Funzioni di Azure](security-baseline.md)
+ [Diagnostica di Funzioni di Azure](functions-diagnostics.md)
