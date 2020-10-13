---
title: Applicazioni attendibili come moduli Azure IoT Edge
description: Usare Open enclave SDK e API per scrivere applicazioni attendibili e distribuirle come moduli IoT Edge per l'elaborazione riservata
author: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: kgremban
ms.openlocfilehash: d81cc6c94c04c683362fd12cd6777c304a4b0a84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361977"
---
# <a name="confidential-computing-at-the-edge"></a>Informatica riservata al perimetro

Azure IoT Edge supporta applicazioni attendibili eseguite all'interno di enclavi sicure nel dispositivo. La crittografia fornisce sicurezza per i dati in transito o inattivi, ma le enclavi forniscono sicurezza per i dati e i carichi di lavoro in uso. IoT Edge supporta Open enclave come standard per lo sviluppo di applicazioni attendibili.

La sicurezza è sempre stata un importante obiettivo del Internet delle cose (tutto) perché spesso i dispositivi per le cose sono spesso in uscita nel mondo anziché essere protetti all'interno di una struttura privata. Questa esposizione mette a rischio i dispositivi per manomissioni e falsificazione, perché sono fisicamente accessibili per gli attori malintenzionati. I dispositivi IoT Edge hanno ancora più necessità di attendibilità e integrità, perché consentono l'esecuzione di carichi di lavoro sensibili al perimetro. A differenza dei sensori e degli attuatori comuni, questi dispositivi perimetrali intelligenti stanno potenzialmente esponendo i carichi di lavoro sensibili precedentemente eseguiti solo in ambienti cloud o locali protetti.

Il [IOT Edge Security Manager](iot-edge-security-manager.md) risolve una parte della richiesta di elaborazione riservata. Il gestore della sicurezza usa un modulo di protezione hardware (HSM) per proteggere i carichi di lavoro di identità e i processi in corso di un dispositivo IoT Edge.

Un altro aspetto dell'elaborazione riservata è la protezione dei dati in uso al perimetro. Un *ambiente di esecuzione attendibile* è un ambiente sicuro e isolato su un processore e viene talvolta definito *enclave*. Un' *applicazione attendibile* è un'applicazione che viene eseguita in un'enclave. A causa della natura delle enclavi, le applicazioni attendibili sono protette da altre app in esecuzione nel processore principale o nel TEE.

## <a name="trusted-applications-on-iot-edge"></a>Applicazioni attendibili su IoT Edge

Le applicazioni attendibili vengono crittografate in transito e inattivi e vengono decrittografate solo per l'esecuzione all'interno di un ambiente di esecuzione attendibile. Questo standard è valido per le applicazioni attendibili distribuite come moduli IoT Edge.

Lo sviluppatore crea l'applicazione attendibile e la impacchetta come un modulo IoT Edge. Prima di eseguire il push nel registro contenitori, l'applicazione viene crittografata. L'applicazione rimane crittografata durante il processo di distribuzione IoT Edge finché il modulo non viene avviato nel dispositivo IoT Edge. Quando l'applicazione attendibile si trova all'interno del TEE del dispositivo, viene decrittografata e può iniziare l'esecuzione.

![Diagramma: le applicazioni attendibili vengono crittografate all'interno IoT Edge moduli fino a quando non vengono distribuite nell'enclave](./media/deploy-trusted-applications/trusted-applications-encrypted.png)

Le applicazioni attendibili su IoT Edge sono un'estensione logica di [Azure Confidential computing](../confidential-computing/overview.md). È anche possibile distribuire i carichi di lavoro eseguiti all'interno di enclavi sicure nel cloud per l'esecuzione all'interno di enclavi sicure al perimetro.

## <a name="open-enclave"></a>Open Enclave

[Open enclave SDK](https://openenclave.io/sdk/) è un progetto open source che consente agli sviluppatori di creare applicazioni attendibili per più piattaforme e ambienti. Open enclave SDK opera all'interno dell'ambiente di esecuzione attendibile di un dispositivo, mentre l'API Open enclave funge da interfaccia tra il TEE e l'ambiente di elaborazione non TEE.

Open enclave supporta più piattaforme hardware. IoT Edge supporto per le enclavi attualmente richiede il sistema operativo open Portable TEE (OP-TEE OS). Per altre informazioni, vedere [Open enclave SDK per op-Tee OS](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/OP-TEE/Introduction.md).

L'archivio enclave aperto include anche esempi per aiutare gli sviluppatori a iniziare. Per ulteriori informazioni, scegliere uno degli articoli introduttivi:

* [Compilazione degli esempi Open Enclav SDK in Linux](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md)
* [Compilazione degli esempi Open Enclav SDK in Windows](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesWindows.md)

## <a name="hardware"></a>Hardware

Attualmente, [TrustBox by squamoses](https://scalys.com/trustbox-industrial/) è l'unico dispositivo supportato con i contratti di servizio del produttore per la distribuzione di applicazioni attendibili come moduli IOT Edge. Il TrustBox è basato sui dispositivi TrustBox Edge e TrustBox EdgeXL sono precaricati con Open enclave SDK e Azure IoT Edge.

Per altre informazioni, vedere [l'introduzione all'enclave Open per le TrustBox](https://aka.ms/scalys-trustbox-edge-get-started).

## <a name="develop-and-deploy"></a>Sviluppare e distribuire

Quando si è pronti per sviluppare e distribuire l'applicazione attendibile, l'estensione [Open enclave Microsoft](https://marketplace.visualstudio.com/items?itemName=ms-iot.msiot-vscode-openenclave) per Visual Studio Code può essere utile. È possibile usare Linux o Windows come computer di sviluppo per sviluppare moduli per la TrustBox.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come iniziare a sviluppare applicazioni attendibili come moduli IoT Edge con l' [estensione Open enclave per Visual Studio Code](https://github.com/openenclave/openenclave/tree/master/devex/vscode-extension)
