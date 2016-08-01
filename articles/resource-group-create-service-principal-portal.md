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
   ms.date="07/19/2016"
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

2. Selezionare **Active Directory** dal riquadro di sinistra.

     ![selezionare Active Directory](./media/resource-group-create-service-principal-portal/active-directory.png)
     
3. Selezionare il tipo di Active Directory che si vuole usare per creare la nuova applicazione. Se è disponibile più di un tipo di Active Directory, in genere si preferisce creare l'applicazione nella directory in cui si trova la sottoscrizione. È possibile concedere l'accesso alle risorse nella sottoscrizione solo per le applicazioni presenti nella stessa directory della sottoscrizione.

     ![scegliere la directory](./media/resource-group-create-service-principal-portal/active-directory-details.png)
     
    Se è necessario trovare la directory per la sottoscrizione, selezionare **Impostazioni** e cercare il nome della directory.
   
     ![Trovare la directory predefinita](./media/resource-group-create-service-principal-portal/show-default-directory.png)

3. Per visualizzare le applicazioni nella directory, fare clic su **Applicazioni**.

     ![visualizzare le applicazioni](./media/resource-group-create-service-principal-portal/view-applications.png)

4. Se non è stata creata un'applicazione in questa directory, dovrebbe essere visualizzata un'immagine simile a questa riportata di seguito. Fare clic su **AGGIUNGI APPLICAZIONE**

     ![aggiungere un'applicazione](./media/resource-group-create-service-principal-portal/create-application.png)

     In alternativa, fare clic su **Aggiungi** nel riquadro inferiore.

     ![aggiungere](./media/resource-group-create-service-principal-portal/add-icon.png)

5. Selezionare il tipo di applicazione da creare. Per questa esercitazione selezionare **Aggiungi un'applicazione che l'organizzazione sta sviluppando**.

     ![nuova applicazione](./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png)

6. Specificare un nome per l'applicazione e selezionare il tipo di applicazione che si vuole creare. Per questa esercitazione creare un'**APPLICAZIONE WEB E/O API WEB** e fare clic sul pulsante Avanti. Se si seleziona **APPLICAZIONE CLIENT NATIVA**, i rimanenti passaggi di questo articolo non corrisponderanno all'esperienza utente.

     ![assegnare un nome all'applicazione](./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png)

7. Compilare le proprietà per l'app. Per **URL ACCESSO** specificare l'URI di un sito Web che descrive l'applicazione. L'esistenza del sito Web non viene convalidata. Per **URI ID APP** specificare l'URI che identifica l'applicazione.

     ![proprietà dell'applicazione](./media/resource-group-create-service-principal-portal/app-properties.png)

L'applicazione è stata creata.

## Ottenere l'ID client e la chiave di autenticazione

Quando si esegue l'accesso a livello di codice, è necessario l'ID dell'applicazione. Se l'applicazione viene eseguita con le proprie credenziali, è necessaria anche una chiave di autenticazione.

1. Fare clic sulla scheda **Configura** per configurare la password dell'applicazione.

     ![configura applicazione](./media/resource-group-create-service-principal-portal/application-configure.png)

2. Copiare l'**ID CLIENT**.
  
     ![id client](./media/resource-group-create-service-principal-portal/client-id.png)

3. Se l'applicazione viene eseguita con credenziali proprie, scorrere fino alla sezione **Chiavi** e selezionare la durata della validità della password.

     ![chiavi](./media/resource-group-create-service-principal-portal/create-key.png)

4. Selezionare **Salva** per creare la chiave.

     ![salvare](./media/resource-group-create-service-principal-portal/save-icon.png)

     Viene visualizzata la chiave salvata, che è possibile copiare. Non si sarà in grado di recuperare la chiave in un secondo momento,quindi la si dovrebbe copiare ora.

     ![chiave salvata](./media/resource-group-create-service-principal-portal/save-key.png)

## Ottenere l'ID tenant

Quando si esegue l'accesso a livello di codice, è necessario specificare l'ID tenant con la richiesta di autenticazione. Per le app Web e le app per le API Web è possibile recuperare l'ID tenant selezionando **Visualizza endpoint** nella parte inferiore della schermata e recuperare l'ID come illustrato di seguito.

   ![tenant id](./media/resource-group-create-service-principal-portal/save-tenant.png)

È anche possibile recuperare l'ID tenant usando PowerShell:

    Get-AzureRmSubscription

Oppure l'interfaccia della riga di comando di Azure:

    azure account show --json

## Impostare autorizzazioni delegate

Se l'applicazione accede alle risorse per conto dell'utente connesso, è necessario concedere all'applicazione l'autorizzazione delegata per accedere alle altre applicazioni. Eseguire questa operazione nella sezione **Autorizzazioni per altre applicazioni** della scheda **Configura**. Per impostazione predefinita, un'autorizzazione delegata è già abilitata per Azure Active Directory. Lasciare questa autorizzazione delegata invariata.

1. Selezionare **Aggiungi applicazione**.

2. Nell'elenco selezionare **API di gestione del servizio Microsoft Azure**. Quindi, fare clic sull'icona di completamento.

      ![seleziona app](./media/resource-group-create-service-principal-portal/select-app.png)

3. Nell'elenco a discesa relativo alle autorizzazioni delegate selezionare **Access Azure Service Management as organization** (Accedi alla gestione dei servizi Azure come organizzazione).

      ![seleziona autorizzazione](./media/resource-group-create-service-principal-portal/select-permissions.png)

4. Salvare la modifica.

## Configurare applicazioni multi-tenant

Se gli utenti da altre directory di Azure Active Directory possono fornire il consenso all'applicazione ed eseguire l'accesso, è necessario abilitare multi-tenancy. Nella scheda **Configura** impostare **L'applicazione è multi-tenant** su **Sì**.

![multi-tenant](./media/resource-group-create-service-principal-portal/multi-tenant.png)

## Assegnare l'applicazione al ruolo

Se l'applicazione viene eseguita con le proprie credenziali, è necessario assegnare l'applicazione a un ruolo. È necessario decidere quale ruolo rappresenti le autorizzazioni appropriate per l'applicazione. Per informazioni sui ruoli disponibili, vedere [RBAC: Ruoli predefiniti](./active-directory/role-based-access-built-in-roles.md).

Per assegnare un ruolo, è necessario avere l'accesso `Microsoft.Authorization/*/Write`, che viene concesso tramite il ruolo [Proprietario](./active-directory/role-based-access-built-in-roles.md#owner) o [Amministratore Accesso utenti](./active-directory/role-based-access-built-in-roles.md#user-access-administrator).

È possibile impostare l'ambito al livello della sottoscrizione, del gruppo di risorse o della risorsa. Le autorizzazioni vengono ereditate a livelli inferiori dell'ambito. Se ad esempio si aggiunge un'applicazione al ruolo Lettore per un gruppo di risorse, l'applicazione è in grado di leggere il gruppo di risorse e le risorse in esso contenute.

1. Per assegnare l'applicazione a un ruolo, passare dal portale classico al [portale di Azure](https://portal.azure.com).

1. Nel portale passare al livello dell'ambito al quale si vuole assegnare l'applicazione. Per questo argomento è possibile passare a un gruppo di risorse e nel relativo pannello selezionare l'icona **Accesso**.

     ![selezionare gli utenti](./media/resource-group-create-service-principal-portal/select-users.png)

2. Selezionare **Aggiungi**.

     ![selezionare aggiungi](./media/resource-group-create-service-principal-portal/select-add.png)

3. Selezionare il ruolo **Lettore** o il ruolo al quale si vuole assegnare l'applicazione.

     ![selezionare il ruolo](./media/resource-group-create-service-principal-portal/select-role.png)

4. Quando viene visualizzato per la prima volta l'elenco degli utenti che è possibile aggiungere al ruolo, le applicazioni non verranno visualizzate. Verranno visualizzati solo il gruppo e gli utenti.

     ![mostrare gli utenti](./media/resource-group-create-service-principal-portal/show-users.png)

5. Per individuare l'applicazione, è necessario cercarla. Digitando il nome dell'applicazione, l'elenco delle opzioni disponibili cambierà. Quando l'applicazione viene visualizzata nell'elenco, selezionarla.

     ![assegnare al ruolo](./media/resource-group-create-service-principal-portal/assign-to-role.png)

6. Selezionare **OK** per completare l'assegnazione al ruolo. L'applicazione dovrebbe essere ora visualizzata nell'elenco degli utenti assegnati a un ruolo per il gruppo di risorse.

     ![mostrare](./media/resource-group-create-service-principal-portal/show-app.png)

Per ulteriori informazioni sull'assegnazione di utenti e applicazioni a ruoli tramite il portale, vedere [Gestire gli accessi tramite il portale di gestione di Azure](role-based-access-control-configure.md#manage-access-using-the-azure-management-portal).

## Applicazioni di esempio

Le applicazioni di esempio seguenti illustrano come effettuare l'accesso come entità servizio.

**.NET**

- [Deploy an SSH Enabled VM with a Template with .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/) (Distribuire una macchina virtuale abilitata per SSH con un modello con .NET)
- [Manage Azure resources and resource groups with .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/) (Gestire risorse e gruppi di risorse di Azure con .NET)

**Java**

- [Getting Started with Resources - Deploy Using ARM Template - in Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/) (Introduzione alle risorse: distribuzione tramite il modello di Azure Resource Manager in Java)
- [Getting Started with Resources - Manage Resource Group - in Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//) (Introduzione alle risorse: gestire un gruppo di risorse in Java)

**Python**

- [Deploy an SSH Enabled VM with a Template in Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/) (Distribuire una macchina virtuale abilitata per SSH con un modello in Python)
- [Managing Azure Resource and Resource Groups with Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/) (Gestione di risorse e gruppi di risorse di Azure con Python)

**Node.JS**

- [Deploy an SSH Enabled VM with a Template in Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/) (Distribuire una macchina virtuale abilitata per SSH con un modello in Node.js)
- [Manage Azure resources and resource groups with Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/) (Gestire risorse e gruppi di risorse di Azure con Node.js)

**Ruby**

- [Deploy an SSH Enabled VM with a Template in Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/) (Distribuire una macchina virtuale abilitata per SSH con un modello in Ruby)
- [Managing Azure Resource and Resource Groups with Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/) (Gestione di risorse e gruppi di risorse di Azure con Ruby)


## Passaggi successivi

- Per informazioni su come specificare i criteri di sicurezza, vedere [Controllo degli accessi in base al ruolo](./active-directory/role-based-access-control-configure.md).
- Per una dimostrazione video di questi passaggi, vedere l'articolo relativo all'[abilitazione della gestione a livello di codice di una risorsa di Azure con Azure Active Directory](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory).

<!---HONumber=AcomDC_0720_2016-->