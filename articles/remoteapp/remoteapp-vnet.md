---
title: Convalidare la rete virtuale di Azure da usare con Azure RemoteApp | Documentazione Microsoft
description: "Informazioni su come assicurarsi che la rete virtuale di Azure sia pronta all’utilizzo con Azure RemoteApp"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: b573ba02-4587-4be5-9821-27bd891a73b2
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 05c8a0ff04293947cec391b6467cc4adddb6a7b0
ms.lasthandoff: 03/31/2017


---
# <a name="validate-the-azure-vnet-to-use-with-azure-remoteapp"></a>Convalidare la rete virtuale di Azure da usare con Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp verrà sospeso a partire dal 31 agosto 2017. Per i dettagli, vedere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Prima di utilizzare una rete virtuale di Azure con Azure RemoteApp, è possibile convalidare la rete virtuale. Consente di evitare problemi con la connettività.

Per convalidare la rete virtuale di Azure, procedere come segue:

1. Creare una macchina virtuale di Azure all'interno della subnet della rete virtuale di Azure da usare con Azure RemoteApp.
2. Connettersi a tale macchina virtuale usando l’opzione **Connetti** nel portale di gestione.
3. Aggiungere la macchina virtuale allo stesso dominio che si desidera usare con Azure RemoteApp. Se si sta creando una raccolta ibrida che si connette alla rete locale, aggiungere la macchina virtuale al dominio locale.

Se l'operazione viene completata, la rete virtuale di Azure è pronta per l'utilizzo con RemoteApp.

Per ulteriori informazioni sul flusso di lavoro della raccolta ibrida end-to-end, vedere gli articoli seguenti:

* [Come pianificare la rete virtuale per RemoteApp di Azure](remoteapp-planvnet.md)
* [Creare una raccolta ibrida](remoteapp-create-hybrid-deployment.md)
* [Distribuire la raccolta di Azure RemoteApp alla rete virtuale di Azure (con supporto per ExpressRoute)](http://blogs.msdn.com/b/rds/archive/2015/04/23/deploy-azure-remoteapp-collection-to-your-azure-virtual-network-with-support-for-expressroute.aspx)


