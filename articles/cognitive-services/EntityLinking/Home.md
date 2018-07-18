---
title: API Servizio di intelligence Entity Linking | Microsoft Docs
description: Informazioni su come usare l'API Servizio di intelligence Entity Linking per le attività di collegamento delle entità in Servizi cognitivi.
services: cognitive-services
author: DavidLiCIG
manager: wkwok
ms.service: cognitive-services
ms.component: entity-linking-intelligence
ms.topic: article
ms.date: 03/28/2016
ms.author: davl
ms.openlocfilehash: 662295c42a421fe76892e6e5aac7b7e7178d6155
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373004"
---
# <a name="entity-linking-intelligence-service-api"></a>API Servizio di intelligence Entity Linking

Introduzione a Servizio di intelligence Entity Linking di Microsoft, un servizio Web creato per consentire agli sviluppatori di eseguire attività relative al collegamento delle entità.

## <a name="entity-linking"></a>Collegamento delle entità

In alcuni casi è possibile che in contesti diversi una parola venga usata come entità denominata, verbo o un'altra parola all'interno di una frase specifica. Ad esempio, nel caso in cui "times" sia un'entità denominata, è possibile che faccia riferimento a due entità separatamente distinguibili, ad esempio "The New York Times" o "Times Square". Se si specifica un determinato paragrafo in un documento, Servizio di intelligence Entity Linking riconoscerà e identificherà ogni entità separata in base al contesto.  

La figura seguente illustra un esempio di collegamento di entità. In particolare, se si usa Wikipedia, Servizio di intelligence Entity Linking rileva tutte le entità nominate nel testo di input e le collega ai relativi punti di riferimento in base all'ID della pagina.

 ![Esempio di collegamento di entità per Marte](./Images/EntityLinkingSample1.png)
 
## <a name="get-started"></a>Attività iniziali
 
Per esplorare rapidamente le funzionalità di base e il processo di sottoscrizione di Entity Linking, fare riferimento all'esercitazione introduttiva.
[Introduzione all'API Entity Linking in C#](GettingStarted.md)


