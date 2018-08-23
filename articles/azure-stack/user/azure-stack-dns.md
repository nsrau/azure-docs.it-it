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
ms.date: 08/15/2018
ms.author: sethm
ms.openlocfilehash: acb8b262256031ae8615180e0f55c98cb56b538d
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139303"
---
# <a name="using-dns-in-azure-stack"></a>Uso di DNS in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Azure Stack supporta le seguenti funzionalità di sistema DNS (Domain Name):

* Risoluzione del nome host DNS
* Creare e gestire le zone DNS e i record usando l'API

## <a name="support-for-dns-hostname-resolution"></a>Supporto per la risoluzione del nome host DNS

È possibile specificare un'etichetta di nome di dominio DNS per le risorse IP pubblico. Usa Azure Stack *domainnamelabel.location*. server DNS gestiti da cloudapp.azurestack.external per il nome dell'etichetta ed esegue il mapping di indirizzi per l'indirizzo IP pubblico in Azure Stack.

Ad esempio, se si crea una risorsa IP pubblica con **contoso** come un'etichetta del nome di dominio nel percorso locale Azure Stack, il [il nome di dominio completo](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN)  **Contoso.Local.cloudapp.azurestack.External** viene risolto l'indirizzo IP pubblico della risorsa. È possibile usare questo nome di dominio completo per creare un dominio personalizzato record CNAME che punti all'indirizzo IP pubblico in Azure Stack.

Per altre informazioni sulla risoluzione dei nomi, vedere la [risoluzione DNS](https://docs.microsoft.com/azure/dns/dns-for-azure-services?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) articolo.

> [!IMPORTANT]
> Ogni etichetta del nome di dominio che si crea deve essere univoco nella rispettiva posizione di Azure Stack.

Acquisizione di schermata successiva mostra il **Crea indirizzo IP pubblico** finestra di dialogo per la creazione di un indirizzo IP pubblico usando il portale.

![Creare un indirizzo IP pubblico](media/azure-stack-whats-new-dns/image01.png)

**Scenario di esempio**

È necessario un bilanciamento del carico di elaborazione delle richieste da un'applicazione web. Dietro il carico balancer è un sito web in esecuzione in uno o più macchine virtuali. È possibile accedere usando un nome DNS, anziché un indirizzo IP il sito web con bilanciamento del carico.

## <a name="create-and-manage-dns-zones-and-records-using-the-api"></a>Creare e gestire le zone DNS e i record usando l'API

È possibile creare e gestire le zone DNS e record in Azure Stack.

Azure Stack offre un servizio DNS, ad esempio Azure, usando le API che siano coerenti con le API di DNS di Azure.  Ospitando i domini in DNS di Azure Stack, è possibile gestire i record DNS usando le stesse credenziali, API e strumenti. È anche possibile usare la stessa fatturazione e supporto tecnico degli altri servizi di Azure.

L'infrastruttura DNS di Azure Stack è più compatta di quelle di Azure. Le dimensioni e la posizione di una distribuzione di Azure Stack influirà su ambito, scalabilità e prestazioni DNS. Questo significa anche che le prestazioni, disponibilità, distribuzione globale e a disponibilità elevata può variare da una distribuzione per distribuzione.

## <a name="comparison-with-azure-dns"></a>Confronto con DNS di Azure

DNS in Azure Stack è simile a DNS di Azure, ma sono presenti eccezioni principali, che è necessario conoscere.

* **Non supporta i record AAAA**

    Azure Stack non supporta i record AAAA perché Azure Stack non supporta gli indirizzi IPv6.  Questa è una differenza chiave tra DNS in Azure e Azure Stack.
* **Non è multi-tenant**

    Il servizio DNS in Azure Stack non multi-tenant. Ogni tenant non è possibile creare la zona DNS stesso. Solo la prima sottoscrizione che tenta di creare la zona ha esito positivo e le successive richieste hanno esito negativo.  È un problema noto e una differenza fondamentale tra Azure e DNS di Azure Stack. Questo problema verrà risolto in una versione futura.
* **I tag, metadati e gli eTag**

    Esistono piccole differenze nel modo in cui Azure Stack gestisce i tag, i metadati, gli eTag e limiti.

Per altre informazioni sul servizio DNS di Azure, vedere [zone e record DNS](../../dns/dns-zones-records.md).

### <a name="tags-metadata-and-etags"></a>I tag, metadati e gli eTag

**Tag**

DNS di Azure Stack supporta l'uso di tag di Azure Resource Manager in risorse della zona DNS. Non supporta i tag nei set di record DNS, anche se come alternativa 'metadata' è supportata nei set di record DNS come descritto di seguito.

**Metadata**

In alternativa ai tag di set di record, DNS di Azure Stack supporta l'annotazione tramite "metadati" set di record. Analogamente ai tag, i metadati permettono di associare coppie nome-valore a ogni set di record. Ad esempio, ciò può essere utile per registrare lo scopo di ogni set di record. Diversamente dai tag, i metadati non possono essere usati per fornire una visualizzazione filtrata della fattura di Azure e non possono essere specificati in criteri di Azure Resource Manager.

**ETag**

Si supponga che due persone o due processi provino a modificare un record DNS nello stesso momento. Quale prevale? E quello che prevale sa di avere sovrascritto le modifiche create da qualcun altro?

DNS di Azure Stack Usa gli eTag per gestire le modifiche simultanee alla stessa risorsa in modo sicuro. Gli eTag sono separati da Azure Resource Manager 'Tag'. Ogni risorsa DNS (zona o set di record) ah un Etag associato. Quando viene recuperata una risorsa, viene recuperato anche il relativo valore Etag. Quando si aggiorna una risorsa, è possibile scegliere di passare di nuovo il valore Etag in modo che DNS di Azure Stack può verificare che il valore Etag nella corrisponde a server. Dal momento che ogni aggiornamento di una risorsa comporta la rigenerazione dell'Etag, una mancata corrispondenza del valore Etag indica che si è verificata una modifica simultanea. Gli ETag possono essere usati anche quando si crea una nuova risorsa per garantire che questa non esista già.

Per impostazione predefinita, Azure Stack DNS PowerShell Usa gli eTag per bloccare le modifiche simultanee a zone e set di record. L'opzione facoltativa *-Overwrite* può essere usata per disattivare i controlli di ETag e in questo caso le eventuali modifiche simultanee vengono sovrascritte.

A livello dell'API REST di Azure Stack DNS, gli eTag vengono specificati usando le intestazioni HTTP. Il relativo comportamento è illustrato nella tabella seguente:

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

[Introduzione a iDNS per Azure Stack](azure-stack-understanding-dns.md)
