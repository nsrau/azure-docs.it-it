---
title: Concetti relativi alla sicurezza nel servizio Azure Device Provisioning in hub IoT | Microsoft Docs
description: Descrive i concetti relativi al provisioning della sicurezza specifici dei dispositivi con il servizio Device Provisioning e l'hub IoT
services: iot-dps
keywords: 
author: nberdy
ms.author: nberdy
ms.date: 09/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 3ccbaaf55d2bdfedffcdb5ca069798328e2d75fd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>Concetti relativi alla sicurezza del servizio Device Provisioning in hub IoT 

Il servizio Device Provisioning in hub IoT è un servizio helper per l'hub IoT che consente di configurare il provisioning completamente automatico dei dispositivi in un hub IoT specifico. Con il servizio Device Provisioning è possibile effettuare il provisioning di milioni di dispositivi in modo sicuro e scalabile. Questo articolo fornisce una panoramica dei concetti relativi alla *sicurezza* in relazione al servizio Device Provisioning. L'articolo è di particolare interesse per tutte le persone coinvolte nella preparazione di un dispositivo per la distribuzione.

## <a name="attestation-mechanism"></a>Meccanismo di attestazione

Il meccanismo di attestazione è il metodo usato per verificare l'identità di un dispositivo. Tale meccanismo è importante anche per l'elenco di registrazione, che indica al servizio di provisioning il metodo di attestazione da usare con un determinato dispositivo.

> [!NOTE]
> L'hub IoT usa lo "schema di autenticazione" per un concetto simile in tale servizio.

Il servizio Device Provisioning supporta due tipi di attestazione:
* **Certificati X.509** basati sul flusso di autenticazione del certificato X.509 standard.
* **Token di firma di accesso condiviso** basati su una verifica nonce tramite lo standard TPM per le chiavi. Non è necessario un modulo TPM fisico nel dispositivo, ma il servizio prevede di eseguire l'attestazione usando la chiave di verifica dell'autenticità in base alle [specifiche TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).

## <a name="hardware-security-module"></a>Modulo di protezione hardware

Il modulo di protezione hardware viene usato per l'archiviazione sicura, basata su hardware dei segreti dei dispositivi ed è il tipo di archiviazione dei segreti più sicuro. Nel modulo di protezione hardware è possibile archiviare sia i certificati X.509 che i token di firma di accesso condiviso. I moduli di protezione hardware possono essere usati con entrambi i meccanismi di attestazione supportati dal provisioning.

> [!TIP]
> È consigliabile usare un modulo di protezione hardware con i dispositivi per archiviare in modo sicuro i segreti nei dispositivi.

I segreti dei dispositivi possono essere archiviati anche nel software (memoria), ma si tratta di un tipo di archiviazione meno sicuro rispetto a un modulo di protezione hardware.

## <a name="trusted-platform-module-tpm"></a>TPM (Trusted Platform Module)

Il concetto di TPM può fare riferimento a uno standard per archiviare in modo sicuro le chiavi usate per autenticare la piattaforma oppure all'interfaccia di I/O usata per interagire con i moduli che implementano lo standard. Il TPM può essere un componente hardware distinto o integrato e può essere basato su firmware o su software. Vedere altre informazioni su [TPM e attestazione TPM](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation). Il servizio Device Provisioning supporta solo il TPM 2.0.

## <a name="endorsement-key"></a>Chiave di verifica dell'autenticità

La chiave di verifica dell'autenticità è una chiave asimmetrica contenuta all'interno del TPM, dove è stata inserita in fase di produzione, ed è univoca per ogni TPM. La chiave di verifica dell'autenticità non può essere modificata o rimossa. La parte privata della chiave di verifica dell'autenticità non viene mai rilasciata al di fuori del TPM, mentre la parte pubblica viene usata per riconoscere un TPM originale. Vedere altre informazioni sulla [chiave di verifica dell'autenticità](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

## <a name="storage-root-key"></a>Chiave radice di archiviazione

La chiave radice di archiviazione viene archiviata nel TPM e usata per proteggere le chiavi TPM create dalle applicazioni, in modo che queste chiavi non possano essere usate senza il TPM. La chiave radice di archiviazione viene generata quando si acquisisce la proprietà del TPM. Quando si cancella il TPM in modo che un altro utente possa acquisirne la proprietà, viene generata una nuova chiave radice di archiviazione. Vedere altre informazioni sulla [chiave radice di archiviazione](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="root-certificate"></a>Certificato radice

Un certificato radice è un tipo di certificato X.509 che rappresenta un'autorità di certificazione ed è autofirmato. Costituisce la parte finale della catena di certificati.

## <a name="intermediate-certificate"></a>Certificato intermedio

Un certificato intermedio è un certificato X.509 che è stato firmato dal certificato radice (o da un altro certificato con il certificato radice nella relativa catena) e che viene usato per firmare il certificato foglia.

## <a name="leaf-certificate"></a>Certificato foglia

Un certificato foglia o certificato dell'entità finale, viene usato per identificare il titolare del certificato e ha il certificato radice nella relativa catena di certificati. Il certificato foglia non viene usato per firmare altri certificati.
