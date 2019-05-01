---
title: Creare un nuovo pacchetto di accesso nella gestione dei diritti di Azure AD (anteprima) - Azure Active Directory
description: Informazioni su come creare un nuovo pacchetto di accesso delle risorse che si desidera condividere in Gestione dei diritti di Azure Active Directory (anteprima).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/24/2019
ms.author: rolyon
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ad6570a3f30e40e4074502a8ce85bf739f58d3f
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866425"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management-preview"></a>Creare un nuovo pacchetto di accesso nella gestione dei diritti di Azure AD (anteprima)

> [!IMPORTANT]
> Gestione dei diritti di Azure Active Directory (Azure AD) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Un pacchetto di accesso consente di eseguire un'attività una tantum dei criteri e le risorse che gestisce automaticamente l'accesso per il ciclo di vita del pacchetto di accesso. Questo articolo descrive come creare un nuovo pacchetto di accesso.

## <a name="overview"></a>Panoramica

Tutti i pacchetti di accesso devono essere inseriti in un contenitore chiamato un catalogo. Un catalogo consente di definire le risorse a cui è possibile aggiungere il pacchetto di accesso. Se non si specifica un catalogo, il pacchetto di accesso viene inserito nel catalogo generale. Attualmente, è possibile spostare un pacchetto di accesso esistente in un catalogo diverso.

Tutti i pacchetti di accesso devono avere almeno un criterio. I criteri di specificano che possono richiedere il pacchetto di accesso e anche le impostazioni di approvazione e scadenza. Quando si crea un nuovo pacchetto di accesso, è possibile creare un criterio iniziale per gli utenti nella directory, per gli utenti non nella directory, solo, assegnazioni dirette amministratore o è possibile scegliere di creare i criteri in un secondo momento.

Il diagramma seguente illustra il processo generale per creare un nuovo pacchetto di accesso.

![Creare un processo del pacchetto di accesso](./media/entitlement-management-access-package-create/access-package-process.png)

## <a name="start-new-access-package"></a>Avvia nuovo pacchetto di accesso

**Ruolo prerequisiti:** L'utente amministratore o proprietario catalogo

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Fare clic su **Azure Active Directory** e quindi fare clic su **governance delle identità**.

1. Nel menu a sinistra, fare clic su **l'accesso ai pacchetti**.

    ![Gestione Entitlement nel portale di Azure](./media/entitlement-management-shared/elm-access-packages.png)

1. Fare clic su **nuovo pacchetto di accesso**.

## <a name="basics"></a>Nozioni di base

Nel **nozioni di base** scheda, assegnare un nome al pacchetto di accesso e specificare quale catalogo per creare il pacchetto di accesso in.

1. Immettere un nome visualizzato e una descrizione per il pacchetto di accesso. Gli utenti vedranno queste informazioni quando si invia una richiesta per il pacchetto di accesso.

1. Nel **catalogo** -elenco a discesa, seleziona il catalogo a cui si vuole creare l'accesso del pacchetto in. Ad esempio, potrebbe essere un proprietario del catalogo che gestisce tutte le risorse di marketing che possono essere richieste. In questo caso, è possibile selezionare il catalogo di marketing.

    Verranno visualizzati solo i cataloghi si è autorizzati a creare pacchetti di accesso in. Per creare il pacchetto di accesso in un catalogo esistente, è necessario essere almeno un utente amministratore, proprietario del catalogo o Gestione pacchetti di accesso.

    ![Pacchetto di accesso - nozioni di base](./media/entitlement-management-access-package-create/basics.png)

    Se si desidera creare il pacchetto di accesso in un nuovo catalogo, fare clic su **Crea nuovo**. Immettere il nome del catalogo e la descrizione e quindi fare clic su **Create**.

    Il pacchetto di accesso che si sta creando e tutte le risorse incluse in essa verranno aggiunti al nuovo catalogo. Inoltre, verrà portato automaticamente il primo proprietario del catalogo. È possibile aggiungere i proprietari del catalogo aggiuntivi.

    Per creare un nuovo catalogo, è necessario essere almeno un utente amministratore o creatore del catalogo.

1. Fare clic su **Avanti**.

## <a name="resource-roles"></a>Ruoli delle risorse

Nel **i ruoli delle risorse** scheda, si seleziona le risorse da includere nel pacchetto di accesso.

1. Fare clic sul tipo di risorsa da aggiungere (**gruppi**, **Applications**, o **siti SharePoint**).

1. Nel riquadro di selezione che viene visualizzato, selezionare una o più risorse dall'elenco.

    ![Pacchetto di accesso - ruoli delle risorse](./media/entitlement-management-access-package-create/resource-roles.png)

    Se si sta creando il pacchetto di accesso in un nuovo catalogo o il catalogo generale, sarà possibile selezionare qualsiasi risorsa dalla directory in cui si è proprietari. È necessario essere almeno un utente amministratore o creatore del catalogo.

    Se si sta creando il pacchetto di accesso in un catalogo esistente, è possibile selezionare qualsiasi risorsa che è già presente nel catalogo senza essere il proprietario.

    Se sei un utente amministratore o proprietario del catalogo, è necessario l'opzione aggiuntiva di selezione delle risorse si è proprietari che non sono ancora presenti nel catalogo. Se si seleziona le risorse non è attualmente nel catalogo selezionato, verranno aggiunto anche queste risorse per il catalogo per gli altri amministratori del catalogo creare pacchetti di accesso con. Se si desidera selezionare le risorse che sono attualmente nel catalogo selezionato, verificare i **ricadrebbero** casella di controllo nella parte superiore del pan selezionare.

1. Dopo aver selezionato le risorse, nelle **ruolo** elencare, selezionare il ruolo che si desidera che gli utenti per l'assegnazione per la risorsa.

    ![Pacchetto di accesso - selezione del ruolo di risorse](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Fare clic su **Avanti**.

## <a name="policy"></a>Policy

Nel **criteri** scheda, si crea il primo criterio per specificare che possono richiedere il pacchetto di accesso e anche le impostazioni di approvazione e scadenza. In un secondo momento, è possibile creare più criteri per consentire altri gruppi di utenti di richiedere il pacchetto di accesso con le proprie impostazioni di approvazione e scadenza. È anche possibile scegliere creare i criteri in un secondo momento.

1. Impostare il **creare i criteri prima** passare alla **ora** o **Later**.

    ![Package - criteri di accesso](./media/entitlement-management-access-package-create/policy.png)

1. Se si seleziona **Later**, passare alle [revisione + Crea](#review--create) sezione per creare il pacchetto di accesso.

1. Se si seleziona **ora**, eseguire i passaggi in una delle seguenti sezioni dei criteri.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="review--create"></a>Rivedi e crea

Nel **esaminare + crea** scheda, è possibile esaminare le impostazioni e cercare eventuali errori di convalida.

1. Esaminare le impostazioni del pacchetto di accesso

    ![Pacchetto di accesso - impostazione dei criteri di abilitazione criteri](./media/entitlement-management-access-package-create/review-create.png)

1. Fare clic su **Create** per creare il pacchetto di accesso.

    Il nuovo pacchetto di accesso viene visualizzata nell'elenco dei pacchetti di accesso.

## <a name="next-steps"></a>Passaggi successivi

- [Modificare e gestire un pacchetto di accesso esistente](entitlement-management-access-package-edit.md)
- [Creare e gestire un catalogo](entitlement-management-catalog-create.md)
