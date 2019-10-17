---
title: Modificare le impostazioni di rete per il cluster Microsoft Azure FXT Edge Filer
description: Come personalizzare le impostazioni di rete dopo aver creato il cluster Microsoft Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: d250e566d884760244ee25e4c43d30fbe5323a7c
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254887"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>Esercitazione: Configurare le impostazioni di rete del cluster 

Prima di usare un nuovo cluster Azure FXT Edge Filer, è consigliabile controllare e personalizzare diverse impostazioni di rete per il flusso di lavoro. 

Questa esercitazione presenta le impostazioni di rete che può essere necessario modificare per un nuovo cluster. 

Si acquisiranno le nozioni seguenti: 

> [!div class="checklist"]
> * Impostazioni di rete che può essere necessario aggiornare dopo la creazione di un cluster
> * Casi di utilizzo di Azure FXT Edge Filer in cui è necessario un server Active Directory o un server DNS 
> * Come configurare il sistema DNS round robin (RRDNS) per bilanciare automaticamente il carico delle richieste client al cluster FXT

La quantità di tempo necessaria per completare questi passaggi dipende dal numero di modifiche di configurazione necessarie nel sistema:

* Se è necessario solo leggere l'esercitazione e controllare alcune impostazioni, sono necessari 10-15 minuti. 
* Se è necessario configurare il sistema DNS round robin, questa attività può richiedere un'ora o più.

## <a name="adjust-network-settings"></a>Modificare le impostazioni di rete

Diverse attività correlate alla rete fanno parte della configurazione di un nuovo cluster Azure FXT Edge Filer. Controllare questo elenco per scegliere quelle valide per il proprio sistema.

Per altre informazioni sulle impostazioni di rete per il cluster, vedere [Configurazione dei servizi di rete](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html) nella guida alla configurazione del cluster.

* Configurare il sistema DNS round robin per la rete esposta ai client (facoltativo)

  È possibile bilanciare il carico del traffico del cluster configurando il sistema DNS come descritto in [Configurare DNS per il cluster FXT Edge Filer](#configure-dns-for-load-balancing).

* Verificare le impostazioni NTP

* Configurare Active Directory e i download di nomi utente/nomi dei gruppi (se necessario)

  Se gli host di rete usano Active Directory o un altro tipo di servizio directory esterno, è necessario modificare la configurazione dei servizi directory del cluster per impostare il modo in cui il cluster scarica le informazioni relative a nomi utente e nomi dei gruppi. Per informazioni dettagliate, vedere la sezione **Cluster** > **Servizi directory** nella guida alla configurazione del cluster.

  Se si vuole aggiungere il supporto SMB, è necessario un server Active Directory. Configurare Active Directory prima di iniziare a configurare SMB.

* Definire le VLAN (facoltativo)
  
  Configurare eventuali VLAN aggiuntive necessarie prima della definizione dei server virtuali e dello spazio dei nomi globale del cluster. Per altre informazioni, vedere [Uso di VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) nella guida alla configurazione del cluster.

* Configurare i server proxy (se necessario)

  Se il cluster usa un server proxy per raggiungere indirizzi esterni, completare questi passaggi per configurarlo:

  1. Definire il server proxy nella pagina delle impostazioni **Proxy Configuration** (Configurazione proxy)
  1. Applicare la configurazione del server proxy tramite la pagina **Cluster** > **General Setup** (Configurazione generale) nella pagina **Core Filer Details** (Dettagli archiviatore di base).
  
  Per altre informazioni, vedere [Uso di proxy Web](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html) nella guida alla configurazione del cluster.

* Caricare [certificati di crittografia](#encryption-certificates) per il cluster da usare (facoltativo)

### <a name="encryption-certificates"></a>Certificati di crittografia

Il cluster FXT Edge Filter usa certificati X.509 per queste funzioni:

* Crittografare il traffico di amministrazione del cluster

* Eseguire l'autenticazione per conto di un client in server KMIP di terze parti

* Verificare i certificati server dei provider di servizi cloud

Se è necessario caricare certificati nel cluster, usare la pagina delle impostazioni **Cluster** > **Certificates** (Certificati). Per informazioni dettagliate, vedere [Cluster > Certificati](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html) della guida alla configurazione del cluster.

Per crittografare le comunicazioni relative alla gestione del cluster, usare la pagina delle impostazioni **Cluster** > **General Setup** (Configurazione generale) per selezionare il certificato da usare per il protocollo SSL di amministrazione.

> [!Note] 
> Le chiavi di accesso dei servizi cloud vengono archiviate usando la pagina di configurazione **Cloud Credentials** (Credenziali cloud). La sezione [Aggiungere un archiviatore di base](fxt-add-storage.md#add-a-core-filer) precedente mostra un esempio. Per informazioni dettagliate, vedere la sezione [Credenziali cloud](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html) della guida alla configurazione del cluster. 

## <a name="configure-dns-for-load-balancing"></a>Configurare DNS per il bilanciamento del carico

Questa sezione descrive le operazioni di base per configurare un sistema DNS round robin (RRDNS) per la distribuzione del carico dei client tra tutti gli indirizzi IP esposti ai client nel cluster FXT Edge Filer. 

### <a name="decide-whether-or-not-to-use-dns"></a>Scegliere se usare o meno DNS

Il bilanciamento del carico è sempre consigliabile, ma non sempre è necessario usare DNS. Ad esempio, con alcuni tipi di flussi di lavoro client può essere più logico usare uno script per assegnare gli indirizzi IP del cluster in modo uniforme tra i client quando si monta il cluster. Alcuni metodi vengono descritti in [Montare il cluster](fxt-mount-clients.md). 

Per decidere se usare o meno un server DNS, tenere a mente quanto segue: 

* Se il sistema è accessibile solo da parte di client NFS, DNS non è necessario. È possibile specificare tutti gli indirizzi di rete usando indirizzi IP numerici. 

* Se il sistema supporta l'accesso SMB (CIFS), il DNS è necessario, perché è necessario specificare un dominio DNS per il server Active Directory.

* Il DNS è necessario se si vuole usare l'autenticazione Kerberos.

### <a name="round-robin-dns-configuration-details"></a>Dettagli di configurazione del sistema DNS round robin

Quando i client accedono al cluster, RRDNS bilancia automaticamente le richieste tra tutte le interfacce disponibili.

Per ottenere prestazioni ottimali, configurare il server DNS per gestire gli indirizzi del cluster lato client come illustrato nel diagramma seguente.

A sinistra è visualizzato un vserver cluster, al centro e a destra compaiono gli indirizzi IP. Configurare ogni punto di accesso client con puntatori e record A come illustrato.

![Diagramma del sistema DNS round robin del cluster - Collegamento al testo alternativo dettagliato dopo l'immagine](media/fxt-cluster-config/fxt-rrdns-diagram.png) 
[Descrizione dettagliata](https://azure.github.io/Avere/legacy/Azure-FXT-EdgeFilerDNSconfiguration-alt-text.html)

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

I comandi ``nsupdate`` seguenti forniscono un esempio di una corretta configurazione di DNS:

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

### <a name="enable-dns-in-the-cluster"></a>Abilitare DNS nel cluster 

Specificare il server DNS usato dal cluster nella pagina delle impostazioni **Cluster** > **Administrative Network** (Rete amministrativa). Le impostazioni in questa pagina includono:

* Indirizzo del server DNS
* Nome di dominio DNS
* Domini di ricerca DNS

Per altre informazioni, vedere [Impostazioni DNS](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) nella guida alla configurazione del cluster.

## <a name="next-steps"></a>Passaggi successivi

Questo è l'ultimo passaggio di configurazione di base per il cluster Azure FXT Edge Filer. 

* Per informazioni sui LED di sistema e su altri indicatori, vedere [Monitorare lo stato dell'hardware](fxt-monitor.md).
* Per altre informazioni sul montaggio del cluster FXT Edge Filer nei client, vedere [Montare il cluster](fxt-mount-clients.md). 
* Per altre informazioni sull'uso e sulla gestione di un cluster FXT Edge Filer, vedere la [guida alla configurazione del cluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html). 