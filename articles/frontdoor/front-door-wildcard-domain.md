---
title: Sportello anteriore di Azure-supporto per i domini con caratteri jolly
description: Questo articolo illustra come il front-end di Azure supporta il mapping e la gestione di domini con caratteri jolly nell'elenco dei domini personalizzati.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81768312"
---
# <a name="wildcard-domains"></a>Domini con caratteri jolly

Oltre ai domini e ai sottodomini Apex, è possibile eseguire il mapping di un nome di dominio con caratteri jolly all'elenco di host front-end o di domini personalizzati nel profilo di porta anteriore di Azure. La presenza di domini con caratteri jolly nella configurazione di Azure front door semplifica il comportamento del routing del traffico per più sottodomini per un'API, un'applicazione o un sito Web dalla stessa regola di routing. Non è necessario modificare la configurazione per aggiungere o specificare separatamente ogni sottodominio. È ad esempio possibile definire il routing `customer1.contoso.com`per, `customer2.contoso.com`e `customerN.contoso.com` usando la stessa regola di routing e l'aggiunta del dominio `*.contoso.com`con caratteri jolly.

Gli scenari principali migliorati con il supporto per i domini con caratteri jolly includono:

- Non è necessario caricare ogni sottodominio nel profilo di sportello anteriore di Azure e quindi abilitare HTTPS per associare un certificato per ogni sottodominio.
- Se un'applicazione aggiunge un nuovo sottodominio, non è più necessario modificare la configurazione della porta di ingresso di Azure di produzione. In precedenza era necessario aggiungere il sottodominio, associarvi un certificato, allegare un criterio web application firewall (WAF) e quindi aggiungere il dominio a diverse regole di routing.

> [!NOTE]
> Attualmente, i domini con caratteri jolly sono supportati solo tramite API, PowerShell e l'interfaccia della riga di comando di Azure. Il supporto per l'aggiunta e la gestione di domini con caratteri jolly nell'portale di Azure non è disponibile.

## <a name="adding-wildcard-domains"></a>Aggiunta di domini con caratteri jolly

È possibile aggiungere un dominio con caratteri jolly nella sezione per gli host o i domini front-end. Analogamente ai sottodomini, Azure front door verifica che esista il mapping dei record CNAME per il dominio con caratteri jolly. Questo mapping DNS può essere un mapping di record CNAME diretto `*.contoso.com` come eseguito `contoso.azurefd.net`il mapping a. In alternativa, è possibile usare il mapping temporaneo afdverify. Ad esempio, `afdverify.contoso.com` mappato `afdverify.contoso.azurefd.net` a convalida la mappa dei record CNAME per il carattere jolly.

> [!NOTE]
> DNS di Azure supporta record con caratteri jolly.

È possibile aggiungere tutti i sottodomini a singolo livello del dominio con caratteri jolly negli host front-end, fino al limite degli host front-end. Questa funzionalità potrebbe essere necessaria per:

- Definizione di una route diversa per un sottodominio rispetto al resto dei domini (dal dominio con caratteri jolly).

- Avere un criterio WAF diverso per un sottodominio specifico. Ad esempio, `*.contoso.com` consente di `foo.contoso.com` aggiungere senza dover dimostrare nuovamente la proprietà del dominio. Ma non consente `foo.bar.contoso.com` perché non è un sottodominio a livello singolo di `*.contoso.com`. Per aggiungere `foo.bar.contoso.com` senza ulteriore convalida della proprietà del `*.bar.contosonews.com` dominio, è necessario aggiungere.

È possibile aggiungere i domini con caratteri jolly e i relativi sottodomini con determinate limitazioni:

- Se viene aggiunto un dominio con caratteri jolly a un profilo di porta anteriore di Azure:
  - Il dominio con caratteri jolly non può essere aggiunto a un altro profilo di porta anteriore di Azure.
  - Non è possibile aggiungere sottodomini di primo livello del dominio con caratteri jolly a un altro profilo di Azure front door o a un profilo di rete per la distribuzione di contenuti di Azure.
- Se un sottodominio di un dominio con caratteri jolly viene aggiunto a un profilo della porta anteriore di Azure o a un profilo di rete per la distribuzione di contenuti di Azure, il dominio con caratteri jolly non può essere aggiunto ad altri profili di porte di Azure.
- Se due profili (Azure front door o rete per la distribuzione di contenuti di Azure) hanno diversi sottodomini di un dominio radice, non è possibile aggiungere i domini con caratteri jolly a uno dei profili.

## <a name="certificate-binding"></a>Associazione certificato

Per accettare il traffico HTTPS nel dominio con caratteri jolly, è necessario abilitare HTTPS nel dominio con caratteri jolly. Il binding del certificato per un dominio con caratteri jolly richiede un certificato con caratteri jolly. Ovvero, il nome del soggetto del certificato deve avere anche il dominio con caratteri jolly.

> [!NOTE]
> Attualmente, è disponibile solo l'uso di un certificato SSL personalizzato per abilitare HTTPS per i domini con caratteri jolly. Non è possibile usare i certificati gestiti di Azure front door per i domini con caratteri jolly.

È possibile scegliere di usare lo stesso certificato con caratteri jolly da Azure Key Vault o dai certificati gestiti front door di Azure per i sottodomini.

Se viene aggiunto un sottodominio per un dominio con caratteri jolly a cui è già associato un certificato, non è possibile disabilitare HTTPS per il sottodominio. Il sottodominio usa l'associazione al certificato per il dominio con caratteri jolly, a meno che un altro Key Vault o un certificato gestito di Azure front door non lo esegua.

## <a name="waf-policies"></a>Criteri di WAF

I criteri WAF possono essere collegati a domini con caratteri jolly, in modo analogo ad altri domini. Un criterio WAF diverso può essere applicato a un sottodominio di un dominio con caratteri jolly. Per i sottodomini, è necessario specificare i criteri WAF da usare anche se si tratta dello stesso criterio del dominio con caratteri jolly. I sottodomini non *ereditano automaticamente i* criteri WAF dal dominio con caratteri jolly.

Se non si vuole eseguire un criterio WAF per un sottodominio, è possibile creare un criterio WAF vuoto senza RuleSet gestiti o personalizzati.

## <a name="routing-rules"></a>Regole di routing

Quando si configura una regola di routing, è possibile selezionare un dominio con caratteri jolly come host front-end. È anche possibile avere un comportamento di route diverso per i domini e i sottodomini con caratteri jolly. Come descritto nel modo in cui il front-end di [Azure esegue la corrispondenza delle route](front-door-route-matching.md), la corrispondenza più specifica per il dominio tra regole di routing diverse viene scelta in fase di esecuzione.

> [!IMPORTANT]
> È necessario disporre di modelli di percorso corrispondenti nelle regole di routing oppure i client visualizzeranno errori. Ad esempio, sono presenti due regole di routing come Route 1`*.foo.com/*` (con mapping al pool back-end a) e route`bar.foo.com/somePath/*` 2 (con mapping al pool back-end B). Viene quindi ricevuta una richiesta per `bar.foo.com/anotherPath/*`. Il front-end di Azure seleziona la Route 2 in base a una corrispondenza di dominio più specifica, ma non trova alcun modello di percorso corrispondente tra le route.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare un profilo di sportello anteriore di Azure](quickstart-create-front-door.md).
- Informazioni su come [aggiungere un dominio personalizzato all'ingresso di Azure](front-door-custom-domain.md).
- Informazioni su come [abilitare HTTPS in un dominio personalizzato](front-door-custom-domain-https.md).
