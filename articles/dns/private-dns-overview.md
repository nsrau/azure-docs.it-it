---
title: Che cos'è DNS privato di Azure?
description: Panoramica del servizio di hosting DNS privato in Microsoft Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: overview
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: 152087ab3dc20dfc95cfeaa0353d961917d362d6
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959341"
---
# <a name="what-is-azure-private-dns"></a>Che cos'è DNS privato di Azure?

Domain Name System o DNS è responsabile della conversione (o risoluzione) del nome di un servizio nel relativo indirizzo IP.  DNS di Azure è un servizio di hosting per i domini DNS che fornisce la risoluzione dei nomi usando l'infrastruttura di Microsoft Azure. Oltre ai domini DNS con connessione Internet, DNS di Azure supporta anche le zone DNS private.

DNS privato di Azure fornisce un servizio DNS protetto e affidabile per gestire e risolvere i nomi di dominio in una rete virtuale senza la necessità di aggiungere una soluzione DNS personalizzata. Usando le zone DNS private è possibile usare i nomi di dominio personalizzati anziché i nomi forniti da Azure attualmente disponibili. L'uso di nomi di dominio personalizzati consente di personalizzare l'architettura di rete virtuale in base alle esigenze della propria organizzazione. Offre la risoluzione dei nomi per le macchine virtuali all'interno di una rete virtuale e tra reti virtuali. È anche possibile configurare i nomi di zone con una visualizzazione di tipo split-horizon, che consente a una zona DNS privata e pubblica di condividere il nome.

Per risolvere i record di una zona DNS privata dalla rete virtuale, è necessario collegare la rete virtuale alla zona. Le reti virtuali collegate hanno accesso completo e possono risolvere tutti i record DNS pubblicati nella zona privata. È inoltre possibile abilitare la registrazione automatica su un collegamento di rete virtuale. Se si abilita la registrazione automatica su un collegamento di rete virtuale, i record DNS per le macchine virtuali nella rete virtuale vengono registrati nella zona privata. Se è abilitata la registrazione automatica, DNS di Azure aggiorna anche i record di zona ogni volta che una macchina virtuale viene creata o eliminata o quando cambia il relativo indirizzo IP.

![Panoramica del servizio DNS](./media/private-dns-overview/scenario.png)

> [!NOTE]
> Come procedura consigliata, non usare un dominio *.local* per la zona DNS privata. Non tutti i sistemi operativi supportano questa configurazione.

## <a name="benefits"></a>Vantaggi

DNS privato di Azure offre i vantaggi seguenti:

* **Elimina la necessità di soluzioni DNS personalizzate**. Molti clienti in precedenza creavano soluzioni personalizzate DNS per gestire le zone DNS nella rete virtuale. È ora possibile gestire le zone DNS usando l'infrastruttura nativa di Azure, eliminando così le attività di creazione e gestione di soluzioni DNS personalizzate.

* **Consente di usare tutti i tipi di record DNS comuni**. DNS di Azure supporta i record A, AAAA, CNAME, MX, PTR, SOA, SRV e TXT.

* **Gestione automatica dei record dei nomi host**. Oltre a ospitare i record DNS personalizzati, Azure mantiene automaticamente i record dei nomi host per le macchine virtuali nelle reti virtuali specificate. In questo scenario è possibile ottimizzare i nomi di dominio usati senza dover creare soluzioni DNS personalizzate o modificare le applicazioni.

* **Risoluzione di nomi host tra reti virtuali**. A differenza dei nomi host forniti da Azure, le zone DNS private possono essere condivise tra le reti virtuali. Questa funzionalità semplifica gli scenari tra più reti e l'individuazione dei servizi come il peering di rete virtuale.

* **Strumenti comuni ed esperienza utente**. Per ridurre la curva di apprendimento, questo servizio usa gli strumenti DNS di Azure già noti, ovvero portale di Azure, Azure PowerShell, interfaccia della riga di comando di Azure, modelli di Azure Resource Manager e API REST.

* **Supporto DNS split-horizon**. DNS di Azure consente di creare zone con lo stesso nome che si risolvono in risposte diverse dall'interno di una rete virtuale e dalla rete Internet pubblica. Uno scenario tipico di DNS split-horizon consiste nel fornire una versione dedicata di un servizio da usare all'interno della rete virtuale.

* **Disponibile in tutte le aree di Azure**. La funzionalità zone private di DNS di Azure è disponibile in tutte le aree di Azure nel cloud pubblico di Azure.

## <a name="capabilities"></a>Capabilities

DNS di Azure offre le funzionalità seguenti:

* **Registrazione automatica di macchine virtuali da una rete virtuale collegata a una zona privata con registrazione automatica abilitata**. Le macchine virtuali sono registrate (aggiunte) alla zona privata come record A che puntano ai rispettivi indirizzi IP privati. Quando viene eliminata una macchina virtuale in un collegamento di rete virtuale con registrazione automatica abilitata, DNS di Azure rimuove automaticamente anche il record DNS corrispondente dalla zona privata collegata.

* **Inoltro della risoluzione DNS supportato sulle reti virtuali collegate alla zona privata**. Per la risoluzione DNS fra reti virtuali non è presente alcuna dipendenza esplicita che richiede il peering delle reti virtuali tra loro. Potrebbe tuttavia essere necessario eseguire il peering di reti virtuali per altri scenari, ad esempio il traffico HTTP.

* **Ricerca DNS inversa supportata nell'ambito della rete virtuale**. La ricerca DNS inversa di un indirizzo IP privato all'interno della rete virtuale assegnata a una zona privata restituisce l'FQDN che include il nome dell'host/record, nonché il nome della zona come suffisso.

## <a name="other-considerations"></a>Altre considerazioni

DNS di Azure presenta le limitazioni seguenti:

* Una determinata rete virtuale può essere collegata a una sola zona privata se è abilitata la registrazione automatica dei record DNS delle macchine virtuali. È tuttavia possibile collegare più reti virtuali a una singola zona DNS.
* Il DNS inverso funziona solo per lo spazio IP privato nella rete virtuale collegata
* Il DNS inverso per un indirizzo IP privato di una rete virtuale collegata restituisce *internal.cloudapp.net* come suffisso predefinito per la macchina virtuale. Per le reti virtuali collegate a una zona privata con registrazione automatica abilitata, il DNS inverso per un indirizzo IP privato restituisce due nomi di dominio completi, uno con il suffisso predefinito *internal.cloudapp.net* e l'altro con il suffisso della zona privata.
* L'inoltro condizionale al momento non è supportato in modo nativo. Per abilitare la risoluzione tra reti di Azure e locali, vedere [Risoluzione dei nomi per le risorse in reti virtuali di Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
 
## <a name="pricing"></a>Prezzi

Per altre informazioni sui prezzi, vedere la pagina relativa ai [prezzi del DNS di Azure](https://azure.microsoft.com/pricing/details/dns/).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come creare una zona privata nel DNS di Azure tramite [Azure PowerShell](./private-dns-getstarted-powershell.md) o l'[interfaccia della riga di comando di Azure](./private-dns-getstarted-cli.md).

* Consultare alcuni [scenari comuni sulle zone private](./private-dns-scenarios.md) che possono essere realizzati con le zone private in DNS di Azure.

* Per domande e risposte comuni sulle zone private in DNS di Azure, tra cui i comportamenti specifici previsti per determinati tipi di operazioni, vedere [Domande frequenti su DNS privato](./dns-faq-private.md).

* Per informazioni sui record e le zone DNS visitare la pagina [Panoramica delle zone e dei record DNS](dns-zones-records.md).

* Informazioni su alcune altre [funzionalità di rete](../networking/networking-overview.md) chiave di Azure.
