---
title: File di inclusione
description: File di inclusione
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: e971af5d415ea788f97ab11b58541e629c7eb0ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76694588"
---
## <a name="lifecycle"></a>Ciclo di vita

Nella scheda **Ciclo di vita** è possibile specificare la scadenza dell'assegnazione di un utente al pacchetto di accesso. È inoltre possibile specificare se gli utenti possono estendere le assegnazioni.

1. Nella sezione **Scadenza** **impostare Le assegnazioni** dei pacchetti di Access scadono su **Data**, **Numero di giorni**o **Mai**.

    Per **Data**, selezionare una data di scadenza futura.

    Per **Numero di giorni**, specificare un numero compreso tra 0 e 3660 giorni.

    In base alla selezione effettuata, l'assegnazione di un utente al pacchetto di accesso scade in una determinata data, in un determinato numero di giorni dopo l'approvazione o mai.

1. Fare clic su **Mostra impostazioni di scadenza avanzate** per visualizzare altre impostazioni.

    ![Pacchetto di Access - Impostazioni di scadenza del ciclo di vita](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. Per consentire all'utente di estendere le assegnazioni, impostare **Consenti agli utenti di estendere l'accesso** su **Sì**.

    Se le estensioni sono consentite nei criteri, l'utente riceverà un messaggio di posta elettronica 14 giorni e anche 1 giorno prima che l'assegnazione del pacchetto di accesso sia impostata in modo da scadere richiedendogli di estendere l'assegnazione. Se l'utente invia una richiesta di estensione dell'accesso, la data di estensione deve trovarsi in corrispondenza o prima della scadenza delle assegnazioni, come definito nei criteri utilizzati per concedere all'utente l'accesso al pacchetto di accesso. Ad esempio, se il criterio indica che le assegnazioni sono impostate per scadere il 30 giugno, l'estensione massima che un utente può richiedere è il 30 giugno.

    Se l'accesso di un utente viene esteso, non sarà in grado di richiedere il pacchetto di accesso dopo la data di estensione specificata (data impostata nel fuso orario dell'utente che ha creato il criterio).

1. Per richiedere l'approvazione per concedere un'estensione, **impostare Richiedi approvazione per concedere l'estensione** su **Sì**.

    Verranno utilizzate le stesse impostazioni di approvazione specificate nella scheda **Richieste.**

1. Fare clic su **Avanti** o **Aggiorna**.
