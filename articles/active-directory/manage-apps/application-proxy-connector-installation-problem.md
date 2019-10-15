---
title: Problemi di installazione del connettore dell'agente proxy dell'applicazione | Microsoft Docs
description: Come risolvere i problemi che potrebbero verificarsi durante l'installazione del connettore dell'agente proxy dell'applicazione
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: a35558b81d064680981bcf403a3584e3a3d00e4f
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311750"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Problemi di installazione del connettore dell'agente proxy dell'applicazione

Il connettore proxy dell'applicazione AAD di Microsoft è un componente di dominio interno che usa le connessioni in uscita per stabilire la connettività dall'endpoint disponibile del cloud al dominio interno.

## <a name="general-problem-areas-with-connector-installation"></a>Aree problematiche generali con l'installazione del connettore

Quando l'installazione di un connettore non riesce, la causa principale riguarda in genere una delle seguenti aree:

1.  **Connettività**: per completare un'installazione, è necessario che il nuovo connettore registri e definisca le future proprietà di attendibilità. Questa operazione viene eseguita tramite la connessione al servizio cloud proxy dell’applicazione AAD.

2.  **Definizione dell'attendibilità**: il nuovo connettore crea un certificato autofirmato ed effettua la registrazione al servizio cloud.

3.  **Autenticazione dell'amministratore**: durante l'installazione, l'utente deve fornire le credenziali di amministratore per completare l'installazione del connettore.

> [!NOTE]
> I log di installazione del connettore sono disponibili nella cartella% TEMP% e consentono di fornire informazioni aggiuntive su ciò che causa un errore di installazione.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Verificare la connettività al servizio proxy dell'applicazione cloud e alla pagina di accesso Microsoft

**Obiettivo:** verificare che il computer di connessione possa connettersi all'endpoint di registrazione proxy dell'applicazione AAD, nonché alla pagina di accesso Microsoft.

1.  Sul server del connettore, eseguire un test di porta utilizzando [Telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) o un altro strumento di test della porta per verificare che le porte 443 e 80 siano aperte.

2.  Se una di queste porte ha esito negativo, verificare che il firewall o il proxy back-end abbia accesso ai domini e alle porte richiesti. vedere [preparare l'ambiente locale](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

3.  Aprire un browser (scheda separata) e passare alla pagina Web <https://login.microsoftonline.com>, quindi assicurarsi di poter accedere a tale pagina.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>Verificare che il computer e i componenti di back-end supportino il certificato di attendibilità proxy dell'applicazione

**Obiettivo:** verificare che il computer di connessione, il proxy e il firewall di back-end possano supportare il certificato creato dal connettore per l'attendibilità futura.

>[!NOTE]
>Il connettore tenta di creare un certificato SHA512 supportato da TLS1.2. Se il computer o il firewall e il proxy back-end non supportano TLS 1.2, l'installazione non riesce.
>
>

**Per risolvere il problema:**

1.  Verificare che il computer supporti TLS1.2: tutte le versioni di Windows successive alla 2012 R2 dovrebbero supportare TLS 1.2. Se la versione del computer di connessione è 2012 R2 o precedente, assicurarsi che vi siano installate le Knowledge Base seguenti: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Contattare l'amministratore di rete e chiedere di verificare che il proxy e il firewall di back-end non blocchino SHA512 per il traffico in uscita.

## <a name="verify-admin-is-used-to-install-the-connector"></a>Verificare che il connettore venga installato da un utente con il ruolo di amministratore

**Obiettivo:** verificare che l'utente che tenta di installare il connettore sia un amministratore con le credenziali corrette. Attualmente, l'utente deve essere almeno un amministratore dell'applicazione affinché l'installazione abbia esito positivo.

**Per verificare che le credenziali siano corrette:**

Connettersi a <https://login.microsoftonline.com> e usare le stesse credenziali. Assicurarsi che l'accesso venga completato. È possibile controllare il ruolo dell'utente in **Azure Active Directory**  - &gt; **Utenti e gruppi**  - &gt; **Tutti gli utenti**. 

Selezionare l'account utente, quindi "Ruolo directory" dal menu che viene visualizzato. Verificare che il ruolo selezionato sia "amministratore applicazione". Se non si riesce ad accedere a nessuna delle pagine con questa procedura, non si ha il ruolo necessario.

## <a name="next-steps"></a>Passaggi successivi
[Comprendere i connettori del proxy applicazione di Azure AD](application-proxy-connectors.md)
