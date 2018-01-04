---
title: Pubblicare un'applicazione gestita di Azure tramite il portale | Microsoft Docs
description: Questo articolo descrive come usare il portale di Azure per creare un'applicazione gestita di Azure studiata per i membri della propria organizzazione.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: 764eb479733a7d4acdb6e6c3eee721cb4a161c88
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2017
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>Pubblicare un'applicazione del catalogo di servizi tramite il portale di Azure

È possibile usare il portale di Azure per pubblicare [applicazioni gestite](overview.md) studiate per i membri della propria organizzazione. Un reparto IT può, ad esempio, pubblicare applicazioni gestite che garantiscano la conformità agli standard aziendali. Queste applicazioni gestite sono disponibili nel catalogo dei servizi, non in Azure Marketplace.

## <a name="prerequisites"></a>Prerequisiti

Quando si pubblica un'applicazione gestita, si specifica un'identità per la gestione delle risorse. È consigliabile specificare un gruppo di utenti Azure Active Directory. Per creare un gruppo di utenti Azure Active Directory, vedere [Creare un gruppo e aggiungere membri in Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md). 

Il file ZIP che contiene la definizione di applicazione gestita deve essere disponibile tramite un URI. È consigliabile caricare il file ZIP in un BLOB di archiviazione. 

## <a name="create-managed-application-with-portal"></a>Creare l'applicazione gestita con il portale

1. Nell'angolo in alto a sinistra selezionare **+ Nuovo**.

   ![Nuovo servizio](./media/publish-portal/new.png)

1. Cercare il **catalogo di servizi**.

1. Nei risultati scorrere fino alla **definizione di applicazione gestita del catalogo di servizi**. Selezionarlo.

   ![Ricercare le definizioni di applicazioni gestite](./media/publish-portal/select-managed-apps-definition.png)

1. Selezionare **Crea** per avviare il processo di creazione della definizione di applicazione gestita.

   ![Creare la definizione di applicazione gestita](./media/publish-portal/create-definition.png)

1. Fornire valori per il nome, il nome visualizzato, la descrizione, la località, le sottoscrizioni e il gruppo di risorse. Per l'URI del file di pacchetto specificare il percorso del file con estensione zip creato.

   ![Fornire i valori](./media/publish-portal/fill-application-values.png)

1. Quando si arriva alla sezione Authentication and Lock Level (Autenticazione e livello di blocco) selezionare **Add Authorization** (Aggiungi autorizzazione).

   ![Aggiungere l'autorizzazione](./media/publish-portal/add-authorization.png)

1. Selezionare un gruppo di Azure Active Directory per gestire le risorse e quindi **OK**.

   ![Aggiungere il gruppo di autorizzazioni](./media/publish-portal/add-auth-group.png)

1. Dopo avere fornito tutti i valori, selezionare **Crea**.

   ![Creare l'applicazione gestita](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alle applicazioni gestite, vedere [Panoramica delle applicazioni gestite di Azure](overview.md).
* Per esempi di applicazioni gestite, vedere [Progetti di esempio per applicazioni gestite di Azure](sample-projects.md).
* Per informazioni sulla creazione di un file di definizione dell'interfaccia utente per un'applicazione gestita, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).