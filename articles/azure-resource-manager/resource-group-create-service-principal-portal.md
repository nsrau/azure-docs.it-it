---
title: "Creare un&quot;identità per un&quot;app Azure nel portale | Documentazione Microsoft"
description: "Descrive come creare una nuova applicazione ed entità servizio di Active Directory da usare con il controllo degli accessi in base al ruolo in Gestione risorse di Azure per gestire l&quot;accesso alle risorse."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/17/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 2a9075f4c9f10d05df3b275a39b3629d4ffd095f
ms.openlocfilehash: 3b132bbc89f64928f971f92365691d40c1aab420


---
# <a name="use-portal-to-create-active-directory-application-and-service-principal-that-can-access-resources"></a>Usare il portale per creare un'applicazione Active Directory e un'entità servizio che accedono alle risorse
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-authenticate-service-principal.md)
> * [Interfaccia della riga di comando di Azure](resource-group-authenticate-service-principal-cli.md)
> * [Portale](resource-group-create-service-principal-portal.md)
>
>

Quando un'applicazione deve accedere alle risorse o modificarle, è necessario configurare un'applicazione Active Directory (AD) a cui assegnare le autorizzazioni richieste. Questo approccio è preferibile all'esecuzione dell'app con le credenziali dell'utente per i motivi seguenti:

* È possibile assegnare all'identità dell'app autorizzazioni diverse rispetto a quelle dell'utente. Tali autorizzazioni sono in genere limitate alle specifiche operazioni che devono essere eseguite dall'app.
* Non è necessario modificare le credenziali dell'app in caso di cambiamento delle responsabilità dell'utente. 
* È possibile usare un certificato per automatizzare l'autenticazione in caso di esecuzione di uno script automatico.

Questo argomento illustra come eseguire questa procedura tramite il portale. È incentrato su un'applicazione con un tenant singolo dove si prevede che l'applicazione venga eseguita all'interno di una sola organizzazione. Le applicazioni con un tenant singolo si usano in genere per applicazioni line-of-business eseguite all'interno dell'organizzazione.
 
## <a name="required-permissions"></a>Autorizzazioni necessarie
Per completare questo argomento è necessario disporre di autorizzazioni sufficienti per registrare un'applicazione con Active Directory e assegnare l'applicazione a un ruolo nella sottoscrizione di Azure. Assicurarsi di avere le autorizzazioni appropriate per eseguire questi passaggi.

### <a name="check-active-directory-permissions"></a>Controllare le autorizzazioni di Active Directory
1. Accedere all'account di Azure tramite il [portale di Azure](https://portal.azure.com).
2. Selezionare **Azure Active Directory**.

     ![Selezionare Azure Active Directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)
3. In Active Directory selezionare **Impostazioni utente**.

     ![Selezionare Impostazioni utente](./media/resource-group-create-service-principal-portal/select-user-settings.png)
4. Controllare l'impostazione **Registrazioni per l'app**. Se il valore è **Sì**, gli utenti non amministratori possono registrare app di Active Directory. Questa impostazione indica che qualsiasi utente in Active Directory può registrare un'app. È possibile passare a [Controllare le autorizzazioni di sottoscrizione di Azure](#check-azure-subscription-permissions).

     ![Visualizzare le registrazioni dell'app](./media/resource-group-create-service-principal-portal/view-app-registrations.png)
5. Se Registrazioni per l'app è impostata su **No**, solo gli utenti amministratori possono registrare app. È necessario controllare se l'account è un amministratore per Active Directory. Selezionare **Panoramica** e **Trova un utente** da Attività rapide.

     ![Trova un utente](./media/resource-group-create-service-principal-portal/find-user.png)
6. Cercare il proprio account e selezionarlo.

     ![Cercare un utente](./media/resource-group-create-service-principal-portal/show-user.png)
7. Per il proprio account selezionare **Ruolo della directory**. 

     ![Ruolo della directory](./media/resource-group-create-service-principal-portal/select-directory-role.png)
8. Visualizzare il ruolo assegnato per Active Directory. Se l'account è assegnato al ruolo Utente, ma l'impostazione Registrazioni per l'app (dei passaggi precedenti) è limitata agli utenti amministratori, chiedere all'amministratore di essere assegnati a un ruolo amministrativo o di consentire agli utenti di registrare le app.

     ![Visualizzare il ruolo](./media/resource-group-create-service-principal-portal/view-role.png)

### <a name="check-azure-subscription-permissions"></a>Controllare le autorizzazioni di sottoscrizione di Azure
Nella sottoscrizione di Azure è necessario che l'account disponga dell'accesso `Microsoft.Authorization/*/Write` per assegnare un'app di Active Directory a un ruolo. Questa azione è concessa tramite il ruolo [Proprietario](../active-directory/role-based-access-built-in-roles.md#owner) o [Amministratore accessi utente](../active-directory/role-based-access-built-in-roles.md#user-access-administrator). Se il proprio account è assegnato al ruolo **Collaboratore**, non si dispone dell'autorizzazione appropriata. Se si tenterà di assegnare l'entità servizio a un ruolo si riceverà un errore. 

Per controllare le proprie autorizzazioni di sottoscrizione:

1. Se non è già visualizzato il proprio account Active Directory in seguito ai passaggi precedenti, selezionare **Azure Active Directory** nel pannello a sinistra.

2. Individuare il proprio account Active Directory. Selezionare **Panoramica** e **Trova un utente** da Attività rapide.

     ![Trova un utente](./media/resource-group-create-service-principal-portal/find-user.png)
2. Cercare il proprio account e selezionarlo.

     ![Cercare un utente](./media/resource-group-create-service-principal-portal/show-user.png) 
     
3. Selezionare **Risorse di Azure**.

     ![Selezionare le risorse](./media/resource-group-create-service-principal-portal/select-azure-resources.png) 
3. Visualizzare i propri ruoli assegnati e determinare se si dispone delle autorizzazioni adeguate per assegnare un'app di Active Directory a un ruolo. In caso contrario chiedere all'amministratore della sottoscrizione di essere aggiunti al ruolo Amministratore accessi utente. Nella figura seguente l'utente è assegnato al ruolo Proprietario per due sottoscrizioni, perciò dispone delle autorizzazioni adeguate. 

     ![Visualizzare le autorizzazioni](./media/resource-group-create-service-principal-portal/view-assigned-roles.png)

## <a name="create-an-active-directory-application"></a>Creare un'applicazione di Active Directory
1. Accedere all'account di Azure tramite il [portale di Azure](https://portal.azure.com).
2. Selezionare **Azure Active Directory**.

     ![Selezionare Azure Active Directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

4. Selezionare **Registrazioni per l'app**.   

     ![Selezionare Registrazioni per l'app](./media/resource-group-create-service-principal-portal/select-app-registrations.png)
5. Selezionare **Aggiungi**.

     ![Aggiungere l'app](./media/resource-group-create-service-principal-portal/select-add-app.png)

6. Specificare un nome e un URL per l'applicazione. Selezionare **App Web/API** o **Nativa** come tipo di applicazione da creare. Dopo aver impostato i valori selezionare **Crea**.

     ![assegnare un nome all'applicazione](./media/resource-group-create-service-principal-portal/create-app.png)

L'applicazione è stata creata.

## <a name="get-application-id-and-authentication-key"></a>Ottenere l'ID applicazione e la chiave di autenticazione
Quando si esegue l'accesso a livello di codice sono necessari l'ID dell'applicazione e una chiave di autenticazione. Per ottenere questi valori eseguire la procedura seguente:

1. Da **Registrazioni dell'app** in Active Directory selezionare l'applicazione.

     ![Selezionare l'applicazione](./media/resource-group-create-service-principal-portal/select-app.png)
2. Copiare l'**ID applicazione** e archiviarlo nel codice dell'applicazione. Le applicazioni nella sezione delle [applicazioni di esempio](#sample-applications) indicano questo valore come ID client.

     ![ID CLIENT](./media/resource-group-create-service-principal-portal/copy-app-id.png)
3. Per generare una chiave di autenticazione selezionare **Chiavi**.

     ![Selezionare Chiavi](./media/resource-group-create-service-principal-portal/select-keys.png)
4. Specificare una descrizione e una durata per la chiave. Al termine scegliere **Salva**.

     ![Salvare la chiave](./media/resource-group-create-service-principal-portal/save-key.png)

     Dopo aver salvato la chiave viene visualizzato il valore della chiave. Copiare il valore in quanto non sarà possibile recuperare la chiave in seguito. Il valore della chiave sarà fornito insieme all'ID applicazione per eseguire l'accesso come applicazione. Salvare il valore della chiave in una posizione in cui l'applicazione possa recuperarlo.

     ![chiave salvata](./media/resource-group-create-service-principal-portal/copy-key.png)

## <a name="get-tenant-id"></a>Ottenere l'ID tenant
Quando si esegue l'accesso a livello di codice è necessario specificare l'ID tenant con la richiesta di autenticazione. 

1. Per ottenere l'ID tenant selezionare **Proprietà** per Active Directory. 

     ![Selezionare Poprietà di Active Directory](./media/resource-group-create-service-principal-portal/select-ad-properties.png)

2. Copiare l'**ID directory**. Questo valore è l'ID tenant.

     ![tenant id](./media/resource-group-create-service-principal-portal/copy-directory-id.png)

## <a name="assign-application-to-role"></a>Assegnare l'applicazione al ruolo
Per accedere alle risorse della propria sottoscrizione è necessario assegnare l'applicazione a un ruolo. Decidere quale ruolo rappresenti le autorizzazioni appropriate per l'applicazione. Per informazioni sui ruoli disponibili, vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](../active-directory/role-based-access-built-in-roles.md).

È possibile impostare l'ambito al livello della sottoscrizione, del gruppo di risorse o della risorsa. Le autorizzazioni vengono ereditate a livelli inferiori dell'ambito. Se ad esempio si aggiunge un'applicazione al ruolo Lettore per un gruppo di risorse, l'applicazione può leggere il gruppo di risorse e le risorse in esso contenute.

1. Passare al livello dell'ambito al quale si vuole assegnare l'applicazione. Ad esempio, per assegnare un ruolo a un ambito della sottoscrizione, selezionare **Sottoscrizioni**. In alternativa è possibile selezionare una risorsa o un gruppo di risorse.

     ![selezionare la sottoscrizione](./media/resource-group-create-service-principal-portal/select-subscription.png)

2. Selezionare la sottoscrizione specifica (risorsa o un gruppo di risorse) a cui assegnare l'applicazione.

     ![selezionare la sottoscrizione per l'assegnazione](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

3. Selezionare **Controllo di accesso (IAM)**.

     ![selezionare accesso](./media/resource-group-create-service-principal-portal/select-access-control.png)

4. Selezionare **Aggiungi**.

     ![selezionare aggiungi](./media/resource-group-create-service-principal-portal/select-add.png)
6. Selezionare il ruolo che si desidera assegnare all'applicazione. L'immagine seguente mostra il ruolo **Lettore**.

     ![selezionare il ruolo](./media/resource-group-create-service-principal-portal/select-role.png)

8. Cercare l'applicazione e selezionarla.

     ![Cercare l'app](./media/resource-group-create-service-principal-portal/search-app.png)
9. Selezionare **OK** per completare l'assegnazione del ruolo. L'applicazione ora compare nell'elenco degli utenti assegnati a un ruolo per quell'ambito.

## <a name="log-in-as-the-application"></a>Eseguire l'accesso come applicazione

L'applicazione è ora configurata in Active Directory. Si dispone di un ID e una chiave da usare per eseguire l'accesso come applicazione. L'applicazione viene assegnata a un ruolo che le consente di eseguire alcune azioni. 

Per eseguire l'accesso con PowerShell, vedere [Fornire le credenziali tramite PowerShell](resource-group-authenticate-service-principal.md#provide-credentials-through-powershell).

Per eseguire l'accesso con l'interfaccia della riga di comando di Azure, vedere [Fornire le credenziali tramite l'interfaccia della riga di comando di Azure](resource-group-authenticate-service-principal-cli.md#provide-credentials-through-azure-cli).

Per ottenere il token di accesso per operazioni REST, vedere [Create the request](/rest/api/#create-the-request) (Creare la richiesta).

Per informazioni su come eseguire l'accesso tramite il codice dell'applicazione, vedere le applicazioni di esempio riportate di seguito.

### <a name="sample-applications"></a>Applicazioni di esempio
Le seguenti applicazioni di esempio illustrano come eseguire l'accesso come applicazione AD.

**.NET**

* [Deploy an SSH Enabled VM with a Template with .NET (Distribuire una macchina virtuale abilitata per SSH con un modello con .NET)](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
* [Manage Azure resources and resource groups with .NET (Gestire risorse e gruppi di risorse di Azure con .NET)](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

* [Getting Started with Resources - Deploy Using ARM Template - in Java (Introduzione alle risorse: distribuzione tramite il modello di Azure Resource Manager in Java)](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
* [Getting Started with Resources - Manage Resource Group - in Java (Introduzione alle risorse: gestire un gruppo di risorse in Java)](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

* [Deploy an SSH Enabled VM with a Template in Python (Distribuire una macchina virtuale abilitata per SSH con un modello in Python)](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
* [Managing Azure Resource and Resource Groups with Python (Gestione di risorse e gruppi di risorse di Azure con Python)](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.JS**

* [Deploy an SSH Enabled VM with a Template in Node.js (Distribuire una macchina virtuale abilitata per SSH con un modello in Node.js)](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
* [Manage Azure resources and resource groups with Node.js (Gestire risorse e gruppi di risorse di Azure con Node.js)](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

* [Deploy an SSH Enabled VM with a Template in Ruby (Distribuire una macchina virtuale abilitata per SSH con un modello in Ruby)](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
* [Managing Azure Resource and Resource Groups with Ruby (Gestione di risorse e gruppi di risorse di Azure con Ruby)](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Passaggi successivi
* Per configurare un'applicazione multi-tenant, vedere [Guida per gli sviluppatori all'autorizzazione con l'API di Azure Resource Manager](resource-manager-api-authentication.md).
* Per informazioni su come specificare i criteri di sicurezza, vedere [Controllo degli accessi in base al ruolo nel portale di Azure](../active-directory/role-based-access-control-configure.md).  




<!--HONumber=Jan17_HO4-->


