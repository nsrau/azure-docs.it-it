---
title: Gestione dei lead per Dynamics 365 per il coinvolgimento dei clienti | Azure Marketplace
description: Configurare la gestione dei lead per Dynamics 365 per il coinvolgimento dei clienti.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: evansma
ms.openlocfilehash: 181a3f3a8d3cabb2fdf6caf79cef16201fab0c68
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177798"
---
# <a name="configure-lead-management-for-dynamics-365-for-customer-engagement"></a>Configurare la gestione dei lead per Dynamics 365 per Customer Engagement

Questo articolo descrive come configurare Dynamics 365 per l'engagement dei clienti (in precedenza Dynamics CRM online) [. per altre informazioni sulla modifica,](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) vedere l'articolo relativo all'elaborazione dei lead di vendita dall'offerta del Marketplace. 

>[!Note]
>Queste istruzioni sono specifiche per l'ambiente Microsoft Hosted cloud Dynamics 365 for Customer Engagement. La connessione diretta a un ambiente locale Dynamics non è attualmente supportata. sono disponibili altre opzioni per la ricezione di lead, ad esempio la configurazione di un [endpoint HTTPS](./commercial-marketplace-lead-management-instructions-https.md) o di una [tabella di Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) per la ricezione di lead.

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi descritti in questo articolo, sono necessarie le autorizzazioni utente seguenti:

* È necessario essere un amministratore dell'istanza di Dynamics 365 per customer engagement per poter installare una soluzione e seguire queste istruzioni.
* È necessario essere un amministratore tenant per creare un nuovo account di servizio per il servizio lead usato per inviare lead dalle offerte del Marketplace.
* È necessario avere accesso al portale di amministrazione di Office 365.
* È necessario avere accesso al portale di Azure.

## <a name="install-the-solution"></a>Installare la soluzione

1.  Scaricare la [soluzione Microsoft Marketplace lead writer](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) e salvarla localmente nel computer.

2.  Aprire Dynamics 365 per Customer Engagement passando all'URL per l'istanza di Dynamics, ad esempio `https://tenant.crm.dynamics.com`.

3.  Accedere alle impostazioni selezionando l'icona a forma di ingranaggio e **le impostazioni avanzate** nella barra di spostamento superiore.
 
    ![Dynamics-impostazioni avanzate](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

4.  Una volta nella pagina Impostazioni, accedere al menu impostazione dalla barra di spostamento superiore e selezionare **soluzioni**.

    >[!Note]
    >Se le opzioni non vengono visualizzate nella schermata successiva, non si dispone delle autorizzazioni necessarie per continuare. Rivolgersi a un amministratore di Dynamics 365 per l'istanza di engagement del cliente.

    ![Dynamics 365-soluzioni](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

5. Nella pagina soluzioni selezionare **Importa** e passare al percorso in cui è stata salvata la soluzione *Microsoft Marketplace lead writer* scaricata nel passaggio 1.

    ![Dynamics 365 per Customer Engagement-importazione](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

6. Completare l'importazione della soluzione seguendo la procedura guidata Importa soluzione.

## <a name="configure-user-permissions"></a>Configurare le autorizzazioni utente

Per scrivere i lead nell'istanza di Dynamics 365 per Customer Engagement, è necessario condividere un account del servizio con US e configurare le autorizzazioni per l'account.

Usare la procedura seguente per creare l'account del servizio e assegnare le autorizzazioni. Usare **Azure Active Directory** oppure **Office 365**.

>[!Note]
>In base all'opzione di autenticazione selezionata, è possibile passare alle istruzioni corrispondenti in base alla scelta effettuata. Vedere [Azure Active Directory](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory) o [Office 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

Questa opzione è consigliata perché si ottiene il vantaggio aggiuntivo che non è mai necessario aggiornare il nome utente e la password per ottenere i lead. Per usare l'opzione Azure Active Directory, specificare l'ID app, la chiave dell'applicazione e l'ID directory dall'applicazione Active Directory.

Usare la procedura seguente per configurare Azure Active Directory per Dynamics 365 per il coinvolgimento dei clienti.

1. Accedere a [portale di Azure](https://portal.azure.com/)e quindi selezionare il servizio Azure Active Directory dal percorso di spostamento a sinistra.

2. Selezionare **Proprietà** dalla Azure Active Directory spostamento a sinistra e copiare il valore di **ID directory** nella pagina. Salvare questo valore, in quanto si tratta del valore di *ID directory* che è necessario fornire nel portale di pubblicazione per ricevere i lead per l'offerta del Marketplace.

    ![Proprietà Azure Active Directory](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

3. Selezionare **registrazioni app** dal Azure Active Directory di spostamento a sinistra e quindi selezionare **nuova registrazione** nella pagina.
4. Immettere un nome per il nome dell'applicazione. Specificare un nome di applicazione significativo.
5. In tipi di account supportati selezionare **account in qualsiasi directory dell'organizzazione**.
6. In URI di reindirizzamento selezionare **Web** e specificare un URI (ad esempio `https://contosoapp1/auth`). 
7. Selezionare **Registra**.

    ![Registrare un'applicazione](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

8. Ora che l'applicazione è stata registrata, accedere alla pagina Panoramica dell'applicazione e copiare il valore di **ID applicazione (client)** in quella pagina. Salvare questo valore, poiché si tratta del valore *ID dell'applicazione (client)* che è necessario fornire nel portale di pubblicazione e in Dynamics per ricevere lead per l'offerta del Marketplace.

    ![ID applicazione (client)](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

9. Selezionare **certificati e segreti** dal percorso di spostamento a sinistra dell'app e selezionare **nuovo segreto client** nella pagina. Immettere una descrizione significativa per il segreto client e selezionare l'opzione **Never** in Expires. Selezionare **Aggiungi** per creare il segreto client.

    ![Applicazione-certificazione e segreti](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

10. Non appena il segreto client viene creato correttamente, **copiare il valore del segreto client**. Non sarà possibile recuperare il valore dopo aver esplorato la pagina. Salvare questo valore, poiché si tratta del valore del *segreto client* che è necessario fornire nel portale di pubblicazione per ricevere i lead per l'offerta del Marketplace. 
11. Selezionare **autorizzazioni API** dal percorso di spostamento a sinistra delle app e quindi selezionare **Aggiungi un'autorizzazione**.
12. Selezionare API Microsoft, quindi selezionare **Dynamics CRM** come API.
13. In base al *tipo di autorizzazioni richiesto dall'applicazione*, assicurarsi che sia selezionata l'opzione **autorizzazioni delegate** . Controllare l'autorizzazione per **user_impersonation** *Access Common Data Service come utenti dell'organizzazione*. Selezionare **Aggiungi autorizzazioni**.

    ![Aggiungere autorizzazioni](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

14. Dopo aver completato i passaggi 1-13 nella portale di Azure, passare all'istanza di Dynamics 365 per Customer Engagement passando all'URL, ad esempio `https://tenant.crm.dynamics.com`.
15. Accedere alle impostazioni selezionando l'icona dell'ingranaggio e **le impostazioni avanzate** nella barra di spostamento superiore.
16. Una volta nella pagina Impostazioni, accedere al menu impostazione dalla barra di spostamento superiore e selezionare **sicurezza**.
17. Nella pagina sicurezza selezionare **utenti**.  Nella pagina utenti selezionare l'elenco a discesa "utenti abilitati" per passare agli **utenti dell'applicazione**.
18. Selezionare **Nuovo** per creare un nuovo utente. 

    ![Creare un nuovo utente](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

19. In **nuovo utente**verificare che l'utente: UTENTE applicazione selezionato. Specificare un nome utente, un nome completo e un indirizzo di posta elettronica per l'utente che si desidera utilizzare con la connessione. Incollare anche l' **ID applicazione** per l'app creata nel portale di Azure del passaggio 8. Selezionare **Salva e Chiudi** per completare l'aggiunta dell'utente.

    ![Nuovo utente](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

20. Passare alla sezione"Impostazioni di sicurezza" di questo articolo per completare la configurazione della connessione per l'utente corrente.

### <a name="office-365"></a>Office 365

Se non si vuole usare Azure Active Directory, è possibile registrare un nuovo utente nell'interfaccia di *amministrazione di Microsoft 365*. Per continuare a ottenere lead, è necessario aggiornare il nome utente o la password ogni 90 giorni.

Usare la procedura seguente per configurare Office 365 per Dynamics 365 per il coinvolgimento dei clienti.

1. Accedere al centro di [amministrazione Microsoft 365](https://admin.microsoft.com).

2. Selezionare **Aggiungi utente**.

    ![Interfaccia di amministrazione di Microsoft 365-aggiungere un utente](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

4. Creare un nuovo utente per il servizio Lead Writer. Configurare le seguenti impostazioni:

    * Specificare un nome utente
    * Specificare una password e deselezionare l'opzione "imposta l'utente per la modifica della password al primo accesso".
    * Selezionare il ruolo "Utente - nessun accesso amministratore" come ruolo per l'utente.
    * Selezionare "piano di engagement per i clienti di Dynamics 365" come licenza del prodotto visualizzata nella schermata successiva. L'addebito avverrà in base alla licenza scelta. 

Salvare questi valori in quanto si tratta dei valori di *nome utente e password* che è necessario fornire nel portale di pubblicazione per ricevere i lead per l'offerta del Marketplace.

![Centro di amministrazione Microsoft 365-nuovo utente](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Impostazioni di sicurezza

Il passaggio finale consente all'utente creato di scrivere i lead.

1. Aprire Dynamics 365 per Customer Engagement passando all'URL per l'istanza di Dynamics, ad esempio `https://tenant.crm.dynamics.com`.
2. Accedere alle impostazioni selezionando l'icona a forma di ingranaggio e **le impostazioni avanzate** nella barra di spostamento superiore.
3. Una volta nella pagina Impostazioni, accedere al menu impostazione dalla barra di spostamento superiore e selezionare **sicurezza**.
4. Nella pagina sicurezza selezionare **utenti** e selezionare l'utente creato nella sezione configurare le autorizzazioni utente di questo documento e quindi selezionare **Gestisci ruoli**. 

    ![Gestire i ruoli](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

5. Cercare il nome del ruolo "Microsoft Marketplace writer lead" e selezionarlo per assegnare l'utente al ruolo.

    ![Gestire i ruoli utente](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!Note]
    >Questo ruolo viene creato dalla soluzione importata. Dispone solo di autorizzazioni per la scrittura dei lead e dell'individuazione della versione della soluzione per garantirne la compatibilità.

6. Tornare alla pagina sicurezza e selezionare **ruoli di sicurezza**. Cercare il ruolo "Microsoft Marketplace writer lead" e selezionarlo.

    ![Ruoli di sicurezza](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

7. Al ruolo di sicurezza, selezionare la scheda **record principali** . Cercare l'entità "User Entity UI Settings" e abilitare le autorizzazioni di creazione, lettura e scrittura per l'utente (1/4 cerchio giallo) per tale entità facendo clic una volta in ciascuno dei cerchi corrispondenti.

    ![Writer Lead Microsoft Marketplace-record principali](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

8. Passare quindi alla scheda **personalizzazione** . Cercare l'entità "processo di sistema" di Tor e abilitare le autorizzazioni di lettura, scrittura e AppendTo per l'organizzazione (verde solido) per tale entità facendo clic quattro volte in ciascuno dei cerchi corrispondenti.

    ![Autore del lead di Microsoft Marketplace-personalizzazione](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

9. **Salvare e chiudere**.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-for-customer-engagement"></a>Configurare l'offerta per inviare lead a Dynamics 365 per il coinvolgimento dei clienti

Quando si è pronti per configurare le informazioni di gestione dei lead per l'offerta nel portale di pubblicazione, seguire questa procedura:

1. Passare alla pagina di **configurazione dell'offerta** per l'offerta.
2. Selezionare **Connetti** nella sezione gestione dei lead.

    ![Connettersi alla gestione dei lead](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

3. Nella finestra popup Dettagli connessione selezionare **Dynamics 365 per Customer Engagement** per la destinazione lead

    ![Dettagli connessione-destinazione lead](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

4. Specificare l' **URL dell'istanza di Dynamics 365** , ad esempio `https://contoso.crm4.dynamics.com`.
5. Selezionare il metodo di **autenticazione**, Azure Active Directory o Office 365. 
6. Se è stata selezionata l'opzione Azure Active Directory, specificare l' **ID dell'applicazione (client)** (ad esempio: `23456052-aaaa-bbbb-8662-1234df56788f`), l' **ID directory** (ad esempio: `12345678-8af1-4asf-1234-12234d01db47`) e il **segreto client** (ad esempio: `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`).

    ![Dettagli connessione-Azure Active Directory](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

7. Se è stata selezionata l'opzione Office 365, specificare il **nome utente** (ad esempio: `contoso@contoso.onmicrosoft.com`) e la password (ad esempio: `P@ssw0rd`).

    ![Dettagli connessione-nome utente](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

>[!Note]
>È necessario completare la configurazione del resto dell'offerta e pubblicarla prima di poter ricevere i lead per l'offerta.
