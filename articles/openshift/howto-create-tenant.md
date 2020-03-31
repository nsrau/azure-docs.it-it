---
title: Creare un tenant di Azure AD per Azure Red Hat OpenShiftCreate an Azure AD tenant for Azure Red Hat OpenShift
description: Ecco come creare un tenant di Azure Active Directory (Azure AD) per ospitare il cluster Microsoft Azure Red Hat OpenShift.Here's how to create an Azure Active Directory (Azure AD) tenant to host your Microsoft Azure Red Hat OpenShift cluster.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ad03538cafcce9c1d660d0f2ac5eb3c6ae5f4f38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243692"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Creare un tenant di Azure AD per Azure Red Hat OpenShiftCreate an Azure AD tenant for Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift richiede un tenant di [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) in cui creare il cluster. Un tenant è un'istanza dedicata di Azure AD che un'organizzazione o uno sviluppatore di app riceve quando crea una relazione con Microsoft effettuando l'iscrizione ad Azure, Microsoft Intune o Microsoft 365.A *tenant* is a dedicated instance of Azure AD that an organization or app developer receives when they create a relationship with Microsoft by signing up for Azure, Microsoft Intune, or Microsoft 365. Ogni tenant di Azure AD è distinto e separato dagli altri tenant di Azure AD e ha le proprie identità aziendali e dell'istituto di istruzione e le registrazioni delle app.

Se non si dispone già di un tenant di Azure AD, seguire queste istruzioni per crearne uno.

## <a name="create-a-new-azure-ad-tenant"></a>Creare un nuovo tenant Azure AD

Per creare un tenant:

1. Accedere al [portale](https://portal.azure.com/) di Azure usando l'account che si vuole associare al cluster Azure Red Hat OpenShift.Sign in to the Azure portal using the account you wish to associate with your Azure Red Hat OpenShift cluster.
2. Aprire il [pannello Azure Active Directory](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) per creare un nuovo tenant (noto anche come nuovo Azure Active *Directory).*
3. Specificare un **nome di organizzazione**.
4. Specificare un **nome di dominio iniziale**. Questo avrà *onmicrosoft.com* aggiunto ad esso. È possibile riutilizzare il valore di *Nome organizzazione* qui.
5. Scegliere un paese o un'area geografica in cui verrà creato il tenant.
6. Fare clic su **Crea**.
7. Dopo aver creato il tenant di Azure AD, selezionare il collegamento **Fare clic qui per gestire la nuova directory.** Il nuovo nome del tenant dovrebbe essere visualizzato nell'angolo superiore destro del portale di Azure:Your new tenant name should be displayed in the upper-right of the Azure portal:  

    ![Screenshot del portale che mostra il nome del tenant in alto a destra][tenantcallout]  

8. Prendere nota *dell'ID tenant* in modo da poter specificare in un secondo momento dove creare il cluster Azure Red Hat OpenShift. Nel portale dovrebbe ora essere visualizzato il pannello Panoramica di Azure Active Directory per il nuovo tenant. Selezionare **Proprietà** e copiare il valore per **l'ID directory**. Faremo riferimento a `TENANT` questo valore come nell'esercitazione [sul cluster Create an Azure Red Hat OpenShift.We](tutorial-create-cluster.md) will refer to this value as in the Create an Azure Red Hat OpenShift cluster tutorial.

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Risorse

Per altre informazioni sui tenant di Azure AD, vedere la [documentazione](https://docs.microsoft.com/azure/active-directory/) di Azure Active Directory.Check out Azure Active Directory documentation for more info on Azure [AD tenants](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come creare un'entità servizio, generare un segreto client e un URL di callback di autenticazione e creare un nuovo utente di Active Directory per testare le app nel cluster OpenShift di Azure Red Hat.Learn how to create a service principal, generate a client secret and authentication callback URL, and create a new Active Directory user for testing apps on your Azure Red Hat OpenShift cluster.

[Creare un utente e un oggetto app di Azure AD](howto-aad-app-configuration.md)
