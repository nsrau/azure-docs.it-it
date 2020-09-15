---
title: Trasferire le sottoscrizioni di Azure da un partner a un altro (anteprima)
description: Questo articolo contiene le informazioni da sapere prima e dopo aver trasferito la proprietà di fatturazione della sottoscrizione di Azure.
author: bandersmsft
ms.reviewer: mcville
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: banders
ms.openlocfilehash: 246128ec3d26e4bf3f2a5a8f0660aab7d9daca3d
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/08/2020
ms.locfileid: "89554203"
---
# <a name="transfer-azure-subscriptions-from-one-partner-to-another-preview"></a>Trasferire le sottoscrizioni di Azure da un partner a un altro (anteprima)

Questo articolo contiene le informazioni da sapere prima e dopo aver trasferito la proprietà di fatturazione della sottoscrizione di Azure. Per avviare un trasferimento della sottoscrizione di Azure in un piano di Azure da un partner Microsoft a un altro, è necessario contattare il partner. Il partner invierà le istruzioni per iniziare. Al termine del processo di trasferimento, la proprietà di fatturazione della sottoscrizione viene cambiata.

## <a name="user-access"></a>Accesso utente

La transizione non influisce sull'accesso per utenti, gruppi o entità servizio esistenti assegnati con il controllo degli accessi in base al ruolo di Azure. Il [controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md) consente di gestire chi può accedere alle risorse di Azure, le operazioni che si possono eseguire e le aree accessibili. Con il trasferimento della sottoscrizione, al nuovo partner non viene assegnato l'accesso alle risorse con controllo degli accessi in base al ruolo. Il partner precedente mantiene il proprio accesso con controllo degli accessi in base al ruolo.

Di conseguenza, è importante rimuovere l'accesso con controllo degli accessi in base al ruolo di Azure per il vecchio partner e aggiungere l'accesso per quello nuovo. Per altre informazioni sull'assegnazione dell'accesso al nuovo partner, vedere [Che cos'è il controllo degli accessi in base al ruolo di Azure?](../../role-based-access-control/overview.md) Per altre informazioni sulla rimozione dell'accesso con controllo degli accessi in base al ruolo del partner precedente, vedere [Rimuovere un assegnazione di ruolo](../../role-based-access-control/role-assignments-portal.md#remove-a-role-assignment).

Inoltre, il nuovo partner non ottiene automaticamente l'accesso [Admin on Behalf Of (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) alle sottoscrizioni. L'accesso AOBO è necessario per il partner per gestire le sottoscrizioni di Azure per proprio conto. Per altre informazioni su come concedere l'accesso AOBO al nuovo partner, vedere [Trasferire la proprietà di fatturazione di un account della sottoscrizione di Azure a un altro account](billing-subscription-transfer.md).

## <a name="stop-a-transfer"></a>Interrompere un trasferimento

Dopo aver ricevuto la conferma che è stata inviata una richiesta di trasferimento, è possibile usare una delle opzioni seguenti **se non si vuole che il trasferimento continui**.

- Se la richiesta non è stata ancora accettata dal partner corrente, è possibile chiedere al nuovo partner di annullare la richiesta di trasferimento avviata (quando lo stato è in sospeso).
- Chiedere al partner corrente di non eseguire alcuna azione quando riceve la richiesta di trasferimento. Senza il consenso del partner corrente, il trasferimento non può continuare. La richiesta scadrà.
- È possibile _rimuovere la relazione di rivenditore_ con il nuovo partner. Questa azione rimuove la possibilità di spostare la sottoscrizione. Annulla effettivamente la richiesta.

È anche possibile chiedere assistenza, segnalare comportamenti non consentiti o attività sospette usando una delle opzioni disponibili nel sito Web di [risorse legali Microsoft](https://www.microsoft.com/legal/). L'opzione per segnalare un problema è disponibile nella sezione Compliance & ethics (Conformità ed etica).

## <a name="next-steps"></a>Passaggi successivi

- Per assegnare al nuovo partner l'accesso con controllo degli accessi in base al ruolo, vedere [Che cos'è il controllo degli accessi in base al ruolo di Azure?](../../role-based-access-control/overview.md)
- Per assegnare al nuovo partner l'accesso AOBO, vedere [Trasferire la proprietà di fatturazione di un account della sottoscrizione di Azure a un altro account](billing-subscription-transfer.md).