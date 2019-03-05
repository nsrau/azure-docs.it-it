---
title: Snapshot temporizzato di Configurazione app di Azure | Microsoft Docs
description: Panoramica del funzionamento dello snapshot temporizzato in Configurazione app di Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 4f26f3a31dc6303e991c39fc7f85d9bf254d57bc
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885137"
---
# <a name="point-in-time-snapshot"></a>Snapshot temporizzato

Configurazione app di Azure conserva i record dei momenti precisi in cui una nuova coppia chiave-valore viene creata e successivamente modificata. Questi record formano una sequenza temporale completa relativa alle modifiche delle coppie chiave-valore. Un archivio di configurazione di app consente di ricostruire la cronologia di qualsiasi coppia chiave-valore e di risalire a un valore precedente in un momento qualsiasi, fino a quello attuale. Questa funzionalità consente "viaggiare indietro nel tempo" e di recuperare una coppia chiave-valore precedente. È ad esempio possibile ottenere le impostazioni di configurazione del giorno precedente, subito prima della distribuzione più recente, in modo da poter recuperare una configurazione precedente nel caso in cui sia necessario eseguire il rollback dell'applicazione.

## <a name="key-value-retrieval"></a>Recupero di coppie chiave-valore

Per recuperare coppie chiave-valore precedenti, è necessario specificare il momento di acquisizione dello snapshot delle coppie chiave-valore nell'intestazione HTTP di una chiamata all'API REST. Ad esempio: 

        GET /revisions HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

Attualmente, il servizio Configurazione app conserva sette giorni di cronologia delle modifiche.

## <a name="next-steps"></a>Passaggi successivi

* [Guida introduttiva: Creare un'app Web ASP.NET](quickstart-aspnet-core-app.md)  
