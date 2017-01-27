---
title: Monitorare un cluster del servizio contenitore di Azure con Sysdig | Documentazione Microsoft
description: Monitorare un cluster del servizio contenitore di Azure con Sysdig.
services: container-service
documentationcenter: 
author: sauryadas
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Contenitori, controller di dominio/sistema operativo, Azure
ms.assetid: 91d9a28a-3a52-4194-879e-30f2fa3d946b
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2016
ms.author: saudas
translationtype: Human Translation
ms.sourcegitcommit: 5da28eecf2df764f71906897450ebe67b3048b3f
ms.openlocfilehash: d57212a8c858c4fb53cc619a578b59da92176bb2


---
# <a name="monitor-an-azure-container-service-cluster-with-sysdig"></a>Monitorare un cluster del servizio contenitore di Azure con Sysdig
In questo articolo verranno distribuiti agenti di Sysdig in tutti i nodi agente nel cluster del servizio contenitore di Azure. Per questa configurazione, è necessario un account con Sysdig. 

## <a name="prerequisites"></a>Prerequisiti
[Distribuire](container-service-deployment.md) e [connettere](container-service-connect.md) un cluster configurato dal servizio contenitore di Azure. Esplorare l' [interfaccia utente](container-service-mesos-marathon-ui.md)di Marathon. Andare a [http://app.sysdigcloud.com](http://app.sysdigcloud.com) per configurare un account cloud Sysdig. 

## <a name="sysdig"></a>Sysdig
Sysdig è un servizio di monitoraggio che consente di monitorare i contenitori nel cluster. Sysdig non solo consente di risolvere problemi, ma include anche le metriche di monitoraggio di base per CPU, rete, memoria e I/O. Con Sysdig è facile determinare quali contenitori sono più usati o quali usano la maggior quantità di memoria e CPU. Questa visualizzazione è disponibile nella sezione della panoramica, attualmente nella versione beta. 

![Interfaccia utente di Sysdig](./media/container-service-monitoring-sysdig/sysdig6.png) 

## <a name="configure-a-sysdig-deployment-with-marathon"></a>Configurare una distribuzione Sysdig con Marathon
Questi passaggi illustrano come configurare e distribuire le applicazioni Sysdig nel cluster con Marathon. 

Accedere all'interfaccia utente del controller di dominio/sistema operativo tramite [http://localhost:80/](http://localhost:80/)Dall'interfaccia utente del controller di dominio/sistema operativo passare a "Universe" ("Universo") in basso a sinistra e quindi cercare "Sysdig".

![Sysdig in Universe (Universo) per il controller di dominio/sistema operativo](./media/container-service-monitoring-sysdig/sysdig1.png)

Per completare la configurazione, è necessario un account cloud Sysdig o un account di valutazione gratuito. Una volta connessi al sito Web del cloud di Sysdig, fare clic sul nome utente. Nella pagina verrà visualizzata la chiave di accesso. 

![Chiave API di Sysdig](./media/container-service-monitoring-sysdig/sysdig2.png) 

Immettere quindi la chiave di accesso nella configurazione di Sysdig in Universe (Universo) per il controller di dominio/sistema operativo. 

![Configurazione di Sysdig in Universe (Universo) per il controller di dominio/sistema operativo](./media/container-service-monitoring-sysdig/sysdig3.png)

Impostare ora le istanze su 10000000 in modo che, quando viene aggiunto un nuovo nodo al cluster, Sysdig distribuirà automaticamente un agente nel nuovo nodo. Questa è una soluzione provvisoria per verificare che Sysdig venga distribuito in tutti i nuovi agenti del cluster. 

![Configurazione di Sysdig nelle istanze di Universe (Universo) per il controller di dominio/sistema operativo](./media/container-service-monitoring-sysdig/sysdig4.png)

Una volta installato il pacchetto, tornare all'interfaccia utente di Sysdig per esplorare le diverse metriche di utilizzo per i contenitori nel cluster. 

È anche possibile installare i dashboard specifici di Mesos e Marathon tramite la [creazione guidata nuovo dashboard](https://app.sysdigcloud.com/#/dashboards/new).



<!--HONumber=Jan17_HO2-->


