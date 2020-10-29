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
ms.date: 09/29/2020
ms.author: memildin
ms.openlocfilehash: d1837d84c97227ba3d8743c3717e2f68dafd6b95
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911393"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>Impedisci le voci DNS in sospeso ed evita l'acquisizione di sottodomini

Questo articolo descrive le minacce alla sicurezza comuni di acquisizione dei sottodomini e i passaggi da eseguire per attenuare il problema.


## <a name="what-is-subdomain-takeover"></a>Che cos'è l'acquisizione di sottodomini?

Le acquisizioni di sottodomini rappresentano una minaccia comune e a gravità elevata per le organizzazioni che creano regolarmente ed eliminano molte risorse. L'acquisizione di un sottodominio può verificarsi quando si dispone di un [record DNS](https://docs.microsoft.com/azure/dns/dns-zones-records#dns-records) che punta a una risorsa di Azure di cui è stato effettuato il provisioning. Tali record DNS sono noti anche come voci "DNS in sospeso". I record CNAME sono particolarmente vulnerabili a questa minaccia. Le acquisizioni di sottodominio consentono agli attori malintenzionati di reindirizzare il traffico destinato al dominio di un'organizzazione a un sito che esegue attività dannose.

Uno scenario comune per l'acquisizione di sottodomini:

1. **CREAZIONE**

    1. Si esegue il provisioning di una risorsa di Azure con un nome di dominio completo (FQDN) di `app-contogreat-dev-001.azurewebsites.net` .

    1. Si assegna un record CNAME nella zona DNS con il sottodominio `greatapp.contoso.com` che instrada il traffico alla risorsa di Azure.

1. **DEPROVISIONING**

    1. La risorsa di Azure viene sottoposta a deprovisioning o eliminata dopo che non è più necessaria. 
    
        A questo punto, il record CNAME `greatapp.contoso.com` *deve* essere rimosso dalla zona DNS. Se il record CNAME non viene rimosso, viene annunciato come dominio attivo, ma non instrada il traffico a una risorsa di Azure attiva. Si tratta della definizione di un record DNS "penzoloni".

    1. Il sottodominio penzoloni, `greatapp.contoso.com` , è ora vulnerabile e può essere preso in assegnazione a un'altra risorsa della sottoscrizione di Azure.

1. **ACQUISIZIONE**

    1. Usando metodi e strumenti comunemente disponibili, un attore di minacce rileva il sottodominio in sospeso.  

    1. L'attore minaccia esegue il provisioning di una risorsa di Azure con lo stesso FQDN della risorsa precedentemente controllata. In questo esempio, `app-contogreat-dev-001.azurewebsites.net`.

    1. Il traffico inviato al sottodominio `greatapp.contoso.com` viene ora indirizzato alla risorsa dell'attore malintenzionato in cui controllano il contenuto.



![Acquisizione del sottodominio da un sito Web di cui è stato effettuato il deprovision](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-subdomain-takeover"></a>Rischi dell'acquisizione di sottodomini

Quando un record DNS punta a una risorsa che non è disponibile, il record stesso dovrebbe essere stato rimosso dalla zona DNS. Se non è stato eliminato, si tratta di un record DNS "penzolante" e crea la possibilità di acquisizione di sottodomini.

Le voci DNS in sospeso consentono agli attori minaccia di assumere il controllo del nome DNS associato per ospitare un sito Web o un servizio dannoso. Le pagine e i servizi dannosi nel sottodominio di un'organizzazione possono causare:

- **Perdita di controllo sul contenuto del sottodominio** : la pressione negativa sull'impossibilità di proteggere il contenuto dell'organizzazione, nonché il danneggiamento del marchio e la perdita di attendibilità.

- **Cookie harvesting da visitatori non sospetti** : è comune per le app Web esporre i cookie di sessione ai sottodomini (*. contoso.com), di conseguenza qualsiasi sottodominio può accedervi. Gli attori di minaccia possono utilizzare l'acquisizione di sottodomini per creare una pagina di ricerca autenticata, ingannare gli utenti ignari e raccogliere i cookie (anche cookie sicuri). Un malinteso comune è che l'uso di certificati SSL protegge il sito e i cookie degli utenti, da un'acquisizione. Tuttavia, un attore minaccia può utilizzare il sottodominio dirottato per richiedere e ricevere un certificato SSL valido. I certificati SSL validi garantiscono l'accesso ai cookie protetti e possono aumentare ulteriormente la legittimità percepita del sito dannoso.

- **Campagne di phishing** : i sottodomini con aspetto autentico potrebbero essere usati nelle campagne di phishing. Questo vale per i siti dannosi e per i record MX che consentono all'attore minaccia di ricevere messaggi di posta elettronica destinati a un sottodominio legittimo di un marchio noto.

- **Ulteriori rischi** : i siti dannosi potrebbero essere usati per eseguire l'escalation in altri attacchi classici, ad esempio XSS, CSRF, CORS bypass e altro ancora.



## <a name="identify-dangling-dns-entries"></a>Identificare le voci DNS in sospeso

Per identificare le voci DNS all'interno dell'organizzazione che potrebbero essere tralasciate, usare gli strumenti di PowerShell ["Get-DanglingDnsRecords"](https://aka.ms/DanglingDNSDomains)ospitati da GitHub di Microsoft.

Questo strumento consente ai clienti di Azure di elencare tutti i domini con un CNAME associato a una risorsa di Azure esistente creata nei rispettivi tenant o sottoscrizioni.

Se i CNAME si trovano in altri servizi DNS e puntano a risorse di Azure, fornire i CNAME in un file di input allo strumento.

Lo strumento supporta le risorse di Azure elencate nella tabella seguente. Lo strumento estrae o accetta come input tutti i CNAME del tenant.


| Servizio                   | Type                                        | FQDNproperty                               | Esempio                         |
|---------------------------|---------------------------------------------|--------------------------------------------|---------------------------------|
| Frontdoor di Azure          | microsoft.network/frontdoors                | Properties. cName                           | `abc.azurefd.net`               |
| Archiviazione BLOB di Azure        | microsoft.storage/storageaccounts           | Properties. primari. blob           | `abc. blob.core.windows.net`    |
| Rete CDN di Azure                 | microsoft.cdn/profiles/endpoints            | Properties. hostName                        | `abc.azureedge.net`             |
| Indirizzi IP pubblici       | microsoft.network/publicipaddresses         | Properties. dnsSettings. FQDN                | `abc.EastUs.cloudapp.azure.com` |
| Gestione traffico di Azure     | microsoft.network/trafficmanagerprofiles    | Properties. dnsConfig. FQDN                  | `abc.trafficmanager.net`        |
| Istanza di contenitore di Azure  | microsoft.containerinstance/containergroups | Properties. ipAddress. FQDN                  | `abc.EastUs.azurecontainer.io`  |
| Gestione API di Azure      | microsoft.apimanagement/service             | Properties. hostnameConfigurations. hostName | `abc.azure-api.net`             |
| Servizio app di Azure         | microsoft.web/sites                         | Properties. defaultHostName                 | `abc.azurewebsites.net`         |
| Servizio app Azure-slot | microsoft.web/sites/slots                   | Properties. defaultHostName                 | `abc-def.azurewebsites.net`     |



### <a name="prerequisites"></a>Prerequisiti

Eseguire la query come utente che ha:

- almeno l'accesso a livello di lettore alle sottoscrizioni di Azure
- accesso in lettura al grafico delle risorse di Azure

Se si è un amministratore globale del tenant dell'organizzazione, elevare il proprio account per accedere a tutte le sottoscrizioni dell'organizzazione usando le linee guida disponibili in [elevare l'accesso per gestire tutti i gruppi di gestione e le sottoscrizioni di Azure](../../role-based-access-control/elevate-access-global-admin.md).


> [!TIP]
> Azure Resource Graph presenta limiti di limitazione e paging da considerare se si dispone di un ambiente di Azure di grandi dimensioni. 
> 
> [Altre informazioni sull'uso di set di dati di risorse di Azure di grandi dimensioni](../../governance/resource-graph/concepts/work-with-data.md).
> 
> Lo strumento usa la suddivisione in batch delle sottoscrizioni per evitare queste limitazioni.

### <a name="run-the-script"></a>Eseguire lo script

Altre informazioni sullo script di PowerShell, **Get-DanglingDnsRecords.ps1** e scaricarlo da GitHub: https://aka.ms/DanglingDNSDomains .

## <a name="remediate-dangling-dns-entries"></a>Correggere le voci DNS in sospeso 

Esaminare le zone DNS e identificare i record CNAME che sono in sospeso o che sono stati rilevati. Se i sottodomini risultano sospesi o sono stati rilevati, rimuovere i sottodomini vulnerabili e attenuare i rischi con i passaggi seguenti:

1. Dalla zona DNS, rimuovere tutti i record CNAME che puntano a FQDN di risorse senza più provisioning.

1. Per consentire il routing del traffico alle risorse nel controllo, effettuare il provisioning di risorse aggiuntive con i nomi di dominio completi specificati nei record CNAME dei sottodomini in sospeso.

1. Esaminare il codice dell'applicazione per i riferimenti a sottodomini specifici e aggiornare eventuali riferimenti a sottodomini non corretti o obsoleti.

1. Verificare se si è verificata una compromissione e agire in base alle procedure di risposta agli eventi imprevisti dell'organizzazione. Suggerimenti e procedure consigliate per l'analisi di questo problema sono disponibili di seguito.

    Se la logica dell'applicazione è tale che i segreti come le credenziali OAuth sono stati inviati al sottodominio in sospeso o che sono state inviate informazioni riservate alla privacy ai sottodomini sospesi, i dati potrebbero essere stati esposti a terze parti.

1. Comprendere il motivo per cui il record CNAME non è stato rimosso dalla zona DNS quando è stato effettuato il deprovisioning della risorsa ed è stata eseguita la procedura per assicurarsi che i record DNS vengano aggiornati in modo appropriato quando le risorse di Azure vengono sottoposte a deprovisioning in futuro.


## <a name="prevent-dangling-dns-entries"></a>Impedisci voci DNS in sospeso

Assicurarsi che l'organizzazione abbia implementato i processi per impedire che le voci DNS in sospeso e le acquisizioni di sottodomini risultanti siano una parte essenziale del programma di sicurezza.

Alcuni servizi di Azure offrono funzionalità per semplificare la creazione di misure preventive e sono descritte in dettaglio di seguito. Altri metodi per evitare questo problema devono essere stabiliti tramite le procedure consigliate dell'organizzazione o le procedure operative standard.


### <a name="use-azure-dns-alias-records"></a>Usare i record di alias DNS di Azure

I [record alias](../../dns/dns-alias.md#scenarios) di DNS di Azure possono impedire i riferimenti a penzoloni associando il ciclo di vita di un record DNS a una risorsa di Azure. Si consideri, ad esempio, un record DNS che è qualificato come record alias per puntare a un indirizzo IP pubblico o a un profilo di Gestione traffico. Se si eliminano le risorse sottostanti, il record dell'alias DNS diventa un set di record vuoto. Non fa più riferimento alla risorsa eliminata. È importante notare che ci sono limiti per gli elementi che è possibile proteggere con i record degli alias. Attualmente, l'elenco è limitato a:

- Frontdoor di Azure
- Profili di Gestione traffico
- Endpoint della rete per la distribuzione di contenuti (CDN) di Azure
- Indirizzi IP pubblici

Nonostante le offerte di servizio limitate attualmente, è consigliabile usare i record alias per difendersi da un'acquisizione di sottodominio laddove possibile.

[Altre informazioni sulle funzionalità dei record alias di DNS di Azure](../../dns/dns-alias.md#capabilities).



### <a name="use-azure-app-services-custom-domain-verification"></a>Usare la verifica del dominio personalizzato del servizio app Azure

Quando si creano voci DNS per il servizio app Azure, creare un asuid. sottodominio Record TXT con ID di verifica del dominio. Quando esiste un record TXT di questo tipo, nessun'altra sottoscrizione di Azure può convalidare il dominio personalizzato. 

Questi record non impediscono a un utente di creare il servizio app Azure con lo stesso nome presente nella voce CNAME. Senza la possibilità di dimostrare la proprietà del nome di dominio, gli attori minacciati non possono ricevere traffico o controllare il contenuto.

[Altre informazioni su come eseguire il mapping di un nome DNS personalizzato esistente al servizio app Azure](../../app-service/app-service-web-tutorial-custom-domain.md).



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>Creare e automatizzare i processi per attenuare la minaccia

Spesso gli sviluppatori e i team operativi eseguono processi di pulizia per evitare le minacce DNS. Le procedure riportate di seguito consentono di evitare la sofferenza di questa minaccia da parte dell'organizzazione. 

- **Creare procedure per la prevenzione:**

    - Informare gli sviluppatori di applicazioni di reindirizzare gli indirizzi ogni volta che eliminano risorse.

    - Inserire "Rimuovi voce DNS" nell'elenco dei controlli necessari durante la rimozione delle autorizzazioni per un servizio.

    - Inserire i [blocchi Delete](../../azure-resource-manager/management/lock-resources.md) per tutte le risorse che dispongono di una voce DNS personalizzata. Un blocco di eliminazione funge da indicatore per cui il mapping deve essere rimosso prima che venga effettuato il deprovisioning della risorsa. Misure come questa possono funzionare solo se vengono combinate con i programmi di formazione interni.

- **Creare procedure per l'individuazione:**

    - Esaminare regolarmente i record DNS per assicurarsi che i sottodomini siano tutti mappati alle risorse di Azure:

        - Exist: esegue una query sulle zone DNS per le risorse che puntano a sottodomini di Azure, ad esempio *. azurewebsites.net o *. cloudapp.azure.com (vedere l' [elenco di riferimento dei domini di Azure](azure-domains.md)).
        - Si è proprietari: confermare che si è proprietari di tutte le risorse a cui sono destinati i sottodomini DNS.

    - Mantenere un catalogo di servizi per gli endpoint del nome di dominio completo (FQDN) di Azure e i proprietari dell'applicazione. Per compilare il catalogo di servizi, eseguire lo script di query di Azure Resource Graph seguente. Questo script proietta le informazioni sull'endpoint FQDN delle risorse a cui si ha accesso e le restituisce in un file CSV. Se si ha accesso a tutte le sottoscrizioni per il tenant, lo script prende in considerazione tutte le sottoscrizioni, come illustrato nello script di esempio seguente. Per limitare i risultati a un set specifico di sottoscrizioni, modificare lo script come illustrato.


- **Creare procedure per la correzione:**
    - Quando vengono rilevate voci DNS in sospeso, il team deve verificare se si è verificata una compromissione.
    - Individuare il motivo per cui l'indirizzo non è stato reindirizzato al momento della rimozione della risorsa.
    - Eliminare il record DNS se non è più in uso o puntare alla risorsa di Azure (FQDN) corretta di proprietà dell'organizzazione.
 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui servizi correlati e sulle funzionalità di Azure che è possibile usare per la difesa dall'acquisizione dei sottodomini, vedere le pagine seguenti.

- [Impedisci record DNS in sospeso con DNS di Azure](../../dns/dns-alias.md#prevent-dangling-dns-records)

- [Usare un ID di verifica del dominio quando si aggiungono domini personalizzati nel servizio app Azure](../../app-service/app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id)

- [Guida introduttiva: Eseguire la prima query di Resource Graph usando Azure PowerShell](../../governance/resource-graph/first-query-powershell.md)
