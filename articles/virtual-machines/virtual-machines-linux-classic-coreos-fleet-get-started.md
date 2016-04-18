<properties
	pageTitle="Guida introduttiva a fleet in CoreOS | Microsoft Azure"
	description="Fornisce esempi di base sull'uso di fleet e Docker in un cluster di macchine virtuali Linux CoreOS creato con il modello di distribuzione classica in Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="03/09/2016"
	ms.author="danlep"/>

# Guida introduttiva a fleet in un cluster di macchina virtuale CoreOS in Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](https://github.com/Azure/azure-quickstart-templates/tree/master/coreos-with-fleet-multivm).


Di seguito sono riportati due esempi rapidi relativi all'uso di [fleet](https://github.com/coreos/fleet) e [Docker](https://www.docker.com/) per eseguire le applicazioni in un cluster di macchine virtuali [CoreOS] in Azure.

Per utilizzare questi esempi, configurare innanzitutto un cluster CoreOS a tre nodi come descritto in [Come utilizzare CoreOS in Azure]. Una volta eseguita questa operazione, si comprenderanno gli elementi di base delle distribuzioni di CoreOS e si disporrà di un in funzione e di un computer client. In questi esempi verrà utilizzato esattamente lo stesso nome cluster. Gli esempi presumono anche l'uso di un host Linux locale per eseguire i comandi **fleetctl**. Vedere [Usare il client](https://coreos.com/fleet/docs/latest/using-the-client.html) per altre informazioni sul client **fleetctl**.


## Esempio 1: Hello World con Docker</a>

Ecco una semplice applicazione "Hello World"che si esegue in un singolo contenitore docker. Viene utilizzata l’[immagine Docker Hub busybox].

Nel computer client Linux utilizzare l’editor di testo preferito per creare il seguente file unità **systemd** e denominarlo `helloworld.service` (per informazioni dettagliate sulla sintassi, vedere [File unità]).

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

>[AZURE.NOTE] Per eseguire i comandi **fleetctl** remoti senza il parametro **--tunnel**, a scelta impostare la variabile di ambiente FLEETCTL\_TUNNEL per effettuare il tunneling delle richieste. Ad esempio: `export FLEETCTL_TUNNEL=coreos-cluster.cloudapp.net:22`.


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


## Esempio 2: server nginx a disponibilità elevata</a>

Un vantaggio dell'utilizzo di CoreOS, Docker, e **fleet** consiste nella possibilità di eseguire i servizi a disponibilità elevata in modo semplice. In questo esempio verrà distribuito un servizio costituito da tre contenitori identici che eseguono il server web nginx. I contenitori saranno eseguiti sulle tre macchine virtuali del cluster. Questo esempio è simile a uno esempio in [Avvio dei contenitori con fleet] e utilizza l’[immagine Docker Hub nginx].

>[AZURE.IMPORTANT] Per eseguire il server web a disponibilità elevata, è necessario configurare un endpoint HTTP con carico bilanciato nelle macchine virtuali (porta pubblica 80, porta privata 80). È possibile eseguire questa operazione dopo la creazione del cluster CoreOS, tramite il portale di Azure classico o il comando **azure vm endpoint**. Per altre informazioni, vedere [Configurare un set con carico bilanciato].

Nel computer client, utilizzare l’editor di testo preferito per creare un file unità del modello **systemd**, denominato nginx@.service. Questo modello verrà utilizzato per avviare tre istanze distinte, denominate nginx@1.service, nginx@2.service e nginx@3.service:

```
[Unit]
Description=High Availability Nginx
After=docker.service
Requires=docker.service

[Service]
TimeoutStartSec=0
ExecStartPre=/usr/bin/docker pull nginx
ExecStart=/usr/bin/docker run --rm --name nginx1 -p 80:80 nginx
ExecStop=/usr/bin/docker stop nginx1

[X-Fleet]
X-Conflicts=nginx@*.service
```

>[AZURE.NOTE] L’attributo `X-Conflicts` indica a CoreOS che è possibile eseguire solo un'istanza di questo contenitore in un determinato host CoreOS. Per ulteriori informazioni vedere [File unit].

Ora avviare le istanze di unità nel cluster CoreOS. Si noterà che vengono eseguite in tre computer diversi:

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 start nginx@{1,2,3}.service

unit nginx@3.service launched on 00c927e4.../100.79.62.16
unit nginx@1.service launched on 62f0f66e.../100.79.86.62
unit nginx@2.service launched on df85f2d1.../100.78.126.15

```
Per raggiungere il server web in esecuzione in una delle unità, inviare una semplice richiesta al servizio cloud che funziona da host per il cluster CoreOS.

`curl http://coreos-cluster.cloudapp.net`

Verrà visualizzato il testo predefinito restituito dal server nginx simile a quello mostrato di seguito:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

È possibile provare ad arrestare una o più macchine virtuali del cluster per verificare che il servizio web continui a essere eseguito.

Al termine, arrestare e scaricare le unità.

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 stop nginx@{1,2,3}.service
fleetctl --tunnel coreos-cluster.cloudapp.net:22 unload nginx@{1,2,3}.service

```

## Passaggi successivi

* È possibile provare a eseguire altre operazioni con il cluster CoreOS tre nodi in Azure. È possibile scoprire come creare cluster più complessi e usare Docker per creare applicazioni più interessanti leggendo l'[esercitazione su CoreOS di Tim Park], l'[esercitazione su CoreOS di Patrick Chanezon], la documentazione di [Docker] e la [panoramica di CoreOS].

* Per iniziare a utilizzare Fleet e CoreOS in Gestione risorse di Azure, provare questo [modello delle Guide rapide](https://github.com/Azure/azure-quickstart-templates/tree/master/coreos-with-fleet-multivm).

* Vedere anche [Computing Linux e open source in Azure] per ulteriori informazioni sull'utilizzo di ambienti open source su macchine virtuali Linux in Azure.

<!--Link references-->
[Azure Command-Line Interface (Azure)]: ../xplat-cli-install.md
[CoreOS]: https://coreos.com/
[panoramica di CoreOS]: https://coreos.com/using-coreos/
[CoreOS with Azure]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
[esercitazione su CoreOS di Tim Park]: https://github.com/timfpark/coreos-azure
[esercitazione su CoreOS di Patrick Chanezon]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
[Docker]: http://docker.io
[YAML]: http://yaml.org/
[Come utilizzare CoreOS in Azure]: virtual-machines-linux-classic-coreos-howto.md
[Configurare un set con carico bilanciato]: ../load-balancer/load-balancer-get-started-internet-classic-cli.md
[Avvio dei contenitori con fleet]: https://coreos.com/docs/launching-containers/launching/launching-containers-fleet/
[File unit]: https://coreos.com/docs/launching-containers/launching/fleet-unit-files/
[File unità]: https://coreos.com/docs/launching-containers/launching/fleet-unit-files/
[immagine Docker Hub busybox]: https://registry.hub.docker.com/_/busybox/
[immagine Docker Hub nginx]: https://hub.docker.com/_/nginx/
[Computing Linux e open source in Azure]: virtual-machines-linux-opensource-links.md

<!---HONumber=AcomDC_0406_2016-->