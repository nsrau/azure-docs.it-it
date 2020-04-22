---
title: Porta frontale di Azure - Supporto per i domini con caratteri jolly
description: Questo articolo consente di comprendere in che modo Azure Front Door supporta il mapping e la gestione dei domini con caratteri jolly nell'elenco dei domini personalizzati.
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2020
ms.author: sharadag
ms.openlocfilehash: 6d8a6d6f0b05b9b7fd0144959c82b6a2c9e659a3
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768312"
---
# <a name="wildcard-domains"></a>Domini con caratteri jolly

Oltre ai domini apice e ai sottodomini, è possibile eseguire il mapping di un nome di dominio con caratteri jolly all'elenco di host front-end o domini personalizzati nel profilo di Azure Front Door. La presenza di domini con caratteri jolly nella configurazione di Azure Front Door semplifica il comportamento di routing del traffico per più sottodomini per un'API, un'applicazione o un sito Web dalla stessa regola di routing. Non è necessario modificare la configurazione per aggiungere o specificare ogni sottodominio separatamente. Ad esempio, è possibile definire `customer1.contoso.com` `customer2.contoso.com`il `customerN.contoso.com` routing per , , e utilizzando `*.contoso.com`la stessa regola di routing e aggiungendo il dominio con caratteri jolly .

Gli scenari chiave migliorati con il supporto per i domini con caratteri jolly includono:Key scenarios that are improved with support for wildcard domains include:

- Non è necessario eseguire l'onboarding di ogni sottodominio nel profilo di Azure Front Door e quindi abilitare HTTPS per associare un certificato per ogni sottodominio.
- Non è più necessario modificare la configurazione di Azure Front Door di produzione se un'applicazione aggiunge un nuovo sottodominio. In precedenza, era necessario aggiungere il sottodominio, associare un certificato ad esso, collegare un criterio di Web Application Firewall (WAF) e quindi aggiungere il dominio a regole di routing diverse.

> [!NOTE]
> Attualmente, i domini con caratteri jolly sono supportati solo tramite API, PowerShell e l'interfaccia della riga di comando di Azure.Currently, wildcard domains are only supported via API, PowerShell, and the Azure CLI. Il supporto per l'aggiunta e la gestione di domini con caratteri jolly nel portale di Azure non è disponibile.

## <a name="adding-wildcard-domains"></a>Aggiunta di domini con caratteri jolly

È possibile aggiungere un dominio con caratteri jolly nella sezione per gli host o i domini front-end. Analogamente ai sottodomini, Azure Front Door convalida la disponibilità di mapping di record CNAME per il dominio con caratteri jolly. Questo mapping DNS può essere un `*.contoso.com` mapping diretto `contoso.azurefd.net`di record CNAME come mappato a . In alternativa, è possibile utilizzare il mapping temporaneo di afdverify. Ad esempio, `afdverify.contoso.com` il `afdverify.contoso.azurefd.net` mapping per convalidare la mappa dei record CNAME per il carattere jolly.

> [!NOTE]
> DNS di Azure supporta record con caratteri jolly.

È possibile aggiungere tutti i sottodomini a livello singolo del dominio con caratteri jolly negli host front-end, fino al limite degli host front-end. Questa funzionalità potrebbe essere necessaria per:This functionality might be required for:

- Definizione di una route diversa per un sottodominio rispetto al resto dei domini (dal dominio con caratteri jolly).

- Avere un criterio WAF diverso per un sottodominio specifico. Ad esempio, `*.contoso.com` `foo.contoso.com` consente l'aggiunta senza dover dimostrare nuovamente la proprietà del dominio. Ma non lo `foo.bar.contoso.com` consente perché non è un sottodominio a livello singolo di `*.contoso.com`. Per `foo.bar.contoso.com` aggiungere senza ulteriore `*.bar.contosonews.com` convalida della proprietà del dominio, è necessario aggiungere.

È possibile aggiungere domini con caratteri jolly e relativi sottodomini con alcune limitazioni:

- Se un dominio con caratteri jolly viene aggiunto a un profilo di Azure Front Door:If a wildcard domain is added to an Azure Front Door profile:
  - Il dominio con caratteri jolly non può essere aggiunto a nessun altro profilo di Azure Front Door.The wildcard domain can't be added to any other Azure Front Door profile.
  - I sottodomini di primo livello del dominio con caratteri jolly non possono essere aggiunti a un altro profilo porta frontale di Azure o a un profilo Rete per la distribuzione di contenuti di Azure.First-level subdomains of the wildcard domain can't be added to another Azure Front Door profile or an Azure Content Delivery Network profile.
- Se un sottodominio di un dominio con caratteri jolly viene aggiunto a un profilo di Azure Front Door o a un profilo di rete per la distribuzione di contenuti di Azure, il dominio con caratteri jolly non può essere aggiunto ad altri profili di Azure Front Door.
- Se due profili (Azure Front Door o Rete per la distribuzione di contenuti di Azure) hanno vari sottodomini di un dominio radice, i domini con caratteri jolly non possono essere aggiunti a nessuno dei profili.

## <a name="certificate-binding"></a>Associazione certificati

Per accettare il traffico HTTPS nel dominio con caratteri jolly, è necessario abilitare HTTPS nel dominio con caratteri jolly. L'associazione di certificati per un dominio con caratteri jolly richiede un certificato con caratteri jolly. Ovvero, il nome del soggetto del certificato deve avere anche il dominio con caratteri jolly.

> [!NOTE]
> Attualmente, solo utilizzando l'opzione di certificato SSL personalizzato è disponibile per l'abilitazione di HTTPS per i domini con caratteri jolly. I certificati gestiti di Azure Front Door non possono essere usati per i domini con caratteri jolly.

È possibile scegliere di usare lo stesso certificato con caratteri jolly da Archiviazione delle chiavi di Azure o dai certificati gestiti di Azure Front Door per i sottodomini.

Se viene aggiunto un sottodominio per un dominio con caratteri jolly a cui è già associato un certificato, HTTPS per il sottodominio non può essere disabilitato. Il sottodominio usa l'associazione del certificato per il dominio con caratteri jolly, a meno che non venga eseguito l'override di un insieme di credenziali delle chiavi o di un certificato gestito di Azure Front Door.The subdomain uses the certificate binding for the wildcard domain, unless a different Key Vault or Azure Front Door managed certificate overrides it.

## <a name="waf-policies"></a>Politiche WAF

I criteri WAF possono essere collegati a domini con caratteri jolly, in modo simile ad altri domini. Un criterio WAF diverso può essere applicato a un sottodominio di un dominio con caratteri jolly. Per i sottodomini, è necessario specificare il criterio WAF da utilizzare anche se si tratta dello stesso criterio del dominio con caratteri jolly. I sottodomini *non* ereditano automaticamente il criterio WAF dal dominio con caratteri jolly.

Se non si desidera eseguire un criterio WAF per un sottodominio, è possibile creare un criterio WAF vuoto senza set di regole gestiti o personalizzati.

## <a name="routing-rules"></a>Regole di routing

Quando si configura una regola di routing, è possibile selezionare un dominio con caratteri jolly come host front-end. È inoltre possibile avere un comportamento di route diverso per i domini con caratteri jolly e i sottodomini. Come descritto in Come descritto in [Come lo sportello frontale di Azure instrada](front-door-route-matching.md)la corrispondenza , in fase di esecuzione viene scelta la corrispondenza più specifica per il dominio tra regole di routing diverse.

> [!IMPORTANT]
> È necessario disporre di modelli di percorso corrispondenti tra le regole di routing, altrimenti i client vedranno errori. Ad esempio, si dispone di due`*.foo.com/*` regole di routing come Route 1`bar.foo.com/somePath/*` (mappato al pool back-end A) e Route 2 (mappato al pool back-end B). Quindi, arriva una `bar.foo.com/anotherPath/*`richiesta per . La porta frontale di Azure seleziona Route 2 in base a una corrispondenza di dominio più specifica, solo per non trovare modelli di percorso corrispondenti tra le route.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare un profilo di Porta frontale](quickstart-create-front-door.md)di Azure.
- Informazioni su come [aggiungere un dominio personalizzato in Azure Front Door](front-door-custom-domain.md).
- Informazioni su come [abilitare HTTPS in un dominio personalizzato.](front-door-custom-domain-https.md)
