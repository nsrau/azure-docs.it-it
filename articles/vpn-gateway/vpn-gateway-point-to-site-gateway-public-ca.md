---
title: Transizione ai certificati della CA pubblici per i gateway P2S | Gateway VPN di Azure | Microsoft Docs
description: Questo articolo illustra come eseguire correttamente la transizione ai nuovi certificati della CA pubblici per i gateway P2S.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: cherylmc
ms.openlocfilehash: ea3ee00e60ae5eaff5c7220e9502da11358c8793
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65786735"
---
# <a name="transition-to-a-public-ca-gateway-certificate-for-p2s"></a>Passaggio a un certificato del gateway CA pubblico per la connessione da punto a sito

Gateway VPN di Azure non rilascia più certificati autofirmati a livello di Azure ai gateway per le connessioni da punto a sito. I certificati rilasciati vengono ora firmati da un'Autorità di certificazione (CA) pubblica. Tuttavia, alcuni dei gateway meno recenti potrebbero usare ancora certificati autofirmati. Questi certificati autofirmati sono prossimi alla scadenza, quindi è necessario eseguire la transizione ai certificati della CA pubblici.

>[!NOTE]
> * I certificati autofirmati usati per l'autenticazione client per le connessioni da punto a sito non sono interessati da questa modifica ai certificati a livello di Azure. È possibile continuare a emettere e usare certificati autofirmati come di consueto.
>

I certificati in questo contesto sono un certificato aggiuntivo a livello di Azure. Non si tratta delle catene di certificati che si usano quando si generano i propri certificati radice autofirmati e i certificati client per l'autenticazione. Tali certificati non subiscono modifiche e scadranno il giorno previsto quando sono stati generati.

In passato un certificato autofirmato per il gateway emesso in background da Azure doveva essere aggiornato ogni 18 mesi. I file di configurazione del client VPN dovevano quindi essere generati e ridistribuiti a tutti i client P2S. Il passaggio ai certificati della CA pubblici elimina questa limitazione. Oltre alla transizione per i certificati, questa modifica offre anche miglioramenti a livello di piattaforma, metriche e stabilità.

Questa modifica interessa solo i gateway meno recenti. Se è necessario eseguire la transizione del certificato del gateway in uso, si riceverà una comunicazione o un avviso popup nel portale di Azure. È possibile controllare se il gateway in uso è interessato dalla transizione seguendo la procedura descritta in questo articolo.

> [!IMPORTANT]
> **Questa transizione è stata completata il 12 maggio 2019.**
>
> La transizione è pianificata per il 12 marzo 2019 a partire dalle 18:00 UTC. Se si preferisce una finestra temporale diversa, è possibile creare un caso di supporto. Creare e finalizzare la richiesta con almeno 24 ore di anticipo.  È possibile richiedere una delle finestre seguenti:
>
> * 06:00 UTC del 25 febbraio
> * 18:00 UTC del 25 febbraio
> * 06:00 UTC del 1° marzo
> * 18:00 UTC del 1° marzo
>
> Tutti i gateway rimanenti passeranno marzo 12 2019 partire dall'ora UTC 18.00.
>
> Una volta completato il processo di transizione de gateway, i clienti riceveranno un messaggio di posta elettronica.
> 

## <a name="1-verify-your-certificate"></a>1. Verificare il certificato

### <a name="resource-manager"></a>Gestione risorse

1. Verificare se si è interessati da questo aggiornamento. Scaricare la configurazione corrente del client VPN seguendo la procedura descritta in [questo articolo](point-to-site-vpn-client-configuration-azure-cert.md).

2. Aprire o estrarre il file ZIP e passare alla cartella "Generic". La cartella Generic contiene due file, uno dei quali è *VPNSettings.xml*.
3. Aprire *VPNSettings.xml* in un editor/visualizzatore XML qualsiasi. Nel file XML cercare i campi seguenti:

   * `<ServerCertRootCn>DigiCert Global Root CA</ServerCertRootCn>`
   * `<ServerCertIssuerCn>DigiCert Global Root CA</ServerCertIssuerCn>`
4. Se il valore di *ServerCertRotCn* e *ServerCertIssuerCn* è "DigiCert Global Root CA", non si è interessati da questo aggiornamento e non è necessario eseguire la procedura illustrata in questo articolo. Se invece il valore è diverso, il certificato del gateway in uso è interessato dall'aggiornamento, quindi ne verrà eseguita la transizione.

### <a name="classic"></a>Classica

1. In un computer client, passare al percorso `%appdata%/Microsoft/Network/Connections/Cm/<gatewayID>`. Nella cartella dell'ID del gateway è possibile visualizzare il certificato.
2. Nella scheda Generale per il certificato, verificare che l'autorità emittente sia "DigiCert Global Root CA". Se il valore dell'autorità emittente è diverso, il certificato del gateway in uso è interessato dall'aggiornamento, quindi ne verrà eseguita la transizione.

## <a name="2-check-certificate-transition-schedule"></a>2. Verificare la pianificazione della transizione del certificato

Se il certificato è interessato dall'aggiornamento, verrà eseguita la transizione del certificato del gateway in uso. Per le tempistiche della transizione, vedere la nota **Importante**. Dopo l'aggiornamento, i client P2S non potranno più connettersi usando il vecchio profilo. È necessario generare nuovi profili per i client VPN e installarli nei client.

## <a name="3-generate-vpn-client-configuration-profile"></a>3. Generare il profilo di configurazione del client VPN

Completata la transizione del certificato, scaricare il nuovo profilo VPN (file di configurazione del client VPN) dal portale di Azure. Per la procedura da eseguire, vedere [Creare e installare i file di configurazione dei client VPN](point-to-site-vpn-client-configuration-azure-cert.md). Non è necessario generare nuovi certificati client.

## <a name="4-deploy-vpn-client-profile"></a>4. Distribuire il profilo del client VPN

Distribuire il nuovo profilo a tutti i client VPN da punto a sito. I client VPN perderanno la connettività finché il nuovo profilo VPN per le connessioni da punto a sito non sarà stato scaricato e distribuito nei dispositivi client. I certificati client che sono già installati nei computer client VPN non devono essere rilasciati di nuovo.

## <a name="5-connect-the-vpn-client"></a>5. Connettere il client VPN

Connettersi ad Azure dal client VPN come di consueto.
