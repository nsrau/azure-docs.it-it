---
title: Assegnazione di gruppi alle app Azure AD | Documentazione Microsoft
description: Come implementare l&quot;assegnazione di gruppo per le applicazioni Azure.
services: active-directory
documentationcenter: 
author: IHenkel
manager: femila
editor: 
ms.assetid: 29b5ba89-a1c7-4f1f-a294-248a40106617
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: inhenk
translationtype: Human Translation
ms.sourcegitcommit: e66b606433f8924a509f2d04dae67ff00ded6dca
ms.openlocfilehash: 81b9b66bb16e183b8c3c206a4a49b2f4845bc201


---
# <a name="azure-ad-and-applications-assign-groups-to-an-application"></a>Azure AD e applicazioni: assegnare gruppi a un'applicazione
Prima di assegnare utenti e gruppi a un'applicazione, è necessario richiedere l'assegnazione dell’utente. Per informazioni su come richiedere l'assegnazione dell'utente, vedere l'articolo [Richiedere l'assegnazione utente](active-directory-applications-guiding-developers-requiring-user-assignment.md) .

In questo articolo si presuppone che siano già stati creati gruppi nella active directory che si utilizza per questa applicazione.

## <a name="assigning-groups-to-an-application"></a>Assegnazione dei gruppi a un'applicazione
1. Accedere al portale di Azure con un account amministratore.
2. Fare clic sulla voce **Tutti gli elementi** del menu principale.
3. Scegliere la directory utilizzata per l'applicazione.
4. Fare clic sulla scheda **APPLICAZIONI** .
5. Selezionare l'applicazione dall'elenco di applicazioni associate a questa directory.
6. Fare clic sulla scheda **UTENTI E GRUPPI** .
7. Filtrare l'elenco dei gruppi nella active directory utilizzando l’elenco a discesa **Gruppi** .
8. Selezionare il gruppo.
9. Fare clic su **ASSEGNARE**.
10. Fare clic su **sì** quando richiesto.

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]



<!--HONumber=Feb17_HO1-->


