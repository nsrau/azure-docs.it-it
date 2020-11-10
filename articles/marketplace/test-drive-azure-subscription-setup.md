---
title: Configurare una sottoscrizione di Azure Marketplace per le unità di test ospitate
description: Spiega come configurare una sottoscrizione di Azure Marketplace per Dynamics 365 per Customer Engagement e Dynamics 365 for Operations test drives
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 11/09/2020
ms.openlocfilehash: 8e77c21c6d776aef20fa3ca71ad12fdb424b07ab
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94415021"
---
# <a name="set-up-an-azure-marketplace-subscription-for-hosted-test-drives"></a>Configurare una sottoscrizione di Azure Marketplace per le unità di test ospitate

Questo articolo illustra come configurare una sottoscrizione di Azure Marketplace e **dynamics 365 per Customer Engagement** o **Dynamics 365 for Operation** Environment per test drive.

## <a name="set-up-for-dynamics-365-for-customer-engagement"></a>Configurare per Dynamics 365 per Customer Engagement

1. Accedere al [portale di Azure](https://portal.azure.com/) con un account amministratore.
2. Verificare di trovarsi nel tenant associato all'istanza di Dynamics 365 test drive posizionando il puntatore del mouse sull'icona dell'account nell'angolo superiore destro. Se non si è nel tenant corretto, selezionare l'icona dell'account per passare al tenant corretto.

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="Selezione del tenant corretto.":::

3. Verificare che sia disponibile la licenza **Dynamics 365 Customer Engagement Plan** .

    [![Verifica della licenza del piano.](media/test-drive/check-plan-license.png)](media/test-drive/check-plan-license.png#lightbox)

4. Creare un'app Azure Active Directory (AD) in Azure. AppSource userà questa app per eseguire il provisioning e il deprovisioning dell'utente test drive nel tenant.
    1. Dal riquadro filtro selezionare **Azure Active Directory**.
    2. Selezionare **Registrazioni per l'app**.

        [![Selezione delle registrazioni per l'app.](media/test-drive/app-registrations.png)](media/test-drive/app-registrations.png#lightbox)

    3. Selezionare **Nuova registrazione**.
    4. Specificare un nome di applicazione appropriato.

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="Registrazione di un'applicazione.":::

    5. In tipi di account supportati selezionare **account in qualsiasi directory dell'organizzazione e account Microsoft personali**.
    6. Selezionare **Crea** e attendere che venga creata l'app.
    7. Una volta creata l'app, annotare l' **ID applicazione** visualizzato nella schermata panoramica. Questo valore sarà necessario in un secondo momento durante la configurazione del test drive.
    8. Per aggiungere un URI di reindirizzamento nativeClient, selezionare il pannello **autenticazione** . In **configurazione piattaforma** selezionare **Aggiungi piattaforma**  >  applicazione desktop per **dispositivi mobili**  >  **Desktop** . Scegliere l'URI di Reindirizzamento **nativeClient** e selezionare **Configura**.

        :::image type="content" source="./media/test-drive/configure-desktop-devices.png" alt-text="Aggiunta di un URI di reindirizzamento nativeClient.":::

    9. In **Gestisci applicazione** selezionare **autorizzazioni** per le API.
    10. Selezionare **Aggiungi un'autorizzazione** e quindi **Microsoft Graph API**.
    11. Selezionare la categoria autorizzazione **applicazione** , quindi le autorizzazioni **Directory. Read. All** e **Directory. ReadWrite. All** .

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="Impostazione delle autorizzazioni dell'applicazione.":::

    12. Per aggiungere **Dynamics CRM-** accesso per la rappresentazione utente per consenti elenco Azure ad app, selezionare di nuovo **Aggiungi autorizzazione** .

        :::image type="content" source="./media/test-drive/request-api-permissions.png" alt-text="Richiesta delle autorizzazioni dell'applicazione.":::

    13. Una volta aggiunta l'autorizzazione, selezionare **concedi il consenso dell'amministratore per Microsoft**.
    14. Nell'avviso del messaggio selezionare **Sì**.

        [![Mostra le autorizzazioni dell'applicazione concesse correttamente.](media/test-drive/api-permissions-confirmation-customer.png)](media/test-drive/api-permissions-confirmation-customer.png#lightbox)

    15. Per generare un segreto per il App Azure AD:
        1. In **Gestisci applicazione** selezionare **certificato e segreti**.
        2. In segreti client selezionare **nuovo segreto client**.
        3. Immettere una descrizione, ad esempio *test drive* , e selezionare una durata appropriata. Il test drive si interrompe dopo la scadenza della chiave. a questo punto sarà necessario generare e fornire a AppSource una nuova chiave.
        4. Selezionare **Aggiungi** per generare il segreto dell'app di Azure. Copiare questo valore poiché verrà nascosto non appena si lave questo pannello. Questo valore sarà necessario in un secondo momento durante la configurazione del test drive.

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="Aggiunta di un segreto client.":::

5. In alcuni casi è necessario più tempo del previsto per sincronizzare un utente da Azure AD a un'istanza di CRM. Per facilitare questa operazione, è stato aggiunto un processo per forzare l'utente della sincronizzazione, ma è necessario che l'applicazione Azure AD sia inserita nell'elenco elementi consentiti dal centro per i partner. Per eseguire questa operazione, vedere [sincronizzazione utente per l'istanza di engagement del cliente](https://github.com/microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/CDS_Utility_to_ForceUserSync_in_CRM_Instance.md).
6. Aggiungere il ruolo entità servizio all'applicazione per consentire all'app Azure AD di rimuovere gli utenti dal tenant di Azure.
    1. Aprire un prompt dei comandi di PowerShell di livello amministrativo.
    2. Install-Module MSOnline (eseguire questo comando se MSOnline non è installato).
    3. Connect-MsolService (verrà visualizzata una finestra popup; accedere con il tenant dell'organizzazione appena creato).
    4. $applicationId = **<YOUR_APPLICATION_ID>**.
    5. $sp = $applicationId Get-MsolServicePrincipal-AppPrincipalId.
    6. Add-MsolRoleMember-RoleObjectId fe930be7-5e62-47dB-91AF-98c3a49a38b1-RoleMemberObjectId $sp. ObjectId-Rolemembertype User servicePrincipal.

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="Accesso al proprio account.":::

7. Aggiungere l'app di Azure creata in precedenza come utente dell'applicazione all'istanza di test drive CRM.
    1. Aggiungere un nuovo utente in **Azure Active Directory**. Per creare l'utente sono necessari solo i valori **Name** e **username** (appartenenti allo stesso tenant), lasciando gli altri campi come predefiniti. Copiare il valore del nome utente.
    2. Accedere all' **istanza di CRM** e selezionare **impostazione**  >  **Security**  >  **utenti** sicurezza.
    3. Modificare la visualizzazione in **utenti applicazione**.

        :::image type="content" source="./media/test-drive/application-users.png" alt-text="Impostazione delle informazioni sull'account per un utente.":::

    4. Aggiungere un nuovo utente (assicurarsi che il modulo sia per l'utente dell'applicazione).
    5. Immettere lo stesso nome utente nei campi **nome utente** e **indirizzo di posta elettronica primario** . Aggiungere la **ApplicationID di Azure** nell' **ID applicazione**.
    6. Assegnare un **nome completo**.
    7. Selezionare **Salva**.
    8. Selezionare **Gestisci ruoli**.
    9. Assegnare un ruolo di sicurezza personalizzato o OOB che contenga i privilegi di lettura, scrittura e assegnazione del ruolo, ad esempio *amministratore di sistema*.

        :::image type="content" source="./media/test-drive/security-roles-selection.png" alt-text="Selezione dei privilegi del ruolo.":::

    10. Assegnare all'utente dell'applicazione il ruolo di sicurezza personalizzato creato per il test drive.

## <a name="set-up-for-dynamics-365-for-operations"></a>Configurazione per Dynamics 365 per le operazioni

1. Accedere al [portale di Azure](https://portal.azure.com/) con un account amministratore.
2. Verificare di trovarsi nel tenant associato all'istanza di Dynamics 365 test drive posizionando il puntatore del mouse sull'icona dell'account nell'angolo superiore destro. Se non si è nel tenant corretto, selezionare l'icona dell'account per passare al tenant corretto.

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="Selezione del tenant corretto.":::

3. Creare un App Azure AD in Azure. AppSource userà questa app per eseguire il provisioning e il deprovisioning dell'utente test drive nel tenant.
    1. Dal riquadro filtro selezionare **Azure Active Directory**.
    2. Selezionare **Registrazioni per l'app**.

        :::image type="content" source="./media/test-drive/app-registrations.png" alt-text="Selezione di una registrazione per l'app.":::

    3. Selezionare **Nuova registrazione**.
    4. Specificare un nome di applicazione appropriato.

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="Registrazione di un'applicazione.":::

    5. In tipi di account supportati selezionare **account in qualsiasi directory dell'organizzazione e account Microsoft personali**.
    6. Selezionare **Crea** e attendere che venga creata l'app.
    7. Una volta creata l'app, annotare l' **ID applicazione** visualizzato nella schermata panoramica. Questo valore sarà necessario in un secondo momento durante la configurazione del test drive.
    8. In **Gestisci applicazione** selezionare **autorizzazioni** per le API.
    9. Selezionare **Aggiungi un'autorizzazione** e quindi **Microsoft Graph API**.
    10. Selezionare la categoria autorizzazione **applicazione** , quindi le autorizzazioni **Directory. Read. All** e **Directory. ReadWrite. All** .

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="Impostazione delle autorizzazioni dell'applicazione.":::

    11. Selezionare **Aggiungi autorizzazione**.
    12. Una volta aggiunta l'autorizzazione, selezionare **concedi il consenso dell'amministratore per Microsoft**.
    13. Nell'avviso del messaggio selezionare **Sì**.

        [![Mostra le autorizzazioni dell'applicazione concesse correttamente.](media/test-drive/api-permissions-confirmation-operations.png)](media/test-drive/api-permissions-confirmation-operations.png#lightbox)

    14. Per generare un segreto per il App Azure AD:
        1. In **Gestisci applicazione** selezionare **certificato e segreti**.
        2. In segreti client selezionare **nuovo segreto client**.
        3. Immettere una descrizione, ad esempio *test drive* , e selezionare una durata appropriata. Il test drive si interrompe dopo la scadenza della chiave. a questo punto sarà necessario generare e fornire a AppSource una nuova chiave.
        4. Selezionare **Aggiungi** per generare il segreto dell'app di Azure. Copiare questo valore poiché verrà nascosto non appena si lave questo pannello. Questo valore sarà necessario in un secondo momento durante la configurazione del test drive.

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="Aggiunta di un segreto client.":::

4. Aggiungere il ruolo entità servizio all'applicazione per consentire all'app Azure AD di rimuovere gli utenti dal tenant di Azure.
    1. Aprire un prompt dei comandi di PowerShell di livello amministrativo.
    2. Install-Module MSOnline (eseguire questo comando se MSOnline non è installato).
    3. Connect-MsolService (verrà visualizzata una finestra popup; accedere con il tenant dell'organizzazione appena creato).
    4. $applicationId = **<YOUR_APPLICATION_ID>**.
    5. $sp = $applicationId Get-MsolServicePrincipal-AppPrincipalId.
    6. Add-MsolRoleMember-RoleObjectId fe930be7-5e62-47dB-91AF-98c3a49a38b1-RoleMemberObjectId $sp. ObjectId-Rolemembertype User servicePrincipal.

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="Accesso al proprio account.":::

5. Aggiungere ora l'app precedente a **Dynamics 365 per le operazioni** per consentire all'app di gestire gli utenti.
    1. Trovare l'istanza **di Dynamics 365 per le operazioni** .
    2. Nell'angolo superiore sinistro fare clic sul menu a tre righe (hamburger).
    3. Selezionare **Amministrazione sistema**.
    4. Selezionare **Azure Active Directory applicazioni**.
    5. Selezionare **+ Nuovo**.
    6. Immettere l' **ID client dell'app Azure ad** che eseguirà le azioni per conto dell'utente.

    > [!NOTE]
    > ID utente per conto del quale verranno eseguite le azioni, in genere l'amministratore di sistema dell'istanza o un utente con privilegi per l'aggiunta di altri utenti.

    [![ID utente per conto del quale verranno eseguite le azioni, in genere l'amministratore di sistema dell'istanza o un utente con privilegi per l'aggiunta di altri utenti.](media/test-drive/system-admin-user-id.png)](media/test-drive/system-admin-user-id.png#lightbox)
<!--
## Next steps

- [Commercial marketplace partner and customer usage attribution](azure-partner-customer-usage-attribution.md) -->
