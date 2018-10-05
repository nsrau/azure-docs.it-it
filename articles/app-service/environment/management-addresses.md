---
title: Indirizzi di gestione dell'Ambiente del servizio app di Azure
description: Elenca gli indirizzi di gestione usati per inviare comandi a un Ambiente del servizio app
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: ccompy
ms.openlocfilehash: 590679daff20f9c469fb8fcfcc0fbbad77f91b5b
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162855"
---
# <a name="app-service-environment-management-addresses"></a>Indirizzi di gestione dell'Ambiente del servizio app

L'ambiente del servizio app (ASE) è una distribuzione di Servizio app di Azure in una subnet nella rete virtuale di Azure (VNet).  L'ambiente ASE deve essere accessibile dal piano di gestione usato dal Servizio app di Azure.  Il traffico di gestione dell'ambiente ASE attraversa la rete controllata dall'utente. Se questo traffico è bloccato o indirizzato in modo errato l'ambiente ASE verrà sospeso. Per informazioni dettagliate sulle dipendenze di rete dell'ambiente del servizio app, leggere [Considerazioni sulla rete e ambiente del servizio app][networking]. Per informazioni generali sull'ambiente ASE, è possibile iniziare con [Introduzione agli ambienti del servizio app][intro].

Questo documento elenca gli indirizzi di origine del servizio app per il traffico di gestione all'ambiente ASE e ha un duplice obiettivo.  

1. È possibile usare questi indirizzi per creare gruppi di sicurezza di rete per bloccare il traffico in ingresso.  
2. È possibile creare route con questi indirizzi per supportare le distribuzioni con tunneling forzato. Per informazioni dettagliate su come configurare l'ambiente ASE per operare in un ambiente in cui il traffico in uscita viene inviato in locale, leggere [Configurare l'ambiente del servizio app con il tunneling forzato][forcedtunnel].

Tutti gli ambienti del servizio app hanno un indirizzo VIP pubblico a cui arriva il traffico di gestione. Il traffico di gestione in ingresso da questi indirizzi proviene dalle porte 454 e 455 sull'indirizzo VIP pubblico dell'ambiente ASE.  

## <a name="list-of-management-addresses"></a>Elenco di indirizzi di gestione ##

| Region | Indirizzi |
|--------|-----------|
| Tutte le aree pubbliche | 70.37.57.58, 157.55.208.185, 52.174.22.21, 13.94.149.179, 13.94.143.126, 13.94.141.115, 52.178.195.197, 52.178.190.65, 52.178.184.149, 52.178.177.147, 13.75.127.117, 40.83.125.161, 40.83.121.56, 40.83.120.64, 52.187.56.50, 52.187.63.37, 52.187.59.251, 52.187.63.19, 52.165.158.140, 52.165.152.214, 52.165.154.193, 52.165.153.122, 104.44.129.255, 104.44.134.255, 104.44.129.243, 104.44.129.141, 23.102.188.65, 191.236.154.88, 13.64.115.203, 65.52.193.203, 70.37.89.222, 52.224.105.172, 23.102.135.246 |
| Microsoft Azure per enti pubblici (Fairfax o MAG) | 23.97.29.209, 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="get-your-management-addresses-from-api"></a>Ottenere gli indirizzi di gestione dall'API ##

È possibile elencare gli indirizzi di gestione che corrispondono all'ambiente del servizio app con la chiamata API seguente.

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

L'API restituisce un documento JSON che include tutti gli indirizzi in ingresso per l'ambiente del servizio app. L'elenco di indirizzi include gli indirizzi di gestione, l'indirizzo VIP usato dall'ambiente del servizio app e l'intervallo di indirizzi subnet dell'ambiente stesso.  

Per chiamare l'API con [armclient](http://github.com/projectkudu/ARMClient), usare i comandi seguenti sostituendo i segnaposto con l'ID sottoscrizione, il gruppo di risorse e il nome dell'ambiente del servizio app.  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
