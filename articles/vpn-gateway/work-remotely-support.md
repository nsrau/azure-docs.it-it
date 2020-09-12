---
title: 'Lavorare in remoto con P2S: gateway VPN di Azure'
description: Questa pagina descrive come sfruttare il gateway VPN per abilitare il funzionamento in modalità remota a causa della pandemia COVID-19.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: a70f7ccd1c3ca078bab0ff473283b505c0ded316
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89441562"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>Lavorare in remoto con il gateway VPN di Azure da punto a sito

>[!NOTE]
>Questo articolo descrive come sfruttare il gateway VPN di Azure, Azure, Microsoft Network e l'ecosistema di partner di Azure per lavorare in modalità remota e mitigare i problemi di rete da affrontare a causa della crisi COVID-19.
>

Questo articolo descrive le opzioni disponibili per le organizzazioni per configurare l'accesso remoto per gli utenti o per integrare le soluzioni esistenti con capacità aggiuntiva durante l'epidemia COVID-19.

La soluzione da punto a sito di Azure è basata sul cloud ed è possibile eseguirne rapidamente il provisioning per soddisfare la maggiore richiesta di utenti di lavorare da casa. La scalabilità verticale può risultare semplice e disattivata in modo semplice e rapido quando la capacità aumentata non è più necessaria.

## <a name="about-point-to-site-vpn"></a><a name="p2s"></a>Informazioni sulla VPN da punto a sito

Una connessione gateway VPN da punto a sito (P2S) consente di creare una connessione sicura alla rete virtuale da un singolo computer client. Una connessione da punto a sito viene stabilita avviandola dal computer client. Questa soluzione è utile per i telelavoro che vogliono connettersi ad Azure reti virtuali o ai data center locali da una posizione remota, ad esempio da casa o da una conferenza. Questo articolo descrive come consentire agli utenti di lavorare in modalità remota in base a diversi scenari.

Nella tabella seguente sono illustrati i sistemi operativi client e le opzioni di autenticazione disponibili. Potrebbe essere utile selezionare il metodo di autenticazione in base al sistema operativo client già in uso. Ad esempio, selezionare OpenVPN con autenticazione basata su certificati se si dispone di una combinazione di sistemi operativi client che devono connettersi. Si noti anche che la VPN da punto a sito è supportata solo nei gateway VPN basati su route.

![da punto a sito](./media/working-remotely-support/ostable.png "OS")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a><a name="scenario1"></a>Scenario 1: gli utenti devono accedere solo alle risorse in Azure

In questo scenario, gli utenti remoti devono solo accedere alle risorse in Azure.

![da punto a sito](./media/working-remotely-support/scenario1.png "Scenario 1")

A livello generale, sono necessari i passaggi seguenti per consentire agli utenti di connettersi alle risorse di Azure in modo sicuro:

1. Creare un gateway di rete virtuale, se non ne esiste uno.
2. Configurare la VPN da punto a sito sul gateway.

   * Per l'autenticazione del certificato, seguire [questo collegamento](vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw).
   * Per OpenVPN, seguire [questo collegamento](vpn-gateway-howto-openvpn.md).
   * Per Azure AD autenticazione, seguire [questo collegamento](openvpn-azure-ad-tenant.md).
   * Per la risoluzione dei problemi relativi alle connessioni da punto a sito, seguire [questo collegamento](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
3. Scaricare e distribuire la configurazione del client VPN.
4. Distribuire i certificati (se è stata selezionata l'autenticazione del certificato) ai client.
5. Connettersi alla VPN di Azure.

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a><a name="scenario2"></a>Scenario 2: gli utenti devono accedere alle risorse in Azure e/o nelle risorse locali

In questo scenario, gli utenti remoti devono accedere alle risorse che si trovano in Azure e nei data center locali.

![da punto a sito](./media/working-remotely-support/scenario2.png "Scenario 2")

A livello generale, sono necessari i passaggi seguenti per consentire agli utenti di connettersi alle risorse di Azure in modo sicuro:

1. Creare un gateway di rete virtuale, se non ne esiste uno.
2. Configurare la VPN da punto a sito nel gateway (vedere lo [scenario 1](#scenario1)).
3. Configurare un tunnel da sito a sito nel gateway di rete virtuale di Azure con BGP abilitato.
4. Configurare il dispositivo locale per la connessione al gateway di rete virtuale di Azure.
5. Scaricare il profilo da punto a sito dal portale di Azure e distribuirlo ai client

Per informazioni su come configurare un tunnel VPN da sito a sito, vedere [questo collegamento](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>Domande frequenti per l'autenticazione del certificato di Azure nativo

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>Domande frequenti per l'autenticazione RADIUS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Configurare una connessione P2S-autenticazione Azure AD](openvpn-azure-ad-tenant.md)

* [Configurare una connessione da punto a sito usando l'autenticazione RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurare una connessione da punto a sito usando l'autenticazione del certificato nativa di Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" è un marchio di OpenVPN Inc.**