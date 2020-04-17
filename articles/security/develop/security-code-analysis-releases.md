---
title: Rilasci di Microsoft Security Code Analysis
description: Questo articolo descrive le prossime versioni per l'estensione Microsoft Security Code Analysis
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/14/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: a07ce7437d664baca0cfdc310dbc2631f41fdbcc
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81462035"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>Rilasci e roadmap di Microsoft Security Code Analysis

Il team Microsoft Security Code Analysis in collaborazione con Developer Support è orgoglioso di annunciare miglioramenti recenti e futuri alla nostra estensione MSCA. Si prega di consultare la tabella di marcia qui sotto.

![Rilasci](./media/security-code-analysis-releases/releases.png)

## <a name="credential-scanner-v20-released-on-april-1-2020"></a>Credential Scanner v2.0: rilasciato il 1 aprile 2020

### <a name="innovations--improvements"></a>Innovazioni & miglioramenti

- **Motore core**

   - Aggiornamento medio delle prestazioni del 25% con tempi di esecuzione quasi lineari
   - Ricerca basata su contesto/prove e classificazione per una maggiore precisione
   - Miglioramenti al rilevamento generale delle password e alla logica di corrispondenza per i segnaposto ovvi (ad esempio, fakePassword)

- **Copertura** - Supporto per oltre 25 tipi segreti, inclusi i seguenti top requested:

   - Passphrase del certificato dell'account Fabric
   - Chiave segreto client/API
   - Intestazione di autorizzazione HTTP
   - Chiave di accesso segreta del client Amazon S3
   - Azure Active Directory Client Access Token
   - Azure Function Master/API Key
   - Chiave di accesso di Power BI
   - Modello di password del modello di Azure Resource ManagerAzure Resource Manager template password pattern

- **Output**

   - Supporto per formati di file di output SARIF 2.1 e CSV

## <a name="binskim-v160-to-be-released-on-april-2020"></a>BinSkim v1.6.0: da rilasciare ad aprile 2020

### <a name="improvements"></a>Miglioramenti

- FEATURE: Aggiornamento alla versione finale SARIF v2 (versione 2.1.16). In questo modo la memorizzazione nella cache dei risultati durante il passaggio di --ishes nella riga di comando, un miglioramento significativo delle prestazioni durante l'analisi ricorsiva delle directory con più copie delle destinazioni di analisi.
- BUG FIX: Correggere l'errore di battitura in BA2021. Output di DoNotMarkWritableSectionsAsExecutable.
- PERFORMANCE: eliminare il caricamento PDB per tutti gli assembly gestiti non in modalità mista, inclusi i file binari della libreria IL (in anticipo compilati).
- FALSE NEGATIVE FIX: Verificare che un PDB posizionato accanto a un file binario corrisponda effettivamente al file binario in fase di analisi
- FEATURE: Fornire l'argomento --local-symbol-directories per specificare percorsi PDB aggiuntivi (locali, non-server)
- FALSE POSITIVE FIX: Saltare l'analisi basata su PDB per il exe nativo bootstrap nativo di .NET generato (che non è codice controllabile dall'utente).

## <a name="whats-next-in-fy20"></a>Cosa c'è di nuovo nel FY20?

- Strumento di analisi della sicurezza Java
- Strumento di analisi della sicurezza di Python
- ES Lint per sostituire TS Lint per TypeScript e JavaScript

## <a name="next-steps"></a>Passaggi successivi

Per istruzioni su come eseguire l'onboarding e l'installazione di Microsoft Security Code Analysis, fare riferimento alla [guida all'onboarding e all'installazione](security-code-analysis-onboard.md).

Se hai altre domande sull'estensione e sugli strumenti offerti, dai un'occhiata alla nostra [pagina FAQ](security-code-analysis-faq.md).
