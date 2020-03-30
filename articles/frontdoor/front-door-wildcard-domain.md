---
title: Porta frontale di Azure - Supporto per i domini con caratteri jolly
description: Questo articolo consente di comprendere in che modo Azure Front Door supporta il mapping e la gestione dei domini con caratteri jolly nell'elenco dei domini personalizzati
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2020
ms.author: sharadag
ms.openlocfilehash: c568c9cc5c57098385cc7399459ec656cdbfc305
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537440"
---
# <a name="wildcard-domains"></a>Domini con caratteri jolly

Oltre ai domini apice e ai sottodomini, è anche possibile eseguire il mapping di un nome di dominio con caratteri jolly all'elenco degli host front-end o dei domini personalizzati del profilo Front Door. La presenza di domini con caratteri jolly nella configurazione Front Door semplifica il comportamento di routing del traffico per più sottodomini per un'API, un'applicazione o un sito Web dalla stessa regola di routing senza dover modificare la configurazione per aggiungere e/o specificare ogni sottodominio separatamente. Ad esempio, è possibile definire `customer1.contoso.com` `customer2.contoso.com`il `customerN.contoso.com` routing per , e utilizzando `*.contoso.com`la stessa regola di routing aggiungendo un dominio con caratteri jolly.

Alcuni degli scenari chiave risolti con il supporto per i domini con caratteri jolly includono:

- Non è più necessario eseguire l'onboarding di ogni sottodominio sulla porta frontale e quindi abilitare HTTPS per associare un certificato per ogni sottodominio.
- Se un'applicazione aggiunge un nuovo sottodominio, non è più necessario modificare la configurazione frontdoor di produzione. In caso contrario, in precedenza era necessario aggiungere il sottodominio, associarvi un certificato, allegare un criterio WAF (Web Application Firewall), aggiungere il dominio a regole di routing diverse.

> [!NOTE]
> Attualmente, i domini con caratteri jolly sono supportati solo tramite l'API, PowerShell e l'interfaccia della riga di comando. Il supporto per l'aggiunta di domini con caratteri jolly tramite il portale di Azure non è disponibile.

## <a name="adding-wildcard-domains"></a>Aggiunta di domini con caratteri jolly

È possibile eseguire l'onboarding di un dominio con caratteri jolly nella sezione Host o domini front-end. Analogamente ai sottodomini, Front Door verifica che sia presente un mapping CNAME anche per il dominio con caratteri jolly. Questo mapping DNS può essere un `*.contoso.com` mapping `contoso.azurefd.net` CNAME diretto come il mapping `afdverify.contoso.com` o `afdverify.contoso.azurefd.net` tramite il mapping temporaneo di afdverify come mappato per convalidare anche la mappa CNAME per i caratteri jolly (il DNS di Azure supporta i record con caratteri jolly).

È inoltre possibile aggiungere tutti i sottodomini a livello singolo del dominio con caratteri jolly negli host front-end se non raggiungono il massimo. limite degli host frontend. Questa funzionalità può essere necessaria per definire una route diversa per un sottodominio rispetto al resto dei domini (dal dominio con caratteri jolly) o avere un criterio WAF diverso per un sottodominio specifico. Così, `*.contoso.com` permetterà `foo.contoso.com` l'aggiunta senza dover `foo.bar.contoso.com` dimostrare di nuovo la proprietà `*.contoso.com`del dominio, ma non come che non è un sottodominio a livello singolo di . Per `foo.bar.contoso.com` aggiungere senza ulteriore `*.bar.contosonews.com` convalida della proprietà del dominio, sarà necessario aggiungere.

### <a name="limitations"></a>Limitazioni

1. Se viene aggiunto un dominio con caratteri jolly in un determinato profilo Front Door, non è possibile aggiungere lo stesso ad altri profili Front Door. 
2. Se viene aggiunto un dominio con caratteri jolly in un determinato profilo Front Door, non è possibile aggiungere eventuali sottodomini di tale dominio con caratteri jolly ad altri Front Door o a una rete CDN di Azure dal profilo Microsoft
3. Se un sottodominio di un dominio con caratteri jolly viene aggiunto in un profilo Front Door o in una rete CDN di Azure dal profilo Microsoft, il dominio con caratteri jolly non può essere aggiunto ad altri profili Front Door. 
4. Se due profili (Front Door o cdn di Azure di Microsoft) hanno vari sottodomini di un dominio radice, i domini con caratteri jolly non possono essere aggiunti a nessuno dei profili.

## <a name="certificate-binding-for-wildcard-domains-and-its-subdomains"></a>Associazione di certificati per i domini con caratteri jolly e i relativi sottodomini

Per accettare il traffico HTTPS nel dominio con caratteri jolly, è necessario abilitare HTTPS nel dominio con caratteri jolly. L'associazione del certificato per il dominio con caratteri jolly richiede un certificato con caratteri jolly, ovvero il nome del soggetto del certificato deve avere anche il dominio con caratteri jolly.

> [!NOTE]
> Attualmente, solo utilizzando l'opzione di certificato SSL personalizzato è disponibile per l'abilitazione di HTTPS per i domini con caratteri jolly. I certificati gestiti Front Door non possono essere utilizzati per i domini con caratteri jolly. 

È possibile scegliere di utilizzare lo stesso certificato con caratteri jolly dall'insieme di credenziali delle chiavi per i sottodomini oppure è supportato anche l'utilizzo di certificati gestiti Front Door per i sottodomini.
Se viene aggiunto un sottodominio per un dominio con caratteri jolly e al dominio con caratteri jolly è già associato un certificato, HTTPS per questo sottodominio non può essere disabilitato. Per impostazione predefinita, il sottodominio utilizzerà l'associazione del certificato del dominio con caratteri jolly, a meno che non venga sostituito da un certificato dell'insieme di credenziali delle chiavi o da un certificato gestito Front Door diverso.

## <a name="web-application-firewall-for-wildcard-domains-and-its-subdomains"></a>Firewall applicazione Web per i domini con caratteri jolly e i relativi sottodomini

I criteri WAF possono essere collegati a un dominio con caratteri jolly simile ad altri domini. Un criterio WAF diverso può essere applicato a un sottodominio di un dominio con caratteri jolly. Per i sottodomini, è necessario specificare in modo esplicito il criterio WAF da utilizzare e anche se si tratta dello stesso criterio del dominio con caratteri jolly. I sottodomini **non** erediteranno automaticamente il criterio WAF dal dominio con caratteri jolly.

Se si dispone di uno scenario in cui non si desidera eseguire WAF per un sottodominio, è possibile creare un criterio WAF vuoto senza set di regole gestiti o personalizzati.

## <a name="routing-rules-for-wildcard-domains-and-its-subdomains"></a>Regole di routing per i domini con caratteri jolly e i relativi sottodomini

Quando si configura una regola di routing, è possibile selezionare un dominio con caratteri jolly come host front-end. È inoltre possibile avere un comportamento di route diverso per il dominio con caratteri jolly rispetto ai sottodomini. Come descritto in [come Front Door instrada la corrispondenza](front-door-route-matching.md), la corrispondenza più specifica per il dominio tra diverse regole di routing verrà scelta in fase di esecuzione.

> [!WARNING]
> Se si dispone di due regole di routing, ad esempio **Route 1**: `*.foo.com/*` mappate al pool back-end A e alla route **2:** `bar.foo.com/somePath/*` mappate al pool back-end B e, se una richiesta arriva per `bar.foo.com/anotherPath/*`, i client vedranno errori poiché Front Door non troverà alcuna corrispondenza in entrambe le route. Questo perché in base [all'algoritmo di corrispondenza del percorso,](front-door-route-matching.md)Front Door selezionerà Route 2 in base a una corrispondenza di dominio più specifica, ma solo per scoprire che non esistono modelli di percorso corrispondenti. 


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni su come [aggiungere un dominio personalizzato su Front Door](front-door-custom-domain.md).
- Informazioni su come [abilitare HTTPS in un dominio personalizzato.](front-door-custom-domain-https.md)
