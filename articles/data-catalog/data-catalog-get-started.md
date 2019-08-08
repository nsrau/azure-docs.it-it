---
title: Creare un'istanza di Azure Data Catalog
description: Guida di avvio rapido alla creazione di un'istanza di Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: quickstart
ms.date: 08/01/2019
ms.openlocfilehash: df2f536dc95dd74dbae1c8bda1a9934a73fd1fe0
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736485"
---
# <a name="quickstart-create-an-azure-data-catalog"></a>Guida introduttiva: Creare un'istanza di Azure Data Catalog

Azure Data Catalog è un servizio cloud completamente gestito che funge da sistema di registrazione e di individuazione per asset di dati aziendali. Per una panoramica dettagliata, vedere [Definizione di Azure Data Catalog](overview.md).

Questa guida di avvio rapido illustra come creare un'istanza di Azure Data Catalog.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare è necessario:

* Una sottoscrizione di [Microsoft Azure](https://azure.microsoft.com/).
* È necessario avere il proprio [tenant di Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

Per configurare Data Catalog, l'utente deve essere proprietario o comproprietario di una sottoscrizione di Azure.

## <a name="create-a-data-catalog"></a>Creare un catalogo dati

È possibile effettuare il provisioning di un solo catalogo dati per organizzazione (dominio di Azure Active Directory). Se quindi il proprietario o il comproprietario di una sottoscrizione di Azure che appartiene a questo dominio di Azure Active Directory ha già creato un catalogo, non potrà crearne un altro anche se ha più sottoscrizioni di Azure. Per testare se un utente ha creato un catalogo dati nel dominio di Azure Active Directory, passare alla [home page di Azure Data Catalog](http://azuredatacatalog.com) e verificare se il catalogo è visualizzato. Se è già stato creato un catalogo, ignorare la procedura seguente e passare alla sezione successiva.

1. Passare al [portale di Azure](https://portal.azure.com) > **Crea una risorsa** e selezionare **Data Catalog**.

    ![Creare un'istanza di Data Catalog](media/data-catalog-get-started/data-catalog-create.png)

2. Specificare un **nome** per il catalogo, la **sottoscrizione** che si vuole usare, la **posizione** del catalogo e il **piano tariffario**. Selezionare quindi **Crea**.

3. Passare alla [home page di Azure Data Catalog](http://azuredatacatalog.com) e fare clic su **Pubblica dati**.

   ![Azure Data Catalog - Pulsante Pubblica dati](media/data-catalog-get-started/data-catalog-publish-data.png)

   È anche possibile accedere alla home page di Data Catalog dalla [pagina del servizio Data Catalog](https://azure.microsoft.com/services/data-catalog) selezionando **Introduzione**.

   ![Azure Data Catalog - Pagina di destinazione di marketing](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)

4. Passare alla pagina **Impostazioni**.

    ![Azure Data Catalog - Effettuare il provisioning del catalogo dati](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)

5. Espandere **Prezzi** e selezionare l'**edizione** di Azure Data Catalog (Gratuito o Standard).

    ![Azure Data Catalog: selezionare l'edizione](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)

6. Se si sceglie *Standard* come piano tariffario, è possibile espandere **Gruppi di sicurezza** e abilitare l'autorizzazione dei gruppi di sicurezza di Active Directory per accedere a Data Catalog e abilitare l'adeguamento automatico della fatturazione.

    ![Gruppi di sicurezza di Azure Data Catalog](media/data-catalog-get-started/data-catalog-standard-security-groups.png)

7. Espandere **Utenti del catalogo** e fare clic su **Aggiungi** per aggiungere utenti per il catalogo dati. L'utente verrà aggiunto automaticamente a questo gruppo.

    ![Azure Data Catalog: utenti](media/data-catalog-get-started/data-catalog-add-catalog-user.png)

8. Se si sceglie *Standard* come piano tariffario, è possibile espandere **Amministratori del glossario** e fare clic su **Aggiungi** per aggiungere utenti amministratori del glossario. L'utente verrà aggiunto automaticamente a questo gruppo.

    ![Amministratori del glossario di Azure Data Catalog](media/data-catalog-get-started/data-catalog-standard-glossary-admin.png)

9. Espandere **Amministratori del catalogo** e fare clic su **Aggiungi** per aggiungere altri amministratori per il catalogo dati. L'utente verrà aggiunto automaticamente a questo gruppo.

    ![Azure Data Catalog: amministratori](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)

10. Espandere **Titolo del portale** e aggiungere ulteriore testo che verrà visualizzato nel titolo del portale.

    ![Titolo del portale di Azure Data Catalog](media/data-catalog-get-started/data-catalog-portal-title.png)

11. Dopo aver completato la pagina **Impostazioni**, passare alla pagina **Pubblica**.

    ![Azure Data Catalog: creazione completata](media/data-catalog-get-started/data-catalog-created.png)

## <a name="find-a-data-catalog-in-the-azure-portal"></a>Trovare un catalogo dati nel portale di Azure

1. In una scheda o una finestra separata del Web browser passare al [portale di Azure](https://portal.azure.com) e accedere con lo stesso account usato per creare il catalogo dati nel passaggio precedente.

2. Selezionare **Tutti i servizi** e quindi fare clic su **Data Catalog**.

    ![Azure Data Catalog - Esplorare Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png)

    Verrà visualizzato il catalogo dati creato.

    ![Azure Data Catalog - Visualizzare il catalogo nell'elenco](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)

3. Fare clic sul catalogo creato. Nel portale verrà visualizzato il pannello **Catalogo dati** .

   ![Azure Data Catalog - Pannello nel portale](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)

4. È possibile visualizzare le proprietà del catalogo dati e aggiornarle. Fare ad esempio clic su **Piano tariffario** e modificare l'edizione.

    ![Azure Data Catalog - Piano tariffario](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come creare un'istanza di Azure Data Catalog per l'organizzazione. È ora possibile registrare le origini dati nel catalogo dati.

> [!div class="nextstepaction"]
> [Registrare le origini dati in Azure Data Catalog](data-catalog-how-to-register.md)
