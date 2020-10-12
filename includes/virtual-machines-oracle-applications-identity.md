---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 2b28cc742bb981e0db535d39cb0c7fbf027470ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83673765"
---
### <a name="identity-tier"></a>Livello identità 

La partnership tra Microsoft e Oracle consente di configurare un'identità unificata tra Azure, OCI e l'applicazione Oracle. Per la famiglia di applicazioni JD Edwards EnterpriseOne o PeopleSoft è necessaria un'istanza di Oracle HTTP Server (OHS) per configurare il Single Sign-On tra Azure AD e Oracle IDCS.

OHS funge da proxy inverso per il livello applicazione, vale a dire che tutte le richieste alle applicazioni finali passano attraverso di esso. Oracle Access Manager WebGate è un plug-in del server Web OHS che intercetta ogni richiesta indirizzata all'applicazione finale. Se si accede a una risorsa protetta (ossia che richiede una sessione autenticata), WebGate avvia il flusso di autenticazione OIDC con Identity Cloud Service tramite il browser dell'utente. Per altre informazioni sui flussi supportati da OpenID Connect WebGate, vedere la [documentazione di Oracle Access Manager](https://docs.oracle.com/cd/E52734_01/oam/AIAAG/GUID-1E927D1B-FB83-425B-8768-85DB441821A4.htm#AIAAG7327).

Con questa configurazione, un utente già connesso ad Azure AD può passare all'applicazione JD Edwards EnterpriseOne o PeopleSoft senza eseguire di nuovo l'accesso, tramite Oracle Identity Cloud Service. I clienti che distribuiscono questa soluzione usufruiscono dei vantaggi del Single Sign-On, tra cui un singolo set di credenziali, un'esperienza di accesso migliorata, una maggiore sicurezza e una riduzione dei costi di help desk.

Per altre informazioni sulla configurazione del Single Sign-On per JD Edwards EnterpriseOne o PeopleSoft con Azure AD, vedere il [white paper di Oracle](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf) associato.