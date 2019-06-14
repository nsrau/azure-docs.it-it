---
title: Che cos'è DNS Private di Azure?
description: Panoramica del servizio di hosting DNS privato in Microsoft Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: overview
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: a8548b4972d5853f09630ae3e9ded05ed6fee32b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076408"
---
# <a name="what-is-azure-private-dns"></a>Che cos'è DNS Private di Azure?

> [!IMPORTANT]
> DNS di Azure privato è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Domain Name System o DNS è responsabile della conversione (o risoluzione) del nome di un servizio nel relativo indirizzo IP.  DNS di Azure è un servizio di hosting per i domini DNS che fornisce la risoluzione dei nomi usando l'infrastruttura di Microsoft Azure. Oltre al supporto per domini DNS con connessione internet, DNS di Azure supporta anche le zone DNS private.

Azure DNS Private fornisce un servizio DNS protetto e affidabile per gestire e risolvere i nomi di dominio in una rete virtuale senza la necessità di aggiungere una soluzione DNS personalizzata. Usando le zone DNS private è possibile usare i nomi di dominio personalizzati anziché i nomi forniti da Azure attualmente disponibili. L'uso di nomi di dominio personalizzati consente di personalizzare l'architettura di rete virtuale in base alle esigenze della propria organizzazione. Offre la risoluzione dei nomi per le macchine virtuali all'interno di una rete virtuale e tra reti virtuali. È anche possibile configurare i nomi di zone con una visualizzazione di tipo split-horizon, che consente a una zona DNS privata e pubblica di condividere il nome.

Per risolvere i record di una zona DNS privata dalla rete virtuale, è necessario collegare la rete virtuale con la zona. Le reti virtuali collegate hanno accesso completo e possono risolvere tutti i record DNS pubblicati nella zona privata. Inoltre, è anche possibile abilitare registrazione automatica su un collegamento di rete virtuale. Se si abilita la registrazione automatica su un collegamento di rete virtuale, i record DNS per le macchine virtuali nella rete virtuale vengono registrati nella zona privata. Se registrazione automatica è abilitata, DNS di Azure aggiorna anche i record di zona ogni volta che viene creata una macchina virtuale, le modifiche relative ' indirizzo IP, o viene eliminato.

![Panoramica del servizio DNS](./media/private-dns-overview/scenario.png)

> [!NOTE]
> Come procedura consigliata, non usare un *Local* dominio per la zona DNS privata. Non tutti i sistemi operativi supportano questo.

## <a name="benefits"></a>Vantaggi

DNS di Azure privato offre i vantaggi seguenti:

* **Elimina la necessità di soluzioni DNS personalizzate**. Molti clienti in precedenza creavano soluzioni personalizzate DNS per gestire le zone DNS nella rete virtuale. È ora possibile gestire le zone DNS usando l'infrastruttura di Azure native, che pone problemi di creazione e gestione di soluzioni DNS personalizzate.

* **Consente di usare tutti i tipi di record DNS comuni**. DNS di Azure supporta i record A, AAAA, CNAME, MX, PTR, SOA, SRV e TXT.

* **Gestione automatica dei record dei nomi host**. Oltre a ospitare i record DNS personalizzati, Azure mantiene automaticamente i record dei nomi host per le macchine virtuali nelle reti virtuali specificate. In questo scenario è possibile ottimizzare i nomi di dominio usati senza dover creare soluzioni DNS personalizzate o modificare le applicazioni.

* **Risoluzione di nomi host tra reti virtuali**. A differenza dei nomi host forniti da Azure, le zone DNS private possono essere condivise tra le reti virtuali. Questa funzionalità semplifica gli scenari tra più reti e l'individuazione dei servizi come il peering di rete virtuale.

* **Strumenti comuni ed esperienza utente**. Per ridurre la curva di apprendimento, questo servizio Usa ampiamente diffusa strumenti DNS di Azure (portale di Azure, Azure PowerShell, CLI di Azure, modelli di Azure Resource Manager e l'API REST).

* **Supporto DNS split-horizon**. DNS di Azure consente di creare zone con lo stesso nome che si risolvono in risposte diverse dall'interno di una rete virtuale e dalla rete Internet pubblica. Uno scenario tipico di DNS split-horizon consiste nel fornire una versione dedicata di un servizio da usare all'interno della rete virtuale.

* **Disponibile in tutte le aree di Azure**. La funzionalità zone private di DNS di Azure è disponibile in tutte le aree di Azure nel cloud pubblico di Azure.

## <a name="capabilities"></a>Capabilities

DNS di Azure offre le funzionalità seguenti:

* **La registrazione automatica di macchine virtuali da una rete virtuale collegata a una zona privata con registrazione automatica abilitata**. Le macchine virtuali sono registrate (aggiunte) alla zona privata come record a che puntano ai relativi indirizzi IP privati. Quando viene eliminata una macchina virtuale in un collegamento di rete virtuale di registrazione automatica abilitata, DNS di Azure rimuove automaticamente anche il record DNS corrispondente dalla zona privata collegata.

* **È supportata la risoluzione DNS diretta tra reti virtuali collegate alla zona privata**. Per la risoluzione DNS della rete virtuale incrociata, non è disponibile alcuna dipendenza esplicita in modo che le reti virtuali con peering tra loro. Tuttavia, si potrebbe voler eseguire il peering reti virtuali per altri scenari (ad esempio, il traffico HTTP).

* **Ricerca DNS inversa supportata nell'ambito della rete virtuale**. La ricerca DNS inversa di un indirizzo IP privato all'interno della rete virtuale assegnata a una zona privata restituisce l'FQDN che include il nome dell'host/record, nonché il nome della zona come suffisso.

## <a name="other-considerations"></a>Altre considerazioni

DNS di Azure presenta le limitazioni seguenti:

* È consentita una sola rete virtuale di registrazione per zona privata.
* Sono consentite fino a 10 reti virtuali di risoluzione per zona privata. Il limite verrà rimosso quando la funzionalità sarà disponibile a livello generale.
* Una determinata rete virtuale può essere collegata a una sola zona privata come rete virtuale di registrazione.
* Una determinata rete virtuale può essere collegata a un massimo di 10 zone private come rete virtuale di risoluzione. Il limite verrà rimosso quando la funzionalità sarà disponibile a livello generale.
* Se si specifica una rete virtuale di registrazione, i record DNS per le macchine virtuali di tale rete che sono registrati nella zona privata non sono visualizzabili né recuperabili tramite Azure PowerShell o le API dell'interfaccia della riga di comando di Azure, ma sono effettivamente registrati e vengono risolti correttamente.
* Il DNS inverso funziona solo per lo spazio IP privato nella rete virtuale di registrazione.
* Il DNS inverso per un indirizzo IP privato che non è registrato nella zona privata, ad esempio un indirizzo IP privato per una macchina virtuale in una rete virtuale collegata a una zona privata come rete virtuale di risoluzione a una zona privata, restituisce *internal.cloudapp.net* come suffisso DNS. Tuttavia questo suffisso non è risolvibile.
* Quando viene collegata per la prima volta a una zona privata come rete virtuale di registrazione o di risoluzione, la rete virtuale deve essere completamente vuota. Questo requisito tuttavia non si applica quando la rete virtuale viene successivamente collegata come rete virtuale di registrazione o di risoluzione ad altre zone private.
* Attualmente l'inoltro condizionale non è supportato, ad esempio per abilitare la risoluzione tra reti di Azure e locali. Per informazioni su come i clienti possono creare questo scenario tramite altri meccanismi, vedere [Risoluzione dei nomi per le macchine virtuali e le istanze del ruolo](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
* Funzionamento del DNS inverso solo per spazio IP privato della rete virtuale collegata
* Il DNS inverso per un indirizzo IP privato per una rete virtuale collegato restituisce "internal.cloudapp.net" come suffisso predefinito per la macchina virtuale. Per le reti virtuali collegate a una zona privata con registrazione automatica abilitata, il DNS inverso per un indirizzo IP privato restituisce 2 nomi di dominio completi, una con impostazione predefinita il suffisso *internal.cloudapp.net* e l'altra con il suffisso zona privata.
* Inoltro condizionale non è supportata. Ad esempio, per abilitare la risoluzione tra le reti locali e Azure. Informazioni su come è possibile abilitare questo scenario tramite altri meccanismi. vedere [Risoluzione dei nomi per le risorse in reti virtuali di Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
 


## <a name="pricing"></a>Prezzi

Per informazioni sui prezzi, vedere [prezzi di DNS di Azure](https://azure.microsoft.com/pricing/details/dns/).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come creare una zona privata nel DNS di Azure tramite [Azure PowerShell](./private-dns-getstarted-powershell.md) o l'[interfaccia della riga di comando di Azure](./private-dns-getstarted-cli.md).

* Consultare alcuni [scenari comuni sulle zone private](./private-dns-scenarios.md) che possono essere realizzati con le zone private in DNS di Azure.

* Per domande e risposte sulle zone private in DNS di Azure comuni, inclusi comportamenti specifici è possibile prevedere per determinati tipi di operazioni, vedere [domande frequenti su DNS privato](./dns-faq-private.md).

* Per informazioni sui record e le zone DNS visitare la pagina [Panoramica delle zone e dei record DNS](dns-zones-records.md).

* Informazioni su alcune altre [funzionalità di rete](../networking/networking-overview.md) chiave di Azure.
