---
title: Risolvere i problemi di installazione di Azure AD Connect | Microsoft Docs
description: In questo argomento viene descritta la procedura per risolvere i problemi di installazione di Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25314b4a306678dc877a95194907b3d73979e4f8
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89275862"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Risoluzione dei problemi: installazione di Azure AD Connect

## <a name="recommended-steps"></a>**Procedura consigliata**
Individuare il [tipo di installazione di Azure AD Connect](./how-to-connect-install-select-installation.md) adatto alle proprie esigenze. Se si soddisfano i criteri per l'installazione rapida, è consigliabile procedere con questo tipo di installazione. L'installazione rapida offre opzioni minime per completare l'installazione, pertanto è meno probabile che si verifichino problemi. 

Se tuttavia non si soddisfano i criteri per l'installazione rapida ed è necessario eseguire l'installazione personalizzata, di seguito vengono fornite alcune procedure consigliate che è possibile seguire per evitare problemi comuni. Per maggiore semplicità, vengono citate di seguito solo opzioni selettive:

* Assicurarsi di essere un amministratore nel computer in cui si installa AAD Connect. Accedere al computer con le credenziali di amministratore.

* Accettare tutte le opzioni predefinite nella pagina seguente, ad eccezione di "Usa un'istanza di SQL Server esistente", se si vuole usare l'istanza di SQL Server esistente. Ecco [altri dettagli](./how-to-connect-install-custom.md) su come usare opzioni di installazione personalizzate. 

    ![Usare un'istanza di SQL Server esistente](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* Nella pagina seguente selezionare l'opzione "Crea un nuovo account AD" per evitare problemi di autorizzazione con l'account esistente.

    ![Account della foresta AD](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Problemi comuni**

* [Problemi di connettività con Active Directory locali](./reference-connect-adconnectivitytools.md).

* [Problemi di connettività con Azure Active Directory online](./tshoot-connect-connectivity.md).

* [Problemi di autorizzazione con Active Directory locali](./how-to-connect-configure-ad-ds-connector-account.md).

## <a name="recommended-documents"></a>**Documenti consigliati**
* [Prerequisiti di Azure AD Connect](./how-to-connect-install-prerequisites.md)
* [Selezionare il tipo di installazione da usare per Azure AD Connect](./how-to-connect-install-select-installation.md)
* [Introduzione alle impostazioni rapide per Azure AD Connect](./how-to-connect-install-express.md)
* [Installazione personalizzata di Azure AD Connect](./how-to-connect-install-custom.md)
* [Azure AD Connect: eseguire l'aggiornamento da una versione precedente alla versione più recente](./how-to-upgrade-previous-version.md)
* [Azure AD Connect: che cos'è il server di staging?](./plan-connect-topologies.md#staging-server)
* [Informazioni sul modulo ADConnectivityTool di PowerShell](./how-to-connect-adconnectivitytools.md)

## <a name="next-steps"></a>Passaggi successivi
- [Azure ad Connect sincronizzazione](how-to-connect-sync-whatis.md).
- [Informazioni sull'identità ibrida](whatis-hybrid-identity.md)