---
title: Uso di DNS di Azure per i domini privati | Microsoft Docs
description: Panoramica del servizio di hosting DNS in Microsoft Azure.
services: dns
documentationcenter: na
author: KumudD
manager: jennoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: 7f1bd8cdcab7bdd61b3f006acf6090c53db8eda6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="using-azure-dns-for-private-domains"></a>Uso di DNS di Azure per i domini privati
Domain Name System o DNS è responsabile della conversione (o risoluzione) del nome di un servizio nel relativo indirizzo IP. DNS di Azure è un servizio di hosting per i domini DNS che fornisce la risoluzione dei nomi usando l'infrastruttura di Microsoft Azure.  Oltre ai domini DNS con connessione Internet, DNS di Azure ora supporta anche i domini DNS privati come una funzionalità di anteprima.  
 
DNS di Azure fornisce un servizio DNS protetto e affidabile per gestire e risolvere i nomi di dominio in una rete virtuale senza la necessità di aggiungere una soluzione DNS personalizzata. Le zone DNS private consentono di usare nomi di dominio personalizzati anziché i nomi forniti da Azure attualmente disponibili.  L'uso di nomi di dominio personalizzati consente di personalizzare l'architettura di rete virtuale in base alle esigenze della propria organizzazione. Viene fornita la risoluzione dei nomi per le macchine virtuali all'interno di una rete virtuale e tra reti virtuali. È anche possibile configurare i nomi di zone con una visualizzazione di tipo split-horizon, consentendo a una zona DNS privata e pubblica di condividere lo stesso nome.

![Panoramica del servizio DNS](./media/private-dns-overview/scenario.png)

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="benefits"></a>Vantaggi

* **Elimina la necessità di soluzioni DNS personalizzate.** Molti clienti in precedenza creavano soluzioni personalizzate DNS per gestire le zone DNS nella rete virtuale.  La gestione delle zone DNS può ora essere eseguita usando l'infrastruttura nativa di Azure eliminando così le attività di creazione e gestione di soluzioni DNS personalizzate.

* **Usare tutti i tipi di record DNS comuni.**  DNS di Azure supporta i record A, AAAA, CNAME, MX, NS, PTR, SOA, SRV, e TXT.

* **Gestione automatica dei record dei nomi host.** Oltre a ospitare i record DNS personalizzati, Azure mantiene automaticamente i record dei nomi host per le macchine virtuali nelle reti virtuali specificate.  Ciò consente di ottimizzare i nomi di dominio usati senza dover creare soluzioni DNS personalizzate o modificare l'applicazione.

* **Risoluzione di nomi host tra reti virtuali.** A differenza dei nomi host forniti da Azure, le zone DNS private possono essere condivise tra le reti virtuali.  Questa funzionalità semplifica gli scenari tra più reti e l'individuazione dei servizi come il peering di rete virtuale.

* **Strumenti comuni ed esperienza utente.** Per ridurre la curva di apprendimento, questa nuova offerta usa gli strumenti DNS di Azure già noti (PowerShell, modelli di Resource Manager, API REST).

* **Supporto DNS split-horizon.** DNS di Azure consente di creare zone con lo stesso nome che si risolvono in risposte diverse dall'interno di una rete virtuale e dalla rete Internet pubblica.  Uno scenario tipico di DNS split-horizon consiste nel fornire una versione dedicata di un servizio da usare all'interno della rete virtuale.

* **Disponibili in tutte le aree di Azure.** Zone private di DNS di Azure è disponibile in tutte le aree di Azure nel cloud pubblico di Azure. 


## <a name="capabilities"></a>Capabilities 
* Registrazione automatica di macchine virtuali da una singola rete virtuale collegata a una zona privata come rete virtuale di registrazione. Le macchine virtuali saranno registrate (aggiunte) alla zona privata come record A che puntano ai rispettivi indirizzi IP privati. Quando una macchina virtuale in una rete virtuale di registrazione viene eliminata, Azure rimuove automaticamente anche il record DNS corrispondente dalla zona privata collegata. Si noti che le reti virtuali registrazione agiscono per impostazione predefinita anche come reti virtuali di risoluzione, in quanto la risoluzione DNS per la zona funzionerà da una o più macchine virtuali all'interno della rete virtuale di registrazione. 
* Inoltrare la risoluzione DNS supportata su più reti virtuali collegate alla zona privata come reti virtuali di risoluzione. Per la risoluzione DNS della rete virtuale incrociata non è presente alcuna dipendenza esplicita per cui sia possibile eseguire il peer delle reti virtuali tra loro. Tuttavia, i clienti potrebbero voler eseguire il peer di reti virtuali per altri scenari (ad es., il traffico HTTP).
* Ricerca DNS inversa supportata all'interno dell'ambito della rete virtuale. La ricerca DNS inversa per un indirizzo IP privato all'interno della rete virtuale assegnata a una zona privata restituirà lo FQDN che include il nome dell'host/record, nonché il nome della zona come suffisso. 


## <a name="limitations"></a>Limitazioni
* È consentita una sola rete virtuale di registrazione per zona privata
* È consentito un massimo di 10 reti virtuali di risoluzione per zona privata
* Una determinata rete virtuale può essere collegata a una sola zona privata come rete virtuale di registrazione
* Una determinata rete virtuale può essere collegata a un massimo di 10 zone private come rete virtuale di risoluzione
* Se viene specificata una rete virtuale di registrazione, i record DNS per le macchine virtuali di tale rete che sono registrati nella zona privata non sono visualizzabili né recuperabili tramite PowerShell/CLI/API, ma sono effettivamente registrati e vengono risolti correttamente
* Il DNS inverso funziona solo per lo spazio IP privato nella rete virtuale di registrazione
* Il DNS inverso per un indirizzo IP privato che non è registrato nella zona privata, ad esempio un indirizzo IP privato per una macchina virtuale in una rete virtuale collegata a una zona privata come rete virtuale di risoluzione, restituisce "internal.cloudapp.net" come suffisso DNS, ma questo suffisso non è risolvibile.   
* La rete virtuale deve essere vuota (ovvero, senza alcun record della macchina virtuale) quando viene collegata inizialmente (per la prima volta) a una zona privata come rete virtuale di registrazione o di risoluzione. Questo requisito tuttavia non si applica quando la rete virtuale viene successivamente collegata come rete virtuale di registrazione o di risoluzione ad altre zone private. 
* Attualmente, l'inoltro condizionale non è supportato, ad esempio per abilitare la risoluzione tra reti di Azure e locali. Per informazioni su come i clienti possono sfruttare questo scenario tramite altri meccanismi, vedere [Risoluzione dei nomi per le macchine virtuali e le istanze del ruolo](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

È consigliabile consultare anche le [domande frequenti](./dns-faq.md#private-dns) per alcune domande e risposte comuni sulle zone private in DNS di Azure, inclusi comportamenti di registrazione e risoluzione DNS specifici che è possibile prevedere per determinati tipi di operazioni. 


## <a name="pricing"></a>Prezzi

Le zone DNS private sono gratuite durante l'anteprima pubblica. Durante la disponibilità generale, questa funzionalità userà un modello di determinazione prezzi basato sull'utilizzo simile a quello dell'offerta DNS di Azure esistente. 


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come creare una zona privata nel DNS di Azure tramite [PowerShell](./private-dns-getstarted-powershell.md) o [CLI](./private-dns-getstarted-cli.md).

Per informazioni su alcuni scenari comuni che possono essere realizzati con le zone private in DNS di Azure, vedere [Private Zone scenarios](./private-dns-scenarios.md) (Scenari relativi alle zone private).

Consultare le [domande frequenti](./dns-faq.md#private-dns) per alcune domande e risposte comuni sulle zone private in DNS di Azure, inclusi comportamenti specifici che è possibile prevedere per determinati tipi di operazioni. 

Per informazioni sui record e le zone DNS visitare la pagina [Panoramica delle zone e dei record DNS](dns-zones-records.md).

Informazioni su alcune altre [funzionalità di rete](../networking/networking-overview.md) chiave di Azure.

