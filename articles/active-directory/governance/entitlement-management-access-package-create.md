---
title: Creare un nuovo pacchetto di accesso in Azure AD gestione dei diritti (anteprima)-Azure Active Directory
description: Informazioni su come creare un nuovo pacchetto di risorse di accesso che si vuole condividere in Azure Active Directory gestione dei diritti (anteprima).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f033cf57c5a285e94372728677c91e021065fa9
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678208"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management-preview"></a>Creare un nuovo pacchetto di accesso in Azure AD gestione dei diritti (anteprima)

> [!IMPORTANT]
> Gestione entitlement di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Un pacchetto di accesso consente di eseguire una singola installazione di risorse e criteri che amministra automaticamente l'accesso per la durata del pacchetto di accesso. Questo articolo descrive come creare un nuovo pacchetto di accesso.

## <a name="overview"></a>Panoramica

Tutti i pacchetti di accesso devono essere inseriti in un contenitore denominato catalogo. Un catalogo consente di definire le risorse che è possibile aggiungere al pacchetto di accesso. Se non si specifica un catalogo, il pacchetto di accesso verrà inserito nel catalogo generale. Attualmente non è possibile spostare un pacchetto di accesso esistente in un catalogo diverso.

Tutti i pacchetti di accesso devono avere almeno un criterio. I criteri specificano chi può richiedere il pacchetto di accesso e anche le impostazioni di approvazione e scadenza. Quando si crea un nuovo pacchetto di accesso, è possibile creare un criterio iniziale per gli utenti nella directory, per gli utenti non inclusi nella directory, solo per le assegnazioni dirette all'amministratore oppure è possibile scegliere di creare il criterio in un secondo momento.

Il diagramma seguente illustra il processo di alto livello per la creazione di un nuovo pacchetto di accesso.

![Creazione di un processo di pacchetto di accesso](./media/entitlement-management-access-package-create/access-package-process.png)

## <a name="start-new-access-package"></a>Avvia nuovo pacchetto di accesso

**Ruolo prerequisito:** Amministratore globale, amministratore utente o proprietario del catalogo

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Pacchetti di accesso**.

    ![Gestione entitlement nel portale di Azure](./media/entitlement-management-shared/elm-access-packages.png)

1. Fare clic su **Nuovo pacchetto di accesso**.

## <a name="basics"></a>Generale

Nella scheda **nozioni di base** assegnare un nome al pacchetto di accesso e specificare il catalogo in cui creare il pacchetto di accesso.

1. Immettere un nome visualizzato e una descrizione per il pacchetto di accesso. Queste informazioni verranno visualizzate dagli utenti quando inviano una richiesta per il pacchetto di accesso.

1. Nell'elenco a discesa **Catalogo** selezionare il catalogo in cui si vuole creare il pacchetto di accesso. Ad esempio, si potrebbe avere un proprietario del catalogo che gestisce tutte le risorse di marketing che possono essere richieste. In questo caso, è possibile selezionare il catalogo marketing.

    Vengono visualizzati solo i cataloghi per i quali si dispone dell'autorizzazione per creare pacchetti di accesso in. Per creare un pacchetto di accesso in un catalogo esistente, è necessario essere almeno un amministratore globale, un amministratore utente, un proprietario del catalogo nel catalogo oppure accedere a gestione pacchetti in tale catalogo.

    ![Accedere a Package-nozioni di base](./media/entitlement-management-access-package-create/basics.png)

    Se si è un amministratore globale o un amministratore utente e si desidera creare il pacchetto di accesso in un nuovo catalogo non elencato, fare clic su **Crea nuovo**. Immettere il nome del catalogo e la descrizione, quindi fare clic su **Crea**.

    Il pacchetto di accesso che si sta creando e le eventuali risorse incluse verranno aggiunte al nuovo catalogo. È anche possibile aggiungere altri proprietari del catalogo in un secondo momento.


1. Fare clic su **Avanti**.

## <a name="resource-roles"></a>Ruoli delle risorse

Nella scheda **ruoli risorsa** selezionare le risorse da includere nel pacchetto di accesso.  Gli utenti che richiedono e ricevono il pacchetto di accesso riceveranno tutti i ruoli delle risorse nel pacchetto di accesso.

1. Fare clic sul tipo di risorsa che si desidera aggiungere (**gruppi**, **applicazioni**o **siti di SharePoint**).

1. Nel riquadro Seleziona visualizzato selezionare una o più risorse dall'elenco.

    ![Accedere ai ruoli delle risorse del pacchetto](./media/entitlement-management-access-package-create/resource-roles.png)

    Se si sta creando il pacchetto di accesso nel catalogo generale o in un nuovo catalogo, sarà possibile scegliere qualsiasi risorsa dalla directory di cui si è proprietari. È necessario essere almeno un amministratore globale, un amministratore utente o un creatore del catalogo.

    Se si sta creando il pacchetto di accesso in un catalogo esistente, è possibile selezionare qualsiasi risorsa già presente nel catalogo senza possederla.

    Se si è un amministratore globale, un amministratore utente o un proprietario del catalogo, è possibile selezionare le risorse di cui si è proprietari e che non sono ancora presenti nel catalogo. Se si selezionano risorse non presenti nel catalogo selezionato, queste risorse verranno aggiunte anche al catalogo per consentire ad altri amministratori del catalogo di compilare pacchetti di accesso con. Se si desidera solo selezionare le risorse attualmente presenti nel catalogo selezionato, selezionare la casella di controllo **Visualizza solo** nella parte superiore del riquadro di selezione.

1. Dopo aver selezionato le risorse, nell'elenco **ruolo** selezionare il ruolo che si desidera assegnare agli utenti per la risorsa.

    ![Accedere al pacchetto: selezione del ruolo delle risorse](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Fare clic su **Avanti**.

## <a name="policy"></a>Criteri

Nella scheda **criteri** si crea il primo criterio per specificare gli utenti che possono richiedere il pacchetto di accesso e anche le impostazioni di approvazione e scadenza. Successivamente, è possibile creare più criteri per consentire a gruppi aggiuntivi di utenti di richiedere il pacchetto di accesso con le proprie impostazioni di approvazione e scadenza. È anche possibile scegliere di creare il criterio in un secondo momento.

1. Impostare l'interruttore **Crea primo criterio** su **ora** o **versione successiva**.

    ![Criteri di accesso ai pacchetti](./media/entitlement-management-access-package-create/policy.png)

1. Se si seleziona in **seguito**, passare alla sezione [Review + create](#review--create) per creare il pacchetto di accesso.

1. Se si seleziona **ora**, eseguire i passaggi in una delle sezioni seguenti dei criteri.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="review--create"></a>Rivedi e crea

Nella scheda **Verifica e crea** è possibile esaminare le impostazioni e verificare la presenza di eventuali errori di convalida.

1. Esaminare le impostazioni del pacchetto di accesso

    ![Pacchetto di accesso-criteri-Abilita impostazione dei criteri](./media/entitlement-management-access-package-create/review-create.png)

1. Fare clic su **Crea** per creare il pacchetto di accesso.

    Il nuovo pacchetto di accesso verrà visualizzato nell'elenco dei pacchetti di accesso.

## <a name="next-steps"></a>Passaggi successivi

- [Modificare e gestire un pacchetto di accesso esistente](entitlement-management-access-package-edit.md)
- [Aggiungere un proprietario del catalogo o una gestione pacchetti di accesso](entitlement-management-delegate.md#add-a-catalog-owner-or-an-access-package-manager)
- [Creare e gestire un catalogo](entitlement-management-catalog-create.md)
