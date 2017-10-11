---
title: Problema durante la creazione di un'applicazione Proxy di applicazione | Microsoft Docs
description: Come risolvere i problemi di creazione di applicazioni Proxy di applicazione nel portale di amministrazione di Azure AD
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: fe56f56162ba7186f1b660a5b44fcef38f1dee9d
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="problem-creating-an-application-proxy-application"></a>Problema durante la creazione di un'applicazione Proxy di applicazione 

Di seguito vengono riportati alcuni dei problemi comuni relativi alla creazione di una nuova applicazione Proxy di applicazione.

## <a name="recommended-documents"></a>Documenti consigliati 

Per altre informazioni sulla creazione di un'applicazione Proxy di applicazione tramite il portale di amministrazione, vedere [Pubblicare applicazioni mediante il proxy di applicazione AD Azure](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal).

Se si seguono i passaggi in tale documento e si riceve un errore durante la creazione dell'applicazione, vedere i dettagli dell'errore per informazioni e suggerimenti per la correzione dell'applicazione. La maggior parte dei messaggi di errore include una correzione suggerita. 

## <a name="specific-things-to-check"></a>Elementi specifici da controllare

Per evitare errori comuni, verificare quanto segue:

-   Si dispone del ruolo di amministratore con l'autorizzazione a creare un'applicazione proxy dell'applicazione

-   L'URL interno è univoco

-   L'URL esterno è univoco

-   Gli URL iniziano con http o https e terminano con un carattere "/"

-   L'URL deve essere un nome di dominio e non un indirizzo IP

Quando si crea l'applicazione, il messaggio di errore dovrebbe essere visualizzato in alto a destra. È anche possibile selezionare l'icona di notifica per visualizzare i messaggi di errore.

   ![Prompt di notifica](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>Passaggi successivi
[Abilitare il proxy di applicazione nel portale di Azure](active-directory-application-proxy-enable.md)
