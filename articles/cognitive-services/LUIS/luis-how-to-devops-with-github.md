---
title: Come applicare DevOps con GitHub-LUIS
titleSuffix: Azure Cognitive Services
description: Applicare DevOps con Language Understanding (LUIS) e GitHub.
services: cognitive-services
author: andycw
manager: cmayo
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/5/2020
ms.author: anwigley
ms.openlocfilehash: 6df65040277ac61cca5fb4bf7fce5b5a7b2f3afe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84783761"
---
# <a name="apply-devops-to-luis-app-development-using-github-actions"></a>Applicare DevOps per lo sviluppo di app LUIS usando GitHub Actions

Passare al [repository del modello Luis DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template) per una soluzione completa che implementi le procedure consigliate per la progettazione software e DEVOPS per Luis. È possibile usare questo repository di modelli per creare un repository personalizzato con supporto incorporato per flussi di lavoro e procedure di integrazione continua/recapito continuo che consentono il [controllo del codice sorgente](luis-concept-devops-sourcecontrol.md), le [compilazioni automatizzate](luis-concept-devops-automation.md), i [test](luis-concept-devops-testing.md)e la [gestione delle versioni](luis-concept-devops-automation.md#release-management) con Luis per il proprio progetto.

## <a name="the-luis-devops-template-repo"></a>Repository del modello LUIS DevOps

Il [repository di modelli Luis DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template) illustra come eseguire le operazioni seguenti:

* **Clonare il repository del modello** : copiare il modello nel repository GitHub.
* **Configurare le risorse Luis** : creare le [risorse Luis Authoring and prediction in Azure](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-azure-subscription#create-resources-in-azure-cli) che verranno usate dai flussi di lavoro di integrazione continua.
* **Configurare i flussi di lavoro ci/CD** : configurare i parametri per i flussi di lavoro di integrazione continua/recapito continuo e archiviarli nei [segreti di GitHub](https://help.github.com/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets).
* Illustra **il ["ciclo interno](https://mitchdenny.com/the-inner-loop/) ** di sviluppo": lo sviluppatore esegue gli aggiornamenti a un'app Luis di esempio mentre lavora in un ramo di sviluppo, verifica gli aggiornamenti e quindi genera una richiesta pull per proporre le modifiche e cercare l'approvazione della verifica.
* **Eseguire flussi di lavoro ci/CD** -eseguire [flussi di lavoro di integrazione continua per compilare e testare un'app Luis usando le azioni di](luis-concept-devops-automation.md) github.
* **Eseguire test automatizzati** : eseguire [test automatizzati in batch per un'app Luis](luis-concept-devops-testing.md) per valutare la qualità dell'app.
* **Distribuire l'app Luis** : eseguire un [processo di recapito continuo (CD)](luis-concept-devops-automation.md#continuous-delivery-cd) per pubblicare l'app Luis.
* **Usare il repository con** il proprio progetto: spiega come usare il repository con l'applicazione Luis personalizzata.

## <a name="next-steps"></a>Passaggi successivi

* Usare il [repository di modelli Luis DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template) per applicare DevOps con il proprio progetto.
* [Strategie di controllo del codice sorgente e gestione rami per LUIS](luis-concept-devops-sourcecontrol.md)
* [Test per DevOps LUIS](luis-concept-devops-testing.md)
* [Flussi di lavoro di automazione per LUIS DevOps](luis-concept-devops-automation.md)
