---
title: Collaborare con altri utenti QnA Maker
description: ''
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/15/2020
ms.openlocfilehash: ecf81152605d77e1036c9fbf5d3308af2b1fb988
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96346156"
---
# <a name="collaborate-with-other-authors-and-editors"></a>Collaborare con altri autori ed editor

Collaborare con altri autori ed editor usando il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) inserito nella risorsa QnA Maker.

## <a name="access-is-provided-on-the-qna-maker-resource"></a>L'accesso viene fornito sulla risorsa QnA Maker

Tutte le autorizzazioni sono controllate dalle autorizzazioni posizionate nella risorsa QnA Maker. Queste autorizzazioni sono allineate a lettura, scrittura, pubblicazione e accesso completo.

Questa funzionalità RBAC di Azure include:
* Azure Active Directory (AAD) è compatibile con le versioni precedenti del 100% con l'autenticazione basata su chiavi per proprietari e collaboratori. Nelle richieste, i clienti possono usare l'autenticazione basata su chiavi o l'autenticazione basata su RBAC di Azure.
* È possibile aggiungere rapidamente autori ed editor a tutte le Knowledge base nella risorsa perché il controllo si trova a livello di risorsa, non a livello di Knowledge base.

## <a name="access-is-provided-by-a-defined-role"></a>L'accesso viene fornito da un ruolo definito

[!INCLUDE [Azure RBAC permissions table](../includes/role-based-access-control.md)]

## <a name="authentication-flow"></a>Flusso di autenticazione

Il diagramma seguente illustra il flusso, dal punto di vista dell'autore, per accedere al portale di QnA Maker e usare le API di creazione.

> [!div class="mx-imgBorder"]
> ![Il diagramma seguente illustra il flusso, dal punto di vista dell'autore, per accedere al portale di QnA Maker e usare le API di creazione.](../media/qnamaker-how-to-collaborate-knowledge-base/rbac-flow-from-portal-to-service.png)

|Passaggi|Descrizione|
|--|--|
|1|Il portale acquisisce il token per QnA Maker risorsa.|
|2|Il portale chiama l'API di creazione QnA Maker appropriata (gestione API) passando il token anziché le chiavi.|
|3|API QnA Maker convalida il token.|
|4 |API QnA Maker chiama il servizio QnAMaker.|

Se si intende chiamare le [API di creazione](../index.yml), vedere altre informazioni su come configurare l'autenticazione.

## <a name="authenticate-by-qna-maker-portal"></a>Eseguire l'autenticazione tramite il portale di QnA Maker

Se si crea e si collabora usando il portale di QnA Maker, dopo aver [aggiunto il ruolo appropriato alla risorsa per un collaboratore](../index.yml), il portale di QnA Maker gestisce tutte le autorizzazioni di accesso.

## <a name="authenticate-by-qna-maker-apis-and-sdks"></a>Eseguire l'autenticazione con QnA Maker API e SDK

Se si creano e collaborano usando le API, tramite REST o gli SDK, è necessario [creare un'entità servizio](../../authentication.md#assign-a-role-to-a-service-principal) per gestire l'autenticazione.

## <a name="next-step"></a>Passaggio successivo

* Progettare una Knowledge base per le [lingue](../index.yml) e per [le applicazioni client](../index.yml)