---
title: Risoluzione dei problemi relativi allo strumento Importazione/Esportazione di Azure | Documentazione Microsoft
description: Informazioni sui problemi comuni rilevati dagli utenti durante l&quot;uso dello strumento Importazione/Esportazione e su come gestirli.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: b91ca5eb-c557-460a-9afc-0590b38471f9
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 9aca8aad3f268bf21f3bad9fa22821f5d825f99d
ms.openlocfilehash: 53755e0048cfd80d9f930419a105893e5b34dc41
ms.lasthandoff: 02/16/2017


---

# <a name="troubleshooting-the-azure-import-export-tool"></a>Risoluzione dei problemi relativi allo strumento Importazione/Esportazione di Azure
Lo strumento Importazione/Esportazione di Microsoft Azure restituisce messaggi di errore in caso di problemi. Questo argomento elenca alcuni problemi comuni in cui possono incorrere gli utenti.  
  
## <a name="a-copy-session-fails-what-i-should-do"></a>Cosa fare se una sessione di copia non viene completata correttamente?  
 Quando una sessione di copia non viene completata correttamente, sono disponibili due opzioni.  
  
 Se l'errore non è irreversibile, ad esempio se la condivisione di rete è stata offline per un breve periodo e ora è tornata online, è possibile riprendere la sessione di copia. Se l'errore è irreversibile, ad esempio se è stata specificata una directory del file di origine errata nei parametri della riga di comando, è necessario interrompere la sessione di copia. Vedere [Preparing Hard Drives for an Import Job](storage-import-export-tool-preparing-hard-drives-import-v1.md) (Preparazione dei dischi rigidi per un processo di importazione) per altre informazioni su come riprendere e interrompere le sessioni di copia.  
  
## <a name="i-cant-resume-or-abort-a-copy-session"></a>Non è possibile riprendere o interrompere una sessione di copia.  
 Se la sessione di copia è la prima sessione di copia per un'unità, dovrebbe essere visualizzato un messaggio di errore che indica che "la prima sessione di copia non può essere ripresa o interrotta". In questo caso, è possibile eliminare il file journal precedente e rieseguire il comando.  
  
 Se una sessione di copia non è la prima per un'unità, può essere sempre ripresa o interrotta.  
  
## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>È possibile creare il processo anche se il file journal è andato perso?  
 Il file journal per un'unità contiene le informazioni complete sulla copia dei dati in questa unità, è necessario per aggiungere altri file all'unità e verrà usato per creare un processo di importazione. Se il file journal è andato perso, sarà necessario ripetere tutte le sessioni di copia per l'unità.  
  
## <a name="see-also"></a>Vedere anche  
 [Configurazione dello strumento Importazione/Esportazione di Azure](storage-import-export-tool-setup-v1.md)   
 [Preparing Hard Drives for an Import Job](storage-import-export-tool-preparing-hard-drives-import-v1.md)  (Preparazione dei dischi rigidi per un processo di importazione)  
 [Reviewing Job Status with Copy Log Files](storage-import-export-tool-reviewing-job-status-v1.md)  (Revisione dello stato dei processi con i file di log di copia)  
 [Repairing an Import Job](storage-import-export-tool-repairing-an-import-job-v1.md)  (Riparazione di un processo di importazione)  
 [Repairing an Export Job](storage-import-export-tool-repairing-an-export-job-v1.md) (Riparazione di un processo di esportazione)

