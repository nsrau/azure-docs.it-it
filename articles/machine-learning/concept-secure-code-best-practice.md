---
title: Procedure consigliate per il codice sicuro
titleSuffix: Azure Machine Learning
description: Informazioni sulle potenziali minacce alla sicurezza che possono esistere durante lo sviluppo per Azure Machine Learning. Informazioni sulle mitigazioni fornite da Azure ML e procedure consigliate per assicurare che gli ambienti di sviluppo rimangano protetti.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: cjgronlund
ms.date: 11/12/2019
ms.openlocfilehash: 4bc9a982f6ce77b803a3ba91e050bcda9ec74fed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91728523"
---
# <a name="secure-code-best-practices-with-azure-machine-learning"></a>Procedure consigliate per il codice protetto con Azure Machine Learning

In Azure Machine Learning, è possibile caricare file e contenuto da qualsiasi origine in Azure. Il contenuto all'interno di Jupyter notebook o script caricati può potenzialmente leggere i dati delle sessioni, accedere ai dati all'interno dell'organizzazione in Azure o eseguire processi dannosi per conto dell'utente.

> [!IMPORTANT]
> Eseguire solo notebook o script da origini attendibili. Ad esempio, in cui l'utente o il team di sicurezza ha esaminato il notebook o lo script.

## <a name="potential-threats"></a>Potenziali minacce

Lo sviluppo con Azure Machine Learning spesso comporta ambienti di sviluppo basati sul Web (notebook & Azure ML Studio). Quando si usano gli ambienti di sviluppo basati sul Web, le potenziali minacce sono:

* [Scripting tra siti (XSS)](https://owasp.org/www-community/attacks/xss/)

    * __Injection Dom__: questo tipo di attacco può modificare l'interfaccia utente visualizzata nel browser. Ad esempio, modificando il comportamento del pulsante Run in un Jupyter Notebook.
    * __Token di accesso/cookie__: gli attacchi XSS possono accedere anche all'archiviazione locale e ai cookie del browser. Il token di autenticazione di Azure Active Directory (AAD) viene archiviato nella risorsa di archiviazione locale. Un attacco XSS può usare questo token per effettuare chiamate API per conto dell'utente e quindi inviare i dati a un sistema esterno o a un'API.

* [Richiesta tra siti falsificazione (CSRF)](https://owasp.org/www-community/attacks/csrf): questo attacco può sostituire l'URL di un'immagine o un collegamento con l'URL di uno script o di un'API dannoso. Quando l'immagine viene caricata o si fa clic sul collegamento, viene effettuata una chiamata all'URL.

## <a name="azure-ml-studio-notebooks"></a>Notebook di Azure ML Studio

Azure Machine Learning Studio offre un'esperienza di notebook ospitata nel browser. Le celle in un notebook possono generare documenti HTML o frammenti che contengono codice dannoso.  Quando viene eseguito il rendering dell'output, il codice può essere eseguito.

__Possibili minacce__:
* Scripting tra siti (XSS)
* Richiesta tra siti falsificazione (CSRF)

__Mitigazioni fornite da Azure Machine Learning__:
* L' __output della cella di codice__ è in modalità sandbox in un iframe. L'iframe impedisce che lo script acceda al DOM padre, ai cookie o all'archiviazione della sessione.
* Il contenuto della __cella Markdown__ viene pulito usando la libreria dompurify. Questo impedisce l'esecuzione di script dannosi con le celle Markdown.
* L' __URL dell'immagine__ e i __collegamenti Markdown__ vengono inviati a un endpoint di proprietà di Microsoft, che verifica la presenza di valori dannosi. Se viene rilevato un valore dannoso, l'endpoint rifiuta la richiesta.

__Azioni consigliate__:
* Assicurarsi di considerare attendibile il contenuto dei file prima del caricamento in studio. Quando si carica, è necessario confermare che si sta caricando file attendibili.
* Quando si seleziona un collegamento per aprire un'applicazione esterna, verrà richiesto di considerare attendibile l'applicazione.

## <a name="azure-ml-compute-instance"></a>Istanza di calcolo di Azure ML

Azure Machine Learning istanza di calcolo ospita __Jupyter__ e __Jupyter Lab__. Quando si usa una delle due, le celle in un notebook o in un codice in possono restituire documenti o frammenti HTML che contengono codice dannoso. Quando viene eseguito il rendering dell'output, il codice può essere eseguito. Le stesse minacce si applicano anche quando si usa __rstudio__ ospitato in un'istanza di calcolo.

__Possibili minacce__:
* Scripting tra siti (XSS)
* Richiesta tra siti falsificazione (CSRF)

__Mitigazioni fornite da Azure Machine Learning__:
* No. Jupyter e Jupyter Lab sono applicazioni open source ospitate nell'istanza di calcolo Azure Machine Learning.

__Azioni consigliate__:
* Assicurarsi di considerare attendibile il contenuto dei file prima del caricamento in studio. Quando si carica, è necessario confermare che si sta caricando file attendibili.

## <a name="report-security-issues-or-concerns"></a>Segnala problemi di sicurezza o problemi 

Azure Machine Learning è idoneo per il programma Microsoft Azure Bounty. Per ulteriori informazioni, visitare il  [https://www.microsoft.com/msrc/bounty-microsoft-azure](https://www.microsoft.com/msrc/bounty-microsoft-azure) .

## <a name="next-steps"></a>Passaggi successivi

* [Sicurezza aziendale per Azure Machine Learning](concept-enterprise-security.md)