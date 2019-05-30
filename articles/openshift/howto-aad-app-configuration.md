---
title: Integrazione di Azure Active Directory per Azure Red Hat OpenShift | Microsoft Docs
description: Informazioni su come creare un gruppo di sicurezza di Azure AD e un utente di test delle app nel cluster di Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: openshift
manager: jeconnoc
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/13/2019
ms.openlocfilehash: adc5a601a04936a376d7c69b26c2429940ebdf6e
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306464"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Integrazione di Azure Active Directory per Azure Red Hat OpenShift

Se è ancora stato creato un tenant di Azure Active Directory (Azure AD), seguire le istruzioni disponibili nel [creare un tenant di Azure AD per Azure Red Hat OpenShift](howto-create-tenant.md) prima di continuare con queste istruzioni.

Microsoft Azure Red Hat OpenShift necessita delle autorizzazioni per eseguire attività per conto del cluster. Se l'organizzazione non ha ancora un utente di Azure AD, il gruppo di sicurezza di Azure AD o registrazione di un'app di Azure AD da utilizzare come entità servizio, seguire queste istruzioni per crearli.

## <a name="create-a-new-azure-active-directory-user"></a>Creare un nuovo utente di Azure Active Directory

Nel [portale di Azure](https://portal.azure.com), assicurarsi che il tenant viene visualizzato sotto il nome dell'utente in alto a destra del portale:

![Screenshot del portale tenant di elencati in alto a destra](./media/howto-create-tenant/tenant-callout.png) se il tenant errato è visualizzato, fare clic sul nome utente in alto a destra, quindi fare clic su **cambia Directory**, selezionare il tenant corretto dal **tutti Directory** elenco.

Creare un nuovo utente amministratore globale di Azure Active Directory per accedere al cluster di Azure Red Hat OpenShift.

1. Andare alla [-gli utenti per tutti gli utenti](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) pannello.
2. Fare clic su **+ nuovo utente** per aprire il **utente** riquadro.
3. Immettere un **nome** per questo utente.
4. Creare un **nome utente** in base al nome del tenant è stato creato, con `.onmicrosoft.com` aggiunto alla fine. Ad esempio: `yourUserName@yourTenantName.onmicrosoft.com`. Annotare il nome utente specificato. È necessario poter accedere al cluster.
5. Fare clic su **ruolo della Directory** per aprire il riquadro del ruolo di directory e selezionare **amministratore globale** e quindi fare clic su **Ok** nella parte inferiore del riquadro.
6. Nel **utente** riquadro, fare clic su **Show Password** e registrare la password temporanea. Dopo l'accesso la prima volta, verrà richiesto di ripristinare le impostazioni.
7. Nella parte inferiore del riquadro, fare clic su **Create** per creare l'utente.

## <a name="create-an-azure-ad-security-group"></a>Creare un gruppo di sicurezza di Azure AD

Per concedere l'accesso di amministratore del cluster, le appartenenze a un gruppo di sicurezza di Azure AD vengono sincronizzate di OpenShift "osa-customer-gli amministratori del gruppo". Se non specificato, non verrà concesso accesso amministratore cluster.

1. Aprire il [gruppi di Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups) pannello.
2. Fare clic su **+ nuovo gruppo**
3. Specificare un nome di gruppo e una descrizione.
4. Impostare **tipo di gruppo** al **sicurezza**.
5. Impostare **tipo di appartenenza** al **Assigned**.

    Aggiungere l'utente di Azure AD che è stato creato nel passaggio precedente per questo gruppo di sicurezza.

6. Fare clic su **membri** per aprire il **selezionare i membri** riquadro.
7. Nell'elenco dei membri, selezionare l'utente di Azure AD creata in precedenza.
8. Nella parte inferiore del portale, fare clic su **selezionate** e quindi **crea** per creare il gruppo di sicurezza.

    Prendere nota del valore di ID del gruppo

9. Quando viene creato il gruppo, questa verrà visualizzata nell'elenco di tutti i gruppi. Fare clic sul nuovo gruppo.
10. Nella pagina visualizzata copiare il **ID di oggetto**. Si farà riferimento a questo valore come `GROUPID` nella [creare un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md) esercitazione.

## <a name="create-an-azure-ad-app-registration"></a>Creare la registrazione di un'app di Azure AD

È possibile creare automaticamente un client di registrazione app di Azure Active Directory (Azure AD) come parte della creazione del cluster omettendo il `--aad-client-app-id` flag per il `az openshift create` comando. Questa esercitazione illustra come creare la registrazione dell'app Azure AD per motivi di completezza.

Se l'organizzazione non ha ancora la registrazione di un'app di Azure Active Directory (Azure AD) da usare come un'entità servizio, seguire queste istruzioni per crearne uno.

1. Aprire il [pannello registrazioni per l'App](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) e fare clic su **+ registrazione nuova**.
2. Nel **registrare un'applicazione** riquadro, immettere un nome per la registrazione dell'applicazione.
3. Verificare che in **tipi di account supportati** che **gli account in questa directory dell'organizzazione solo** sia selezionata. Questa è la scelta più sicura.
4. Si aggiungerà un URI di reindirizzamento in un secondo momento quando si conosce l'URI del cluster. Scegliere il **registrare** pulsante per creare la registrazione dell'applicazione Azure AD.
5. Nella pagina visualizzata copiare il **ID applicazione (client)** . Si farà riferimento a questo valore come `APPID` nella [creare un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md) esercitazione.

![Screenshot della pagina di oggetti dell'app](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>Creare un segreto client

Generare un segreto client per l'autenticazione all'app di Azure Active Directory.

1. Nel **Manage** sezione della pagina delle registrazioni dell'app, fare clic su **i certificati e i segreti**.
2. Nel **certificati e i segreti** riquadro, fare clic su **+ nuovo segreto client**.  Il **aggiungere un segreto client** viene visualizzato il riquadro.
3. Fornire una **descrizione**.
4. Impostare **Expires** per la durata, ad esempio si preferisce **In 2 Years**.
5. Fare clic su **Add** e il valore della chiave verrà visualizzato nei **i segreti Client** sezione della pagina.
6. Copiare il valore della chiave. Si farà riferimento a questo valore come `SECRET` nella [creare un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md) esercitazione.
 
![Screenshot del riquadro di segreti e certificati](./media/howto-create-tenant/create-key.png)
 
Per altre informazioni sugli oggetti applicazione di Azure, vedi [dell'applicazione e oggetti entità servizio in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals).

Per informazioni dettagliate sulla creazione di una nuova applicazione Azure AD, vedere [registrare un'app con l'endpoint v1.0 di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

## <a name="resources"></a>Risorse

* [Le applicazioni e oggetti entità servizio in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)  
* [Avvio rapido: Registrare un'app con l'endpoint v1.0 di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)  

## <a name="next-steps"></a>Passaggi successivi

Se è stato soddisfatto tutti i [prerequisiti di Azure Red Hat OpenShift](howto-setup-environment.md), si è pronti per creare il primo cluster.

Provare l'esercitazione:
> [!div class="nextstepaction"]
> [Creare un cluster di Azure Red Hat OpenShift](tutorial-create-cluster.md)