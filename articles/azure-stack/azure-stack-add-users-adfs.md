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
ms.date: 10/15/2018
ms.author: patricka
ms.reviewer: unknown
ms.openlocfilehash: ee571ec14a93653b524401d1d304021178ecd794
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2019
ms.locfileid: "54120813"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>Aggiungere utenti di Azure Stack in AD FS
È possibile usare la **Active Directory Users and Computers** snap-in per aggiungere altri utenti a un ambiente Azure Stack usando AD FS come provider di identità.

## <a name="add-windows-server-active-directory-users"></a>Aggiungere gli utenti di Windows Server Active Directory
> [!TIP]
> Questo esempio Usa impostazione predefinita con azurestack. Local ASDK active directory. 

1.  Accedere a un computer con un account che fornisce accesso agli strumenti di amministrazione di Windows e aprire una nuova Microsoft Management Console (MMC).
2.  Fare clic su **File > Aggiungi o Rimuovi snap-in**.
3.  Selezionare **Active Directory Users and Computers** > **con azurestack. Local** > **utenti**.
4.  Fare clic su **azione** > **nuova** > **utente**.
5.  Nuovo oggetto-finestra dell'utente, fornire e confermare una password
6.  Fare clic su **successivo** finalizzare i valori e fare clic su Fine per creare l'utente.


## <a name="next-steps"></a>Passaggi successivi
[Creare entità servizio](azure-stack-create-service-principals.md)