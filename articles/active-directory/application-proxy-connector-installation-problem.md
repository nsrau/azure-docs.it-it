---
title: Problemi di installazione del connettore dell'agente proxy dell'applicazione | Microsoft Docs
description: Come risolvere i problemi che potrebbero verificarsi durante l'installazione del connettore dell'agente proxy dell'applicazione
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 8fbd707b6708661ab0d655afadff2b18694a981e
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2018
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Problemi di installazione del connettore dell'agente proxy dell'applicazione

Il connettore proxy dell'applicazione AAD di Microsoft è un componente di dominio interno che usa le connessioni in uscita per stabilire la connettività dall'endpoint disponibile del cloud al dominio interno.

## <a name="general-problem-areas-with-connector-installation"></a>Aree problematiche generali con l'installazione del connettore

Quando l'installazione di un connettore non riesce, la causa principale riguarda in genere una delle seguenti aree:

1.  **Connettività**: per completare un'installazione, è necessario che il nuovo connettore registri e definisca le future proprietà di attendibilità. Questa operazione viene eseguita tramite la connessione al servizio cloud proxy dell’applicazione AAD.

2.  **Definizione dell'attendibilità**: il nuovo connettore crea un certificato autofirmato ed effettua la registrazione al servizio cloud.

3.  **Autenticazione dell'amministratore**: durante l'installazione, l'utente deve fornire le credenziali di amministratore per completare l'installazione del connettore.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Verificare la connettività al servizio proxy dell'applicazione cloud e alla pagina di accesso Microsoft

**Obiettivo:** verificare che il computer di connessione possa connettersi all'endpoint di registrazione proxy dell'applicazione AAD, nonché alla pagina di accesso Microsoft.

1.  Aprire un browser e passare alla pagina Web seguente: <https://aadap-portcheck.connectorporttest.msappproxy.net>, quindi verificare che la connettività al data center degli Stati Uniti orientali e Stati Uniti centrali con le porte 80 e 443 sia funzionante.

2.  Se la connessione a una di queste porte non funziona (non è presente un segno di spunta verde), verificare che \*.msappproxy.net con le porte 80 e 443 sia definito correttamente per il firewall o proxy di back-end.

3.  Aprire un browser (scheda separata) e passare alla pagina Web seguente: <https://login.microsoftonline.com>, quindi assicurarsi che sia possibile accedere a tale pagina.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>Verificare che il computer e i componenti di back-end supportino il certificato di attendibilità proxy dell'applicazione

**Obiettivo:** verificare che il computer di connessione, il proxy e il firewall di back-end possano supportare il certificato creato dal connettore per l'attendibilità futura.

>[!NOTE]
>Il connettore tenta di creare un certificato SHA512 supportato da TLS1.2. Se il computer o il proxy e il firewall di back-end non supportano TLS1.2, l'installazione non viene completata.
>
>

**Per risolvere il problema:**

1.  Verificare che il computer supporti TLS1.2: tutte le versioni di Windows successive alla 2012 R2 dovrebbero supportare TLS 1.2. Se la versione del computer di connessione di cui si dispone è 2012 R2 o precedente, assicurarsi che le seguenti Knowledge Base siano installate: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Contattare l'amministratore di rete e chiedere di verificare che il proxy e il firewall di back-end non blocchino SHA512 per il traffico in uscita.

## <a name="verify-admin-is-used-to-install-the-connector"></a>Verificare che il connettore venga installato da un utente con il ruolo di amministratore

**Obiettivo:** verificare che l'utente che tenta di installare il connettore sia un amministratore con le credenziali corrette. Al momento per completare l'installazione è necessario che l'utente sia un amministratore globale.

**Per verificare che le credenziali siano corrette:**

Connettersi a <https://login.microsoftonline.com> e usare le stesse credenziali. Assicurarsi che l'accesso venga completato. È possibile controllare il ruolo dell'utente in **Azure Active Directory**  - &gt; **Utenti e gruppi**  - &gt; **Tutti gli utenti**. 

Selezionare l'account utente, quindi "Ruolo directory" dal menu che viene visualizzato. Verificare che il ruolo selezionato sia "Amministratore globale". Se non si è in grado di accedere a nessuna delle pagine con questa procedura, non si è un amministratore globale.

## <a name="next-steps"></a>Passaggi successivi
[Comprendere i connettori del proxy applicazione di Azure AD](application-proxy-understand-connectors.md)
