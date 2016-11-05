---
title: Pool di agenti DC/OS pubblici e privati nel servizio contenitore di Azure | Microsoft Docs
description: Funzionamento dei pool di agenti pubblici e privati con un cluster del servizio contenitore di Azure.
services: container-service
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
tags: acs, azure-container-service
keywords: Docker, Contenitori, Micro-servizi, Mesos, Azure

ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2016
ms.author: adegeo

---
# Pool di agenti DC/OS per il servizio contenitore di Azure
Il servizio contenitore di Azure DC/OS divide gli agenti in pool pubblici o privati. La distribuzione può essere eseguita in uno dei pool, ma ciò influisce sull'accessibilità tra i computer nel servizio contenitore. I computer possono essere esposti a Internet (pubblico) o rimanere interni (privato). Questo articolo fornisce una breve panoramica dei motivi per cui esiste un pool pubblico e uno privato.

### Agenti privati
I nodi di agenti privati vengono eseguiti tramite una rete non instradabile, accessibile unicamente dalla zona di amministrazione o attraverso il router perimetrale della zona pubblica. Per impostazione predefinita, DC/OS avvia le applicazioni in nodi di agenti privati. Per altre informazioni sulla sicurezza di rete, vedere la [documentazione di DC/OS](https://dcos.io/docs/1.7/administration/securing-your-cluster/).

### Agenti pubblici
I nodi di agenti pubblici eseguono app DC/OS attraverso una rete accessibile pubblicamente. Per altre informazioni sulla sicurezza di rete, vedere la [documentazione di DC/OS](https://dcos.io/docs/1.7/administration/securing-your-cluster/).

## Uso dei pool di agenti
Per impostazione predefinita, **Marathon** distribuisce le nuove applicazioni in nodi di agenti *privati*. Per distribuire l'applicazione nel nodo *pubblico*, è necessario farlo in modo esplicito mentre la si crea. Selezionare la scheda **Optional** (Facoltativo) e immettere **slave\_public** come valore di **Accepted Resource Roles** (Ruoli risorsa accettati). Questo processo è documentato [qui](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) e nella documentazione di [DC/OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/).

## Passaggi successivi
Sono disponibili altre informazioni sulla [gestione dei contenitori DC/OS](container-service-mesos-marathon-ui.md).

Informazioni su come [aprire il firewall](container-service-enable-public-access.md) fornito da Azure per consentire l'accesso pubblico al contenitore DC/OS.

<!---HONumber=AcomDC_0907_2016-->