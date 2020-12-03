---
title: File di inclusione
description: File di inclusione
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 55b545471cbe45fe35e28879270e4340cf9d3834
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027331"
---
Durante la distribuzione delle risorse in Azure, è possibile avere una notevole flessibilità al momento di decidere quali tipi di risorse distribuire, dove posizionarle e come configurarle. Tuttavia, tale flessibilità potrebbe rendere disponibili più opzioni rispetto a quelle che devono essere consentite nell'organizzazione. Quando si valuta la distribuzione delle risorse in Azure, ci si potrebbe chiedere:

* Come soddisfare i requisiti legali per la sovranità dei dati in determinati paesi?
* Come controllare i costi?
* Come garantire che un utente non apporti inavvertitamente modifiche a un sistema critico?
* Come tenere traccia dei costi delle risorse e fatturarli in modo accurato?

In questo articolo vengono illustrate le risposte a tali domande. In particolare, verrà descritto come:

> [!div class="checklist"]
> * Assegnare utenti ai ruoli e assegnare i ruoli a un ambito in modo che gli utenti dispongano dell'autorizzazione per eseguire le azioni previste, ma non altre azioni.
> * Applicare criteri che definiscono convenzioni per le risorse nella sottoscrizione.
> * Bloccare le risorse di importanza critica per il sistema.
> * Aggiungere tag alle risorse in modo da tenerne traccia tramite valori significativi per l'organizzazione.

Questo articolo è incentrato sulle attività da eseguire per implementare la governance. Per una discussione più ampia dei concetti, vedere [Governance in Azure](../articles/governance/index.yml).