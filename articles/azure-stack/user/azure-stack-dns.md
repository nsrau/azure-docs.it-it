---
title: DNS nello Stack di Azure | Documenti Microsoft
description: Tramite DNS nello Stack di Azure
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: mabrigg
ms.openlocfilehash: 4e854a2751ce366e3ca3a353487f2c972401c248
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
---
# <a name="using-dns-in-azure-stack"></a>Tramite DNS nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Stack di Azure supporta le seguenti funzionalità di sistema DNS (Domain Name):

* Risoluzione del nome host DNS
* Creazione e gestione di zone DNS e i record tramite l'API

## <a name="support-for-dns-hostname-resolution"></a>Supporto per la risoluzione dei nomi host DNS

È possibile specificare un'etichetta di nome di dominio DNS per le risorse IP pubbliche. Usa Azure Stack *domainnamelabel.location*. cloudapp.azurestack.external per il nome dell'etichetta e mappe che per l'indirizzo IP pubblico è risolvere nello Stack di Azure i server DNS gestiti.

Ad esempio, se si crea una risorsa IP pubblica con **contoso** come un'etichetta del nome di dominio nel percorso dello Stack locali di Azure, la [il nome di dominio completo](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN)  **Contoso.Local.cloudapp.azurestack.External** venga risolto nell'indirizzo IP pubblico della risorsa. È possibile utilizzare questo nome di dominio completo per creare un dominio personalizzato, record CNAME che punti all'indirizzo IP pubblico nello Stack di Azure.

Per ulteriori informazioni sulla risoluzione dei nomi, consultare il [risoluzione del DNS](https://docs.microsoft.com/en-us/azure/dns/dns-for-azure-services?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) articolo.

> [!IMPORTANT]
> Ogni etichetta del nome di dominio che crei deve essere univoco nella relativa posizione dello Stack di Azure.

Nella schermata successiva di acquisizione viene illustrata la **Crea indirizzo IP pubblico** finestra di dialogo per la creazione di un indirizzo IP pubblico con il portale.

![Creare un indirizzo IP pubblico](media/azure-stack-whats-new-dns/image01.png)

**Scenario di esempio**

È necessario un bilanciamento del carico di elaborazione delle richieste da un'applicazione web. Dietro il carico di bilanciamento del carico è un sito web in esecuzione in uno o più macchine virtuali. È possibile accedere utilizzando un nome DNS, anziché un indirizzo IP il sito web con bilanciamento del carico.

## <a name="create-and-manage-dns-zones-and-records-using-the-api"></a>Creare e gestire le zone DNS e i record tramite l'API

È possibile creare e gestire le zone DNS e i record nello Stack di Azure.

Stack Azure fornisce un servizio DNS come Azure, utilizzando le API che siano coerenti con le API DNS di Azure.  Grazie all'ampia nei domini nel DNS dello Stack di Azure, è possibile gestire i record DNS utilizzando lo stesso credenziali, le API e strumenti. È anche possibile usare la fatturazione stesso e supportano come altri servizi di Azure.

L'infrastruttura di Azure Stack DNS è più compatta di quelle di Azure. Le dimensioni e la posizione di una distribuzione di Azure Stack influirà ambito DNS, scalabilità e prestazioni. Ciò significa anche che le prestazioni, disponibilità, distribuzione globale e a disponibilità elevata può variare da una distribuzione a distribuzione.

## <a name="comparison-with-azure-dns"></a>Confronto con DNS di Azure

DNS nello Stack di Azure è simile a DNS in Azure, ma ci sono eccezioni principali, che è necessario comprendere.

* **Non supporta il record AAAA**

    Stack di Azure non supporta record AAAA perché lo Stack di Azure non supporta gli indirizzi IPv6.  Si tratta di una differenza fondamentale tra DNS in Azure e Azure Stack.
* **Non è multi-tenant**

    Il servizio DNS nello Stack di Azure non è multi-tenant. Ogni tenant non è possibile creare la zona DNS stessa. La prima sottoscrizione che tenta di creare la zona ha esito positivo, e le richieste successive non riuscire.  È un problema noto e una differenza fondamentale tra Azure e Azure Stack DNS. Questo problema verrà risolto in una versione futura.
* **Tag, i metadati e i valori eTag**

    Esistono differenze minime nei come Stack di Azure gestisce i tag, i metadati, ETag e limiti.

Per altre informazioni su DNS di Azure, vedere [zone DNS e registra](../../dns/dns-zones-records.md).

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
