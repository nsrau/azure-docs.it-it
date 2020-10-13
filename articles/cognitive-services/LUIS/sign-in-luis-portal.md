---
title: Accedere al portale LUIS
description: Se si è un nuovo utente che accede al portale LUIS, l'esperienza di accesso sarà leggermente diversa in base all'account utente corrente.
services: cognitive-services
ms.custom: ''
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/08/2020
ms.topic: how-to
ms.author: a-sakand
author: skandil
ms.openlocfilehash: 22f5ab332c52a3b567b37089def6e4a494a15d29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91309473"
---
# <a name="sign-in-to-luis-portal"></a>Accedere al portale LUIS

Se si è un nuovo utente che accede al portale LUIS, l'esperienza di accesso sarà leggermente diversa in base all'account utente corrente:
  * Associato a una sottoscrizione di Azure
  * Non associato a una sottoscrizione di Azure

## <a name="determine-account-type"></a>Determinare il tipo di account

Quando si accede per la prima volta al portale LUIS, usare gli indicatori visivi seguenti per determinare il tipo di conto.

### <a name="account-without-azure-subscription"></a>Account senza sottoscrizione di Azure

Un account, che non è associato a una sottoscrizione di Azure, dispone dell'icona di Azure nella barra di spostamento in alto a destra. Quando si esegue la migrazione al tipo di account associato, l'icona non viene più visualizzata.

:::image type="content" source="media/sign-in/sign-in-with-account-without-azure-subscription.png" alt-text="Screenshot parziale della barra di spostamento LUIS con l'icona di Azure.":::

### <a name="account-with-azure-subscription"></a>Account con sottoscrizione di Azure

Un account associato a una sottoscrizione di Azure consente di selezionare la sottoscrizione e la risorsa da usare.

:::image type="content" source="media/sign-in/resource-selection.png" alt-text="Screenshot parziale della barra di spostamento LUIS con l'icona di Azure.":::

## <a name="sign-in-with-account-associated-with-an-azure-subscription"></a>Accedere con l'account associato a una sottoscrizione di Azure

1. Accedere a [Luis Portal](https://www.luis.ai) e accettare le condizioni per l'utilizzo.

1. Sono disponibili due opzioni per l'iscrizione:

    * Continuare a usare una risorsa di Azure, ovvero il percorso consigliato e presto sarà l'unico percorso disponibile. Questo percorso consente di collegare l'account LUIS a una risorsa di creazione di Azure durante l'iscrizione scegliendo una risorsa esistente nella sottoscrizione o creando una nuova risorsa. Questa operazione equivale all'iscrizione migrata senza la necessità di eseguire il processo di [migrazione](luis-migration-authoring.md#what-is-migration) in un secondo momento. A tutti gli utenti verrà richiesto di eseguire la migrazione entro il 2 novembre 2020.

    * Continuare a usare la chiave di avvio o di valutazione. Questo percorso consente di accedere a LUIS con lo Starter o la risorsa di valutazione fornita senza dover creare risorse. Se si sceglie questo percorso, alla fine verrà richiesto di [eseguire la migrazione dell'account](luis-migration-authoring.md#migration-steps) e di collegare le applicazioni a una risorsa di creazione. Per questo motivo, è consigliabile scegliere il percorso in cui si continua con la risorsa di Azure.

    [Altre informazioni sulle chiavi di creazione e di avvio](luis-how-to-azure-subscription.md#luis-resources). Entrambe le risorse offrono 1 milione transazioni di creazione gratuite e 1000 transazioni dell'endpoint di stima gratuite.

    :::image type="content" source="media/sign-in/signup-landing-page.png" alt-text="Screenshot parziale della barra di spostamento LUIS con l'icona di Azure.":::

1. Usa una risorsa di creazione esistente

    :::image type="content" source="media/sign-in/signup-choose-resource.png" alt-text="Screenshot parziale della barra di spostamento LUIS con l'icona di Azure.":::

    Se nella sottoscrizione sono già presenti risorse per la creazione di LUIS e si associa una con l'account LUIS durante l'accesso, scegliere l'opzione **Usa la risorsa di creazione esistente** e specificare le informazioni seguenti:

    * **Tenant**: tenant a cui è associata la sottoscrizione di Azure. Non sarà possibile cambiare i tenant dalla finestra esistente. È possibile cambiare i tenant selezionando l'avatar più a destra, che contiene le iniziali nella barra superiore.
    * **Nome sottoscrizione** : la sottoscrizione che verrà associata alla risorsa. Se si dispone di più di una sottoscrizione appartenente al tenant, selezionare quella desiderata dall'elenco a discesa.
    * **Nome risorsa** : la risorsa di creazione a cui si vuole associare l'account.

    > [!Note]
    > Se la risorsa di creazione che si sta cercando è disattivata nell'elenco a discesa, significa che è stato effettuato l'accesso a un altro portale regionale. [Comprendere il concetto di portali regionali](luis-reference-regions.md#luis-authoring-regions).

1. Creare una nuova risorsa di creazione

    :::image type="content" source="media/sign-in/signup-create-resource.png" alt-text="Screenshot parziale della barra di spostamento LUIS con l'icona di Azure.":::

    Durante la **creazione di una nuova risorsa di creazione** specificare le informazioni seguenti:

    * **Tenant**: tenant a cui è associata la sottoscrizione di Azure. Non sarà possibile cambiare i tenant dalla finestra esistente. È possibile cambiare i tenant selezionando l'avatar più a destra, che contiene le iniziali nella barra superiore.
    * **Nome risorsa** : nome personalizzato scelto, usato come parte dell'URL per le transazioni di creazione. Il nome della risorsa può includere solo caratteri alfanumerici,'-' e non può iniziare o terminare con '-'. Se nel nome sono inclusi altri simboli, la creazione di una risorsa avrà esito negativo.
    * **Nome sottoscrizione** : la sottoscrizione che verrà associata alla risorsa. Se si dispone di più di una sottoscrizione appartenente al tenant, selezionare quella desiderata dall'elenco a discesa.
    * **Gruppo di risorse** : nome del gruppo di risorse personalizzato scelto nella sottoscrizione. I gruppi di risorse consentono di raggruppare le risorse di Azure per l'accesso e la gestione. Se non si dispone attualmente di un gruppo di risorse nella sottoscrizione, non sarà possibile crearne uno nel portale LUIS. Passare a [portale di Azure](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) per crearne uno, quindi passare a Luis per continuare il processo di accesso.

1. Dopo aver scelto il percorso, l'utente potrebbe richiedere alcuni secondi finché non viene visualizzato un segno che indica che la migrazione dell'account è stata completata. Per completare la selezione, **continuare**.

    :::image type="content" source="media/sign-in/signup-confirm-2.png" alt-text="Screenshot parziale della barra di spostamento LUIS con l'icona di Azure.":::

    > [!Note]
    > Se si dispone di una sottoscrizione e di almeno una risorsa di creazione nell'area corrispondente a quella a cui si sta effettuando l'iscrizione nel portale, è possibile accedere automaticamente a LUIS migrato e associato a una risorsa senza dover scegliere il percorso da usare.


## <a name="sign-in-with-user-account-not-associated-with-an-azure-subscription"></a>Accedere con un account utente non associato a una sottoscrizione di Azure

1. Accedere al [portale di Luis](https://www.luis.ai) e verificare di accettare le condizioni per l'utilizzo.

1. Per completare la selezione, **continuare**. Si accederà automaticamente con una chiave di prova/Starter. Ciò significa che alla fine verrà richiesto di [eseguire la migrazione dell'account](luis-migration-authoring.md#migration-steps) e di collegare le applicazioni a una risorsa di creazione. Per eseguire il processo di migrazione, sarà necessario accedere a una versione di [valutazione gratuita di Azure](https://azure.microsoft.com/free/).

    :::image type="content" source="media/sign-in/signup-no-subscription.png" alt-text="Screenshot parziale della barra di spostamento LUIS con l'icona di Azure.":::

## <a name="troubleshooting"></a>Risoluzione dei problemi

* Se si crea una risorsa di creazione dalla portale di Azure in un'area diversa da quella del portale a cui si accede, la risorsa di creazione sarà disattivata.
* Quando si crea una nuova risorsa, verificare che il nome della risorsa includa solo caratteri alfanumerici,'-' e non può iniziare o terminare con '-'. In caso contrario, l'operazione non riesce.
* Assicurarsi di disporre delle [autorizzazioni appropriate per la sottoscrizione per creare una risorsa di Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles). Se non si dispone delle autorizzazioni appropriate, contattare l'amministratore della sottoscrizione per concedere autorizzazioni sufficienti.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [avviare una nuova app](luis-how-to-start-new-app.md)
