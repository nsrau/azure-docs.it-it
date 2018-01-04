---
title: Panoramica della sicurezza CA X.509 dell'hub IoT di Azure | Microsoft Docs
description: Panoramica - Come autenticare i dispositivi nell'hub IoT con i certificati della CA X.509.
services: iot-hub
documentationcenter: .net
author: eustacea
manager: arjmands
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 7497753cc27867b1fdb9635ba6613ac75b449090
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="device-authentication-using-x509-ca-certificates"></a>Autenticazione dei dispositivi con i certificati della CA X.509

In questo articolo viene descritto come usare i certificati della CA X.509 per autenticare i dispositivi che si connettono all'hub IoT.  Contenuto dell'articolo:

* Come ottenere un certificato della CA X.509
* Come registrare il certificato della CA X.509 all'hub IoT
* Come firmare i dispositivi con i certificati della CA X.509
* Come vengono autenticati i dispositivi firmati con i certificati della CA X.509

## <a name="overview"></a>Panoramica

La funzionalità CA X.509 consente di abilitare l'autenticazione dei dispositivi nell'hub IoT usando una CA. Questa procedura consente di semplificare in modo considerevole il processo di registrazione iniziale dei dispositivi e la logistica della catena di fornitura durante la produzione dei dispositivi stessi. [In questo articolo relativo a questo scenario specifico sono disponibili altre informazioni sui vantaggi derivanti dall'uso dei certificati della CA X.509](iot-hub-x509ca-concept.md) nell'ambito dell'autenticazione dei dispositivi.  È consigliabile leggere attentamente questo articolo prima di continuare perché vengono descritti in dettaglio e in sequenza tutti i passaggi necessari.

## <a name="prerequisite"></a>Prerequisito

Per usare la funzionalità CA X.509, è necessario disporre di un account dell'hub IoT.  [Informazioni su come creare un'istanza dell'hub IoT](iot-hub-csharp-csharp-getstarted.md) se non ne è già stata creata una.

## <a name="how-to-get-an-x509-ca-certificate"></a>Come ottenere un certificato della CA X.509

Il certificato della CA X.509 rappresenta l'elemento superiore della catena di certificati per ciascuno dei dispositivi disponibili.  È possibile acquistarne o crearne uno, a seconda di come si intende usarlo.

Per un ambiente di produzione, è consigliabile acquistare un certificato della CA X.509 da una CA radice pubblica. Il vantaggio dell'acquisto di un certificato della CA risiede nel fatto che la CA radice funge da terze parti attendibili a garanzia della legittimità dei dispositivi in uso. Prendere in considerazione questa opzione se si prevede che i dispositivi in uso siano membri di una rete IoT aperta e che interagiscano con prodotti o servizi di terze parti.

È anche possibile creare un certificato della CA X.509 autofirmato come prova o per l'uso in reti IoT chiuse.

Indipendentemente dalla modalità di acquisizione del certificato della CA X.509, assicurarsi di non divulgare mai la corrispondente chiave privata.  Ciò è necessario per garantire l'attendibilità del processo di autenticazione della CA X.509. 

Informazioni su come [creare un certificato della CA autofirmato](iot-hub-security-x509-create-certificates.md#createcerts) da usare come esempio nella descrizione di questa funzionalità.

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>Firmare i dispositivi nella catena di certificati

Il proprietario di un certificato della CA X.509 può firmare a livello di crittografia una CA intermedia, che a sua volta firma un'altra CA intermedia e così via, finché l'ultima CA intermedia conclude il processo firmando un dispositivo. Il risultato è una catena di certificati a cascata definita "catena di certificati". In uno scenario reale questo processo equivale alla delega dell'attendibilità dei dispositivi di firma. Questa delega è importante perché definisce una catena variabile di protezione a livello di crittografia che impedisce la condivisione delle chiavi di firma.

![img-generic-cert-chain-of-trust](./media/generic-cert-chain-of-trust.png)

Informazioni su come [creare una catena di certificati](iot-hub-security-x509-create-certificates.md#createcertchain) simile a quella creata durante il processo di firma dei dispositivi.

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>Come registrare il certificato della CA X.509 all'hub IoT

Registrare il certificato della CA X.509 nell'hub IoT in cui verrà usato per autenticare i dispositivi durante la connessione e la registrazione.  La registrazione del certificato della CA X.509 è un processo in due fasi che comprende il caricamento del file di certificato e l'uso del token proof-of-possession.

Il processo di caricamento comporta il caricamento di un file che contiene il certificato.  Questo file non deve contenere mai le chiavi private.

Il passaggio relativo all'uso del token proof-of-possession prevede un processo basato sulla crittografia e sullo scambio della richiesta di verifica e della relativa risposta tra il dispositivo e l'hub IoT.  Dato che il contenuto di un certificato digitale è pubblico e pertanto suscettibile all'intercettazione, l'hub IoT deve verificare l'effettiva titolarità del possesso del certificato della CA.  A tale scopo, verrà generata una richiesta casuale di verifica che sarà necessario firmare usando la corrispondente chiave privata associata al certificato.  Se la chiave privata non è stata divulgata ed è stata conservata in un luogo protetto come indicato in precedenza, solo l'utente proprietario sarà in grado di completare questo passaggio. In questo metodo il fattore determinante della relazione di attendibilità è dato dalla riservatezza delle chiavi private.  Dopo avere firmato la richiesta di verifica, completare questo passaggio caricando un file contenente i risultati.

Informazioni su come [registrare il certificato della CA](iot-hub-security-x509-get-started.md#registercerts).

## <a name="how-to-create-a-device-on-iot-hub"></a>Come creare un dispositivo nell'hub IoT

Per impedire la rappresentazione del dispositivo, è necessario indicare all'hub IoT il dispositivo previsto.  A tale scopo, creare una voce specifica nel registro dei dispositivi dell'hub IoT.  Questo processo è automatico quando si usa il [servizio Device Provisioning](https://azure.microsoft.com/en-us/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/) (DPS) dell'hub IoT. 

Informazioni su come [creare manualmente un dispositivo nell'hub IoT](iot-hub-security-x509-get-started.md#createdevice).

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>Autenticazione dei dispositivi firmati con i certificati della CA X.509

Dopo aver registrato il certificato della CA X.509 e aver firmato i dispositivi con una catena di certificati, è necessario eseguire l'autenticazione del dispositivo quando questo si connette per la prima volta.  Al momento della connessione, un dispositivo firmato con un certificato della CA X.509 carica la relativa catena di certificati per la convalida. La catena include tutti i certificati della CA intermedi e i certificati del dispositivo.  L'hub IoT userà queste informazioni per autenticare il dispositivo in un processo che si articola in due passaggi.  L'hub IoT convalida a livello di crittografia la catena di certificati per la verifica della coerenza interna e quindi genera una richiesta di verifica del token proof-of-possession per il dispositivo.  L'hub IoT dichiara quindi autentico il dispositivo alla ricezione di una risposta valida a fronte del token proof-of-possession dal dispositivo.  Questa dichiarazione presuppone che la chiave privata del dispositivo sia protetta e che solo il dispositivo sia in grado di rispondere a questa richiesta di verifica.  Per proteggere le chiavi private nei dispositivi, è consigliabile usare chip sicuri quali, ad esempio, i moduli di protezione hardware.

Una connessione riuscita del dispositivo all'hub IoT completa il processo di autenticazione e indica un'installazione corretta.

Informazioni su come [completare questo passaggio di connessione del dispositivo](iot-hub-security-x509-get-started.md#authenticatedevice).

## <a name="next-steps"></a>Fasi successive

Informazioni sul [significato dell'autenticazione con il certificato della CA X.509](iot-hub-x509ca-concept.md) in IoT.

Introduzione al [servizio Device Provisioning ](https://docs.microsoft.com/azure/iot-dps/) nell'hub IoT.