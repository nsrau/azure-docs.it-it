---
title: Ospitare il codice sorgente dell'applicazione per dispositivi mobili nel cloud con GitHub e Azure DevOps
description: Informazioni sui servizi per ospitare il codice dell'applicazione per dispositivi mobili nel cloud con i servizi Microsoft.
author: elamalani
ms.assetid: 12a8a079-9b3c-4faf-2222-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 8662b9f77614339ff514fa4fcf97dc1ee8fc7417
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454478"
---
# <a name="cloud-hosted-source-code-management-services"></a>Servizi di gestione del codice sorgente ospitati nel cloud
Se si hanno team di sviluppo con più membri del team che lavorano sulla stessa codebase, è necessario trovare un posto appropriato per ospitare il codice. L'archiviazione dei dati nel cloud e la presenza di un repository centralizzato consentono a tutti di caricare, modificare e gestire i file di codice. Possono anche interagire con altri sviluppatori nei progetti. Il codice può essere facilmente accessibile ovunque ci si trovi, purché si disponga di una connessione a Internet.

L'hosting cloud è molto più semplice rispetto alle opzioni locali. Richiede una configurazione hardware inferiore e consente alle organizzazioni di completare il processo di implementazione in modo più agile.

## <a name="benefits-of-hosting-source-code-in-the-cloud"></a>Vantaggi dell'hosting di codice sorgente nel cloud
- **Archiviazione cloud centralizzata:** Visualizza e gestisce i dati ovunque ti trovi.
- **Migliore collaborazione e codice più pulito:** Tenere traccia del codice all'interno dei team e gestire i progetti per garantire il recapito continuo di software eccellente.
- **Maggiore facilità di partecipazione:** Contribuisci facilmente ai tuoi progetti.
- **Ciclo di rilascio più veloce:** Lavora più rapidamente nei tuoi team e contribuisci facilmente ai tuoi progetti.
- **Ridurre i costi:** Non preoccuparti di gestire hardware, server, VPN e così via.

Usare i servizi seguenti per ospitare i dati dell'applicazione nel cloud.

## <a name="github"></a>GitHub
[GitHub](https://github.com/) è un servizio di hosting di repository open source che ospita progetti di codice sorgente in un'ampia gamma di linguaggi di programmazione diversi. GitHub tiene traccia delle diverse modifiche apportate a ogni iterazione.

**Caratteristiche principali**
- Usare l'hosting del codice per memorizzare tutto il codice in un'unica posizione. I repository privati, pubblici e open source sono tutti dotati di strumenti che consentono di ospitare, versione e codice di rilascio.
- Esaminare il codice e usare gli strumenti di revisione predefiniti per fare in modo che il codice riveda una parte essenziale del processo del team:
    - Proteggi i rami, Suggerisci modifiche e richieste di revisione.
    - Individuare le differenze, commentare il contesto e ottenere un feedback chiaro.
- Coordinare in anticipo, restare allineati e ottenere più tempo con gli strumenti di gestione dei progetti di GitHub:
    - Vedere il quadro generale del progetto.
    - Usare le lavagne delle attività che si trovano subito accanto al codice all'interno di GitHub.
    - Trascinare le schede per assegnare problemi o richieste pull ai membri del team.
    - Impostare le attività cardine per organizzare e tenere traccia dello stato di avanzamento.
    - Scrivere note per acquisire idee che potrebbero essere utili ma che non appartengono a un problema o a una richiesta pull particolare.
- Individua e scegli facilmente gli strumenti appropriati per una migliore comunicazione e automazione del lavoro acquistando applicazioni dal [Marketplace GitHub](https://github.com/marketplace).
- Gestire e espandere i team utilizzando: 
    - Ruoli utente che consentono di organizzare i team e le autorizzazioni di accesso.
    - Strumenti per thread di discussione per gestire le conversazioni su argomenti e team.
    - Linee guida della community per configurare rapidamente i nuovi membri del team con un account.
- Esplora i progetti più diffusi per seguirli.
- Rete e apprendimento da altri utenti nel settore.

**Riferimenti**
- [GitHub](https://github.com/)
- [Guide di GitHub](https://guides.github.com/)
- [Forum della community di GitHub](https://github.community/)
- [Marketplace GitHub](https://github.com/marketplace)

## <a name="azure-devops"></a>Azure DevOps
[Azure DevOps](https://azure.microsoft.com/services/devops/) supporta [Azure Repos](https://azure.microsoft.com/services/devops/repos/) e [controllo della versione di Team Foundation (TFVC)](https://docs.microsoft.com/azure/devops/repos/tfvc/index?view=azure-devops) come opzioni di controllo del codice sorgente. Dispone di repository privati gratuiti illimitati con revisioni del codice collaborative, gestione avanzata dei file, ricerca del codice e criteri di ramo per garantire codice di alta qualità. Azure Repos è ideale per progetti di piccole dimensioni e organizzazioni di grandi dimensioni che necessitano di supporto Azure Active Directory nativo e criteri avanzati.
    
**Caratteristiche principali**
- Usa un repository di codice sorgente git ospitato su cloud illimitato per i repository pubblici e privati:
    - Ottenere supporto per qualsiasi client Git.
    - Usare gli hook Web e l'integrazione dell'API.
- Avviare la compilazione successiva da una richiesta pull del repository:
    - Collaborare per creare codice migliore usando la discussione threaded e l'integrazione continua per ogni modifica.
    - Configurare l'integrazione continua/recapito continuo (CI/CD) per attivare automaticamente le compilazioni, i test e le distribuzioni con tutte le richieste pull completate. È possibile utilizzare Azure Pipelines o gli strumenti di.
    - Proteggi la qualità del codice con i criteri del ramo.
- Mantenere il controllo della versione centralizzato con controllo della versione di Team Foundation, che include la revisione del codice.
- Connettersi al codice usando Xcode, Eclipse, IntelliJ, Android Studio, Visual Studio, Visual Studio Code e altro ancora.
- Usare una potente ricerca di codice semantico.
- Ottieni vantaggi dalle funzionalità per le aziende. Azure DevOps offre l'integrazione nativa con Azure Active Directory, che semplifica il processo di gestione dell'accesso ai repository di codice.
- Verificare la qualità del codice con criteri di ramo, ad esempio un numero minimo di revisioni del codice, i requisiti per le compilazioni completate e l'applicazione delle strategie di merge di git.
- Connetti l'ambiente di sviluppo preferito per accedere ai repository e gestire il lavoro.

**Riferimenti**
- [Inizia a usare Azure Repos](https://azure.microsoft.com/services/devops/repos/) 
- [Documentazione di Azure Repos](/azure/devops/repos/?view=azure-devops)