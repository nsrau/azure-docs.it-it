---
title: Come collegare una sottoscrizione di Azure ad Azure AD B2C | Documentazione Microsoft
description: Guida dettagliata all'abilitazione della fatturazione per tenant Azure AD B2C in una sottoscrizione di Azure.
services: active-directory-b2c
documentationcenter: dev-center-name
author: parakhj
manager: krassk
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2017
ms.author: parja
ms.openlocfilehash: 35fab74abf2c2ba27a8bf99eb93eb53f39b26227
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2017
---
# <a name="linking-an-azure-subscription-to-an-azure-ad-b2c-tenant"></a>Collegamento di una sottoscrizione di Azure a un tenant di Azure AD B2C

> [!IMPORTANT]
> Le informazioni più aggiornate su fatturazione e prezzi per l'utilizzo di Azure AD B2C sono disponibili nella pagina [Prezzi di Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

I costi per l'utilizzo di Azure AD B2C vengono addebitati a una sottoscrizione di Azure. Quando viene creato un tenant di Azure AD B2C, l'amministratore del tenant deve collegarlo in modo esplicito a una sottoscrizione di Azure. Questo articolo illustra i passaggi da eseguire.

> [!NOTE]
> Una sottoscrizione collegata a un tenant di Azure AD B2C può essere usata solo per la fatturazione dei costi di utilizzo di Azure AD B2C. La sottoscrizione non può essere usata per aggiungere altri servizi di Azure o licenze di Office 365 *nel tenant di Azure AD B2C*.

 Questo collegamento alla sottoscrizione viene realizzato mediante la creazione di una "risorsa" di Azure AD B2C nella sottoscrizione di Azure di destinazione. È possibile creare molte "risorse" di Azure AD B2C all'interno di una singola sottoscrizione, insieme ad altre risorse di Azure, ad esempio macchine virtuali, archivi dati, app per la logica e così via. È possibile visualizzare tutte le risorse nella sottoscrizione passando al tenant di Azure AD cui è associata la sottoscrizione.

Per continuare, è necessaria una sottoscrizione di Azure valida.

## <a name="create-an-azure-ad-b2c-tenant"></a>Creare un tenant di Azure AD B2C

Prima di tutto, è necessario [creare il tenant di Azure AD B2C](active-directory-b2c-get-started.md) cui si vuole collegare una sottoscrizione. Ignorare questo passaggio se è già stato creato un tenant di Azure AD B2C.

## <a name="open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Aprire il portale di Azure nel tenant di Azure AD che mostra la sottoscrizione di Azure

Passare al tenant di Azure AD che mostra la sottoscrizione di Azure. Aprire il [portale di Azure](https://portal.azure.com) e passare al tenant di Azure AD che mostra la sottoscrizione di Azure che si vuole usare.

![Passaggio al tenant di Azure AD](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="find-azure-ad-b2c-in-the-azure-marketplace"></a>Trovare Azure Active Directory B2C in Azure Marketplace

Fare clic sul pulsante **Nuovo**. Nel campo **Cerca nel Marketplace** immettere `B2C`.

![Pulsante Aggiungi evidenziato e testo Azure Active Directory B2C nel campo Cerca nel Marketplace](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c.png)

Nell'elenco dei risultati selezionare **Azure Active Directory B2C**.

![Azure Active Directory B2C selezionato nell'elenco dei risultati](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c-result.png)

Vengono visualizzate informazioni dettagliate su Azure AD B2C. Per iniziare la configurazione del nuovo tenant di Azure Active Directory B2C, fare clic sul pulsante **Crea**.

Nella schermata di creazione delle risorse selezionare **Collega un tenant Azure AD B2C esistente alla sottoscrizione di Azure**.

## <a name="create-an-azure-ad-b2c-resource-within-the-azure-subscription"></a>Creare una risorsa di Azure AD B2C nella sottoscrizione di Azure

Nella finestra di dialogo di creazione delle risorse selezionare un tenant di Azure AD B2C nell'elenco a discesa. Verranno visualizzati tutti i tenant di cui si è l'amministratore globale e quelli che non sono già collegati a una sottoscrizione.

Il nome della risorsa di Azure AD B2C sarà preselezionato in modo da corrispondere al nome di dominio del tenant di Azure AD B2C.

Per Sottoscrizione, selezionare una sottoscrizione attiva di Azure di cui si è l'amministratore.

Selezionare un gruppo di risorse e la località del gruppo di risorse. La selezione non influisce su località, prestazioni o stato di fatturazione del tenant di Azure AD B2C.

![Creare una risorsa B2C](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="manage-your-azure-ad-b2c-tenent-resources"></a>Gestire le risorse del tenant di Azure AD B2C

Dopo aver creato una risorsa di Azure AD B2C nella sottoscrizione di Azure, verrà visualizzata una nuova risorsa di tipo "Tenant B2C" aggiunta insieme alle altre risorse di Azure.

È possibile usare questa risorsa per:

- Passare alla sottoscrizione per esaminare le informazioni di fatturazione.
- Passare al tenant di Azure AD B2C.
- Inviare una richiesta di supporto.
- Spostare la risorsa del tenant di Azure AD B2C in un'altra sottoscrizione di Azure o in un altro gruppo di risorse.

![Impostazioni della risorsa B2C](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)

## <a name="known-issues"></a>Problemi noti

### <a name="csp-subscriptions"></a>Sottoscrizioni CSP

Attualmente un tenant di Azure AD B2C **non può** essere collegato a sottoscrizioni CSP.

### <a name="self-imposed-restrictions"></a>Restrizioni imposte personalmente

Un utente potrebbe aver stabilito una restrizione regionale per la creazione di risorse di Azure. Questa restrizione può impedire la creazione di una risorsa di Azure AD B2C. Per attenuate il problema, ridurre questa restrizione.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver completato questa procedura per ogni tenant di Azure AD B2C, i costi della sottoscrizione di Azure vengono addebitati in base ai dettagli relativi al contratto Enterprise Agreement o ad Azure Direct.

È possibile esaminare le informazioni su utilizzo e fatturazione all'interno della sottoscrizione di Azure selezionata. È anche possibile esaminare report dettagliati sull'utilizzo giornaliero con l'[API di segnalazione dell'utilizzo](active-directory-b2c-reference-usage-reporting-api.md).
