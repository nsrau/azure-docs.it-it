---
title: DNS in Azure Stack | Microsoft Docs
description: Uso di DNS in Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.openlocfilehash: ba1e310234485d972646320f082d8b882a3d43f1
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052343"
---
# <a name="using-dns-in-azure-stack"></a>Uso di DNS in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Azure Stack supporta le seguenti funzionalità di sistema DNS (Domain Name):

* Risoluzione del nome host DNS
* Creare e gestire le zone DNS e i record usando l'API

## <a name="support-for-dns-hostname-resolution"></a>Supporto per la risoluzione del nome host DNS

È possibile specificare un'etichetta di nome di dominio DNS per le risorse IP pubblico. Usa Azure Stack **domainnamelabel.location.cloudapp.azurestack.external** per il nome dell'etichetta e le mappe per l'indirizzo IP pubblico in Azure Stack gestiti i server DNS.

Ad esempio, se si crea una risorsa IP pubblica con **contoso** come un'etichetta del nome di dominio nel percorso locale, Azure Stack le [il nome di dominio completo (FQDN)](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)  **Contoso.Local.cloudapp.azurestack.External** viene risolto l'indirizzo IP pubblico della risorsa. È possibile usare questo nome di dominio completo per creare un dominio personalizzato record CNAME che punti all'indirizzo IP pubblico in Azure Stack.

Per altre informazioni sulla risoluzione dei nomi, vedere la [risoluzione DNS](../../dns/dns-for-azure-services.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) articolo.

> [!IMPORTANT]
> Ogni etichetta del nome di dominio che si crea deve essere univoco nella rispettiva posizione di Azure Stack.

La schermata seguente mostra le **Crea indirizzo IP pubblico** finestra di dialogo per la creazione di un indirizzo IP pubblico usando il portale:

![Creare un indirizzo IP pubblico](media/azure-stack-whats-new-dns/image01.png)

### <a name="example-scenario"></a>Scenario di esempio

È necessario un bilanciamento del carico di elaborazione delle richieste da un'applicazione web. Dietro il carico balancer è un sito web in esecuzione in uno o più macchine virtuali. È possibile accedere usando un nome DNS, anziché un indirizzo IP il sito web con bilanciamento del carico.

## <a name="create-and-manage-dns-zones-and-records-using-the-api"></a>Creare e gestire le zone DNS e i record usando l'API

È possibile creare e gestire le zone DNS e record in Azure Stack.

Azure Stack offre un servizio DNS simile in Azure, usando le API che siano coerenti con l'API di DNS di Azure.  Ospitando i domini in DNS di Azure Stack, è possibile gestire i record DNS usando le stesse credenziali, API e strumenti. È anche possibile usare la stessa fatturazione e supporto tecnico degli altri servizi di Azure.

L'infrastruttura DNS di Azure Stack è più compatto rispetto a Azure. Le dimensioni e la posizione di una distribuzione di Azure Stack influisce su ambito, scalabilità e prestazioni DNS. Questo significa anche che le prestazioni, disponibilità, distribuzione globale e disponibilità elevata può variare da una distribuzione per distribuzione.

## <a name="comparison-with-azure-dns"></a>Confronto con DNS di Azure

DNS in Azure Stack è simile a DNS di Azure, ma esistono alcune importanti eccezioni:

* **Non supporta i record AAAA**: Azure Stack non supporta i record AAAA perché Azure Stack non supporta gli indirizzi IPv6. Questa è una differenza chiave tra DNS in Azure e Azure Stack.

* **Non è multitenant**: Il servizio DNS in Azure Stack non multi-tenant. Ogni tenant non è possibile creare la zona DNS stesso. Solo la prima sottoscrizione che tenta di creare la zona ha esito positivo e le successive richieste hanno esito negativo. Questa è una differenza chiave tra Azure e DNS di Azure Stack.

* **I tag, metadati e gli eTag**: Esistono piccole differenze nel modo in cui Azure Stack gestisce i tag, i metadati, gli eTag e limiti.

Per altre informazioni sul servizio DNS di Azure, vedere [zone e record DNS](../../dns/dns-zones-records.md).

### <a name="tags"></a>Tag

DNS di Azure Stack supporta l'uso di tag di Azure Resource Manager in risorse della zona DNS. Non supporta i tag nei set di record DNS, anche se in alternativa **metadati** è supportato nei set di record DNS, come illustrato nella sezione successiva.

### <a name="metadata"></a>Metadata

In alternativa ai tag di set di record, DNS di Azure Stack supporta l'annotazione di set di record usando *metadati*. Analogamente ai tag, i metadati permettono di associare coppie nome-valore a ogni set di record. Ad esempio, ciò può essere utile per registrare lo scopo di ogni set di record. A differenza dei tag, è possibile usare i metadati per fornire una visualizzazione filtrata della fattura di Azure e i metadati non è possibile specificare criteri di Azure Resource Manager.

### <a name="etags"></a>ETag

Si supponga che due persone o due processi provino a modificare un record DNS nello stesso momento. Quale prevale? E quello che prevale sa di avere sovrascritto le modifiche create da qualcun altro?

Usa DNS di Azure Stack *Etags* per gestire in modo sicuro le modifiche simultanee alla stessa risorsa. Gli eTag sono diversi da Azure Resource Manager *tag*. Ogni risorsa DNS (zona o set di record) ah un Etag associato. Quando viene recuperata una risorsa, l'Etag verrà anche recuperato. Quando si aggiorna una risorsa, è possibile scegliere di passare di nuovo il valore Etag in modo che DNS di Azure Stack può verificare che il valore Etag nella corrisponde a server. Dal momento che ogni aggiornamento di una risorsa comporta la rigenerazione dell'Etag, una mancata corrispondenza del valore Etag indica che si è verificata una modifica simultanea. Gli ETag possono essere usati anche quando si crea una nuova risorsa per garantire che questa non esista già.

Per impostazione predefinita, i cmdlet di PowerShell per Azure Stack DNS utilizzano eTag per bloccare le modifiche simultanee a zone e set di record. È possibile usare l'opzione facoltativa `-Overwrite` passare per disattivare i controlli di Etag, in modo che eventuali modifiche simultanee si sono verificati per essere sovrascritti.

A livello dell'API REST di Azure Stack DNS, gli eTag vengono specificati usando le intestazioni HTTP. Il comportamento è descritto nella tabella seguente:

| Intestazione | Comportamento|
|--------|---------|
| Nessuna   | PUT riesce sempre (nessun controllo di Etag)|
| If-match| PUT riesce solo se la risorsa esiste e l'Etag corrisponde|
| If-match *| PUT riesce solo se la risorsa esiste|
| If-none-match *| PUT riesce solo se la risorsa non esiste|

### <a name="limits"></a>Limiti

I seguenti limiti predefiniti si applicano quando si usa DNS di Azure Stack:

| Risorsa| Limite predefinito|
|---------|--------------|
| Zone per ogni sottoscrizione| 100|
| Set di record per ogni zona| 5000|
| Record per ogni set di record| 20|

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione a iDNS per Azure Stack](azure-stack-understanding-dns.md)
