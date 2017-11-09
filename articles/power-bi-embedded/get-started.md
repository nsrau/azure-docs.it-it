---
title: Introduzione a Microsoft Power BI Embedded | Microsoft Docs
description: Power BI Embedded nell'applicazione di Business Intelligence
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 4787cf44-5d1c-4bc3-b3fd-bf396e5c1176
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: b32b06e9d6cbaacdfbdfe92e2c72cb6763c9eb52
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-microsoft-power-bi-embedded"></a>Introduzione a Microsoft Power BI Embedded

**Power BI Embedded** consente ai fornitori di software indipendenti (Independent Software Vendor) e agli sviluppatori di aggiungere in modo rapido oggetti visivi, report e dashboard accattivanti alle applicazioni tramite un modello basato su capacità con modello a consumo orario.

![Diagramma dell'incorporamento di un flusso](media/get-started/introduction.png)

Power BI Embedded offre vantaggi a un fornitore di software indipendente e ai rispettivi sviluppatori e clienti. Un fornitore di software indipendente può iniziare a creare gratuitamente oggetti visivi con Power BI Desktop. I fornitori di software indipendenti possono ottenere un time-to-market rapido riducendo al minimo il lavoro richiesto per lo sviluppo di oggetti visivi analitici e possono distinguersi dalla concorrenza grazie a esperienze dati differenziate. I fornitori di software indipendenti possono anche scegliere di applicare un addebito aggiuntivo per il valore aggiunto creato con le analisi incorporate.

Gli sviluppatori possono concentrarsi sulla creazione delle competenze principali delle applicazioni, invece di dedicare tempo allo sviluppo di oggetti visivi e di analisi. Gli sviluppatori possono soddisfare rapidamente le richieste dei clienti relative a report e dashboard e possono eseguire con facilità l'incorporamento con API e SDK completamente documentati. Abilitando l'esplorazione semplificata dei dati nelle app, i fornitori di software indipendenti consentono ai clienti di prendere in tutta sicurezza decisioni rapide basate sui dati e contestualizzate da qualsiasi dispositivo.

## <a name="register-an-application-within-azure-active-directory"></a>Registrare un'applicazione in Azure Active Directory

Per l'incorporamento di un'applicazione personalizzata è necessaria un'applicazione registrata in Azure Active Directory (AAD). L'applicazione registrata richiede che il tenant sia un tenant di Power BI, ovvero è necessario che almeno un utente dell'organizzazione si sia iscritto a Power BI. L'iscrizione di un utente a Power BI consentirà la visualizzazione delle API di Power BI entro l'applicazione registrata.

Per altre informazioni su come registrare un'applicazione in AAD, vedere [Registrare un'app di Azure AD per incorporare il contenuto di Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/).

## <a name="embed-content-in-your-application"></a>Incorporare contenuto nell'applicazione

Dopo avere registrato l'applicazione in AAD, incorporare il contenuto di Power BI nell'applicazione. Incorporare il contenuto usando l'API REST insieme alle API JavaScript.

Sono disponibili esempi introduttivi. Per una procedura dettagliata relativa all'esempio, vedere [Integrare un dashboard, un riquadro o un report nell'applicazione](https://powerbi.microsoft.com/documentation/powerbi-developer-embed-sample-app-owns-data/).

## <a name="get-capacity-and-move-to-production"></a>Ottenere capacità e passare in produzione

Creare capacità di Power BI Embedded in Microsoft Azure per spostare l'applicazione in produzione. Per informazioni su come creare capacità, vedere [Create Power BI Embedded capacity in the Azure portal](create-capacity.md) (Creare capacità di Power BI Embedded nel portale di Azure).

Gestire la capacità nel portale di amministrazione di Power BI. Assegnare un assegnatore di area di lavoro per assistenza con le aree di lavoro delle app. Per altre informazioni, vedere [Gestire capacità all'interno di Power BI Premium e Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-admin-premium-manage/).

## <a name="next-steps"></a>Passaggi successivi

Se si è pronti per creare capacità di Power BI Embedded, vedere [Create Power BI Embedded capacity in the Azure portal](create-capacity.md) (Creare capacità di Power BI Embedded nel portale di Azure).

Per una procedura dettagliata di esempio, vedere [Integrare un dashboard, un riquadro o un report nell'applicazione](https://powerbi.microsoft.com/documentation/powerbi-developer-embed-sample-app-owns-data/).

Altre domande? [Contattare la community di Power BI](http://community.powerbi.com/)