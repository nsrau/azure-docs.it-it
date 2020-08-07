---
title: Che cos'è Oracle WebLogic Server in Azure?
description: Informazioni su come eseguire Oracle WebLogic Server in Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: edburns
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2020
ms.author: edburns
ms.openlocfilehash: fe247e75040f658beb94a66176f802993268a7d8
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87851860"
---
# <a name="what-is-oracle-weblogic-server-on-azure"></a>Che cos'è Oracle WebLogic Server in Azure?

Questa pagina descrive le soluzioni per l'esecuzione di WebLogic Server (WLS) in macchine virtuali di Azure.  Queste soluzioni sono sviluppate congiuntamente da Oracle e Microsoft.

Oracle WebLogic Server è il primo server applicazioni della piattaforma Java aziendale, nativo del cloud, finora disponibile per lo sviluppo e la distribuzione di applicazioni aziendali distribuite multilivello. Le offerte di Azure WebLogic Server consentono di adottare cloud computing.  Per la migrazione di WebLogic è possibile scegliere una maggiore flessibilità, tra cui il lift e il passaggio delle applicazioni Java EE al cloud di Azure.   WLS in Azure produce un notevole effetto con un piccolo sforzo. Offre la possibilità di avviare rapidamente le applicazioni line-of-business (LOB).  Ogni offerta effettua automaticamente il provisioning di reti virtuali, risorse di archiviazione e Linux.  Con zero sforzo, WebLogic Server è installato.  WLS in Azure configura la sicurezza con un gruppo di sicurezza di rete, il bilanciamento del carico con app Azure gateway, l'autenticazione con Azure Active Directory e la connessione automatica al database esistente.

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="È possibile usare il portale di Azure per distribuire WebLogic Server in Azure":::

Sono disponibili quattro offerte per soddisfare diversi scenari, ovvero un singolo nodo senza un server di amministrazione, un singolo nodo con un server di amministrazione, un cluster e un cluster dinamico.  Prova le offerte, sono disponibili gratuitamente.

_Le offerte si basano sul modello Bring Your Own License_. Si presuppone che l'utente abbia già ottenuto le licenze appropriate con Oracle e che disponga di una licenza corretta per l'esecuzione di offerte in Microsoft Azure.

_Se si desidera lavorare a stretto contatto con gli scenari di migrazione con il team di progettazione che sviluppa queste offerte, selezionare il pulsante [Contatta](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) l'utente_ in [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview). Program Manager, architetti e ingegneri ti contatteranno a breve e potrai iniziare la collaborazione.

## <a name="oracle-weblogic-server-single-node"></a>Oracle WebLogic Server a nodo singolo

Questa offerta esegue il provisioning di una singola macchina virtuale e ne installa WLS. Non crea un dominio o avvia il server di amministrazione. Un nodo singolo è utile per scenari con configurazione di dominio altamente personalizzata.

## <a name="oracle-weblogic-server-with-admin-server"></a>Oracle WebLogic Server con server di amministrazione

Questa offerta esegue il provisioning di una singola macchina virtuale e ne installa WLS. Viene creato un dominio e viene avviato il server di amministrazione per gestirlo.

## <a name="oracle-weblogic-server-cluster"></a>Cluster di Oracle WebLogic Server

Questa offerta crea un cluster a disponibilità elevata di macchine virtuali WLS. Il server di amministrazione e tutti i server gestiti vengono avviati per impostazione predefinita, consentendo di gestire il dominio.

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Cluster dinamico di Oracle WebLogic Server

Questa offerta crea un cluster dinamico scalabile e a disponibilità elevata di macchine virtuali WLS. Il server di amministrazione e tutti i server gestiti vengono avviati per impostazione predefinita, consentendo di gestire il dominio.

## <a name="next-steps"></a>Passaggi successivi

Esplorare le offerte in Azure Marketplace.

> [!div class="nextstepaction"]
> [Oracle WebLogic Server a nodo singolo](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server con server di amministrazione](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Cluster di Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Cluster dinamico di Oracle WebLogic Server](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
