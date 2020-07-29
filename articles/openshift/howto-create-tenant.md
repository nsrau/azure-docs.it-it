---
title: Creare un tenant di Azure AD per Azure Red Hat OpenShift
description: Di seguito viene illustrato come creare un tenant di Azure Active Directory (Azure AD) per ospitare il cluster Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ad03538cafcce9c1d660d0f2ac5eb3c6ae5f4f38
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84694906"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Creare un tenant di Azure AD per Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift richiede un tenant di [Azure Active Directory (Azure ad)](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) in cui creare il cluster. Un *tenant* è un'istanza dedicata di Azure ad che un'organizzazione o uno sviluppatore di app riceve quando crea una relazione con Microsoft iscrivendosi ad Azure, Microsoft Intune o Microsoft 365. Ogni tenant di Azure AD è distinto e separato dagli altri tenant di Azure AD e ha le proprie identità aziendali e dell'Istituto di istruzione e le registrazioni delle app.

Se non si ha già un tenant di Azure AD, seguire queste istruzioni per crearne uno.

## <a name="create-a-new-azure-ad-tenant"></a>Creare un nuovo tenant Azure AD

Per creare un tenant:

1. Accedere al [portale di Azure](https://portal.azure.com/) usando l'account che si vuole associare al cluster Azure Red Hat OpenShift.
2. Aprire il pannello [Azure Active Directory](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) per creare un nuovo tenant (anche noto come nuovo *Azure Active Directory*).
3. Specificare un **nome**per l'organizzazione.
4. Specificare un **nome di dominio iniziale**. A questa operazione verrà aggiunto *onmicrosoft.com* . È possibile riusare il valore per *nome organizzazione* qui.
5. Scegliere un paese o un'area geografica in cui verrà creato il tenant.
6. Fare clic su **Crea**.
7. Dopo la creazione del tenant di Azure AD, selezionare il collegamento **fare clic qui per gestire la nuova directory** . Il nome del nuovo tenant dovrebbe essere visualizzato in alto a destra nella portale di Azure:  

    ![Screenshot del portale che mostra il nome del tenant in alto a destra][tenantcallout]  

8. Prendere nota dell' *ID tenant* per poter specificare in seguito dove creare il cluster Azure Red Hat OpenShift. Nel portale dovrebbe ora essere visualizzato il pannello panoramica Azure Active Directory per il nuovo tenant. Selezionare **Proprietà** e copiare il valore per l' **ID directory**. Si fa riferimento a questo valore come `TENANT` nell'esercitazione [creare un cluster Azure Red Hat OpenShift](tutorial-create-cluster.md) .

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Risorse

Per altre informazioni sui [tenant Azure ad](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant), vedere [Azure Active Directory documentazione](https://docs.microsoft.com/azure/active-directory/) .

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come creare un'entità servizio, generare un segreto client e un URL di callback di autenticazione e creare un nuovo utente Active Directory per testare le app nel cluster OpenShift di Azure Red Hat.

[Creare un utente e un oggetto app di Azure AD](howto-aad-app-configuration.md)
