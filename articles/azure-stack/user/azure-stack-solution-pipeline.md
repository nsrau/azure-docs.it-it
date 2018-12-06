---
title: Distribuire l'app di Azure e Azure Stack | Microsoft Docs
description: Informazioni su come distribuire le App in Azure e Azure Stack con una pipeline di integrazione continua/distribuzione ibrida.
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
ms.date: 11/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 36637137741aef6b34ab8e70109d692f5399043a
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52967062"
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>Esercitazione: Distribuire le App in Azure e Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Informazioni su come distribuire un'applicazione di Azure e Azure Stack tramite una pipeline di recapito (CI/CD) ibrido/integrazione continua.

In questa esercitazione si creerà un ambiente di esempio:

> [!div class="checklist"]
> * Avviare una nuova compilazione basata su commit del codice nel repository di servizi di Azure DevOps.
> * Distribuire automaticamente l'app di Azure globale per il test di accettazione utente.
> * Quando il codice passi di test, distribuire automaticamente l'app in Azure Stack.

## <a name="benefits-of-the-hybrid-delivery-build-pipe"></a>Vantaggi del recapito ibrida compilare pipe

Continuità aziendale, sicurezza e affidabilità sono elementi principali della distribuzione dell'applicazione. Questi elementi sono essenziali per l'organizzazione e critici al team di sviluppo. Una pipeline di integrazione continua/distribuzione ibrida consente di consolidare le pipe di compilazione tra ambiente locale e nel cloud pubblico. Un modello di distribuzione ibrida consente inoltre di modificare i percorsi di distribuzione senza modificare l'applicazione.

Altri vantaggi dell'uso dell'approccio ibrido sono:

* È possibile mantenere un set di strumenti di sviluppo coerente tra l'ambiente di Azure Stack in locale e cloud pubblico di Azure.  Un set di strumenti comune rende più semplice implementare procedure consigliate e modelli di integrazione continua/recapito Continuo.
* Le App e servizi distribuiti in Azure o Azure Stack sono intercambiabili e lo stesso codice può eseguire in un'altra posizione. È possibile sfruttare funzionalità e le funzionalità di cloud pubblico e in locale.

Per altre informazioni su integrazione continua e recapito Continuo:

* [Che cos'è l'integrazione continua?](https://www.visualstudio.com/learn/what-is-continuous-integration/)
* [Che cos'è il recapito continuo?](https://www.visualstudio.com/learn/what-is-continuous-delivery/)

> [!Tip]  
> ![ibrido-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack è un'estensione di Azure. Azure Stack offre l'agilità e innovazione del cloud computing al tuo ambiente locale e abilitare l'unico cloud ibrido che consente di compilare e distribuire le app ibride ovunque.  
> 
> Il white paper [considerazioni sulla progettazione per applicazioni ibride](https://aka.ms/hybrid-cloud-applications-pillars) esamina i concetti fondamentali della qualità del software (selezione host, scalabilità, disponibilità, resilienza, gestibilità e sicurezza) per la progettazione, distribuzione e gestione ibrida applicazioni. Le considerazioni di progettazione assistere nell'ottimizzazione della progettazione di applicazioni ibride, riducendo al minimo le sfide negli ambienti di produzione.

## <a name="prerequisites"></a>Prerequisiti

È necessario disporre di componenti in grado di creare una pipeline di integrazione continua/distribuzione ibrida. Richiede tempo per preparare i componenti seguenti:

* Un partner OEM/hardware di Azure puoi distribuire un ambiente di produzione Azure Stack. Tutti gli utenti possono distribuire Azure Stack Development Kit (ASDK).
* È necessario anche un operatore di Stack di Azure: distribuire il servizio App, creare piani e offerte, creare una sottoscrizione di tenant e aggiungere l'immagine di Windows Server 2016.

>[!NOTE]
>Se si dispone già di alcuni di questi componenti distribuiti, verificare che soddisfino tutti i requisiti prima di iniziare questa esercitazione.

Questa esercitazione si presuppone una conoscenza di base di Azure e Azure Stack. Per altre informazioni prima di iniziare l'esercitazione, vedere gli articoli seguenti:

* [Introduzione ad Azure](https://azure.microsoft.com/overview/what-is-azure/)
* [Concetti chiave di Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure-requirements"></a>Requisiti di Azure

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
* Creare un [App Web](https://docs.microsoft.com/azure/app-service/app-service-web-overview) in Azure. Prendere nota dell'URL dell'App Web, è necessario usarlo nell'esercitazione.

### <a name="azure-stack-requirements"></a>Requisiti per Azure Stack

* Usare un sistema integrato Azure Stack o distribuire Azure Stack Development Kit (ASDK). Per distribuire il ASDK:
    * Il [esercitazione: distribuire il ASDK usando il programma di installazione](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy) offre istruzioni dettagliate sulla distribuzione.
    * Usare la [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ) script di PowerShell per automatizzare i passaggi di post-distribuzione ASDK.

    > [!Note]
    > L'installazione ASDK richiede circa sette ore per il completamento, pertanto, pianificare di conseguenza.

 * Distribuire [servizio App](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) servizi PaaS per Azure Stack.
 * Creare [piano/offerte](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) nello Stack di Azure.
 * Creare un [sottoscrizione tenant](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) in Azure Stack.
 * Creare un'App Web nella sottoscrizione del tenant. Prendere nota del nuovo URL di App Web per usare in un secondo momento.
 * Distribuire una macchina virtuale di Windows Server 2012 nella sottoscrizione del tenant. Si userà questo server come server di compilazione e per eseguire i servizi di Azure DevOps.
* Fornire un'immagine di Windows Server 2016 con .NET 3.5 per una macchina virtuale (VM). Questa macchina virtuale verrà compilata in Azure Stack come agente di compilazione privata.

### <a name="developer-tool-requirements"></a>Requisiti dello strumento per sviluppatori

* Creare un [dell'area di lavoro di servizi di Azure DevOps](https://docs.microsoft.com/azure/devops/repos/tfvc/create-work-workspaces). Il processo di iscrizione viene creato un progetto denominato **MyFirstProject**.
* [Installare Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) e [Accedi a servizi di Azure DevOps](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).
* Connettersi al progetto e [clonarlo localmente](https://www.visualstudio.com/docs/git/gitquickstart).

 > [!Note]
 > L'ambiente Azure Stack richiede le immagini corrette diffuso per l'esecuzione di Windows Server e SQL Server. È necessario che abbia distribuite del servizio App.

## <a name="prepare-the-private-azure-pipelines-agent-for-azure-devops-services-integration"></a>Preparare l'agente Azure pipeline privata per l'integrazione con servizi di Azure DevOps

### <a name="prerequisites"></a>Prerequisiti

Servizi di Azure DevOps esegue l'autenticazione con Azure Resource Manager usando un'entità servizio. Servizi di Azure DevOps deve avere il **collaboratore** ruolo per il provisioning delle risorse in una sottoscrizione di Azure Stack.

I passaggi seguenti descrivono gli elementi necessari per configurare l'autenticazione:

1. Creare un'entità servizio o usare un'entità servizio esistente.
2. Creare le chiavi di autenticazione per l'entità servizio.
3. Convalidare la sottoscrizione di Azure Stack tramite controllo degli accessi basato su ruolo per consentire il nome dell'entità di servizio (SPN) a far parte del ruolo per i collaboratori.
4. Creare una nuova definizione di servizio in servizi di DevOps di Azure con gli endpoint di Azure Stack e le informazioni di nome SPN.

### <a name="create-a-service-principal"></a>Creare un'entità servizio

Vedere le [la creazione dell'entità servizio](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) istruzioni per creare un'entità servizio. Scegli **App Web/API** per il tipo di applicazione oppure [usare lo script di PowerShell](https://github.com/Microsoft/vsts-rm-extensions/blob/master/TaskModules/powershell/Azure/SPNCreation.ps1#L5) come illustrato nell'articolo [creare una connessione al servizio Azure Resource Manager con un servizio esistente entità ](https://docs.microsoft.com/vsts/pipelines/library/connect-to-azure?view=vsts#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal).

 > [!Note]  
 > Se si usa lo script per creare un endpoint di Azure Stack Azure Resource Manager, è necessario passare il **- azureStackManagementURL** parametro e **- environmentName** parametro. Ad esempio:   
> `-azureStackManagementURL https://management.local.azurestack.external -environmentName AzureStack`

### <a name="create-an-access-key"></a>Creare una chiave di accesso

Un'entità servizio richiede una chiave per l'autenticazione. Usare la procedura seguente per generare una chiave.

1. Da **Registrazioni dell'app** in Azure Active Directory selezionare l'applicazione.

    ![Selezionare l'applicazione](media/azure-stack-solution-hybrid-pipeline/000_01.png)

2. Prendere nota del valore del **ID applicazione**. Tale valore verrà usato quando si configura l'endpoint del servizio in servizi di Azure DevOps.

    ![ID applicazione](media/azure-stack-solution-hybrid-pipeline/000_02.png)

3. Per generare una chiave di autenticazione, selezionare **Impostazioni**.

    ![Modificare le impostazioni dell'app](media/azure-stack-solution-hybrid-pipeline/000_03.png)

4. Per generare una chiave di autenticazione selezionare **Chiavi**.

    ![Configurare le impostazioni principali](media/azure-stack-solution-hybrid-pipeline/000_04.png)

5. Fornire una descrizione per la chiave e impostare la durata della chiave. Al termine scegliere **Salva**.

    ![Durata e descrizione della chiave](media/azure-stack-solution-hybrid-pipeline/000_05.png)

    Dopo aver salvato la chiave, il tasto **valore** viene visualizzato. Copiare questo valore perché non è possibile ottenere questo valore in un secondo momento. Forniscono i **valore della chiave** con l'ID applicazione per accedere come l'applicazione. Salvare il valore della chiave in una posizione in cui l'applicazione possa recuperarlo.

    ![Chiave di valore](media/azure-stack-solution-hybrid-pipeline/000_06.png)

### <a name="get-the-tenant-id"></a>Ottenere l'ID tenant

Come parte della configurazione dell'endpoint servizio, Azure DevOps Services richiede la **Tenant ID** che corrisponde alla Directory di AAD che l'indicatore di Azure Stack viene distribuito in. Usare la procedura seguente per ottenere l'ID del Tenant.

1. Selezionare **Azure Active Directory**.

    ![Azure Active Directory per il tenant](media/azure-stack-solution-hybrid-pipeline/000_07.png)

2. Per ottenere l'ID tenant selezionare **Proprietà** per il tenanto di Azure AD.

    ![Visualizzare le proprietà di tenant](media/azure-stack-solution-hybrid-pipeline/000_08.png)

3. Copiare l'**ID directory**. Questo valore è l'ID tenant.

    ![ID directory](media/azure-stack-solution-hybrid-pipeline/000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>Concedere i diritti dell'entità servizio per distribuire le risorse nella sottoscrizione di Azure Stack

Per accedere alle risorse della propria sottoscrizione è necessario assegnare l'applicazione a un ruolo. Decidere quale ruolo rappresenti le autorizzazioni ottimali per l'applicazione. Per informazioni sui ruoli disponibili, vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

È possibile impostare l'ambito al livello della sottoscrizione, del gruppo di risorse o della risorsa. Le autorizzazioni vengono ereditate a livelli inferiori dell'ambito. Aggiunta di un'applicazione al ruolo lettore per un gruppo di risorse, ad esempio, significa che è possibile leggere il gruppo di risorse e le relative risorse.

1. Passare al livello dell'ambito al quale si vuole assegnare l'applicazione. Ad esempio, per assegnare un ruolo a un ambito della sottoscrizione, selezionare **Sottoscrizioni**.

    ![Selezionare le sottoscrizioni](media/azure-stack-solution-hybrid-pipeline/000_10.png)

2. Nelle **sottoscrizione**, selezionare Visual Studio Enterprise.

    ![Visual Studio Enterprise](media/azure-stack-solution-hybrid-pipeline/000_11.png)

3. In Visual Studio Enterprise, selezionare **controllo di accesso (IAM)**.

    ![Controllo di accesso (IAM)](media/azure-stack-solution-hybrid-pipeline/000_12.png)

4. Selezionare **Aggiungi**.

    ![Add](media/azure-stack-solution-hybrid-pipeline/000_13.png)

5. Nelle **aggiungere autorizzazioni**, selezionare il ruolo che si desidera assegnare all'applicazione. In questo esempio, il **proprietario** ruolo.

    ![Ruolo di proprietario](media/azure-stack-solution-hybrid-pipeline/000_14.png)

6. Per impostazione predefinita, le applicazioni di Azure Active Directory non sono visualizzate nelle opzioni disponibili. Per individuare l'applicazione, è necessario fornire il nome nella **seleziona** campo per cercarlo. Selezionare l'app.

    ![Risultato di ricerca di App](media/azure-stack-solution-hybrid-pipeline/000_16.png)

7. Selezionare **Salva** per completare l'assegnazione del ruolo. L'applicazione ora compare nell'elenco degli utenti assegnati a un ruolo per quell'ambito.

### <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Azure in base al ruolo di controllo di accesso (RBAC) fornisce la gestione accessi con granularità fine per Azure. Tramite RBAC, è possibile controllare il livello di accesso che gli utenti devono svolgere il proprio lavoro. Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [gestire l'accesso alle risorse della sottoscrizione Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

### <a name="azure-devops-services-agent-pools"></a>Pool di agenti di servizi di Azure DevOps

Anziché gestire separatamente ogni agente, è possibile organizzare gli agenti in pool di agenti. Un pool di agenti definisce il limite di condivisione per tutti gli agenti nel pool. In servizi di Azure DevOps, i pool di agenti hanno come ambiti dell'organizzazione di servizi di Azure DevOps, il che significa che è possibile condividere un pool di agenti tra progetti. Per altre informazioni sui pool di agenti, vedere [code e creare pool di agenti](https://docs.microsoft.com/azure/devops/pipelines/agents/pools-queues?view=vsts).

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>Aggiungere un Token di accesso personale (PAT) per Azure Stack

Creare un Token di accesso personale per accedere ai servizi di Azure DevOps.

1. Accedere all'organizzazione di servizi di Azure DevOps e selezionare il nome del profilo dell'organizzazione.

2. Selezionare **Gestisci sicurezza** alla pagina di creazione del token di accesso.

    ![Accesso utente](media/azure-stack-solution-hybrid-pipeline/000_17.png)

    ![Selezionare un progetto](media/azure-stack-solution-hybrid-pipeline/000_18.png)

    ![Aggiungere un token di accesso personale](media/azure-stack-solution-hybrid-pipeline/000_18a.png)

    ![Crea token](media/azure-stack-solution-hybrid-pipeline/000_18b.png)

3. Copiare il token.

    > [!Note]
    > Salvare le informazioni del token. Queste informazioni non viene archiviate e non verranno visualizzate quando si esce dalla pagina di web.

    ![Token di accesso personale](media/azure-stack-solution-hybrid-pipeline/000_19.png)

### <a name="install-the-azure-devops-services-build-agent-on-the-azure-stack-hosted-build-server"></a>Installazione che dell'agente di compilazione di servizi di Azure DevOps nello Stack di Azure in un Server di compilazione ospitato

1. Connettersi al Server di compilazione distribuita nell'host Azure Stack.
2. Scaricare e distribuire l'agente di compilazione come servizio tramite il personal un token di accesso ed eseguire come account di amministratore della macchina virtuale.

    ![Scaricare l'agente di compilazione](media/azure-stack-solution-hybrid-pipeline/010_downloadagent.png)

3. Passare alla cartella dell'agente di compilazione estratti. Eseguire la **cmd** file da un prompt dei comandi con privilegi elevati.

    ![Agente di compilazione estratti](media/azure-stack-solution-hybrid-pipeline/000_20.png)

    ![Registrare l'agente di compilazione](media/azure-stack-solution-hybrid-pipeline/000_21.png)

4. Al termine, la prima cartella dell'agente di compilazione viene aggiornata con i file aggiuntivi. La cartella con il contenuto estratto dovrebbe essere simile al seguente:

    ![Aggiornamento della cartella dell'agente di compilazione](media/azure-stack-solution-hybrid-pipeline/009_token_file.png)

    È possibile visualizzare l'agente nella cartella di servizi di Azure DevOps.

## <a name="endpoint-creation-permissions"></a>Autorizzazioni di creazione di endpoint

La creazione di endpoint di una compilazione di Visual Studio Online (VSTO) è possibile distribuire App del servizio di Azure ad Azure Stack. Servizi di Azure DevOps si connette all'agente di compilazione, che si connette ad Azure Stack.

![App di esempio NorthwindCloud in VSTO](media/azure-stack-solution-hybrid-pipeline/012_securityendpoints.png)

1. Accedi a VSTO e passare alla pagina di impostazioni dell'app.
2. Su **Impostazioni**, selezionare **Sicurezza**.
3. Nelle **gruppi di servizi di Azure DevOps**, selezionare **creatori di Endpoint**.

    ![Creatori di NorthwindCloud Endpoint](media/azure-stack-solution-hybrid-pipeline/013_endpoint_creators.png)

4. Nel **membri** scheda, seleziona **Add**.

    ![Aggiungere un membro](media/azure-stack-solution-hybrid-pipeline/014_members_tab.png)

5. Nelle **aggiungere utenti e gruppi**, immettere un nome utente e selezionare l'utente dall'elenco di utenti.
6. Selezionare **Save changes** (Salva modifiche).
7. Nel **gruppi di servizi di Azure DevOps** elenco, selezionare **amministratori Endpoint**.

    ![Amministratori NorthwindCloud Endpoint](media/azure-stack-solution-hybrid-pipeline/015_save_endpoint.png)

8. Nel **membri** scheda, seleziona **Add**.
9. Nelle **aggiungere utenti e gruppi**, immettere un nome utente e selezionare l'utente dall'elenco di utenti.
10. Selezionare **Save changes** (Salva modifiche).

Ora che le informazioni sull'endpoint esistente, i servizi di DevOps di Azure alla connessione di Azure Stack è pronto per l'uso. L'agente di compilazione in Azure Stack Ottiene le istruzioni da servizi di Azure DevOps e l'agente comunica quindi informazioni sull'endpoint per la comunicazione con Azure Stack.

## <a name="create-an-azure-stack-endpoint"></a>Creare un endpoint di Azure Stack

### <a name="create-an-endpoint-for-azure-ad-deployments"></a>Creare un endpoint per le distribuzioni di Azure AD

È possibile seguire le istruzioni riportate in [creare una connessione al servizio Azure Resource Manager con un servizio esistente entità ](https://docs.microsoft.com/vsts/pipelines/library/connect-to-azure?view=vsts#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal) articolo creare una connessione al servizio con un servizio esistente dell'entità e usare il mapping seguente:

È possibile creare una connessione al servizio usando il mapping seguente:

| NOME | Esempio | DESCRIZIONE |
| --- | --- | --- |
| Nome connessione | Azure Stack, Azure AD | Il nome della connessione. |
| Environment | AzureStack | Il nome dell'ambiente. |
| URL dell'ambiente | `https://management.local.azurestack.external` | L'endpoint di gestione. |
| Livello di ambito | Sottoscrizione | L'ambito della connessione. |
| ID sottoscrizione | 65710926-XXXX-4F2A-8FB2-64C63CD2FAE9 | ID sottoscrizione dell'utente da Azure Stack |
| Nome della sottoscrizione | name@contoso.com | Nome della sottoscrizione utente da Azure Stack. |
| ID client dell'entità servizio | FF74AACF-XXXX-4776-FC 93-C63E6E021D59 | L'ID dell'entità da [ciò](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-pipeline#create-a-service-principal) sezione in questo articolo. |
| Chiave dell'entità servizio | THESCRETGOESHERE = | La chiave da stesso articolo (o la password se si usa lo script). |
| ID tenant | D073C21E-XXXX-4AD0-B77E-8364FCA78A94 | L'ID tenant è recuperare in seguito con l'istruzione [ottenere l'ID tenant](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-pipeline#get-the-tenant-id).  |
| Connessione: | Non verificato | Convalidare le impostazioni di connessione per l'entità servizio. |

Ora che l'endpoint viene creato, è pronto per l'uso di DevOps alla connessione di Azure Stack. L'agente di compilazione in Azure Stack Ottiene le istruzioni dalla metodologia DevOps, e quindi l'agente fornisce informazioni sull'endpoint per la comunicazione con Azure Stack.

![Azure AD dell'agente di compilazione](media/azure-stack-solution-hybrid-pipeline/016_save_changes.png)

### <a name="create-an-endpoint-for-ad-fs"></a>Creare un endpoint per AD FS

L'aggiornamento più recente a DevOps di Azure consente di creare una connessione al servizio usando un'entità servizio con un certificato per l'autenticazione. Ciò è necessario quando si distribuisce Azure Stack con AD FS come provider di identità. 

![L'agente AD FS di compilazione](media/azure-stack-solution-hybrid-pipeline/image06.png)

È possibile creare una connessione al servizio usando il mapping seguente:

| NOME | Esempio | DESCRIZIONE |
| --- | --- | --- |
| Nome connessione | Azure Stack ad FS | Il nome della connessione. |
| Environment | AzureStack | Il nome dell'ambiente. |
| URL dell'ambiente | `https://management.local.azurestack.external` | L'endpoint di gestione. |
| Livello di ambito | Sottoscrizione | L'ambito della connessione. |
| ID sottoscrizione | 65710926-XXXX-4F2A-8FB2-64C63CD2FAE9 | ID sottoscrizione dell'utente da Azure Stack |
| Nome della sottoscrizione | name@contoso.com | Nome della sottoscrizione utente da Azure Stack. |
| ID client dell'entità servizio | FF74AACF-XXXX-4776-FC 93-C63E6E021D59 | L'ID client dall'entità servizio creata per AD FS. |
| Certificate | `<certificate>` |  Convertire il file di certificato da PFX PEM. Incollare il contenuto di file di certificato con estensione PEM in questo campo. <br> Conversione di file PFX in PEM:<br>`openssl pkcs12 -in file.pfx -out file.pem -nodes -password pass:<password_here>` |
| ID tenant | D073C21E-XXXX-4AD0-B77E-8364FCA78A94 | L'ID tenant è recuperare in seguito con l'istruzione [ottenere l'ID tenant](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-pipeline#get-the-tenant-id). |
| Connessione: | Non verificato | Convalidare le impostazioni di connessione per l'entità servizio. |

Ora che l'endpoint viene creato, è pronto per l'uso di DevOps di Azure alla connessione di Azure Stack. L'agente di compilazione in Azure Stack Ottiene le istruzioni dalla metodologia DevOps di Azure e l'agente comunica quindi informazioni sull'endpoint per la comunicazione con Azure Stack.

> [!Note]
> Se l'endpoint di Azure Stack utente ARM non è esposta a Internet, la convalida della connessione avrà esito negativo. Questo comportamento è previsto ed è possibile convalidare la connessione mediante la creazione di una pipeline di rilascio con un'attività semplice. 

## <a name="develop-your-application-build"></a>Sviluppare la compilazione dell'applicazione

In questa parte dell'esercitazione sarà:

* Aggiungere codice a un progetto di servizi di Azure DevOps.
* Creare la distribuzione di app web autonoma.
* Configurare il processo di distribuzione continua

> [!Note]
 > L'ambiente Azure Stack richiede le immagini corrette diffuso per l'esecuzione di Windows Server e SQL Server. È necessario che abbia distribuite del servizio App. Esaminare la documentazione del servizio App sezione "Prerequisiti" per i requisiti di operatore di Azure Stack.

Integrazione continua/distribuzione ibrida è possibile applicare al codice dell'applicazione e al codice dell'infrastruttura. Uso [modelli di Azure Resource Manager, ad esempio web ](https://azure.microsoft.com/resources/templates/) codice dell'app da servizi di DevOps di Azure per distribuire in entrambi i cloud.

### <a name="add-code-to-an-azure-devops-services-project"></a>Aggiungere codice a un progetto di servizi di Azure DevOps

1. Accedi a servizi di Azure DevOps con un'organizzazione che dispone dei diritti di creazione progetto in Azure Stack. La schermata successiva mostra come connettersi al progetto HybridCICD.

    ![Connettersi a un progetto](media/azure-stack-solution-hybrid-pipeline/017_connect_to_project.png)

2. **Clonare il repository** , creare e aprire l'app web predefinita.

    ![Clonare il repository](media/azure-stack-solution-hybrid-pipeline/018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Creare la distribuzione di app web autonoma per i servizi App in entrambi i cloud

1. Modificare il **WebApplication.csproj** file: selezionare **Runtimeidentifier** e quindi aggiungere `win10-x64.` per altre informazioni, vedere [self-contained deployment](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) documentazione.

    ![Configurare Runtimeidentifier](media/azure-stack-solution-hybrid-pipeline/019_runtimeidentifer.png)

2. Usare Team Explorer per controllare il codice in servizi di Azure DevOps.

3. Verificare che il codice dell'applicazione è stato selezionato in servizi di Azure DevOps.

### <a name="create-the-build-pipeline"></a>Creare la pipeline di compilazione

1. Accedi a servizi di Azure DevOps con un'organizzazione che è possibile creare una pipeline di compilazione.

2. Passare il **compilazione di applicazioni Web** pagina per il progetto.

3. Nelle **argomenti**, aggiungere **- r win10-x64** codice. Questa operazione è necessaria per attivare una distribuzione autonoma con.NET Core.

    ![Aggiungere la pipeline di compilazione argomento](media/azure-stack-solution-hybrid-pipeline/020_publish_additions.png)

4. Eseguire la compilazione. Il [compilazione di distribuzione autonoma](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) processo verrà pubblicati gli artefatti che è possono eseguire in Azure e Azure Stack.

### <a name="use-an-azure-hosted-build-agent"></a>Uso di Azure ospitati agente di compilazione

Utilizzo di un agente di compilazione ospitato nei servizi di Azure DevOps è un modo pratico per la compilazione e distribuzione di App web. Gli aggiornamenti e manutenzione dell'agente viene eseguiti automaticamente da Microsoft Azure, che consente a un ciclo di sviluppo continuo e senza interruzioni.

### <a name="configure-the-continuous-deployment-cd-process"></a>Configurare il processo di distribuzione continua (CD)

Servizi di DevOps e Team Foundation Server (TFS) di Azure forniscono una pipeline, altamente configurabile e gestibile per le versioni in più ambienti, ad esempio sviluppo, staging, controllo di qualità (QA) e produzione. Questo processo può includere che richiedono le approvazioni in specifiche fasi del ciclo di vita dell'applicazione.

### <a name="create-release-pipeline"></a>Creare una pipeline di versione

Creazione di una pipeline di rilascio è il passaggio finale nell'applicazione processo di compilazione. Questa pipeline di rilascio viene utilizzata per creare una versione e distribuire una build.

1. Accedi ai servizi di Azure DevOps e passare a **pipeline di Azure** per il progetto.
2. Nel **rilasci** scheda, seleziona  **\[ +]** e quindi selezionare **Crea definizione di versione**.

   ![Creare una pipeline di versione](media/azure-stack-solution-hybrid-pipeline/021a_releasedef.png)

3. Sul **selezionare un modello**, scegliere **distribuzione di Azure App Service**e quindi selezionare **Apply**.

    ![Applica il modello](media/azure-stack-solution-hybrid-pipeline/102.png)

4. Sul **Aggiungi artefatto**, dalle **origine (definizione di compilazione)** dal menu a discesa selezionare l'app di compilazione Cloud di Azure.

    ![Aggiungere un elemento](media/azure-stack-solution-hybrid-pipeline/103.png)

5. Nel **Pipeline** scheda, seleziona il **1 fase**, **1 attività** collegare **consente di visualizzare le attività dell'ambiente**.

    ![Attività di visualizzazione della pipeline](media/azure-stack-solution-hybrid-pipeline/104.png)

6. Nel **attività** , immettere Azure come il **nome ambiente** e selezionare il EP di commercianti Web AzureCloud dal **sottoscrizione di Azure** elenco a discesa.

    ![Impostare le variabili di ambiente](media/azure-stack-solution-hybrid-pipeline/105.png)

7. Immettere il **nome del servizio app di Azure**, ovvero "northwindtraders" nella schermata successiva.

    ![Nome del servizio App](media/azure-stack-solution-hybrid-pipeline/106.png)

8. Per la fase agente, selezionare **Hosted VS2017** dalle **fronta agenta** elenco a discesa.

    ![Agente ospitato](media/azure-stack-solution-hybrid-pipeline/107.png)

9. Nelle **distribuzione servizio App di Azure**, selezionare il valido **pacchetto o cartella** per l'ambiente.

    ![Seleziona pacchetto o cartella](media/azure-stack-solution-hybrid-pipeline/108.png)

10. Nelle **seleziona File o cartella**, selezionare **OK** a **percorso**.

    ![Testo alternativo](media/azure-stack-solution-hybrid-pipeline/109.png)

11. Salvare tutte le modifiche e tornare alla **Pipeline**.

    ![Testo alternativo](media/azure-stack-solution-hybrid-pipeline/110.png)

12. Nel **Pipeline** scheda, seleziona **Aggiungi artefatto**e scegliere il **NorthwindCloud Traders-Vessel** dal **origine (definizione di compilazione)** elenco a discesa.

    ![Aggiungi nuovo elemento](media/azure-stack-solution-hybrid-pipeline/111.png)

13. Sul **selezionare un modello**, aggiungere un altro ambiente. Prelievo **distribuzione di Azure App Service** e quindi selezionare **applica**.

    ![Selezionare il modello](media/azure-stack-solution-hybrid-pipeline/112.png)

14. Immettere "Azure Stack" come il **nome dell'ambiente**.

    ![Nome ambiente](media/azure-stack-solution-hybrid-pipeline/113.png)

15. Nel **attività** scheda, trovare e selezionare Azure Stack.

    ![Ambiente di Azure Stack](media/azure-stack-solution-hybrid-pipeline/114.png)

16. Dal **sottoscrizione di Azure** elenco a discesa, selezionare "EP di commercianti Vessel AzureStack" per l'endpoint di Azure Stack.

    ![Testo alternativo](media/azure-stack-solution-hybrid-pipeline/115.png)

17. Immettere il nome di app web di Azure Stack come le **nome del servizio App**.

    ![Nome del servizio App](media/azure-stack-solution-hybrid-pipeline/116.png)

18. Sotto **selezione di agenti**, scegliere "AzureStack - bDouglas Fir" tra la **fronta agenta** elenco a discesa.

    ![Selezionare l'agente](media/azure-stack-solution-hybrid-pipeline/117.png)

19. Per la **distribuzione servizio App di Azure**, selezionare il valido **pacchetto o cartella** per l'ambiente. Sul **seleziona File o cartella**, selezionare **OK** per la cartella **percorso**.

    ![Seleziona pacchetto o cartella](media/azure-stack-solution-hybrid-pipeline/118.png)

    ![Approvare percorso](media/azure-stack-solution-hybrid-pipeline/119.png)

20. Nel **variabile** scheda, trovare la variabile denominata **VSTS_ARM_REST_IGNORE_SSL_ERRORS**. Impostare il valore della variabile su **true**e impostare il relativo ambito **Azure Stack**.

    ![Configurare variabile](media/azure-stack-solution-hybrid-pipeline/120.png)

21. Nel **Pipeline** scheda, seleziona la **trigger distribuzione continua** icona per l'artefatto di commercianti NorthwindCloud-Web e impostare il **trigger distribuzione continua** a **Abilitato**.  Eseguire la stessa operazione per l'artefatto "NorthwindCloud Traders-nave".

    ![Imposta il trigger distribuzione continua](media/azure-stack-solution-hybrid-pipeline/121.png)

22. Per l'ambiente Azure Stack, selezionare la **condizioni pre-distribuzione** set di icone del trigger al **dopo il rilascio**.

    ![Set di condizioni di pre-distribuzione trigger](media/azure-stack-solution-hybrid-pipeline/122.png)

23. Salvare tutte le modifiche.

> [!Note]
> Alcune impostazioni per le attività di rilascio possono essere state automaticamente definite come [variabili di ambiente](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts#custom-variables) quando creata una pipeline di rilascio da un modello. Queste impostazioni non possono essere modificate nelle impostazioni dell'attività. Tuttavia, è possibile modificare queste impostazioni negli elementi padre di ambiente.

## <a name="create-a-release"></a>Creare una versione

Dopo che aver completato le modifiche alla pipeline di rilascio, è possibile avviare la distribuzione. A tale scopo, si crea un rilascio da pipeline di rilascio. Una versione può essere creata automaticamente; ad esempio, il trigger distribuzione continua è impostato nella pipeline di rilascio. Ciò significa che la modifica del codice sorgente verrà avviata una nuova compilazione e, tramite la quale una nuova versione. Tuttavia, in questa sezione si creerà una nuova versione manualmente.

1. Nel **Pipeline** scheda, aprire il **Release** elenco a discesa elenco e scegliere **Crea versione**.

    ![Creare una versione](media/azure-stack-solution-hybrid-pipeline/200.png)

2. Immettere una descrizione per la versione, verificare che siano selezionati gli elementi corretti e quindi scegliere **Create**. Dopo alcuni istanti, viene visualizzato un banner che indica che è stata creata la nuova versione e il nome di versione viene visualizzato come collegamento. Scegliere il collegamento per vedere la pagina di riepilogo.

    ![Banner di creazione della versione](media/azure-stack-solution-hybrid-pipeline/201.png)

3. La pagina di riepilogo versione Mostra i dettagli sulla versione. Nella schermata seguente per "Release-2", il **ambienti** sezione illustra le **lo stato di distribuzione** per Azure come "IN corso" e lo stato per Azure Stack è "SUCCEEDED". Quando lo stato della distribuzione per l'ambiente di Azure diventa "SUCCEEDED", viene visualizzato un banner che indica che la versione è pronta per l'approvazione. Quando una distribuzione è in sospeso oppure ha esito negativo, di colore blu **(i)** viene visualizzata l'icona informazioni. Passare il mouse sull'icona per visualizzare una finestra popup che contiene il motivo per il ritardo o un errore.

    ![Pagina di riepilogo versione](media/azure-stack-solution-hybrid-pipeline/202.png)

Altre visualizzazioni, ad esempio l'elenco delle versioni, verrà anche visualizzata un'icona che indica l'approvazione è in sospeso. La finestra popup per questa icona Mostra il nome dell'ambiente e altri dettagli relativi alla distribuzione. È facile per un amministratore, vedere lo stato complessivo di rilasci e vedere quali versioni sono in attesa di approvazione.

### <a name="monitor-and-track-deployments"></a>Monitorare e tenere traccia di distribuzioni

In questa sezione viene illustrato come è possibile monitorare e tenere traccia di tutte le distribuzioni. La versione per la distribuzione di due siti Web di servizi App di Azure offre un buon esempio.

1. Nella pagina di riepilogo "Versione-2", selezionare **registri**. Durante una distribuzione, questa pagina Mostra il registro in tempo reale dall'agente. Il riquadro sinistro mostra lo stato di ogni operazione nella distribuzione per ogni ambiente.

    È possibile scegliere un'icona di persona nel **azione** colonna per un'approvazione pre-distribuzione o post-distribuzione per vedere che ha approvato la distribuzione (o rifiutati) e il messaggio è forniti.

2. Al termine della distribuzione, l'intero file di log viene visualizzato nel riquadro di destra. È possibile selezionare qualsiasi **passaggio** nel riquadro sinistro per visualizzare il file di log per un singolo passaggio, ad esempio "Processo di inizializzazione". La possibilità di visualizzare singoli log rende più semplice tenere traccia ed eseguire il debug di parti della distribuzione generale. È anche possibile **salvare** il file di log per un passaggio, o **Scarica tutti i log come zip**.

    ![Log di rilascio](media/azure-stack-solution-hybrid-pipeline/203.png)

3. Aprire il **riepilogo** scheda per visualizzare informazioni generali sulla versione. Questa visualizzazione Mostra informazioni dettagliate sulla compilazione, gli ambienti in cui è stato distribuito, lo stato di distribuzione e altre informazioni sulla versione.

4. Selezionare un collegamento di ambiente (**Azure** oppure **Azure Stack**) per visualizzare informazioni su esistente e le distribuzioni in un ambiente specifico in sospeso. È possibile usare queste viste come un modo rapido per verificare che la stessa build è stata distribuita a entrambi gli ambienti.

5. Aprire il **distribuite app di produzione** nel browser. Ad esempio, per il sito Web di servizi App di Azure, aprire l'URL `http://[your-app-name].azurewebsites.net`.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui modelli Cloud di Azure, vedere [Cloud Design Patterns](https://docs.microsoft.com/azure/architecture/patterns).
