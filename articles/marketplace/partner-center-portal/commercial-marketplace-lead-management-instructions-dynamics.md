---
title: Gestione dei lead per Dynamics 365 Customer Engagement-Marketplace commerciale Microsoft
description: Informazioni su come configurare il coinvolgimento dei clienti di Dynamics 365 per gestire lead da Microsoft AppSource e Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 985b3258eb0b957242d529945f32ed9704a91e7d
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791001"
---
# <a name="configure-lead-management-for-dynamics-365-customer-engagement"></a>Configurare la gestione dei lead per Dynamics 365 Customer Engagement

Questo articolo descrive come configurare Dynamics 365 Customer Engagement (denominato in precedenza Dynamics CRM online). Scopri di più sulla modifica dell' [autenticazione basata su server con Customer Engagement e SharePoint Online](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) per elaborare i lead di vendita dall'offerta del Marketplace commerciale.

>[!NOTE]
>Queste istruzioni sono specifiche per l'ambiente cloud ospitato da Microsoft per il coinvolgimento dei clienti in Dynamics 365. La connessione diretta a un ambiente Dynamics locale non è attualmente supportata. Sono disponibili altre opzioni per la ricezione di lead, ad esempio la configurazione di un [endpoint HTTPS](./commercial-marketplace-lead-management-instructions-https.md) o di una [tabella di Azure](./commercial-marketplace-lead-management-instructions-azure-table.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi descritti in questo articolo, sono necessarie le autorizzazioni utente seguenti:

* Diritti di amministratore per l'istanza di Dynamics 365 customer engagement per poter installare una soluzione.
* Diritti di amministratore del tenant per creare un nuovo account di servizio per il servizio lead usato per inviare lead dalle offerte del Marketplace commerciale.
* Accesso al portale di amministrazione di Office 365.
* Accesso al portale di Azure.

## <a name="install-the-solution"></a>Installare la soluzione

1. Scaricare la [soluzione Microsoft Marketplace lead writer](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip)e salvarla localmente nel computer.

1. Aprire Dynamics 365 Customer Engagement passando all'URL per l'istanza di Dynamics, ad esempio `https://tenant.crm.dynamics.com`.

1. Selezionare l'icona a forma di ingranaggio nella barra superiore, quindi selezionare **Impostazioni avanzate**.
 
    ![Voce di menu impostazioni avanzate di Dynamics 365](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

1. Nella pagina **Impostazioni** aprire il menu **Impostazioni** nella barra superiore e selezionare **soluzioni**.

    >[!NOTE]
    >Se le opzioni non vengono visualizzate nella schermata seguente, non si dispone delle autorizzazioni necessarie per continuare. Contattare un amministratore per l'istanza di Dynamics 365 Customer Engagement.

    ![Opzioni di Dynamics 365 Solutions](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

1. Nella pagina **soluzioni** selezionare **Importa** e passare al percorso in cui è stata salvata la soluzione **Microsoft Marketplace lead writer** scaricata nel passaggio 1.

    ![Pulsante Importa](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

1. Completare l'importazione della soluzione seguendo la procedura guidata Importa soluzione.

## <a name="configure-user-permissions"></a>Configurare le autorizzazioni utente

Per scrivere i lead nell'istanza di Dynamics 365 Customer Engagement, è necessario condividere un account del servizio con Microsoft e configurare le autorizzazioni per l'account.

Usare la procedura seguente per creare l'account del servizio e assegnare le autorizzazioni. Usare Azure Active Directory oppure Office 365.

>[!NOTE]
>Passare alle istruzioni corrispondenti in base all'opzione di autenticazione selezionata. Vedere [Azure Active Directory](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory) o [Office 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

Questa opzione è consigliata perché non è mai necessario aggiornare il nome utente o la password per ottenere i lead. Per usare l'opzione Azure Active Directory, specificare l'ID app, la chiave dell'applicazione e l'ID directory dall'applicazione Active Directory.

Per configurare Azure Active Directory per il coinvolgimento dei clienti di Dynamics 365:

1. Accedere al [portale di Azure](https://portal.azure.com/). Nel riquadro sinistro selezionare **Azure Active Directory**.

1. Selezionare **Proprietà**e copiare il valore di **ID directory** nella pagina delle **proprietà della directory** . Salvare questo valore perché sarà necessario specificarlo nel portale di pubblicazione per ricevere i lead per l'offerta del Marketplace.

    ![Voce di menu Proprietà Azure Active Directory](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

1. Selezionare **registrazioni app** dal riquadro sinistro Azure Active Directory, quindi selezionare **nuova registrazione** nella pagina.
1. Immettere un nome significativo per il nome dell'applicazione.
1. In **tipi di account supportati**selezionare **account in qualsiasi directory dell'organizzazione**.
1. In **URI di reindirizzamento (facoltativo)** selezionare **Web** e immettere un URI, ad esempio `https://contosoapp1/auth`. 
1. Selezionare **Registra**.

    ![Pagina di registrazione di un'applicazione](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

1. Ora che l'applicazione è stata registrata, accedere alla pagina Panoramica dell'applicazione. Copiare il valore dell' **ID dell'applicazione (client)** in quella pagina. Salvare questo valore perché sarà necessario specificarlo nel portale di pubblicazione e in Dynamics 365 per ricevere i lead per l'offerta del Marketplace.

    ![Casella ID applicazione (client)](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

1. Selezionare **certificati & segreti** dal riquadro sinistro dell'app e selezionare il pulsante **nuovo segreto client** . Immettere una descrizione significativa per il segreto client e selezionare l'opzione **Never** in **Expires**. Selezionare **Aggiungi** per creare il segreto client.

    ![Voce di menu certificati & segreti](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

1. Non appena il segreto client viene creato correttamente, copiare il valore del **segreto client** . Non sarà possibile recuperare il valore dopo aver lasciato la pagina. Salvare questo valore perché sarà necessario specificarlo nel portale di pubblicazione per ricevere i lead per l'offerta del Marketplace. 
1. Selezionare **autorizzazioni API** dal riquadro sinistro dell'app e quindi selezionare **+ Aggiungi un'autorizzazione**.
1. Selezionare **API Microsoft**, quindi selezionare **Dynamics CRM** come API.
1. In **quali tipi di autorizzazioni sono necessarie per l'applicazione?** assicurarsi che siano selezionate le **autorizzazioni delegate** . 
1. In **autorizzazione**, selezionare la casella di controllo **user_impersonation** per **Access Common Data Service come utenti dell'organizzazione**. Selezionare quindi **Aggiungi autorizzazioni**.

    ![Pulsante Aggiungi autorizzazioni](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

1. Dopo aver completato i passaggi da 1 a 14 nel portale di Azure, passare all'istanza di Dynamics 365 Customer Engagement passando all'URL, ad esempio `https://tenant.crm.dynamics.com`.
1. Selezionare l'icona a forma di ingranaggio nella barra superiore, quindi selezionare **Impostazioni avanzate**.
1. Nella pagina **Impostazioni** aprire il menu **Impostazioni** nella barra superiore e selezionare **sicurezza**.
1. Nella pagina **sicurezza** selezionare **utenti**. Nella pagina **utenti** selezionare l'elenco a discesa **utenti abilitati** e quindi selezionare **utenti applicazione**.
1. Selezionare **Nuovo** per creare un nuovo utente. 

    ![Creare un nuovo utente](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

1. Nel riquadro **nuovo utente** verificare che **utente: applicazione** utente sia selezionato. Specificare un nome utente, un nome completo e un indirizzo di posta elettronica per l'utente che si desidera utilizzare con la connessione. Incollare anche l' **ID applicazione** per l'app creata nel portale di Azure del passaggio 8. Selezionare **Save & Close** per completare l'aggiunta dell'utente.

    ![Riquadro nuovo utente](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

1. Per completare la configurazione della connessione per questo utente, vedere la sezione "impostazioni di sicurezza" di questo articolo.

### <a name="office-365"></a>Office 365

Se non si vuole usare Azure Active Directory, è possibile registrare un nuovo utente nell'interfaccia di amministrazione di Microsoft 365. Verrà richiesto di aggiornare il nome utente e la password ogni 90 giorni per continuare a ottenere i lead.

Per configurare Office 365 per Dynamics 365 Customer Engagement:

1. Accedere all'[interfaccia di amministrazione di Microsoft 365](https://admin.microsoft.com).

1. Selezionare **Aggiungi utente**.

    ![Interfaccia di amministrazione di Microsoft 365 aggiungere un'opzione utente](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

1. Creare un nuovo utente per il servizio Lead Writer. Configurare le seguenti impostazioni:

    * Immettere un nome utente.
    * Immettere una password e deselezionare l'opzione **imposta la password di modifica della password al momento dell'accesso** .
    * Selezionare **utente (nessun accesso amministratore)** come ruolo per l'utente.
    * Selezionare **Dynamics 365 Customer Engagement Plan** come licenza del prodotto, come illustrato nella schermata seguente. L'addebito avverrà in base alla licenza scelta. 

Salvare questi valori perché è necessario fornire i valori di **nome utente** e **password** nel portale di pubblicazione per ricevere i lead per l'offerta del Marketplace.

![Riquadro nuovo utente Microsoft 365 interfaccia di amministrazione](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Impostazioni di sicurezza

Il passaggio finale consiste nell'abilitare l'utente creato per scrivere i lead.

1. Aprire Dynamics 365 Customer Engagement passando all'URL per l'istanza di Dynamics, ad esempio `https://tenant.crm.dynamics.com`.
1. Selezionare l'icona a forma di ingranaggio nella barra superiore, quindi selezionare **Impostazioni avanzate**.
1. Nella pagina **Impostazioni** aprire il menu **Impostazioni** nella barra superiore e selezionare **sicurezza**.
1. Nella pagina **sicurezza** selezionare **utenti** e selezionare l'utente creato nella sezione "configurare le autorizzazioni utente" di questo documento. Quindi selezionare **Gestisci ruoli**. 

    ![Scheda Gestisci ruoli](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

1. Cercare il nome del ruolo **Microsoft Marketplace writer principale**e selezionarlo per assegnare l'utente al ruolo.

    ![Riquadro Gestisci ruoli utente](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!NOTE]
    >Questo ruolo viene creato dalla soluzione importata. Dispone solo di autorizzazioni per la scrittura dei lead e dell'individuazione della versione della soluzione per garantirne la compatibilità.

1. Tornare alla pagina **sicurezza** e selezionare **ruoli di sicurezza**. Cercare il ruolo **Microsoft Marketplace writer lead**e selezionarlo.

    ![Riquadro ruoli di sicurezza](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

1. Nel ruolo di sicurezza, selezionare la scheda **record principali** . cercare l'elemento **impostazioni dell'interfaccia utente dell'entità utente** . Abilitare le autorizzazioni di creazione, lettura e scrittura per l'utente (1/4 cerchio giallo) per l'entità facendo clic una volta in ognuno dei cerchi corrispondenti.

    ![Microsoft Marketplace scheda record principali del writer principale](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

1. Nella scheda **personalizzazione** cercare l'elemento del **processo di sistema** . Abilitare le autorizzazioni di lettura, scrittura e AppendTo per l'organizzazione (circoli verdi pieni) per tale entità facendo clic quattro volte in ciascuno dei cerchi corrispondenti.

    ![Scheda personalizzazione del writer del Lead Microsoft Marketplace](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

1. Selezionare **Salva e chiudi**.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement"></a>Configurare l'offerta per inviare lead a Dynamics 365 Customer Engagement 

Per configurare le informazioni di gestione dei lead per l'offerta nel portale di pubblicazione:

1. Passare alla pagina di **installazione dell'offerta** per l'offerta.
1. Selezionare **Connetti** nella sezione **gestione dei lead** .

    ![Pulsante Connetti sezione Gestione lead](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

1. Nella finestra popup Dettagli connessione selezionare **Dynamics 365 Customer Engagement** per la destinazione lead.

    ![Casella destinazione lead](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

1. Immettere l' **URL** per l'istanza di Dynamics 365, ad `https://contoso.crm4.dynamics.com`esempio.

1. Selezionare il metodo di **autenticazione**, ovvero Azure Active Directory o Office 365. 
1. Se è stata selezionata l'opzione **Azure Active Directory**, immettere l' **ID dell'applicazione (client)** , ad `23456052-aaaa-bbbb-8662-1234df56788f`esempio, l' **ID directory** ( `12345678-8af1-4asf-1234-12234d01db47`ad esempio,) e il **segreto client** ( `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`ad esempio,).

    ![Autenticazione con Azure Active Directory selezionata](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

1. Se è stata selezionata l'opzione **Office 365**, immettere il **nome utente** ( `contoso@contoso.onmicrosoft.com`ad esempio,) e la **password** (ad esempio, `P@ssw0rd`).

    ![Casella nome utente di Office 365](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

1. Per il **messaggio di posta**elettronica di contatto, immettere gli indirizzi di posta elettronica per gli utenti della società che devono ricevere notifiche tramite posta elettronica quando viene ricevuto un nuovo lead. È possibile immettere più indirizzi di posta elettronica separandoli con punti e virgola.
1. Selezionare **OK**.

Per assicurarsi di avere effettuato la connessione a una destinazione principale, selezionare il pulsante **convalida** . In caso di esito positivo, sarà presente un lead di test nella destinazione principale.

![Casella di posta elettronica di contatto](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-connection-details.png)

>[!NOTE]
>È necessario completare la configurazione del resto dell'offerta e pubblicarla prima di poter ricevere i lead per l'offerta.
