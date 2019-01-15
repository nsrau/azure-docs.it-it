---
title: Creare soluzioni di scalabilità tra cloud con Azure | Microsoft Docs
description: Informazioni su come creare soluzioni di scalabilità tra cloud con Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.openlocfilehash: 800e41fe26cb58840c7371606ed746c149f7024d
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2019
ms.locfileid: "54304143"
---
# <a name="tutorial-create-cross-cloud-scaling-solutions-with-azure"></a>Esercitazione: Creare soluzioni di scalabilità tra cloud con Azure

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Informazioni su come creare una soluzione cross-cloud per fornire un processo attivato manualmente per passare da uno Stack di Azure ospitato l'app web, per un'istanza di Azure ospitato l'app web con la scalabilità automatica tramite Gestione traffico, assicurando utilità cloud flessibile e scalabile in caso di carico.

Con questo modello, il tenant potrebbe non essere pronto per l'esecuzione dell'applicazione nel cloud pubblico. Tuttavia, potrebbe non essere economicamente fattibile all'azienda di gestire la capacità necessaria nell'ambiente locale per gestire i picchi della domanda per l'app. Il tenant può richiedere sfruttare l'elasticità del cloud pubblico con la propria soluzione in locale.

In questa esercitazione si creerà un ambiente di esempio:

> [!div class="checklist"]
> - Creare un'applicazione web a più nodi.
> - Configurare e gestire il processo di distribuzione continua (CD).
> - Pubblicare l'app web in Azure Stack.
> - Creare una versione.
> - Informazioni su come monitorare e tenere traccia delle distribuzioni.

> [!Tip]  
> ![ibrido-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack è un'estensione di Azure. Azure Stack offre l'agilità e innovazione del cloud computing al tuo ambiente locale e abilitare l'unico cloud ibrido che consente di compilare e distribuire le app ibride ovunque.  
> 
> Il white paper [considerazioni sulla progettazione per applicazioni ibride](https://aka.ms/hybrid-cloud-applications-pillars) esamina i concetti fondamentali della qualità del software (selezione host, scalabilità, disponibilità, resilienza, gestibilità e sicurezza) per la progettazione, distribuzione e gestione applicazioni ibride. Le considerazioni di progettazione assistere nell'ottimizzazione della progettazione di applicazioni ibride, riducendo al minimo le sfide negli ambienti di produzione.

## <a name="prerequisites"></a>Prerequisiti

-   Sottoscrizione di Azure. Se necessario, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Un sistema integrato di Azure Stack o la distribuzione di Azure Stack Development Kit.
    - Trovare le istruzioni per l'installazione di Azure Stack in [installazione di Azure Stack Development Kit](../asdk/asdk-install.md).
    - [https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) Questa installazione può richiedere alcune ore per il completamento.

-   Distribuire [servizio App](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) servizi PaaS per Azure Stack.

-   [Crea piano/offerte](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) all'interno dell'ambiente Azure Stack.

-   [Creare una sottoscrizione tenant](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) all'interno dell'ambiente Azure Stack.

-   Creare un'App Web all'interno della sottoscrizione tenant. Prendere nota del nuovo URL di App Web per usare in un secondo momento.

-   Distribuisci macchina virtuale di Azure le pipeline all'interno della sottoscrizione tenant.

-   VM Windows Server 2016 con .NET 3.5 è necessario. Questa macchina virtuale verrà compilata nella sottoscrizione del tenant in Azure Stack come agente di compilazione privata.

-   [Windows Server 2016 con immagine di macchina virtuale di SQL 2017](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image#add-a-vm-image-through-the-portal) è disponibile in Azure Stack Marketplace. Se questa immagine non è disponibile, rivolgersi a un operatore di Azure Stack per assicurarsi che viene aggiunto all'ambiente.

## <a name="issues-and-considerations"></a>Considerazioni e problemi

### <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

Il componente principale di ridimensionamento tra cloud è la capacità di fornire la scalabilità immediata, on demand tra public e infrastruttura cloud, dimostrando servizio affidabile e coerente, come prescritto per la richiesta in locale.

### <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Verificare localmente le app distribuite sono configurate per la disponibilità elevata tramite on-premises hardware configurazione e distribuzione del software.

### <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

La soluzione cross-cloud garantisce facile gestione e interfaccia familiare tra ambienti. PowerShell è consigliato per la gestione multipiattaforma.

## <a name="cross-cloud-scaling"></a>La scalabilità tra cloud

### <a name="obtain-a-custom-domain-and-configure-dns"></a>Ottenere un dominio personalizzato e configurare il server DNS

Aggiornare il file di zona DNS per il dominio. Azure AD verificherà la proprietà del nome di dominio personalizzato. Uso [DNS di Azure](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) per i record DNS di Azure/Office 365/esterni in Azure, oppure aggiungere la voce DNS in [un registrar DNS diverso](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

1.  Registrare un dominio personalizzato con un Registrar di pubblico.

2.  Accedere al registrar per il dominio. Un amministratore approvato potrebbe essere necessario apportare gli aggiornamenti DNS. 

3.  Aggiornare il file di zona DNS per il dominio aggiungendo la voce DNS fornita da Azure AD. (La voce DNS non avranno effetto routing della posta elettronica o i comportamenti di hosting web.) 

### <a name="create-a-default-multi-node-web-app-in-azure-stack"></a>Creare un'app web a più nodi predefinita in Azure Stack

Configurare l'integrazione continua ibride e la distribuzione continua (CI/CD) per distribuire App Web di Azure e Azure Stack e auto push delle modifiche per entrambi i cloud.

> [!Note]  
> Azure Stack con immagini appropriate sfruttati in esecuzione (Windows Server e SQL) e distribuzione del servizio App sono necessarie. Esaminare la documentazione del servizio App "[prima di iniziare con il servizio App in Azure Stack](../azure-stack-app-service-before-you-get-started.md)" sezione per l'operatore di Azure Stack.

### <a name="add-code-to-azure-repos"></a>Aggiungere codice al repository di Azure

Azure Repos

1. Accedere al repository di Azure con un account dotato dei diritti di creazione progetto nei repository relativi a Azure.

    Integrazione continua/distribuzione ibrida è possibile applicare al codice dell'applicazione e al codice dell'infrastruttura. Uso [modelli di Azure Resource Manager](https://azure.microsoft.com/resources/templates/) per lo sviluppo di entrambi i cloud privati e ospitati.

    ![Alt text](media/azure-stack-solution-cloud-burst/image1.JPG)

2. **Clonare il repository** , creare e aprire l'app web predefinita.

    ![Alt text](media/azure-stack-solution-cloud-burst/image2.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Creare la distribuzione di app web autonoma per i servizi App in entrambi i cloud

1.  Modificare il **WebApplication.csproj** file. Selezionare **Runtimeidentifier** e aggiungere **win10-x64**. (Vedere [Contained Deployment](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) documentazione.) 

    ![Alt text](media/azure-stack-solution-cloud-burst/image3.png)

2.  Archivia il codice nel repository di Azure con Team Explorer.

3.  Verificare che il codice dell'applicazione è stato archiviato in repository di Azure.

## <a name="create-the-build-definition"></a>Creare la definizione di compilazione

1. Accedere alla pipeline di Azure per confermare possibilità di creare le definizioni di compilazione.

2. Aggiungere **- r win10-x64** codice. Questa operazione è necessaria attivare una distribuzione autonoma con.NET Core.

    ![Alt text](media/azure-stack-solution-cloud-burst/image4.png)

3. Eseguire la compilazione. Il [compilazione di distribuzione autonoma](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) processo verrà pubblicati gli artefatti che è possono eseguire in Azure e Azure Stack.

## <a name="use-an-azure-hosted-agent"></a>Agente ospitato utilizzare un'istanza di Azure

Usare un agente ospitato nelle pipeline di Azure è un'opzione utile per compilare e distribuire le app web. Gli aggiornamenti e manutenzione viene eseguiti automaticamente da Microsoft Azure, consentendo lo sviluppo continuo e ininterrotto, test e alla distribuzione.

### <a name="manage-and-configure-the-cd-process"></a>Gestire e configurare il processo di recapito Continuo

Le pipeline di Azure e il Server di DevOps di Azure forniscono una pipeline, altamente configurabile e gestibile per le versioni in più ambienti, ad esempio sviluppo, staging, controllo qualità e ambienti di produzione; inclusa la richiesta di approvazione in specifiche fasi.

## <a name="create-release-definition"></a>Creare una definizione della versione

![Alt text](media/azure-stack-solution-cloud-burst/image5.png)

1.  Selezionare il **plus** per aggiungere una nuova versione nel **scheda rilasci** nella pagina compilazione e versione di Visual Studio online.

    ![Alt text](media/azure-stack-solution-cloud-burst/image6.png)

2. Applicare il modello di distribuzione di servizio App di Azure.

    ![Alt text](media/azure-stack-solution-cloud-burst/image7.png)

3. In Aggiungi artefatto, aggiungere l'elemento per le app di compilazione Cloud di Azure.

    ![Alt text](media/azure-stack-solution-cloud-burst/image8.png)

4. Nella scheda della Pipeline, selezionare la **fase, l'attività** collegamento dell'ambiente e impostare i valori di ambiente cloud di Azure.

    ![Alt text](media/azure-stack-solution-cloud-burst/image9.png)

5. Impostare il **nome dell'ambiente** e selezionare Azure **sottoscrizione** per l'endpoint Cloud di Azure.

    ![Alt text](media/azure-stack-solution-cloud-burst/image10.png)

6. In nome dell'ambiente, impostare la necessaria **nome del servizio app di Azure**.

    ![Alt text](media/azure-stack-solution-cloud-burst/image11.png)

7. Immettere **Hosted VS2017** nella coda dell'agente per l'ambiente cloud di Azure ospitati.

    ![Alt text](media/azure-stack-solution-cloud-burst/image12.png)

8. Distribuzione servizio App di Azure selezionare menu validi **pacchetto o cartella** per l'ambiente. Selezionare **OK** al **percorso della cartella**.

    ![Alt text](media/azure-stack-solution-cloud-burst/image13.png)

    ![Alt text](media/azure-stack-solution-cloud-burst/image14.png)

9. Salvare tutte le modifiche e tornare alla **pipeline di rilascio**.

    ![Alt text](media/azure-stack-solution-cloud-burst/image15.png)

10. Aggiungere un nuovo elemento selezionare la compilazione per l'app di Azure Stack.

    ![Alt text](media/azure-stack-solution-cloud-burst/image16.png)

11. Aggiungere un ambiente più applicando la distribuzione di servizio App di Azure.

    ![Alt text](media/azure-stack-solution-cloud-burst/image17.png)

12. Nome del nuovo ambiente Azure Stack.

    ![Alt text](media/azure-stack-solution-cloud-burst/image18.png)

13. Trovare l'ambiente Azure Stack sotto **attività** scheda.

    ![Alt text](media/azure-stack-solution-cloud-burst/image19.png)

14. Selezionare la sottoscrizione per l'endpoint di Azure Stack.

    ![Alt text](media/azure-stack-solution-cloud-burst/image20.png)

15. Impostare il nome di app web di Azure Stack come nome del servizio App.

    ![Alt text](media/azure-stack-solution-cloud-burst/image21.png)

16. Selezionare l'agente di Azure Stack.

    ![Alt text](media/azure-stack-solution-cloud-burst/image22.png)

17. In servizio App di Azure di distribuire sezione selezionare validi **pacchetto o cartella** per l'ambiente. Selezionare **OK** percorso della cartella.

    ![Alt text](media/azure-stack-solution-cloud-burst/image23.png)

    ![Alt text](media/azure-stack-solution-cloud-burst/image24.png)

18. Nella scheda variabile aggiungere una variabile denominata `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`, impostare il relativo valore come **true**e l'ambito per Azure Stack.

    ![Alt text](media/azure-stack-solution-cloud-burst/image25.png)

19. Selezionare il **Continuous** icona di trigger di distribuzione in entrambi gli elementi e abilitare le **continua ad** trigger di distribuzione.

    ![Alt text](media/azure-stack-solution-cloud-burst/image26.png)

20. Selezionare il **pre-distribuzione** icona condizioni nell'ambiente Azure Stack e impostare il trigger su **dopo il rilascio.**

21. Salvare tutte le modifiche.

> [!Note]  
> Alcune impostazioni per le attività possono essere state automaticamente definite come [variabili di ambiente](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables) durante la creazione di una definizione di versione da un modello. Queste impostazioni non possono essere modificate nelle impostazioni di attività; Per modificare queste impostazioni, invece, è necessario selezionare l'elemento padre di ambiente

## <a name="publish-to-azure-stack-via-visual-studio"></a>Pubblicare in Azure Stack tramite Visual Studio

La creazione di endpoint di una compilazione di Visual Studio Online (VSTO) è possibile distribuire App del servizio di Azure ad Azure Stack. Le pipeline di Azure si connette all'agente di compilazione, che si connette ad Azure Stack.

1.  Accedi a VSTO e passare alla pagina di impostazioni dell'app.

2.  Su **Impostazioni**, selezionare **Sicurezza**.

3.  Nelle **gruppi VSTS**, selezionare **creatori di Endpoint**.

4.  Nel **membri** scheda, seleziona **Add**.

5.  Nelle **aggiungere utenti e gruppi**, immettere un nome utente e selezionare l'utente dall'elenco di utenti.

6.  Selezionare **Save changes** (Salva modifiche).

7.  Nel **gruppi VSTS** elenco, selezionare **amministratori Endpoint**.

8.  Nel **membri** scheda, seleziona **Add**.

9.  Nelle **aggiungere utenti e gruppi**, immettere un nome utente e selezionare l'utente dall'elenco di utenti.

10. Selezionare **Save changes** (Salva modifiche).

Ora che le informazioni sull'endpoint esistente, le pipeline di Azure alla connessione di Azure Stack è pronta per l'uso. L'agente di compilazione in Azure Stack Ottiene istruzioni dalle pipeline di Azure e l'agente comunica quindi informazioni sull'endpoint per la comunicazione con Azure Stack.

## <a name="develop-the-application-build"></a>Sviluppare la build dell'applicazione

> [!Note]  
> Azure Stack con immagini appropriate sfruttati in esecuzione (Windows Server e SQL) e distribuzione del servizio App sono necessarie. Esaminare la documentazione del servizio App "[prima di iniziare con il servizio App in Azure Stack](../azure-stack-app-service-before-you-get-started.md)" sezione per l'operatore di Azure Stack.

Uso [modelli di Azure Resource Manager, ad esempio web](https://azure.microsoft.com/resources/templates/) codice dell'app da archivi di Azure per distribuire in entrambi i cloud.

### <a name="add-code-to-a-azure-repos-project"></a>Aggiungere codice al progetto repository di Azure

1.  Accedere al repository di Azure con un account dotato dei diritti di creazione progetto in Azure Stack. La schermata successiva mostra come connettersi al progetto HybridCICD.

2.  **Clonare il repository** , creare e aprire l'app web predefinita.

#### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Creare la distribuzione di app web autonoma per i servizi App in entrambi i cloud

1.  Modificare il **WebApplication.csproj** file: Selezionare **Runtimeidentifier** e quindi aggiungere win10-x64. Per altre informazioni, vedere [self-contained deployment](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) documentazione.

2.  Usare Team Explorer per controllare il codice nel repository di Azure.

3.  Verificare che il codice dell'applicazione è stato archiviato nel repository di Azure.

### <a name="create-the-build-definition"></a>Creare la definizione di compilazione

1.  Accedi alle pipeline di Azure con un account che è possibile creare una definizione di compilazione.

2.  Passare il **compilazione dell'applicazione Web** pagina per il progetto.

3.  Nelle **argomenti**, aggiungere **- r win10-x64** codice. Questa operazione è necessaria per attivare una distribuzione autonoma con.NET Core.

4.  Eseguire la compilazione. Il [compilazione di distribuzione autonoma](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) processo verrà pubblicati gli artefatti che è possono eseguire in Azure e Azure Stack.

#### <a name="use-an-azure-hosted-build-agent"></a>Uso di Azure ospitati agente di compilazione

Utilizzo di un agente di compilazione ospitato nelle pipeline di Azure è un'opzione utile per la compilazione e distribuzione di App web. Gli aggiornamenti e manutenzione dell'agente viene eseguiti automaticamente da Microsoft Azure, che consente a un ciclo di sviluppo continuo e senza interruzioni.

### <a name="configure-the-continuous-deployment-cd-process"></a>Configurare il processo di distribuzione continua (CD)

Le pipeline di Azure e il Server di DevOps di Azure forniscono una pipeline, altamente configurabile e gestibile per le versioni in più ambienti, ad esempio sviluppo, staging, controllo di qualità (QA) e produzione. Questo processo può includere che richiedono le approvazioni in specifiche fasi del ciclo di vita dell'applicazione.

#### <a name="create-release-definition"></a>Creare una definizione della versione

Creazione di una definizione di versione è il passaggio finale nell'applicazione di processo di compilazione. Questa definizione di versione viene utilizzata per creare una versione e distribuire una build.

1.  Accedi alle pipeline di Azure e passare a **compilazione e rilascio** per il progetto.

2.  Nel **rilasci** scheda, seleziona **[+]** e quindi selezionare **Crea definizione di versione**.

3.  Sul **selezionare un modello**, scegliere **distribuzione di Azure App Service**e quindi selezionare **Apply**.

4.  Sul **Aggiungi artefatto**, dalle **origine (definizione di compilazione)** selezionare l'app di compilazione Cloud di Azure.

5.  Nel **Pipeline** scheda, seleziona il **1 fase**, **1 attività** collegare **consente di visualizzare le attività dell'ambiente**.

6.  Nel **attività** , immettere Azure come il **nome ambiente** e selezionare il EP di commercianti Web AzureCloud dal **sottoscrizione di Azure** elenco.

7.  Immettere il **nome del servizio app di Azure**, ovvero `northwindtraders` nella schermata successiva.

8.  Per la fase agente, selezionare **Hosted VS2017** dalle **fronta agenta** elenco.

9.  Nelle **distribuzione servizio App di Azure**, selezionare il valido **pacchetto o cartella** per l'ambiente.

10. Nelle **seleziona File o cartella**, selezionare **OK** a **percorso**.

11. Salvare tutte le modifiche e tornare alla **Pipeline**.

12. Nel **Pipeline** scheda, seleziona **Aggiungi artefatto**e scegliere il **NorthwindCloud Traders-Vessel** dal **origine (definizione di compilazione)** elenco.

13. Sul **selezionare un modello**, aggiungere un altro ambiente. Prelievo **distribuzione di Azure App Service** e quindi selezionare **applica**.

14. Immettere `Azure Stack` come il **nome dell'ambiente**.

15. Nel **attività** scheda, trovare e selezionare Azure Stack.

16. Dal **sottoscrizione di Azure** elenco, selezionare **EP di commercianti Vessel AzureStack** per l'endpoint di Azure Stack.

17. Immettere il nome di app web di Azure Stack come le **nome del servizio App**.

18. Sotto **selezione di agenti**, selezionare **AzureStack -b Douglas Fir** dal **fronta agenta** elenco.

19. Per la **distribuzione servizio App di Azure**, selezionare il valido **pacchetto o cartella** per l'ambiente. Sul **seleziona File o cartella**, selezionare **OK** per la cartella **percorso**.

20. Nel **variabile** scheda, trovare la variabile denominata `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`. Impostare il valore della variabile su **true**e impostare il relativo ambito **Azure Stack**.

21. Nel **Pipeline** scheda, seleziona la **trigger distribuzione continua** icona per l'artefatto di commercianti NorthwindCloud-Web e impostare il **trigger distribuzione continua** a **Abilitato**. Eseguire la stessa operazione il **NorthwindCloud Traders-Vessel** dell'artefatto.

22. Per l'ambiente Azure Stack, selezionare la **condizioni pre-distribuzione** set di icone del trigger al **dopo il rilascio**.

23. Salvare tutte le modifiche.

> [!Note]  
> Alcune impostazioni per le attività di rilascio vengono definite automaticamente come [variabili di ambiente](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables) durante la creazione di una definizione di versione da un modello. Queste impostazioni non possono essere modificate nelle impostazioni di attività, ma possono essere modificate negli elementi padre di ambiente.

## <a name="create-a-release"></a>Creare una versione

1.  Nel **Pipeline** scheda, aprire il **Release** elenco e scegliere **Crea versione**.

2.  Immettere una descrizione per la versione, verificare che siano selezionati gli elementi corretti e quindi scegliere **Create**. Dopo alcuni istanti, viene visualizzato un banner che indica che è stata creata la nuova versione e il nome di versione viene visualizzato come collegamento. Scegliere il collegamento per vedere la pagina di riepilogo.

3.  La pagina di riepilogo versione Mostra i dettagli sulla versione. Nella schermata seguente per "Release-2", il **ambienti** sezione illustra le **lo stato di distribuzione** per Azure come "IN corso" e lo stato per Azure Stack è "SUCCEEDED". Quando lo stato della distribuzione per l'ambiente di Azure diventa "SUCCEEDED", viene visualizzato un banner che indica che la versione è pronta per l'approvazione. Quando una distribuzione è in sospeso oppure ha esito negativo, di colore blu **(i)** viene visualizzata l'icona informazioni. Passare il mouse sull'icona per visualizzare una finestra popup che contiene il motivo per il ritardo o un errore.

4.  Altre visualizzazioni, ad esempio l'elenco delle versioni, visualizzare un'icona che indica l'approvazione è inoltre in sospeso. La finestra popup per questa icona Mostra il nome dell'ambiente e altri dettagli relativi alla distribuzione. È facile per un amministratore, vedere lo stato complessivo di rilasci e vedere quali versioni sono in attesa di approvazione.

## <a name="monitor-and-track-deployments"></a>Monitorare e tenere traccia di distribuzioni

1.  Nel **Release-2** la pagina di riepilogo, seleziona **registri**. Durante una distribuzione, questa pagina Mostra il registro in tempo reale dall'agente. Il riquadro sinistro mostra lo stato di ogni operazione nella distribuzione per ogni ambiente.

2.  Scegliere un'icona di persona nel **azione** colonna per un'approvazione pre-distribuzione o post-distribuzione per vedere che ha approvato la distribuzione (o rifiutati) e il messaggio è forniti.

3.  Al termine della distribuzione, l'intero file di log viene visualizzato nel riquadro di destra. Selezionare una qualsiasi **passo** nel riquadro sinistro per visualizzare il file di log per un singolo passaggio, ad esempio **inizializzare processo**. La possibilità di visualizzare singoli log rende più semplice tenere traccia ed eseguire il debug di parti della distribuzione generale. **Salvare** il file di log per un passaggio, oppure **Scarica tutti i log come zip**.

4.  Aprire il **riepilogo** scheda per visualizzare informazioni generali sulla versione. Questa visualizzazione Mostra informazioni dettagliate sulla compilazione, gli ambienti in cui è stato distribuito, lo stato di distribuzione e altre informazioni sulla versione.

5.  Selezionare un collegamento di ambiente (**Azure** oppure **Azure Stack**) per visualizzare informazioni su esistente e le distribuzioni in un ambiente specifico in sospeso. Usare queste viste come un modo rapido per verificare che la stessa build è stata distribuita a entrambi gli ambienti.

6.  Aprire il **distribuite app di produzione** nel browser. Ad esempio, per il sito Web di servizi App di Azure, aprire l'URL [http://[your-app-name\]. azurewebsites.net](http:// [your-app-name].azurewebsites.net).

**Integrazione di Azure e Azure Stack offre una soluzione tra cloud scalabile**

Un servizio cloud più affidabile e flessibile fornisce sicurezza dei dati, eseguire il backup e ridondanza, disponibilità rapida e coerente, un'archiviazione scalabile e distribuzione e il routing geografico conforme. Questo processo attivato manualmente assicura affidabile ed efficiente di carico passaggio tra le app Web in hosting, garantendo la disponibilità immediata dei dati fondamentali. 

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sui modelli Cloud di Azure, vedere [Cloud Design Patterns](https://docs.microsoft.com/azure/architecture/patterns).
