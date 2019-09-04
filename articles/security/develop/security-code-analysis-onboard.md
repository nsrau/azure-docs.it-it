---
title: Guida all'onboarding dell'analisi del codice di sicurezza Microsoft
description: Questo articolo descrive l'installazione dell'estensione Microsoft Security Code Analysis
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 8936bc21af951cbabb90188b6a31d610caf80395
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241768"
---
# <a name="onboarding-and-installing"></a>Onboarding e installazione

Prerequisiti per la Guida introduttiva all'analisi del codice di sicurezza Microsoft:

- Offerta di Microsoft Unified Support idonea, come descritto nella sezione seguente.
- Un'organizzazione di Azure DevOps.
- Autorizzazione per installare le estensioni nell'organizzazione DevOps di Azure.
- Codice sorgente che può essere sincronizzato con una pipeline di Azure DevOps ospitata nel cloud.

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>Onboarding dell'estensione di analisi del codice di sicurezza Microsoft

- Se si dispone di una delle seguenti offerte di supporto, contattare il Technical Account Manager per acquistare o scambiare ore esistenti per ottenere l'accesso all'estensione:
   - Livello avanzato supporto unificato
   - Livello di prestazioni supporto unificato
   - supporto tecnico Premier per gli sviluppatori
   - supporto tecnico Premier per i partner
   - supporto tecnico Premier per Enterprise
- Se si dispone di uno dei servizi di supporto seguenti oppure non si dispone di un piano di supporto Microsoft, è necessario eseguire l'aggiornamento a un'offerta di supporto idonea:
   - Supporto di Azure per i partner
   - Supporto Basic di Azure
   - supporto tecnico Developer di Azure
   - supporto tecnico Standard di Azure
   - Professional Direct di Azure
   - Livello core supporto unificato
- Per acquistare un'offerta di supporto idonea, passare alla [Home page dei servizi di supporto tecnico](https://www.microsoft.com/enterprise/services/support).
- Al termine di un contratto di supporto, contattare il Technical Account Manager per iniziare a raccogliere tutti i dettagli necessari.

>[!NOTE]
> Se si è un partner registrato in Microsoft Partner Network, l'utente è idoneo ad acquistare supporto tecnico Premier per i partner. In caso contrario, è necessario acquistare una delle offerte di supporto idonee citate in precedenza.

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>Installazione dell'estensione Microsoft Security Code Analysis

1. Dopo che l'estensione è stata condivisa con l'organizzazione DevOps di Azure, passare alla pagina di Azure DevOps Organization. Un URL di esempio per una pagina di http://dev.azure.com/contoso questo tipo è.
1. Selezionare l'icona del carrello nell'angolo superiore destro accanto al nome e quindi selezionare **Gestisci estensioni**.
1. Selezionare Microsoft Security Code Analysis Extension, quindi aprire la procedura guidata dell'interfaccia utente di Azure DevOps per avviare l'installazione.
1. Dall'elenco a discesa scegliere l'organizzazione DevOps di Azure in cui installare l'estensione.
1. Selezionare **Installa**. Al termine dell'installazione, è possibile iniziare a usare l'estensione.

>[!NOTE]
> Anche se non si ha accesso per l'installazione dell'estensione, continuare con la procedura di installazione. È possibile richiedere l'accesso all'amministratore dell'organizzazione di Azure DevOps durante il processo di installazione.

Dopo aver installato l'estensione, le attività di compilazione di sviluppo sicure sono visibili e disponibili per l'aggiunta alle pipeline di Azure.

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>Aggiunta di attività di compilazione specifiche alla pipeline di Azure DevOps

1. Dall'organizzazione DevOps di Azure aprire il progetto team.
1. Selezionare le**compilazioni** **pipeline** > .
1. Selezionare la pipeline in cui si desidera aggiungere le attività di compilazione dell'estensione:
   - Nuova pipeline: Selezionare **nuovo** e seguire i passaggi descritti in dettaglio per creare una nuova pipeline.
   - Modifica pipeline: Selezionare una pipeline esistente e quindi fare clic su **modifica** per iniziare a modificare la pipeline.
1. Selezionare **+** e passare al riquadro **Aggiungi attività** .
1. Dall'elenco o usando la casella di ricerca, trovare l'attività di compilazione che si vuole aggiungere. Selezionare **Aggiungi**.
1. Specificare i parametri necessari per l'attività.
1. Inserire in coda una nuova compilazione.
   >[!NOTE]
   >I percorsi di file e cartelle sono relativi alla radice del repository di origine. Se si specificano i file e le cartelle di output come parametri, questi vengono sostituiti con il percorso comune definito nell'agente di compilazione.

> [!TIP]
>
> - Per eseguire un'analisi dopo la compilazione, inserire le attività di analisi del codice di sicurezza Microsoft dopo il passaggio pubblica elementi di compilazione della compilazione. In questo modo, la compilazione può terminare e pubblicare i risultati prima di eseguire strumenti di analisi statica.
> - Selezionare sempre **continua in errore** per le attività di compilazione di sviluppo sicuro. Anche se si verifica un errore in uno strumento, gli altri possono eseguire. Tra gli strumenti non vi sono interdipendenze.
> - Le attività di compilazione dell'analisi del codice di sicurezza Microsoft hanno esito negativo solo se lo strumento non viene eseguito correttamente. Ma hanno esito positivo anche se uno strumento identifica i problemi nel codice. Utilizzando l'attività di compilazione post-analisi, è possibile configurare la compilazione in modo che abbia esito negativo quando uno strumento identifica i problemi nel codice.
> - Alcune attività di compilazione di Azure DevOps non sono supportate quando vengono eseguite tramite una pipeline di rilascio. In particolare, Azure DevOps non supporta le attività che pubblicano elementi all'interno di una pipeline di rilascio.
> - Per un elenco delle variabili predefinite in Azure DevOps Team Build che è possibile specificare come parametri, vedere le [variabili di compilazione di Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla configurazione delle attività di compilazione, vedere la [Guida alla configurazione](security-code-analysis-customize.md).

Per altre domande sull'estensione e sugli strumenti offerti, vedere la [pagina delle domande frequenti](security-code-analysis-faq.md).
