---
title: Esercitazione - Creare il primo pacchetto di accesso in Gestione entitlement di Azure AD - Azure Active Directory
description: Esercitazione dettagliata per la creazione del primo pacchetto di accesso in Gestione entitlement di Azure Active Directory.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 10/22/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: de4d4a1825149a512d7abdb192d8fb9d49e85a20
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174864"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management"></a>Esercitazione: Creare il primo pacchetto di accesso in Gestione entitlement di Azure AD

La gestione degli accessi a tutte le risorse necessarie per i dipendenti, ad esempio gruppi, applicazioni e siti, è una funzione importante per le organizzazioni. È necessario concedere ai dipendenti il livello di accesso corretto affinché possano essere produttivi e rimuovere l'accesso quando non è più necessario.

In questa esercitazione l'utente lavora come amministratore IT presso Woodgrove Bank. Si è ricevuto l'incarico di creare un pacchetto di risorse per una campagna di marketing per la gestione self-service delle richieste da parte degli utenti interni. Le richieste non richiedono l'approvazione e l'accesso degli utenti scade dopo 30 giorni. Per questa esercitazione, le risorse della campagna di marketing sono semplici appartenenze ad un singolo gruppo, ma potrebbe trattarsi di una raccolta di gruppi, applicazioni o siti di SharePoint Online.

![Panoramica dello scenario](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un pacchetto di accesso con un gruppo come risorsa
> * Consentire a un utente nella directory di richiedere l'accesso
> * Mostrare come un utente interno può richiedere il pacchetto di accesso

Per una dimostrazione dettagliata del processo di distribuzione di Gestione entitlement di Azure Active Directory, inclusa la creazione del primo pacchetto di accesso, vedere il video seguente:

>[!VIDEO https://www.youtube.com/embed/zaaKvaaYwI4]

## <a name="prerequisites"></a>Prerequisiti

Per usare Gestione entitlement di Azure AD è necessario disporre di una delle licenze seguenti:

- Azure AD Premium P2
- Licenza di Enterprise Mobility + Security (EMS) E5

Per altre informazioni, vedere [Requisiti relativi alle licenze](entitlement-management-overview.md#license-requirements).

## <a name="step-1-set-up-users-and-group"></a>Passaggio 1: Configurare utenti e gruppi

Una directory di risorse contiene una o più risorse da condividere. In questo passaggio verrà creato un gruppo denominato **Marketing resources** nella directory Woodgrove Bank che rappresenta la risorsa di destinazione per la gestione entitlement. Verrà anche configurato un richiedente interno.

**Ruolo prerequisito:** Amministratore globale o Amministratore utenti

![Creare utenti e gruppi](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale o amministratore utenti.  

1. Nel menu di spostamento a sinistra fare clic su **Azure Active Directory**.

1. Creare o configurare i due utenti seguenti. È possibile usare questi nomi o nomi diversi. **Admin1** può essere l'utente con cui è stato eseguito l'accesso.

    | NOME | Ruolo directory |
    | --- | --- |
    | **Admin1** | Amministratore globale<br/>-oppure-<br/>Amministratore utenti |
    | **Requestor1** | Utente |

1. Creare un gruppo di sicurezza di Azure AD denominato **Marketing resources** con il tipo di appartenenza **Assegnato**.

    Questo gruppo sarà la risorsa di destinazione per gestione entitlement. Per iniziare, il gruppo non deve contenere membri.

## <a name="step-2-create-an-access-package"></a>Passaggio 2: Creare un pacchetto di accesso

Un *pacchetto di accesso* è un bundle di risorse di cui necessita un team o un progetto e che è disciplinato dai criteri. I pacchetti di accesso sono definiti in contenitori denominati *cataloghi*. In questo passaggio verrà creato un pacchetto di accesso **Marketing Campaign** nel catalogo **Generale**.

**Ruolo prerequisito:** amministratore globale, amministratore utenti, proprietario del catalogo o responsabile dei pacchetti di accesso

![Creare un pacchetto di accesso](./media/entitlement-management-access-package-first/elm-access-package.png)

1. Nel riquadro di spostamento sinistro del portale di Azure fare clic su **Azure Active Directory**.

1. Nel menu a sinistra fare clic su **Identity Governance**

1. Nel menu a sinistra fare clic su **Pacchetti di accesso**.  Se è visualizzato il messaggio **Accesso negato**, assicurarsi che nella directory sia presente una licenza di Azure AD Premium P2.

1. Fare clic su **Nuovo pacchetto di accesso**.

    ![Gestione entitlement nel portale di Azure](./media/entitlement-management-shared/access-packages-list.png)

1. Nella scheda **Informazioni di base** digitare il nome **Marketing Campaign** e la descrizione **Access to resources for the campaign** per il pacchetto di accesso.

1. Lasciare il menu a discesa **Catalogo** impostato su **Generale**.

    ![Nuovo pacchetto di accesso - scheda Informazioni di base](./media/entitlement-management-access-package-first/basics.png)

1. Fare clic su **Avanti** per aprire la scheda **Ruoli delle risorse**.

    In questa scheda sarà necessario selezionare le risorse e il relativo ruolo da includere nel pacchetto di accesso.

1. Fare clic su **Gruppi e team**.

1. Nel riquadro Seleziona gruppi trovare e selezionare il gruppo **Marketing resources** creato in precedenza.

    Per impostazione predefinita, i gruppi sono visualizzati all'interno e all'esterno del catalogo **Generale**. Quando si seleziona un gruppo all'esterno del catalogo **Generale**, questo verrà aggiungo al catalogo **Generale**.

    ![Nuovo pacchetto di accesso - scheda Ruoli delle risorse](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

1. Fare clic su **Seleziona** per aggiungere il gruppo all'elenco.

1. Nell'elenco a discesa **Ruolo** selezionare **Membro**.

    ![Nuovo pacchetto di accesso - scheda Ruoli delle risorse](./media/entitlement-management-access-package-first/resource-roles.png)

1. Fare clic su **Avanti** per aprire la scheda **Richieste**.

    In questa scheda è possibile creare un criterio di richiesta. Un *criterio* definisce le regole o i vincoli per accedere a un pacchetto di accesso. Verrà creato un criterio che consente a un utente specifico nella directory della risorsa di richiedere questo pacchetto di accesso.

1. Nella sezione **Utenti che possono richiedere l'accesso** selezionare **Per gli utenti nella directory** e quindi **Utenti e gruppi specifici**.

    ![Nuovo pacchetto di accesso - scheda Richieste](./media/entitlement-management-access-package-first/requests.png)

1. Fare clic su **Aggiungi utenti e gruppi**.

1. Nel riquadro Seleziona utenti e gruppi selezionare l'utente **Requestor1** creato in precedenza.

    ![Nuovo pacchetto di accesso - scheda Richieste - Seleziona utenti e gruppi](./media/entitlement-management-access-package-first/requests-select-users-groups.png)

1. Fare clic su **Seleziona**.

1. Scorrere verso il basso fino alle sezioni **Approvazione** e **Abilita le richieste**.

1. Lasciare l'opzione **Richiedi approvazione** impostata su **No**.

1. Per **Abilita le richieste**, fare clic su **Sì** per consentire la richiesta di questo pacchetto di accesso non appena viene creato.

    ![Nuovo pacchetto di accesso - scheda Richieste - Approvazione e Abilita le richieste](./media/entitlement-management-access-package-first/requests-approval-enable.png)

1. Fare clic su **Avanti** per aprire la scheda **Ciclo di vita**.

1. Nella sezione **Scadenza** impostare **Scadenza delle assegnazioni di pacchetti di accesso** su **Numero di giorni**.

1. Impostare **Le assegnazioni scadono dopo** su **30** giorni.

    ![Nuovo pacchetto di accesso - scheda Ciclo di vita](./media/entitlement-management-access-package-first/lifecycle.png)

1. Fare clic su **Avanti** per aprire la scheda **Rivedi e crea**.

    ![Nuovo pacchetto di accesso - scheda Rivedi e crea](./media/entitlement-management-access-package-first/review-create.png)

    Dopo alcuni istanti, verrà visualizzata una notifica che indica che il pacchetto di accesso è stato creato.

1. Nel menu a sinistra del pacchetto di accesso Marketing Campaign fare clic su **Panoramica**.

1. Copiare il **collegamento del portale di Accesso personale**.

    Questo collegamento verrà usato nel passaggio successivo.

    ![Panoramica del pacchetto di accesso - Collegamento del portale di Accesso personale](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-3-request-access"></a>Passaggio 3: Richiedere l'accesso

In questo passaggio le procedure verranno eseguite come **richiedente interno** e verrà richiesto l'accesso al pacchetto di accesso. I richiedenti inviano le richieste tramite un sito chiamato portale di Accesso personale. Il portale di Accesso personale consente ai richiedenti di inviare le richieste per i pacchetti di accesso, visualizzare i pacchetti di accesso ai quali sono già autorizzati ad accedere e visualizzare la cronologia delle richieste.

**Ruolo prerequisito:** Richiedente interno

1. Disconnettersi dal portale di Azure.

1. In una nuova finestra del browser passare al collegamento del portale di Accesso personale copiato nel passaggio precedente.

1. Eseguire l'accesso al portale di Accesso personale come **Requestor1**.

    Dovrebbe essere visibile il pacchetto di accesso **Marketing Campaign**.

1. Se necessario, nella colonna **Descrizione** fare clic sulla freccia per visualizzare i dettagli relativi al pacchetto di accesso.

    ![Portale di Accesso personale - Pacchetti di accesso](./media/entitlement-management-shared/my-access-access-packages.png)

1. Fare clic sul segno di spunta per selezionare il pacchetto.

1. Fare clic su **Richiedi accesso** per aprire il riquadro Richiedi accesso.

    ![Portale di Accesso personale - pulsante Richiedi accesso](./media/entitlement-management-access-package-first/my-access-request-access-button.png)

1. Nella casella **Motivazione aziendale** digitare la motivazione **I am working on the new marketing campaign**.

    ![Portale di Accesso personale - Richiedi accesso](./media/entitlement-management-shared/my-access-request-access.png)

1. Fare clic su **Submit** (Invia).

1. Nel menu a sinistra fare clic su **Cronologia delle richieste** per verificare che la richiesta sia stata inviata.

## <a name="step-4-validate-that-access-has-been-assigned"></a>Passaggio 4: Confermare l'assegnazione dell'accesso

In questo passaggio si confermerà che il pacchetto di accesso è stato assegnato al **richiedente interno** che ora è membro del gruppo **Marketing resources**.

**Ruolo prerequisito:** amministratore globale, amministratore utenti, proprietario del catalogo o responsabile dei pacchetti di accesso

1. Disconnettersi dal portale di Accesso personale.

1. Accedere al [portale di Azure](https://portal.azure.com) come **Admin1**.

1. Fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Pacchetti di accesso**.

1. Individuare il pacchetto di accesso **Marketing Campaign** e farvi clic.

1. Nel menu a sinistra fare clic su **Richieste**.

    Si dovrebbero vedere Requestor1 e il criterio iniziale con stato **Recapitati**.

1. Fare clic sulla richiesta per visualizzare i dettagli della richiesta.

    ![Pacchetto di accesso - Dettagli richiesta](./media/entitlement-management-access-package-first/request-details.png)

1. Nel menu di spostamento a sinistra fare clic su **Azure Active Directory**.

1. Fare clic su **Gruppi** e aprire il gruppo **Marketing resources**.

1. Fare clic su **Membri**.

    Nell'elenco dei membri verrà visualizzato anche **Requestor1**.

    ![Membri di Marketing resources](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-5-clean-up-resources"></a>Passaggio 5: Pulire le risorse

In questo passaggio verranno rimosse le modifiche apportate e verrà eliminato il pacchetto di accesso **Marketing Campaign**.

**Ruolo prerequisito:**  Amministratore globale o Amministratore utenti

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Aprire il pacchetto di accesso **Marketing Campaign**.

1. Fare clic su **Assegnazioni**.

1. Per **Requestor1**, fare clic sui puntini di sospensione ( **...** ) e quindi su **Rimuovi accesso**. Nel messaggio visualizzato fare clic su **Sì**.

    Dopo qualche istante lo stato passerà da Recapitato a Scaduto.

1. Fare clic su **Ruoli delle risorse**.

1. Per **Marketing resources**, fare clic sui puntini di sospensione ( **...** ) e quindi su **Rimuovi il ruolo della risorsa**. Nel messaggio visualizzato fare clic su **Sì**.

1. Aprire l'elenco dei pacchetti di accesso.

1. Per **Marketing Campaign**, fare clic sui puntini di sospensione ( **...** ) e quindi su **Elimina**. Nel messaggio visualizzato fare clic su **Sì**.

1. In Azure Active Directory eliminare gli utenti creati, ad esempio **Requestor1** e **Admin1**.

1. Eliminare il gruppo **Marketing resources**.

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo per informazioni sulle procedure degli scenari comuni in Gestione entitlement.
> [!div class="nextstepaction"]
> [Scenari comuni](entitlement-management-scenarios.md)
