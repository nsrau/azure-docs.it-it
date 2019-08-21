---
title: Esercitazione - Creare il primo pacchetto di accesso in Gestione entitlement di Azure AD (anteprima) - Azure Active Directory
description: Esercitazione dettagliata sulla creazione del primo pacchetto di accesso in Gestione entitlement di Azure Active Directory (anteprima).
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
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76ba284ec1a30322a24c762a1829b399f2583c6c
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69032915"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management-preview"></a>Esercitazione: Creare il primo pacchetto di accesso in Gestione entitlement di Azure AD (anteprima)

> [!IMPORTANT]
> Gestione entitlement di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La gestione degli accessi a tutte le risorse necessarie per i dipendenti, ad esempio gruppi, applicazioni e siti, è una funzione importante per le organizzazioni. È necessario concedere ai dipendenti il livello di accesso corretto affinché possano essere produttivi e rimuovere l'accesso quando non è più necessario.

In questa esercitazione l'utente lavora come amministratore presso Woodgrove Bank. All'utente è stato richiesto di creare un pacchetto di risorse per un progetto Web per la gestione self-service delle richieste da parte degli utenti interni. Le richieste richiedono l'approvazione e l'accesso degli utenti scade dopo 30 giorni. Per questa esercitazione, le risorse del progetto Web sono semplici appartenenze in un singolo gruppo, ma potrebbe trattarsi di una raccolta di gruppi, applicazioni o siti di SharePoint Online.

![Panoramica dello scenario](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un pacchetto di accesso con un gruppo come risorsa
> * Designare un responsabile approvazione
> * Mostrare come un utente interno può richiedere il pacchetto di accesso
> * Approvare la richiesta di accesso

Per una dimostrazione dettagliata del processo di distribuzione di Gestione entitlement di Azure Active Directory, inclusa la creazione del primo pacchetto di accesso, vedere il video seguente:

>[!VIDEO https://www.youtube.com/embed/zaaKvaaYwI4]

## <a name="prerequisites"></a>Prerequisiti

Per usare Gestione entitlement di Azure AD (anteprima) è necessario disporre di una delle licenze seguenti:

- Azure AD Premium P2
- Licenza di Enterprise Mobility + Security (EMS) E5

Se non si dispone di una licenza di Azure AD Premium P2 o Enterprise Mobility + Security E5, è possibile creare un account di [valutazione di Enterprise Mobility + Security E5](https://signup.microsoft.com/Signup?OfferId=87dd2714-d452-48a0-a809-d2f58c4f68b7&ali=1) gratuito.

## <a name="step-1-set-up-users-and-group"></a>Passaggio 1: Configurare utenti e gruppi

Una directory di risorse contiene una o più risorse da condividere. In questo passaggio verrà creato un gruppo denominato **Engineering Group** nella directory Woodgrove Bank che rappresenta la risorsa di destinazione per gestione entitlement. Verrà anche configurato un richiedente interno.

**Ruolo prerequisito:** Amministratore globale o Amministratore utenti

![Creare utenti e gruppi](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale o amministratore utenti.  

1. Nel menu di spostamento a sinistra fare clic su **Azure Active Directory**.

1. Creare o configurare i due utenti seguenti. È possibile usare questi nomi o nomi diversi. **Admin1** può essere l'utente con cui è stato eseguito l'accesso.

    | NOME | Ruolo directory | DESCRIZIONE |
    | --- | --- | --- |
    | **Admin1** | Amministratore globale<br/>-oppure-<br/>Amministratore con limitazioni (amministratore utenti) | Amministratore e responsabile approvazione |
    | **Requestor1** | Utente | Richiedente interno |

    In questa esercitazione l'amministratore e il responsabile approvazione sono la stessa persona, ma in genere vengono designate una o più persone come responsabili approvazione.

1. Creare un gruppo di sicurezza di Azure AD denominato **Engineering Group** con un tipo di appartenenza di **Assegnato**.

    Questo gruppo sarà la risorsa di destinazione per gestione entitlement. Per iniziare, il gruppo non deve contenere membri.

## <a name="step-2-create-an-access-package"></a>Passaggio 2: Creare un pacchetto di accesso

Un *pacchetto di accesso* è un'aggregazione di tutte le risorse necessarie affinché un utente possa lavorare su un progetto o svolgere le proprie attività. I pacchetti di accesso sono definiti in contenitori denominati *cataloghi*. In questo passaggio verrà creato un **pacchetto di accesso progetto Web** nel catalogo **Generale**.

**Ruolo prerequisito:** Amministratore globale o Amministratore utenti

![Creare un pacchetto di accesso](./media/entitlement-management-access-package-first/elm-access-package.png)

1. Nel riquadro di spostamento sinistro del portale di Azure fare clic su **Azure Active Directory**.

1. Nel menu a sinistra fare clic su **Identity Governance**

1. Nel menu a sinistra fare clic su **Pacchetti di accesso**.  Se è visualizzato il messaggio **Accesso negato**, assicurarsi che sia presente una licenza di Azure AD Premium P2 in tale directory.

1. Fare clic su **Nuovo pacchetto di accesso**.

    ![Gestione entitlement nel portale di Azure](./media/entitlement-management-access-package-first/access-packages-list.png)

1. Nella scheda **Informazioni di base** digitare il nome **Pacchetto di accesso progetto Web** e la descrizione **Pacchetto di accesso per il progetto Web Engineering**.

1. Lasciare il menu a discesa **Catalogo** impostato su **Generale**.

    ![Nuovo pacchetto di accesso - scheda Informazioni di base](./media/entitlement-management-access-package-first/basics.png)

1. Fare clic su **Avanti** per aprire la scheda **Ruoli delle risorse**.

    In questa scheda sarà necessario selezionare le autorizzazioni da includere nel pacchetto di accesso.

1. Fare clic su **Gruppi**.

1. Nel riquadro Seleziona gruppi trovare e selezionare il gruppo **Engineering Group** creato in precedenza.

    Per impostazione predefinita, i gruppi sono visualizzati all'interno e all'esterno del catalogo **Generale**. Quando si seleziona un gruppo all'esterno del catalogo **Generale**, questo verrà aggiungo al catalogo **Generale**.

    ![Nuovo pacchetto di accesso - scheda Ruoli delle risorse](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

1. Fare clic su **Seleziona** per aggiungere il gruppo all'elenco.

1. Nell'elenco a discesa **Ruolo** selezionare **Membro**.

    ![Nuovo pacchetto di accesso - scheda Ruoli delle risorse](./media/entitlement-management-access-package-first/resource-roles.png)

1. Fare clic su **Avanti** per aprire la scheda **Criteri**.

1. Impostare l'opzione **Crea primo criterio** su **In seguito**.

    Il criterio verrà creato nella sezione successiva.

    ![Nuovo pacchetto di accesso - scheda Criteri](./media/entitlement-management-access-package-first/policy.png)

1. Fare clic su **Avanti** per aprire la scheda **Rivedi e crea**.

    ![Nuovo pacchetto di accesso - scheda Rivedi e crea](./media/entitlement-management-access-package-first/review-create.png)

1. Rivedere le impostazioni del pacchetto di accesso e quindi fare clic su **Crea**.

    Potrebbe essere visualizzato un messaggio che indica che il pacchetto di accesso non sarà visibile agli utenti perché il catalogo non è ancora abilitato.

    ![Nuovo pacchetto di accesso - Messaggio Non visibile](./media/entitlement-management-access-package-first/not-visible.png)

1. Fare clic su **OK**.

    Dopo alcuni istanti, verrà visualizzata una notifica che indica che il pacchetto di accesso è stato creato.

## <a name="step-3-create-a-policy"></a>Passaggio 3: Creare un criterio

Un *criterio* definisce le regole o i vincoli per accedere a un pacchetto di accesso. In questo passaggio verrà creato un criterio che consente a un utente specifico nella directory della risorsa di richiedere il pacchetto di accesso. Verrà anche specificato che le richieste devono essere approvate e si indicherà il responsabile approvazione.

![Creare un criterio del pacchetto di accesso](./media/entitlement-management-access-package-first/elm-access-package-policy.png)

**Ruolo prerequisito:** Amministratore globale o Amministratore utenti

1. Nel menu a sinistra del **pacchetto di accesso progetto Web** fare clic su **Criteri**.

    ![Elenco criteri del pacchetto di accesso](./media/entitlement-management-access-package-first/policies-list.png)

1. Fare clic su **Aggiungi criteri** per aprire Crea criterio.

1. Digitare il nome **Internal requestor policy** (criterio richiedente interno) e la descrizione **Allows users in this directory to request access to web project resources** (Consente agli utenti in questa directory di richiedere l'accesso alle risorse del progetto Web).

1. Nella sezione **Utenti che possono richiedere l'accesso** fare clic su **Per gli utenti nella directory**.

    ![Crea criteri](./media/entitlement-management-access-package-first/policy-create.png)

1. Scorrere verso il basso fino alla sezione **Seleziona utenti e gruppi** e fare clic su **Aggiungi utenti e gruppi**.

1. Nel riquadro Seleziona utenti e gruppi selezionare l'utente **Requestor1** creato in precedenza e quindi fare clic su **Seleziona**.

1. Nella sezione **Richiesta** impostare **Richiedi approvazione** su **Sì**.

1. Nella sezione **Seleziona responsabili approvazione** fare clic su **Aggiungi responsabili approvazione**.

1. Nel riquadro Seleziona responsabili approvazione selezionare l'utente **Admin1** creato in precedenza e quindi fare clic su **Seleziona**.

    In questa esercitazione l'amministratore e il responsabile approvazione sono la stessa persona, ma possibile designare un'altra persona come responsabile approvazione.

1. Nella sezione **Scadenza** impostare**Access package expires** (Scadenza pacchetto di accesso) su **Numero di giorni**.

1. Impostare **Access expires after** (L'accesso scade dopo) su **30** giorni.

1. Per **Attiva criterio** fare clic su **Sì**.

    ![Impostazioni di creazione dei criteri](./media/entitlement-management-access-package-first/policy-create-settings.png)

1. Fare clic su **Crea** per creare i **criteri per il richiedente interno**.

1. Nel menu a sinistra del pacchetto di accesso del progetto Web fare clic su **Panoramica**.

1. Copiare il **collegamento del portale di Accesso personale**.

    Questo collegamento verrà usato nel passaggio successivo.

    ![Panoramica del pacchetto di accesso - Collegamento del portale di Accesso personale](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-4-request-access"></a>Passaggio 4: Richiedere l'accesso

In questo passaggio le procedure verranno eseguite come **richiedente interno** e verrà richiesto l'accesso al pacchetto di accesso. I richiedenti inviano le richieste tramite un sito chiamato portale di Accesso personale. Il portale di Accesso personale consente ai richiedenti di inviare le richieste per i pacchetti di accesso, visualizzare i pacchetti di accesso ai quali sono già autorizzati ad accedere e visualizzare la cronologia delle richieste.

**Ruolo prerequisito:** Richiedente interno

1. Disconnettersi dal portale di Azure.

1. In una nuova finestra del browser passare al collegamento del portale di Accesso personale copiato nel passaggio precedente.

1. Eseguire l'accesso al portale di Accesso personale come **Requestor1**.

    Dovrebbe essere presente il **pacchetto di accesso progetto Web**.

1. Se necessario, nella colonna **Descrizione** fare clic sulla freccia per visualizzare i dettagli relativi al pacchetto di accesso.

    ![Portale di Accesso personale - Pacchetti di accesso](./media/entitlement-management-shared/my-access-access-packages.png)

1. Fare clic sul segno di spunta per selezionare il pacchetto.

1. Fare clic su **Richiedi accesso** per aprire il riquadro Richiedi accesso.

1. Nella casella **Motivazioni aziendali** digitare la motivazione **Collaborazione al progetto Web**.

1. Impostare l'opzione **Request for specific period** (Richiedi per un periodo specifico) su **Sì**.

1. Impostare la **Data di inizio** su oggi e la **Data di fine** su domani.

    ![Portale di Accesso personale - Richiedi accesso](./media/entitlement-management-shared/my-access-request-access.png)

1. Fare clic su **Submit**.

1. Nel menu a sinistra fare clic su **Cronologia delle richieste** per verificare che la richiesta sia stata inviata.

## <a name="step-5-approve-access-request"></a>Passaggio 5: Approvare la richiesta di accesso

In questo passaggio verrà eseguito l'accesso come utente **responsabile approvazione** e verrà approvata la richiesta di accesso per il richiedente interno. I responsabili approvazione usano lo stesso portale di Accesso personale usato dai richiedenti per inviare le richieste. Tramite il portale di Accesso personale, i responsabili approvazione possono visualizzare le approvazioni in sospeso e approvare o rifiutare le richieste.

**Ruolo prerequisito:** Responsabile approvazione

1. Disconnettersi dal portale di Accesso personale.

1. Eseguire l'accesso al [portale di Accesso personale](https://myaccess.microsoft.com) come **Admin1**.

1. Nel menu a sinistra fare clic su **Approvazioni**.

1. Nella scheda **In sospeso** trovare **Requestor1**.

    Se la richiesta di Requestor1 non è visualizzata, attendere alcuni minuti e riprovare.

1. Fare clic sul collegamento **Visualizza** per aprire il riquadro Richiesta di accesso.

1. Fare clic su **Approve** (Approva).

1. Nella casella **Motivo** digitare il motivo **Accesso approvato per il progetto Web**.

    ![Portale di Accesso personale - Richiesta di accesso](./media/entitlement-management-shared/my-access-approve-request.png)

1. Fare clic su **Invia** per inviare la decisione.

    Verrà visualizzato un messaggio indicante che la richiesta è stata approvata.

## <a name="step-6-validate-that-access-has-been-assigned"></a>Passaggio 6: Confermare l'assegnazione dell'accesso

Dopo aver approvato la richiesta di accesso, in questo passaggio si confermerà che il pacchetto di accesso è stato assegnato al **richiedente interno** che ora è membro del gruppo **Engineering Group**.

**Ruolo prerequisito:** Amministratore globale o Amministratore utenti

1. Disconnettersi dal portale di Accesso personale.

1. Accedere al [portale di Azure](https://portal.azure.com) come **Admin1**.

1. Fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Pacchetti di accesso**.

1. Trovare il **Pacchetto di accesso progetto Web** e selezionarlo.

1. Nel menu a sinistra fare clic su **Richieste**.

    Lo stato di Requestor1 e dei criteri per il richiedente interno mostrerà **Recapitati**.

1. Fare clic sulla richiesta per visualizzare i dettagli della richiesta.

    ![Pacchetto di accesso - Dettagli richiesta](./media/entitlement-management-access-package-first/request-details.png)

1. Nel menu di spostamento a sinistra fare clic su **Azure Active Directory**.

1. Fare clic su **Gruppi** e aprire il gruppo **Engineering Group**.

1. Fare clic su **Membri**.

    Nell'elenco dei membri verrà visualizzato anche **Requestor1**.

    ![Membri del gruppo Engineering](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-7-clean-up-resources"></a>Passaggio 7: Pulire le risorse

In questo passaggio verranno rimosse le modifiche apportate e verrà eliminato il pacchetto di accesso **Pacchetto di accesso progetto Web**.

**Ruolo prerequisito:**  Amministratore globale o Amministratore utenti

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Aprire il **Pacchetto di accesso progetto Web**.

1. Fare clic su **Assegnazioni**.

1. Per **Requestor1**, fare clic sui puntini di sospensione ( **...** ) e quindi su **Rimuovi accesso**.

    Lo stato passerà da Recapitato a Scaduto.

1. Fare clic su **Criteri**.

1. Per **Criteri richiedente interno**, fare clic sui puntini di sospensione ( **...** ) e quindi su **Elimina**.

1. Fare clic su **Ruoli delle risorse**.

1. Per **Engineering Group** fare clic sui puntini di sospensione ( **...** ) e quindi su **Remove resource role** (Rimuovi ruolo risorsa).

1. Aprire l'elenco dei pacchetti di accesso.

1. Per **Pacchetto di accesso progetto Web**, fare clic sui puntini di sospensione ( **...** ) e quindi su **Elimina**.

1. In Azure Active Directory eliminare gli utenti creati, ad esempio **Requestor1** e **Admin1**.

1. Eliminare il gruppo **Engineering Group**.

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo per informazioni sulle procedure degli scenari comuni in Gestione entitlement.
> [!div class="nextstepaction"]
> [Scenari comuni](entitlement-management-scenarios.md)
