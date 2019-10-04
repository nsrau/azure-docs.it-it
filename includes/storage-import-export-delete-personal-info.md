---
title: File di inclusione
description: File di inclusione
services: azure-policy
author: craigshoemaker
ms.service: azure-policy
ms.topic: include
ms.date: 05/18/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: e6a0ded137162328fd446b65ddb4a15fa6f1db88
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180360"
---
## <a name="deleting-personal-information"></a>Eliminazione delle informazioni personali

[!INCLUDE [gdpr-intro-sentence.md](gdpr-intro-sentence.md)]

Le informazioni personali sono rilevanti per il servizio di importazione/esportazione (tramite il portale e l'API) durante le operazioni di importazione ed esportazione. I dati usati durante queste procedure includono:

- Nome del contatto
- Numero di telefono
- Email
- Indirizzo
- city
- CAP
- Stato
- Paese/provincia/area geografica
- ID unità
- Numero di account del vettore
- Numero di tracciabilità della spedizione

Quando viene creato un processo di importazione/esportazione, gli utenti forniscano le informazioni di contatto e un indirizzo di spedizione. Le informazioni personali vengono archiviate al massimo in due posizioni diverse, ovvero nel processo e, facoltativamente, nelle impostazioni del portale. Le informazioni personali vengono archiviate solo nelle impostazioni del portale se si seleziona la casella di controllo **Salva il vettore e l'indirizzo del mittente come valori predefiniti** nella sezione *Informazioni sul mittente della spedizione* del processo di esportazione.

Le informazioni di contatto personali potrebbero essere eliminate nei modi seguenti:

- I dati salvati con il processo vengono eliminati con il processo. Gli utenti possono eliminare manualmente i processi e i processi completati vengono eliminati automaticamente dopo 90 giorni. È possibile eliminare manualmente i processi tramite l'API REST o il portale di Azure. Per eliminare il processo nel portale di Azure, andare al processo di importazione/esportazione e fare clic su *Elimina* dalla barra dei comandi. Per informazioni dettagliate su come eliminare un processo di importazione/esportazione tramite l'API REST, vedere [Eliminare un processo di importazione/esportazione](../articles/storage/common/storage-import-export-cancelling-and-deleting-jobs.md).

- Le informazioni di contatto salvate nelle impostazioni del portale potrebbero essere rimosse in seguito all'eliminazione delle impostazioni del portale. È possibile eliminare le impostazioni del portale seguendo questa procedura:
  - Accedere al [portale di Azure](https://portal.azure.com).
  - Fare clic sull'icona *Impostazioni* ![icona delle impostazioni di Azure](media/storage-import-export-delete-personal-info/azure-settings-icon.png)
  - Fare clic su *Esporta tutte le impostazioni* (per salvare le impostazioni correnti in un file `.json`).
  - Fare clic su *Elimina tutte le impostazioni e i dashboard privati* per eliminare tutte le impostazioni, incluse le informazioni di contatto salvate.

Per altre informazioni, consultare l'Informativa sulla privacy Microsoft nel [Centro protezione](https://www.microsoft.com/trustcenter).