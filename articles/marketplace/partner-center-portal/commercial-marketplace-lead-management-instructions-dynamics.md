---
title: Gestione dei lead per Dynamics 365 per il coinvolgimento dei clienti Azure Marketplace
description: Configurare la gestione dei lead per Dynamics 365 per Customer Engagement.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 5b3e35b6d19905e3c5262dfea3e52511510c9ffe
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81252783"
---
# <a name="configure-lead-management-for-dynamics-365-for-customer-engagement"></a>Configurare la gestione dei lead per Dynamics 365 per Customer Engagement

Questo articolo descrive come configurare Dynamics 365 per Customer Engagement (in precedenza Dynamics CRM Online), leggi ulteriori informazioni sulla modifica [qui](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) per elaborare i lead di vendita dalla tua offerta del marketplace. 

>[!Note]
>Queste istruzioni sono specifiche per l'ambiente Microsoft ospitato Dynamics 365 for Customer Engagement. La connessione diretta a un ambiente Dynamics in uso non è attualmente supportata, sono disponibili altre opzioni per ricevere lead, ad esempio la configurazione di un [endpoint https](./commercial-marketplace-lead-management-instructions-https.md) o di una tabella di [Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) per la ricezione dei lead.

## <a name="prerequisites"></a>Prerequisiti

Per completare la procedura descritta in questo articolo sono necessarie le seguenti autorizzazioni utente:

* Devi essere un amministratore dell'istanza di Dynamics 365 per Customer Engagement per poter installare una soluzione e seguire queste istruzioni.
* È necessario essere un amministratore tenant per creare un nuovo account di servizio per il servizio di lead usato per inviare lead dalle offerte del marketplace.
* È necessario avere accesso al portale di amministrazione di Office 365.You need to have access to the Office 365 admin portal.
* È necessario avere accesso al portale di Azure.You need to have access to the Azure portal.

## <a name="install-the-solution"></a>Installare la soluzione

1.  Scaricare la [soluzione Microsoft Marketplace Lead Writer](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) e salvarla in locale nel computer.

2.  Apri Dynamics 365 for Customer Engagement passando all'URL dell'istanza Dynamics (ad `https://tenant.crm.dynamics.com`esempio).

3.  Impostazioni di accesso selezionando l'icona a forma di ingranaggio e **Impostazioni avanzate** nella barra di spostamento superiore.
 
    ![Dynamics - Impostazioni avanzate](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

4.  Una volta nella pagina Impostazioni, accedere al menu Impostazioni dalla barra di spostamento superiore e selezionare **Soluzioni**.

    >[!Note]
    >Se non vedi le opzioni nella schermata successiva, non hai le autorizzazioni necessarie per procedere. Contattare un amministratore nell'istanza di Dynamics 365 for Customer Engagement.

    ![Dynamics 365 - Soluzioni](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

5. Nella pagina Soluzioni selezionare **Importa** e passare alla posizione in cui è stata salvata la soluzione *Microsoft Marketplace Lead Writer* scaricata nel passaggio 1.

    ![Dynamics 365 per coinvolgimento del cliente - Importazione](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

6. Completare l'importazione della soluzione seguendo l'Importazione guidata soluzione.

## <a name="configure-user-permissions"></a>Configurare le autorizzazioni utente

Per scrivere lead nell'istanza di Dynamics 365 for Customer Engagement, devi condividere con noi un account di servizio e configurare le autorizzazioni per l'account.

Usare la procedura seguente per creare l'account del servizio e assegnare le autorizzazioni. Usare **Azure Active Directory** oppure **Office 365**.

>[!Note]
>In base all'opzione di autenticazione selezionata, è possibile passare alle istruzioni corrispondenti in base alla scelta effettuata. Vedere [Azure Active Directory](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory) o Office [365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

Si consiglia questa opzione perché si ottiene il vantaggio aggiuntivo di non dover mai aggiornare il nome utente / password per continuare a ottenere i lead. Per usare l'opzione Azure Active Directory, specificare l'ID app, la chiave dell'applicazione e l'ID directory dell'applicazione Active Directory.

Utilizzare la procedura seguente per configurare Azure Active Directory per Dynamics 365 per il coinvolgimento dei clienti.

1. Accedere al portale di [Azure](https://portal.azure.com/)e quindi selezionare il servizio Azure Active Directory nel riquadro di spostamento sinistro.

2. Selezionare **Proprietà** nel riquadro di spostamento sinistro di Azure Active Directory e copiare il valore **ID directory** nella pagina. Salvare questo valore, poiché è il valore *ID directory* che è necessario fornire nel portale di pubblicazione per ricevere i lead per l'offerta del marketplace.

    ![Azure Active Directory - Proprietà](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

3. Selezionare **Registrazioni app** nel riquadro di spostamento sinistro di Azure Active Directory e quindi selezionare **Nuova registrazione** nella pagina.
4. Immettere un nome per il nome dell'applicazione. Specificare un nome di applicazione significativo.
5. In Tipi di account supportati selezionare **Account in qualsiasi directory dell'organizzazione.**
6. In URI di **Web** reindirizzamento selezionare Web `https://contosoapp1/auth`e specificare un URI, ad esempio . 
7. Selezionare **Registra**.

    ![Registrare un'applicazione](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

8. Ora che l'applicazione è registrata, accedere alla pagina di panoramica dell'applicazione e copiare il valore **ID applicazione (client)** in tale pagina. Salvare questo valore, poiché è il valore *ID applicazione (client)* che è necessario fornire nel portale di pubblicazione e in Dynamics per ricevere lead per l'offerta del marketplace.

    ![ID applicazione (client)](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

9. Seleziona **Certificati e segreti** dal riquadro di spostamento sinistro dell'app e seleziona Nuovo **segreto client** in tale pagina. Immettere una descrizione significativa per il segreto client e selezionare l'opzione **Mai** in Scadenza. Selezionare **Aggiungi** per creare il segreto client.

    ![Applicazione - Certificazione e segreti](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

10. Non appena il segreto client viene creato correttamente, **copiare il valore del segreto client**. Non sarà possibile recuperare il valore dopo l'allontanarsi dalla pagina. Salvare questo valore, poiché è il valore *Segreto client* che è necessario fornire nel portale di pubblicazione per ricevere i lead per l'offerta del marketplace. 
11. Selezionare **Autorizzazioni API** dal riquadro di spostamento sinistro delle app e quindi selezionare **Aggiungi un'autorizzazione**.
12. Selezionare API Microsoft e quindi **Dynamics CRM** come API.
13. In *Tipo di autorizzazioni richiesta dall'applicazione*verificare che sia **selezionata l'opzione Autorizzazioni delegate.** Controllare l'autorizzazione per **user_impersonation** *Access Common Data Service come utenti dell'organizzazione*. Selezionare **Aggiungi autorizzazioni**.

    ![Aggiungere autorizzazioni](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

14. Dopo aver completato i passaggi da 1 a 13 nel portale di Azure, passare all'istanza `https://tenant.crm.dynamics.com`di Dynamics 365 for Customer Engagement passando all'URL (ad esempio ).
15. Impostazioni di accesso selezionando l'icona a forma di ingranaggio e **Impostazioni avanzate** nella barra di spostamento superiore.
16. Una volta nella pagina Impostazioni, accedere al menu Impostazioni dalla barra di spostamento superiore e selezionare **Sicurezza**.
17. Nella pagina Sicurezza selezionare **Utenti**.  Nella pagina Utenti selezionare l'elenco a discesa "Utenti abilitati" per passare a **Utenti applicazione**.
18. Selezionare **Nuovo** per creare un nuovo utente. 

    ![Creare un nuovo utente](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

19. In **Nuovo utente**, assicurarsi che l'utente UTENTE: UTENTE APPLICATIsia sia selezionato. Specificare un nome utente, un nome completo e un indirizzo di posta elettronica per l'utente che si desidera utilizzare con questa connessione. Inoltre, incollare **l'ID applicazione** per l'app creata nel portale di Azure dal passaggio 8.Also, paste in the Application ID for the app you created in the Azure portal from step 8. Selezionare **Salva e chiudi** per completare l'aggiunta dell'utente.

    ![Nuovo utente](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

20. Passare alla sezione"Impostazioni di sicurezza" di questo articolo per completare la configurazione della connessione per l'utente corrente.

### <a name="office-365"></a>Office 365

Se non si vuole usare Azure Active Directory, è possibile registrare un nuovo utente nell'interfaccia di amministrazione di *Microsoft 365.* Per continuare a ottenere lead, è necessario aggiornare il nome utente o la password ogni 90 giorni.

Utilizzare la procedura seguente per configurare Office 365 per Dynamics 365 per Customer Engagement.

1. Accedere all'[interfaccia di amministrazione di Microsoft 365](https://admin.microsoft.com).

2. Selezionare **Aggiungi utente**.

    ![Interfaccia di amministrazione di Microsoft 365 - aggiungere un utente](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

4. Creare un nuovo utente per il servizio Lead Writer. Configurare le seguenti impostazioni:

    * Fornire un nome utente
    * Fornisci una password e deseleziona l'opzione "Fai cambiare la password all'accesso al primo accesso".
    * Selezionare il ruolo "Utente - nessun accesso amministratore" come ruolo per l'utente.
    * Seleziona "Piano di coinvolgimento dei clienti Dynamics 365" come licenza del prodotto mostrata nella schermata successiva. L'addebito avverrà in base alla licenza scelta. 

Salvare questi valori in quanto sono i valori *di nome utente e password* che è necessario fornire nel portale di pubblicazione per ricevere i lead per l'offerta del marketplace.

![Interfaccia di amministrazione di Microsoft 365 - nuovo utente](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Impostazioni di sicurezza

Il passaggio finale consente all'utente creato di scrivere i lead.

1. Apri Dynamics 365 for Customer Engagement passando all'URL dell'istanza Dynamics (ad `https://tenant.crm.dynamics.com`esempio).
2. Impostazioni di accesso selezionando l'icona a forma di ingranaggio e **Impostazioni avanzate** nella barra di spostamento superiore.
3. Una volta nella pagina Impostazioni, accedere al menu Impostazioni dalla barra di spostamento superiore e selezionare **Sicurezza**.
4. Nella pagina Sicurezza selezionare **Utenti** e selezionare l'utente creato nella sezione Configura autorizzazioni utente del documento, quindi selezionare **Gestisci ruoli**. 

    ![Gestire i ruoli](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

5. Cercare il nome del ruolo "Microsoft Marketplace Lead Writer" e selezionarlo per assegnare all'utente il ruolo.

    ![Gestire ruoli utente](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!Note]
    >Questo ruolo viene creato dalla soluzione importata. Dispone solo di autorizzazioni per la scrittura dei lead e dell'individuazione della versione della soluzione per garantirne la compatibilità.

6. Tornare alla pagina Sicurezza e selezionare **Ruoli di sicurezza**. Cercare il ruolo "Microsoft Marketplace Lead Writer" e selezionarlo.

    ![ruoli di sicurezza](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

7. Una volta nel ruolo di sicurezza, selezionare la scheda **Record principali.** Cercare l'entità "Impostazioni dell'interfaccia utente dell'entità utente" e abilitare le autorizzazioni di creazione, lettura e scrittura per l'utente (1/4 cerchio giallo) per l'entità facendo clic una volta in ognuno dei cerchi corrispondenti.

    ![Microsoft Marketplace Lead Writer - Record principali](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

8. Passare ora alla scheda **Personalizzazione.** Cerca l'entità "Processo di sistema" e abilita le autorizzazioni di lettura, scrittura e aggiunta all'organizzazione (verde solido) per tale entità facendo clic quattro volte in ognuno dei cerchi corrispondenti.

    ![Microsoft Marketplace Lead Writer - personalizzazione](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

9. **Salvare e chiudere**.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-for-customer-engagement"></a>Configurare l'offerta per l'invio di lead a Dynamics 365 per Customer Engagement

Quando si è pronti a configurare le informazioni di gestione dei lead per l'offerta nel portale di pubblicazione, attenersi alla seguente procedura:

1. Accedi alla pagina **Configurazione offerta** per la tua offerta.
2. Selezionare **Connetti** nella sezione Gestione lead.

    ![Connettersi alla gestione dei lead](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

3. Nella finestra popup Dettagli connessione selezionare **Dynamics 365 for Customer Engagement** per la destinazione del lead

    ![Dettagli connessione - Destinazione lead](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

4. Specificare **l'URL dell'istanza di Dynamics 365,** ad `https://contoso.crm4.dynamics.com`esempio .

5. Selezionare il metodo di **autenticazione**, Azure Active Directory o Office 365. 
6. Se è stato selezionato Azure Active Directory, specificare `23456052-aaaa-bbbb-8662-1234df56788f` **l'ID applicazione (client)** (esempio: ), l'ID **directory** (esempio: `12345678-8af1-4asf-1234-12234d01db47`) e **il segreto client** (esempio: `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`).

    ![Dettagli della connessione - Azure Active DirectoryConnection details - Azure Active Directory](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

7. Se è stato selezionato Office 365, `contoso@contoso.onmicrosoft.com`specificare il nome `P@ssw0rd` **utente** (ad esempio: ) e Password (esempio: ).

    ![Dettagli connessione - Nome utente](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

8. **Email di contatto:** fornisci email alle persone della tua azienda che devono ricevere notifiche e-mail quando viene ricevuto un nuovo lead. È possibile fornire più messaggi di posta elettronica separandoli con un punto e virgola.
9. Selezionare **OK**.

Per assicurarsi di aver eseguito correttamente la connessione a una destinazione lead, fare clic sul pulsante di convalida. In caso di esito positivo, si avrà un lead di test nella destinazione del lead.

![Gestione lead - Account di archiviazione dei dettagli di connessione](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-connection-details.png)

>[!Note]
>È necessario completare la configurazione del resto dell'offerta e pubblicarla prima di poter ricevere i lead per l'offerta.
