---
title: Cloud intelligente correlazione degli avvisi nel Centro sicurezza di Azure (eventi imprevisti) | Microsoft Docs
description: Questo argomento viene illustrato come sono di fusion utilizza cloud intelligente correlazione degli avvisi per generare eventi imprevisti della sicurezza nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 2ab4dab8cb7729b0c2ca023f22066f7b5d166a02
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571777"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Cloud intelligente correlazione degli avvisi nel Centro sicurezza di Azure (eventi imprevisti)

Il Centro sicurezza analizza continuamente i carichi di lavoro cloud ibridi usando analitica avanzate e intelligence per le minacce per ricevere un avviso sulle attività dannose.

Come aumenta la portata del code coverage delle minacce e la necessità di rilevare anche l'indicazione niente di compromesso aumenta, può essere difficile per gli analisti della sicurezza valutare i diversi avvisi e identificazione di un attacco effettivo. Il Centro sicurezza consente agli analisti affrontano la fatica di avviso e la diagnosi di attacchi appena si verificano, grazie alla correlazione tra diversi avvisi e i segnali di bassa fedeltà in eventi imprevisti di sicurezza.

Fusion è la tecnologia e analitiche back-end su cui si basa il Centro sicurezza gli eventi imprevisti in modo da poter correlare diversi avvisi e contestuali segnali insieme. Fusione works esaminando i segnali diversi segnalati per una sottoscrizione tra le risorse e trovare i modelli più comuni che mostra l'avanzamento di attacco o segnali con le informazioni contestuali condivise che indica una procedure di risposta unificata deve essere creato per essi.

Analitica Fusion combina informazioni di dominio di sicurezza con l'intelligenza artificiale per analizzare gli avvisi, l'individuazione di nuovi modelli di attacco appena si verificano. 

Matrice di attacco MITRE per associare gli avvisi con il loro scopo percepita, il Centro sicurezza Usa aiutando formalizzare informazioni di dominio di sicurezza. Inoltre, utilizzando le informazioni raccolte per ogni passaggio di un attacco, il Centro sicurezza può escludere attività che viene visualizzato come passaggi di un attacco, ma non è.  

Poiché gli attacchi spesso si verificano su tenant diversi, il Centro sicurezza può combinare gli algoritmi di intelligenza artificiale per analizzare le sequenze di attacco che vengono segnalate per ogni sottoscrizione di identificarli come prevalenti modelli avviso anziché solo incidentalmente da associare a ogni Altro.

Durante l'analisi di un evento imprevisto, gli analisti devono spesso un contesto aggiuntivo per raggiungere un verdetto sulla natura della minaccia e su come semplificarla. Ad esempio, anche quando viene rilevata un'anomalia di rete, senza conoscere cos'altro sta accadendo in rete o in relazione alla risorsa interessata è difficile comprendere quali azioni da intraprendere in seguito. Per semplificare, un evento imprevisto della sicurezza può includere artefatti, eventi correlati e informazioni. Le informazioni aggiuntive disponibili per gli eventi imprevisti di sicurezza variano a seconda del tipo di minaccia rilevata e la configurazione dell'ambiente. 

![Dettagli evento imprevisto di sicurezza](./media/security-center-alerts-cloud-smart/security-incident.png)

Per comprendere meglio gli incidenti di sicurezza, vedere [modalità di gestione eventi imprevisti della sicurezza nel Centro sicurezza Azure](https://docs.microsoft.com/azure/security-center/security-center-incident).

