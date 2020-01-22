---
title: Integrazione di Azure Active Directory per Azure Red Hat OpenShift
description: Informazioni su come creare un gruppo di sicurezza e un utente Azure AD per il test delle app nel cluster Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 09ae896d26d534d3f9d5651834e181d37581c8f3
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76288958"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Integrazione di Azure Active Directory per Azure Red Hat OpenShift

Se non è già stato creato un tenant di Azure Active Directory (Azure AD), seguire le istruzioni riportate in [creare un tenant Azure ad per Azure Red Hat OpenShift](howto-create-tenant.md) prima di continuare con queste istruzioni.

Microsoft Azure Red Hat OpenShift richiede le autorizzazioni per eseguire attività per conto del cluster. Se l'organizzazione non dispone già di un utente Azure AD, Azure AD gruppo di sicurezza o di una registrazione dell'app Azure AD da usare come entità servizio, seguire queste istruzioni per crearli.

## <a name="create-a-new-azure-active-directory-user"></a>Creare un nuovo utente di Azure Active Directory

Nella [portale di Azure](https://portal.azure.com)verificare che il tenant sia visualizzato sotto il nome utente in alto a destra nel portale:

![screenshot del portale con tenant elencato in alto a destra](./media/howto-create-tenant/tenant-callout.png) se viene visualizzato il tenant errato, fare clic sul nome utente in alto a destra, quindi fare clic su **Cambia directory**e selezionare il tenant corretto dall'elenco **tutte le directory** .

Creare un nuovo utente amministratore globale Azure Active Directory per accedere al cluster Azure Red Hat OpenShift.

1. Passare al pannello [utenti-tutti gli utenti](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) .
2. Fare clic su **+ nuovo utente** per aprire il riquadro **utente** .
3. Immettere un **nome** per l'utente.
4. Creare un **nome utente** in base al nome del tenant creato, con `.onmicrosoft.com` accodato alla fine. Ad esempio: `yourUserName@yourTenantName.onmicrosoft.com`. Annotare il nome utente. Sarà necessario per accedere al cluster.
5. Fare clic su **ruolo della directory** per aprire il riquadro del ruolo della directory e selezionare **amministratore globale** , quindi fare clic su **OK** nella parte inferiore del riquadro.
6. Nel riquadro **utente** fare clic su **Mostra password** e registrare la password temporanea. Dopo aver eseguito l'accesso per la prima volta, verrà richiesto di reimpostarlo.
7. Nella parte inferiore del riquadro fare clic su **Crea** per creare l'utente.

## <a name="create-an-azure-ad-security-group"></a>Creare un gruppo di sicurezza Azure AD

Per concedere l'accesso amministratore del cluster, le appartenenze in un gruppo di sicurezza Azure AD vengono sincronizzate nel gruppo OpenShift "osa-Customer-Admins". Se non è specificato, non verrà concesso alcun accesso amministrativo al cluster.

1. Aprire il pannello [gruppi di Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups) .
2. Fare clic su **+ nuovo gruppo**.
3. Specificare un nome e una descrizione per il gruppo.
4. Impostare **tipo di gruppo** su **sicurezza**.
5. Impostare **tipo di appartenenza** su **assegnato**.

    Aggiungere l'utente Azure AD creato nel passaggio precedente a questo gruppo di sicurezza.

6. Fare clic su **membri** per aprire il riquadro **Seleziona membri** .
7. Nell'elenco membri selezionare l'utente Azure AD creato in precedenza.
8. Nella parte inferiore del portale, fare clic su **Seleziona** e quindi su **Crea** per creare il gruppo di sicurezza.

    Annotare il valore ID gruppo.

9. Quando il gruppo viene creato, verrà visualizzato nell'elenco di tutti i gruppi. Fare clic sul nuovo gruppo.
10. Nella pagina visualizzata, copiare l' **ID oggetto**. Si fa riferimento a questo valore come `GROUPID` nell'esercitazione [creare un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md) .

## <a name="create-an-azure-ad-app-registration"></a>Creare una registrazione dell'app Azure AD

È possibile creare automaticamente un client di registrazione app Azure Active Directory (Azure AD) come parte della creazione del cluster omettendo il flag `--aad-client-app-id` al comando `az openshift create`. Questa esercitazione illustra come creare la registrazione dell'app Azure AD per completezza.

Se l'organizzazione non dispone già di una registrazione dell'app Azure Active Directory (Azure AD) da usare come entità servizio, seguire queste istruzioni per crearne una.

1. Aprire il pannello [registrazioni app](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) e fare clic su **+ nuova registrazione**.
2. Nel riquadro **registra un'applicazione** immettere un nome per la registrazione dell'applicazione.
3. Verificare che sia selezionata l'opzione in **tipi di account supportati** **in questa directory aziendale** . Questa è la scelta più sicura.
4. Si aggiungerà un URI di reindirizzamento in un secondo momento, dopo aver conosciuto l'URI del cluster. Fare clic sul pulsante **Register (registra** ) per creare la registrazione dell'applicazione Azure ad.
5. Nella pagina visualizzata, copiare l' **ID applicazione (client)** . Si fa riferimento a questo valore come `APPID` nell'esercitazione [creare un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md) .

![Screenshot della pagina dell'oggetto app](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>Creare un segreto client

Generare un segreto client per l'autenticazione dell'app Azure Active Directory.

1. Nella sezione **Gestisci** della pagina registrazioni per l'app fare clic su **certificati & segreti**.
2. Nel riquadro **certificati & segreti** fare clic su **+ nuovo segreto client**.  Viene visualizzato il riquadro **Aggiungi un segreto client** .
3. Fornire una **Descrizione**.
4. Il set **scade** fino alla durata desiderata, ad esempio **in 2 anni**.
5. Fare clic su **Aggiungi** e il valore della chiave verrà visualizzato nella sezione **segreti client** della pagina.
6. Copiare il valore della chiave. We will refer to this value as `SECRET` in the [Create an Azure Red Hat OpenShift cluster](tutorial-create-cluster.md) tutorial.

![Screenshot of the certificates and secrets pane](./media/howto-create-tenant/create-key.png)

For more information about Azure Application Objects, see [Application and service principal objects in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals).

For details on creating a new Azure AD application, see [Register an app with the Azure Active Directory v1.0 endpoint](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

## <a name="add-api-permissions"></a>Aggiungere autorizzazioni API

1. In the **Manage** section click **API permissions**.
2. Click **Add permission** and select **Azure Active Directory Graph** then **Delegated permissions**. 
3. Expand **User** on the list below and enable the **User.Read** permission. If **User.Read** is enabled by default, ensure that it is the **Azure Active Directory Graph** permission **User.Read**, *not* the **Microsoft Graph** permission **User.Read**.
4. Scroll up and select **Application permissions**.
5. Expand **Directory** on the list below and enable **Directory.ReadAll**
6. Click **Add permissions** to accept the changes.
7. The API permissions panel should now show both *User.Read* and *Directory.ReadAll*. Please note the warning in **Admin consent required** column next to *Directory.ReadAll*.
8. If you are the *Azure Subscription Administrator*, click **Grant admin consent for *Subscription Name*** below. If you are not the *Azure Subscription Administrator*, request the consent from your administrator.
![Screenshot of the API permissions panel. User.Read and Directory.ReadAll permissions added, admin consent required for Directory.ReadAll](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> Synchronization of the cluster administrators group will work only after consent has been granted. You will see a green circle with a checkmark and a message "Granted for *Subscription Name*" in the *Admin consent required* column.

For details on managing administrators and other roles, see [Add or change Azure subscription administrators](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator).

## <a name="resources"></a>Risorse

* [Applications and service principal objects in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)
* [Quickstart: Register an app with the Azure Active Directory v1.0 endpoint](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

## <a name="next-steps"></a>Passaggi successivi

If you've met all the [Azure Red Hat OpenShift prerequisites](howto-setup-environment.md), you're ready to create your first cluster!

Try the tutorial:
> [!div class="nextstepaction"]
> [Creare un cluster di Azure Red Hat OpenShift](tutorial-create-cluster.md)
