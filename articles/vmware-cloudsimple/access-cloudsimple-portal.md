---
title: Accedi alla soluzione Azure VMware da CloudSimple - Portale
description: Viene descritto come accedere alla soluzione VMware tramite il portale di CloudSimple dal portale di Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 91d3dd2eee6f771df23b72eae7a33122c42f1690
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869340"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>Accedi al portale VMware Solution by CloudSimple dal portale di Azure

L'accesso Single Sign-On è supportato per l'accesso al portale CloudSimple.Single Sign-On is supported for access to the CloudSimple portal. Dopo aver eseguito l'accesso al portale di Azure, è possibile accedere al portale CloudSimple senza eseguire nuovamente l'accesso. La prima volta che si accede al portale CloudSimple viene richiesto di autorizzare l'applicazione [CloudSimple Service Authorization.](#consent-to-cloudsimple-service-authorization-application)  L'autorizzazione è un'azione una tantera.

## <a name="before-you-begin"></a>Prima di iniziare

Gli utenti con ruoli Proprietario e **Collaboratore** incorporati possono accedere al portale CloudSimple.Users with builtin **Owner** and Contributor roles can access CloudSimple portal.  I ruoli devono essere configurati nel gruppo di risorse in cui viene distribuito il servizio CloudSimple.The roles must be configured on the resource group where CloudSimple service is deployed.  I ruoli possono essere configurati anche nell'oggetto servizio CloudSimple.The roles can also be configured on the CloudSimple service object.  Per altre informazioni sul controllo del ruolo, vedere [l'articolo Visualizzare le assegnazioni](https://docs.microsoft.com/azure/role-based-access-control/check-access) di ruolo. Solo gli utenti con ruoli Proprietario e **Collaboratore** incorporati possono accedere al portale CloudSimple.Only users with built-in **Owner** and Contributor roles can access the CloudSimple portal.  I ruoli devono essere configurati nella sottoscrizione.  Per altre informazioni sul controllo del ruolo, vedere [l'articolo Visualizzare le assegnazioni](https://docs.microsoft.com/azure/role-based-access-control/check-access) di ruolo.

Se si utilizzano ruoli personalizzati, il ruolo deve ```Actions```disporre di una delle seguenti operazioni in .  Per altre informazioni sui ruoli personalizzati, vedere Ruoli personalizzati per le risorse di Azure.For more information on custom roles, see [Custom roles for Azure resources.](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)  Se una delle operazioni fa ```NotActions```parte di , l'utente non può accedere al portale CloudSimple.

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Accedere al portale di CloudSimple

1. Selezionare **Tutti i servizi**.

2. Cercare **CloudSimple Services**.

3. Selezionare il servizio CloudSimple in cui si desidera creare il cloud privato.

4. Nella pagina **Panoramica** fare clic su **Vai al portale CloudSimple**.  Se si accede al portale CloudSimple dal portale di Azure per la prima volta, verrà richiesto di autorizzare l'applicazione [CloudSimple Service Authorization.](#consent-to-cloudsimple-service-authorization-application) 

    ![Avvia il portale CloudSimple](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Se si seleziona un'operazione di cloud privato (ad esempio la creazione o l'espansione di un cloud privato) direttamente dal portale di Azure, il portale CloudSimple si apre nella pagina indicata.

Nel portale CloudSimple, selezionare **Home** nel menu laterale per visualizzare informazioni di riepilogo sui cloud privati. Vengono visualizzate le risorse e la capacità dei cloud privati, insieme a avvisi e attività che richiedono attenzione. Per le attività comuni, fare clic sulle icone con nome nella parte superiore della pagina.

![Home page](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>Consenso all'applicazione cloudSimple Service Authorization

L'avvio del portale CloudSimple dal portale di Azure per la prima volta richiede il consenso dell'applicazione CloudSimple Service Authorization.  Selezionare Accetta per concedere le autorizzazioni richieste e accedere al portale CloudSimple.Select **Accept** to grant requested permissions and access the CloudSimple portal.

![Consenso all'autorizzazione al servizio CloudSimple - amministratori](media/cloudsimple-azure-consent.png)

Se si dispone del privilegio di amministratore globale, è possibile acconsentire all'organizzazione.  Selezionare **Acconsenti per conto dell'organizzazione**.

![Consenso all'autorizzazione del servizio CloudSimple - amministratore globale](media/cloudsimple-azure-consent-global-admin.png)

Se le autorizzazioni non consentono l'accesso al portale CloudSimple, contattare l'amministratore globale del tenant per concedere le autorizzazioni necessarie.  Un amministratore globale può acconsentire per conto della tua organizzazione.

![Consenso all'autorizzazione del servizio CloudSimple - richiede amministratori](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Passaggi successivi

* Scopri come [creare un cloud privato](https://docs.microsoft.com/azure/vmware-cloudsimple/create-private-cloud/)
* Informazioni su come [configurare un ambiente di cloud privato](quickstart-create-private-cloud.md)
