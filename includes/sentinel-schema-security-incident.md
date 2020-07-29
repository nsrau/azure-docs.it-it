---
title: includere il file
description: File di inclusione
services: azure-sentinel
author: yelevin
ms.service: azure-sentinel
ms.topic: include
ms.date: 06/28/2020
ms.author: yelevin
ms.custom: include file
ms.openlocfilehash: 76020b3c1f28e5b5f6363aef181b76bc93a9613e
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294241"
---
### <a name="the-data-model-of-the-schema"></a>Modello di dati dello schema

| Campo | Tipo di dati | Descrizione |
| ---- | ---- | ---- |
| **AdditionalData** | dinamico | Numero di avvisi, numero di segnalibri, numero di commenti, nomi dei prodotti avvisi e tattiche |
| **AlertIds** | dinamico | Avvisi da cui è stato creato l'evento imprevisto |
| **BookmarkIds** | dinamico | Entità con segnalibro |
| **Classificazione** | Stringa | Classificazione di chiusura evento imprevisto |
| **ClassificationComment** | Stringa | Commento sulla classificazione di chiusura degli eventi imprevisti |
| **ClassificationReason** | Stringa | Motivo della classificazione di chiusura degli eventi imprevisti |
| **ClosureTime** | Datetime | Timestamp (UTC) dell'ultima chiusura dell'evento imprevisto |
| **Commenti** | dinamico | Commenti evento imprevisto |
| **CreatedTime** | Datetime | Timestamp (UTC) del momento in cui è stato creato l'evento imprevisto |
| **Descrizione** | Stringa | Descrizione evento imprevisto |
| **FirstActivityTime** | Datetime | Ora primo evento |
| **FirstModifiedTime** | Datetime | Timestamp (UTC) di quando l'evento imprevisto è stato modificato per la prima volta |
| **IncidentName** | Stringa | GUID interno |
| **IncidentNumber** | INT |  |
| **IncidentUrl** | Stringa | Collegamento a evento imprevisto |
| **Etichette** | dinamico | Tag |
| **LastActivityTime** | Datetime | Ora ultimo evento |
| **LastModifiedTime** | Datetime | Timestamp (UTC) dell'Ultima modifica dell'evento imprevisto <br>(la modifica descritta dal record corrente) |
| **ModifiedBy** | Stringa | Utente o sistema che ha modificato l'evento imprevisto |
| **Proprietario** | dinamico |  |
| **RelatedAnalyticRuleIds** | dinamico | Regole da cui sono stati attivati gli avvisi dell'evento imprevisto |
| **Gravità** | Stringa | Gravità dell'evento imprevisto (alta/media/bassa/informativa) |
| **SourceSystem** | Stringa | Costante (' Azure ') |
| **Status** | Stringa |  |
| **TenantId** | Stringa |  |
| **TimeGenerated** | Datetime | Timestamp (UTC) del momento in cui è stato creato il record corrente <br>(dopo la modifica dell'evento imprevisto) |
| **Title** | Stringa | 
| **Tipo** | Stringa | Constant (' SecurityIncident ') |
|
