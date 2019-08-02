---
title: Guida all'onboarding dell'analisi del codice di Microsoft Azure sicurezza
description: Questo articolo descrive l'installazione dell'estensione di analisi del codice di sicurezza
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
ms.openlocfilehash: 8a8eca73205d4f8f33d4d069fda72638af61d355
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718301"
---
# <a name="how-to-onboarding-and-installing"></a>Procedura: Onboarding e installazione

Prerequisiti per iniziare a usare l'analisi del codice di sicurezza Microsoft
  - Offerta di servizi Microsoft Unified Support idonei (dettagli più avanti)
  - Un'organizzazione di Azure DevOps
  - Autorizzazioni per installare le estensioni nell'organizzazione DevOps di Azure
  - Codice sorgente che può essere sincronizzato con una pipeline di Azure DevOps ospitata nel cloud


## <a name="onboarding-microsoft-security-code-analysis-extension"></a>Onboarding dell'estensione di analisi del codice di sicurezza Microsoft

- Se si dispone già di una delle offerte di supporto seguenti, è sufficiente contattare il Technical Account Manager e purchase\swap le ore esistenti per ottenere l'accesso all'estensione.
   - Supporto unificato: livello avanzato e di prestazioni, supporto tecnico Premier per gli sviluppatori, supporto tecnico Premier per i partner o supporto tecnico Premier per le aziende.
- Se si dispone di uno dei servizi di supporto seguenti oppure non si dispone di alcun piano di supporto Microsoft, sarà necessario eseguire l'aggiornamento a un'offerta di supporto idonea:
   - Supporto di Azure per partner, Azure Basic, Azure Developer, Azure standard, Azure Professional Direct o Unified support-livello principale
- Per acquistare un'offerta di supporto idonea, visitare i [servizi di supporto Home page](https://www.microsoft.com/enterprise/services/support)
- Una volta che è stato applicato un contratto di supporto, contattare l'account manager tecnico che può essere utile per iniziare e raccogliere tutti i dettagli necessari.
 
>[!NOTE]
> Solo i partner registrati nella rete partner Microsoft sono idonei per l'acquisto supporto tecnico Premier per i partner. in caso contrario, acquistare una delle offerte di supporto idonee citate in precedenza

## <a name="installing-microsoft-security-code-analysis-extension"></a>Installazione dell'estensione di analisi del codice di sicurezza Microsoft

1. Dopo che l'estensione è stata condivisa con l'organizzazione DevOps di Azure, passare alla pagina dell'organizzazione di Azure DevOps, ad esempio http://dev.azure.com/contoso)
2. Fare clic sull'icona del carrello nell'angolo superiore destro accanto al nome, quindi fare clic su Gestisci estensioni. 
3. Fare clic su Microsoft Security Code Analysis Extension e avviare la procedura guidata dell'interfaccia utente di Azure DevOps per avviare l'installazione.
4. Scegliere l'organizzazione DevOps di Azure in cui installare l'estensione nell'elenco a discesa
5. Fare clic su Installa. Al termine, è possibile procedere con l'uso dell'estensione

>[!NOTE]
> Anche se non si ha accesso, continuare con la procedura di installazione e sarà possibile richiedere l'accesso da parte dell'amministratore dell'organizzazione DevOps di Azure durante il processo.
>
Una volta installata l'estensione, le attività di compilazione di sviluppo sicure saranno visibili e disponibili per l'aggiunta all'Azure Pipelines.

## <a name="adding-specific-build-tasks-to-your-devops-pipeline"></a>Aggiunta di attività di compilazione specifiche alla pipeline DevOps

1. Aprire il progetto team dall'organizzazione DevOps di Azure.
2. Passare alla scheda compilazioni in **pipeline** 
3. Selezionare la pipeline in cui si desidera aggiungere le attività di compilazione dell'estensione. 
   - Nuovo: fare clic su **nuovo** e seguire i passaggi descritti in dettaglio per creare una nuova pipeline.
   - Modifica: selezionare la pipeline e fare clic su **modifica** per iniziare a modificare una pipeline esistente.
4. Fare clic su + per passare al riquadro **Aggiungi attività** .
5. Trovare l'attività di compilazione da aggiungere dall'elenco o usando la casella di ricerca e quindi fare clic su **Aggiungi**. 
6. È ora possibile continuare a specificare i parametri necessari per l'attività.
>[!NOTE]
>I percorsi di file o directory sono relativi alla radice del repository di origine e i parametri che specificano la cartella di output e i file verranno sostituiti con il percorso comune definito nell'agente di compilazione.

7. Inserire in coda una nuova compilazione.
> [!TIP]
>  - Per eseguire l'analisi dopo la compilazione, inserire le attività di compilazione di analisi del codice di sicurezza di Microsoft dopo il passaggio pubblica elementi di compilazione della compilazione. In questo modo, la compilazione può terminare e pubblicare i risultati prima di eseguire strumenti di analisi statica.
>  - Controllare sempre l'opzione **"continua in base all'errore"** delle attività di compilazione di sviluppo sicure. Anche in caso di errore di uno strumento, gli altri possono essere eseguiti. Nessuna interdipendenza.
>  - Le attività di compilazione dell'analisi del codice di sicurezza Microsoft avranno esito negativo solo se l'esecuzione dello strumento ha esito negativo, ma non avranno esito negativo se e quando lo strumento identifica i problemi nel codice. È possibile configurare la compilazione in modo che venga interrotta quando uno strumento identifica i problemi nel codice, usando l'attività di compilazione **post-analisi** .
>  - Alcune attività di compilazione di Azure DevOps non sono supportate quando vengono eseguite tramite una pipeline "release". Si tratta di una limitazione di Azure DevOps. Non supportano attività che pubblicano elementi all'interno di una pipeline di rilascio.
>  - Per un elenco delle variabili predefinite in Azure DevOps Team Build, che è possibile specificare come parametri, vedere le [variabili di compilazione di Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts)

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla configurazione delle attività di compilazione, vedere la [Guida alla configurazione](security-code-analysis-customize.md)

Per altre domande sull'estensione e sugli strumenti offerti, [vedere la pagina delle domande frequenti.](security-code-analysis-faq.md)


