---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: c1cca99a04e78c40cdd1061e52e49f45a73c73eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361557"
---
### <a name="identity-tier"></a>Livello di identità 

La partnership Microsoft-Oracle consente di configurare un'identità unificata tra Azure, OCI e l'applicazione Oracle.The Microsoft-Oracle partnership enables you to set up a unified identity across Azure, OCI, and your Oracle application. Per la suite di applicazioni JD Edwards EnterpriseOne o PeopleSoft, è necessaria un'istanza di Oracle HTTP Server (OHS) per configurare l'accesso Single Sign-On tra Azure AD e Oracle IDCS.

OHS funge da proxy inverso per il livello applicazione, il che significa che tutte le richieste alle applicazioni finali passano attraverso di esso. Oracle Access Manager WebGate è un plug-in del server web OHS che intercetta ogni richiesta inviata all'applicazione finale. Se una risorsa a cui si accede è protetta (richiede una sessione autenticata), WebGate avvia il flusso di autenticazione OIDC con Identity Cloud Service tramite il browser dell'utente. Per ulteriori informazioni sui flussi supportati da OpenID Connect WebGate, vedere la [documentazione](https://docs.oracle.com/en/middleware/idm/access-manager/12.2.1.3/aiaag/integrating-webgate-oidc-server.html)di Oracle Access Manager .

Con questa configurazione, un utente già connesso ad Azure AD può passare all'applicazione JD Edwards EnterpriseOne o PeopleSoft senza eseguire nuovamente l'accesso, tramite Oracle Identity Cloud Service. I clienti che distribuiscono questa soluzione traggono i vantaggi dell'accesso Single Sign-On, tra cui un singolo set di credenziali, un'esperienza di accesso migliorata, una maggiore sicurezza e costi di help desk ridotti.

Per altre informazioni sulla configurazione dell'accesso Single Sign-On per JD Edwards EnterpriseOne o PeopleSoft con Azure AD, vedere il [white paper di Oracle](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf)associato.