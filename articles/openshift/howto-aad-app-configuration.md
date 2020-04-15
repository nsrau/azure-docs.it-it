---
title: Integrazione di Azure Active Directory per Azure Red Hat OpenShift
description: Informazioni su come creare un gruppo di sicurezza di Azure AD e un utente per testare le app nel cluster Microsoft Azure Red Hat OpenShift.Learn how to create an Azure AD security group and user for testing apps on your Microsoft Azure Red Hat OpenShift cluster.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: f6c4fb5caf746650f95872d50afe31e5693422be
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382910"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Integrazione di Azure Active Directory per Azure Red Hat OpenShift

Se non è già stato creato un tenant di Azure Active Directory (Azure AD), seguire le istruzioni in Creare un tenant di [Azure AD per Azure Red Hat OpenShift](howto-create-tenant.md) prima di continuare con queste istruzioni.

Microsoft Azure Red Hat OpenShift ha bisogno di autorizzazioni per eseguire attività per conto del cluster. Se l'organizzazione non dispone già di un utente di Azure AD, di un gruppo di sicurezza di Azure AD o di una registrazione dell'app Azure AD da usare come entità servizio, seguire queste istruzioni per crearle.

## <a name="create-a-new-azure-active-directory-user"></a>Creare un nuovo utente di Azure Active Directory

Nel [portale](https://portal.azure.com)di Azure verificare che il tenant venga visualizzato sotto il nome utente nella parte superiore destra del portale:

![Screenshot del portale con tenant](./media/howto-create-tenant/tenant-callout.png) elencato in alto a destra Se viene visualizzato il tenant errato, fare clic sul proprio nome utente in alto a destra, quindi fare clic su **Cambia directory**e selezionare il tenant corretto dall'elenco **Tutte le directory.**

Creare un nuovo utente 'Proprietario' di Azure Active Directory per accedere al cluster Azure Red Hat OpenShift.Create a new Azure Active Directory 'Owner' user to sign in to your Azure Red Hat OpenShift cluster.

1. Passare al pannello [Utenti-Tutti gli utenti.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)
2. Fare clic su **Nuovo utente** per aprire il riquadro **Utente.**
3. Immettere un **Nome** per l'utente.
4. Creare un **nome utente** basato sul nome `.onmicrosoft.com` del tenant creato, con aggiunta alla fine. Ad esempio: `yourUserName@yourTenantName.onmicrosoft.com`. Annotare questo nome utente. Sarà necessario per accedere al cluster.
5. Fare clic su **Ruolo directory** per aprire il riquadro del ruolo di directory e selezionare **Proprietario** e quindi fare clic su **OK** nella parte inferiore del riquadro.
6. Nel riquadro **Utente** fare clic su **Mostra password** e registrare la password temporanea. Dopo aver eseguito l'accesso per la prima volta, ti verrà richiesto di reimpostarlo.
7. Nella parte inferiore del riquadro fare clic su **Crea** per creare l'utente.

## <a name="create-an-azure-ad-security-group"></a>Creare un gruppo di sicurezza di Azure ADCreate an Azure AD security group

Per concedere l'accesso di amministratore del cluster, le appartenenze in un gruppo di sicurezza di Azure AD vengono sincronizzate nel gruppo OpenShift "osa-customer-admins". Se non specificato, non verrà concesso alcun accesso amministratore del cluster.

1. Aprire il pannello Gruppi di Azure Active Directory.Open the [Azure Active Directory groups](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups) blade.
2. Fare clic su **Nuovo gruppo**.
3. Specificare un nome e una descrizione per il gruppo.
4. Impostare **Tipo di gruppo su** **Sicurezza**.
5. Impostare **Tipo di appartenenza** **su Assegnato**.

    Aggiungere l'utente di Azure AD creato nel passaggio precedente a questo gruppo di sicurezza.

6. Fare clic su **Membri** per aprire il riquadro **Seleziona membri.**
7. Nell'elenco dei membri selezionare l'utente di Azure AD creato in precedenza.
8. Nella parte inferiore del portale fare clic su **Seleziona** e quindi **su Crea** per creare il gruppo di sicurezza.

    Annotare il valore ID gruppo.

9. Quando il gruppo viene creato, verrà visualizzato nell'elenco di tutti i gruppi. Fare clic sul nuovo gruppo.
10. Nella pagina visualizzata copiare **l'ID oggetto.** Faremo riferimento a `GROUPID` questo valore come nell'esercitazione [sul cluster Create an Azure Red Hat OpenShift.We](tutorial-create-cluster.md) will refer to this value as in the Create an Azure Red Hat OpenShift cluster tutorial.

> [!IMPORTANT]
> Per sincronizzare questo gruppo con il gruppo OpenShift osa-customer-admins, creare il cluster usando l'interfaccia della riga di comando di Azure.To sync this group with the osa-customer-admins OpenShift group, create the cluster by using the Azure CLI. Il portale di Azure attualmente non dispone di un campo per impostare questo gruppo.

## <a name="create-an-azure-ad-app-registration"></a>Creare una registrazione dell'app Azure ADCreate an Azure AD app registration

È possibile creare automaticamente un client di registrazione dell'app Azure Active Directory `--aad-client-app-id` (Azure `az openshift create` AD) come parte della creazione del cluster omettendo il flag al comando. Questa esercitazione illustra come creare la registrazione dell'app Azure AD per completezza.

Se l'organizzazione non dispone già di una registrazione dell'app Azure Active Directory (Azure AD) da usare come entità servizio, seguire queste istruzioni per crearne una.

1. Aprire il [pannello Registrazioni app](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) e fare clic su **Nuova registrazione**.
2. Nel riquadro **Registra un'applicazione** immettere un nome per la registrazione dell'applicazione.
3. Assicurarsi che in **Tipi di account supportati** sia selezionata solo **l'opzione Account in questa directory dell'organizzazione.** Questa è la scelta più sicura.
4. Si aggiungerà un URI di reindirizzamento in un secondo momento dopo aver conosciuto l'URI del cluster. Fare clic sul pulsante **Registra** per creare la registrazione dell'applicazione Azure AD.
5. Nella pagina visualizzata copiare **l'ID applicazione (client)**. Faremo riferimento a `APPID` questo valore come nell'esercitazione [sul cluster Create an Azure Red Hat OpenShift.We](tutorial-create-cluster.md) will refer to this value as in the Create an Azure Red Hat OpenShift cluster tutorial.

![Schermata della pagina degli oggetti dell'app](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>Creare un segreto client

Generare un segreto client per l'autenticazione dell'app in Azure Active Directory.Generate a client secret for authenticating your app to Azure Active Directory.

1. Nella sezione **Gestisci** della pagina Registrazioni app fare clic su **Certificati & segreti**.
2. Nel riquadro **Certificati & segreti** fare clic su Nuovo **segreto client**.  Viene visualizzato il riquadro **Aggiungi segreto client.**
3. Specificare una **descrizione**.
4. Impostare **Scadenza** sulla durata preferita, ad esempio **Tra 2 anni.**
5. Fare clic su **Aggiungi** per visualizzare il valore della chiave nella sezione **Segreti client** della pagina.
6. Copiare il valore della chiave. Faremo riferimento a `SECRET` questo valore come nell'esercitazione [sul cluster Create an Azure Red Hat OpenShift.We](tutorial-create-cluster.md) will refer to this value as in the Create an Azure Red Hat OpenShift cluster tutorial.

![Screenshot del riquadro dei certificati e dei segreti](./media/howto-create-tenant/create-key.png)

Per altre informazioni sugli oggetti applicazione di Azure, vedere Oggetti applicazione e oggetti entità servizio in Azure Active Directory.For more information about Azure Application Objects, see [Application and service principal objects in Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

Per informazioni dettagliate sulla creazione di una nuova applicazione Azure AD, vedere [Registrare un'app con l'endpoint di Azure Active Directory v1.0.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

## <a name="add-api-permissions"></a>Aggiungere autorizzazioni API

[//]: # (Non modificare Microsoft Graph. Non funziona con Microsoft Graph.)
1. Nella sezione **Gestisci** fare clic su **Autorizzazioni API**
2. Fare clic su **Aggiungi autorizzazione** e selezionare Grafico di Azure **Active Directory,** quindi **Autorizzazioni delegate**.
> [!NOTE]
> Assicurarsi di aver selezionato il riquadro "Azure Active Directory Graph" e non il riquadro "Microsoft Graph".

3. Espandere **Utente** nell'elenco seguente e abilitare l'autorizzazione **User.Read.** Se **User.Read** è abilitato per impostazione predefinita, verificare che sia l'autorizzazione Azure **Active Directory Graph** **User.Read**.
4. Scorrere verso l'alto e selezionare **Autorizzazioni applicazione**.
5. Espandere **Directory** nell'elenco seguente e abilitare **Directory.ReadAll**.
6. Fare clic su **Aggiungi autorizzazioni** per accettare le modifiche.
7. Il pannello delle autorizzazioni API dovrebbe ora mostrare sia *User.Read* che *Directory.ReadAll*. Si prega di notare l'avviso nella colonna richiesta consenso **amministratore** accanto a *Directory.ReadAll*.
8. Se si è *l'amministratore*della sottoscrizione di Azure , fare clic su **Concedi il consenso dell'amministratore per *Nome sottoscrizione* ** di seguito. Se non si è *l'amministratore*della sottoscrizione di Azure , richiedere il consenso dell'amministratore.

![Screenshot del pannello delle autorizzazioni API. User.Read e Directory.ReadAll autorizzazioni aggiunte, consenso amministratore necessario per Directory.ReadAll](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> La sincronizzazione del gruppo di amministratori del cluster funzionerà solo dopo che è stato concesso il consenso. Verrà visualizzato un cerchio verde con un segno di spunta e un messaggio "Concesso per il *nome dell'abbonamento"* nella colonna Obbligatorio consenso *dell'amministratore.*

Per informazioni dettagliate sulla gestione degli amministratori e di altri ruoli, vedere Aggiungere o modificare gli amministratori della sottoscrizione di Azure.For details on managing administrators and other roles, see [Add or change Azure subscription administrators.](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator)

## <a name="resources"></a>Risorse

* [Applicazioni e oggetti entità servizio in Azure Active DirectoryApplications and service principal objects in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)
* [Guida introduttiva: registrare un'app con l'endpoint v1.0 di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

## <a name="next-steps"></a>Passaggi successivi

Se sono stati soddisfatti tutti i prerequisiti di [Azure Red Hat OpenShift,](howto-setup-environment.md)si è pronti per creare il primo cluster.

Prova il tutorial:
> [!div class="nextstepaction"]
> [Creare un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md)
