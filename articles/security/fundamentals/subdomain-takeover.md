---
title: Impedisci le acquisizioni dei sottodomini con i record degli alias DNS di Azure e la verifica del dominio personalizzato del servizio app Azure
description: Informazioni su come evitare la comune minaccia a livello di gravità dell'acquisizione di sottodomini
services: security
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2020
ms.author: memildin
ms.openlocfilehash: e378ffe00be9215c692a832e232fac7e866ab3c9
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88890825"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>Impedisci le voci DNS in sospeso ed evita l'acquisizione di sottodomini

Questo articolo descrive le minacce alla sicurezza comuni di acquisizione dei sottodomini e i passaggi da eseguire per attenuare il problema.


## <a name="what-is-subdomain-takeover"></a>Che cos'è l'acquisizione di sottodomini?

Le acquisizioni di sottodomini rappresentano una minaccia comune e a gravità elevata per le organizzazioni che creano regolarmente ed eliminano molte risorse. L'acquisizione di un sottodominio può verificarsi quando si dispone di un record DNS che punta a una risorsa di Azure di cui è stato effettuato il provisioning. Tali record DNS sono noti anche come voci "DNS in sospeso". I record CNAME sono particolarmente vulnerabili a questa minaccia.

Uno scenario comune per l'acquisizione di sottodomini:

1. Viene creato un sito Web. 

    In questo esempio, `app-contogreat-dev-001.azurewebsites.net`.

1. Una voce CNAME viene aggiunta al DNS che punta al sito Web. 

    In questo esempio è stato creato il nome descrittivo seguente: `greatapp.contoso.com` .

1. Dopo alcuni mesi, il sito non è più necessario, pertanto viene eliminato **senza** eliminare la voce DNS corrispondente. 

    La voce DNS CNAME è ora "penzoloni".

1. Quasi immediatamente dopo l'eliminazione del sito, un attore di minacce individua il sito mancante e crea il proprio sito Web in `app-contogreat-dev-001.azurewebsites.net` .

    A questo punto, il traffico destinato `greatapp.contoso.com` a passa al sito di Azure di Threat Actor e l'attore minaccia il controllo del contenuto visualizzato. 

    Il DNS penzolante è stato sfruttato e il sottodominio di Contoso "GreatApp" è stato vittima dell'acquisizione del sottodominio. 

![Acquisizione del sottodominio da un sito Web di cui è stato effettuato il deprovision](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-subdomain-takeover"></a>Rischi dell'acquisizione di sottodomini

Quando un record DNS punta a una risorsa che non è disponibile, il record stesso dovrebbe essere stato rimosso dalla zona DNS. Se non è stato eliminato, si tratta di un record DNS "penzolante" e crea la possibilità di acquisizione di sottodomini.

Le voci DNS in sospeso consentono agli attori minaccia di assumere il controllo del nome DNS associato per ospitare un sito Web o un servizio dannoso. Le pagine e i servizi dannosi nel sottodominio di un'organizzazione possono produrre:

- **Perdita di controllo sul contenuto del sottodominio** : la pressione negativa sull'impossibilità di proteggere il contenuto dell'organizzazione, nonché il danneggiamento del marchio e la perdita di attendibilità.

- **Cookie harvesting da visitatori non sospetti** : è comune per le app Web esporre i cookie di sessione ai sottodomini (*. contoso.com), di conseguenza qualsiasi sottodominio può accedervi. Gli attori di minaccia possono utilizzare l'acquisizione di sottodomini per creare una pagina di ricerca autenticata, ingannare gli utenti ignari e raccogliere i cookie (anche cookie sicuri). Un malinteso comune è che l'uso di certificati SSL protegge il sito e i cookie degli utenti, da un'acquisizione. Tuttavia, un attore minaccia può utilizzare il sottodominio dirottato per richiedere e ricevere un certificato SSL valido. I certificati SSL validi garantiscono l'accesso ai cookie protetti e possono aumentare ulteriormente la legittimità percepita del sito dannoso.

- **Campagne di phishing** : i sottodomini con aspetto autentico possono essere usati nelle campagne di phishing. Questo vale per i siti dannosi e anche per i record MX che consentono all'attore minaccia di ricevere messaggi di posta elettronica destinati a un sottodominio legittimo di un marchio noto come affidabile.

- **Ulteriori rischi** : i siti dannosi possono essere usati per eseguire l'escalation in altri attacchi classici, ad esempio XSS, CSRF, CORS bypass e altro ancora.



## <a name="preventing-dangling-dns-entries"></a>Prevenzione delle voci DNS in sospeso

Assicurarsi che l'organizzazione abbia implementato i processi per impedire che le voci DNS in sospeso e le acquisizioni di sottodomini risultanti siano una parte essenziale del programma di sicurezza.

Le misure preventive attualmente disponibili sono elencate di seguito.


### <a name="use-azure-dns-alias-records"></a>Usare i record di alias DNS di Azure

I [record alias](https://docs.microsoft.com/azure/dns/dns-alias#scenarios) di DNS di Azure possono impedire i riferimenti a penzoloni associando il ciclo di vita di un record DNS a una risorsa di Azure. Si consideri, ad esempio, un record DNS che è qualificato come record alias per puntare a un indirizzo IP pubblico o a un profilo di Gestione traffico. Se si eliminano le risorse sottostanti, il record dell'alias DNS diventa un set di record vuoto. Non fa più riferimento alla risorsa eliminata. È importante notare che ci sono limiti per gli elementi che è possibile proteggere con i record degli alias. Attualmente, l'elenco è limitato a:

- Frontdoor di Azure
- Profili di Gestione traffico
- Endpoint della rete per la distribuzione di contenuti (CDN) di Azure
- Indirizzi IP pubblici

Nonostante le offerte di servizio limitate attualmente, è consigliabile usare i record alias per difendersi da un'acquisizione di sottodominio laddove possibile.

[Altre](https://docs.microsoft.com/azure/dns/dns-alias#capabilities) informazioni sulle funzionalità dei record alias di DNS di Azure.



### <a name="use-azure-app-services-custom-domain-verification"></a>Usare la verifica del dominio personalizzato del servizio app Azure

Quando si creano voci DNS per il servizio app Azure, creare un asuid. sottodominio Record TXT con ID di verifica del dominio. Quando esiste un record TXT di questo tipo, nessun'altra sottoscrizione di Azure può convalidare il dominio personalizzato. 

Questi record non impediscono a un utente di creare il servizio app Azure con lo stesso nome presente nella voce CNAME. Senza la possibilità di dimostrare la proprietà del nome di dominio, gli attori minacciati non possono ricevere traffico o controllare il contenuto.

[Altre](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain) informazioni su come eseguire il mapping di un nome DNS personalizzato esistente al servizio app Azure.



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>Creare e automatizzare i processi per attenuare la minaccia

Spesso gli sviluppatori e i team operativi eseguono processi di pulizia per evitare le minacce DNS. Le procedure riportate di seguito consentono di evitare la sofferenza di questa minaccia da parte dell'organizzazione. 

- **Creare procedure per la prevenzione:**

    - Informare gli sviluppatori di applicazioni di reindirizzare gli indirizzi ogni volta che eliminano risorse.

    - Inserire "Rimuovi voce DNS" nell'elenco dei controlli necessari durante la rimozione delle autorizzazioni per un servizio.

    - Inserire i [blocchi Delete](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) per tutte le risorse che dispongono di una voce DNS personalizzata. Un blocco di eliminazione funge da indicatore per cui il mapping deve essere rimosso prima che venga effettuato il deprovisioning della risorsa. Misure come questa possono funzionare solo se vengono combinate con i programmi di formazione interni.

- **Creare procedure per l'individuazione:**

    - Esaminare regolarmente i record DNS per assicurarsi che i sottodomini siano tutti mappati alle risorse di Azure:

        - Exist: esegue una query sulle zone DNS per le risorse che puntano a sottodomini di Azure, ad esempio *. azurewebsites.net o *. cloudapp.azure.com (vedere [questo elenco di riferimenti](azure-domains.md)).
        - Si è proprietari: confermare che si è proprietari di tutte le risorse a cui sono destinati i sottodomini DNS.

    - Mantenere un catalogo di servizi per gli endpoint del nome di dominio completo (FQDN) di Azure e i proprietari dell'applicazione. Per compilare il catalogo di servizi, eseguire lo script di query di Azure Resource Graph seguente. Questo script proietta le informazioni sull'endpoint FQDN delle risorse a cui si ha accesso e le restituisce in un file CSV. Se si ha accesso a tutte le sottoscrizioni per il tenant, lo script prende in considerazione tutte le sottoscrizioni, come illustrato nello script di esempio seguente. Per limitare i risultati a un set specifico di sottoscrizioni, modificare lo script come illustrato.

        >[!IMPORTANT]
        > **Autorizzazioni** : eseguire la query come un utente che ha accesso a tutte le sottoscrizioni di Azure. 
        >
        > **Limitazioni** : il grafico delle risorse di Azure presenta limiti di limitazione e paging da considerare se si dispone di un ambiente di Azure di grandi dimensioni. [Altre](https://docs.microsoft.com/azure/governance/resource-graph/concepts/work-with-data) informazioni sull'uso di set di dati di risorse di Azure di grandi dimensioni. Lo script di esempio seguente utilizza la suddivisione in batch delle sottoscrizioni per evitare queste limitazioni.

        ```powershell
        
            # Fetch the full array of subscription IDs.
            $subscriptions = Get-AzSubscription

            $subscriptionIds = $subscriptions.Id
                    # Output file path and names
                    $date = get-date
                    $fdate = $date.ToString("MM-dd-yyy hh_mm_ss tt")
                    $fdate #log to console
                    $rpath = [Environment]::GetFolderPath("MyDocuments") + '\' # Feel free to update your path.
                    $rname = 'Tenant_FQDN_Report_' + $fdate + '.csv' # Feel free to update the document name.
                    $fpath = $rpath + $rname
                    $fpath #This is the output file of FQDN report.

            # queries
            $allTypesFqdnsQuery = "where type in ('microsoft.network/frontdoors',
                                    'microsoft.storage/storageaccounts',
                                    'microsoft.cdn/profiles/endpoints',
                                    'microsoft.network/publicipaddresses',
                                    'microsoft.network/trafficmanagerprofiles',
                                    'microsoft.containerinstance/containergroups',
                                    'microsoft.web/sites',
                                    'microsoft.web/sites/slots')
                        | extend FQDN = case(
                            type =~ 'microsoft.network/frontdoors', properties['cName'],
                            type =~ 'microsoft.storage/storageaccounts', parse_url(tostring(properties['primaryEndpoints']['blob'])).Host,
                            type =~ 'microsoft.cdn/profiles/endpoints', properties['hostName'],
                            type =~ 'microsoft.network/publicipaddresses', properties['dnsSettings']['fqdn'],
                            type =~ 'microsoft.network/trafficmanagerprofiles', properties['dnsConfig']['fqdn'],
                            type =~ 'microsoft.containerinstance/containergroups', properties['ipAddress']['fqdn'],
                            type =~ 'microsoft.web/sites', properties['defaultHostName'],
                            type =~ 'microsoft.web/sites/slots', properties['defaultHostName'],
                            '')
                        | project id, type, name, FQDN
                        | where isnotempty(FQDN)";

            $apiManagementFqdnsQuery = "where type =~ 'microsoft.apimanagement/service'
                        | project id, type, name,
                            gatewayUrl=parse_url(tostring(properties['gatewayUrl'])).Host,
                            portalUrl =parse_url(tostring(properties['portalUrl'])).Host,
                            developerPortalUrl = parse_url(tostring(properties['developerPortalUrl'])).Host,
                            managementApiUrl = parse_url(tostring(properties['managementApiUrl'])).Host,
                            gatewayRegionalUrl = parse_url(tostring(properties['gatewayRegionalUrl'])).Host,
                            scmUrl = parse_url(tostring(properties['scmUrl'])).Host,
                            additionaLocs = properties['additionalLocations']
                        | mvexpand additionaLocs
                        | extend additionalPropRegionalUrl = tostring(parse_url(tostring(additionaLocs['gatewayRegionalUrl'])).Host)
                        | project id, type, name, FQDN = pack_array(gatewayUrl, portalUrl, developerPortalUrl, managementApiUrl, gatewayRegionalUrl, scmUrl,             
                            additionalPropRegionalUrl)
                        | mvexpand FQDN
                        | where isnotempty(FQDN)";

            $queries = @($allTypesFqdnsQuery, $apiManagementFqdnsQuery);

            # Paging helper cursor
            $Skip = 0;
            $First = 1000;

            # If you have large number of subscriptions, process them in batches of 2,000.
            $counter = [PSCustomObject] @{ Value = 0 }
            $batchSize = 2000
            $response = @()

            # Group the subscriptions into batches.
            $subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

            foreach($query in $queries)
            {
                # Run the query for each subscription batch with paging.
                foreach ($batch in $subscriptionsBatch)
                { 
                    $Skip = 0; #Reset after each batch.

                    $response += do { Start-Sleep -Milliseconds 500;   if ($Skip -eq 0) {$y = Search-AzGraph -Query $query -First $First -Subscription $batch.Group ; } `
                    else {$y = Search-AzGraph -Query $query -Skip $Skip -First $First -Subscription $batch.Group } `
                    $cont = $y.Count -eq $First; $Skip = $Skip + $First; $y; } while ($cont)
                }
            }

            # View the completed results of the query on all subscriptions
            $response | Export-Csv -Path $fpath -Append  

        ```

        Elenco di tipi e relativi `FQDNProperty` valori, come specificato nella query del grafico di risorse precedente:

        |Nome risorsa  | `<ResourceType>`  | `<FQDNproperty>`  |
        |---------|---------|---------|
        |Frontdoor di Azure|microsoft.network/frontdoors|Properties. cName|
        |Archiviazione BLOB di Azure|microsoft.storage/storageaccounts|Properties. primari. blob|
        |Rete CDN di Azure|microsoft.cdn/profiles/endpoints|Properties. hostName|
        |Indirizzi IP pubblici|microsoft.network/publicipaddresses|Properties. dnsSettings. FQDN|
        |Gestione traffico di Azure|microsoft.network/trafficmanagerprofiles|Properties. dnsConfig. FQDN|
        |Istanza di contenitore di Azure|microsoft.containerinstance/containergroups|Properties. ipAddress. FQDN|
        |Gestione API di Azure|microsoft.apimanagement/service|Properties. hostnameConfigurations. hostName|
        |Servizio app di Azure|microsoft.web/sites|Properties. defaultHostName|
        |Servizio app Azure-slot|microsoft.web/sites/slots|Properties. defaultHostName|


- **Creare procedure per la correzione:**
    - Quando vengono rilevate voci DNS in sospeso, il team deve verificare se si è verificata una compromissione.
    - Individuare il motivo per cui l'indirizzo non è stato reindirizzato al momento della rimozione della risorsa.
    - Eliminare il record DNS se non è più in uso o puntare alla risorsa di Azure (FQDN) corretta di proprietà dell'organizzazione.
 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui servizi correlati e sulle funzionalità di Azure che è possibile usare per la difesa dall'acquisizione dei sottodomini, vedere le pagine seguenti.

- [DNS di Azure supporta l'uso di record alias per i domini personalizzati](https://docs.microsoft.com/azure/dns/dns-alias#prevent-dangling-dns-records)

- [Usare l'ID di verifica del dominio quando si aggiungono domini personalizzati nel servizio app Azure](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#get-domain-verification-id) 

- [Guida introduttiva: Eseguire la prima query di Resource Graph usando Azure PowerShell](https://docs.microsoft.com/azure/governance/resource-graph/first-query-powershell)
