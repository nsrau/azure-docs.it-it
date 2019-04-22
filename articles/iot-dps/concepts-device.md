---
title: Concetti relativi ai dispositivi in Azure Device Provisioning | Microsoft Docs
description: Descrive i concetti relativi al provisioning specifici dei dispositivi con il servizio Device Provisioning e l'hub IoT
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 2904da863707c5f653d774b0a480cc48c95c8d1c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785332"
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>Concetti relativi ai dispositivi del servizio Device Provisioning in hub IoT

Il servizio Device Provisioning in hub IoT è un servizio helper per l'hub IoT che consente di configurare il provisioning completamente automatico dei dispositivi in un hub IoT specifico. Con il servizio Device Provisioning è possibile effettuare il provisioning di milioni di dispositivi in modo sicuro e scalabile.

Questo articolo fornisce una panoramica dei concetti relativi ai *dispositivi* in relazione al servizio Device Provisioning. L'articolo è di particolare interesse per le persone coinvolte nella [fase di produzione](about-iot-dps.md#manufacturing-step) in preparazione alla distribuzione di un dispositivo.

## <a name="attestation-mechanism"></a>Meccanismo di attestazione

Il meccanismo di attestazione è il metodo usato per verificare l'identità di un dispositivo. Tale meccanismo è importante anche per l'elenco di registrazione, che indica al servizio di provisioning il metodo di attestazione da usare con un determinato dispositivo.

> [!NOTE]
> L'hub IoT usa lo "schema di autenticazione" per un concetto simile in tale servizio.

Il servizio Device Provisioning supporta i seguenti tipi di attestazione:
* **Certificati X.509** basati sul flusso di autenticazione del certificato X.509 standard.
* **Trusted Platform Module (TPM)** basato su una richiesta di verifica nonce, che usa lo standard TPM relativo alle chiavi per presentare un token di firma di accesso condiviso (SAS). Non è necessario un modulo TPM fisico nel dispositivo, ma il servizio prevede di eseguire l'attestazione usando la chiave di verifica dell'autenticità in base alle [specifiche TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Chiave simmetrica** basata sui [Token di autenticazione](../iot-hub/iot-hub-devguide-security.md#security-tokens) della sulla firma di accesso condiviso (SAS), che include una firma con hash e una scadenza incorporata. Per altre informazioni, vedere [Attestazione con chiave simmetrica](concepts-symmetric-key-attestation.md).

## <a name="hardware-security-module"></a>Modulo di protezione hardware

Il modulo di protezione hardware viene usato per l'archiviazione sicura, basata su hardware dei segreti dei dispositivi ed è il tipo di archiviazione dei segreti più sicuro. Nel modulo di protezione hardware è possibile archiviare sia i certificati X.509 che i token di firma di accesso condiviso. I moduli di protezione hardware possono essere usati con entrambi i meccanismi di attestazione supportati dal servizio di provisioning.

> [!TIP]
> È consigliabile usare un modulo di protezione hardware con i dispositivi per archiviare in modo sicuro i segreti nei dispositivi.

I segreti dei dispositivi possono essere archiviati anche nel software (memoria), ma si tratta di un tipo di archiviazione meno sicuro rispetto a un modulo di protezione hardware.

## <a name="registration-id"></a>ID di registrazione

L'ID di registrazione viene usato per identificare in modo univoco un dispositivo nel servizio Device Provisioning. L'ID dispositivo deve essere univoco nell'[ambito ID](#id-scope) del servizio di provisioning. Ogni dispositivo deve avere un ID di registrazione. L'ID di registrazione è alfanumerico, con caratteri minuscoli e può contenere trattini.

* Nel caso di un TPM, l'ID di registrazione viene fornito dal TPM stesso.
* Nel caso dell'attestazione basata su X.509, l'ID di registrazione viene fornito come nome soggetto del certificato.

## <a name="device-id"></a>ID dispositivo

L'ID dispositivo è l'ID visualizzato nell'hub IoT. È possibile, ma non obbligatorio, impostare l'ID dispositivo desiderato nella voce di registrazione. Se l'ID dispositivo desiderato non viene specificato nell'elenco di registrazione, l'ID di registrazione viene usato come ID dispositivo durante la registrazione del dispositivo. Vedere altre informazioni sugli [ID dispositivo nell'hub IoT](../iot-hub/iot-hub-devguide-identity-registry.md).

## <a name="id-scope"></a>Ambito ID

L'ambito ID viene assegnato a un servizio Device Provisioning quando viene creato dall'utente ed è usato per identificare in modo univoco il servizio di provisioning specifico usato dal dispositivo per la registrazione. L'ambito ID viene generato dal servizio e non è modificabile, per garantire l'univocità.

> [!NOTE]
> L'univocità è importante per le operazioni di distribuzione con esecuzione prolungata e gli scenari di fusione e acquisizione.
