---
title: Monitorare l'utilizzo e le statistiche in un servizio Ricerca di Azure | Microsoft Docs
description: Tenere traccia delle dimensioni di indice e consumo delle risorse per la Ricerca di Azure, un servizio di ricerca ospitato sul cloud in Microsoft Azure.
services: search
documentationcenter: ''
author: HeidiSteen
manager: jhubbard
editor: ''
tags: azure-portal

ms.service: search
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 05/17/2016
ms.author: heidist

---
# Monitorare l’utilizzo e le statistiche in un servizio Ricerca di Azure
Il controllo dell'aumento degli indici e delle dimensioni del documento consente di regolare in modo proattivo la capacità prima di raggiungere il limite massimo stabilito per il servizio.

Per monitorare l'utilizzo delle risorse, è possibile visualizzare facilmente conteggi e statistiche relativi al servizio nel [portale di Azure](https://portal.azure.com), ma è possibile anche ottenere le informazioni a livello di codice se si sta creando uno strumento di amministrazione del servizio personalizzato. In questo articolo vengono illustrati i passaggi per entrambe le tecniche.

È anche possibile usare la nuova funzionalità di analisi del traffico di ricerca per informazioni dettagliate sulle attività a livello di indice. Per iniziare, visitare [Analisi del traffico di ricerca per Ricerca di Azure](search-traffic-analytics.md).

## Visualizzare conteggi e metriche nel portale
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Aprire il dashboard relativo al servizio Ricerca di Azure. Nella Home page sono presenti riquadri per il servizio oppure è possibile accedere al servizio tramite Sfoglia nell'indice. Vedere [Creare un servizio](search-create-service-portal.md) per istruzioni dettagliate.

La sezione Utilizzo include un indicatore che indica la quantità di risorse disponibili attualmente in uso.

  ![][1]

È importante ricordare che il servizio condiviso presenta massimo una replica e partizione. Inoltre, può supportare solo 10.000 documenti totali o 50 MB di dati, a seconda del valore che viene raggiunto per primo.

## Ottenere le statistiche di indice utilizzando l'API REST
L'API REST di Ricerca di Azure e .NET SDK forniscono l'accesso a livello di codice alle metriche di servizio. Se si utilizzano [indicizzatori](https://msdn.microsoft.com/library/azure/dn946891.aspx) per caricare un indice dal database SQL di Azure o da DocumentDB, è disponibile un'API aggiuntiva per ottenere i numeri necessari.

* [Ottenere le statistiche di indice](https://msdn.microsoft.com/library/azure/dn798942.aspx)
* [Conteggio documenti](https://msdn.microsoft.com/library/azure/dn798924.aspx)
* [Ottenere lo stato dell'indicizzatore](https://msdn.microsoft.com/library/azure/dn946884.aspx)

## Passaggi successivi
Rivedere [Limiti e capacità](search-limits-quotas-capacity.md) per determinare la combinazione di partizioni e repliche necessaria se la capacità esistente non è sufficiente.

Visitare [Gestire il servizio di ricerca in Microsoft Azure](search-manage.md) per ulteriori informazioni sull'amministrazione del servizio.

<!--Image references-->
[1]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG






<!---HONumber=AcomDC_0914_2016-->