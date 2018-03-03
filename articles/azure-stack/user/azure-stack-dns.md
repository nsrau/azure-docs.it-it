---
title: DNS nello Stack di Azure | Documenti Microsoft
description: DNS in Azure Stack
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 394abe5295af4ed99e48d50b5886ac93af87e875
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2018
---
# <a name="dns-in-azure-stack"></a>DNS in Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Stack di Azure include le funzionalità DNS seguenti:
* Supporto per la risoluzione dei nomi host DNS
* Creare e gestire le zone DNS e i record tramite l'API

## <a name="support-for-dns-hostname-resolution"></a>Supporto per la risoluzione dei nomi host DNS
È possibile specificare un'etichetta di nome di dominio DNS per una risorsa IP pubblica, che consente di creare un mapping per *domainnamelabel.location*. cloudapp.azurestack.external all'indirizzo IP pubblico nello Stack di Azure gestiti i server DNS.  

Ad esempio, se si crea una risorsa IP pubblica con **contoso** come un'etichetta del nome di dominio nel percorso dello Stack locali di Azure, il nome di dominio completo (FQDN) **contoso.local.cloudapp.azurestack.external**viene risolto nell'indirizzo IP pubblico della risorsa. È possibile utilizzare questo nome di dominio completo per creare un dominio personalizzato, record CNAME che punti all'indirizzo IP pubblico nello Stack di Azure.

> [!IMPORTANT]
> Ogni etichetta del nome di dominio creato deve essere univoco all'interno di percorso Stack di Azure.

Se si crea l'indirizzo IP pubblico con il portale, l'aspetto è simile al seguente:

![Creare un indirizzo IP pubblico](media/azure-stack-whats-new-dns/image01.png)

Questa configurazione è utile se si desidera associare un indirizzo IP pubblico a una risorsa con bilanciamento del carico. Ad esempio, potrebbe essere un bilanciamento del carico di elaborazione delle richieste da un'applicazione web. Dietro il carico di bilanciamento del carico è un sito web che si trova in uno o più macchine virtuali. È ora possibile accedere il sito web con bilanciamento del carico da un nome DNS, anziché da un indirizzo IP.

## <a name="create-and-manage-dns-zones-and-records-using-api"></a>Creare e gestire le zone DNS e i record tramite l'API
È possibile creare e gestire le zone DNS e i record nello Stack di Azure.  

Stack Azure fornisce un servizio DNS come Azure, utilizzando le API che siano coerenti con le API DNS di Azure.  Ospitando i domini di DNS dello Stack di Azure, è possibile gestire i record DNS con le stesse credenziali, API, strumenti, fatturazione e il supporto come altri servizi di Azure. 

Per motivi di ovvi, l'infrastruttura di Azure Stack DNS è più compatta di quelle di Azure. Di conseguenza, l'ambito, scalabilità e prestazioni dipendono la scala della distribuzione Azure Stack, nonché l'ambiente in cui è distribuito.  In tal caso, ad esempio prestazioni, disponibilità, distribuzione globale e a disponibilità elevata (HA) può variare da una distribuzione a distribuzione.

## <a name="comparison-with-azure-dns"></a>Confronto con DNS di Azure
DNS nello Stack di Azure è simile a DNS in Azure, con due eccezioni principali:
* **Non supporta il record AAAA**

    Stack di Azure non supporta record AAAA perché lo Stack di Azure non supporta gli indirizzi IPv6.  Si tratta di una differenza fondamentale tra DNS in Azure e Azure Stack.
* **Non è multi-tenant**

    A differenza di Azure, il servizio DNS nello Stack di Azure non è multi-tenant. Pertanto, ogni tenant non è possibile creare la zona DNS stessa. La prima sottoscrizione che tenta di creare la zona ha esito positivo, e le richieste successive non riuscire.  È un problema noto e una differenza fondamentale tra Azure e Azure Stack DNS. Questo problema verrà risolto in una versione futura.

Inoltre, esistono alcune differenze nella modalità di implementazione di DNS di Azure Stack tag, i metadati, valori eTag e limiti.

Le informazioni seguenti si applica in particolare a Azure Stack DNS e variano leggermente da DNS di Azure. Per ulteriori informazioni su DNS di Azure, vedere [DNS zone e record](../../dns/dns-zones-records.md) nel sito di documentazione di Microsoft Azure.

### <a name="tags-metadata-and-etags"></a>Tag, i metadati e i valori eTag

**Tag**

DNS di Azure Stack supporta l'utilizzo di tag di gestione risorse di Azure in risorse di zona DNS. Non supporta i tag nel set di record DNS, anche se come alternativa 'metadata' è supportata nei record DNS imposta come descritto di seguito.

**Metadata**

Come alternativa ai tag del set di record DNS di Azure Stack supporta annotazione del set di record mediante 'metadata'. Analogamente ai tag, i metadati permettono di associare coppie nome-valore a ogni set di record. Ad esempio, questo può essere utile per registrare lo scopo di ogni set di record. Diversamente dai tag, i metadati non possono essere usati per fornire una visualizzazione filtrata della fattura di Azure e non possono essere specificati in criteri di Azure Resource Manager.

**ETag**

Si supponga che due persone o due processi provino a modificare un record DNS nello stesso momento. Quale prevale? E quello che prevale sa di avere sovrascritto le modifiche create da qualcun altro?

DNS di Azure Stack utilizza eTag per gestire le modifiche simultanee alla stessa risorsa in modo sicuro. ETag sono separati da Gestione risorse di Azure 'Tag'. Ogni risorsa DNS (zona o set di record) ah un Etag associato. Quando viene recuperata una risorsa, viene recuperato anche il relativo valore Etag. Quando si aggiorna una risorsa, è possibile scegliere passare il valore Etag in modo da Azure Stack DNS può verificare che il valore Etag nella corrisponde a server. Dal momento che ogni aggiornamento di una risorsa comporta la rigenerazione dell'Etag, una mancata corrispondenza del valore Etag indica che si è verificata una modifica simultanea. Gli ETag possono essere usati anche quando si crea una nuova risorsa per garantire che questa non esista già.

Per impostazione predefinita, Azure Stack DNS PowerShell Usa eTag per bloccare le modifiche simultanee a zone e set di record. L'opzione facoltativa *-Overwrite* può essere usata per disattivare i controlli di ETag e in questo caso le eventuali modifiche simultanee vengono sovrascritte.

Il livello delle API REST di Azure Stack DNS eTag vengono specificati utilizzando le intestazioni HTTP. Il relativo comportamento è illustrato nella tabella seguente:

| Intestazione | Comportamento|
|--------|---------|
| Nessuna   | PUT riesce sempre (nessun controllo di Etag)|
| If-match| PUT riesce solo se la risorsa esiste e l'Etag corrisponde|
| If-match *| PUT riesce solo se la risorsa esiste|
| If-none-match *| PUT riesce solo se la risorsa non esiste|

### <a name="limits"></a>Limiti

Quando si utilizza Azure Stack DNS, si applicano i limiti predefiniti seguenti:

| Risorsa| Limite predefinito|
|---------|--------------|
| Zone per ogni sottoscrizione| 100|
| Set di record per ogni zona| 5000|
| Record per ogni set di record| 20|

## <a name="next-steps"></a>Passaggi successivi
[Introduzione a nomi IDN per Azure Stack](azure-stack-understanding-dns.md)
