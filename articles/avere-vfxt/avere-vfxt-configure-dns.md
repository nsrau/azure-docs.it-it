---
title: DNS di Avere vFXT - Azure
description: Configurazione di un server DNS per il bilanciamento del carico round robin con Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 38f15acd16acca2edd558a36ba434a1b0ab045fb
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50670248"
---
# <a name="avere-cluster-dns-configuration"></a>Configurazione DNS del cluster Avere

Questa sezione illustra le nozioni di base per configurare un sistema DNS per il bilanciamento del carico del cluster Avere vFXT. 

Questo documento *non include* le istruzioni per configurare e gestire un server DNS nell'ambiente Azure. 

Anziché usare il DNS round robin per bilanciare il carico di un cluster vFXT in Azure, considerare l'uso di metodi manuali per assegnare gli indirizzi IP in modo uniforme tra i client quando vengono montati. Diversi metodi sono descritti in [Montare il cluster Avere vFXT](avere-vfxt-mount-clients.md). 

Per decidere se usare o meno un server DNS, tenere a mente quanto segue: 

* Se il sistema è accessibile solo a client NFS, l'uso di DNS non è necessario: è possibile specificare tutti gli indirizzi di rete usando indirizzi IP numerici. 

* Se il sistema supporta l'accesso SMB (CIFS), il DNS è necessario, perché è necessario specificare un dominio DNS per il server Active Directory.

* Il DNS è necessario se si vuole usare l'autenticazione Kerberos.

## <a name="load-balancing"></a>Bilanciamento del carico.

Per distribuire il carico complessivo, configurare il dominio DNS per usare la distribuzione del carico round robin per gli indirizzi IP lato client.

## <a name="configuration-details"></a>Dettagli di configurazione

Quando i client accedono al cluster, RRDNS bilancia automaticamente le richieste tra tutte le interfacce disponibili.

Per ottenere prestazioni ottimali, configurare il server DNS per gestire gli indirizzi del cluster lato client come illustrato nel diagramma seguente.

A sinistra è visualizzato un vserver cluster, al centro e a destra compaiono gli indirizzi IP. Configurare ogni punto di accesso client con puntatori e record A come illustrato.

![Diagramma del DNS round robin del cluster Avere](media/avere-vfxt-rrdns-diagram.png) 
<!--- separate text description file provided  [diagram text description](avere-vfxt-rrdns-alt-text.md) -->

Ogni indirizzo IP lato client deve avere un nome univoco riservato all'uso interno da parte del cluster. In questo diagramma, gli indirizzi IP client sono denominati vs1-client-IP-* per maggiore chiarezza, ma nell'ambiente di produzione è consigliabile usare qualcosa di più conciso, ad esempio client*.

I client montano il cluster usando il nome vserver come argomento server. 

Modificare il file ``named.conf`` del server DNS per impostare un ordine ciclico per le query al vserver. Questa opzione assicura che tutti i valori disponibili siano alternati ciclicamente. Aggiungere un'istruzione simile alla seguente:

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

I comandi nsupdate seguenti forniscono un esempio di come configurare il DNS in modo corretto:

```
update add vserver1.example.com. 86400 A 10.0.0.10
update add vserver1.example.com. 86400 A 10.0.0.11
update add vserver1.example.com. 86400 A 10.0.0.12
update add vs1-client-IP-10.example.com. 86400 A 10.0.0.10
update add vs1-client-IP-11.example.com. 86400 A 10.0.0.11
update add vs1-client-IP-12.example.com. 86400 A 10.0.0.12
update add 10.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-10.example.com
update add 11.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-11.example.com
update add 12.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-12.example.com
```

## <a name="cluster-dns-settings"></a>Impostazioni DNS del cluster

Specificare il server DNS usato dal cluster vFXT nella pagina di impostazioni **Cluster** > **Administrative Network** (Rete amministrativa). Le impostazioni in questa pagina includono:

* Indirizzo del server DNS
* Nome di dominio DNS
* Domini di ricerca DNS

Leggere [DNS Settings](<http://library.averesystems.com/ops_guide/4_7/gui_admin_network.html#gui-dns>) (Impostazioni DNS) nella guida alla configurazione del cluster Avere per altri dettagli sull'uso di questa pagina.


