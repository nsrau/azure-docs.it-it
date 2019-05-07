---
title: Creare una registrazione di app di Azure AD e l'utente per Azure Red Hat OpenShift | Microsoft Docs
description: Informazioni su come creare un'entità servizio, generare un URL di callback autenticazione e il segreto client e creare un nuovo utente di Active Directory per il test delle app nel cluster di Microsoft Azure Red Hat OpenShift.
author: tylermsft
ms.author: twhitney
ms.service: openshift
manager: jeconnoc
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/06/2019
ms.openlocfilehash: de3f3c30848d26ea399bcccc29a6149a149f6a55
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078521"
---
# <a name="create-an-azure-ad-app-registration-and-user-for-azure-red-hat-openshift"></a>Creare una registrazione di app di Azure AD e l'utente per Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift necessita delle autorizzazioni per eseguire attività per conto del cluster. Se l'organizzazione non ha ancora la registrazione di un'app di Azure Active Directory (Azure AD) da utilizzare come entità servizio, seguire queste istruzioni per crearne uno.

Questo argomento si conclude con le istruzioni per la creazione di un nuovo utente di Azure AD che sarà necessario accedere alle App in esecuzione nel cluster Azure Red Hat OpenShift.

Se è ancora stato creato un tenant di Azure AD, seguire le istruzioni disponibili nel [creare un tenant di Azure AD per Azure Red Hat OpenShift](howto-create-tenant.md) prima di continuare con queste istruzioni.

## <a name="create-a-new-app-registration"></a>Creare una nuova registrazione di app

Un'applicazione che vuole usare la funzionalità di Azure AD deve prima essere registrata in un tenant di Azure AD. Questo processo di registrazione comporta l'assegnazione di dettagli di Azure AD sull'applicazione, ad esempio l'URL in cui si trova l'app, l'URL per inviare risposte dopo che un utente viene autenticato, l'URI che identifica l'app e così via.

1. Nel [portale di Azure](https://portal.azure.com), assicurarsi che il tenant viene visualizzato sotto il nome dell'utente in alto a destra del portale:

    ![Screenshot del portale tenant di elencati in alto a destra][tenantcallout] se il tenant errato è visualizzato, fare clic sul nome utente in alto a destra, quindi fare clic su **cambia Directory**, selezionare il tenant corretto dal **tutti Directory** elenco.

2. Aprire il [pannello registrazioni per l'App](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) e fare clic su **registrazione nuova applicazione**.
3. Nel **Create** riquadro, immettere un nome descrittivo per l'oggetto applicazione.
4. Assicurarsi che **tipo di applicazione** è impostata su *app Web/API*.
5. Creare un **Sign-on di URL** adottando il modello seguente:

    ```
    https://<cluster-name>.<azure-region>.cloudapp.azure.com
    ```

    . . . in cui `<cluster-name>` è il nome desiderato del cluster di Azure Red Hat OpenShift e `<azure-region>` è la [area di Azure che ospita il cluster di Azure Red Hat OpenShift](supported-resources.md#azure-regions). Ad esempio, se il nome del cluster deve essere `contoso`, e si creerà nel `eastus` area, il nome di dominio completo (FQDN) che verrà immesso per il **URL Sign-on** sarebbe `https://contoso.eastus.cloudapp.azure.com`

    > [!IMPORTANT]
    > Il nome del cluster deve contenere solo lettere minuscolo e l'URL di FQDN deve essere univoco.
    > Assicurarsi di che scegliere un nome sufficientemente distinto per il cluster.

    Prendere nota del nome del cluster e URL sign-on, ovvero saranno necessarie in un secondo momento di accedere alle App in esecuzione nel cluster. Si farà riferimento al nome del cluster come `CLUSTER_NAME`e questo URL sign-on come `FQDN` nel [creare un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md) esercitazione.

6. Verificare che il **Sign-on di URL** valore convalida con un segno di spunta verde. (Premere il tasto Tab per lo stato attivo fuori il *Sign-on di URL* campo ed eseguire il controllo di convalida.)
7. Scegliere il **Create** pulsante per creare l'oggetto di applicazione di Azure AD.
8. Nel **App registrata** pagina visualizzata, copia verso il basso il **ID applicazione**. Si farà riferimento a questo valore come `APPID` nella [creare un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md) esercitazione.

    ![Screenshot della casella di testo ID applicazione][appidimage]
    
Per altre informazioni sugli oggetti applicazione di Azure, vedi [dell'applicazione e oggetti entità servizio in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals).

Per informazioni dettagliate sulla creazione di una nuova applicazione Azure AD, vedere [registrare un'app con l'endpoint v1.0 di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

### <a name="create-a-client-secret"></a>Creare un segreto client

A questo punto si è pronti per generare un segreto client per l'autenticazione all'app di Azure Active Directory.

1. Dal **App registrata** pagina, fare clic su **impostazioni** per aprire le impostazioni per l'app registrata.
2. Nel **le impostazioni** riquadro che appare, fare clic su **chiavi**.  Il **chiavi** viene visualizzato il riquadro.
![Screenshot della pagina Crea chiave nel portale][createkeyimage]
3. Fornire una chiave **descrizione**.
4. Impostare un valore per **Expires**, ad esempio *In 2 Years*.
5. Fare clic su **salvare** e verrà visualizzato il valore della chiave.
6. Copiare il valore della chiave. Si farà riferimento a questo valore come `SECRET` nella [creare un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md) esercitazione.

### <a name="create-a-reply-url"></a>Creare un URL di risposta

Azure AD Usa l'oggetto di app *URL di risposta* per specificare il callback da utilizzare per autorizzare l'app. Nel caso di un'applicazione web o API web, l'URL di risposta è il percorso in cui Azure AD invierà la risposta di autenticazione, compreso un token se l'autenticazione ha esito positivo.

Anche il più piccolo non corrispondente (finali barra mancanti, maiuscole e minuscole diverse) causerà l'esito negativo dell'operazione di rilascio di token e non saranno in grado di accedere.

1. Tornare al **impostazioni** riquadro (è possibile fare clic su **impostazioni** nella barra di navigazione nella parte superiore del portale) e fare clic su **gli URL di risposta** sulla destra.  Il **URL di risposta** viene visualizzato il riquadro.
2. L'URL di risposta prima nell'elenco sarà il `FQDN` valore ottenuto nel passaggio 6 [creare una nuova registrazione app](#create-a-new-app-registration). Apportare le modifiche e accodare `/oauth2callback/Azure%20AD`.  Ad esempio, l'URL di risposta a questo punto dovrebbe essere simile `https://mycluster.eastus.cloudapp.azure.com/oauth2callback/Azure%20AD`
3. Fare clic su **salvare** per salvare l'URL di risposta.

## <a name="create-a-new-active-directory-user"></a>Creare un nuovo utente di Active Directory

Creare un nuovo utente in Active Directory da usare per accedere all'app in esecuzione nel cluster Azure Red Hat OpenShift.

1. Andare alla [utenti: tutti gli utenti](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) pannello.
2. Fare clic su **Nuovo utente**. Il **utente** viene visualizzato il riquadro.
3. Immettere un **nome** desiderato per l'utente corrente.
4. Creare un **nome utente** in base al nome del tenant è stato creato con `.onmicrosoft.com` aggiunto alla fine. Ad esempio: `yourUserName@yourTenantName.onmicrosoft.com`. Annotare il nome utente specificato. È necessario poter accedere a usare l'app nel cluster.
5. Fare clic su **ruolo della Directory** e selezionare **amministratore globale** e quindi fare clic su **Ok** nella parte inferiore del riquadro.
6. Al centro della **utente** riquadro, fare clic su **Show Password** e registrare la password temporanea. Dopo l'accesso la prima volta, verrà richiesto di ripristinare le impostazioni.
7. Nella parte inferiore del riquadro, fare clic su **Create** per creare l'utente.

## <a name="resources"></a>Risorse

* [Le applicazioni e oggetti entità servizio in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)  
* [Guida introduttiva: Registrare un'app con l'endpoint v1.0 di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)  

[appidimage]: ./media/howto-create-tenant/get-app-id.png
[createkeyimage]: ./media/howto-create-tenant/create-key.png
[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="next-steps"></a>Passaggi successivi

Se è stato soddisfatto tutti i [prerequisiti di Azure Red Hat OpenShift](howto-setup-environment.md), si è pronti per creare il primo cluster.

Provare l'esercitazione:
> [!div class="nextstepaction"]
> [Creare un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md)
