---
title: Uso di DNS di Azure per i domini privati | Microsoft Docs
description: Panoramica del servizio di hosting DNS in Microsoft Azure.
services: dns
documentationcenter: na
author: KumudD
manager: jennoc
editor: 
ms.assetid: 
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: 95cf8ab2bd34e698e12452e062687219bad49eb6
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2017
---
# <a name="using-azure-dns-for-private-domains"></a>Uso di DNS di Azure per i domini privati
Domain Name System o DNS è responsabile della conversione (o risoluzione) del nome di un servizio nel relativo indirizzo IP. DNS di Azure è un servizio di hosting per i domini DNS che fornisce la risoluzione dei nomi usando l'infrastruttura di Microsoft Azure.  Oltre ai domini DNS con connessione Internet, DNS di Azure ora supporta anche i domini DNS privati come una funzionalità di anteprima.  
 
DNS di Azure fornisce un servizio DNS protetto e affidabile per gestire e risolvere i nomi di dominio in una rete virtuale senza la necessità di aggiungere una soluzione DNS personalizzata. Le zone DNS private consentono di usare nomi di dominio personalizzati anziché i nomi forniti da Azure attualmente disponibili.  L'uso di nomi di dominio personalizzati consente di personalizzare l'architettura di rete virtuale in base alle esigenze della propria organizzazione. Viene fornita la risoluzione dei nomi per le macchine virtuali all'interno di una rete virtuale e tra reti virtuali. È anche possibile configurare i nomi di zone con una visualizzazione di tipo split-horizon, consentendo a una zona DNS privata e pubblica di condividere lo stesso nome.

![Panoramica del servizio DNS](./media/private-dns-overview/scenario.png)

[!INCLUDE [private-dns-preview-notice](../../includes/private-dns-preview-notice.md)]

## <a name="benefits"></a>Vantaggi

* **Elimina la necessità di soluzioni DNS personalizzate.** Molti clienti in precedenza creavano soluzioni personalizzate DNS per gestire le zone DNS nella rete virtuale.  La gestione delle zone DNS può ora essere eseguita usando l'infrastruttura nativa di Azure eliminando così le attività di creazione e gestione di soluzioni DNS personalizzate.

* **Usare tutti i tipi di record DNS comuni.**  DNS di Azure supporta i record A, AAAA, CNAME, MX, NS, PTR, SOA, SRV, e TXT.

* **Gestione automatica dei record dei nomi host.** Oltre a ospitare i record DNS personalizzati, Azure mantiene automaticamente i record dei nomi host per le macchine virtuali nelle reti virtuali specificate.  Ciò consente di ottimizzare i nomi di dominio usati senza dover creare soluzioni DNS personalizzate o modificare l'applicazione.

* **Risoluzione di nomi host tra reti virtuali.** A differenza dei nomi host forniti da Azure, le zone DNS private possono essere condivise tra le reti virtuali.  Questa funzionalità semplifica gli scenari tra più reti e l'individuazione dei servizi come il peering di rete virtuale.

* **Strumenti comuni ed esperienza utente.** Per ridurre la curva di apprendimento, questa nuova offerta usa gli strumenti DNS di Azure già noti (PowerShell, modelli di Resource Manager, API REST).

* **Supporto DNS split-horizon.** DNS di Azure consente di creare zone con lo stesso nome che si risolvono in risposte diverse dall'interno di una rete virtuale e dalla rete Internet pubblica.  Uno scenario tipico di DNS split-horizon consiste nel fornire una versione dedicata di un servizio da usare all'interno della rete virtuale.


## <a name="pricing"></a>Prezzi

Le zone DNS private sono gratuite durante l'anteprima gestita. Durante la disponibilità generale, questa funzionalità userà un modello di determinazione prezzi basato sull'utilizzo simile a quello dell'offerta DNS di Azure esistente. 


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [creare una zona DNS privata](./private-dns-getstarted-powershell.md) in DNS di Azure.

Per informazioni sui record e le zone DNS visitare la pagina [Panoramica delle zone e dei record DNS](dns-zones-records.md).

Informazioni su alcune altre [funzionalità di rete](../networking/networking-overview.md) chiave di Azure.

