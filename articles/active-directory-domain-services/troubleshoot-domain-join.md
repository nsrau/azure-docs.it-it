---
title: Risolvere i problemi relativi all'aggiunta a un dominio con Servizi di dominio Azure AD . Documenti Microsoft
description: Informazioni su come risolvere i problemi comuni quando si tenta di aggiungere un dominio a una macchina virtuale o connettere un'applicazione a Servizi di dominio Azure Active Directory e non è possibile connettersi o eseguire l'autenticazione al dominio gestito.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: f187dba4eace61695a72e4b7b08731e65ff0d7f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299109"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-ad-domain-services-managed-domain"></a>Risolvere i problemi di aggiunta a un dominio con un dominio gestito di Servizi di dominio Azure ADTroubleshoot domain-join problems with an Azure AD Domain Services managed domain

Quando si tenta di aggiungere una macchina virtuale (VM) o connettere un'applicazione a un dominio gestito di servizi di dominio Azure Active Directory (AD DS), è possibile che venga visualizzato un errore che non è possibile eseguire questa operazione. Per risolvere i problemi di aggiunta al dominio, esaminare i punti seguenti in cui si è verificato un problema:

* Se non si riceve una richiesta di autenticazione, la macchina virtuale o l'applicazione non può connettersi al dominio gestito di Servizi di dominio Active Directory di Azure.If you don't receive an authentication prompt, the VM or application can't connect to the Azure AD DS managed domain.
    * Iniziare a risolvere i problemi di [connettività per l'aggiunta a un dominio](#connectivity-issues-for-domain-join).
* Se viene visualizzato un errore durante l'autenticazione, la connessione al dominio gestito di Servizi di dominio Active Directory di Azure ha esito positivo.
    * Iniziare a risolvere i [problemi relativi alle credenziali durante l'aggiunta al dominio](#credentials-related-issues-during-domain-join).

## <a name="connectivity-issues-for-domain-join"></a>Problemi di connettività per l'aggiunta a un dominio

Se la macchina virtuale non riesce a trovare il dominio gestito di Azure AD DS, si verifica in genere un problema di connessione di rete o di configurazione. Esaminare la seguente procedura di risoluzione dei problemi per individuare e risolvere il problema:

1. Verificare che la macchina virtuale sia connessa alla stessa rete virtuale con peered abilitata per Servizi di dominio Active Directory di Azure.Ensure the VM is connected to the same, or a peered, virtual network that's enabled for Azure AD DS. In caso contrario, la macchina virtuale non riesce a trovare e connettersi al dominio per l'aggiunta.
    * Se la macchina virtuale non è connessa alla stessa rete virtuale, verificare che il peering di rete virtuale o la connessione VPN sia *attivo* o *connesso* per consentire il corretto flusso del traffico.
1. Provare a eseguire il ping del dominio usando il nome `ping aaddscontoso.com`di dominio del dominio gestito di Servizi di dominio Active Directory di Azure, ad esempio .
    * Se la risposta ping non riesce, provare a eseguire il ping degli indirizzi IP per il dominio `ping 10.0.0.4`visualizzato nella pagina di panoramica del portale per il dominio gestito di Servizi di dominio Active Directory di Azure, ad esempio .
    * Se è possibile eseguire correttamente il ping dell'indirizzo IP ma non del dominio, DNS potrebbe non essere configurato correttamente. Assicurarsi di aver configurato i server DNS del dominio gestito di Servizi di dominio Active Directory di Azure per la rete virtuale.
1. Provare a scaricare la cache del resolver `ipconfig /flushdns`DNS nella macchina virtuale, ad esempio .

### <a name="network-security-group-nsg-configuration"></a>Configurazione del gruppo di sicurezza di rete

Quando si crea un dominio gestito di Servizi di dominio Active Directory di Azure, viene creato anche un gruppo di sicurezza di rete con le regole appropriate per il corretto funzionamento del dominio. Se si modificano o si creano regole aggiuntive per i gruppi di sicurezza di rete, è possibile bloccare inavvertitamente le porte necessarie affinché Servizi di dominio Azure fornisca servizi di connessione e autenticazione. Queste regole del gruppo di sicurezza di rete possono causare problemi come la sincronizzazione delle password non completata, gli utenti non è in grado di accedere o problemi di aggiunta al dominio.

Se i problemi di connessione persistono, vedere la procedura di risoluzione dei problemi seguente:

1. Controllare lo stato di integrità del dominio gestito di Servizi di dominio Active Directory di Azure nel portale di Azure.Check the health status of your Azure AD DS managed domain in the Azure portal. Se si dispone di un avviso per *AADDS001*, una regola del gruppo di sicurezza di rete blocca l'accesso.
1. Esaminare le [porte e le regole][network-ports]dei gruppi di sicurezza di rete necessarie . Assicurarsi che nessuna regola del gruppo di sicurezza di rete applicata alla macchina virtuale o alla rete virtuale da cui ci si connette blocchi queste porte di rete.
1. Una volta risolti i problemi di configurazione del gruppo di sicurezza di rete, l'avviso *AADDS001* scompare dalla pagina di integrità in circa 2 ore. Con la connettività di rete ora disponibile, provare a aggiungere di nuovo alla macchina virtuale.

## <a name="credentials-related-issues-during-domain-join"></a>Problemi relativi alle credenziali durante l'aggiunta al dominioCredentials-related issues during domain-join

Se viene visualizzata una finestra di dialogo che richiede le credenziali per l'aggiunta al dominio gestito di Servizi di dominio Active Directory di Azure, la macchina virtuale è in grado di connettersi al dominio usando la rete virtuale di Azure.If you get a dialog box that asks for credentials to join the Azure AD DS managed domain, the VM is able to connect to the domain using the Azure virtual network. Il processo di aggiunta al dominio non riesce durante l'autenticazione al dominio o l'autorizzazione a completare il processo di aggiunta al dominio utilizzando le credenziali fornite.

Per risolvere i problemi relativi alle credenziali, esaminare la procedura di risoluzione dei problemi seguente:To troubleshoot credentials-related issues, review the following troubleshooting steps:

1. Provare a usare il formato UPN per specificare le credenziali, ad esempio `dee@aaddscontoso.onmicrosoft.com`. Assicurarsi che questo UPN sia configurato correttamente in Azure AD.
    * Il *SAMAccountName* per l'account può essere generato automaticamente se sono presenti più utenti con lo stesso prefisso UPN nel tenant o se il prefisso UPN è eccessivamente lungo. Pertanto, il formato *SAMAccountName* per l'account potrebbe essere diverso da quello previsto o utilizzato nel dominio locale.
1. Provare a usare le credenziali per un account utente che fa parte del dominio gestito di Azure AD DS per aggiungere macchine virtuali al dominio gestito.
1. Assicurarsi di aver [abilitato la sincronizzazione delle password][enable-password-sync] e di aver aspettato abbastanza a lungo per completare la sincronizzazione iniziale delle password.

## <a name="next-steps"></a>Passaggi successivi

Per una conoscenza più approfondita dei processi di Active Directory nell'ambito dell'operazione di aggiunta al dominio, vedere Problemi di [aggiunta e autenticazione][join-authentication-issues].

Se si verificano ancora problemi durante l'aggiunta della macchina virtuale al dominio gestito di Azure AD DS, trovare la Guida e aprire un ticket di [supporto per Azure Active Directory.][azure-ad-support]

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
