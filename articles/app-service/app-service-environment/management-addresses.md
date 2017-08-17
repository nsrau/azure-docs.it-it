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
ms.date: 07/11/2017
ms.author: ccompy
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 949b445793768f593af205321bf3ab5a7aa150c0
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="app-service-environment-management-addresses"></a>Indirizzi di gestione dell'Ambiente del servizio app

Ambiente del servizio app (ASE) è una distribuzione di Servizio app di Azure in una subnet nella rete virtuale di Azure.  L'ambiente ASE deve essere accessibile da Servizio app di Azure in modo che possa essere gestito.  Il traffico di gestione dell'ambiente ASE attraversa la rete controllata dall'utente.  Proviene dai server di gestione di Servizio app di Azure fino a raggiungere l'indirizzo virtuale pubblico associato all'ambiente ASE.  Per informazioni dettagliate sulle dipendenze di rete dell'ambiente ASE, leggere [Considerazioni sulla rete e ambiente del servizio app][networking].  Per informazioni generali sull'ambiente ASE è possibile iniziare con [Introduzione ad Ambiente del servizio app][intro].

Questo documento elenca gli IP di origine per il traffico di gestione all'ambiente ASE. È possibile usare questi indirizzi per creare gruppi di sicurezza di rete per bloccare il traffico in ingresso o usarlo nelle tabelle di routing in base alle esigenze.  Per usare queste informazioni, sono necessari:

* gli indirizzi IP elencati per tutte le aree
* gli indirizzi IP che corrispondono all'area dell'ambiente ASE in cui viene eseguita la distribuzione.

Il traffico di gestione in ingresso proviene da questi indirizzi IP fino alle porte 454 e 455.

| Region | Indirizzi |
|--------|-----------|
| Tutte le aree | 70.37.57.58, 157.55.208.185, 52.174.22.21,13.94.149.179,13.94.143.126,13.94.141.115, 52.178.195.197, 52.178.190.65, 52.178.184.149, 52.178.177.147, 13.75.127.117, 40.83.125.161, 40.83.121.56, 40.83.120.64, 52.187.56.50, 52.187.63.37, 52.187.59.251, 52.187.63.19, 52.165.158.140, 52.165.152.214, 52.165.154.193, 52.165.153.122, 104.44.129.255, 104.44.134.255, 104.44.129.243, 104.44.129.141 |
| Stati Uniti centro-meridionali e Stati Uniti centro-settentrionali | 23.102.188.65, 191.236.154.88 |
| Australia sudorientale e Australia orientale | 23.101.234.41, 104.210.90.65 |
| Stati Uniti occidentali e Stati Uniti orientali | 104.45.227.37, 191.236.60.72 |
| Europa occidentale ed Europa settentrionale | 191.233.94.45, 191.237.222.191 |
| Stati Uniti centro-occidentali e Stati Uniti occidentali 2 | 13.78.148.75, 13.66.225.188 |
| Stati Uniti centrali e Stati Uniti orientali 2 | 104.43.165.73, 104.46.108.135 |
| Asia orientale e Asia sudorientale | 23.99.115.5, 104.215.158.33 |
| Giappone orientale e Giappone occidentale | 104.41.185.116, 191.239.104.48 |
| Canada centrale e Canada orientale | 40.85.230.101, 40.86.229.100 |
| Regno Unito occidentale e Regno Unito meridionale | 51.141.8.34, 51.140.185.75 |
| Corea meridionale e Corea centrale | 52.231.200.177, 52.231.32.117 |
| Brasile meridionale e Stati Uniti centro-meridionali| 104.41.46.178, 23.102.188.65 |
| India centrale e India meridionale | 104.211.98.24, 104.211.225.66 |
| India occidentale e India meridionale | 104.211.160.229, 104.211.225.66 |


<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md

