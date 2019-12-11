---
title: Risoluzione dei problemi relativi allo strumento Importazione/Esportazione di Azure | Documentazione Microsoft
description: Informazioni su alcuni problemi comuni rilevati durante l'uso dello strumento Importazione/Esportazione di Azure e su come gestirli.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 7e463e1cdd340f852af46e39cca0dd9bfce7b8da
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978935"
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Risoluzione dei problemi relativi allo strumento di importazione/esportazione di Azure
Lo strumento Importazione/Esportazione di Microsoft Azure restituisce messaggi di errore in caso di problemi. Questo argomento elenca alcuni problemi comuni in cui possono incorrere gli utenti.  
  
## <a name="a-copy-session-fails-what-i-should-do"></a>Cosa fare se una sessione di copia non viene completata correttamente?  
 Quando una sessione di copia non viene completata correttamente, sono disponibili due opzioni.  
  
 Se l'errore non è irreversibile, ad esempio se la condivisione di rete è stata offline per un breve periodo e ora è tornata online, è possibile riprendere la sessione di copia. Se l'errore è irreversibile, ad esempio se è stata specificata una directory del file di origine errata nei parametri della riga di comando, è necessario interrompere la sessione di copia. Vedere [Preparing Hard Drives for an Import Job](../storage-import-export-tool-preparing-hard-drives-import-v1.md) (Preparazione dei dischi rigidi per un processo di importazione) per altre informazioni su come riprendere e interrompere le sessioni di copia.  
  
## <a name="i-cant-resume-or-abort-a-copy-session"></a>Non è possibile riprendere o interrompere una sessione di copia.  
 Se la sessione di copia è la prima sessione di copia per un'unità, dovrebbe essere visualizzato un messaggio di errore che indica che "la prima sessione di copia non può essere ripresa o interrotta". In questo caso, è possibile eliminare il file journal precedente e rieseguire il comando.  
  
 Se una sessione di copia non è la prima per un'unità, può essere sempre ripresa o interrotta.  
  
## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>È possibile creare il processo anche se il file journal è andato perso?  
 Il file journal per un'unità contiene le informazioni complete sulla copia dei dati in questa unità, è necessario per aggiungere altri file all'unità e verrà usato per creare un processo di importazione. Se il file journal è andato perso, sarà necessario ripetere tutte le sessioni di copia per l'unità.  
  
## <a name="next-steps"></a>Passaggi successivi
 
* [Configurazione dello strumento Importazione/Esportazione di Azure](../storage-import-export-tool-setup-v1.md)   
* [Preparing hard drives for an import job](../storage-import-export-tool-preparing-hard-drives-import-v1.md) (Preparazione dei dischi rigidi per un processo di importazione)   
* [Revisione dello stato dei processi con i file di log di copia](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [Riparazione di un processo di importazione](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Riparazione di un processo di esportazione](../storage-import-export-tool-repairing-an-export-job-v1.md)
