<properties
   pageTitle="Pool di agenti pubblici e privati nel servizio contenitore di Azure | Microsoft Azure"
   description="Funzionamento dei pool di agenti pubblici e privati con un cluster del servizio contenitore di Azure."
   services="container-service"
   documentationCenter=""
   authors="Thraka"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Contenitori, Micro-servizi, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="adegeo"/>  

# Pool di agenti

Il servizio contenitore di Azure divide gli agenti in più pool. Le singole macchine virtuale in un pool sono identiche, ma ogni pool ha una propria subnet. È quindi possibile contrassegnare ogni pool come pubblico o privato e questo influisce sull'accessibilità tra i computer nel servizio contenitore.

### Agenti privati

I nodi di agenti privati vengono eseguiti tramite una rete non instradabile, accessibile unicamente dalla zona di amministrazione o attraverso il router perimetrale della zona pubblica. Per impostazione predefinita, DC/OS avvia le applicazioni in nodi di agenti privati. Per altre informazioni sulla sicurezza di rete, vedere la [documentazione di DC/OS](https://dcos.io/docs/1.7/administration/securing-your-cluster/).

### Agenti pubblici

I nodi di agenti pubblici eseguono app DC/OS attraverso una rete accessibile pubblicamente. Per altre informazioni sulla sicurezza di rete, vedere la [documentazione di DC/OS](https://dcos.io/docs/1.7/administration/securing-your-cluster/).

## Uso dei pool di agenti

Per impostazione predefinita, **Marathon** distribuisce le nuove applicazioni in nodi di agenti *privati*. Per distribuire l'applicazione nel nodo *pubblico*, è necessario farlo in modo esplicito. La distribuzione nel nodo pubblico non è semplice come selezionare una casella. Durante la creazione di una nuova applicazione, passare alla scheda **Optional** (Facoltativo) e immettere **slave\_public** per **Accepted Resource Roles** (Ruoli risorsa accettati). Questo processo è documentato [qui](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) e nella documentazione di [DC/OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/).

## Passaggi successivi

Sono disponibili altre informazioni sulla [gestione dei contenitori DC/OS](container-service-mesos-marathon-ui.md).

<!---HONumber=AcomDC_0824_2016-->