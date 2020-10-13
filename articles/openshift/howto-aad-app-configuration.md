---
title: Integrazione di Azure Active Directory per Azure Red Hat OpenShift
description: Informazioni su come creare un gruppo di sicurezza e un utente Azure AD per il test delle app nel cluster Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 8d3326587d9c03e0a3960016b8ba3668c825ee6d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89470086"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Integrazione di Azure Active Directory per Azure Red Hat OpenShift

Se non è già stato creato un tenant di Azure Active Directory (Azure AD), seguire le istruzioni riportate in [creare un tenant Azure ad per Azure Red Hat OpenShift](howto-create-tenant.md) prima di continuare con queste istruzioni.

Microsoft Azure Red Hat OpenShift richiede le autorizzazioni per eseguire attività per conto del cluster. Se l'organizzazione non dispone già di un utente Azure AD, Azure AD gruppo di sicurezza o di una registrazione dell'app Azure AD da usare come entità servizio, seguire queste istruzioni per crearli.

## <a name="create-a-new-azure-active-directory-user"></a>Creare un nuovo utente di Azure Active Directory

Nella [portale di Azure](https://portal.azure.com)verificare che il tenant sia visualizzato sotto il nome utente in alto a destra nel portale:

![Screenshot del portale con tenant elencato in alto a destra ](./media/howto-create-tenant/tenant-callout.png) se viene visualizzato il tenant errato, fare clic sul nome utente in alto a destra, quindi fare clic su **Cambia directory**e selezionare il tenant corretto dall'elenco **tutte le directory** .

Creare un nuovo utente Azure Active Directory ' proprietario ' per accedere al cluster Azure Red Hat OpenShift.

1. Passare al pannello [utenti-tutti gli utenti](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) .
2. Fare clic su **+ nuovo utente** per aprire il riquadro **utente** .
3. Immettere un **nome** per l'utente.
4. Creare un **nome utente** in base al nome del tenant creato, con  `.onmicrosoft.com` accodato alla fine. Ad esempio: `yourUserName@yourTenantName.onmicrosoft.com`. Annotare il nome utente. Sarà necessario per accedere al cluster.
5. Fare clic su **ruolo della directory** per aprire il riquadro del ruolo della directory e selezionare **proprietario** , quindi fare clic su **OK** nella parte inferiore del riquadro.
6. Nel riquadro **utente** fare clic su **Mostra password** e registrare la password temporanea. Dopo aver eseguito l'accesso per la prima volta, verrà richiesto di reimpostarlo.
7. Nella parte inferiore del riquadro fare clic su **Crea** per creare l'utente.

## <a name="create-an-azure-ad-security-group"></a>Creare un gruppo di sicurezza di Azure AD

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

> [!IMPORTANT]
> Per sincronizzare questo gruppo con il gruppo osa-Customer-Admins OpenShift, creare il cluster usando l'interfaccia della riga di comando di Azure. Il portale di Azure attualmente non dispone di un campo per impostare questo gruppo.

## <a name="create-an-azure-ad-app-registration"></a>Creare una registrazione dell'app Azure AD

È possibile creare automaticamente un client di registrazione app Azure Active Directory (Azure AD) come parte della creazione del cluster omettendo il `--aad-client-app-id` flag per il `az openshift create` comando. Questa esercitazione illustra come creare la registrazione dell'app Azure AD per completezza.

Se l'organizzazione non dispone già di una registrazione dell'app Azure Active Directory (Azure AD) da usare come entità servizio, seguire queste istruzioni per crearne una.

1. Aprire il pannello [registrazioni app](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) e fare clic su **+ nuova registrazione**.
2. Nel riquadro **registra un'applicazione** immettere un nome per la registrazione dell'applicazione.
3. Verificare che sia selezionata l'opzione in **tipi di account supportati** **in questa directory aziendale** . Questa è la scelta più sicura.
4. Si aggiungerà un URI di reindirizzamento in un secondo momento, dopo aver conosciuto l'URI del cluster. Fare clic sul pulsante **Register (registra** ) per creare la registrazione dell'applicazione Azure ad.
5. Nella pagina visualizzata, copiare l' **ID applicazione (client)**. Si fa riferimento a questo valore come `APPID` nell'esercitazione [creare un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md) .

![Screenshot della pagina dell'oggetto app](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>Creare un segreto client

Generare un segreto client per l'autenticazione dell'app Azure Active Directory.

1. Nella sezione **Gestisci** della pagina registrazioni per l'app fare clic su **certificati & segreti**.
2. Nel riquadro **certificati & segreti** fare clic su **+ nuovo segreto client**.  Viene visualizzato il riquadro **Aggiungi un segreto client** .
3. Fornire una **Descrizione**.
4. Il set **scade** fino alla durata desiderata, ad esempio **in 2 anni**.
5. Fare clic su **Aggiungi** e il valore della chiave verrà visualizzato nella sezione **segreti client** della pagina.
6. Copiare il valore della chiave. Si fa riferimento a questo valore come `SECRET` nell'esercitazione [creare un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md) .

![Screenshot del riquadro certificati e segreti](./media/howto-create-tenant/create-key.png)

Per ulteriori informazioni sugli oggetti applicazione Azure, vedere [oggetti applicazione e oggetti entità servizio in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Per informazioni dettagliate sulla creazione di una nuova applicazione Azure AD, vedere [registrare un'app con l'endpoint Azure Active Directory v 1.0](../active-directory/develop/quickstart-register-app.md).

## <a name="add-api-permissions"></a>Aggiungere autorizzazioni API

[//]: # (Non modificare in Microsoft Graph. Non funziona con Microsoft Graph.)
1. Nella sezione **Gestisci** fare clic su **autorizzazioni API**
2. Fare clic su **Aggiungi autorizzazione** e selezionare **Azure Active Directory grafico** , quindi **autorizzazioni delegate**.
> [!NOTE]
> Assicurarsi di aver selezionato "Azure Active Directory Graph" e non il riquadro "Microsoft Graph".

3. Espandere **utente** nell'elenco seguente e abilitare l'autorizzazione **User. Read** . Se **User. Read** è abilitato per impostazione predefinita, assicurarsi che sia il **Azure Active Directory** utente dell'autorizzazione Graph **. Read**.
4. Scorrere verso l'alto e selezionare le **autorizzazioni dell'applicazione**.
5. Espandere **directory** nell'elenco seguente e abilitare **Directory. ReadAll**.
6. Fare clic su **Aggiungi autorizzazioni** per accettare le modifiche.
7. Il pannello autorizzazioni API dovrebbe ora visualizzare sia *User. Read* che *Directory. ReadAll*. Si noti la colonna avviso nella colonna **consenso dell'amministratore** accanto a *Directory. ReadAll*.
8. Se si è l' *amministratore della sottoscrizione di Azure*, fare clic su **concedi il consenso dell'amministratore per il *nome della sottoscrizione* ** . Se non si è l' *amministratore della sottoscrizione di Azure*, richiedere il consenso dell'amministratore.

![Screenshot del pannello delle autorizzazioni dell'API. Sono state aggiunte le autorizzazioni User. Read e directory. ReadAll. il consenso dell'amministratore è necessario per directory. ReadAll](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> La sincronizzazione del gruppo Administrators del cluster funzionerà solo dopo che è stato concesso il consenso. Viene visualizzato un cerchio verde con un segno di spunta e un messaggio "concesso per *nome sottoscrizione*" nella colonna *autorizzazione amministratore richiesta* .

Per informazioni dettagliate sulla gestione degli amministratori e di altri ruoli, vedere [aggiungere o modificare gli amministratori della sottoscrizione di Azure](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="resources"></a>Risorse

* [Applicazioni e oggetti entità servizio in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md)
* [Guida introduttiva: registrare un'app con l'endpoint v1.0 di Azure Active Directory](../active-directory/develop/quickstart-register-app.md)

## <a name="next-steps"></a>Passaggi successivi

Se sono stati soddisfatti tutti i [prerequisiti di Azure Red Hat OpenShift](howto-setup-environment.md), si è pronti per creare il primo cluster.

Provare l'esercitazione:
> [!div class="nextstepaction"]
> [Creare un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md)