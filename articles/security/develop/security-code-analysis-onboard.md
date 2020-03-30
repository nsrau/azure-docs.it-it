---
title: Guida all'onboarding dell'analisi dei codici di sicurezza Microsoft
description: In questo articolo viene descritta l'installazione dell'estensione Microsoft Security Code Analysis
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
ms.openlocfilehash: 6132aab98cc8145cb99cf153c64f20fbac00131c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197006"
---
# <a name="onboarding-and-installing"></a>Onboarding e installazione

Prerequisiti per iniziare a utilizzare Microsoft Security Code Analysis:

- Un'offerta di supporto unificato Microsoft idonea, come descritto nella sezione seguente.
- Un'organizzazione Azure DevOps.
- Autorizzazione per installare le estensioni nell'organizzazione DevOps di Azure.Permission to install extensions to the Azure DevOps organization.
- Codice sorgente che può essere sincronizzato con una pipeline DevOps di Azure ospitata nel cloud.

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>Onboarding dell'estensione Microsoft Security Code Analysis

### <a name="interested-in-purchasing-the-microsoft-security-code-analysis-extension"></a>Sei interessato ad acquistare l'estensione Microsoft Security Code Analysis?

Se disponi di una delle seguenti offerte di supporto, contatta il tuo Technical Account Manager per acquistare o scambiare le ore esistenti per accedere all'estensione:

- Livello avanzato supporto unificato
- Livello Prestazioni supporto unificato
- Supporto Premier per gli sviluppatori
- Supporto tecnico Premier per i partner
- Supporto Premier per le aziende

Se non si dispone di uno dei suddetti contratti di supporto, è possibile acquistare l'estensione da uno dei nostri partner.

**Passaggi successivi:**

Contattare un partner dall'elenco seguente e chiedere di acquistare l'estensione Microsoft Security Code Analysis.

>**Partner:**

- Aree - Dettagli di contatto:cloudsupport@zones.com

### <a name="become-a-partner"></a>Diventa un partner

Il team Microsoft Security Code Analysis sta cercando di collaborare con i partner con un contratto Premier Support for Partners. I partner consentono ai clienti DevOps di Azure di svilupparsi in modo più sicuro vendendo l'estensione ai clienti che desiderano acquistarla, ma non hanno un contratto di supporto Enterprise con Microsoft. I partner interessati possono registrarsi [qui](http://www.microsoftpartnersupport.com/msrd/opin).

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>Installazione dell'estensione Microsoft Security Code Analysis

1. Dopo aver condiviso l'estensione con l'organizzazione DevOps di Azure, passare alla pagina dell'organizzazione DevOps di Azure.After the extension is shared with your Azure DevOps organization, go to your Azure DevOps organization page. Un URL di esempio `https://dev.azure.com/contoso`per una pagina di questo tipo è .
1. Seleziona l'icona del carrello nell'angolo in alto a destra accanto al tuo nome, quindi seleziona **Gestisci estensioni**.
1. Selezionare **Condiviso**.
1. Selezionare l'estensione Microsoft Security Code Analysis, selezionare **install**.
1. Nell'elenco a discesa scegliere l'organizzazione DevOps di Azure in cui installare l'estensione.
1. Selezionare **Installa**. Al termine dell'installazione, è possibile iniziare a utilizzare l'estensione.

>[!NOTE]
> Anche se non si dispone dell'accesso per l'installazione dell'estensione, continuare con la procedura di installazione. È possibile richiedere l'accesso all'amministratore dell'organizzazione DevOps di Azure durante il processo di installazione.

Dopo aver installato l'estensione, le attività di compilazione di sviluppo sicuro sono visibili e disponibili per l'aggiunta alle pipeline di Azure.After you install the extension, the secure development build tasks are visible and available to add to your Azure Pipelines.

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>Aggiunta di attività di compilazione specifiche alla pipeline DevOps di AzureAdding specific build tasks to your Azure DevOps pipeline

1. Dall'organizzazione DevOps di Azure aprire il progetto team.
1. Selezionare **Pipelines** > **Builds**.
1. Selezionare la pipeline in cui si desidera aggiungere le attività di compilazione dell'estensione:Select the pipeline into which you want to add the extension build tasks:
   - Nuova pipeline: selezionare **Nuovo** e seguire i passaggi descritti per creare una nuova pipeline.
   - Modifica pipeline: selezionare una pipeline esistente e quindi selezionare **Modifica** per iniziare a modificare la pipeline.
1. Selezionare **+** e passare al riquadro **Aggiungi attività.**
1. Nell'elenco o tramite la casella di ricerca individuare l'attività di compilazione che si desidera aggiungere. Selezionare **Aggiungi**.
1. Specificare i parametri necessari per l'attività.
1. Inserire in coda una nuova compilazione.
   >[!NOTE]
   >I percorsi di file e cartelle sono relativi alla radice del repository di origine. Se si specificano i file e le cartelle di output come parametri, questi vengono sostituiti con il percorso comune definito nell'agente di compilazione.

> [!TIP]
>
> - Per eseguire un'analisi dopo la compilazione, inserire le attività di compilazione Microsoft Security Code Analysis dopo il passaggio Pubblica elementi della compilazione. In questo modo, la compilazione può terminare e pubblicare i risultati prima di eseguire gli strumenti di analisi statica.
> - Selezionare sempre **Continua in caso di errore** per le attività di compilazione di sviluppo sicuro. Anche se si verifica un errore in uno strumento, gli altri possono essere eseguiti. Non ci sono interdipendenze tra gli strumenti.
> - Le attività di compilazione di Microsoft Security Code Analysis hanno esito negativo solo se uno strumento non viene eseguito correttamente. Ma hanno successo anche se uno strumento identifica i problemi nel codice. Utilizzando l'attività di compilazione post-analisi, è possibile configurare la compilazione in modo che non riesca quando uno strumento identifica i problemi nel codice.
> - Alcune attività di compilazione DevOps di Azure non sono supportate quando vengono eseguite tramite una pipeline di rilascio. Più specificamente, Azure DevOps non supporta le attività che pubblicano elementi dall'interno di una pipeline di rilascio.
> - Per un elenco di variabili predefinite in Azure DevOps Team Build che è possibile specificare come parametri, vedere Variabili di [compilazione DevOps](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts)di Azure .

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla configurazione delle attività di compilazione, vedere la [guida alla configurazione](security-code-analysis-customize.md) o la guida alla configurazione [YAML](yaml-configuration.md).

Se hai altre domande sull'estensione e sugli strumenti offerti, dai un'occhiata alla nostra [pagina FAQ](security-code-analysis-faq.md).
