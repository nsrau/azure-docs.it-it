---
title: Ospita il codice sorgente dell'applicazione per dispositivi mobili nel cloud con GitHub e DevOps di AzureHost your mobile application source code in the cloud with GitHub and Azure DevOps
description: Informazioni sui servizi per ospitare il codice dell'applicazione per dispositivi mobili nel cloud con i servizi Microsoft.Learn the services to host your mobile application code in the cloud with Microsoft services.
author: codemillmatt
ms.assetid: 12a8a079-9b3c-4faf-2222-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 66d8980ab53010af0703d789fbe791c60a32052d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240954"
---
# <a name="cloud-hosted-source-code-management-services"></a>Servizi di gestione del codice sorgente ospitati nel cloud
Se si dispone di team di sviluppo con più membri del team che lavorano sulla stessa base di codice, è necessario trovare una posizione adatta per ospitare il codice. L'archiviazione dei dati nel cloud e la disponibilità di un repository centralizzato consentono a tutti di caricare, modificare e gestire i file di codice. Possono anche interagire con altri sviluppatori sui progetti. Il codice può essere facilmente accessibile ovunque ti trovi, purché tu abbia una connessione internet.

L'hosting cloud è molto più semplice rispetto alle opzioni locali. Richiede una configurazione hardware inferiore e consente alle organizzazioni di completare il processo di implementazione in modo più agile.

## <a name="benefits-of-hosting-source-code-in-the-cloud"></a>Vantaggi dell'hosting del codice sorgente nel cloud
- **Archiviazione cloud centralizzata:** Visualizza e gestisci i tuoi dati ovunque ti trovi.
- **Migliore collaborazione e codice più pulito:** Tieni traccia del codice all'interno dei team e gestisci i progetti per garantire la distribuzione continua di software eccellente.
- **Più facile essere coinvolti:** Contribuisci facilmente ai tuoi progetti.
- **Ciclo di rilascio più rapido:** Lavora più velocemente nei tuoi team e contribuisci facilmente ai tuoi progetti.
- **Riduci i costi:** Non preoccuparti di mantenere il tuo hardware, server, VPN e così via.

Usare i servizi seguenti per ospitare i dati dell'applicazione nel cloud.

## <a name="github"></a>GitHub
[GitHub](https://github.com/) è un servizio di hosting di repository open source che ospita progetti di codice sorgente in una varietà di linguaggi di programmazione diversi. GitHub tiene traccia delle varie modifiche apportate a ogni iterazione.

**Caratteristiche principali**
- Usare l'hosting del codice per conservare tutto il codice in un'unica posizione. I repository privati, pubblici e open source sono tutti dotati di strumenti che consentono di ospitare, versione e codice di rilascio.
- Esaminare il codice e utilizzare gli strumenti di revisione incorporati per rendere la revisione del codice una parte essenziale del processo di qualsiasi team:Review code and use the built-in review tools to make code review an essential part of any team's process:
    - Proteggere le filiali, proporre modifiche e richiedere recensioni.
    - Individua le differenze, commenta nel contesto e ottieni un feedback chiaro.
- Coordinati in anticipo, mantieniti allineati e fai di più con gli strumenti di gestione dei progetti di GitHub:
    - Guarda il quadro generale del progetto.
    - Usare le bacheche di attività che si trovano accanto al codice all'interno di GitHub.Use task boards that are right next to your code inside GitHub.
    - Trascinare le schede per assegnare problemi o pull delle richieste ai membri del team.
    - Impostare le attività cardine per organizzare e tenere traccia dello stato di avanzamento.
    - Scrivere note per acquisire idee che potrebbero essere utili ma che non appartengono a un problema specifico o a una richiesta pull.
- Scopri e scegli facilmente gli strumenti giusti per una migliore comunicazione e automazione del lavoro acquistando applicazioni da [GitHub Marketplace.](https://github.com/marketplace)
- Gestisci e crea team utilizzando: 
    - Ruoli utente per organizzare i team e le autorizzazioni di accesso.
    - Strumenti del thread di discussione per mantenere le conversazioni sull'argomento e sul team focalizzati.
    - Linee guida della community per impostare rapidamente nuovi membri del team con un account.
- Sfoglia e stelle progetti popolari per seguirli.
- Rete e imparare da altri nel settore.

**Riferimenti**
- [GitHub](https://github.com/)
- [Guide GitHub](https://guides.github.com/)
- [GitHub Community Forum](https://github.community/)
- [GitHub Marketplace](https://github.com/marketplace)

## <a name="azure-devops"></a>Azure DevOps
[Azure DevOps](https://azure.microsoft.com/services/devops/) supporta [Azure Repos](https://azure.microsoft.com/services/devops/repos/) e [Team Foundation Version Control (TFVC)](https://docs.microsoft.com/azure/devops/repos/tfvc/index?view=azure-devops) come opzioni di controllo del codice sorgente. Ha archivi privati gratuiti illimitati con revisioni del codice collaborativo, gestione avanzata dei file, ricerca di codice e criteri di ramo per garantire codice di alta qualità. Azure Repos è ideale per progetti di piccole dimensioni e organizzazioni di grandi dimensioni che richiedono il supporto nativo di Azure Active Directory e criteri avanzati.
    
**Caratteristiche principali**
- Usa un repository illimitato di codice sorgente Git ospitato nel cloud per i tuoi repository pubblici e privati:
    - Ottenere supporto per qualsiasi client Git.Get support for any Git client.
    - Usa gli hook Web e l'integrazione API.
- Avvia la tua prossima build da una richiesta di repo pull:
    - Collaborare per creare codice migliore utilizzando la discussione in thread e l'integrazione continua per ogni modifica.
    - Configurare l'integrazione continua/distribuzione continua (CI/CD) per attivare automaticamente compilazioni, test e distribuzioni con ogni richiesta pull completata. È possibile usare le pipeline di Azure o gli strumenti.
    - Proteggere la qualità del codice con i criteri di diramazione.
- Mantenere il controllo della versione centralizzato con il controllo della versione di Team Foundation, che include la revisione del codice.
- Connettersi al codice usando Xcode, Eclipse, IntelliJ, Android Studio, Visual Studio, Visual Studio Code e altro ancora.
- Usa una potente ricerca nel codice semantico.
- Ottieni i vantaggi dalle funzionalità pronte per l'azienda. Azure DevOps ha l'integrazione nativa con Azure Active Directory, che semplifica il processo di gestione dell'accesso ai repository di codice.
- Garantire la qualità del codice con i criteri di ramo, ad esempio un numero minimo di revisioni del codice, i requisiti per le compilazioni riuscite e l'applicazione delle strategie di unione Git.Ensure code quality with branch policies such as a minimum number of code reviews, requirements for successful builds, and enforcement of Git merge strategies.
- Connetti il tuo ambiente di sviluppo preferito per accedere ai repository e gestire il lavoro.

**Riferimenti**
- [Introduzione ad Azure Repos](https://azure.microsoft.com/services/devops/repos/) 
- [Documentazione di Azure ReposAzure Repos documentation](/azure/devops/repos/?view=azure-devops)