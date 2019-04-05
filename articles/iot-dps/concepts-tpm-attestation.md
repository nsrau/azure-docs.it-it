---
title: Servizio Device Provisioning in hub IoT di Azure - Attestazione TPM
description: In questo articolo viene fornita una panoramica concettuale del flusso di un'attestazione TPM con il servizio Device Provisioning IoT.
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 07c5dbce0b98d1c197164f4fc77682f78ede57f0
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59048878"
---
# <a name="tpm-attestation"></a>Attestazione TPM

Il servizio Device Provisioning in hub IoT è un servizio helper per l'hub IoT che consente di configurare il provisioning completamente automatico dei dispositivi in un hub IoT specifico. Con il servizio Device Provisioning è possibile effettuare il provisioning di milioni di dispositivi in modo sicuro.

In questo articolo viene descritto il processo di attestazione dell’identità quando si usa un [TPM](./concepts-device.md). TPM è l'acronimo di Trusted Platform Module ed è un tipo di modulo di protezione hardware (HSM). In questo articolo si presuppone che l’utente utilizzi un firmware discreto o integrato TPM. I TPM emulatori di software sono ideali per la creazione di prototipi o test, ma non forniscono lo stesso livello di sicurezza di un firmware discreto o integrato TPM. Non è consigliabile utilizzare software TPM nell'ambiente di produzione. Per altre informazioni sui tipi di moduli TPM, vedere [una breve introduzione a TPM](https://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf).

Questo articolo è rilevante solo per i dispositivi con TPM 2.0 con supporto chiave HMAC e le relative chiavi di verifica dell'autenticità. Non è rilevante per l'autenticazione dei dispositivi con certificati X.509. TPM è uno standard ISO di settore appartenente al Trusted Computing Group. Per ulteriori informazioni su TPM consultare la [specifica TPM 2.0 completa](https://trustedcomputinggroup.org/tpm-library-specification/) o la [specifica ISO/IEC 11889 ](https://www.iso.org/standard/66510.html). In questo articolo si presuppone inoltre che l’utente sia familiare con coppie di chiavi pubbliche e private, e sulle relative modalità di utilizzo per la crittografia.

Il Software Development KIT del dispositivo del servizio Device Provisioning SDK gestisce per l’utente tutte le informazioni descritte in questo articolo. Non è necessario implementare ulteriori operazioni se si utilizza l’SDK dei dispositivi. Questo articolo aiuta a comprendere concettualmente il comportamento del chip di sicurezza TPM durante il provisioning del dispositivo e spiega perché questa operazione è sicura.

## <a name="overview"></a>Panoramica

I TPM utilizzano la cosiddetta chiave di verifica dell'autenticità come radice di attendibilità sicura. La chiave di verifica dell'autenticità è univoca per i TPM e modificarla significa essenzialmente modificare il dispositivo come fosse nuovo.

Esiste anche un altro tipo di chiave di cui dispongono i TPM, denominata chiave radice di archiviazione (SRK). Una SRK può essere generata dal proprietario del TPM dopo che quest’ultima diventa proprietaria del TPM. Diventare proprietario del TPM è il modo con cui un TPM intende segnalare che "un qualsiasi utente imposta una password nel modulo HSM." Se un dispositivo TPM è venduto a un nuovo proprietario, il nuovo proprietario può diventare proprietario del TPM per generare una nuova SRK. La nuova generazione SRK garantisce che il proprietario precedente non utilizzi il TPM. Poiché la SRK è univoca per il proprietario del TPM, è utilizzabile per bloccare i dati nel TPM per tale proprietario. La SRK fornisce un ambiente sandbox in cui il proprietario può archiviare le chiavi e revocarne l’accesso se il dispositivo o il TPM viene venduto. È come cambiare casa: diventare proprietari significa cambiare le serrature alle porte e sbarazzarsi del mobilio dei proprietari precedente (SRK), ma non cambiare indirizzo di casa (chiave di verifica dell'autenticità).

Quando un dispositivo è stato configurato ed è pronto all'uso, viene consentito l’utilizzo di una chiave di verifica dell'autenticità e di una chiave radice di archiviazione.

![Acquisizione della proprietà di un TPM](./media/concepts-tpm-attestation/tpm-ownership.png)

Una nota sul assunzione di proprietà del TPM: Assunzione di proprietà di un modulo TPM dipendono da fattori, inclusi produttore TPM, il set di strumenti TPM in uso e il sistema operativo del dispositivo. Seguire le istruzioni relative al sistema per diventare proprietario.

Il servizio Device Provisioning utilizza la parte pubblica della chiave di verifica dell'autenticità (EK_pub) per identificare e registrare i dispositivi. Il fornitore del dispositivo può leggere la EK_pub durante la produzione o le fasi finali di test e caricare la EK_pub sul servizio di provisioning in modo che il dispositivo venga riconosciuto anche quando si connette per eseguire il provisioning. Il servizio Device Provisioning non verifica la SRK o il proprietario, in tal modo "eliminando" il TPM vengono cancellati i dati dei clienti, ma la chiave di verifica dell'autenticità (e altri dati del fornitore) vengono conservati e per il dispositivo verrà comunque riconosciuto dal servizio Device Provisioning quando si connette per eseguire il provisioning.

## <a name="detailed-attestation-process"></a>Processo di attestazione dettagliato

Quando un dispositivo con un TPM si connette per la prima volta al servizio Device Provisioning, il servizio controlla prima la EK_pub fornita rispetto alla EK_pub archiviata nell'elenco di registrazione. Se le EK_pub non corrispondono, al dispositivo non è consentito eseguire il provisioning. Se le EK_pub corrispondono, il servizio richiede quindi al dispositivo di attestare la proprietà della parte privata della chiave di verifica dell'autenticità tramite una richiesta di verifica nonce, un richiesta sicura utilizzata per attestare l'identità. Il servizio Device Provisioning genera un parametro nonce e poi lo crittografa nuovamente con la SRK e, successivamente, con la EK_pub, le quali vengono fornite dal dispositivo durante la chiamata di registrazione iniziale. Il TPM conserva sempre in modo sicuro la parte privata della chiave di verifica dell'autenticità. Ciò impedisce la contraffazione e assicura che venga effettuato il provisioning dei token di firma di accesso condiviso per i dispositivi autorizzati.

Analizziamo il processo di un'attestazione in dettaglio.

### <a name="device-requests-an-iot-hub-assignment"></a>Richiesta del dispositivo di un'assegnazione hub IoT

Prima di tutto, il dispositivo si connette al servizio di Device Provisioning e richiede l’esecuzione del provisioning. In questo modo, il dispositivo fornisce il servizio con l'ID registrazione, l’ambito ID e le EK_pub e SRK_pub dal TPM. Il servizio trasferisce nuovamente il parametro nonce crittografato al dispositivo e chiede a quest’ultimo di decrittografare tale parametro e utilizzarlo per firmare un token di firma di accesso condiviso per riconnettersi e completare il provisioning.

![Provisioning delle richieste del dispositivo](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>Richiesta nonce

Il dispositivo accetta il parametro nonce e utilizza le parti private della chiave di verifica dell'autenticità e della chiave radice di archiviazione per decrittografarlo nel TPM; l'ordine di crittografia nonce richiama il trust dalla chiave di verifica dell'autenticità, che è immutabile, alla chiave radice di archiviazione, che possono cambiare se un nuovo proprietario assume la proprietà del TPM.

![Decrittografia del parametro nonce](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>Convalida del parametro nonce e ricezione delle credenziali

Il dispositivo può quindi firmare un token di firma di accesso condiviso con il parametro nonce decrittografato e ristabilire una connessione con il servizio Device Provisioning utilizzando il token di firma di accesso condiviso firmato. Completata la richiesta nonce, il servizio consente al dispositivo di eseguire il provisioning.

![Il dispositivo ristabilisce la connessione al servizio Device Provisioning per la convalida della proprietà della chiave di verifica dell'autenticità](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>Passaggi successivi

A questo punto, il dispositivo si connette all'hub IoT e viene garantita l’archiviazione sicura delle chiavi dei dispositivi. Ora che è stato appreso come il servizio Device Provisioning verifica in modo sicuro l’identità di un dispositivo utilizzando il TPM, è possibile consultare gli articoli seguenti per altre informazioni:

* [Informazioni su tutti i concetti di provisioning automatico](./concepts-auto-provisioning.md)
* [Introduzione all'uso del provisioning automatico](./quick-setup-auto-provision.md) tramite SDK per la gestione del flusso.
