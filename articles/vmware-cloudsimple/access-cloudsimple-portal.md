---
title: Soluzione Azure VMware accesso CloudSimple - portale
description: Viene descritto come accedere soluzione VMware dal portale CloudSimple dal portale di Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 61c3d37f365034984231c780199e181872c010c6
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544121"
---
# <a name="accessing-the-vmware-solution-by-cloudsimple-portal-from-azure-portal"></a>L'accesso alla soluzione VMware dal portale CloudSimple dal portale di Azure

Accesso Single sign-on è supportato per l'accesso al portale CloudSimple. Dopo l'accesso al portale di Azure, è possibile accedere al portale CloudSimple senza eseguire nuovamente l'accesso. La prima volta che si accede al portale di CloudSimple viene richiesto di autorizzare il [autorizzazione del servizio CloudSimple](#consent-to-cloudsimple-service-authorization-application) dell'applicazione.  L'autorizzazione è un'operazione occasionale.

## <a name="before-you-begin"></a>Prima di iniziare

Gli utenti con builtin **Owner** e **collaboratore** ruoli è possono accedere al portale CloudSimple.  I ruoli devono essere configurati nel gruppo di risorse in cui viene distribuito il servizio CloudSimple.  I ruoli possono essere configurati anche nell'oggetto del servizio CloudSimple.  Per altre informazioni sul controllo del proprio ruolo, vedere [visualizzare le assegnazioni di ruolo](https://docs.microsoft.com/azure/role-based-access-control/check-access) articolo.

Se si usa i ruoli personalizzati, il ruolo dovrebbe disporre una delle operazioni seguenti in ```Actions```.  Per altre informazioni sui ruoli personalizzati, vedere [ruoli personalizzati per le risorse di Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).  Se una delle operazioni è una parte di ```NotActions```, l'utente non può accedere CloudSimple portale. 

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

2. Cercare **CloudSimple servizi**.

3. Selezionare il servizio CloudSimple in cui si desidera creare il Cloud privato.

4. Nel **Overview** pagina, fare clic su **Vai al portale CloudSimple**.  Se si accede al portale CloudSimple dal portale di Azure per la prima volta, verrà chiesto di autorizzare il [autorizzazione del servizio CloudSimple](#consent-to-cloudsimple-service-authorization-application) dell'applicazione. 

    ![Avviare il portale CloudSimple](media/launch-cloudsimple-portal.png)

> [!TIP]
> Se si seleziona un'operazione di Cloud privato (ad esempio la creazione o l'espansione di un Cloud privato) direttamente dal portale di Azure, il portale CloudSimple viene visualizzata la pagina indicata.

Nel portale di CloudSimple, selezionare **Home** nel menu laterale per visualizzare le informazioni di riepilogo dei cloud privati. Le risorse e capacità dei cloud privati vengono visualizzati, insieme a avvisi e alle attività che richiedono attenzione. Per attività comuni, fare clic su denominata icone nella parte superiore della pagina.

![Home Page](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>Fornire il consenso all'applicazione l'autorizzazione del servizio CloudSimple

Avviare il portale CloudSimple dal portale di Azure per la prima volta che richiede il consenso dell'utente per l'applicazione CloudSimple autorizzazione del servizio.  Selezionare **Accept** per concedere le autorizzazioni richieste e accedere al portale CloudSimple. 

![Fornire il consenso per l'autorizzazione del servizio CloudSimple - gli amministratori](media/cloudsimple-azure-consent.png)

Se si dispone dei privilegi di amministratore globale, è possibile fornire il consenso per l'organizzazione.  Selezionare **fornire il consenso per conto dell'organizzazione**.

![Fornire il consenso per l'autorizzazione del servizio CloudSimple - amministratore globale](media/cloudsimple-azure-consent-global-admin.png)

Se le autorizzazioni non consentono l'accesso al portale CloudSimple, contattare l'amministratore globale del tenant per concedere le autorizzazioni necessarie.  Un amministratore globale può fornire il consenso per conto dell'organizzazione.

![Fornire il consenso per l'autorizzazione del servizio CloudSimple, richiede amministratori](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare un cloud privato](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Informazioni su come [configurare un ambiente cloud privato](quickstart-create-private-cloud.md)
