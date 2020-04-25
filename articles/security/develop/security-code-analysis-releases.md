---
title: Versioni di analisi del codice di sicurezza Microsoft
description: Questo articolo descrive le versioni future per Microsoft Security Code Analysis Extension
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/24/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: d4281d3b6132e551283a71cd1801ef462fbfc68c
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146133"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>Roadmap e versioni di analisi del codice di sicurezza Microsoft

Il team di analisi del codice di sicurezza Microsoft in collaborazione con supporto tecnico Developer è orgoglioso di annunciare i miglioramenti recenti e imminenti per l'estensione MSCA. Vedere la roadmap riportata di seguito.

![Rilasci](./media/security-code-analysis-releases/releases.png)

## <a name="credential-scanner-v20-released-in-april-2020"></a>Credential Scanner v 2.0: rilasciato ad aprile 2020

### <a name="innovations--improvements"></a>Miglioramenti delle innovazioni &

- **Motore di base**

   - Aggiornamento medio delle prestazioni del 25% con tempi di esecuzione quasi lineari
   - Ricerca e classificazione basate su contesto/evidenza per una maggiore accuratezza
   - Miglioramenti ai rilevamenti generali delle password e alla logica di corrispondenza per i segnaposti evidenti (ad esempio, fakePassword)

- **Code coverage** -supporto per più di 25 tipi di segreto, inclusi i seguenti:

   - Passphrase certificato account infrastruttura
   - Segreto client/chiave API
   - Intestazione di autorizzazione HTTP
   - Chiave di accesso segreto client Amazon S3
   - Azure Active Directory token di accesso client
   - Chiave master/API funzione di Azure
   - Chiave di accesso Power BI
   - Modello di password del modello di Azure Resource Manager

- **Output**

   - Supporto per i formati di file di output di file SARIF 2,1 e CSV

## <a name="binskim-v160-released-in-april-2020"></a>BinSkim v 1.6.0: rilasciata ad aprile 2020

### <a name="improvements"></a>Miglioramenti

- FUNZIONALITÀ: aggiornamento alla versione finale di SARIF V2 (versione 2.1.16). Ciò consente la memorizzazione nella cache dei risultati quando vengono superati gli hash dalla riga di comando, un miglioramento significativo delle prestazioni quando si analizza in modo ricorsivo le directory con più copie di destinazioni di analisi.
- CORREZIONE di BUG: correzione del digitazione in BA2021. Output DoNotMarkWritableSectionsAsExecutable.
- PRESTAZIONI: eliminare IL caricamento del PDB per tutte le modalità non mista per gli assembly gestiti, inclusi i file binari della libreria IL (Ahead of Time compilati).
- CORREZIONE falsa negativa: verificare che un PDB situato insieme a un file binario corrisponda effettivamente al file binario sotto l'analisi
- FUNZIONALITÀ: fornire l'argomento--local-symbol-Directories per specificare percorsi di ricerca PDB aggiuntivi (locali, non simboli)
- CORREZIONE di falsi positivi: ignora l'analisi guidata da PDB per il file exe bootstrap nativo di .NET Core, che non è un codice controllabile dall'utente.

## <a name="whats-next-in-fy20"></a>Passaggi successivi in FY20

- Strumento di analisi della sicurezza Java
- Strumento di analisi della sicurezza Python
- Pelucchi ES per sostituire i pelucchi di Servizi terminal per TypeScript e JavaScript

## <a name="next-steps"></a>Passaggi successivi

Per istruzioni su come caricare e installare Microsoft Security Code Analysis, vedere la [Guida all'onboarding e all'installazione](security-code-analysis-onboard.md).

Per altre domande sull'estensione e sugli strumenti offerti, vedere la [pagina delle domande frequenti](security-code-analysis-faq.md).
