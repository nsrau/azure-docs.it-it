---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: c1cca99a04e78c40cdd1061e52e49f45a73c73eb
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361557"
---
### <a name="identity-tier"></a>Livello di identità 

La partnership Microsoft-Oracle consente di configurare un'identità unificata in Azure, OCI e nell'applicazione Oracle. Per JD Edwards EnterpriseOne o PeopleSoft Application Suite, è necessaria un'istanza del server HTTP Oracle (OHS) per configurare l'accesso Single Sign-on tra Azure AD e Oracle IDC.

OHS funge da proxy inverso per il livello applicazione, il che significa che tutte le richieste alle applicazioni finali passano attraverso di essa. Oracle Access Manager WebGate è un plug-in del server Web OHS che intercetta tutte le richieste che passano all'applicazione finale. Se una risorsa a cui si accede è protetta (richiede una sessione autenticata), il WebGate avvia il flusso di autenticazione OIDC con il servizio cloud di identità tramite il browser dell'utente. Per ulteriori informazioni sui flussi supportati dal WebGate di OpenID Connect, vedere la [documentazione di Oracle Access Manager](https://docs.oracle.com/en/middleware/idm/access-manager/12.2.1.3/aiaag/integrating-webgate-oidc-server.html).

Con questa configurazione, un utente che ha già effettuato l'accesso a Azure AD può passare all'applicazione JD Edwards EnterpriseOne o PeopleSoft senza eseguire di nuovo l'accesso, tramite Oracle Identity Cloud Service. I clienti che distribuiscono questa soluzione ottengono i vantaggi di Single Sign-on, tra cui un singolo set di credenziali, un'esperienza di accesso migliorata, una maggiore sicurezza e un ridotto costo del supporto tecnico.

Per ulteriori informazioni sulla configurazione di Single Sign-on per JD Edwards EnterpriseOne o PeopleSoft con Azure AD, vedere il [white paper Oracle](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf)associato.