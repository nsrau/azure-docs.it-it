---
title: Creare un tenant di Azure AD per Azure Red Hat OpenShift | Microsoft Docs
description: Di seguito viene illustrato come creare un tenant di Azure Active Directory (Azure AD) per ospitare il cluster di Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
manager: jeconnoc
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 560cdcf8a99a486c7f5177b675cff327c6fb6a41
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306455"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Creare un tenant di Azure AD per Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift richiede un [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) tenant in cui creare il cluster. Oggetto *tenant* è un'istanza dedicata di Azure Active Directory che uno sviluppatore di app o organizzazione riceve quando si crea una relazione con Microsoft effettuando l'iscrizione per Azure, Microsoft Intune o Microsoft 365. Ogni tenant di Azure AD è distinto e separato da altri Azure AD tenant e ha il proprio lavoro e le identità dell'istituto di istruzione e registrazioni per l'app.

Se non si ha già un tenant di Azure AD, seguire queste istruzioni per crearne uno.

## <a name="create-a-new-azure-ad-tenant"></a>Creare un nuovo tenant Azure AD

Per creare un tenant:

1. Accedi per il [portale di Azure](https://portal.azure.com/) usando l'account si vuole associare al cluster di Azure Red Hat OpenShift.
2. Aprire il [pannello Azure Active Directory](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) per creare un nuovo tenant (noto anche come un nuovo *Azure Active Directory*).
3. Fornire un' **nome organizzazione**.
4. Fornire un' **nome di dominio iniziale**. Questo avrà *onmicrosoft.com* aggiunto a tale file. È possibile riutilizzare il valore per *nome organizzazione* qui.
5. Scegliere un paese o area geografica in cui verrà creato il tenant.
6. Fare clic su **Create**(Crea).
7. Dopo aver creato il tenant di Azure AD, selezionare la **fare clic qui per gestire la nuova directory** collegamento. Il nuovo nome del tenant deve essere visualizzato in alto a destra del portale di Azure:  

    ![Screenshot del portale che mostra il nome del tenant in alto a destra][tenantcallout]  

8. Assicurarsi di annotare il *ID tenant* quindi in un secondo momento è possibile specificare la posizione in cui creare il cluster di Azure Red Hat OpenShift. Nel portale, viene visualizzato il pannello di panoramica di Azure Active Directory per il nuovo tenant. Selezionare **delle proprietà** e copiare il valore per il **ID Directory**. Si farà riferimento a questo valore come `TENANT` nella [creare un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md) esercitazione.

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Risorse

Consulta [documentazione di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) per altre informazioni sui [tenant Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come creare un'entità servizio, generare un URL di callback autenticazione e il segreto client e creare un nuovo utente di Active Directory per il test di App nel cluster Azure Red Hat OpenShift.

[Creare un oggetto di app di Azure AD e l'utente](howto-aad-app-configuration.md)