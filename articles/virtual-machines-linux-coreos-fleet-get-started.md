<properties 
	pageTitle="Guida introduttiva a fleet su CoreOS in Azure" 
	description="Vengono forniti esempi di base dell'utilizzo di Fleet e Docker in una macchina virtuale Linux CoreOS in Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="dlepow" 
	manager="timlt"
	editor="madhana"/>

<tags 
	ms.service="virtual-machines" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.tgt_pltfrm="vm-linux" 
	ms.workload="infrastructure-services" 
	ms.date="03/17/2015" 
	ms.author="danlep"/>


<!--The next line, with one pound sign at the beginning, is the page title-->
# Guida introduttiva a fleet su CoreOS in Azure

In questo articolo vengono forniti due esempi rapidi di utilizzo di [fleet](https://github.com/coreos/fleet) e [Docker](https://www.docker.com/) per eseguire le applicazioni in un cluster di macchine virtuali [CoreOS].

Per utilizzare questi esempi, configurare innanzitutto un cluster CoreOS a tre nodi come descritto in [Come utilizzare CoreOS in Azure]. Una volta eseguita questa operazione, si comprenderanno gli elementi di base delle distribuzioni di CoreOS e si disporrà di un in funzione e di un computer client. In questi esempi verrà utilizzato esattamente lo stesso nome cluster. Inoltre, in questi esempi si presume che si utilizzi l’host Linux locale per eseguire i comandi **fleet**.




## <a id='simple'>Example 1: Hello World con docker</a>

Ecco una semplice applicazione "Hello World"che si esegue in un singolo contenitore docker. Viene utilizzata l’[immagine Docker Hub busybox].

Nel computer client Linux utilizzare l’editor di testo preferito per creare il seguente file unità **systemd** e denominarlo `helloworld.service` \(per informazioni dettagliate sulla sintassi, vedere [File unità]\).

```
[Unit]
Description=HelloWorld
After=docker.service
Requires=docker.service

[Service]

TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill busybox1
ExecStartPre=-/usr/bin/docker rm busybox1
ExecStartPre=/usr/bin/docker pull busybox
ExecStart=/usr/bin/docker run --name busybox1 busybox /bin/sh -c "while true; do echo Hello World; sleep 1; done"
ExecStop=/usr/bin/docker stop busybox1

```

Connettersi ora al cluster CoreOS e avviare l'unità eseguendo il comando **fleetctl** seguente. L'output mostra che l'unità viene avviata e l’ubicazione.


```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 start helloworld.service


Unit helloworld.service launched on 62f0f66e.../100.79.86.62
```

>[AZURE.NOTE]Per eseguire i comandi **fleetctl** remoti senza il parametro **--tunnel**, a scelta impostare la variabile di ambiente FLEETCTL\_TUNNEL per effettuare il tunneling delle richieste. Ad esempio: `export FLEETCTL_TUNNEL=coreos-cluster.cloudapp.net:22`.


È possibile connettersi al contenitore per visualizzare l'output del servizio:

```
fleetctl --tunnel coreos-cloudapp.cluster.net:22 journal helloworld.service

Mar 04 21:29:26 node-1 docker[57876]: Hello World
Mar 04 21:29:27 node-1 docker[57876]: Hello World
Mar 04 21:29:28 node-1 docker[57876]: Hello World
Mar 04 21:29:29 node-1 docker[57876]: Hello World
Mar 04 21:29:30 node-1 docker[57876]: Hello World
Mar 04 21:29:31 node-1 docker[57876]: Hello World
Mar 04 21:29:32 node-1 docker[57876]: Hello World
Mar 04 21:29:33 node-1 docker[57876]: Hello World
Mar 04 21:29:34 node-1 docker[57876]: Hello World
Mar 04 21:29:35 node-1 docker[57876]: Hello World
```

Per pulire, interrompere e scaricare l'unità.

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 stop helloworld.service
fleetctl --tunnel coreos-cluster.cloudapp.net:22 unload helloworld.service
```


## <a id='highavail'>Esempio 2: server Apache a disponibilità elevata</a>

Un vantaggio dell'utilizzo di CoreOS, Docker, e **fleet** consiste nella possibilità di eseguire i servizi a disponibilità elevata in modo semplice. In questo esempio verrà distribuito un servizio costituito da tre contenitori identici che eseguono il server web Apache. I contenitori saranno eseguiti sulle tre macchine virtuali del cluster. Questo esempio è simile a uno esempio in [Avvio dei contenitori con flotta] e utilizza l’[immagine Docker Hub Apache CoreOS].

>[AZURE.NOTE]Per eseguire il server Apache a disponibilità elevata, è necessario configurare un endpoint HTTP con carico bilanciato nelle macchine virtuali \(porta pubblica 80, porta privata 80\). È possibile eseguire questa operazione dopo la creazione del cluster CoreOS, tramite il portale di gestione di Azure o il comando **azure vm endpoint**. Per ulteriori informazioni, vedere [Configurare un set con carico bilanciato].

Nel computer client, utilizzare l’editor di testo preferito per creare un file unità del modello **systemd**, denominato apache@.service. Tale modello verrà utilizzato per avviare tre istanze distinte, denominate apache@1.service, apache@2.service e apache@3.service:

```
[Unit]
Description=High Availability Apache
After=docker.service
Requires=docker.service

[Service]
TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill apache1
ExecStartPre=-/usr/bin/docker rm apache1
ExecStartPre=/usr/bin/docker pull coreos/apache
ExecStart=/usr/bin/docker run -rm --name apache1 -p 80:80 coreos/apache /usr/sbin/apache2ctl -D FOREGROUND
ExecStop=/usr/bin/docker stop apache1

[X-Fleet]
X-Conflicts=apache@*.service
```

>[AZURE.NOTE]L’attributo `X-Conflicts` indica a CoreOS che è possibile eseguire solo un'istanza di questo contenitore in un determinato host CoreOS. Per ulteriori informazioni vedere [File unit].

Ora avviare le istanze di unità nel cluster CoreOS. Si noterà che vengono eseguite in tre computer diversi:

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 start apache@{1,2,3}.service

unit apache@3.service launched on 00c927e4.../100.79.62.16
unit apache@1.service launched on 62f0f66e.../100.79.86.62
unit apache@2.service launched on df85f2d1.../100.78.126.15

```
Per raggiungere il server Apache in esecuzione in una delle unità, inviare una semplice richiesta al servizio cloud che funziona da host per il cluster CoreOS.

`curl http://coreos-cluster.cloudapp.net`

Verrà visualizzato il testo predefinito restituito dal server Apache simile a quello mostrato di seguito:

```
\<htm\l>\<body\>\<h1\>It works!\</h1\>
\<p\>This is the default web page for this server.\</p\>
\<p\>The web server software is running but no content has been added, yet.\</p\>
\</body\>\</html\>
```

È possibile provare ad arrestare una o più macchine virtuali del cluster per verificare che il servizio di Apache continui a essere eseguito.

Al termine, arrestare e scaricare le unità.

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 stop apache@{1,2,3}.service
fleetctl --tunnel coreos-cluster.cloudapp.net:22 unload apache@{1,2,3}.service

```

## Passaggi successivi

È possibile provare a eseguire altre operazioni con il cluster CoreOS tre nodi in Azure. È possibile scoprire come creare cluster più complessi, usare Docker e creare applicazioni più interessanti leggendo l'[esercitazione su CoreOS di Tim Park], l'[esercitazione su CoreOS di Patrick Chanezon], la documentazione di [Docker] e la [panoramica di CoreOS].

Vedere anche [Computing Linux e open source in Azure] per ulteriori informazioni sull'utilizzo di ambienti open source su macchine virtuali Linux in Azure.

<!--Link references-->
[Azure Cross-Platform Interface (xplat-cli)]: xplat-cli.md
[CoreOS]: https://coreos.com/
[panoramica di CoreOS]: https://coreos.com/using-coreos/
[CoreOS with Azure]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
[esercitazione su CoreOS di Tim Park]: https://github.com/timfpark/coreos-azure
[esercitazione su CoreOS di Patrick Chanezon]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
[Docker]: http://docker.io
[YAML]: http://yaml.org/
[Come utilizzare CoreOS in Azure]: virtual-machines-linux-coreos-how-to.md
[Configurare un set con carico bilanciato]: http://msdn.microsoft.com/library/azure/dn655055.aspx
[Avvio dei contenitori con flotta]: https://coreos.com/docs/launching-containers/launching/launching-containers-fleet/
[File unit]: https://coreos.com/docs/launching-containers/launching/fleet-unit-files/
[File unità]: https://coreos.com/docs/launching-containers/launching/fleet-unit-files/
[immagine Docker Hub busybox]: https://registry.hub.docker.com/_/busybox/
[immagine Docker Hub Apache CoreOS]: https://registry.hub.docker.com/u/coreos/apache/
[Computing Linux e open source in Azure]: virtual-machines-linux-opensource.md
<!--HONumber=54-->