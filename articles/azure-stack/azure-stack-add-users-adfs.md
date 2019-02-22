---
title: Aggiungere gli utenti per Azure Stack di ad FS | Microsoft Docs
description: Informazioni su come aggiungere gli utenti per le distribuzioni di ad FS di Azure Stack
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: 1b47739200c79317273ea0c788f21a7ee4a3b818
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56648507"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>Aggiungere utenti di Azure Stack in AD FS
È possibile usare la **Active Directory Users and Computers** snap-in per aggiungere altri utenti a un ambiente Azure Stack usando AD FS come provider di identità.

## <a name="add-windows-server-active-directory-users"></a>Aggiungere gli utenti di Windows Server Active Directory
> [!TIP]
> Questo esempio Usa impostazione predefinita con azurestack. Local ASDK active directory. 

1. Accedere a un computer con un account che fornisce accesso agli strumenti di amministrazione di Windows e aprire una nuova Microsoft Management Console (MMC).
2. Selezionare **File > Aggiungi o Rimuovi snap-in**.
3. Selezionare **Active Directory Users and Computers** > **con azurestack. Local** > **utenti**.
4. Selezionare **azione** > **nuova** > **utente**.
5. Nel nuovo oggetto utente, fornire dettagli sull'utente. Selezionare **Avanti**.
6. Fornire e confermare una password.
7. Selezionare **successivo** per finalizzare i valori. Selezionare **fine** per creare l'utente.


## <a name="next-steps"></a>Passaggi successivi
[Creare entità servizio](azure-stack-create-service-principals.md)