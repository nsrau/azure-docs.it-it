---
title: File di inclusione
description: File di inclusione
services: azure-sentinel
author: yelevin
ms.service: azure-sentinel
ms.topic: include
ms.date: 06/28/2020
ms.author: yelevin
ms.custom: include file
ms.openlocfilehash: 63cb53dc60a718892d4bf86140e7fd51303bd61c
ms.sourcegitcommit: f1b18ade73082f12fa8f62f913255a7d3a7e42d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/24/2020
ms.locfileid: "88761721"
---
### <a name="the-data-model-of-the-schema"></a>Modello di dati dello schema

| Campo | Tipo di dati | Descrizione |
| ---- | ---- | ---- |
| **AdditionalData** | dinamico | Numero di avvisi, numero di segnalibri, numero di commenti, nomi dei prodotti avvisi e tattiche |
| **AlertIds** | dinamico | Avvisi da cui è stato creato l'evento imprevisto |
| **BookmarkIds** | dinamico | Entità con segnalibro |
| **Classificazione** | string | Classificazione di chiusura evento imprevisto |
| **ClassificationComment** | string | Commento sulla classificazione di chiusura degli eventi imprevisti |
| **ClassificationReason** | string | Motivo della classificazione di chiusura degli eventi imprevisti |
| **ClosedTime** | Datetime | Timestamp (UTC) dell'ultima chiusura dell'evento imprevisto |
| **Commenti** | dinamico | Commenti evento imprevisto |
| **CreatedTime** | Datetime | Timestamp (UTC) del momento in cui è stato creato l'evento imprevisto |
| **Descrizione** | string | Descrizione evento imprevisto |
| **FirstActivityTime** | Datetime | Ora primo evento |
| **FirstModifiedTime** | Datetime | Timestamp (UTC) di quando l'evento imprevisto è stato modificato per la prima volta |
| **IncidentName** | string | GUID interno |
| **IncidentNumber** | INT |  |
| **IncidentUrl** | string | Collegamento a evento imprevisto |
| **Etichette** | dinamico | Tag |
| **LastActivityTime** | Datetime | Ora ultimo evento |
| **LastModifiedTime** | Datetime | Timestamp (UTC) dell'Ultima modifica dell'evento imprevisto <br>(la modifica descritta dal record corrente) |
| **ModifiedBy** | string | Utente o sistema che ha modificato l'evento imprevisto |
| **Proprietario** | dinamico |  |
| **RelatedAnalyticRuleIds** | dinamico | Regole da cui sono stati attivati gli avvisi dell'evento imprevisto |
| **Gravità** | string | Gravità dell'evento imprevisto (alta/media/bassa/informativa) |
| **SourceSystem** | string | Costante (' Azure ') |
| **Status** | string |  |
| **TenantId** | string |  |
| **TimeGenerated** | Datetime | Timestamp (UTC) del momento in cui è stato creato il record corrente <br>(dopo la modifica dell'evento imprevisto) |
| **Title** | string | 
| **Tipo** | string | Constant (' SecurityIncident ') |
|
