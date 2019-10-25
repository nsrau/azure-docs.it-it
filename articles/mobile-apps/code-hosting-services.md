---
title: Ospitare il codice sorgente dell'applicazione per dispositivi mobili nel cloud con GitHub e Azure DevOps
description: Informazioni sui servizi per ospitare il codice dell'applicazione per dispositivi mobili nel cloud con i servizi Microsoft.
author: elamalani
ms.assetid: 12a8a079-9b3c-4faf-2222-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: f0af2ee5e62bbdbfb5d18cffc3e3ed62edec81f3
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795627"
---
# <a name="cloud-hosted-source-code-management-services"></a>Servizi di gestione del codice sorgente ospitati nel cloud
Se si hanno team di sviluppo con più membri del team che lavorano sulla stessa codebase, è necessario trovare un posto appropriato per ospitare il codice. L'archiviazione dei dati nel cloud e la presenza di un repository centralizzato consentono a chiunque di caricare, modificare e gestire i file di codice, interagire con altri sviluppatori nei progetti e fornire la possibilità che il codice sia facilmente accessibile indipendentemente da dove si trovano. disporre di una connessione Internet.

L'hosting cloud è molto più semplice rispetto alle opzioni locali perché richiede meno configurazione hardware e consente alle organizzazioni di completare il processo di implementazione in modo più agile.

## <a name="benefits-of-hosting-source-code-in-the-cloud"></a>Vantaggi dell'hosting di codice sorgente nel cloud
- **Archiviazione cloud centralizzata** per visualizzare e gestire i dati ovunque ci si trovi.
- Consente di **migliorare la collaborazione e il codice più pulito** nei team per tenere traccia del codice e gestire i progetti per garantire il recapito continuo di software eccellente.
- **Entra in gioco** e semplifica la **collaborazione** dei tuoi progetti.
- **Ciclo di rilascio più rapido** perché il team può lavorare in modo più rapido e **semplice per contribuire** ai progetti.
- **Riduci i costi** senza doverti preoccupare della gestione di hardware, server, VPN e così via.

Usare i servizi seguenti per ospitare i dati dell'applicazione nel cloud.

## <a name="github"></a>GitHub
[GitHub](https://github.com/) è un servizio di hosting di repository open source che ospita progetti di codice sorgente in un'ampia gamma di linguaggi di programmazione diversi e tiene traccia delle diverse modifiche apportate a ogni iterazione.

**Funzionalità principali**
- **Hosting di codice** con tutto il codice in un'unica posizione. Privato, pubblico o open source, tutti i repository sono dotati di strumenti che consentono di ospitare, versione e codice di rilascio.
- La revisione del **codice** e gli strumenti di revisione predefiniti rendono il codice una parte essenziale di qualsiasi processo dei team.
    - Proteggere i rami, proporre modifiche e revisioni delle richieste.
    - Vedere la differenza e il commento nel contesto e ottenere un feedback chiaro.
- Coordinare in anticipo, rimanere allineati e ottenere maggiore successo con gli **strumenti di gestione dei progetti**di GitHub.
    - Vedere il quadro generale del progetto.
    - **Lavagne delle attività** che si trovano subito accanto al codice all'interno di GitHub.
    - **Trascinare le schede** per consentire l'assegnazione di problemi o richieste pull ai membri del team.
    - **Attività cardine** per organizzare e tenere traccia dello stato di avanzamento.
    - **Note** per acquisire idee che potrebbero essere utili ma che non appartengono a un problema o a una richiesta pull particolare.
- Individua e scegli facilmente gli strumenti appropriati per una migliore comunicazione e automazione del lavoro acquistando applicazioni dal **[Marketplace GitHub](https://github.com/marketplace)** .
- **Gestione team** per gestire e espandere con facilità i team, i **ruoli utente** per organizzare i team e le autorizzazioni di accesso, gli strumenti per i **thread di discussione** per rimanere in discussione su argomenti e team e le **linee guida della community** per impostare rapidamente nuovi membri del team con un account.
- **Esplora** i progetti **più diffusi per** seguirli.
- **Rete e apprendimento** da altri utenti nel settore.

**Riferimenti**
- [GitHub](https://github.com/)
- [Guide di GitHub](https://guides.github.com/)
- [Forum della community di GitHub](https://github.community/)
- [Marketplace GitHub](https://github.com/marketplace)

## <a name="azure-devops"></a>Azure DevOps
[Azure DevOps](https://azure.microsoft.com/services/devops/) supporta [Azure Repos](https://azure.microsoft.com/services/devops/repos/) e [controllo della versione di Team Foundation (TFVC)](https://docs.microsoft.com/azure/devops/repos/tfvc/index?view=azure-devops) come opzioni di controllo del codice sorgente. Dispone di repository privati gratuiti illimitati con revisioni del codice collaborative, gestione avanzata dei file, ricerca del codice e criteri di ramo per garantire codice di alta qualità. Azure Repos è ideale per progetti di piccole dimensioni, nonché per organizzazioni di grandi dimensioni che necessitano del supporto nativo di AAD e dei criteri avanzati.
    
**Funzionalità principali**
- **Repository di codice sorgente git ospitato su cloud illimitato** per i repository pubblici e privati
    - Supporto per qualsiasi client Git.
    - Hook Web e integrazione dell'API.
- **Avviare la compilazione successiva** da una richiesta pull di repository
    - Collaborare per creare codice migliore usando la discussione threaded e l'integrazione continua per ogni modifica.
    - Configura le funzionalità di integrazione continua/distribuzione continua (CI/CD) per attivare automaticamente compilazioni, test e distribuzioni con ogni richiesta pull compilata usando Azure Pipelines o i tuoi strumenti.
    - Proteggi la qualità del codice con i criteri del ramo.
- **Controllo della versione centralizzato con controllo della versione di Team Foundation**, inclusa la revisione del codice.
- **Connettersi al codice** con Xcode, Eclipse, IntelliJ, Android Studio, Visual Studio, Visual Studio Code e altro ancora.
- **Potente ricerca di codice semantico**.
- **Pronto** per l'azienda, in quanto dispone di integrazione nativa con Azure Active Directory (ad), semplificando il processo di gestione dell'accesso ai repository di codice.
- **Verificare la qualità del codice** con criteri ramo, ad esempio il numero minimo di revisioni del codice, la necessità di compilazioni riuscite e l'applicazione delle strategie di merge git.
- **Connetti l'ambiente di sviluppo preferito** per accedere ai repository e gestire il lavoro.

**Riferimenti**
- [Inizia a usare Azure Repos](https://azure.microsoft.com/services/devops/repos/) 
- [Documentazione di Azure Repos](/azure/devops/repos/?view=azure-devops)