---
title: Ripristinare un'app in Azure
description: Informazioni su come ripristinare l'app da uno snapshot.
services: app-service
documentationcenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: multiple
ms.topic: article
ms.date: 04/04/2018
ms.author: aelnably;nicking
ms.openlocfilehash: e1ae8fcc30323c865aa96937f43054515f293394
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Ripristinare un'app in Azure da uno snapshot
Questo articolo illustra come ripristinare un'app in [Servizio app di Azure](../app-service/app-service-web-overview.md) da uno snapshot. È possibile ripristinare uno stato precedente dell'app, in base a uno degli snapshot dell'app. Non è necessario abilitare il backup degli snapshot. La piattaforma salva automaticamente uno snapshot di tutte le app per scopi di ripristino dei dati.

Gli snapshot sono copie shadow incrementali e offrono diversi vantaggi rispetto ai normali [backup](web-sites-backup.md):
- Nessun errore di copia di file a causa di blocchi di file.
- Nessuna limitazione delle dimensioni di archiviazione.
- Nessuna configurazione necessaria.

Il ripristino da snapshot è disponibile per le app in esecuzione a livello **Premium** o superiore. Per informazioni sul passaggio dell'app a un piano superiore, vedere [Scalare un'app Web in Servizio app di Azure](web-sites-scale.md).

## <a name="limitations"></a>Limitazioni

- La funzionalità è attualmente in anteprima.
- È possibile eseguire il ripristino solo nella stessa app o in uno slot appartenente a tale app.
- Il servizio app arresta l'app di destinazione o lo slot di destinazione durante il ripristino.
- Il servizio app conserva tre mesi di snapshot per scopi di ripristino dei dati della piattaforma.
- È possibile ripristinare gli snapshot solo per gli ultimi 30 giorni.
 

## <a name="restore-an-app-from-a-snapshot"></a>Ripristinare un'app da uno snapshot

1. Nella pagina **Impostazioni** dell'app nel [portale di Azure](https://portal.azure.com) fare clic su **Backup** per visualizzare la pagina **Backup**. Fare quindi clic su **Ripristina** nella sezione **Snapshot (anteprima)**.
   
    ![](./media/app-service-web-restore-snapshots/1.png)

2. Nella pagina di **ripristino** selezionare lo snapshot da ripristinare.
   
    ![](./media/app-service-web-restore-snapshots/2.png)
   
3. Specificare la destinazione per il ripristino dell'app in **Destinazione di ripristino**.
   
    ![](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > Se si sceglie **Sovrascrivi**, tutti i dati esistenti nel file system corrente dell'app verranno cancellati e sovrascritti. Prima di scegliere **OK**, assicurarsi che sia ciò che si vuole eseguire.
   > 
   > 
      
   > [!Note]
   > A causa delle limitazioni tecniche correnti, è possibile eseguire il ripristino solo in app nella stessa unità di scala. Questa limitazione verrà rimossa in una versione futura.
   > 
   > 
   
    È possibile selezionare **App esistente** per eseguire il ripristino in uno slot. Prima di usare questa opzione, è necessario avere già creato uno slot dell'app.

4. È possibile scegliere di ripristinare la configurazione del sito.
   
    ![](./media/app-service-web-restore-snapshots/4.png)

5. Fare clic su **OK**.
