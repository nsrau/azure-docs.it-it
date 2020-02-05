---
title: Accedere alle soluzioni VMware di Azure (AVS)-portale
description: Viene descritto come accedere alle soluzioni VMware di Azure (AVS) dalla portale di Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0b0601b9d2cf505aa5b25e10875338352cab771
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015951"
---
# <a name="access-azure-vmware-solutions-avs-from-the-azure-portal"></a>Accedi alle soluzioni VMware di Azure (AVS) dalla portale di Azure

Single Sign-on è supportato per l'accesso al portale AVS. Dopo aver eseguito l'accesso alla portale di Azure, è possibile accedere al portale AVS senza eseguire di nuovo l'accesso. La prima volta che si accede al portale di AVS, viene richiesto di autorizzare l'applicazione di [autorizzazione del servizio AVS](#consent-to-avs-service-authorization-application) . L'autorizzazione è un'azione una volta sola.

## <a name="before-you-begin"></a>Prima di iniziare

Gli utenti con ruoli predefiniti di **proprietario** e **collaboratore** possono accedere al portale AVS. I ruoli devono essere configurati nel gruppo di risorse in cui è distribuito il servizio AVS. I ruoli possono anche essere configurati nell'oggetto servizio AVS. Per altre informazioni sul controllo del ruolo, vedere l'articolo [visualizzare le assegnazioni di ruolo](https://docs.microsoft.com/azure/role-based-access-control/check-access) . Solo gli utenti con ruoli predefiniti di **proprietario** e **collaboratore** possono accedere al portale AVS. I ruoli devono essere configurati nella sottoscrizione. Per altre informazioni sul controllo del ruolo, vedere l'articolo [visualizzare le assegnazioni di ruolo](https://docs.microsoft.com/azure/role-based-access-control/check-access) .

Se si utilizzano ruoli personalizzati, il ruolo deve disporre di una delle seguenti operazioni in ```Actions```.  Per altre informazioni sui ruoli personalizzati, vedere [ruoli personalizzati per le risorse di Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). Se una qualsiasi delle operazioni fa parte di ```NotActions```, l'utente non può accedere al portale AVS. 

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Accedere a Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-avs-portal"></a>Accedere al portale AVS

1. Selezionare **Tutti i servizi**.

2. Cercare **AVS Services**.

3. Selezionare il servizio AVS in cui si vuole creare il cloud privato.

4. Nella pagina **Overview** fare clic su **go to the AVS Portal**. Se si accede al portale AVS dal portale di Azure per la prima volta, verrà richiesto di autorizzare l'applicazione di autorizzazione del [servizio AVS](#consent-to-avs-service-authorization-application) . 

    ![Avviare il portale AVS](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Se si seleziona un'operazione di cloud privato, ad esempio la creazione o l'espansione di un cloud privato, direttamente dalla portale di Azure, il portale AVS si apre alla pagina indicata.

Nel portale di AVS scegliere **Home** dal menu laterale per visualizzare le informazioni di riepilogo sul cloud privato AVS. Vengono visualizzate le risorse e la capacità del cloud privato AVS, insieme agli avvisi e alle attività che richiedono attenzione. Per le attività comuni, fare clic sulle icone denominate nella parte superiore della pagina.

![Home page](media/cloudsimple-portal-home.png)

## <a name="consent-to-avs-service-authorization-application"></a>Consenso per l'applicazione di autorizzazione del servizio AVS

L'avvio del portale AVS dalla portale di Azure per la prima volta richiede il consenso per l'applicazione di autorizzazione del servizio AVS. Selezionare **Accept (accetta** ) per concedere le autorizzazioni richieste e accedere al portale AVS.

![Consenso per l'autorizzazione del servizio AVS-amministratori](media/cloudsimple-azure-consent.png)

Se si dispone dei privilegi di amministratore globale, è possibile fornire il consenso per l'organizzazione. Selezionare il **consenso per conto dell'organizzazione**.

![Consenso per l'autorizzazione del servizio AVS-amministratore globale](media/cloudsimple-azure-consent-global-admin.png)

Se le autorizzazioni non consentono l'accesso al portale AVS, contattare l'amministratore globale del tenant per concedere le autorizzazioni necessarie. Un amministratore globale può fornire il consenso per conto dell'organizzazione.

![Consenso per l'autorizzazione del servizio AVS: richiede amministratori](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare un cloud privato](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Informazioni su come [configurare un ambiente cloud privato](quickstart-create-private-cloud.md)
