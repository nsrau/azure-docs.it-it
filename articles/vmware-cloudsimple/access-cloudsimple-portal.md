---
title: Accedere alla soluzione VMware di Azure tramite CloudSimple-Portal
description: Viene descritto come accedere alla soluzione VMware dal portale di CloudSimple da portale di Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 95d27bab243f6805436465f5027e519d33e20f6f
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563231"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>Accedere alla soluzione VMware tramite il portale di CloudSimple dal portale di Azure

Single Sign-on è supportato per l'accesso al portale di CloudSimple. Dopo aver eseguito l'accesso alla portale di Azure, è possibile accedere al portale di CloudSimple senza eseguire di nuovo l'accesso. La prima volta che si accede al portale di CloudSimple, viene richiesto di autorizzare l'applicazione di [autorizzazione del servizio CloudSimple](#consent-to-cloudsimple-service-authorization-application) .  L'autorizzazione è un'azione una volta sola.

## <a name="before-you-begin"></a>Prima di iniziare

Gli utenti con ruoli predefiniti di **proprietario** e **collaboratore** possono accedere al portale di CloudSimple.  I ruoli devono essere configurati nel gruppo di risorse in cui viene distribuito il servizio CloudSimple.  I ruoli possono anche essere configurati nell'oggetto servizio CloudSimple.  Per altre informazioni sul controllo del ruolo, vedere l'articolo visualizzare le assegnazioni di [ruolo](https://docs.microsoft.com/azure/role-based-access-control/check-access) . Solo gli utenti con ruoli predefiniti di **proprietario** e **collaboratore** possono accedere al portale di CloudSimple.  I ruoli devono essere configurati nella sottoscrizione.  Per altre informazioni sul controllo del ruolo, vedere l'articolo visualizzare le assegnazioni di [ruolo](https://docs.microsoft.com/azure/role-based-access-control/check-access) .

Se si utilizzano ruoli personalizzati, il ruolo deve disporre di una delle operazioni seguenti in ```Actions```.  Per altre informazioni sui ruoli personalizzati, vedere [ruoli personalizzati per le risorse di Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).  Se una delle operazioni è parte di, l' ```NotActions```utente non può accedere al portale di CloudSimple.

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Accedere al portale di CloudSimple

1. Selezionare **Tutti i servizi**.

2. Cercare i **Servizi CloudSimple**.

3. Selezionare il servizio CloudSimple in cui si vuole creare il cloud privato.

4. Nella pagina **Overview** fare clic su **go to the CloudSimple Portal**.  Se si accede al portale di CloudSimple dalla portale di Azure per la prima volta, verrà richiesto di autorizzare l'applicazione di [autorizzazione del servizio CloudSimple](#consent-to-cloudsimple-service-authorization-application) . 

    ![Avviare il portale di CloudSimple](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Se si seleziona un'operazione di cloud privato, ad esempio la creazione o l'espansione di un cloud privato, direttamente dalla portale di Azure, il portale di CloudSimple si apre alla pagina indicata.

Nel portale di CloudSimple selezionare **Home** dal menu laterale per visualizzare le informazioni di riepilogo sui cloud privati. Vengono visualizzate le risorse e la capacità dei cloud privati, insieme agli avvisi e alle attività che richiedono attenzione. Per le attività comuni, fare clic sulle icone denominate nella parte superiore della pagina.

![Home page](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>Consenso all'applicazione di autorizzazione del servizio CloudSimple

L'avvio del portale di CloudSimple dalla portale di Azure per la prima volta richiede il consenso dell'applicazione di autorizzazione del servizio CloudSimple.  Selezionare **accetta** per concedere le autorizzazioni richieste e accedere al portale di CloudSimple.

![Consenso per l'autorizzazione del servizio CloudSimple-amministratori](media/cloudsimple-azure-consent.png)

Se si dispone dei privilegi di amministratore globale, è possibile fornire il consenso per l'organizzazione.  Selezionare il **consenso per conto dell'organizzazione**.

![Consenso per l'autorizzazione del servizio CloudSimple-amministratore globale](media/cloudsimple-azure-consent-global-admin.png)

Se le autorizzazioni non consentono l'accesso al portale di CloudSimple, contattare l'amministratore globale del tenant per concedere le autorizzazioni necessarie.  Un amministratore globale può fornire il consenso per conto dell'organizzazione.

![Consenso per l'autorizzazione del servizio CloudSimple: richiede amministratori](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare un cloud privato](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Informazioni su come [configurare un ambiente cloud privato](quickstart-create-private-cloud.md)
