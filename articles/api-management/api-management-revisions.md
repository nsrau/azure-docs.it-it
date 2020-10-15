---
title: Revisioni in gestione API di Azure | Microsoft Docs
description: Informazioni sul concetto di revisioni in gestione API di Azure.
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 06/12/2020
ms.author: jodowns
ms.custom: fasttrack-new
ms.openlocfilehash: 9a20a7966daff372cf5c0abc9b7b1dbbfd459838
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91403239"
---
# <a name="revisions-in-azure-api-management"></a>Revisioni in gestione API di Azure

Le revisioni consentono di apportare modifiche alle API in modo controllato e sicuro. Quando si desidera apportare modifiche, creare una nuova revisione. È quindi possibile modificare e testare l'API senza compromettere i consumer dell'API. Quando si è pronti, si renderà aggiornata la revisione. Allo stesso tempo, è possibile pubblicare una voce nel log delle modifiche, in modo che i consumer dell'API siano aggiornati con le modifiche apportate. Il log delle modifiche viene pubblicato nel portale per sviluppatori.

> [!NOTE]
> Il portale per sviluppatori non è disponibile al livello A consumo.

Con le revisioni è possibile:

- Apportare modifiche in modo sicuro alle definizioni e ai criteri dell'API senza compromettere l'API di produzione.
- Provare le modifiche prima di pubblicarle.
- Documentare le modifiche apportate, in modo che gli sviluppatori possano comprendere le novità.
- Eseguire il rollback se si riscontrano problemi.

[Per iniziare a usare le revisioni, seguire questa procedura dettagliata.](./api-management-get-started-revise-api.md)

## <a name="accessing-specific-revisions"></a>Accesso a revisioni specifiche

È possibile accedere a ogni revisione dell'API usando un URL appositamente formato. Aggiungere `;rev={revisionNumber}` alla fine dell'URL dell'API, ma prima della stringa di query, per accedere a una revisione specifica dell'API. Ad esempio, è possibile usare questo URL per accedere alla revisione 3 dell' `customers` API:

`https://apis.contoso.com/customers;rev=3?customerId=123`

Per impostazione predefinita, ogni revisione ha le stesse impostazioni di sicurezza della revisione corrente. È possibile modificare intenzionalmente i criteri per una revisione specifica se si desidera applicare una sicurezza diversa per ogni revisione. Ad esempio, potrebbe essere necessario aggiungere un [criterio di filtro IP](./api-management-access-restriction-policies.md#RestrictCallerIPs) per impedire ai chiamanti esterni di accedere a una revisione ancora in fase di sviluppo.

Una revisione può essere portata offline, rendendola inaccessibile ai chiamanti anche se tentano di accedere alla revisione tramite il relativo URL. È possibile contrassegnare una revisione come offline usando il portale di Azure. Se si usa PowerShell, è possibile usare il `Set-AzApiManagementApiRevision` cmdlet e impostare l' `Path` argomento su `$null` .

> [!NOTE]
> È consigliabile portare offline le revisioni quando non vengono usate per il test.

## <a name="current-revision"></a>Revisione corrente

Una singola revisione può essere impostata come Revisione *corrente* . Questa revisione sarà quella usata per tutte le richieste API che non specificano un numero di revisione esplicito nell'URL. È possibile eseguire il rollback a una revisione precedente impostando la revisione come corrente.

È possibile impostare una revisione come Current usando il portale di Azure. Se si usa PowerShell, è possibile usare il `New-AzApiManagementApiRelease` cmdlet.

## <a name="revision-descriptions"></a>Descrizioni delle revisioni

Quando si crea una revisione, è possibile impostare una descrizione per i propri scopi di rilevamento. Le descrizioni non vengono riprodotte agli utenti dell'API.

Quando si imposta una revisione come aggiornata, è anche possibile specificare facoltativamente una nota di log delle modifiche pubblica. Il log delle modifiche è incluso nel portale per sviluppatori per la visualizzazione da parte degli utenti dell'API. È possibile modificare la nota del log delle modifiche usando il `Update-AzApiManagementApiRelease` cmdlet di PowerShell.

## <a name="versions-and-revisions"></a>Versioni e revisioni

Le versioni e le revisioni sono funzioni distinte. Ogni versione può avere più revisioni, proprio come un'API senza versioni. È possibile usare le revisioni senza usare versioni o viceversa. In genere le versioni vengono usate per separare le versioni dell'API con modifiche di rilievo, mentre le revisioni possono essere usate per modifiche secondarie e non di rilievo a un'API.

Se si ritiene che la revisione abbia modifiche di rilievo o se si desidera trasformare in modo formale la revisione in una versione beta/di test, è possibile creare una versione da una revisione. Utilizzando la portale di Azure, fare clic su "Crea versione da revisione" nel menu di scelta rapida revisione della scheda revisioni.
