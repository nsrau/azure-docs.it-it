---
title: Versioni in gestione API di Azure | Microsoft Docs
description: Informazioni sul concetto di versioni in gestione API di Azure.
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 06/12/2020
ms.author: jodowns
ms.custom: fasttrack-new
ms.openlocfilehash: 578bb511175d88a1507af9520265a1acd068b27c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87097998"
---
# <a name="versions-in-azure-api-management"></a>Versioni in gestione API di Azure

Le versioni consentono di presentare gruppi di API correlate agli sviluppatori. È possibile usare le versioni per gestire in modo sicuro le modifiche di rilievo nell'API. I client possono scegliere di usare la nuova versione dell'API quando sono pronti, mentre i client esistenti continuano a usare una versione precedente. Le versioni sono differenziate tramite un identificatore di versione (ovvero qualsiasi valore stringa scelto) e uno schema di controllo delle versioni consente ai client di identificare la versione di un'API che desidera utilizzare.

Per la maggior parte degli scopi, ogni versione dell'API può essere considerata una propria API indipendente. Due diverse versioni API possono avere set di operazioni e criteri diversi.

Con le versioni è possibile:

- Pubblicare più versioni dell'API nello stesso momento.
- Usare un percorso, una stringa di query o un'intestazione per distinguere le versioni.
- Usare qualsiasi valore stringa per identificare la versione, che può essere un numero, una data o un nome.
- Mostrare le versioni dell'API raggruppate nel portale per sviluppatori.
- Eseguire un'API esistente (senza versione) e crearne una nuova versione senza suddividere i client esistenti.

[Per iniziare a usare le versioni, seguire questa procedura dettagliata.](./api-management-get-started-publish-versions.md)

## <a name="versioning-schemes"></a>Schemi di controllo delle versioni

Diversi sviluppatori di API hanno requisiti diversi per il controllo delle versioni. Gestione API di Azure non prescrive un unico approccio al controllo delle versioni, ma fornisce invece diverse opzioni.

### <a name="path-based-versioning"></a>Controllo delle versioni basato sul percorso

Quando si usa lo schema di controllo delle versioni dei percorsi, l'identificatore di versione deve essere incluso nel percorso URL per le richieste API.

Ad esempio, `https://apis.contoso.com/products/v1` e `https://apis.contoso.com/products/v2` possono fare riferimento alla stessa `products` API, ma alle versioni `v1` e `v2` rispettivamente.

Il formato di un URL della richiesta API quando si usa il controllo delle versioni basato su intestazione è: `https://{yourDomain}/{apiName}/{versionIdentifier}/{operationId}` .

### <a name="header-based-versioning"></a>Controllo delle versioni basato su intestazione

Quando si usa lo schema di controllo delle versioni delle intestazioni, l'identificatore di versione deve essere incluso in un'intestazione di richiesta HTTP per tutte le richieste API. È possibile specificare il nome dell'intestazione della richiesta HTTP.

Ad esempio, è possibile creare un'intestazione personalizzata denominata `Api-Version` e i client possono specificare `v1` o `v2` nel valore di questa intestazione.

### <a name="query-string-based-versioning"></a>Controllo delle versioni basato su stringa di query

Quando si usa lo schema di controllo delle versioni delle stringhe di query, l'identificatore di versione deve essere incluso in un parametro della stringa di query per tutte le richieste API. È possibile specificare il nome del parametro della stringa di query.

Il formato di un URL della richiesta API quando si usa il controllo delle versioni basato su stringa di query è: `https://{yourDomain}/{apiName}/{operationId}?{queryStringParameterName}={versionIdentifier}` .

Ad esempio, `https://apis.contoso.com/products?api-version=v1` e `https://apis.contoso.com/products/api-version=v2` possono fare riferimento alla stessa `products` API, ma alle versioni `v1` e `v2` rispettivamente.

## <a name="original-versions"></a>Versioni originali

Se si aggiunge una versione a un'API senza versione, verrà `Original` creata automaticamente una versione che risponderà sull'URL predefinito, senza specificare un identificatore di versione. La `Original` versione garantisce che tutti i chiamanti esistenti non vengano interrotti dal processo di aggiunta di una versione. Se si crea una nuova API con le versioni abilitate all'inizio, `Original` non viene creata una versione.

## <a name="how-versions-are-represented"></a>Rappresentazione delle versioni

Gestione API di Azure gestisce una risorsa denominata *set*di versioni, che rappresenta un set di versioni per una singola API logica. Quando si usa il portale di Azure per gestire le versioni non è visibile il set di versioni, ma se si interagisce con il servizio gestione API usando PowerShell, i modelli Gestione risorse o l'API Azure Resource Manager, è possibile visualizzare e gestire direttamente i set di versioni. Un set di versioni contiene il nome visualizzato dell'API con versione, nonché lo schema di [controllo](#versioning-schemes) delle versioni utilizzato per indirizzare le richieste alle versioni specificate.

Ogni versione di un'API viene gestita come risorsa API, che viene quindi associata a un set di versioni. Un set di versioni potrebbe contenere API con operazioni o criteri molto diversi, il che riflette il fatto che è possibile apportare modifiche significative tra le versioni dell'API.

### <a name="migrating-a-non-versioned-api-to-a-versioned-api"></a>Migrazione di un'API senza versione a un'API con versione

Quando si usa il portale di Azure per abilitare il controllo delle versioni su un'API esistente, vengono apportate le seguenti modifiche alle risorse di gestione API:

 * Viene creato un nuovo set di versioni.
 * La versione esistente viene mantenuta e [configurata come `Original` versione dell'API](#original-versions). L'API è collegata al set di versioni ma non richiede che venga specificato un identificatore di versione.
 * La nuova versione viene creata come nuova API ed è collegata al set di versioni. Per accedere a questa nuova API è necessario usare lo schema di controllo delle versioni e l'identificatore.

## <a name="versions-and-revisions"></a>Versioni e revisioni

Le versioni e le revisioni sono funzioni distinte. Ogni versione può avere più revisioni, proprio come un'API senza versioni. È possibile usare le revisioni senza usare versioni o viceversa. In genere le versioni vengono usate per separare le versioni dell'API con modifiche di rilievo, mentre le revisioni possono essere usate per modifiche secondarie e non di rilievo a un'API.

Se si ritiene che la revisione abbia modifiche di rilievo o se si desidera trasformare in modo formale la revisione in una versione beta/di test, è possibile creare una versione da una revisione. Utilizzando la portale di Azure, fare clic su "Crea versione da revisione" nel menu di scelta rapida revisione della scheda revisioni.

## <a name="developer-portal"></a>Portale per sviluppatori

Il [portale per sviluppatori](./api-management-howto-developer-portal.md) elenca ogni versione di un'API separatamente.

![Portale per sviluppatori di gestione API che visualizza un elenco di API con versione](media/api-management-versions/portal-list.png)

I dettagli di un'API mostrano anche un elenco di tutte le versioni di tale API. Una `Original` versione viene visualizzata senza un identificatore di versione.

![Portale per sviluppatori di gestione API che Visualizza i dettagli di un'API e un elenco di versioni per tale API](media/api-management-versions/portal-details.png)

> [!TIP]
> È necessario aggiungere le versioni API a un prodotto prima che siano visibili nel portale per sviluppatori.
