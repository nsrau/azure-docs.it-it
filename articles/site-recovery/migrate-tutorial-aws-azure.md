---
title: Eseguire la migrazione di macchine virtuali AWS ad Azure con il servizio Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come eseguire la migrazione di macchine virtuali Windows in esecuzione in Amazon Web Services (AWS) ad Azure usando Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: dd91e99b45405cca10b9ddc2982674e72ad6bf86
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281294"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Eseguire la migrazione di macchine virtuali Amazon Web Services (AWS) ad Azure

Questo articolo descrive le opzioni per la migrazione di istanze di Amazon Web Services (AWS) in Azure.

## <a name="migrate-with-azure-migrate"></a>Eseguire la migrazione con Azure Migrate

È consigliabile eseguire la migrazione di istanze di AWS ad Azure usando il servizio [Azure Migrate](../migrate/migrate-services-overview.md). Azure Migrate offre un hub centralizzato per la valutazione e la migrazione di computer locali in Azure, usando Azure Migrate, altri servizi Azure e strumenti di terze parti.

[Informazioni su come](../migrate/tutorial-migrate-aws-virtual-machines.md) eseguire la migrazione di istanze di AWS con Azure Migrate. 


## <a name="migrate-with-site-recovery"></a>Eseguire la migrazione con Site Recovery

Site Recovery deve essere usato solo per il ripristino di emergenza e non per la migrazione.

Se si usa già Azure Site Recovery e si vuole continuare a usarlo per la migrazione di AWS, seguire la stessa procedura usata per configurare il [ripristino di emergenza dei computer fisici](physical-azure-disaster-recovery.md).


> [!NOTE]
> Quando si esegue un failover per il ripristino di emergenza, come ultimo passaggio è necessario eseguire il commit del failover. Quando si esegue la migrazione di istanze di AWS, l'opzione **Esegui commit** non è pertinente. Selezionare invece l'opzione **Completa la migrazione**. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare le domande comuni](../migrate/resources-faq.md) su Azure Migrate.
