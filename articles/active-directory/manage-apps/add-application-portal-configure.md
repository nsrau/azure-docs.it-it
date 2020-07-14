---
title: "Avvio rapido: Configurare le proprietà per un'applicazione nel tenant di Azure Active Directory (Azure AD)"
description: In questo argomento di avvio rapido si usa il portale di Azure per configurare un'applicazione registrata nel tenant di Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82a3310f6fc2169a515b4b13d81c88d187bd0f9c
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85956153"
---
# <a name="quickstart-configure-properties-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Avvio rapido: Configurare le proprietà per un'applicazione nel tenant di Azure Active Directory (Azure AD)

Nell'argomento di avvio rapido precedente è stata aggiunta un'applicazione al tenant di Azure AD. Quando si aggiunge un'applicazione, si accetta che il tenant di Azure AD sappia che è il provider di identità per l'app. A questo punto, verranno configurate alcune delle proprietà per l'app.
 
## <a name="prerequisites"></a>Prerequisiti

Per configurare le proprietà di un'applicazione nel tenant di Azure AD, sono necessari:

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uno dei ruoli seguenti: amministratore globale, amministratore di applicazioni cloud o amministratore di applicazioni oppure proprietario dell'entità servizio.
- (Facoltativo: completamento di [Visualizzare le app](view-applications-portal.md)).
- (Facoltativo: completamento di [Aggiungere un'app](add-application-portal.md)).

>[!IMPORTANT]
>Per testare i passaggi di questo argomento di avvio rapido, è consigliabile usare un ambiente non di produzione.

## <a name="configure-app-properties"></a>Configurare le proprietà dell'app

Dopo aver aggiunto un'applicazione al tenant di Azure AD, viene immediatamente visualizzata la pagina di panoramica di tale applicazione. Se si configura un'applicazione che è già stata aggiunta, esaminare il primo argomento di avvio rapido che spiega come visualizzare le applicazioni aggiunte al tenant. 

Per modificare le proprietà dell'applicazione:

1. Nel portale di Azure AD selezionare **Applicazioni aziendali** e quindi trovare e selezionare l'applicazione da configurare.
2. Nella sezione Gestisci selezionare **Proprietà** per aprire il riquadro delle proprietà per la modifica.
    ![Mostra la schermata Proprietà e le proprietà modificabili dell'app](media/add-application-portal/edit-properties.png)
3. Dedicare qualche istante all'esame delle opzioni disponibili per la configurazione.
    - **Abilitata per l'accesso degli utenti?** determina se gli utenti assegnati all'applicazione potranno eseguire l'accesso.
    - **Assegnazione di utenti obbligatoria** determina se gli utenti non assegnati all'applicazione potranno eseguire l'accesso.
    - **Visibile agli utenti?** determina se gli utenti assegnati a un'app possono visualizzarla nel pannello di accesso (https://myapps.microsoft.com) e nell'icona di avvio delle app di O365 (il menu cialda in alto a sinistra in un sito Web Office 365 o Microsoft 365).
4. Usare le tabelle seguenti per scegliere le opzioni ottimali per le proprie esigenze.

   - Comportamento per gli utenti **assegnati**:

       | Proprietà dell'applicazione | Proprietà dell'applicazione | Proprietà dell'applicazione | Esperienza degli utenti assegnati | Esperienza degli utenti assegnati |
       |---|---|---|---|---|
       | Abilitata per l'accesso degli utenti? | Assegnazione utenti obbligatoria | Visibile agli utenti? | Gli utenti assegnati possono eseguire l'accesso? | L'applicazione viene visualizzata agli utenti assegnati?* |
       | sì | sì | sì | sì | sì  |
       | sì | sì | no  | sì | no   |
       | sì | no  | sì | sì | sì  |
       | sì | no  | no  | sì | no   |
       | no  | sì | sì | no  | no   |
       | no  | sì | no  | no  | no   |
       | no  | no  | sì | no  | no   |
       | no  | no  | no  | no  | no   |

   - Comportamento per gli utenti **non assegnati**:

       | Proprietà dell'applicazione | Proprietà dell'applicazione | Proprietà dell'applicazione | Esperienza degli utenti non assegnati | Esperienza degli utenti non assegnati |
       |---|---|---|---|---|
       | Abilitata per l'accesso degli utenti? | Assegnazione utenti obbligatoria | Visibile agli utenti? | Gli utenti non assegnati possono eseguire l'accesso? | L'applicazione viene visualizzata agli utenti non assegnati?* |
       | sì | sì | sì | no  | no   |
       | sì | sì | no  | no  | no   |
       | sì | no  | sì | sì | no   |
       | sì | no  | no  | sì | no   |
       | no  | sì | sì | no  | no   |
       | no  | sì | no  | no  | no   |
       | no  | no  | sì | no  | no   |
       | no  | no  | no  | no  | no   |

     *L'applicazione viene visualizzata agli utenti nel pannello di accesso e nell'icona di avvio delle app di Office 365?

## <a name="use-a-custom-logo"></a>Usare un logo personalizzato

Per usare un logo personalizzato:

1. Creare un logo di 215x215 pixel e salvarlo in formato PNG.
2. Nel portale di Azure AD selezionare **Applicazioni aziendali** e quindi trovare e selezionare l'applicazione da configurare.
3. Nella sezione Gestisci selezionare **Proprietà** per aprire il riquadro delle proprietà per la modifica. 
4. Selezionare l'icona per caricare il logo.
5. Al termine, fare clic su **Salva**. 
    ![Mostra come cambiare il logo nella pagina delle proprietà dell'app](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > L'anteprima visualizzata in questo riquadro **Proprietà** non viene aggiornata immediatamente. Per visualizzare l'icona aggiornata, è possibile chiudere e riaprire le proprietà.

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver configurato le proprietà di un'applicazione, è possibile continuare con la configurazione dell'accesso Single Sign-On.

- [Configurare Single Sign-On](add-application-portal-setup-sso.md)
- [Eliminare un'app](delete-application-portal.md)