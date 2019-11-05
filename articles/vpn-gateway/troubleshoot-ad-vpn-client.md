---
title: "Risolvere i problemi relativi a un client VPN per l'autenticazione Azure AD: gateway VPN di Azure | Microsoft Docs"
description: Risolvere i problemi del gateway VPN P2S Azure AD client di autenticazione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: e239f283d5065e032e98e87a72a4bfe9fbafbdb1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517280"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>Risolvere i problemi relativi a un client VPN per l'autenticazione Azure AD

Questo articolo illustra come risolvere i problemi relativi a un client VPN per connettersi a una rete virtuale usando la VPN da punto a sito e l'autenticazione Azure Active Directory.

## <a name="status"></a>Visualizza log di stato

Visualizzare il log di stato per i messaggi di errore.

![log](./media/troubleshoot-ad-vpn-client/1.png)

1. Fare clic sull'icona delle frecce nell'angolo inferiore destro della finestra del client per visualizzare i **log di stato**.
2. Controllare se nei registri sono presenti errori che possono indicare il problema.
3. I messaggi di errore vengono visualizzati in rosso.

## <a name="clear"></a>Cancella informazioni di accesso

Cancellare le informazioni di accesso.

![sign in](./media/troubleshoot-ad-vpn-client/2.png)

1. Selezionare il... accanto al profilo di cui si desidera eseguire la risoluzione dei problemi. Selezionare **Configura-> Cancella account salvato**.
2. Selezionare **Salva**.
3. Provare a connettersi.
4. Se la connessione non riesce, continuare con la sezione successiva.

## <a name="diagnostics"></a>Esegui diagnostica

Eseguire la diagnostica sul client VPN.

![diagnostica](./media/troubleshoot-ad-vpn-client/3.png)

1. Fare clic su **...** accanto al profilo su cui si desidera eseguire la diagnostica. Selezionare diagnosi **-> eseguire la diagnosi**.
2. Il client eseguirà una serie di test e visualizzerà il risultato del test

   * Accesso a Internet: verifica se il client dispone di connettività Internet
   * Credenziali client: verificare se l'endpoint di autenticazione Azure Active Directory è raggiungibile
   * Risoluzione del server: Contatta il server DNS per risolvere l'indirizzo IP del server VPN configurato
   * Server raggiungibile: verifica se il server VPN risponde o meno
3. Se uno dei test ha esito negativo, contattare l'amministratore di rete per risolvere il problema.
4. La sezione successiva illustra come raccogliere i log, se necessario.

## <a name="logfiles"></a>Raccogli file di log client

Raccogliere i file di log del client VPN. I file di log possono essere inviati al supporto/amministratore tramite un metodo a scelta. Ad esempio, posta elettronica.

1. Fare clic su "..." accanto al profilo su cui si desidera eseguire la diagnostica. Selezionare **diagnosi-> Mostra directory dei log**.

   ![Mostra log](./media/troubleshoot-ad-vpn-client/4.png)
2. Esplora risorse apre la cartella che contiene i file di log.

   ![Visualizza file](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [creare un tenant di Azure Active Directory per le connessioni VPN aperte P2S che usano l'autenticazione Azure ad](openvpn-azure-ad-tenant.md).