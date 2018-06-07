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
ms.openlocfilehash: 41e6f64ada7c95674cc2573048eef8afc83e4385
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604353"
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>Esercitazione: distribuire le App in Azure e Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Informazioni su come distribuire un'applicazione in Azure e Azure Stack utilizzando una pipeline di ibrida integrazione continua/continui recapito (CI/CD).

In questa esercitazione si creerà un ambiente di esempio a:

> [!div class="checklist"]
> * Avviare una nuova compilazione in base a codice commit nel repository di Visual Studio Team Services (VSTS).
> * Distribuire automaticamente l'app in Azure globale per il test di accettazione utente.
> * Quando il codice passi di test, distribuire automaticamente l'app allo Stack di Azure.

## <a name="benefits-of-the-hybrid-delivery-build-pipe"></a>Vantaggi del recapito ibrida compilare pipe

Continuità aziendale, protezione e affidabilità sono gli elementi principali della distribuzione dell'applicazione. Questi elementi sono essenziali per l'organizzazione e avvisi critici per il team di sviluppo. Una pipeline di CI/CD ibrido consente di consolidare la pipe di compilazione tra ambiente locale e nel cloud pubblico. Un modello di distribuzione ibrida consente inoltre di modificare i percorsi di distribuzione senza modificare l'applicazione.

Altri vantaggi derivanti dall'utilizzo l'approccio ibrido sono:

* È possibile mantenere un set di strumenti di sviluppo coerente tra l'ambiente dello Stack di Azure in locale e cloud pubblico di Azure.  Un set di strumenti comuni rende più semplice implementare procedure consigliate e modelli di elemento di configurazione/CD-ROM.
* Le applicazioni e servizi distribuiti in Azure o Azure Stack sono intercambiabili e lo stesso codice può eseguire in questa posizione. È possibile sfruttare le caratteristiche in locale e le funzionalità di cloud pubblico.

Per altre informazioni sulla CI e CD:

* [Che cos'è continuate?](https://www.visualstudio.com/learn/what-is-continuous-integration/)
* [Che cos'è il recapito continuo?](https://www.visualstudio.com/learn/what-is-continuous-delivery/)

## <a name="prerequisites"></a>Prerequisiti

È necessario disporre dei componenti in grado di compilare una pipeline di CI/CD ibrido. Operazione richiederà un tempo preparare i componenti seguenti:

* Un partner di Azure OEM/hardware è possibile distribuire un ambiente di produzione Azure Stack. Tutti gli utenti possono distribuire il Kit di sviluppo dello Stack di Azure (ASDK).
* Un operatore di Stack di Azure deve anche: distribuire il servizio App, creare i piani e alle offerte, creare una sottoscrizione tenant e aggiungere l'immagine di Windows Server 2016.

>[!NOTE]
>Se si dispone già di alcuni di questi componenti distribuiti, assicurarsi che soddisfino tutti i requisiti prima di iniziare questa esercitazione.

Questa esercitazione si presuppone una discreta conoscenza di base dello Stack di Azure e Azure. Per altre informazioni prima di iniziare l'esercitazione, leggere gli articoli seguenti:

* [Introduzione a Azure](https://azure.microsoft.com/overview/what-is-azure/)
* [Concetti chiave di Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure-requirements"></a>Requisiti di Azure

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
* Creare una [App Web](https://docs.microsoft.com/azure/app-service/app-service-web-overview) in Azure. Assicurarsi di prendere nota dell'URL dell'App Web, è necessario utilizzarla nell'esercitazione.

### <a name="azure-stack-requirements"></a>Requisiti per Azure Stack

* Utilizzare un sistema Azure Stack integrato o distribuire il Kit di sviluppo dello Stack di Azure (ASDK). Per distribuire il ASDK:
    * Il [esercitazione: distribuire ASDK utilizzando il programma di installazione](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy) vengono fornite istruzioni dettagliate di distribuzione.
    * Usare la [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ) script di PowerShell per automatizzare i passaggi di post-distribuzione ASDK.

    > [!Note]
    > L'installazione ASDK richiede circa 7 ore per completare, pertanto, pianificare di conseguenza.

 * Distribuire [servizio App](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) servizi PaaS allo Stack di Azure.
 * Creare [piano/offerte](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) nello Stack di Azure.
 * Creare una [sottoscrizione del tenant](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) nello Stack di Azure.
 * Creare un'App Web nella sottoscrizione tenant. Prendere nota dell'URL del nuovo Web App per usare in un secondo momento.
 * Distribuire Virtual Machine VSTS nella sottoscrizione tenant.
* Fornire un'immagine di Windows Server 2016 con .NET 3.5 per una macchina virtuale (VM). Questa macchina virtuale verrà creata nello Stack Azure come un agente di compilazione privata.

### <a name="developer-tool-requirements"></a>Requisiti dello strumento per sviluppatori

* Creare un [dell'area di lavoro di Visual Studio Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services). Il processo di iscrizione crea un progetto denominato **MyFirstProject**.
* [Installare Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) e [Accedi a Visual Studio Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).
* Connettersi al progetto e [clonarlo localmente](https://www.visualstudio.com/docs/git/gitquickstart).

 > [!Note]
 > L'ambiente dello Stack di Azure richiede le immagini corrette RSS per eseguire Windows Server e SQL Server. È necessario che abbia App servizio distribuito.

## <a name="prepare-the-private-build-and-release-agent-for-visual-studio-team-services-integration"></a>Preparare la compilazione privata e l'agente della versione per l'integrazione con Visual Studio Team Services

### <a name="prerequisites"></a>Prerequisiti

Visual Studio Team Services (VSTS) esegue l'autenticazione con Azure Resource Manager utilizzando un'entità servizio. Visual Studio Team Services deve avere il **collaboratore** ruolo alle risorse di effettuare il provisioning di una sottoscrizione di Azure Stack.

I passaggi seguenti descrivono gli elementi necessari per configurare l'autenticazione:

1. Creare un'entità servizio oppure utilizzare un'entità servizio esistente.
2. Creare le chiavi di autenticazione per l'entità servizio.
3. Convalidare la sottoscrizione dello Stack di Azure tramite il controllo di accesso basata su ruoli per consentire il nome dell'entità di servizio (SPN) da parte del ruolo di collaboratore.
4. Creare una nuova definizione di servizio in Visual Studio Team Services utilizzando le informazioni di SPN e l'endpoint dello Stack di Azure.

### <a name="create-a-service-principal"></a>Creare un'entità servizio

Fare riferimento ai [la creazione dell'entità servizio](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) istruzioni per creare un'entità servizio e quindi scegliere **App/API Web** per il tipo di applicazione.

### <a name="create-an-access-key"></a>Creare una chiave di accesso

Un'entità servizio richiede una chiave per l'autenticazione. Utilizzare la procedura seguente per generare una chiave.

1. Da **Registrazioni dell'app** in Azure Active Directory selezionare l'applicazione.

    ![Selezionare l'applicazione](media\azure-stack-solution-hybrid-pipeline\000_01.png)

2. Prendere nota del valore del **ID applicazione**. Tale valore verrà usato quando si configura l'endpoint del servizio in Visual Studio Team Services.

    ![ID applicazione](media\azure-stack-solution-hybrid-pipeline\000_02.png)

3. Per generare una chiave di autenticazione, selezionare **Impostazioni**.

    ![Modificare le impostazioni dell'app](media\azure-stack-solution-hybrid-pipeline\000_03.png)

4. Per generare una chiave di autenticazione selezionare **Chiavi**.

    ![Configurare le impostazioni della chiave](media\azure-stack-solution-hybrid-pipeline\000_04.png)

5. Fornire una descrizione per la chiave e impostare la durata della chiave. Al termine scegliere **Salva**.

    ![Durata e descrizione della chiave](media\azure-stack-solution-hybrid-pipeline\000_05.png)

    Dopo aver salvato la chiave, la chiave **valore** viene visualizzato. Copiare questo valore perché non è possibile ottenere questo valore in un secondo momento. Fornire il **valore della chiave** con l'ID dell'applicazione ad accedere come l'applicazione. Salvare il valore della chiave in una posizione in cui l'applicazione possa recuperarlo.

    ![VALORE di chiave](media\azure-stack-solution-hybrid-pipeline\000_06.png)

### <a name="get-the-tenant-id"></a>Ottenere l'ID tenant

Come parte della configurazione di endpoint del servizio, è necessario Visual Studio Team Services il **ID Tenant** che corrisponde alla Directory AAD che viene distribuito il timbro Azure Stack. Utilizzare la procedura seguente per ottenere l'ID del Tenant.

1. Selezionare **Azure Active Directory**.

    ![Azure Active Directory per il tenant](media\azure-stack-solution-hybrid-pipeline\000_07.png)

2. Per ottenere l'ID tenant selezionare **Proprietà** per il tenanto di Azure AD.

    ![Visualizzare le proprietà tenant](media\azure-stack-solution-hybrid-pipeline\000_08.png)

3. Copiare l'**ID directory**. Questo valore è l'ID tenant.

    ![ID directory](media\azure-stack-solution-hybrid-pipeline\000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>Concedere i diritti dell'entità servizio per distribuire le risorse nella sottoscrizione Azure Stack

Per accedere alle risorse della propria sottoscrizione è necessario assegnare l'applicazione a un ruolo. Decidere quale ruolo rappresenta le autorizzazioni migliore per l'applicazione. Per informazioni sui ruoli disponibili, vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

È possibile impostare l'ambito al livello della sottoscrizione, del gruppo di risorse o della risorsa. Le autorizzazioni vengono ereditate a livelli inferiori dell'ambito. Aggiunta di un'applicazione per il ruolo di lettore per un gruppo di risorse, ad esempio, significa che è possibile leggere il gruppo di risorse e le relative risorse.

1. Passare al livello dell'ambito al quale si vuole assegnare l'applicazione. Ad esempio, per assegnare un ruolo a un ambito della sottoscrizione, selezionare **Sottoscrizioni**.

    ![Selezionare le sottoscrizioni](media\azure-stack-solution-hybrid-pipeline\000_10.png)

2. In **sottoscrizione**, selezionare Visual Studio Enterprise.

    ![Visual Studio Enterprise](media\azure-stack-solution-hybrid-pipeline\000_11.png)

3. In Visual Studio Enterprise, selezionare **controllo di accesso (IAM)**.

    ![Controllo di accesso (IAM)](media\azure-stack-solution-hybrid-pipeline\000_12.png)

4. Selezionare **Aggiungi**.

    ![Add](media\azure-stack-solution-hybrid-pipeline\000_13.png)

5. In **aggiungere le autorizzazioni**, selezionare il ruolo che si desidera assegnare all'applicazione. In questo esempio, il **proprietario** ruolo.

    ![Ruolo di proprietario](media\azure-stack-solution-hybrid-pipeline\000_14.png)

6. Per impostazione predefinita, le applicazioni di Azure Active Directory non sono visualizzate nelle opzioni disponibili. Per individuare l'applicazione, è necessario fornire il nome di **selezionare** campo per cercarlo. Selezionare l'app.

    ![Risultato della ricerca App](media\azure-stack-solution-hybrid-pipeline\000_16.png)

7. Selezionare **Salva** per completare l'assegnazione del ruolo. L'applicazione ora compare nell'elenco degli utenti assegnati a un ruolo per quell'ambito.

### <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Azure Role-Based Access controllo (RBAC) offre la gestione di accesso con granularità fine per Azure. Tramite RBAC, è possibile controllare il livello di accesso che gli utenti devono svolgere il proprio lavoro. Per ulteriori informazioni su Role-Based Access Control, vedere [gestire l'accesso alle risorse di sottoscrizione Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

### <a name="vsts-agent-pools"></a>Pool di agenti di Visual Studio Team Services

Anziché gestire separatamente ogni agente, è possibile organizzare gli agenti in pool di agenti. Un pool di agenti definisce il limite di condivisione per tutti gli agenti nel pool. In Visual Studio Team Services, i pool di agenti sono limitati all'account di Visual Studio Team Services, il che significa che è possibile condividere un pool di agenti tra progetti team. Per ulteriori informazioni sui pool di agenti, vedere [creare i pool di agenti e code](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts).

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>Aggiungere un Token di accesso personale (PAT) per Azure Stack

Creare un Token di accesso personale per accedere a Visual Studio Team Services.

1. Accedere al proprio account di Visual Studio Team Services e selezionare il nome del profilo account.
2. Selezionare **Gestisci sicurezza** alla pagina di creazione del token di accesso.

    ![Accesso degli utenti](media\azure-stack-solution-hybrid-pipeline\000_17.png)

    ![Selezionare il progetto team](media\azure-stack-solution-hybrid-pipeline\000_18.png)

    ![Aggiungere un token di accesso personale](media\azure-stack-solution-hybrid-pipeline\000_18a.png)

    ![Creare token](media\azure-stack-solution-hybrid-pipeline\000_18b.png)

3. Copiare il token.

    > [!Note]
    > Salvare le informazioni del token. Queste informazioni non vengono memorizzate e non verranno visualizzate quando si chiude la pagina web.

    ![Token di accesso personale](media\azure-stack-solution-hybrid-pipeline\000_19.png)

### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Installare che l'agente di compilazione di Visual Studio Team Services nello Stack di Azure in un Server di compilazione ospitato

1. Connettersi al server di compilazione che è stato distribuito nell'host dello Stack di Azure.
2. Scaricare e distribuire l'agente di compilazione come servizio con personali token (PAT) di accesso ed eseguire come account di amministratore di macchina virtuale.

    ![Scaricare l'agente di compilazione](media\azure-stack-solution-hybrid-pipeline\010_downloadagent.png)

3. Passare alla cartella per l'agente di compilazione estratti. Eseguire la **run.cmd** file da un prompt dei comandi con privilegi elevati.

    ![Agente di compilazione estratti](media\azure-stack-solution-hybrid-pipeline\000_20.png)

    ![Registrare l'agente di compilazione](media\azure-stack-solution-hybrid-pipeline\000_21.png)

4. Al termine dell'esecuzione di run.cmd, la cartella dell'agente di compilazione viene aggiornata con i file aggiuntivi. La cartella con il contenuto estratto dovrebbe essere simile al seguente:

    ![L'agente di compilazione aggiornamento della cartella](media\azure-stack-solution-hybrid-pipeline\009_token_file.png)

    È possibile visualizzare l'agente nella cartella di Visual Studio Team Services.

## <a name="endpoint-creation-permissions"></a>Autorizzazioni di creazione di endpoint

Tramite la creazione di endpoint, una compilazione di Visual Studio Online (VSTO) è possibile distribuire le applicazioni di servizio di Azure allo Stack di Azure. Visual Studio Team Services si connette all'agente di compilazione, si connette allo Stack di Azure.

![App di esempio NorthwindCloud nel VSTO](media\azure-stack-solution-hybrid-pipeline\012_securityendpoints.png)

1. Accedi a VSTO e passare alla pagina di impostazioni dell'app.
2. Nel **impostazioni**, selezionare **protezione**.
3. In **gruppi VSTS**, selezionare **creatori di Endpoint**.

    ![Creatori di NorthwindCloud Endpoint](media\azure-stack-solution-hybrid-pipeline\013_endpoint_creators.png)

4. Nel **membri** , selezionare **Add**.

    ![Aggiungere un membro](media\azure-stack-solution-hybrid-pipeline\014_members_tab.png)

5. In **aggiungere utenti e gruppi**, immettere un nome utente e selezionare l'utente dall'elenco di utenti.
6. Selezionare **Save changes** (Salva modifiche).
7. Nel **gruppi VSTS** elenco, selezionare **amministratori Endpoint**.

    ![Amministratori NorthwindCloud Endpoint](media\azure-stack-solution-hybrid-pipeline\015_save_endpoint.png)

8. Nel **membri** , selezionare **Add**.
9. In **aggiungere utenti e gruppi**, immettere un nome utente e selezionare l'utente dall'elenco di utenti.
10. Selezionare **Save changes** (Salva modifiche).

Ora che le informazioni sull'endpoint, VSTS alla connessione di Stack di Azure è pronta all'uso. L'agente di compilazione nello Stack di Azure Ottiene le istruzioni da Visual Studio Team Services e l'agente comunica quindi informazioni sull'endpoint per la comunicazione con lo Stack di Azure.

![L'agente di compilazione](media\azure-stack-solution-hybrid-pipeline\016_save_changes.png)

## <a name="develop-your-application-build"></a>Sviluppare la compilazione dell'applicazione

In questa parte dell'esercitazione effettuare le operazioni seguenti:

* Aggiungere codice a un progetto di Visual Studio Team Services.
* Creare la distribuzione di app web indipendente.
* Configurare il processo di distribuzione continua

> [!Note]
 > L'ambiente dello Stack di Azure richiede le immagini corrette RSS per eseguire Windows Server e SQL Server. È necessario che abbia App servizio distribuito. Rivedere la documentazione di servizio App sezione "Prerequisiti" di Azure Stack operatore requisiti.

Ibrida CI/CD possibile applicare al codice dell'applicazione e il codice dell'infrastruttura. Uso [modelli di gestione risorse di Azure come web ](https://azure.microsoft.com/resources/templates/) codice dell'app da Visual Studio Team Services per distribuire in entrambi i cloud.

### <a name="add-code-to-a-vsts-project"></a>Aggiungere codice a un progetto di Visual Studio Team Services

1. Accedi a Visual Studio Team Services con un account che dispone dei diritti di creazione progetto nello Stack di Azure. Nella schermata successiva viene illustrato come connettersi al progetto HybridCICD.

    ![Connettersi a un progetto](media\azure-stack-solution-hybrid-pipeline\017_connect_to_project.png)

2. **Clonare il repository** , creare e aprire l'app web predefinita.

    ![Repository di clone](media\azure-stack-solution-hybrid-pipeline\018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Creare la distribuzione di app web indipendente per i servizi di App in entrambi i cloud

1. Modificare il **WebApplication.csproj** file: selezionare **Runtimeidentifier** e quindi aggiungere `win10-x64.` per ulteriori informazioni, vedere [distribuzione indipendente](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) documentazione.

    ![Configurare Runtimeidentifier](media\azure-stack-solution-hybrid-pipeline\019_runtimeidentifer.png)

2. Usare Team Explorer per controllare il codice in Visual Studio Team Services.

3. Verificare che il codice dell'applicazione è stato verificato in Visual Studio Team Services.

### <a name="create-the-build-definition"></a>Creare la definizione di compilazione

1. Accedi a Visual Studio Team Services con un account che è possibile creare una definizione di compilazione.
2. Passare il **compilare applicazioni Web** pagina per il progetto.

3. In **gli argomenti**, aggiungere **win10-x64 - r** codice. Questa operazione è necessaria per attivare una distribuzione autonoma con .net Core.

    ![Aggiungere la definizione di compilazione di argomento](media\azure-stack-solution-hybrid-pipeline\020_publish_additions.png)

4. Eseguire la compilazione. Il [compilazione di distribuzione indipendente](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) processo pubblicheranno gli elementi che possono eseguire in Azure e Azure Stack.

### <a name="use-an-azure-hosted-build-agent"></a>Utilizzo di Azure ospitati agente di compilazione

Utilizzo di un agente di compilazione ospitato in Visual Studio Team Services è una soluzione comoda per la compilazione e distribuzione di App web. Gli aggiornamenti e manutenzione dell'agente viene eseguiti automaticamente da Microsoft Azure, che consente a un ciclo di sviluppo continuo e senza interruzioni.

### <a name="configure-the-continuous-deployment-cd-process"></a>Configurare il processo di distribuzione continua (CD)

Visual Studio Team Services (VSTS) e Team Foundation Server (TFS) forniscono una pipeline, altamente configurabile e gestibile per le versioni per più ambienti, ad esempio sviluppo, gestione temporanea, qualità (QA) e produzione. Questo processo può includere che richiedono le approvazioni in specifiche fasi del ciclo di vita dell'applicazione.

### <a name="create-release-definition"></a>Creare una definizione della versione

La creazione di una definizione di versione è il passaggio finale nell'applicazione processo di compilazione. Questa definizione di versione viene utilizzata per creare una versione e distribuire una compilazione.

1. Accedi a Visual Studio Team Services e passare a **compilazione e versione** per il progetto.
2. Nel **versioni** , selezionare  **\[ +]** e quindi scegliere **Crea definizione di versione**.

   ![Creare una definizione della versione](media\azure-stack-solution-hybrid-pipeline\021a_releasedef.png)

3. Nel **selezionare un modello**, scegliere **distribuzione di servizio App di Azure**, quindi selezionare **applica**.

    ![Applica il modello](media\azure-stack-solution-hybrid-pipeline\102.png)

4. Nel **Aggiungi artefatto**, dal **origine (definizione di compilazione)** dal menu a discesa selezionare l'app di compilazione Cloud di Azure.

    ![Aggiungere un elemento](media\azure-stack-solution-hybrid-pipeline\103.png)

5. Nel **Pipeline** , selezionare il **1 fasi**, **1 attività** collegare **consente di visualizzare le attività di ambiente**.

    ![Attività relative alle viste di pipeline](media\azure-stack-solution-hybrid-pipeline\104.png)

6. Nel **attività** , immettere Azure come il **nome dell'ambiente** e selezionare il EP Traders Web cloud dal **sottoscrizione di Azure** elenco a discesa.

    ![Impostare le variabili di ambiente](media\azure-stack-solution-hybrid-pipeline\105.png)

7. Immettere il **nome del servizio app di Azure**, ovvero "northwindtraders" nell'acquisizione schermata successiva.

    ![Nome del servizio App](media\azure-stack-solution-hybrid-pipeline\106.png)

8. Per la fase dell'agente, selezionare **ospitato VS2017** dal **coda dell'agente** elenco a discesa.

    ![Agenti ospitati](media\azure-stack-solution-hybrid-pipeline\107.png)

9. In **distribuire Azure App Service**, selezionare la validità **pacchetto o cartella** per l'ambiente.

    ![Selezionare pacchetto o cartella](media\azure-stack-solution-hybrid-pipeline\108.png)

10. In **seleziona File o la cartella**, selezionare **OK** al **percorso**.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\109.png)

11. Salvare tutte le modifiche e tornare alla **Pipeline**.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\110.png)

12. Nel **Pipeline** , selezionare **Aggiungi artefatto**e scegliere il **NorthwindCloud Traders-nave** dal **origine (definizione di compilazione)** elenco a discesa.

    ![Aggiungi nuovo elemento](media\azure-stack-solution-hybrid-pipeline\111.png)

13. Nel **selezionare un modello**, aggiungere un altro ambiente. Pick **distribuzione di servizio App di Azure** e quindi selezionare **applica**.

    ![Selezionare il modello](media\azure-stack-solution-hybrid-pipeline\112.png)

14. Immettere "Azure Stack" come le **nome dell'ambiente**.

    ![Nome ambiente](media\azure-stack-solution-hybrid-pipeline\113.png)

15. Nel **attività** scheda, individuare e selezionare Azure Stack.

    ![Azure ambiente dello Stack](media\azure-stack-solution-hybrid-pipeline\114.png)

16. Dal **sottoscrizione di Azure** elenco a discesa, selezionare "AzureStack Traders nave EP" per l'endpoint dello Stack di Azure.

    ![Testo alternativo](media\azure-stack-solution-hybrid-pipeline\115.png)

17. Immettere il nome dell'app web di Azure Stack come le **nome del servizio App**.

    ![Nome del servizio App](media\azure-stack-solution-hybrid-pipeline\116.png)

18. Sotto **selezione agenti**, scegliere "AzureStack - bDouglas Fir" tra il **coda dell'agente** elenco a discesa.

    ![Selezionare l'agente](media\azure-stack-solution-hybrid-pipeline\117.png)

19. Per **distribuire Azure App Service**, selezionare la validità **pacchetto o cartella** per l'ambiente. Nel **seleziona File o cartella**, selezionare **OK** per la cartella **percorso**.

    ![Selezione pacchetto o cartella](media\azure-stack-solution-hybrid-pipeline\118.png)

    ![Approvare percorso](media\azure-stack-solution-hybrid-pipeline\119.png)

20. Nel **variabile** scheda, trovare la variabile di denominato **VSTS_ARM_REST_IGNORE_SSL_ERRORS**. Impostare il valore della variabile **true**e impostare il relativo ambito **Azure Stack**.

    ![Configurare variabile](media\azure-stack-solution-hybrid-pipeline\120.png)

21. Nel **Pipeline** , selezionare il **trigger distribuzione continua** icona per l'elemento NorthwindCloud Traders-Web e impostare il **trigger distribuzione continua** a **Abilitata**.  Eseguire la stessa operazione per l'elemento "NorthwindCloud Traders-nave".

    ![Imposta il trigger la distribuzione continua](media\azure-stack-solution-hybrid-pipeline\121.png)

22. Per l'ambiente dello Stack di Azure, selezionare il **pre-distribuzione condizioni** icona Imposta il trigger **dopo il rilascio**.

    ![Set di condizioni di pre-distribuzione trigger](media\azure-stack-solution-hybrid-pipeline\122.png)

23. Salvare tutte le modifiche.

> [!Note]
> Alcune impostazioni per le attività di rilascio possono essere state automaticamente definite come [variabili di ambiente](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) al momento della creazione di una definizione di versione da un modello. Queste impostazioni non possono essere modificate nelle impostazioni di attività. Tuttavia, è possibile modificare queste impostazioni sono presenti gli elementi di ambiente padre.

## <a name="create-a-release"></a>Crea una versione

Dopo aver completato le modifiche alla definizione di versione, è necessario avviare la distribuzione. A tale scopo, si crea una versione dalla definizione di versione. Una versione può essere creata automaticamente; ad esempio, il trigger di una distribuzione continua è impostato nella definizione di versione. Ciò significa che dopo la modifica del codice sorgente verrà avviata una nuova compilazione e, dal che, una nuova versione. Tuttavia, in questa sezione si creerà una nuova versione manualmente.

1. Nel **Pipeline** scheda, aprire il **rilasciare** elenco a discesa e scegliere **Crea versione**.

    ![Crea una versione](media\azure-stack-solution-hybrid-pipeline\200.png)

2. Immettere una descrizione per la versione, verificare che siano selezionati gli elementi corretti e quindi scegliere **crea**. Dopo qualche istante, viene visualizzato un banner di che indica che è stata creata la nuova versione e il nome di versione viene visualizzato come collegamento. Scegliere il collegamento per visualizzare la pagina di riepilogo di rilascio.

    ![Banner di creazione della versione](media\azure-stack-solution-hybrid-pipeline\201.png)

3. Pagina di riepilogo di rilascio per Mostra i dettagli sulla versione. Nell'acquisizione di schermata seguente "Versione-2", il **ambienti** sezione viene illustrata la **lo stato di distribuzione** per Azure come "IN corso" e lo stato per lo Stack di Azure è "è stato completato". Quando lo stato della distribuzione per l'ambiente Azure cambia in "SUCCEEDED", viene visualizzata un'intestazione che indica che la versione è pronta per l'approvazione. Quando una distribuzione è in sospeso o non è riuscita, un blu **(i)** viene visualizzata l'icona di informazioni. Passare il mouse sull'icona per visualizzare una finestra popup che contiene il motivo per ritardo o l'errore.

    ![Pagina di riepilogo di rilascio](media\azure-stack-solution-hybrid-pipeline\202.png)

Altre visualizzazioni, quali l'elenco delle versioni, verrà anche visualizzata un'icona che indica l'approvazione in sospeso. La finestra popup per questa icona Mostra il nome dell'ambiente e altri dettagli correlati alla distribuzione. È facile per un amministratore, vedere lo stato complessivo di versioni e vedere quali versioni sono in attesa di approvazione.

### <a name="monitor-and-track-deployments"></a>Distribuzioni di monitorare e tenere traccia

In questa sezione viene illustrato come è possibile monitorare e tenere traccia di tutte le distribuzioni. La versione per la distribuzione di due siti Web di servizi di App di Azure fornisce un buon esempio.

1. Nella pagina di riepilogo "Versione-2", selezionare **registri**. Durante una distribuzione, questa pagina Mostra il registro in tempo reale dall'agente. Riquadro a sinistra mostra lo stato di ogni operazione in una distribuzione per ogni ambiente.

    È possibile scegliere un'icona di persona nel **azione** colonna per un'approvazione pre-distribuzione o post-distribuzione che ha approvato la distribuzione (o rifiutati), e il messaggio specificato.

2. Una volta completata la distribuzione, l'intero file di log viene visualizzato nel riquadro di destra. È possibile selezionare qualsiasi **passaggio** nel riquadro sinistro per visualizzare il file di log per un unico passaggio, ad esempio "Processo di inizializzazione". La possibilità di visualizzare singoli log semplifica la traccia ed eseguire il debug di parti di distribuzione. È anche possibile **salvare** il file di log per un passaggio o **scaricare tutti i log come zip**.

    ![Registri di rilascio](media\azure-stack-solution-hybrid-pipeline\203.png)

3. Aprire la **riepilogo** scheda per visualizzare informazioni generali sulla versione. Questa vista vengono visualizzati i dettagli della compilazione, gli ambienti in cui è stato distribuito, lo stato di distribuzione e altre informazioni sulla versione.

4. Selezionare un collegamento di ambiente (**Azure** oppure **Azure Stack**) per visualizzare informazioni sui esistente e le distribuzioni a un ambiente specifico in sospeso. È possibile utilizzare queste viste come un modo rapido per verificare che la stessa build è stata distribuita per entrambi gli ambienti.

5. Aprire la **distribuito dell'applicazione di produzione** nel browser. Ad esempio, per il sito Web di servizi di App di Azure, aprire l'URL `http://[your-app-name].azurewebsites.net`.

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sui pattern di Cloud di Azure, vedere [schemi progettuali Cloud](https://docs.microsoft.com/azure/architecture/patterns).
