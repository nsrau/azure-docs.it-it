---
title: Distribuire l'app in Azure e Azure Stack | Documenti Microsoft
description: Informazioni su come distribuire le App in Azure e Azure Stack con una pipeline di CI/CD ibrido.
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
ms.date: 05/15/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 483122f5d2e39c8595d0f28a6b937772c4ea2e50
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2018
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>Esercitazione: Distribuire le App in Azure e Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Una pipeline di ibrida integrazione continua/continui recapito (CI/CD) consente di compilare, testare e distribuire l'app per più cloud.  In questa esercitazione si creerà un ambiente di esempio a:
 
> [!div class="checklist"]
> * Avviare una nuova compilazione in base a codice commit nel repository di Visual Studio Team Services (VSTS).
> * Distribuire automaticamente il codice appena compilato in Azure globale per il test di accettazione utente.
> * Una volta il codice ha superato i test, distribuire automaticamente allo Stack di Azure.

### <a name="about-the-hybrid-delivery-build-pipe"></a>Sul recapito ibrida compilare pipe

La continuità di distribuzione dell'applicazione, sicurezza e affidabilità è essenziale per l'organizzazione e avvisi critici per il team di sviluppo. Con una pipeline di CI/CD ibrida, è possibile consolidare le pipeline tra ambiente locale e nel cloud pubblico. È possibile modificare percorso senza dover passare all'applicazione.

Questo approccio consente inoltre di mantenere un set di strumenti di sviluppo coerente. Strumenti coerenti tra l'ambiente dello Stack di Azure in locale e cloud pubblico di Azure significa che è molto più semplice per l'implementazione pratica di dev CI/CD-ROM. Le applicazioni e servizi distribuiti in Azure o Azure Stack sono intercambiabili e lo stesso codice può eseguire in questa posizione, sfruttando la possibilità di in locale e cloud pubblico caratteristiche e funzionalità.

Altre informazioni su:
 - [Che cos'è continuate?](https://www.visualstudio.com/learn/what-is-continuous-integration/)
 - [Che cos'è il recapito continuo?](https://www.visualstudio.com/learn/what-is-continuous-delivery/)


## <a name="prerequisites"></a>Prerequisiti

È necessario disporre di alcuni componenti per compilare una pipeline di CI/CD ibrido. Si può richiedere del tempo di preparazione.
 
 - Un partner di Azure OEM/hardware può distribuire un ambiente di produzione Azure Stack e tutti gli utenti possono distribuire un Kit di sviluppo dello Stack di Azure (ASDK). 
 - Anche un operatore di Stack di Azure deve distribuire il servizio App, creare i piani e alle offerte, creare una sottoscrizione tenant e aggiungere l'immagine di Windows Server 2016.

Se si dispone già di alcuni di questi componenti, verificare che soddisfino i requisiti prima di iniziare.

In questo argomento si presuppone inoltre che si dispone di conoscenza dello Stack di Azure e Azure. Se si desidera per altre informazioni prima di procedere, assicurarsi di avviare con questi argomenti:


Questa esercitazione si presuppone inoltre che si dispone di conoscenza dello Stack di Azure e Azure. 

Se si desidera ulteriori informazioni prima di procedere, è possibile iniziare con questi argomenti:
 - [Introduzione a Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Concetti chiave di Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

 - Creare una [App Web](https://docs.microsoft.com/azure/app-service/app-service-web-overview) in Azure. Prendere nota dell'URL del nuovo Web App, perché è usato in un secondo momento.

Azure Stack
 - Utilizzare un sistema Azure Stack integrato o distribuire Azure Stack Development Kit (ASDK) riportate di seguito:
    - È possibile trovare istruzioni dettagliate sulla distribuzione ASDK in "[esercitazione: distribuire ASDK utilizzando il programma di installazione](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy)"
    - È possibile automatizzare molte i passaggi di post-distribuzione ASDK con il seguente script di PowerShell [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ).

    > [!Note]  
    > L'installazione ASDK richiede un sette ore per completare, pertanto, pianificare di conseguenza.

 - Distribuire [servizio App](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) servizi PaaS allo Stack di Azure. 
 - Creare [piano/offerte](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) all'interno dell'ambiente dello Stack di Azure. 
 - Creare [sottoscrizione del tenant](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) all'interno dell'ambiente dello Stack di Azure. 
 - Creare un'App Web nella sottoscrizione tenant. Prendere nota dell'URL del nuovo Web App per usare in un secondo momento.
 - Distribuire macchina virtuale di Visual Studio Team Services, ancora all'interno della sottoscrizione tenant.
 - Windows Server 2016 macchina virtuale con .NET 3.5 necessari. Questa macchina virtuale verrà creata nello Stack Azure come agente di compilazione privata. 

### <a name="developer-tools"></a>Strumenti per sviluppatori

 - Creare un [dell'area di lavoro di Visual Studio Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services). Il processo di iscrizione crea un progetto denominato **MyFirstProject**.
 - [Installare Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) e [Accedi a Visual Studio Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).
 - Connettersi al progetto e [clonare localmente](https://www.visualstudio.com/docs/git/gitquickstart).
 
 > [!Note]  
 > È necessario Azure Stack con immagini appropriate RSS per l'esecuzione (Windows Server e SQL) e dispone di servizio App distribuito.
 
## <a name="prepare-the-private-build-and-release-agent-for-visual-studio-team-services-integration"></a>Preparare la compilazione privata e l'agente della versione per l'integrazione con Visual Studio Team Services

### <a name="prerequisites"></a>Prerequisiti

Visual Studio Team Services (VSTS) esegue l'autenticazione con Azure Resource Manager utilizzando un'entità servizio. Per Visual Studio Team Services essere in grado di effettuare il provisioning delle risorse in una sottoscrizione di Azure Stack, richiede lo stato di collaboratore.

Di seguito sono indicati i passaggi più importanti che devono essere configurati per abilitare l'autenticazione di questo tipo:

1. Entità servizio deve essere creato o può essere usata una esistente.
2. Chiavi di autenticazione devono essere creati per l'entità servizio.
3. Sottoscrizione di Azure Stack deve essere convalidata tramite il controllo di accesso basata su ruoli per consentire il nome SPN di far parte del ruolo di collaboratore.
4. Tramite gli endpoint di Azure Stack, nonché informazioni relative a SPN è necessario creare una nuova definizione di servizio in Visual Studio Team Services.

### <a name="service-principal-creation"></a>Creazione dell'entità servizio

Consultare il [la creazione dell'entità servizio](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) istruzioni per creare un'entità servizio e scegliere App/API Web per il tipo di applicazione.

### <a name="access-key-creation"></a>Creazione della chiave di accesso

Un'entità servizio è necessaria una chiave per l'autenticazione, seguire i passaggi descritti in questa sezione per generare una chiave.


1. Da **Registrazioni dell'app** in Azure Active Directory selezionare l'applicazione.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\000_01.png)

2.  Prendere nota del valore del **ID applicazione**. Tale valore verrà usato quando si configura l'endpoint del servizio in Visual Studio Team Services.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\000_02.png)

3. Per generare una chiave di autenticazione, selezionare **Impostazioni**.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\000_03.png)

4. Per generare una chiave di autenticazione selezionare **Chiavi**.
 
    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\000_04.png)

5. Specificare una descrizione e una durata per la chiave. Al termine scegliere **Salva**.
 
    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\000_05.png)

    Dopo aver salvato la chiave viene visualizzato il valore della chiave. Copiare il valore in quanto non sarà possibile recuperare la chiave in seguito. Fornire il **valore della chiave** con l'ID dell'applicazione ad accedere come l'applicazione. Salvare il valore della chiave in una posizione in cui l'applicazione possa recuperarlo.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\000_06.png)

### <a name="get-tenant-id"></a>Ottenere l'ID tenant

Come parte della configurazione di endpoint del servizio, è necessario Visual Studio Team Services il **ID Tenant** che corrisponde alla Directory AAD che è stato distribuito il timbro dello Stack di Azure. Seguire i passaggi descritti in questa sezione per ottenere l'ID del Tenant.

1. Selezionare **Azure Active Directory**.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\000_07.png)

2. Per ottenere l'ID tenant selezionare **Proprietà** per il tenanto di Azure AD.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\000_08.png)
 
3. Copiare l'**ID directory**. Questo valore è l'ID tenant.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>Concedere i diritti dell'entità servizio per distribuire le risorse nella sottoscrizione Azure Stack 

Per accedere alle risorse della propria sottoscrizione è necessario assegnare l'applicazione a un ruolo. Decidere quale ruolo rappresenti le autorizzazioni appropriate per l'applicazione. Per informazioni sui ruoli disponibili, vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

È possibile impostare l'ambito al livello della sottoscrizione, del gruppo di risorse o della risorsa. Le autorizzazioni vengono ereditate a livelli inferiori dell'ambito. Se ad esempio si aggiunge un'applicazione al ruolo Lettore per un gruppo di risorse, l'applicazione può leggere il gruppo di risorse e le risorse in esso contenute.

1. Passare al livello dell'ambito al quale si vuole assegnare l'applicazione. Ad esempio, per assegnare un ruolo a un ambito della sottoscrizione, selezionare **Sottoscrizioni**. In alternativa è possibile selezionare una risorsa o un gruppo di risorse.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\000_10.png)

2. Selezionare il **sottoscrizione** (gruppo di risorse o risorse) per assegnare l'applicazione.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\000_11.png)

3. Selezionare **Controllo di accesso (IAM)**.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\000_12.png)

4. Selezionare **Aggiungi**.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\000_13.png)

5. Selezionare il ruolo che si desidera assegnare all'applicazione. La figura seguente mostra la **proprietario** ruolo.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\000_14.png)

6. Per impostazione predefinita, le applicazioni di Azure Active Directory non sono visualizzate nelle opzioni disponibili. Per individuare l'applicazione, è necessario **consente di specificare il nome** nel campo della ricerca. Selezionarlo.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\000_16.png)

7. Selezionare **Salva** per completare l'assegnazione del ruolo. L'applicazione ora compare nell'elenco degli utenti assegnati a un ruolo per quell'ambito.

### <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Azure Role-Based Access controllo (RBAC) consente la gestione di accesso con granularità fine per Azure. L'uso del Controllo degli accessi in base al ruolo permette di concedere agli utenti solo il livello di accesso necessario per lavorare. Per ulteriori informazioni su Role-Based Access Control, vedere [gestire l'accesso alle risorse di sottoscrizione Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

### <a name="vsts-agent-pools"></a>Pool di agenti di Visual Studio Team Services

Anziché gestire singolarmente ogni agente, organizzare gli agenti in pool di agenti. Un pool di agenti definisce il limite di condivisione per tutti gli agenti nel pool. In Visual Studio Team Services, i pool di agenti nell'ambito all'account di Visual Studio Team Services. Pertanto, è possibile condividere un pool di agenti tra progetti team. Per ulteriori informazioni e un'esercitazione su come creare pool di agenti di Visual Studio Team Services, vedere [creare i pool di agenti e code](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts).

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>Aggiungere un token di accesso personale (PAT) per lo Stack di Azure

1. Accedere al proprio account di Visual Studio Team Services e selezionare il nome del profilo account.
2. Selezionare **Gestisci sicurezza** alla pagina di creazione del token di accesso.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\000_17.png)

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\000_18.png)

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\000_18a.png)

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\000_18b.png)

3. Copiare il token.
    
    > [!Note]  
    > Ottenere le informazioni del token. Non verrà visualizzato dopo aver se si lascia questa schermata. 
    
    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\000_19.png)
    

### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Installare che l'agente di compilazione di Visual Studio Team Services nello Stack di Azure in un Server di compilazione ospitato

1.  Connettersi al server di compilazione che è stato distribuito nell'host dello Stack di Azure.

2.  Scaricare e distribuire l'agente di compilazione come servizio con personali token (PAT) di accesso ed eseguire come account di amministratore di macchina virtuale.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\010_downloadagent.png)

3. Passare alla cartella dell'agente di compilazione estratti. Eseguire la **run.cmd** file da un prompt dei comandi con privilegi elevati. 

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\000_20.png)

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\000_21.png)

4.  Al termine il run.cmd la cartella con il contenuto estratto dovrebbe essere simile al seguente:

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\009_token_file.png)

    È ora possibile visualizzare l'agente nella cartella di Visual Studio Team Services.

## <a name="endpoint-creation-permissions"></a>Autorizzazioni di creazione di endpoint

Gli utenti possono creare gli endpoint in modo da VSTO compilazioni possono distribuire le applicazioni di servizio di Azure allo stack. Visual Studio Team Services si connette all'agente di compilazione, quindi si connette con lo Stack di Azure. 

![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\012_securityendpoints.png)

1. Nel **impostazioni** dal menu **protezione**.
2. Nel **gruppi VSTS** elenco a sinistra, seleziona **creatori di Endpoint**. 

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\013_endpoint_creators.png)

3. Nel **membri** , selezionare **+ Aggiungi**. 

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\014_members_tab.png)

4. Digitare un nome utente e selezionare l'utente dall'elenco.
5. Fare clic su **Salva modifiche**.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\015_save_endpoint.png)

6. Nel **gruppi VSTS** elenco a sinistra, seleziona **amministratori Endpoint**.
7. Nel **membri** , selezionare **+ Aggiungi**.
8. Digitare un nome utente e selezionare l'utente dall'elenco.
9. Fare clic su **Salva modifiche**.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\016_save_changes.png)

    L'agente di compilazione nello Stack di Azure riceve istruzioni dal Visual Studio Team Services, che comunica quindi informazioni sull'endpoint per la comunicazione con lo Stack di Azure. 
    
    Visual Studio Team Services a connessione dello Stack di Azure è ora pronto.

## <a name="develop-your-application"></a>Sviluppare l'applicazione

Consente di impostare CI/CD-ROM per distribuire l'App Web in Azure e Azure Stack e auto push delle modifiche per entrambi i cloud ibridi.

> [!Note]  
> È necessario Azure Stack con immagini appropriate RSS per l'esecuzione (Windows Server e SQL) e dispone di servizio App distribuito. Rivedere la documentazione di servizio App sezione "Prerequisiti" di Azure Stack operatore requisiti.

### <a name="add-code-to-vsts-project"></a>Aggiungere codice al progetto di Visual Studio Team Services

1. Accedi a Visual Studio con un account che dispone dei diritti di creazione progetto nello Stack di Azure.

    Ibrida CI/CD possibile applicare al codice dell'applicazione e il codice dell'infrastruttura. Uso [modelli di gestione risorse di Azure come web ](https://azure.microsoft.com/resources/templates/) codice dell'app da Visual Studio Team Services per entrambi i cloud.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\017_connect_to_project.png)

2. **Clonare il repository** , creare e aprire l'app web predefinita.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Creare la distribuzione di app web indipendente per i servizi di App in entrambi i cloud

1. Modificare il **WebApplication.csproj** file: selezionare **Runtimeidentifier** e aggiungere `win10-x64.` per ulteriori informazioni, vedere [distribuzione indipendente](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) documentazione .

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\019_runtimeidentifer.png)

2. Controllare il codice in Visual Studio Team Services con Team Explorer.

3. Verificare che il codice dell'applicazione è stato archiviato in Visual Studio Team Services. 

### <a name="create-the-build-definition"></a>Creare la definizione di compilazione

1. Accedi a Visual Studio Team Services per confermare possibilità di creare definizioni di compilazione.

2. Aggiungere **win10-x64 - r** codice. Questa operazione è necessaria attivare una distribuzione autonoma con .net Core. 

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\020_publish_additions.png)

3. Eseguire la compilazione. Il [compilazione di distribuzione indipendente](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) processo pubblicheranno gli elementi che possono eseguire in Azure e Azure Stack.

### <a name="using-an-azure-hosted-agent"></a>Utilizzo di un agente ospitato di Azure

Utilizzo di un agente ospitato in Visual Studio Team Services è una soluzione comoda per compilare e distribuire le app web. Manutenzione e gli aggiornamenti vengono eseguiti automaticamente da Microsoft Azure, abilitando il continuo e ininterrotto sviluppo, testing e distribuzione.

### <a name="manage-and-configure-the-continuous-deployment-cd-process"></a>Gestire e configurare il processo di distribuzione continua (CD)

Visual Studio Team Services (VSTS) e Team Foundation Server (TFS) forniscono una pipeline, altamente configurabile e gestibile per le versioni per più ambienti, ad esempio sviluppo, gestione temporanea, QA e ambienti di produzione; tra cui richiedere approvazioni in specifiche fasi.

### <a name="create-release-definition"></a>Creare una definizione della versione

![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\021a_releasedef.png)

1. Selezionare il  **\[ +]** per aggiungere una nuova versione sotto la **scheda versioni** della pagina di compilazione e versione di Visual Studio online.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\102.png)

2. Applicare il **distribuzione di servizio App di Azure** modello.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\103.png)

3. Nel menu a discesa elemento Add, **aggiungere l'elemento** per l'app di compilazione Cloud di Azure.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\104.png)

4. Nella scheda della Pipeline, selezionare il **fase**, **attività** collegare dell'ambiente e impostare valori di ambiente per il cloud di Azure.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\105.png)

5. Impostare il **nome dell'ambiente** e selezionare Azure **sottoscrizione** per l'endpoint Cloud di Azure.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\106.png)

6. In nome dell'ambiente, impostare la **nome del servizio app di Azure**.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\107.png)

7. Immettere **ospitato VS2017** nella coda dell'agente per l'ambiente cloud di Azure ospitati.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\108.png)

8. Nel menu di distribuzione di servizio App di Azure, selezionare la validità **pacchetto o cartella** per l'ambiente. Selezionare **OK** alla **percorso della cartella**.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\109.png)

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\110.png)

9. Salvare tutte le modifiche e tornare alla **pipeline di tipo versione**.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\111.png)

10. Aggiungere un **nuovo artefatto** selezionando la compilazione per l'app Azure Stack.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\112.png)

11. Aggiungere un ambiente più applicato il **distribuzione di servizio App di Azure**.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\113.png)

12. Denominare il nuovo ambiente **Azure Stack**.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\114.png)

13. Trovare l'ambiente dello Stack di Azure sotto **attività** scheda.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\115.png)

14. Selezionare il **sottoscrizione** per l'endpoint dello Stack di Azure.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\116.png)

15. Impostare il nome dell'app web di Azure Stack come le **nome del servizio App**.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\117.png)

16. Selezionare il **agente di Azure Stack**.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\118.png)

17. Distribuire Azure App Service sezione selezionare validi **pacchetto o cartella** per l'ambiente. Scegliere OK per **percorso cartella**.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\119.png)

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\120.png)

18. Nella scheda variabile aggiungere una variabile denominata **VSTS_ARM_REST_IGNORE_SSL_ERRORS**, impostarne il valore come **true**e definire l'ambito per **Azure Stack**.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\121.png)

19. Selezionare il **continuo** distribuzione attivare icona in entrambi gli elementi e abilitare il trigger di distribuzione continua.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\122.png)

20. Selezionare il **pre-distribuzione** icona condizioni di azure stack ambiente e impostare il trigger **dopo il rilascio**.

21. Salvare tutte le modifiche.

> [!Note]  
> Alcune impostazioni per le attività possono essere state automaticamente definite come [variabili di ambiente](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) al momento della creazione di una definizione di versione da un modello. Queste impostazioni non possono essere modificate nelle impostazioni di attività; In alternativa, è necessario selezionare l'elemento ambiente padre per modificare queste impostazioni.

## <a name="create-a-release"></a>Crea una versione

Dopo aver completato le modifiche alla definizione di versione, è necessario avviare la distribuzione. A tale scopo, si crea una versione dalla definizione di versione. Una versione può essere creata automaticamente; ad esempio, il trigger di una distribuzione continua è impostato nella definizione di versione. Ciò significa che dopo la modifica del codice sorgente verrà avviata una nuova compilazione e, dal che, una nuova versione. Tuttavia, in questa sezione si creerà una nuova versione manualmente.

1. Aprire la **rilasciare** elenco a discesa e scegliere **Crea versione**.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\200.png)
 
2. Immettere una descrizione per la versione, verificare che siano selezionati gli elementi corretti e quindi scegliere **crea**. Dopo qualche istante, viene visualizzato un banner di che indica che la nuova versione è stata creata. Scegliere il collegamento (il nome della versione).

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\201.png)
 
3. Viene visualizzata la pagina di riepilogo di rilascio con i dettagli della versione. Nel **ambienti** sezione, verrà visualizzato lo stato della distribuzione per l'ambiente "QA" modifica da "IN corso" a "SUCCEEDED" e, a questo punto, viene visualizzata un'intestazione che indica che la versione è in attesa di approvazione. Quando una distribuzione in un ambiente è in sospeso o non è riuscita, un blu (i) viene visualizzata l'icona informazioni. Scegliere questa opzione per visualizzare una finestra popup che contiene il motivo.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\202.png)

Altre visualizzazioni, ad esempio l'elenco delle versioni, visualizzare un'icona che indica l'approvazione è in sospeso. L'icona Mostra una finestra popup contenente il nome dell'ambiente e altri dettagli quando si passa a esso. Ciò semplifica l'amministratore verificare quali versioni sono in attesa di approvazione, nonché lo stato complessivo di tutte le versioni.

### <a name="monitor-and-track-deployments"></a>Distribuzioni di monitorare e tenere traccia

In questa sezione, si noterà come è possibile monitorare e tenere traccia delle distribuzioni - in questo esempio due siti Web di servizi di App di Azure - della versione è stato creato nella sezione precedente.

1. Nella pagina di riepilogo del rilascio, scegli il **registri** collegamento. Durante la distribuzione viene eseguita, questa pagina Mostra il registro in tempo reale dall'agente e, nel riquadro sinistro, l'indicazione dello stato di ogni operazione del processo di distribuzione per ogni ambiente.

    Scegliere l'icona nella **azione** colonna per un'approvazione pre-distribuzione o post-distribuzione visualizzare i dettagli della persona approvato (o rifiutati) la distribuzione e il messaggio di tale forniti dall'utente.

2. Al termine della distribuzione, l'intero file di log viene visualizzato nel riquadro di destra. Selezionare una qualsiasi del **i passaggi del processo** nel riquadro sinistro per visualizzare solo i log contenuto del file per il passaggio. Questo rende più semplice tenere traccia ed eseguire il debug delle singole parti di distribuzione. In alternativa, è possibile scaricare i singoli file di log o un file zip di tutti i file di log, dalle icone e i collegamenti nella pagina.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\203.png)
 
3. Aprire la **riepilogo** tab per visualizzare i dettagli generali della versione. Mostra i dettagli di compilazione e gli ambienti in cui che è stato distribuito, insieme alle altre informazioni sulla versione e lo stato della distribuzione.

4. Selezionare tutte le **collegamenti ambiente** per visualizzare ulteriori dettagli sui esistente e le distribuzioni a un ambiente specifico in sospeso. È possibile utilizzare queste pagine per verificare che la stessa build è stata distribuita per entrambi gli ambienti.

5. Aprire la **distribuito dell'applicazione di produzione** nell'esplorazione. Ad esempio, per un sito Web di servizi di App di Azure, dall'URL `http://[your-app-name].azurewebsites.net`.

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sui pattern di Cloud di Azure, vedere [schemi progettuali Cloud](https://docs.microsoft.com/azure/architecture/patterns).
