---
title: Proteggere Azure Data Lake Analytics per più utenti
description: Informazioni su come configurare più utenti per eseguire i processi in Azure Data Lake Analytics.
ms.service: data-lake-analytics
services: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 05/30/2018
ms.openlocfilehash: 9006a22c588a7f1456585d40da0b4345145c6d05
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87132484"
---
# <a name="configure-user-access-to-job-information-to-job-information-in-azure-data-lake-analytics"></a>Configurare l'accesso dell'utente alle informazioni sul processo in Azure Data Lake Analytics 

In Azure Data Lake Analytics è possibile usare più account utente o entità servizio per eseguire i processi. 

Affinché tali utenti possano visualizzare le informazioni dettagliate sul processo, è necessario che siano in grado di leggere il contenuto delle cartelle dei processi. Le cartelle dei processi si trovano nella directory `/system/`. 

Se non sono configurate le autorizzazioni necessarie l'utente potrebbe visualizzare un errore: `Graph data not available - You don't have permissions to access the graph data.` 

## <a name="configure-user-access-to-job-information"></a>Configurare l'accesso dell'utente alle informazioni sul processo

È possibile usare **Aggiunta guidata utente** per configurare gli ACL nelle cartelle. Per altre informazioni, vedere [Aggiungere un nuovo utente](data-lake-analytics-manage-use-portal.md#add-a-new-user).

Se è necessario eseguire un controllo più granulare o elaborare le autorizzazioni, proteggere le cartelle come indicato di seguito:

1. Concedere le autorizzazioni di **esecuzione**, tramite un ACL di accesso, nella cartella radice:
   - /
   
2. Concedere le autorizzazioni di **esecuzione** e **lettura**, tramite un ACL di accesso e un ACL predefinito, nelle cartelle che contengono le cartelle del processo. Ad esempio, per un processo specifico che è stato eseguito il 25 maggio 2018, è necessario accedere a queste cartelle:
   - /system
   - /system/jobservice
   - /system/jobservice/jobs
   - /system/jobservice/jobs/Usql
   - /system/jobservice/jobs/Usql/2018
   - /system/jobservice/jobs/Usql/2018/05
   - /system/jobservice/jobs/Usql/2018/05/25
   - /system/jobservice/jobs/Usql/2018/05/25/11
   - /system/jobservice/jobs/Usql/2018/05/25/11/01
   - /system/jobservice/jobs/Usql/2018/05/25/11/01/b074bd7a-1448-d879-9d75-f562b101bd3d

## <a name="next-steps"></a>Passaggi successivi
[Aggiungere un nuovo utente](data-lake-analytics-manage-use-portal.md#add-a-new-user)
