---
title: Uso dello strumento Importazione/Esportazione di Azure | Documentazione Microsoft
description: Informazioni su come usare lo strumento Importazione/Esportazione per preparare i dischi rigidi per un processo di importazione o per ripristinare un processo di importazione o esportazione.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 19614570369e3814658f9ca6e50635507f094712
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977319"
---
# <a name="using-the-azure-importexport-tool"></a>Uso dello strumento di importazione/esportazione di Azure 

Lo strumento Importazione/Esportazione di Azure (WAImportExport.exe) consente di creare e gestire i processi per il servizio Importazione/Esportazione di Azure e trasferire così grandi quantità di dati da e verso l'archiviazione BLOB di Azure.

Questa documentazione riguarda la versione più recente dello strumento di importazione/esportazione di Azure. Per informazioni sull'utilizzo del modello di distribuzione classica, vedere [Using the Azure Import/Export Tool v1](storage-import-export-tool-how-to-v1.md) (Uso dello strumento di importazione/esportazione di Azure - versione 1).

Gli articoli seguenti mostrano come:  

- Installare e configurare lo strumento di importazione/esportazione di Azure.
- Preparare i dischi rigidi per un processo di importazione di dati dalle unità dell'utente all'archivio BLOB di Azure.
- Esaminare lo stato di un processo con i file di log di copia. 
- Ripristinare un processo di importazione. 
- Ripristinare un processo di esportazione. 
- Risolvere i problemi relativi allo strumento di importazione/esportazione di Azure. 

## <a name="next-steps"></a>Passaggi successivi

* [Setting up the WAImportExport tool](storage-import-export-tool-setup.md) (Configurazione dello strumento WAImportExport)
