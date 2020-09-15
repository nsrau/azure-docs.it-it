---
title: Servizio Device provisioning in hub Internet-ruoli e operazioni
description: Questo articolo fornisce una panoramica concettuale dei ruoli e delle operazioni che interessano durante lo sviluppo e la soluzione Internet delle cose usando il servizio Device provisioning (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 09/14/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotga
ms.openlocfilehash: 52c0dcf4bd9f7b2ca8928eab5272a6500cbb379d
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90533073"
---
# <a name="roles-and-operations"></a>Ruoli e operazioni

Le fasi di sviluppo di una soluzione Internet delle cose possono estendersi a settimane o mesi, a causa di realtà di produzione come la produzione, la spedizione, il processo doganale e così via. Inoltre, possono estendersi sulle attività tra più ruoli, in base alle varie entità interattive. In questo argomento vengono esaminati in modo più approfondito i vari ruoli e operazioni correlate a ogni fase, quindi viene illustrato il flusso in un diagramma di sequenza. 

Il provisioning pone anche i requisiti per il produttore del dispositivo, specifici dell'abilitazione del [meccanismo di attestazione](concepts-service.md#attestation-mechanism). Le operazioni di produzione possono continuare indipendentemente dai tempi delle fasi di provisioning automatico, soprattutto nel caso in cui vengano acquistati nuovi dispositivi dopo aver eseguito il provisioning automatico.

Nel sommario a sinistra è disponibile una serie di guide introduttive che consentono di illustrare il provisioning automatico tramite esperienze pratiche. Per facilitare il processo di apprendimento, inoltre, viene usata un'applicazione software per simulare la procedura di registrazione di un dispositivo fisico. Data la natura simulata delle guide introduttive, alcune di esse richiedono che l'utente esegua operazioni di pertinenza di ruoli diversi, incluse operazioni per ruoli non esistenti.

| Ruolo | Operazione | Descrizione |
|------| --------- | ------------|
| Produttore | Codifica dell'identità e dell'URL di registrazione | In base al meccanismo di attestazione usato, il produttore è responsabile di codificare le informazioni di identità del dispositivo e l'URL di registrazione del servizio Device Provisioning.<br><br>**Guide introduttive**: poiché il dispositivo è simulato, il ruolo Produttore non esiste. Vedere il ruolo Sviluppatore per istruzioni su come ottenere queste informazioni, usate nel processo di codifica di un'applicazione di registrazione di esempio. |
| | Assegnazione dell'identità del dispositivo | In qualità di autore delle informazioni sull'identità del dispositivo, il produttore è responsabile di comunicarle all'operatore (o a un agente designato) o di registrarle direttamente nel servizio Device Provisioning tramite API.<br><br>**Guide introduttive**: poiché il dispositivo è simulato, il ruolo Produttore non esiste. Vedere il ruolo Sviluppatore per informazioni su come ottenere l'identità del dispositivo, usata per registrare un dispositivo simulato nell'istanza del servizio Device Provisioning. |
| Operatore | Configurazione del provisioning automatico | Questa operazione corrisponde alla prima fase del provisioning automatico.<br><br>**Guide introduttive**: l'utente svolge il ruolo di operatore configurando le istanze del servizio Device Provisioning e dell'hub IoT nella sottoscrizione di Azure. |
|  | Registrazione dell'identità del dispositivo | Questa operazione corrisponde alla seconda fase del provisioning automatico.<br><br>**Guide introduttive**: l'utente svolge il ruolo di operatore registrando il dispositivo simulato nell'istanza del servizio Device Provisioning. L'identità del dispositivo è determinata dal metodo di attestazione simulata nella guida introduttiva (TPM o X.509). Vedere il ruolo Sviluppatore per informazioni dettagliate sull'attestazione. |
| Servizio Device Provisioning,<br>Hub IoT | \<all operations\> | Sia in un'implementazione di produzione con dispositivi fisici che in guide rapide con dispositivi simulati, questi ruoli vengono svolti tramite i servizi IoT configurati nella sottoscrizione di Azure. Il funzionamento dei ruoli e delle operazioni, infatti, è identico, poiché è indifferente per i servizi IoT che venga eseguito il provisioning di dispositivi fisici o simulati. |
| Sviluppatore | Compilazione/distribuzione del software di registrazione | Questa operazione corrisponde alla terza fase del provisioning automatico. Lo sviluppatore è responsabile della compilazione e della distribuzione del software di registrazione nel dispositivo tramite l'SDK appropriato.<br><br>**Guida introduttiva di**: l'applicazione di registrazione di esempio creata simula un dispositivo reale (per la piattaforma e il linguaggio scelti), che viene eseguito nella workstation anziché essere distribuito in un dispositivo fisico. L'applicazione di registrazione esegue le stesse operazioni che svolgerebbe se fosse stata distribuita in un dispositivo fisico. Si specifica il metodo di attestazione (certificato X.509 o TPM), nonché l'URL di registrazione e "l'ambito ID" dell'istanza del servizio Device Provisioning. L'identità del dispositivo è determinata dalla logica di attestazione dell'SDK in fase di runtime, basata sul metodo specificato: <ul><li>**Attestazione TPM**: la workstation di sviluppo viene eseguita su un'[applicazione del simulatore TPM](how-to-use-sdk-tools.md#trusted-platform-module-tpm-simulator). Successivamente, viene usata un'applicazione separata per estrarre la "chiave di verifica dell'autenticità" del TPM e "l'ID di registrazione" da usare per la registrazione dell'identità del dispositivo. La logica di attestazione dell'SDK usa il simulatore anche durante la procedura di registrazione per presentare un token di firma di accesso condiviso firmato per l'autenticazione e la verifica della registrazione.</li><li>**Attestazione X509**: si usa uno strumento per [generare un certificato](how-to-use-sdk-tools.md#x509-certificate-generator) e, successivamente, si crea il file del certificato da usare nella procedura di registrazione. La logica di attestazione dell'SDK usa il certificato anche durante la procedura di registrazione per presentarlo per l'autenticazione e la verifica della registrazione.</li></ul> |
| Dispositivo | Avvio e registrazione | Questa operazione corrisponde alla terza fase del provisioning automatico, eseguita dal software di registrazione dei dispositivi creato dallo sviluppatore. Vedere il ruolo Sviluppatore per informazioni dettagliate. Al primo avvio: <ol><li>L'applicazione si connette all'istanza del servizio Device Provisioning sulla base dell'URL globale e dell'ambito ID specificati durante la fase di sviluppo.</li><li>Una volta connesso, il dispositivo viene autenticato tramite il metodo di attestazione e l'identità specificati durante la fase di registrazione.</li><li>Dopo essere stato autenticato, il dispositivo viene registrato nell'istanza dell'hub IoT specificata dall'istanza del servizio di provisioning.</li><li>Al termine della registrazione, all'applicazione di registrazione vengono restituiti un ID dispositivo univoco e un endpoint dell'hub IoT da usare per le comunicazioni con l'hub IoT.</li><li> A questo punto, il dispositivo può recuperare lo stato iniziale del [dispositivo gemello](~/articles/iot-hub/iot-hub-devguide-device-twins.md) per la configurazione e avviare il processo di segnalazione dei dati di telemetria.</li></ol>**Guida introduttiva**: poiché il dispositivo è simulato, il software di registrazione viene eseguito sulla workstation di sviluppo.|

Nel diagramma seguente vengono riepilogati i ruoli e la sequenza di operazioni eseguite durante il provisioning automatico dei dispositivi:
<br><br>
[![Sequenza di provisioning automatico per un dispositivo](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png)](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png#lightbox) 

> [!NOTE]
> Facoltativamente, il produttore può anche eseguire l'operazione "Registrazione dell'identità del dispositivo" usando le API del servizio Device Provisioning, anziché tramite il ruolo Operatore. Per un'analisi approfondita di questa sequenza e altre informazioni, vedere il video [Zero touch device registration with Azure IoT](https://youtu.be/cSbDRNg72cU?t=2460) (Registrazione automatica dei dispositivi con Azure IoT), a partire dal minuto 41:00.

## <a name="roles-and-azure-accounts"></a>Ruoli e account di Azure

Il modo in cui viene eseguito il mapping di ogni ruolo a un account Azure dipende dallo scenario; sono disponibili alcuni scenari che possono essere coinvolti. I modelli comuni seguenti dovrebbero aiutare a fornire una conoscenza generale su come viene eseguito generalmente il mapping dei ruoli in un account Azure.

#### <a name="chip-manufacturer-provides-security-services"></a>Il produttore del chip fornisce servizi di sicurezza

In questo scenario, il produttore gestisce la sicurezza per i clienti di primo livello. Questo scenario potrebbe essere preferito da questi clienti di primo livello in quanto non devono gestire la sicurezza dettagliata. 

Il produttore introduce la protezione nei moduli di protezione hardware (HSM). Questa protezione può includere le chiavi, i certificati e altro ottenuti dal produttore dai potenziali clienti che dispongono già di istanze del servizio Device Provisioning e della configurazione dei gruppi di registrazione. Anche il produttore potrebbe generare queste informazioni di protezione per i clienti.

In questo scenario, potrebbero essere coinvolti due account di Azure:

- **Account #1**: probabilmente condiviso tra l'operatore e i ruoli degli sviluppatori a un certo livello. Questa entità può acquistare i chip del modulo di protezione hardware dal produttore. Tali chip fanno riferimento alle istanze del servizio Device Provisioning associate all'Account #1. Con le registrazioni del servizio Device Provisioning, questa entità può assegnare in lease i dispositivi a più clienti di livello 2, riconfigurando le impostazioni di registrazione del dispositivo nel servizio Device Provisioning. Questa entità può anche avere hub Internet allocati per i sistemi back-end dell'utente finale per l'interfaccia con per accedere ai dati di telemetria del dispositivo e così via. In quest'ultimo caso, un secondo account potrebbe non essere necessario.

- **Account #2**: gli utenti finali, a livello di due clienti possono avere gli hub Internet. L'entità associata all'Account #1 fa riferimento proprio ai punti assegnati in lease all'hub corretto in questo account. Questa configurazione richiede il collegamento del servizio Device Provisioning e degli hub IoT agli account di Azure, che può essere eseguito con i modelli di Azure Resource Manager.

#### <a name="all-in-one-oem"></a>All-in-one OEM

Il produttore potrebbe essere un "All-in-one OEM" in cui è necessario solo l'account di un singolo produttore. Il produttore gestisce la sicurezza e il provisioning end to end.

Il produttore potrebbe fornire un'applicazione basata su cloud ai clienti che acquistano dispositivi. Questa applicazione si interfaccerebbe con l'hub IoT allocato dal produttore.

I distributori per la vendita o le macchine automatiche per il caffè rappresentano esempi di questo scenario.




## <a name="next-steps"></a>Passaggi successivi

Può risultare utile aggiungere un segnalibro a questo articolo per usarlo come punto di riferimento mentre si segue la guida introduttiva per la procedura di provisioning automatico corrispondente. 

Per iniziare, completare la guida introduttiva "Configurare il provisioning automatico" relativa allo strumento di gestione in uso, che copre la fase "Configurazione del servizio":

- [Configurare il provisioning automatico usando l'interfaccia della riga di comando di Azure](quick-setup-auto-provision-cli.md)
- [Configurare il provisioning automatico usando il portale di Azure](quick-setup-auto-provision.md)
- [Configurare il provisioning automatico usando un modello di Resource Manager](quick-setup-auto-provision-rm.md)

Continuare quindi con la Guida introduttiva "effettuare il provisioning di un dispositivo" adatta al meccanismo di attestazione del dispositivo e al servizio Device provisioning SDK/lingua preferenza. Questa guida rapida copre le fasi "Registrazione del dispositivo" e "Registrazione e configurazione del dispositivo": 

| Meccanismo di attestazione dispositivo | Linguaggio/SDK della guida rapida | 
| ------------------------------- | -------------------- |
| Chiave simmetrica | [C](quick-create-simulated-device-symm-key.md)<br>[Java](quick-create-simulated-device-symmetric-key-java.md)<br>[Python](quick-create-device-symmetric-key-python.md) |
| Certificato X.509 | [C](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C#](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |
| Trusted Platform Module simulato (TPM) | [C](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C#](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |




