<properties
   pageTitle="Creare un'entità servizio nel portale | Microsoft Azure"
   description="Descrive come creare una nuova applicazione ed entità servizio di Active Directory da usare con il controllo degli accessi in base al ruolo in Gestione risorse di Azure per gestire l'accesso alle risorse."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/07/2016"
   ms.author="tomfitz"/>

# Usare il portale per creare un'applicazione Active Directory e un'entità servizio che accedono alle risorse

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Interfaccia della riga di comando di Azure](resource-group-authenticate-service-principal-cli.md)
- [Portale](resource-group-create-service-principal-portal.md)


Quando un'applicazione deve accedere alle risorse o modificarle, è necessario configurare un'applicazione Active Directory (AD) a cui assegnare le autorizzazioni richieste. Questo argomento illustra come eseguire questa procedura tramite il portale. Attualmente, è necessario usare il portale classico per creare una nuova applicazione di Active Directory e quindi passare al portale di Azure per assegnare un ruolo all'applicazione.

> [AZURE.NOTE] Può risultare più semplice configurare l'applicazione AD e l'entità servizio tramite [PowerShell](resource-group-authenticate-service-principal.md) o l'[interfaccia della riga di comando di Azure](resource-group-authenticate-service-principal-cli.md), soprattutto se si vuole usare un certificato per l'autenticazione. Questo argomento non illustra come usare un certificato.

Per una spiegazione dei concetti relativi ad Active Directory, vedere [Oggetti applicazione e oggetti entità servizio](./active-directory/active-directory-application-objects.md). Per altre informazioni sull'autenticazione in Active Directory, vedere [Scenari di autenticazione per Azure AD](./active-directory/active-directory-authentication-scenarios.md).

Per informazioni dettagliate sull'integrazione di un'applicazione in Azure per la gestione delle risorse, vedere [Guida per gli sviluppatori all'autorizzazione con l'API di Azure Resource Manager](resource-manager-api-authentication.md).

## Creare un'applicazione di Active Directory

1. Accedere all'account di Azure tramite il [portale classico](https://manage.windowsazure.com/).

2. Verificare di conoscere la directory Active Directory predefinita per la sottoscrizione. È possibile concedere l'accesso solo per le applicazioni presenti nella stessa directory della sottoscrizione. Selezionare **Impostazioni** e cercare il nome della directory associato alla sottoscrizione. Per altre informazioni, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md).
   
     ![Trovare la directory predefinita](./media/resource-group-create-service-principal-portal/show-default-directory.png)  

2. Selezionare **Active Directory** dal riquadro di sinistra.

     ![selezionare Active Directory](./media/resource-group-create-service-principal-portal/active-directory.png)
     
3. Selezionare il tipo di Active Directory che si vuole usare per creare l'applicazione. Se è disponibile più di un tipo di Active Directory, creare l'applicazione nella directory predefinita della sottoscrizione.

     ![scegliere la directory](./media/resource-group-create-service-principal-portal/active-directory-details.png)  
     
3. Per visualizzare le applicazioni nella directory, selezionare **Applicazioni**.

     ![visualizzare le applicazioni](./media/resource-group-create-service-principal-portal/view-applications.png)  

4. Se non è stata creata un'applicazione in questa directory, dovrebbe essere visualizzata un'immagine simile a questa riportata di seguito. Selezionare **AGGIUNGI UN'APPLICAZIONE**.

     ![aggiungere un'applicazione](./media/resource-group-create-service-principal-portal/create-application.png)  

     In alternativa, fare clic su **Aggiungi** nel riquadro inferiore.

     ![add](./media/resource-group-create-service-principal-portal/add-icon.png)  

5. Selezionare il tipo di applicazione da creare. Per questa esercitazione selezionare **Aggiungi un'applicazione che l'organizzazione sta sviluppando**.

     ![nuova applicazione](./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png)  

6. Specificare un nome per l'applicazione e selezionare il tipo di applicazione che si vuole creare. Per questa esercitazione creare un'**APPLICAZIONE WEB E/O API WEB** e fare clic sul pulsante Avanti. Se si seleziona **APPLICAZIONE CLIENT NATIVA**, i rimanenti passaggi di questo articolo non corrisponderanno all'esperienza utente.

     ![assegnare un nome all'applicazione](./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png)  

7. Compilare le proprietà per l'app. Per **URL ACCESSO** specificare l'URI di un sito Web che descrive l'applicazione. L'esistenza del sito Web non viene convalidata. Per **URI ID APP** specificare l'URI che identifica l'applicazione.

     ![proprietà dell'applicazione](./media/resource-group-create-service-principal-portal/app-properties.png)  

L'applicazione è stata creata.

## Ottenere l'ID client e la chiave di autenticazione

Quando si esegue l'accesso a livello di codice, è necessario l'ID dell'applicazione. Se l'applicazione viene eseguita con le proprie credenziali, è necessaria anche una chiave di autenticazione.

1. Selezionare la scheda **Configura** per configurare la password dell'applicazione.

     ![configura applicazione](./media/resource-group-create-service-principal-portal/application-configure.png)  

2. Copiare l'**ID CLIENT**.
  
     ![id client](./media/resource-group-create-service-principal-portal/client-id.png)  

3. Se l'applicazione viene eseguita con credenziali proprie, scorrere fino alla sezione **Chiavi** e selezionare la durata della validità della password.

     ![chiavi](./media/resource-group-create-service-principal-portal/create-key.png)

4. Selezionare **Salva** per creare la chiave.

     ![salvare](./media/resource-group-create-service-principal-portal/save-icon.png)

     Viene visualizzata la chiave salvata, che è possibile copiare. Dato che non sarà possibile recuperare la chiave in seguito, copiarla ora.

     ![chiave salvata](./media/resource-group-create-service-principal-portal/save-key.png)  

## Ottenere l'ID tenant

Quando si esegue l'accesso a livello di codice, è necessario specificare l'ID tenant con la richiesta di autenticazione. Per le app Web e le app per le API Web è possibile recuperare l'ID tenant selezionando **Visualizza endpoint** nella parte inferiore della schermata e recuperare l'ID come illustrato nell'immagine seguente.

   ![tenant id](./media/resource-group-create-service-principal-portal/save-tenant.png)

È anche possibile recuperare l'ID tenant usando PowerShell:

    Get-AzureRmSubscription

Oppure l'interfaccia della riga di comando di Azure:

    azure account show --json

## Impostare autorizzazioni delegate

Se l'applicazione accede alle risorse per conto dell'utente connesso, è necessario concedere all'applicazione l'autorizzazione delegata per accedere alle altre applicazioni. Concedere questo accesso nella sezione **Autorizzazioni per altre applicazioni** della scheda **Configura**. Per impostazione predefinita, un'autorizzazione delegata è già abilitata per Azure Active Directory. Lasciare questa autorizzazione delegata invariata.

1. Selezionare **Aggiungi applicazione**.

2. Nell'elenco selezionare **API Gestione dei servizi di Microsoft Azure**. Quindi, fare clic sull'icona di completamento.

      ![seleziona app](./media/resource-group-create-service-principal-portal/select-app.png)

3. Nell'elenco a discesa relativo alle autorizzazioni delegate selezionare **Access Azure Service Management as organization** (Accedi alla gestione dei servizi Azure come organizzazione).

      ![seleziona autorizzazione](./media/resource-group-create-service-principal-portal/select-permissions.png)  

4. Salvare la modifica.

## Assegnare l'applicazione al ruolo

Se l'applicazione viene eseguita con le proprie credenziali, è necessario assegnare l'applicazione a un ruolo. Decidere quale ruolo rappresenti le autorizzazioni appropriate per l'applicazione. Per informazioni sui ruoli disponibili, vedere [RBAC: Ruoli predefiniti](./active-directory/role-based-access-built-in-roles.md).

Per assegnare un ruolo a un'applicazione, è necessario avere le autorizzazioni corrette. In particolare, è necessario avere l'accesso `Microsoft.Authorization/*/Write`, che viene concesso tramite il ruolo [Proprietario](./active-directory/role-based-access-built-in-roles.md#owner) o [Amministratore Accesso utenti](./active-directory/role-based-access-built-in-roles.md#user-access-administrator). Il ruolo Collaboratore non ha l'accesso corretto.

È possibile impostare l'ambito al livello della sottoscrizione, del gruppo di risorse o della risorsa. Le autorizzazioni vengono ereditate a livelli inferiori dell'ambito. Se ad esempio si aggiunge un'applicazione al ruolo Lettore per un gruppo di risorse, l'applicazione può leggere il gruppo di risorse e le risorse in esso contenute.

1. Per assegnare l'applicazione a un ruolo, passare dal portale classico al [portale di Azure](https://portal.azure.com).

1. Controllare le autorizzazioni per verificare che sia possibile assegnare l'entità servizio a un ruolo. Selezionare **Autorizzazioni personali** per l'account.

    ![Selezionare Autorizzazioni personali](./media/resource-group-create-service-principal-portal/my-permissions.png)  

1. Visualizzare le autorizzazioni assegnate per l'account. Come già osservato, è necessario appartenere ai ruoli Proprietario o Amministratore Accesso utenti oppure avere un ruolo personalizzato che concede l'accesso in scrittura per Microsoft.Authorization. L'immagine seguente illustra un account assegnato al ruolo Collaboratore per la sottoscrizione, che non ha le autorizzazioni adeguate per assegnare un'applicazione a un ruolo.

    ![Visualizzare le autorizzazioni personali](./media/resource-group-create-service-principal-portal/show-permissions.png)  

     Se non si hanno le autorizzazioni corrette per concedere l'accesso a un'applicazione, è necessario richiedere all'amministratore della sottoscrizione di essere aggiunti al ruolo Amministratore Accesso utenti o richiedere che un amministratore conceda l'accesso all'applicazione.

1. Passare al livello dell'ambito al quale si vuole assegnare l'applicazione. Per assegnare un ruolo a un ambito della sottoscrizione, selezionare **Sottoscrizioni**.

     ![selezionare la sottoscrizione](./media/resource-group-create-service-principal-portal/select-subscription.png)  

     Selezionare la sottoscrizione specifica a cui assegnare l'applicazione.

     ![selezionare la sottoscrizione per l'assegnazione](./media/resource-group-create-service-principal-portal/select-one-subscription.png)  

     Selezionare l'icona di **accesso** nell'angolo in alto a destra.

     ![selezionare accesso](./media/resource-group-create-service-principal-portal/select-access.png)  
     
     In alternativa, per assegnare un ruolo all'ambito del gruppo di risorse, passare a un gruppo di risorse. Nel pannello del gruppo di risorse selezionare **Controllo di accesso**.

     ![selezionare gli utenti](./media/resource-group-create-service-principal-portal/select-users.png)  

     I passaggi seguenti sono uguali per tutti gli ambiti.

2. Selezionare **Aggiungi**.

     ![selezionare aggiungi](./media/resource-group-create-service-principal-portal/select-add.png)  

3. Selezionare il ruolo **Lettore** o il ruolo al quale si vuole assegnare l'applicazione.

     ![selezionare il ruolo](./media/resource-group-create-service-principal-portal/select-role.png)  

4. Quando viene visualizzato per la prima volta l'elenco degli utenti che è possibile aggiungere al ruolo, le applicazioni non verranno visualizzate. Verranno visualizzati solo il gruppo e gli utenti.

     ![mostrare gli utenti](./media/resource-group-create-service-principal-portal/show-users.png)  

5. Per individuare l'applicazione, è necessario cercarla. Digitando il nome dell'applicazione, l'elenco delle opzioni disponibili cambierà. Quando l'applicazione viene visualizzata nell'elenco, selezionarla.

     ![assegnare al ruolo](./media/resource-group-create-service-principal-portal/assign-to-role.png)  

6. Selezionare **OK** per completare l'assegnazione al ruolo. L'applicazione dovrebbe essere ora visualizzata nell'elenco degli utenti assegnati a un ruolo per il gruppo di risorse.


Per altre informazioni sull'assegnazione di utenti e applicazioni a ruoli tramite il portale, vedere [Usare le assegnazioni di ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](role-based-access-control-configure.md#manage-access-using-the-azure-management-portal).

## Applicazioni di esempio

Le applicazioni di esempio seguenti illustrano come effettuare l'accesso come entità servizio.

**.NET**

- [Deploy an SSH Enabled VM with a Template with .NET (Distribuire una macchina virtuale abilitata per SSH con un modello con .NET)](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [Manage Azure resources and resource groups with .NET (Gestire risorse e gruppi di risorse di Azure con .NET)](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [Getting Started with Resources - Deploy Using ARM Template - in Java (Introduzione alle risorse: distribuzione tramite il modello di Azure Resource Manager in Java)](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [Getting Started with Resources - Manage Resource Group - in Java (Introduzione alle risorse: gestire un gruppo di risorse in Java)](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [Deploy an SSH Enabled VM with a Template in Python (Distribuire una macchina virtuale abilitata per SSH con un modello in Python)](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Managing Azure Resource and Resource Groups with Python (Gestione di risorse e gruppi di risorse di Azure con Python)](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.JS**

- [Deploy an SSH Enabled VM with a Template in Node.js (Distribuire una macchina virtuale abilitata per SSH con un modello in Node.js)](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Manage Azure resources and resource groups with Node.js (Gestire risorse e gruppi di risorse di Azure con Node.js)](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

- [Deploy an SSH Enabled VM with a Template in Ruby (Distribuire una macchina virtuale abilitata per SSH con un modello in Ruby)](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [Managing Azure Resource and Resource Groups with Ruby (Gestione di risorse e gruppi di risorse di Azure con Ruby)](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)


## Passaggi successivi

- Per informazioni su come specificare i criteri di sicurezza, vedere [Controllo degli accessi in base al ruolo](./active-directory/role-based-access-control-configure.md).
- Per una dimostrazione video di questi passaggi, vedere l'articolo relativo all'[abilitazione della gestione a livello di codice di una risorsa di Azure con Azure Active Directory](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory).

<!---HONumber=AcomDC_0914_2016-->