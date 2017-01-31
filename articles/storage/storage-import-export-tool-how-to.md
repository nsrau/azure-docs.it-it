---
title: Uso dello strumento di importazione/esportazione di Azure - versione 1 | Documentazione Microsoft
description: Informazioni su come usare lo strumento di importazione/esportazione per preparare i dischi rigidi per un processo di importazione o per ripristinare un processo di importazione o esportazione.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: f77535bb-d577-438a-bdd3-e15a82e0c543
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: ca2d4bf2b1e1cb56910fa3ee7af6e45b6b77b450
ms.openlocfilehash: 7c628022a6de01de50f50256bd2ea0cdba2c2555


---

# <a name="using-the-azure-importexport-tool"></a>Uso dello strumento di importazione/esportazione di Azure 

Lo strumento di importazione/esportazione di Azure (WAImportExport.exe) consente di creare e gestire i processi per il servizio Importazione/Esportazione di Azure e trasferire così grandi quantità di dati da e verso l'archivio BLOB di Azure.

Questa documentazione riguarda la versione più recente dello strumento di importazione/esportazione di Azure. Per informazioni sull'uso della versione 1, vedere [Uso dello strumento di importazione/esportazione di Azure (v1)](storage-import-export-tool-how-to-v1.md).

Questi articoli illustrano come usare lo strumento per eseguire le operazioni seguenti:  

- Installare e configurare lo strumento di importazione/esportazione.
- Preparare i dischi rigidi per un processo di importazione di dati dalle unità dell'utente all'archivio BLOB di Azure.
- Esaminare lo stato di un processo con i file di log di copia. 
- Ripristinare un processo di importazione. 
- Ripristinare un processo di esportazione. 
- Risolvere i problemi relativi allo strumento di importazione/esportazione di Azure, in caso di problemi durante il processo. 



<!--HONumber=Dec16_HO3-->


