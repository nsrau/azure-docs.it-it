---
title: 'Gateway VPN: risolvere i problemi relativi al client VPN - Autenticazione di Azure AD'
description: Risolvere i problemi relativi ai client di autenticazione di Azure AD del gateway VPN P2STroubleshoot VPN Gateway P2S Azure AD authentication clients
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 8871e92f0911c4d3cbcc1772bef1daeb5c70b5d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74151962"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>Risolvere i problemi relativi a un client VPN di autenticazione di Azure ADTroubleshoot an Azure AD authentication VPN client

Questo articolo consente di risolvere i problemi di un client VPN per connettersi a una rete virtuale usando la VPN da punto a sito e l'autenticazione di Azure Active Directory.This article helps you troubleshoot a VPN client to connect to a virtual network using Point-to-Site VPN and Azure Active Directory authentication.

## <a name="view-status-log"></a><a name="status"></a>Visualizza registro di stato

Visualizzare il registro di stato per i messaggi di errore.

![log](./media/troubleshoot-ad-vpn-client/1.png)

1. Fare clic sull'icona delle frecce nell'angolo inferiore destro della finestra del client per visualizzare i **registri di stato**.
2. Controllare i registri per gli errori che possono indicare il problema.
3. I messaggi di errore vengono visualizzati in rosso.

## <a name="clear-sign-in-information"></a><a name="clear"></a>Cancellare le informazioni di accesso

Cancellare le informazioni di accesso.

![sign in](./media/troubleshoot-ad-vpn-client/2.png)

1. Selezionare il ... accanto al profilo che si desidera risolvere. Selezionare **Configura -> Cancella account salvato**.
2. Selezionare **Salva**.
3. Provare a connettersi.
4. Se la connessione continua a non riuscire, passare alla sezione successiva.

## <a name="run-diagnostics"></a><a name="diagnostics"></a>Eseguire la diagnostica

Eseguire la diagnostica sul client VPN.

![diagnostica](./media/troubleshoot-ad-vpn-client/3.png)

1. Fare clic sul **pulsante ...** accanto al profilo su cui si desidera eseguire la diagnostica. Selezionare **Diagnostica -> Diagnosi esecuzione**.
2. Il cliente eseguirà una serie di test e visualizzerà il risultato del test

   * Accesso a Internet – Verifica se il client dispone di connettività Internet
   * Credenziali client: verificare se l'endpoint di autenticazione di Azure Active Directory è raggiungibileClient Credentials – Check to see if the Azure Active Directory authentication endpoint is reachable
   * Server resolvable – Contatta il server DNS per risolvere l'indirizzo IP del server VPN configurato
   * Server raggiungibile – Controlla se il server VPN risponde o no
3. Se uno dei test ha esito negativo, contattare l'amministratore di rete per risolvere il problema.
4. Nella sezione successiva viene illustrato come raccogliere i log, se necessario.

## <a name="collect-client-log-files"></a><a name="logfiles"></a>Raccogliere i file di registro del clientCollect client log files

Raccogliere i file di registro del client VPN. I file di registro possono essere inviati al supporto/amministratore tramite un metodo di vostra scelta. Ad esempio, e-mail.

1. Fare clic sul pulsante "..." accanto al profilo su cui si desidera eseguire la diagnostica. Selezionare **Diagnostica-> Mostra directory registri**.

   ![mostrare i registri](./media/troubleshoot-ad-vpn-client/4.png)
2. Esplora risorse verrà aperto nella cartella che contiene i file di registro.

   ![file di visualizzazione](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere Creare un tenant di [Azure Active Directory per le connessioni P2S Open VPN che usano l'autenticazione](openvpn-azure-ad-tenant.md)di Azure AD.