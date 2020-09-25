---
title: Prerequisiti per le API-Azure Marketplace
description: Prerequisiti per l'uso delle API portale Cloud Partner.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 09/23/2020
ms.openlocfilehash: 374cc3398d1037b19ceddcbdaafd333f643e44ff
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91261159"
---
# <a name="api-prerequisites"></a>Prerequisiti per API

> [!NOTE]
> Le API portale Cloud Partner sono integrate con e continueranno a funzionare nel centro per i partner. La transizione introduce piccole modifiche. Esaminare le modifiche elencate in [portale cloud partner riferimento API](cloud-partner-portal-api-overview.md) per assicurarsi che il codice continui a funzionare dopo la transizione al centro per i partner. Usare solo le API CPP per i prodotti esistenti già integrati prima della transizione al centro per i partner; i nuovi prodotti devono usare le API di invio del centro per i partner.

Per usare le API portale Cloud Partner sono necessarie due risorse programmatiche, ovvero un'entità servizio e un token di accesso Azure Active Directory (Azure AD).

## <a name="create-service-principal-in-azure-active-directory-tenant"></a>Creare un'entità servizio nel tenant Azure Active Directory

In primo luogo, creare un'entità servizio nel tenant di Azure AD. A questo tenant verrà assegnato un proprio set di autorizzazioni nel portale Cloud Partner. Il codice chiamerà le API usando questo tenant anziché le credenziali personali. Per una spiegazione completa della creazione di un'entità servizio, vedere [procedura: usare il portale per creare un'applicazione Azure ad e un'entità servizio che possano accedere alle risorse](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="add-service-principal-to-your-account"></a>Aggiungere un'entità servizio all'account

Ora che è stata creata l'entità servizio nel tenant, è possibile aggiungerla come utente per l'account del portale del centro per i partner. Esattamente come un utente, l'entità servizio può agire come proprietario o come collaboratore del portale. Per informazioni dettagliate, vedere i **passaggi successivi** .

## <a name="next-steps"></a>Passaggi successivi

Vedere [gestire le applicazioni Azure ad](partner-center-portal/manage-account.md#manage-azure-ad-applications).
