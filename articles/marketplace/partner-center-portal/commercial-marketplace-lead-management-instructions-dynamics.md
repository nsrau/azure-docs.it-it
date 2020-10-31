---
title: Gestione dei lead per Dynamics 365 Customer Engagement - Marketplace commerciale Microsoft
description: Informazioni su come configurare Dynamics 365 Customer Engagement per gestire i lead da Microsoft AppSource e Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/30/2020
ms.openlocfilehash: 54754f4c7753661b247f3f90942fb3074c34a38b
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130613"
---
# <a name="configure-lead-management-for-dynamics-365-customer-engagement"></a>Configurare la gestione dei lead per Dynamics 365 Customer Engagement

Questo articolo descrive come configurare Dynamics 365 Customer Engagement (in precedenza denominato Dynamics CRM Online). Per altre informazioni sulla modifica, vedere [Configurare l'autenticazione basata su server con Customer Engagement e SharePoint Online](/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) per elaborare i lead dall'offerta del marketplace commerciale.

>[!NOTE]
>Queste istruzioni sono specifiche dell'ambiente cloud ospitato da Microsoft per Dynamics 365 Customer Engagement. La connessione diretta a un ambiente Dynamics locale non è attualmente supportata. Esistono altre opzioni per ricevere i lead, ad esempio configurare un [endpoint HTTPS](./commercial-marketplace-lead-management-instructions-https.md) o una [tabella di Azure](./commercial-marketplace-lead-management-instructions-azure-table.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare le procedure descritte in questo articolo, sono necessarie le autorizzazioni utente seguenti:

* Diritti di amministratore per l'istanza di Dynamics 365 Customer Engagement per poter installare una soluzione.
* Diritti di amministratore del tenant per creare un nuovo account per il servizio di lead usato per inviare i lead dalle offerte del marketplace commerciale.
* Accesso al portale di amministrazione.
* Accesso al portale di Azure.

## <a name="install-the-solution"></a>Installare la soluzione

1. Scaricare la [soluzione Microsoft Marketplace Lead Writer](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) e salvarla in locale nel computer.

1. Aprire Dynamics 365 Customer Engagement passando all'URL dell'istanza di Dynamics, ad esempio `https://tenant.crm.dynamics.com`.

1. Selezionare l'icona a forma di ingranaggio nella barra superiore e quindi selezionare **Impostazioni avanzate** .
 
    ![Voce di menu Impostazioni avanzate di Dynamics 365](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

1. Nella pagina **Impostazioni** aprire il menu **Impostazioni** sulla barra superiore e selezionare **Soluzioni** .

    >[!NOTE]
    >Se non vengono visualizzate le opzioni nella schermata successiva, significa che non si possiedono le autorizzazioni necessarie per continuare. Contattare un amministratore per l'istanza di Dynamics 365 Customer Engagement.

    ![Opzione Soluzioni di Dynamics 365](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

1. Nella pagina **Soluzioni** selezionare **Importa** e passare al percorso in cui si è salvata la soluzione **Microsoft Marketplace Lead Writer** scaricata nel passaggio 1.

    ![Pulsante Importa](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

1. Completare l'importazione della soluzione seguendo la procedura guidata Importa soluzione.

## <a name="configure-user-permissions"></a>Configurare le autorizzazioni utente

Per scrivere i lead nell'istanza di Dynamics 365 Customer Engagement, è necessario condividere un account del servizio con Microsoft e configurarne le autorizzazioni.

Usare la procedura seguente per creare l'account del servizio e assegnare le autorizzazioni. Usare Azure Active Directory oppure Office 365.

>[!NOTE]
>Passare alle istruzioni corrispondenti in base all'opzione di autenticazione selezionata. Vedere [Azure Active Directory](#azure-active-directory) oppure [Office 365](#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

Questa è l'opzione consigliata perché non è mai necessario aggiornare il nome utente o la password per continuare a ricevere i lead. Per usare l'opzione Azure Active Directory, fornire ID app, chiave applicazione e ID directory dall'applicazione Active Directory.

Per configurare Azure Active Directory per Dynamics 365 Customer Engagement:

1. Accedere al [portale di Azure](https://portal.azure.com/). Nel riquadro sinistro selezionare **Azure Active Directory** .

1. Selezionare **Proprietà** e copiare il valore di **ID directory** nella pagina **Proprietà directory** . Salvare questo valore perché sarà necessario specificarlo nel portale di pubblicazione per ricevere i lead per l'offerta del marketplace.

    ![Voce di menu Proprietà di Azure Active Directory](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

1. Selezionare **Registrazioni app** nel riquadro sinistro di Azure Active Directory e quindi selezionare **Nuova registrazione** nella pagina.
1. Immettere un nome significativo per l'applicazione.
1. In **Tipi di account supportati** selezionare **Account in qualsiasi directory dell'organizzazione** .
1. In **URI di reindirizzamento (facoltativo)** selezionare **Web** e immettere un URI, ad esempio `https://contosoapp1/auth`. 
1. Selezionare **Registra** .

    ![Pagina Registra un'applicazione](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

1. Ora che l'applicazione è stata registrata, accedere alla pagina di panoramica dell'applicazione. Copiare il valore di **ID applicazione (client)** nella pagina. Salvare questo valore perché sarà necessario specificarlo nel portale di pubblicazione e in Dynamics 365 per ricevere i lead per l'offerta del marketplace.

    ![Casella ID applicazione (client)](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

1. Selezionare **Certificati e segreti** nel riquadro sinistro dell'app e selezionare il pulsante **Nuovo segreto client** . Immettere una descrizione significativa per il segreto client e selezionare l'opzione **Mai** in **Scade** . Selezionare **Aggiungi** per creare il segreto client.

    ![Voce di menu Certificati e segreti](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

1. Non appena la creazione del segreto client viene completata, copiare il valore di **Segreto client** . Non sarà possibile recuperare il valore dopo aver chiuso la pagina. Salvare questo valore perché sarà necessario specificarlo nel portale di pubblicazione per ricevere i lead per l'offerta del marketplace. 
1. Selezionare **Autorizzazioni API** nel riquadro sinistro dell'app e quindi selezionare **+ Aggiungi un'autorizzazione** .
1. Selezionare **API Microsoft** e quindi selezionare **Dynamics CRM** come API.
1. In **Indicare le autorizzazioni necessarie per l'applicazione** verificare che l'opzione **Autorizzazioni delegate** sia selezionata. 
1. In **Autorizzazioni** selezionare la casella di controllo **user_impersonation** per **Access Common Data Service as organization users** (Accesso a Common Data Service come utenti dell'organizzazione). Selezionare quindi **Aggiungi autorizzazioni** .

    ![Pulsante Aggiungi autorizzazioni](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

1. Dopo aver completato i passaggi da 1 a 14 nel portale di Azure, accedere all'istanza di Dynamics 365 Customer Engagement passando all'URL, ad esempio `https://tenant.crm.dynamics.com`.
1. Selezionare l'icona a forma di ingranaggio nella barra superiore e quindi selezionare **Impostazioni avanzate** .
1. Nella pagina **Impostazioni** aprire il menu **Impostazioni** sulla barra superiore e selezionare **Sicurezza** .
1. Nella pagina **Sicurezza** selezionare  **Utenti** . Nella pagina **Utenti** selezionare l'elenco a discesa **Utenti abilitati** e quindi selezionare **Utenti applicazioni** .
1. Selezionare **Nuovo** per creare un nuovo utente. 

    ![Creare un nuovo utente](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

1. Nel riquadro **Nuovo utente** verificare che l'opzione **UTENTE: UTENTE APPLICAZIONE** sia selezionata. Specificare nome utente, nome e cognome e indirizzo di posta elettronica per l'utente che si vuole usare con la connessione. Incollare anche l' **ID applicazione** per l'app creata nel portale di Azure nel passaggio 8. Selezionare **Salva e chiudi** per completare l'aggiunta dell'utente.

    ![Riquadro Nuovo utente](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

1. Passare alla sezione "Impostazioni di sicurezza" di questo articolo per completare la configurazione della connessione per l'utente corrente.

### <a name="office-365"></a>Office 365

Se non si vuole usare Azure Active Directory, è possibile registrare un nuovo utente nell'interfaccia di amministrazione di Microsoft 365. Per continuare a ottenere lead, è necessario aggiornare il nome utente e la password ogni 90 giorni.

Per configurare Office 365 per Dynamics 365 Customer Engagement:

1. Accedere all'[interfaccia di amministrazione di Microsoft 365](https://admin.microsoft.com).

1. Selezionare **Aggiungi utente** .

    ![Opzione Aggiungi utente dell'interfaccia di amministrazione di Microsoft 365](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

1. Creare un nuovo utente per il servizio Lead Writer. Configurare le seguenti impostazioni:

    * Immettere un nome utente.
    * Immettere una password e deselezionare l'opzione **Chiedi all'utente di cambiare la password al primo accesso** .
    * Selezionare il ruolo **Utente (senza accesso di amministratore)** come ruolo per l'utente.
    * Selezionare **Dynamics 365 Customer Engagement Plan** (Piano di Dynamics 365 Customer Engagement) come licenza del prodotto, come illustrato nella schermata seguente. L'addebito avverrà in base alla licenza scelta. 

Salvare questi valori perché sarà necessario specificare i valori **Nome utente** e **Password** nel portale di pubblicazione per ricevere i lead per l'offerta del marketplace.

![Riquadro nuovo utente dell'interfaccia di amministrazione di Microsoft 365](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Impostazioni di sicurezza

Il passaggio finale consente all'utente creato di scrivere i lead.

1. Aprire Dynamics 365 Customer Engagement passando all'URL dell'istanza di Dynamics, ad esempio `https://tenant.crm.dynamics.com`.
1. Selezionare l'icona a forma di ingranaggio nella barra superiore e quindi selezionare **Impostazioni avanzate** .
1. Nella pagina **Impostazioni** aprire il menu **Impostazioni** sulla barra superiore e selezionare **Sicurezza** .
1. Nella pagina **Sicurezza** selezionare **Utenti** e selezionare l'utente creato nella sezione "Configurare le autorizzazioni utente" di questo documento, quindi selezionare **Gestisci ruoli** . 

    ![Scheda Gestisci ruoli](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

1. Cercare il nome del ruolo **Microsoft Marketplace Lead Writer** e selezionarlo per assegnare l'utente al ruolo.

    ![Riquadro Gestisci ruoli utente](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!NOTE]
    >Questo ruolo viene creato dalla soluzione importata. Dispone solo di autorizzazioni per la scrittura dei lead e dell'individuazione della versione della soluzione per garantirne la compatibilità.

1. Tornare alla pagina **Sicurezza** e selezionare **Ruoli di sicurezza** . Cercare il ruolo **Microsoft Marketplace Lead Writer** e selezionarlo.

    ![Riquadro Ruoli di sicurezza](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

1. Nel ruolo di sicurezza selezionare la scheda **Record principali** . Cercare l'elemento **Impostazioni interfaccia utente entità utente** . Abilitare le autorizzazioni di creazione, lettura e scrittura per l'utente (1/4 di cerchio giallo) per tale entità facendo clic una volta in ognuno dei cerchi corrispondenti.

    ![Scheda Record principali di Microsoft Marketplace Lead Writer](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

1. Nella scheda **Personalizzazione** cercare l'elemento **Processo di sistema** . Abilitare le autorizzazioni di lettura, scrittura e aggiunta per l'organizzazione (cerchi verdi pieni) per tale entità facendo clic quattro volte in ognuno dei cerchi corrispondenti.

    ![Scheda Personalizzazione di Microsoft Marketplace Lead Writer](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

1. Selezionare **Salva e chiudi** .

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement"></a>Configurare l'offerta per inviare lead a Dynamics 365 Customer Engagement 

Per configurare le informazioni di gestione dei lead per l'offerta nel portale di pubblicazione:

1. Passare alla pagina **Configurazione dell'offerta** per l'offerta.
1. Nella sezione **Customer leads** (Lead clienti) selezionare **Connetti** .

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-dynamics/customer-leads.png" alt-text="Lead clienti":::

1. Nella finestra popup Dettagli connessione selezionare **Dynamics 365 Customer Engagement** come destinazione del lead.

    ![Casella della destinazione del lead](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

1. Immettere l' **URL** per l'istanza di Dynamics 365, ad esempio `https://contoso.crm4.dynamics.com`.

1. Come metodo di **autenticazione** selezionare Azure Active Directory o Office 365. 
1. Se si è selezionato **Azure Active Directory** , immettere l' **ID applicazione (client)** (ad esempio, `23456052-aaaa-bbbb-8662-1234df56788f`), l' **ID directory** (ad esempio, `12345678-8af1-4asf-1234-12234d01db47`) e il **segreto client** (ad esempio, `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`).

    ![Autenticazione con Azure Active Directory selezionata](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

1. Se si è selezionato **Office 365** , immettere il **nome utente** (ad esempio, `contoso@contoso.onmicrosoft.com`) e la **password** (ad esempio, `P@ssw0rd`).

    ![Casella Nome utente di Office 365](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

1. Per **Indirizzo posta elettronica contatto** immettere gli indirizzi delle persone dell'organizzazione che dovranno ricevere le notifiche tramite posta elettronica quando si riceve un nuovo lead. È possibile immettere più indirizzi di posta elettronica separandoli con un punto e virgola.
1. Selezionare **OK** .

Per assicurarsi di avere eseguito la connessione a una destinazione del lead, selezionare il pulsante **Convalida** . In caso di esito positivo, sarà presente un lead di test nella destinazione del lead.

![Casella Indirizzo di posta elettronica contatto](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-connection-details.png)

>[!NOTE]
>È necessario completare la configurazione del resto dell'offerta e pubblicarla prima di poter ricevere i lead per l'offerta.