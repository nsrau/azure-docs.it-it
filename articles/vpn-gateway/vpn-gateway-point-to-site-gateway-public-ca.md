---
title: Transizione dai certificati autofirmati ai certificati della CA pubblici per i gateway P2S | Gateway VPN di Azure | Microsoft Docs
description: Questo articolo illustra come eseguire correttamente la transizione ai nuovi certificati della CA pubblici per i gateway P2S.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: cherylmc
ms.openlocfilehash: ac1ae4125418a9c0b3e9587cd03a44e752ac8f82
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56236958"
---
# <a name="transition-from-self-signed-to-public-ca-certificates-for-p2s-gateways"></a>Transizione dai certificati autofirmati ai certificati della CA pubblici per i gateway P2S

Gateway VPN di Azure non rilascia più certificati autofirmati ai gateway per le connessioni da punto a sito. I certificati rilasciati vengono ora firmati da un'Autorità di certificazione (CA) pubblica. Tuttavia, i gateway meno recenti potrebbero usare ancora certificati autofirmati. Questi certificati autofirmati sono prossimi alla scadenza, quindi è necessario eseguire la transizione ai certificati della CA pubblici.

In passato il certificato autofirmato per il gateway doveva essere aggiornato ogni 18 mesi. I file di configurazione del client VPN dovevano quindi essere generati e ridistribuiti a tutti i client P2S. Il passaggio ai certificati della CA pubblici elimina questa limitazione. Oltre alla transizione per i certificati, questa modifica offre anche miglioramenti a livello di piattaforma, metriche e stabilità.

Questa modifica interessa solo i gateway meno recenti. Se è necessario eseguire la transizione del certificato del gateway in uso, si riceverà una comunicazione o un avviso popup nel portale di Azure. È possibile controllare se il gateway in uso è interessato dalla transizione seguendo la procedura descritta in questo articolo.

>[!IMPORTANT]
>La transizione è pianificata per il 12 marzo 2019 a partire dalle 18:00 UTC. Se si preferisce una finestra temporale diversa, è possibile creare un caso di supporto. Creare e finalizzare la richiesta con almeno 24 ore di anticipo.  È possibile richiedere una delle finestre seguenti:
>
>* 06:00 UTC il 25 febbraio
>* 18:00 UTC il 25 febbraio
>* 06:00 UTC l'1 marzo
>* 18:00 UTC l'1 marzo
>
>**La transizione di tutti i gateway rimanenti verrà eseguita il 12 marzo 2019 a partire dalle 18:00 UTC**.
>

## <a name="1-verify-your-certificate"></a>1. Verificare il certificato

1. Verificare se si è interessati da questo aggiornamento. Scaricare la configurazione corrente del client VPN seguendo la procedura descritta in [questo articolo](point-to-site-vpn-client-configuration-azure-cert.md).

2. Aprire o estrarre il file ZIP e passare alla cartella "Generic". La cartella Generic contiene due file, uno dei quali è *VPNSettings.xml*.
3. Aprire *VPNSettings.xml* in un editor/visualizzatore XML qualsiasi. Nel file XML cercare i campi seguenti:

  * `<ServerCertRootCn>DigiCert Global Root CA</ServerCertRootCn>`
  * `<ServerCertIssuerCn>DigiCert Global Root CA</ServerCertIssuerCn>`
4. Se il valore di *ServerCertRotCn* e *ServerCertIssuerCn* è "DigiCert Global Root CA", non si è interessati da questo aggiornamento e non è necessario eseguire la procedura illustrata in questo articolo. Se invece il valore è diverso, il certificato del gateway in uso è interessato dall'aggiornamento, quindi ne verrà eseguita la transizione.

## <a name="2-check-certificate-transition-schedule"></a>2. Verificare la pianificazione della transizione del certificato

Se il certificato è interessato dall'aggiornamento, verrà eseguita la transizione del certificato del gateway in uso. Per le tempistiche della transizione, vedere la nota **Importante**. Dopo l'aggiornamento, i client P2S non potranno più connettersi usando il vecchio profilo. È necessario generare nuovi profili per i client VPN e installarli nei client.

## <a name="3-generate-vpn-client-configuration-profile"></a>3. Generare il profilo di configurazione del client VPN

Completata la transizione del certificato, scaricare il nuovo profilo VPN (file di configurazione del client VPN) dal portale di Azure. Per la procedura da eseguire, vedere [Creare e installare i file di configurazione dei client VPN](point-to-site-vpn-client-configuration-azure-cert.md). Non è necessario generare nuovi certificati client.

## <a name="4-deploy-vpn-client-profile"></a>4. Distribuire il profilo del client VPN

Distribuire il nuovo profilo a tutti i client VPN da punto a sito. I client VPN perderanno la connettività finché il nuovo profilo VPN per le connessioni da punto a sito non sarà stato scaricato e distribuito nei dispositivi client. I certificati client che sono già installati nei computer client VPN non devono essere rilasciati di nuovo.

## <a name="5-connect-the-vpn-client"></a>5. Connettere il client VPN

Connettersi ad Azure dal client VPN come di consueto.
