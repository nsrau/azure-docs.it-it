---
title: Applicazioni Oracle WebLogic Server in Azure | Microsoft Docs
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
ms.openlocfilehash: 9ba4e3a66ae8ef71b39aa26fd54048381237c2fa
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664049"
---
# <a name="oracle-weblogic-server-azure-applications"></a>Applicazioni Oracle WebLogic Server in Azure

## <a name="oracle-weblogic-server-is-a-scalable-enterprise-ready-java-ee-application-server"></a>Oracle WebLogic Server è un server applicazioni Java EE scalabile e di livello enterprise.

Oracle WebLogic Server è il primo server applicazioni della piattaforma Java aziendale, nativo del cloud, finora disponibile per lo sviluppo e la distribuzione di applicazioni aziendali distribuite multilivello. Le offerte di Azure WebLogic Server consentono di adottare il cloud computing grazie ad ampie possibilità di scelta e alla flessibilità per la migrazione a WebLogic, tra cui il trasferimento in modalità lift-and-shift delle applicazioni Java EE nel cloud di Azure, con interventi minimi e il massimo impatto. Con queste offerte è possibile effettuare automaticamente il provisioning di reti virtuali, risorse di archiviazione e Linux, installare WebLogic Server, configurare la sicurezza con un gruppo di sicurezza di rete, bilanciare il carico con Gateway applicazione di Azure, implementare l'autenticazione con Azure Active Directory e semplificare la connettività dei database, per avviare rapidamente le applicazioni aziendali.

Sono disponibili quattro offerte per soddisfare diversi scenari: nodo singolo senza server di amministrazione, nodo singolo con server di amministrazione, cluster e cluster dinamico.  È possibile provare liberamente le offerte, che sono disponibili gratuitamente.

_Le offerte si basano sul modello Bring Your Own License_. Si presuppone che siano già state acquistate le licenze appropriate di Oracle e che siano anche valide per eseguire le offerte in Microsoft Azure.

_Se si vuole collaborare a stretto contatto con il team di progettazione che ha sviluppato queste offerte per gli scenari di migrazione, premere il pulsante [Contatti](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)_  in [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview). Si verrà presto contattati da program manager, architetti e ingegneri per avviare la collaborazione.

### <a name="oracle-weblogic-server-single-node"></a>Oracle WebLogic Server a nodo singolo

Questa offerta prevede il provisioning di una singola macchina virtuale in cui verrà installato Oracle WebLogic Server. Non viene creato un dominio e non viene avviato il server di amministrazione. Questa opzione è utile per gli scenari con una configurazione del dominio altamente personalizzata.

### <a name="oracle-weblogic-server-with-admin-server"></a>Oracle WebLogic Server con server di amministrazione

Questa offerta prevede il provisioning di una singola macchina virtuale in cui verrà installato Oracle WebLogic Server. Viene creato un dominio e viene avviato il server di amministrazione per gestirlo.

### <a name="oracle-weblogic-server-cluster"></a>Cluster di Oracle WebLogic Server

Questa offerta prevede la creazione di un cluster a disponibilità elevata di macchine virtuali Oracle WebLogic Server. Il server di amministrazione e tutti i server gestiti vengono avviati per impostazione predefinita, consentendo di gestire il dominio.

### <a name="oracle-weblogic-server-dynamic-cluster"></a>Cluster dinamico di Oracle WebLogic Server

Questa offerta prevede la creazione di un cluster dinamico, scalabile e a disponibilità elevata di macchine virtuali Oracle WebLogic Server. Il server di amministrazione e tutti i server gestiti vengono avviati per impostazione predefinita, consentendo di gestire il dominio.

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
