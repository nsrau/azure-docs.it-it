---
title: Come collegare una sottoscrizione di Azure ad Azure AD B2C | Documentazione Microsoft
description: Guida dettagliata all&quot;abilitazione della fatturazione per tenant Azure AD B2C in una sottoscrizione di Azure.
services: active-directory-b2c
documentationcenter: dev-center-name
author: rojasja
manager: mbaldwin
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2016
ms.author: joroja
translationtype: Human Translation
ms.sourcegitcommit: 0475c0f209cde80177df7dbf23eaf8dd17a44752
ms.openlocfilehash: b5754a08e0683344cc97bdc664ed26ef9a9cf34d


---
# <a name="linking-an-azure-subscription-to-an-azure-b2c-tenant-to-pay-for-usage-charges"></a>Collegamento di una sottoscrizione di Azure a un tenant di Azure B2C per pagare gli addebiti per l'utilizzo
> [!IMPORTANT]
> Presto disponibile. Questa funzionalità non è disponibile per tutti i tenant B2C.

I costi per l'utilizzo in corso di Azure Active Directory B2C (o Azure AD B2C) vengono addebitati a una sottoscrizione di Azure. Dopo la creazione del tenant B2C, l'amministratore del tenant Azure AD B2C deve collegarlo in modo esplicito a una sottoscrizione di Azure.  Questo collegamento viene realizzato mediante la creazione di una risorsa "Tenant B2C" di Azure AD B2C nella sottoscrizione di Azure di destinazione. È possibile collegare più tenant B2C a una singola sottoscrizione di Azure insieme ad altre risorse di Azure, come macchine virtuali, archivio dati o app per la logica.


> [!IMPORTANT]
> Le informazioni più recenti sulla fatturazione relativa all'utilizzo e sui prezzi per B2C sono disponibili alla pagina [Prezzi di Azure Active Directory B2C](
https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="step-1---create-an-azure-ad-b2c-tenant"></a>Passaggio 1: Creare un tenant Azure AD B2C

La prima operazione da eseguire è la creazione del tenant B2C. Se il tenant B2C di destinazione è già stato creato, è possibile ignorare questo passaggio. [Introduzione ad Azure AD B2C](https://azure.microsoft.com/documentation/articles/active-directory-b2c-get-started/)

## <a name="step-2---open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Passaggio 2: Aprire il portale di Azure nel tenant di Azure AD che mostra la sottoscrizione di Azure
Passare all'indirizzo portal.azure.com. Passare al tenant di Azure AD che mostra la sottoscrizione di Azure che si vuole usare. Questo tenant di Azure AD è diverso dal tenant B2C. Nel portale di Azure fare clic sul nome dell'account nell'angolo superiore destro del dashboard e selezionare il tenant di Azure AD. Per continuare è necessaria una sottoscrizione di Azure. [Ottenere una sottoscrizione di Azure](https://account.windowsazure.com/signup?showCatalog=True)

![Passaggio al tenant di Azure AD](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="step-3---create-a-b2c-tenant-resource-in-azure-marketplace"></a>Passaggio 3: Creare una risorsa Tenant B2C in Azure Marketplace
Per aprire Marketplace, fare clic sulla relativa icona o sul segno "+" verde nell'angolo superiore sinistro del dashboard.  Cercare e selezionare Azure Active Directory B2C. Selezionare Crea.
![Selezionare Marketplace](./media/active-directory-b2c-how-to-enable-billing/marketplace.png)

![Cercare AD B2C](./media/active-directory-b2c-how-to-enable-billing/searchb2c.png)

La finestra di dialogo di creazione Risorsa di Azure AD B2C include i parametri seguenti:

1. Tenant Azure AD B2C: selezionare un tenant Azure AD B2C dall'elenco a discesa,  che mostra solo i tenant Azure AD B2C idonei.  I tenant B2C idonei soddisfano le condizioni seguenti: l'utente ne è l'amministratore globale e il tenant B2C non è attualmente associato a una sottoscrizione di Azure.

2. Nome risorsa di Azure AD B2C: è preselezionata l'opzione che corrisponde al nome di dominio del tenant B2C.

3. Sottoscrizione: sottoscrizione di Azure attiva di cui l'utente è amministratore o coamministratore.  È possibile aggiungere più tenant Azure AD B2C a una singola sottoscrizione di Azure.

4. Gruppo di risorse e Località del gruppo di risorse: questo elemento consente di organizzare più risorse di Azure.  La scelta non influisce in alcun modo sulla località, sulle prestazioni o sullo stato di fatturazione del tenant B2C.

5. Selezionare l'opzione Aggiungi al dashboard per accedere più facilmente alle informazioni di fatturazione e alle impostazioni del tenant B2C. ![Creare una risorsa B2C](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="step-4---manage-your-b2c-tenant-resources-optional"></a>Passaggio 4: Gestire le risorse del tenant B2C (facoltativo)
Al termine della distribuzione viene creata una nuova risorsa "Tenant B2C" nel gruppo di risorse di destinazione e nella sottoscrizione di Azure correlata.  Insieme alle altre risorse di Azure viene visualizzata una nuova risorsa di tipo "Tenant B2C".

![Creare una risorsa B2C](./media/active-directory-b2c-how-to-enable-billing/b2cresourcedashboard.png)

Facendo clic sulla risorsa Tenant B2C è possibile:
- Fare clic sul nome della sottoscrizione per rivedere le informazioni di fatturazione. Visualizzare Fatturazione e utilizzo.
- Fare clic su Impostazioni di Azure AD B2C per aprire una nuova scheda del browser direttamente nel pannello Impostazioni del tenant B2C.
- Inviare una richiesta di supporto.
- Spostare le risorse del tenant B2C in un'altra sottoscrizione di Azure o in un altro gruppo di risorse.  Questa opzione permette di cambiare la sottoscrizione di Azure a cui vengono addebitati i costi per l'utilizzo.

![Impostazioni della risorsa B2C](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)


## <a name="next-steps"></a>Passaggi successivi
Dopo aver completato questa procedura per ogni tenant B2C, i costi vengono addebitati alla sottoscrizione di Azure in base al Contratto Enterprise o Azure Direct.
- Esaminare l'utilizzo e la fatturazione all'interno della sottoscrizione di Azure selezionata.
- Esaminare i report dettagliati sull'utilizzo giornaliero con l'API Segnalazione utilizzo funzionalità (da definire).



<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/        
[yah]: http://search.yahoo.com/  
[msn]: http://search.msn.com/    



<!--HONumber=Jan17_HO1-->


