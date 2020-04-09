---
title: 'Lavorare in remoto con P2S: Gateway VPN di Azure'
description: Questa pagina descrive come sfruttare il gateway VPN per consentire di lavorare in remoto a causa della pandemia COVID-19.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: alzam
ms.openlocfilehash: 2d07a13c654f30e48c37d2e8d3e801166e26f4f4
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886586"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>Lavoro remoto con il gateway VPN di Azure da punto a sito

>[!NOTE]
>Questo articolo descrive come sfruttare Gateway VPN di Azure, Azure, la rete Microsoft e l'ecosistema dei partner di Azure per lavorare in remoto e ridurre i problemi di rete che si trovano ad affrontare a causa di una crisi di COVID-19.
>

In questo articolo vengono descritte le opzioni disponibili per le organizzazioni per configurare l'accesso remoto per gli utenti o per integrare le soluzioni esistenti con capacità aggiuntiva durante l'epidemia di COVID-19.

La soluzione da punto a sito di Azure è basata sul cloud e può essere eseguito rapidamente il provisioning per soddisfare l'aumento della richiesta degli utenti di lavorare da casa. Può scalare facilmente e spegnere altrettanto facilmente e rapidamente quando la maggiore capacità non è più necessaria.

## <a name="about-point-to-site-vpn"></a><a name="p2s"></a>Informazioni sulla VPN da punto a sito

Una connessione gateway VPN da punto a sito (P2S) consente di creare una connessione sicura alla rete virtuale da un singolo computer client. Una connessione da punto a sito viene stabilita avviandola dal computer client. Questa soluzione è utile per i telelavoratori che desiderano connettersi a reti virtuali di Azure o data center locali da una postazione remota, ad esempio da casa o da una conferenza. In questo articolo viene descritto come consentire agli utenti di lavorare in remoto in base a vari scenari.

La tabella seguente mostra i sistemi operativi client e le opzioni di autenticazione disponibili. Sarebbe utile selezionare il metodo di autenticazione in base al sistema operativo client già in uso. Ad esempio, selezionare OpenVPN con autenticazione basata su certificati se si dispone di una combinazione di sistemi operativi client che devono connettersi. Inoltre, tenere presente che la VPN da punto a sito è supportata solo nei gateway VPN basati su route.

![da punto a sito](./media/working-remotely-support/ostable.png "OS")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a><a name="scenario1"></a>Scenario 1 - Gli utenti devono accedere solo alle risorse in AzureScenario 1 - Users need access to resources in Azure only

In this scenario, the remote users only need to access to resources that are in Azure.

![da punto a sito](./media/working-remotely-support/scenario1.png "Scenario 1")

A livello generale, i passaggi seguenti sono necessari per consentire agli utenti di connettersi alle risorse di Azure in modo sicuro:At a high level, the following steps are needed to enable users to connect to Azure resources securely:

1. Creare un gateway di rete virtuale (se non ne esiste uno).
2. Configurare la VPN da punto a sito nel gateway.

   * Per l'autenticazione del certificato, seguire [questo collegamento](vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw).
   * Per OpenVPN, seguire [questo collegamento](vpn-gateway-howto-openvpn.md).
   * Per l'autenticazione di Azure AD, seguire [questo collegamento](openvpn-azure-ad-tenant.md).
   * Per la risoluzione dei problemi relativi alle connessioni da punto a sito, seguire [questo collegamento](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
3. Scaricare e distribuire la configurazione del client VPN.
4. Distribuire i certificati (se è selezionata l'autenticazione del certificato) ai client.
5. Connettersi ad Azure VPN.

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a><a name="scenario2"></a>Scenario 2 - Gli utenti devono accedere alle risorse in Azure e/o le risorse ponfileScenario 2 - Users need access to resources in Azure and/or on-prem resources

In questo scenario, gli utenti remoti devono accedere alle risorse che si trovano in Azure e nei data center locali.

![da punto a sito](./media/working-remotely-support/scenario2.png "Scenario 2")

A livello generale, i passaggi seguenti sono necessari per consentire agli utenti di connettersi alle risorse di Azure in modo sicuro:At a high level, the following steps are needed to enable users to connect to Azure resources securely:

1. Creare un gateway di rete virtuale (se non ne esiste uno).
2. Configurare la VPN da punto a sito nel gateway (vedere [Scenario 1](#scenario1)).
3. Configurare un tunnel da sito a sito nel gateway di rete virtuale di Azure con BGP abilitato.
4. Configurare il dispositivo locale per connettersi al gateway di rete virtuale di Azure.Configure the on-premises device to connect to Azure virtual network gateway.
5. Scaricare il profilo da punto a sito dal portale di Azure e distribuirlo ai clientDownload the point-to-site profile from the Azure portal and distribute to clients

Per informazioni su come configurare un tunnel VPN da sito a sito, vedere [questo collegamento](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>Domande frequenti per l'autenticazione del certificato di Azure nativo

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>Domande frequenti per l'autenticazione RADIUS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Configurare una connessione P2S - Autenticazione di Azure ADConfigure a P2S connection - Azure AD authentication](openvpn-azure-ad-tenant.md)

* [Configurare una connessione da punto a sito usando l'autenticazione RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurare una connessione da punto a sito usando l'autenticazione del certificato nativa di Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" è un marchio registrato di OpenVPN Inc.**